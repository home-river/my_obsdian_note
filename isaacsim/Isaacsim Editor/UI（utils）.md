
**UI (utils) 组件的核心作用**

在 Isaac Sim / Omniverse 里，如果直接使用底层 `ui` API，你往往需要写很多样板代码来：

- 声明和布局各种控件（按钮、文本框、滑块、曲线图等）
    
- 把它们与数据模型绑定，编写回调函数
    
- 统一控件的颜色、尺寸、间距，使整块面板看起来一致
    
- 维护页眉/信息区、滚动区域、分隔线等“页面框架”元素
    

**`isaacsim.gui.components` 中的 UI (utils) 就是在这里“代你动手”**：  
它把常见的小部件及组合形式都封装成“一行生成”的 _builder/helper_ 函数，你只要传入文本、默认值、回调，就能立刻得到风格统一、功能完备的控件或整段 UI。

| 函数                                                                  | 作用简述                                              |
| ------------------------------------------------------------------- | ------------------------------------------------- |
| `add_folder_picker_btn`                                             | 为文本输入框附加“选择文件夹”按钮，选择后把路径传给 `on_click_fn`。         |
| `add_folder_picker_icon`                                            | 单独的文件夹图标按钮，点击回调同上。                                |
| `add_line_rect_flourish`                                            | 在当前布局中绘制横线＋矩形小装饰，用于视觉分隔。                          |
| `add_separator`                                                     | 轻量横向分隔线。                                          |
| `btn_builder`                                                       | 创建带标签 / tooltip 的按钮 (`ui.Button`)。                |
| `state_btn_builder`                                                 | 双态按钮，每次点击在两种文本与状态间切换。                             |
| `multi_btn_builder`                                                 | 批量生成同一行多个按钮并返回列表。                                 |
| `cb_builder`                                                        | 单个复选框 (`ui.CheckBox`)。                            |
| `multi_cb_builder`                                                  | 一行多复选框，返回模型列表。                                    |
| `dropdown_builder`                                                  | 单选下拉框，支持默认值与回调。                                   |
| `multi_dropdown_builder`                                            | 复选下拉框，返回所选项列表。                                    |
| `combo_cb_dropdown_builder`                                         | 复选框 + Dropdown 组合，可启/禁下拉框。                        |
| `color_picker_builder`                                              | RGBA 颜色选择器，返回颜色模型。                                |
| `str_builder`                                                       | 字符串输入框；可配置只读、多行、路径过滤等。                            |
| `int_builder` / `float_builder`                                     | 整数 / 浮点输入框，支持上下限、tooltip。                         |
| `combo_intfield_slider_builder` / `combo_floatfield_slider_builder` | Int/FloatField + Slider 同步控件。                     |
| `xyz_builder`                                                       | 一次生成 X/Y/Z 三个 FloatField。                         |
| `format_tt`                                                         | 将长字符串格式化成自动换行的 tooltip。                           |
| `plot_builder`                                                      | 创建静态折线/柱状图 (`ui.Plot`)。                           |
| `combo_cb_plot_builder`                                             | Checkbox‑controlled 动态 Plot。                      |
| `xyz_plot_builder`                                                  | 绘制 X/Y/Z 三通道曲线。                                   |
| `combo_cb_xyz_plot_builder`                                         | 复选框控制的 XYZ 曲线组，返回 (plot_list, model_list)。        |
| `progress_bar_builder`                                              | 生成进度条并返回其数值模型。                                    |
| `scrolling_frame_builder`                                           | 指定可视行数的滚动面板，附带“复制到剪贴板”。                           |
| `combo_cb_scrolling_frame_builder`                                  | 复选框启停的 ScrollingFrame。                            |
| `build_header`                                                      | 根据 `ext_path`/`file_path` 生成扩展顶部横幅（标题、源码跳转、文档链接）。 |
| `build_info_frame`                                                  | 可折叠的信息面板，用于概览说明或警告。                               |
| `build_simple_search`                                               | 文本栏 + 放大镜按钮的搜索条。                                  |
| `setup_ui_headers`                                                  | 一键插入标准扩展页眉（Logo、版本、文档链接）。                         |