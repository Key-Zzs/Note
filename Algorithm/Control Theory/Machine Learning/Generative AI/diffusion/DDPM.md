# Diffusion Model

## 生成式 AI 架构

生成式 AI 常见的架构如下，通常需要三个部分：
1. **Encoder**: 将输入信息，如文本、图片等，编码为“中间产物”（latent representation）。文本一般编码为向量，图片可以编码为像素块或”小图“
2. **Generation Model**: 将输入的“中间产物”还原为生成信息的编码版本，即新生成的目标向量、像素块或“小图”，即新的“中间产物”；
3. **Decoder**: 将新的“中间产物”解码为生成信息

<img src="/Algorithm/Control Theory/Machine Learning/Generative AI/Pic/generative AI framework.png" alt="generative AI framework" width="800">

现有生成式 AI 大部分都采用该架构，如下：

<table>
    <tr>
        <td ><center><img src="/Algorithm/Control Theory/Machine Learning/Generative AI/Pic/Stable Diffusion framework.png" width=200/> Stable Diffusion framework </center></td>
        <td ><center><img src="/Algorithm/Control Theory/Machine Learning/Generative AI/Pic/DALL-E  framework.png" width=200/> DALL-E framework </center></td>
        <td><center><img src="/Algorithm/Control Theory/Machine Learning/Generative AI/Pic/Imagen framework.png" width=200/>Imagen framework</center></td>
    </tr>
</table>

## 理论基础

### 1 概率论与数理统计

#### 1.1 条件概率与贝叶斯公式

#### 1.2 大数定律与切比雪夫定律

#### 1.3 概率分布

### 2 FID(Frechet inception Distance)

用于评估模型生成信息与目标信息的差异程度

### 3 CLIP(Contrastive Language-lmage Pre-Training)

用于评估文本与图像的匹配程度

### 4 KL 散度

## Diffusion Model

### 架构

Diffusion Model 与生成式 AI 的思路一致，但架构中的 encoder 被替换为添加噪声的操作，decoder 被替换为 Denoise 的操作模块

<img src="/Algorithm/Machine Learning/Generative AI/Pic/Diffusion process.png" alt="Diffusion process" width="800">

其中，添加噪声的步骤称为 **forward diffusion process** ，Denoise 模块称为 **reverse diffusion process** ，其内部架构如下

<img src="/Algorithm/Machine Learning/Generative AI/Pic/Denoise.png" alt="Denoise" width="800">

### 算法流程及数学推导

discrete direction probability method(DDPM) 的训练流程与推理（sample）流程如下

<table>
    <tr>
        <td ><center><img src="/Algorithm/Machine Learning/Generative AI/Pic/DDPM Training.png" width=500/> DDPM Training </center></td>
        <td ><center><img src="/Algorithm/Machine Learning/Generative AI/Pic/DDPM Sampling.png" width=500/> DDPM Sampling </center></td>
    </tr>
</table>

#### 1 Training

![[DDPM Training.png]]

DDPM 的训练目标是让 diffusion model 中的 noise predictor 预测出含噪声图片中的噪声。

训练流程是取上一步的图片 $x$ （初始为原始干净照片），并取一个服从正态分布的噪声信号 $\epsilon$ ，将 $x$ 与 $\epsilon$ 做加权和得到含噪声图片，并对该步的 $x$ 与含噪图片的差异（后面会详细介绍如何评价分布的差异）做梯度下降，最终得到预测噪声。

下面介绍如何将多步的噪声预测推导为单步预测

#### 2 Sampling

![[DDPM Sampling.png]]

DDPM 单步的推理流程图如下，

![[DDPM Sampling framework.png]]


# Diffusion Policy



---

## 参考资料

### 文献

- [1] [Diffusion Policy: Visuomotor Policy Learning via Action Diffusion](https://arxiv.org/pdf/2303.04137v5)
- [2] [Understanding Diffusion Models: A Unified Perspective](https://arxiv.org/pdf/2208.11970)

### 学习资料

- [1] [扩散模型 - Diffusion Model【李宏毅2023】](https://www.bilibili.com/video/BV14c411J7f2/?p=3&share_source=copy_web&vd_source=20492a387166d54ed6b0a03ef2b28f1f)
