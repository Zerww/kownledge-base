# LMP 的概念

**代表一个功能块，通过一个简单的示例生成python代码**

```
tabletop_ui (LMP 功能块)
→ 生成高层策略代码，里面调用 parse_obj(...)
parse_obj (LMP 功能块)
→ 生成解析代码，里面调用get_objs_bigger_than_area_th(...)
function_generation (LMP 功能块)
→ 发现函数未定义，生成 def get_objs_bigger_than_area_th(...)
get_pos(), put_first_on_second() (API，不是 LMP)
→ 人工实现，被生成代码调用
```

- LMP 可分为 低级LMP 和 高级LMP。
低级LMP包含基础Python语句，第三方库以及第一方库的使用。
高级LMP包含控制循环，组合LMPs, 分层式生成代码。
注意：我们常说的LMP是高级的LMP。

# CaP Model

CaP的代码生成以及推理主要使用LLM。论文中做了横向比对。

| 方法| 感知方式| LLM 是否直接用感知结果              |
| ------------------------- | ---------------------------- | -------------------------- |
| Huang et al.(直接使用LLM，无感知) | 仿真符号状态，几乎无视觉                 | 否                          |
| SayCan(不将感知数据用于推理输入)      | RGB → 技能价值函数                 | 否（LLM 只选技名）                |
| Socratic Models           | VLM 把视觉信息写进 prompt           | 间接（文本形式）                   |
| CaP                       | 检测器 API（MDETR/ViLD）→ 代码里直接调用 | 是(get_pos(), detect_object()) |

# CaP primitive
CaP原语 = 感知原语 + 控制原语

**切记不要把原语和高层封装的函数进行混淆，也不要和LMP进行混淆**


# 总结

- 对于每一个LMP进行代码生成都有对应的prompt,在针对新任务进行操作时，需要为LMP写新的prompt以适应新的操作环境，并非真正能够自我适应。
- 该框架的强大之处在于针对已有的原语进行功能的泛化，但是超出已有原语能够执行的任务则无法解决
- 速度慢，不适合实时系统
- 跨平台能力弱
- 无错误反馈调整再执行

