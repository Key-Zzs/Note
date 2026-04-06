# Diffusion Model

## 生成式 AI 架构

生成式 AI 常见的架构如下，通常需要三个部分：
1. **Encoder**: 将输入信息，如文本、图片等，编码为“中间产物”（latent representation）。文本一般编码为向量，图片可以编码为像素块或”小图“
2. **Generation Model**: 将输入的“中间产物”还原为生成信息的编码版本，即新生成的目标向量、像素块或“小图”，即新的“中间产物”；
3. **Decoder**: 将新的“中间产物”解码为生成信息

<img src="/Algorithm/Machine Learning/Generative AI/Pic/generative AI framework.png" alt="generative AI framework" width="800">

现有生成式 AI 大部分都采用该架构，如下：

<table>
    <tr>
        <td ><center><img src="/Algorithm/Machine Learning/Generative AI/Pic/Stable Diffusion framework.png" width=200/> Stable Diffusion framework </center></td>
        <td ><center><img src="/Algorithm/Machine Learning/Generative AI/Pic/DALL-E  framework.png" width=200/> DALL-E framework </center></td>
        <td><center><img src="/Algorithm/Machine Learning/Generative AI/Pic/Imagen framework.png" width=200/>Imagen framework</center></td>
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

其中，添加噪声的步骤称为 **forward process** ，Denoise 模块的内部架构如下

<img src="/Algorithm/Machine Learning/Generative AI/Pic/Denoise.png" alt="Denoise" width="800">

### 算法流程及数学推导

discrete direction probability method(DDPM)

<table>
    <tr>
        <td ><center><img src="/Algorithm/Machine Learning/Generative AI/Pic/DDPM Training.png" width=500/> DDPM Training </center></td>
        <td ><center><img src="/Algorithm/Machine Learning/Generative AI/Pic/DDPM Sampling.png" width=500/> DDPM Sampling </center></td>
    </tr>
</table>


