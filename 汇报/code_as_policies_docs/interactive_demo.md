# interactive_demo.py 类与函数调用结构

## 类定义

| 类 | 行号 | 职责 |
|---|---|---|
| **Robotiq2F85** | 117–218 | 二指夹爪：后台线程维持对称开合，提供夹紧/松开/接触检测 |
| **PickPlaceEnv** | 225–648 | 桌面操作仿真环境：管理 PyBullet 仿真、UR5e 机械臂、物体、相机 |
| **LMP_wrapper** | 654–742 | LLM 可调用的机器人 API 层：`put_first_on_second`、坐标归一化等 |
| **FunctionParser** | 795–815 | AST 解析器，提取 LLM 代码中的函数调用 |
| **LMPFGen** | 822–937 | 函数生成器：发现缺失函数 → 调用 LLM 生成 → 递归检查子函数 |
| **LMP** | 944–1074 | 核心类：构建 prompt → 调用 LLM → 执行代码 → 错误重试 |

## 模块级辅助函数

- `var_exists(name, all_vars)` — 检查变量是否存在于作用域
- `merge_dicts(dicts)` — 合并多个字典
- `exec_safe(code_str, gvars, lvars)` — 安全执行 LLM 生成的代码
- `setup_LMP(env, cfg)` — 组装 LMP 环境（关键组装函数）
- `main()` — CLI 入口

## 全局常量

- `COLORS` — 10 种颜色的 RGBA 映射
- `CORNER_POS` — 桌面上 9 个预定义命名位置
- `ALL_BLOCKS` / `ALL_BOWLS` — 所有可用方块/碗名称
- `PIXEL_SIZE`, `BOUNDS` — 像素比例与桌面工作区边界

## 调用关系图

```
main()
 ├─ PickPlaceEnv.__init__(render, high_res, high_frame_rate)
 ├─ PickPlaceEnv.reset(object_list)
 │    ├─ Robotiq2F85.__init__(robot, tool, assets_path)
 │    │    └─ Robotiq2F85.step()                ◄── 后台线程(daemon)
 │    └─ Robotiq2F85.release()
 ├─ setup_LMP(env, cfg)
 │    ├─ LMP_wrapper(env, cfg)                   ◄── 包装 env 为 LLM API
 │    ├─ LMPFGen(fgen_cfg, fixed_vars, variable_vars)
 │    ├─ LMP('parse_obj_name', ...)        ─┐
 │    ├─ LMP('parse_position', ...)         ├── 4 个子 LMP，注入 variable_vars
 │    ├─ LMP('parse_question', ...)         │
 │    ├─ LMP('transform_shape_pts', ...)    ─┘
 │    └─ LMP('tabletop_ui', ...)                ◄── 主 LMP，返回
 │
 ├─ 录制初始帧: env.get_camera_image() → cache_video
 │
 └─ LMP.__call__(command, context)              ◄── 执行用户指令
      ├─ build_prompt(query, context)            ◄── 组合 prompt
      │    ├─ 替换 {variable_vars_imports}
      │    ├─ 追加 self.exec_hist（如 maintain_session）
      │    ├─ 追加 context（如 objects = [...]）
      │    └─ 包装 query（prefix + query + suffix）
      │
      ├─ OpenAI API: chat.completions.create()   ◄── 调用 LLM
      │
      ├─ LMPFGen.create_new_fs_from_code(code_str)  ◄── 发现并生成缺失函数
      │    ├─ FunctionParser.visit(ast.parse(code_str))
      │    │    ├─ visit_Call()                  ◄── 提取函数调用
      │    │    └─ visit_Assign()                ◄── 提取赋值中的函数调用
      │    │
      │    └─ for f_name, f_sig in fs:
      │         ├─ var_exists(f_name, all_vars)  ◄── 检查是否已存在
      │         └─ create_f_from_sig(f_name, f_sig, ...)  ◄── LLM 生成函数体
      │              └─ (递归) create_new_fs_from_code(f_def_body)
      │                   ◄── 检查新函数体内是否还有缺失函数
      │
      └─ exec_safe(to_exec, gvars, lvars)        ◄── 执行 LLM 主代码
           (gvars = fixed_vars + variable_vars + new_fs)
           │
           └─ 代码内调用: put_first_on_second(arg1, arg2)
                └─ LMP_wrapper.put_first_on_second()
                     └─ PickPlaceEnv.step(action)
                          ├─ movep(hover_xyz)           ← 悬停
                          │    └─ servoj(joints)        ← IK → 位置控制
                          │         └─ pybullet.setJointMotorControlArray()
                          ├─ gripper.activate()          ← 夹紧
                          ├─ movep(pick_xyz)             ← 下降
                          ├─ gripper.release()          ← 松开
                          └─ 循环调用:
                               └─ step_sim_and_render()
                                    ├─ pybullet.stepSimulation()
                                    ├─ get_camera_image()
                                    │    └─ render_image()
                                    │         ├─ 计算视图矩阵 + 投影矩阵
                                    │         └─ pybullet.getCameraImage()
                                    ├─ cache_video.append(img)   ← 录制
                                    └─ cv2.imshow()             ← 实时显示
```

