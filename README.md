# SAM调研

## 定义
    
- "Segment Anything Model"（SAM）是一种通用的模型，它专注于图像分割任务。图像分割是计算机视觉领域的重要任务，旨在将图像中的不同对象或区域分割出来，从而实现对图像内容的理解和分析。
SAM 架构如下所示，主要包含三个部分：图像编码器；提示编码器；以及掩码解码器。
![图片](https://pic2.zhimg.com/80/v2-6db32205ff49ee14fae5163078aed0d1_720w.webp)

- 1. 图像编码器
在最高级别，图像编码器（掩码自动编码器、MAE、预训练视觉变换器、ViT）生成一次性图像嵌入，可以在提示模型之前应用。

2. 提示编码器
提示编码器将背景点、遮罩、边界框或文本实时编码到嵌入向量中。 该研究考虑了两组提示：稀疏（点、框、文本）和密集（掩码）。
点和框由位置编码表示，并为每种提示类型添加学习嵌入。 自由格式的文本提示由来自 CLIP 的现成文本编码器表示。 密集提示，如蒙版，嵌入卷积并与图像嵌入逐元素求和。

3. 掩码解码器
轻量级掩码解码器根据来自图像和提示编码器的嵌入预测分割掩码。 它将图像嵌入、提示嵌入和输出标记映射到掩码。 所有嵌入都由解码器块更新，解码器块在两个方向（从提示到图像嵌入和返回）使用提示自我注意和交叉注意。
掩码被注释并用于更新模型权重。 这种布局增强了数据集，并允许模型随着时间的推移学习和改进，使其高效灵活。

- ***SAM 用于图像处理***
这部分主要分场景进行介绍，包括：软件场景、真实场景以及复杂场景。
1. 软件场景:软件场景需要对图像编辑和修复进行操作，例如移除对象、填充对象和替换对象。
2. 真实场景:研究者表示 SAM 具有协助处理许多真实世界场景的能力，例如真实世界的物体检测、物体计数以及移动物体检测场景。
3. 复杂场景:除了上述的常规场景，SAM 是否能解决复杂场景（如低对比度场景）中的分割问题，也是一个有意义的问题，可以扩大其应用范围。

## 原理
SAM 模型的核心思想包括以下几个方面：
1. 多尺度特征提取： SAM 使用多尺度特征提取器来捕获图像中不同层次的信息，从而有效地表示图像内容。
2. 注意力机制： SAM 引入了注意力机制，以便模型能够在图像的不同部分进行聚焦，并更好地理解对象的位置和形状。
3. 语义信息融合： SAM 通过融合语义信息来提高分割性能。这包括利用预训练的语义分割模型或其他先验知识来指导分割过程。
4. 端到端训练： SAM 采用端到端的训练方式，可以直接从原始图像到分割结果进行学习，避免了手工设计特征和后处理步骤。
5. 数据增强和正则化： SAM 使用数据增强和正则化技术来提高模型的泛化能力，并减少过拟合的风险。

## 应用

### 视觉相关

#### 医疗成像。
医疗图像分割的目的是展示相应组织的解剖或病理结构，可以用于计算机辅助诊断和智能临床手术。
下图为医疗图像 SAM 概览，包括了计算机断层扫描（CT）图像、磁共振成像（MRI）图像、结肠镜检查图像、多格式图像、H&E 染色组织切片图像等。
![图片](https://pic2.zhimg.com/80/v2-ef5f6d5ce6c2e525fedb47fdba84b0f5_720w.webp)

#### 视频。
在计算机视觉领域，视频目标跟踪（VOT）和视频分割被认为是至关重要且不可或缺的任务。VOT 涉及在视频帧中定位特定目标，然后在整个视频的其余部分对其进行跟踪。因此，VOT 具有各种实际应用，例如监视和机器人技术。例子：跟踪一切模型（Track Anything Model, TAM），高效地在视频中实现了出色的交互式跟踪和分割。
![图片](https://pic4.zhimg.com/80/v2-c6b0c3451c11c002875ce0acd23576cf_720w.webp)

#### 数据注释。
SAMText是一种用于视频中场景文本掩码注释的可扩展 pipeline。它利用 SAM 在大型数据集 SAMText-9M 上生成掩码注释，该数据集包含超过 2,400 个视频片段和超过 900 万个掩码注释。

### 视觉之外

#### 3D 重建。
除了实现细粒度的 3D 分割，SA3D可以用于 3D 重建。利用 3D 掩码网格，研究者可以确定物体在 3D 中的占用空间，并以各种方式重建。
下图为 SA3D 的整体 pipeline。

![图片](https://pic2.zhimg.com/80/v2-23082b99be2394251297b707bb1ae6dd_720w.webp)


#### 非欧式域。
为了为不同任务处理不同特征维度，下图 16 中所示的 SNA 方法引入了一个专门的可精简图卷积层。该层可以根据输入的特征维度进行通道的动态激活或停用。
![图片](https://pic2.zhimg.com/80/v2-4462c5131e212af338c892c265e14289_1440w.webp)

#### 机器人。
下图 展示了 Instruct2Act的整体流程。在感知部分，预定义的 API 用于访问多个基础模型。SAM准确定位候选对象，CLIP对它们进行分类。该框架利用基础模型的专业知识和机器人能力将复杂的高级指令转换为精确的策略代码。
![图片](https://pic4.zhimg.com/80/v2-2e8aad4ed21525c2a521650113eef2d3_720w.webp)


#### 视频文本定位。
下图展示了一种为视频文本定位任务生成掩码注释的可扩展高效解决方案 SAMText。通过将 SAM 模型应用于边界框注释，它可以为大规模视频文本数据集生成掩码注释。

![图片](https://pic3.zhimg.com/80/v2-c8fdb0db633d1561dbf8eff8c4d10356_720w.webp)

#### 图像字幕。
Wang et al.提出了一种用于可控图像字幕的方法 Caption Anything（CAT），如下图所示，CAT 的框架将多模态控制引入图像字幕，呈现符合人类意图的各种视觉焦点和语言风格。
![图片](https://pic4.zhimg.com/80/v2-27bf55cb92ce3288dbf4fbe72a5f9ca7_720w.webp)

#### 视听也有涉及。
视听定位和分割方法用于学习可以对齐音频和视觉信息的跨模态表示，具体如下图所示。AV-SAM 利用预训练音频编码器和图像编码器中跨音频和视觉特征的像素级视听融合来聚合跨模态表示。然后将聚合的跨模态特征输入 prompt 编码器和掩码解码器，生成最终的视听分割掩码。

![图片](https://pic4.zhimg.com/80/v2-024f7c0d91c8017138da1ac6ff874ecb_720w.webp)

#### 多模态视觉和开放词汇交互分割。
旨在使用仅文本输入的 CLIP 策略来完全取代手动点（manual point）。这种方法提供来自文本输入的像素级结果，可以很容易地转换为 SAM 模型的点 prompt。

![图片](https://pic4.zhimg.com/80/v2-ed12b4d91fb4412a46bb0fe2e902f333_720w.webp)
