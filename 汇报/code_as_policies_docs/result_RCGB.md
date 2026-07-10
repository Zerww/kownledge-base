# Robot Code-Gen Benchmark 结果

## 实验概述

- **模型**: DeepSeek Chat (`deepseek-chat`)
- **问题数量**: 37 (涵盖 numpy 运算、控制理论、shapely 几何、API 调用)
- **策略**: 4 种组合方式 (Code-Gen × Prompt)

## 总体结果

| Code-Gen 策略 | Prompt 类型 | 成功率 | 成功数/总数 |
|---|---|---|---|
| Hierarchical | Hierarchical | **51%** | 19/37 |
| Hierarchical | Flat | **70%** | 26/37 |
| Flat | Hierarchical | **49%** | 18/37 |
| Flat | Flat | **70%** | 26/37 |

## 按类别详细结果

### Hierarchical Code-Gen × Hierarchical Prompt (19/37)

| 类别 | 成功率 | 详情 (1=成功, 0=失败) |
|---|---|---|
| numpy 运算 (16题) | 12/16 | 11111111 01101000 |
| 控制理论 (4题) | 2/4 | 0110 |
| Shapely 几何 (10题) | 2/10 | 00000011 00 |
| API 调用 (7题) | 4/7 | 1110100 |

### Hierarchical Code-Gen × Flat Prompt (26/37)

| 类别 | 成功率 |
|---|---|
| numpy 运算 (16题) | 16/16 |
| 控制理论 (4题) | 1/4 |
| Shapely 几何 (10题) | 3/10 |
| API 调用 (7题) | 6/7 |

### Flat Code-Gen × Hierarchical Prompt (18/37)

| 类别 | 成功率 | 详情 (1=成功, 0=失败) |
|---|---|---|
| numpy 运算 (16题) | 12/16 | 11111111 01101000 |
| 控制理论 (4题) | 2/4 | 0110 |
| Shapely 几何 (10题) | 0/10 | 00000000 00 |
| API 调用 (7题) | 4/7 | 1110100 |

### Flat Code-Gen × Flat Prompt (26/37)

| 类别 | 成功率 | 详情 (1=成功, 0=失败) |
|---|---|---|
| numpy 运算 (16题) | 16/16 | 11111111 11111111 |
| 控制理论 (4题) | 1/4 | 0010 |
| Shapely 几何 (10题) | 3/10 | 00000011 00 |
| API 调用 (7题) | 6/7 | 1111110 |

## 问题列表

| # | 函数签名 | 类别 |
|---|---|---|
| 1 | `idx = get_top_most_idx(points_np)` | numpy |
| 2 | `idx = get_bottom_most_idx(points_np)` | numpy |
| 3 | `idx = get_left_most_idx(points_np)` | numpy |
| 4 | `idx = get_right_most_idx(points_np)` | numpy |
| 5 | `idx = get_farthest_idx(points_np, point_np)` | numpy |
| 6 | `idx = get_closest_idx(points_np, point_np)` | numpy |
| 7 | `area = get_bbox_xyxy_area(bbox_xyxy)` | numpy |
| 8 | `contains = bbox_xyxy_contains_pt(bbox_xyxy)` | numpy |
| 9 | `pts = interpolate_pts_np(start, end, n)` | numpy |
| 10 | `pts = reverse_pts(pts_np)` | numpy |
| 11 | `normalized_vector = normalize_vector(vector)` | numpy |
| 12 | `new_pts_np = translate_pts_np(pts_np, delta_np)` | numpy |
| 13 | `new_pts_np = rotate_pts_around_pts_center_np(pts_np, angle_deg)` | numpy |
| 14 | `new_pts_np = scale_pts_around_centroid_np(pts_np, scale_x=1.5, scale_y=1.5)` | numpy |
| 15 | `(poly_x_coeffs, poly_y_coeffs) = fit_2d_np_polys(ts, pts_2d_np, deg)` | numpy |
| 16 | `pts_2d_np = evaluate_pts_2d_from_poly_coeffs(poly_x_coeffs, poly_y_coeffs, ts)` | numpy |
| 17 | `u = pd_control(x_curr, x_goal, x_dot, Kp, Kv)` | control |
| 18 | `tau = end_effector_impedance_control(x_curr, x_goal, x_dot, K_x_mat, D_x_mat, J)` | control |
| 19 | `is_stable = is_discrete_system_stable(A_mat)` | control |
| 20 | `is_stable = is_closed_loop_discrete_system_stable(A_mat, B_mat, K_mat)` | control |
| 21 | `point_np = get_closest_point_on_line_to_point(line, pt_np)` | shapely |
| 22 | `direction = get_direction_orthogonal_to_line(line)` | shapely |
| 23 | `direction = get_direction_orthogonal_to_line_to_point(line, pt_np)` | shapely |
| 24 | `points_np = get_points_from_polygon(polygon)` | shapely |
| 25 | `pts_coords = interpolate_pts_along_exterior(exterior=shape.exterior, n=5)` | shapely |
| 26 | `pts_coords = interpolate_pts_on_line(line, n)` | shapely |
| 27 | `line = make_line(start_pt_np, end_pt_np)` | shapely |
| 28 | `circle = make_circle(radius, center)` | shapely |
| 29 | `rectangle = make_rectangle(width, height, center)` | shapely |
| 30 | `ellipse = make_ellipse(center, major_axis, minor_axis)` | shapely |
| 31 | `ret_val = obj_shape_does_not_contain_others(obj_name, other_obj_names)` | API |
| 32 | `ret_val = is_obj0_bigger_than_obj1(obj0_name, obj1_name)` | API |
| 33 | `bbox_xyxy = get_one_bbox_xyxy_of_all_objs(all_obj_names)` | API |
| 34 | `ret_val = are_obj_centers_close(obj_names, threshold)` | API |
| 35 | `obj_name = get_name_of_biggest_obj(obj_names)` | API |
| 36 | `ret_val = do_any_object_shapes_intersect_with_each_other(obj_names)` | API |
| 37 | `obj_name = get_name_of_obj_with_min_dist_to_pt(pt_np, obj_names)` | API |

## 分析

1. **Flat Prompt 优于 Hierarchical Prompt**: 两种 Code-Gen 策略下，Flat Prompt 均比 Hierarchical Prompt 高出约 20 个百分点。DeepSeek 在简洁的 Flat Prompt 下表现更好。

2. **numpy 类问题表现最佳**: Flat Code-Gen + Flat Prompt 在 16 个 numpy 问题上全部通过 (100%)。

3. **Shapely 几何问题是主要瓶颈**: 所有策略在 shapely 问题上表现较差，原因包括 DeepSeek 生成的代码包含 `from utils import get_obj_outer_pts_np`（已被 `exec_safe` 屏蔽）、以及调用了 prompt 中定义的辅助函数（如 `translate_pt_np`, `make_line`）时需要递归生成子函数。

4. **Flat Code-Gen 与 Hierarchical 各有利弊**: Flat 策略 (不递归生成子函数) 在 Flat Prompt 下表现一致，但在 Hierarchical Prompt 下表现最差 (49%)，说明生成的代码依赖未定义的辅助函数时无法回退。
