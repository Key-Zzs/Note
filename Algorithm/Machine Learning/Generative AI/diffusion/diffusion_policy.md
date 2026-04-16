# Diffusion Policy

## [Diffusion Policy 源码解构](https://github.com/real-stanford/diffusion_policy)

1. 主程序工作空间 `diffusion_policy/workspace/`
    程序的“顶层”，负责管理整个实验的生命周期（初始化、训练、保存、评估）。

    - `base_workspace.py`: 所有任务的基类，定义了保存和加载 Checkpoint 的标准流程。
    - `train_diffusion_unet_hybrid_workspace.py`: 主程序入口。它把模型、优化器、数据集和仿真环境缝合在一起。在这个文件中，你可以看到 Train 循环和 Evaluate 循环。

2. 策略层 `diffusion_policy/policy/`
    这一层实现了具体算法（如 DP, IBC, LSTM-GMM）。它负责管理扩散过程的步数（Inference Steps）和噪声调度。

    - `base_policy.py` : 定义了 predict_action 接口，规定了输入观测、输出动作的标准格式。
    - `diffusion_unet_image_policy.py` : 核心文件。实现了基于图像输入的 DP。包含了：
      - `Normalizer`: 动作和观测的归一化处理。
      - `VisionEncoder`: 将视觉信息转为特征向量。
      - `Noise Scheduler`: 处理 DDPM/DDIM 的加噪和去噪调度。
    - `diffusion_unet_lowdim_policy.py` : 针对状态输入（非图像）的 DP 实现，逻辑与图像版类似但省去了视觉编码器。

3. 内部模型层 `diffusion_policy/model/`
   神经网络的具体架构，负责预测噪声或提取特征。

   - `diffusion_policy/model/diffusion/`    
     - `conditional_unet1d.py`: 最核心的网络代码。DP 默认使用 1D CNN 网络，因为它在处理时间序列动作时比传统的 2D UNet 更高效。
     - `transformer_for_diffusion.py`: 使用 Transformer 作为扩散模型的骨干网络（Alternative）。
   - `diffusion_policy/model/vision/`: 
     - `model_getter.py`: 根据配置自动创建 ResNet 或 ViT。
     - `crop_randomizer.py`: 实现视觉数据增强（如 Random Crop），这对于机器人泛化至关重要。
   - `diffusion_policy/model/common/`: 
     - `dict_of_tensor_mixin.py`: 一个工具类，让模型能方便地处理包含多个传感器输入的字典数据。

4. 数据处理层
    将原始录像和传感器数据转化为模型能训练的 Tensor。

    - `diffusion_policy/dataset/`
      - `base_dataset.py`: 规定了返回 obs 和 action 的标准形式。
      - `robomimic_replay_image_dataset.py`: 适配 Robomimic 格式的数据集。
      - `pusht_image_dataset.py`: 专门针对入门任务 Push-T 的数据集加载器。
    - `diffusion_policy/common/`
      - `replay_buffer.py`: 实现了一个高效的磁盘/内存缓冲区，用于存储和检索轨迹数据。
      - `sampler/sampler.py`: 关键逻辑。实现了“滑动窗口”采样，确保模型能拿到连续的 $T_{obs}$ 帧观测和 $T_{pred}$ 帧动作。

5. 仿真环境层 `diffusion_policy/env/` 和 `diffusion_policy/env_runner/`
    用于在训练过程中进行在线验证（Evaluation）。
    
    - `diffusion_policy/env/pusht/`: Push-T 任务的环境实现。
    - `diffusion_policy/env_runner/pusht_image_runner.py`: 负责在训练中断时跑几次仿真，计算 Success Rate 并记录到 Tensorboard/WandB。

6. 配置层 `diffusion_policy/config/`
    该项目大量使用 Hydra 进行配置管理。
    
    - `diffusion_policy/config/task/`: 定义不同任务（Push-T, Franka Kitchen 等）的参数。
    - `diffusion_policy/config/train_xxx_workspace.yaml`: 定义策略的超参数和训练的 Batch Size, Learning Rate 等。