## LMP 之间的交叉调用

（由 prompt 中的 few-shot 示例定义）

```
tabletop_ui → parse_obj_name, parse_position, parse_question,
              transform_shape_pts, put_first_on_second, get_obj_names,
              get_corner_name, get_side_name, is_obj_visible, say,
              stack_objects_in_order (由 fgen 动态生成)

parse_obj_name → get_obj_pos, parse_position, get_obj_positions_np,
                get_closest_idx

parse_position → denormalize_xy, parse_obj_name, get_obj_names, get_obj_pos,
                make_line, interpolate_pts_on_line, make_triangle,
                get_closest_point, get_farthest_point,
                get_all_object_positions_np

parse_question → get_obj_pos, parse_obj_name, bbox_contains_pt, is_obj_visible

transform_shape_pts → scale_pts_around_centroid_np, translate_pts_np,
                     rotate_pts_around_centroid_np, parse_obj_name, get_obj_pos
```

## LMP 配置表 (`cfg_tabletop`)

| LMP | has_return | maintain_session | return_val_name | stop tokens |
|---|---|---|---|---|
| tabletop_ui | × | ✓ | — | `#`, `objects = [` |
| parse_obj_name | ✓ | × | `ret_val` | `#`, `objects = [` |
| parse_position | ✓ | × | `ret_val` | `#` |
| parse_question | ✓ | × | `ret_val` | `#`, `objects = [` |
| transform_shape_pts | ✓ | × | `new_shape_pts` | `#` |
| fgen | — | × | — | `# define`, `# example` |

## 数据流

```
自然语言指令 (str)
  → LMP.__call__()
    → LLM 生成 Python 代码 (str)
      → LMPFGen.create_new_fs_from_code()  (递归生成缺失函数)
        → exec_safe() 执行代码 (gvars = LMP_wrapper API + np + shapely)
          → LMP_wrapper.put_first_on_second()
            → PickPlaceEnv.step()
              → movep() → servoj() → PyBullet 位置控制
              → gripper.activate() / release()
              → step_sim_and_render()
                → render_image() → 视频帧缓存 + cv2 显示
```

## 关键设计点

1. **Code as Policies**：LLM 不是直接输出动作序列，而是输出完整的 Python 代码，通过 `exec_safe()` 在预注入的命名空间中执行
2. **递归函数生成**：`LMPFGen.create_new_fs_from_code()` 递归检查 LLM 生成代码中的所有函数调用，自动发现缺失函数并调用 LLM 生成，直到所有依赖满足
3. **安全执行**：`exec_safe()` 过滤 import 语句、禁止 `__` 私有属性访问、替换 `exec`/`eval` 为空函数
4. **错误重试**：`LMP.__call__()` 最多尝试 3 次，遇到 API 错误或执行错误后自动重试（逐步提高 temperature）
5. **会话维护**：`tabletop_ui` 的 `maintain_session=True`，会把之前执行过的代码追加到 prompt 中，支持多轮对话