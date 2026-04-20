# 项目说明文档：Impression: Me

## 1. 概念：数据故事与主题选择

**项目名称**：Impression: Me
**数据故事**：我们每天无意识选择的穿搭颜色、所处环境的色调以及当下的心情表现，是否在某种纬度上与艺术史上的流派或画家存在相似性？本项目试图通过色彩这一基本视觉元素，将个体在 7 天中的“微观生活切片”与 19 世纪印象派的“宏观画作数据集”进行叠合。
**选择初衷**：受到 “Dear Data” 项目的启发，将个人化、情感化的日常记录（如情绪和衣着）进行数据化，但不再局限于二人之间的通信，而是将这种个人数据抛入庞大的艺术史坐标系中进行校验，从而在冰冷的数据工程与主观的个人审美之间建立一种理性的结构轴线。

## 2. 数据：来源、结构与处理

**数据来源**：

- **宏观数据集 (Macro Data)**：来源于 Kaggle 的 “Impressionist Classifier Data”，包含 10 位著名画家（如塞尚、梵高、莫奈等）的约 5000 幅画作，代表艺术史上的色彩样本。

![image-20260420190327662](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420190327662.png)

- **微观数据集 (Micro Data)**：由作者本人在不连续的 7 天中，手工记录产生。每天包含 7 个数据维度：穿搭（上衣、裤子、鞋子、配饰，共 5 张图片）、心情（两组代表色）、环境主色系（1 张图片），共计 49 个数据点。

![image-20260420190446252](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420190446252.png)

![image-20260420190503861](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420190503861.png)

![image-20260420190520677](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420190520677.png)

![image-20260420190537104](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420190537104.png)

![image-20260420190555359](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420190555359.png)

**处理与特征提取过程（全部依靠Vibe Coding实现）**：

![image-20260420190643001](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420190643001.png)

1. 使用 Python 和 OpenCV 对5000幅画作进行遍历，提取每幅画作的平均特征色，并将其从 RGB 转换至更符合人眼感知的 CIE-LAB 色彩空间。
2. 利用 UMAP 降维算法，基于画作的 LAB 色相距离将高维特征投射至二维 (x, y) 平面，生成散点坐标。
3. 对于个人的 7 天微观数据，通过同样的色彩空间映射公式转换为 LAB 空间下的数据点，使其能无缝并入宏观数据的 UMAP 坐标系中。
4. 最终输出前后端解耦的 `macro_data.json` 与 `micro_data.json` 供前端读取。

![image-20260420190737977](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420190737977.png)

![image-20260420190826632](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420190826632.png)

## 3. 叙事逻辑与交互设计

**叙事逻辑**：
采用“宏观(背景) -> 微观(个体) -> 碰撞(比对)”的漏斗式叙事。页面划分为三大主体：左侧展现全局星云图与个体时间序列，中间呈现微观到宏观的对比结果，右侧开放扩展接口，供使用者上传自己的数据进行分析处理。所有设计遵循克制的工业面板风格。

**核心交互意图**：

- **底栏 Day 1 ~ Day 7 切换按钮（时间切片控制）**：
  *意图*：作为全局唯一的交互控制源。点击特定天数，一方面在时间序列图中高亮当天色块，另一方面驱动星云图中属于该天的7个个体验本点高亮弹出。

![image-20260420193659978](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420193659978.png)

- **星云图节点点击与 Similarity Match 弹窗**：
  *意图*：当个人数据点高亮时，点击该点会在星云中通过连线的方式找到绝对距离最接近的一幅艺术画作，并在右上角弹窗揭示原图与作者。

![image-20260420193622158](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420193622158.png)

- **最优拟合**：
  *意图*：当点击特定天数时，对应的颜色会随机生成一幅蒙德里安风格的色块艺术，并计算出相似风格的艺术家和平均 LAB 距离。

![image-20260420191156311](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420191156311.png)

- **风格磁贴筛选**：
  *意图*：允许用户点击具体画家姓名，过滤掉其他画派，以观察单一画家在 UMAP 色彩降维图中的聚集模式。

![image-20260420191034918](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420191034918.png)

- **上传界面**：
  *意图*：打破7天固定数据的封闭性。观者可以实时上传当天的服装图片并选择当前心情，网页利用原生 Canvas 实时提取平均像素特征，生成“第8天”的动态数据并投射参与运算。让叙事具有延续性。

![image-20260420191051638](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420191051638.png)

## 4. 技术实现路径

- **前端框架与工具**：使用 Vite 作为构建工具，采用原生 Vanilla JS + CSS3 实现纯浏览器客户端渲染。

- **数据可视化库**：

  - **D3.js**：用于构建底层数据逻辑与坐标映射（如 `d3.scaleBand` 用于脉络图的分区，`d3.scaleLinear` 用于坐标系放缩转化，`d3.stack` 用于处理堆叠数据）。
  - **Canvas & SVG 混合渲染**：5000个宏观数据点使用 HTML5 Canvas 绘制以保障渲染帧率，而交互高光层、点连线、蒙德里安色块基于 SVG 与原生 DOM 以方便响应点击等交互事件。
  - **GSAP**：用于处理点位在激活与失焦时的平滑缓动动画。

  ![image-20260420191556746](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20260420191556746.png)

- **色彩特征对齐**：使用欧氏距离公式在 LAB 色彩空间计算个人色彩矩阵与画作色彩的差值，找出最优拟合艺术家。

## 5. 创作反思

**最满意的部分**：
在于技术底层结构与 UI 视觉语汇的高度统一。针对极庞大的散点数据，成功采用 Canvas + SVG 缝合的方案兼顾了性能与复杂交互；同时在界面视觉上，运用了严谨的三栏布局，结合边框与合理的字体排版，做到了既美观简约，又忠于数据本身。

**最具挑战的部分**：
最开始的**创意构想、图像设计和布局阶段**是最具挑战性的，在这个阶段中，目前的AI工具只能提供一些灵感，而设计者需要整合这些庞杂的信息，使整个项目变得新颖有趣。除此之外，**Vibe Coding** 本身是一件具有挑战性的事情，提示词的撰写，产出结果的测试评估都花费了不少时间。
