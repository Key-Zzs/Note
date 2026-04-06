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

