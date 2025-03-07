# PVTV2
> [PVT v2: Improved Baselines with Pyramid Vision Transformer](https://arxiv.org/abs/2106.13797)

## Introduction

In this work, the authors present new baselines by improving the original Pyramid Vision Transformer (PVT v1) by adding
three designs, including (1) linear complexity attention layer, (2) overlapping patch embedding, and (3) convolutional
feed-forward network. With these modifications, PVT v2 reduces the computational complexity of PVT v1 to linear and
achieves significant improvements on fundamental vision tasks such as classification, detection, and
segmentation.[[1](#references)]

<p align="center">
  <img src="https://user-images.githubusercontent.com/53842165/219326579-de903edb-131f-4905-a3fe-7be2cb8cc8b7.png" width=500 />
</p>
<p align="center">
  <em>Figure 1. Architecture of PVTV2 [<a href="#references">1</a>] </em>
</p>

## Results

Our reproduced model performance on ImageNet-1K is reported as follows.

<div align="center">

| Model    | Context  | Top-1 (%) | Top-5 (%) | Params (M) | Recipe                                                                                         | Download                                                                                |
|----------|----------|-----------|-----------|------------|------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| PVTV2_b0 | D910x8-G | 71.50     | 90.60     | 3.67       | [yaml](https://github.com/mindspore-lab/mindcv/blob/main/configs/pvt_v2/pvt_v2_b0_ascend.yaml) | [weights](https://download.mindspore.cn/toolkits/mindcv/pvt_v2/pvt_v2_b0-1c4f6683.ckpt) |
| PVTV2_b1 | D910x8-G | 78.91     | 94.49     | 14.01      | [yaml](https://github.com/mindspore-lab/mindcv/blob/main/configs/pvt_v2/pvt_v2_b1_ascend.yaml) | [weights](https://download.mindspore.cn/toolkits/mindcv/pvt_v2/pvt_v2_b1-3ceb171a.ckpt) |

</div>

#### Notes

- Context: Training context denoted as {device}x{pieces}-{MS mode}, where mindspore mode can be G - graph mode or F - pynative mode with ms function. For example, D910x8-G is for training on 8 pieces of Ascend 910 NPU using graph mode.
- Top-1 and Top-5: Accuracy reported on the validation set of ImageNet-1K.

## Quick Start

### Preparation

#### Installation
Please refer to the [installation instruction](https://github.com/mindspore-ecosystem/mindcv#installation) in MindCV.

#### Dataset Preparation
Please download the [ImageNet-1K](https://www.image-net.org/challenges/LSVRC/2012/index.php) dataset for model training and validation.

### Training

* Distributed Training

It is easy to reproduce the reported results with the pre-defined training recipe. For distributed training on multiple Ascend 910 devices, please run

```shell
# distrubted training on multiple GPU/Ascend devices
mpirun -n 8 python train.py --config configs/pvt_v2/pvt_v2_b0_ascend.yaml --data_dir /path/to/imagenet
```

> If the script is executed by the root user, the `--allow-run-as-root` parameter must be added to `mpirun`.

Similarly, you can train the model on multiple GPU devices with the above `mpirun` command.

For detailed illustration of all hyper-parameters, please refer to [config.py](https://github.com/mindspore-lab/mindcv/blob/main/config.py).

**Note:**  As the global batch size  (batch_size x num_devices) is an important hyper-parameter, it is recommended to keep the global batch size unchanged for reproduction or adjust the learning rate linearly to a new global batch size.

* Standalone Training

If you want to train or finetune the model on a smaller dataset without distributed training, please run:

```shell
# standalone training on a CPU/GPU/Ascend device
python train.py --config configs/pvt_v2/pvt_v2_b0_ascend.yaml --data_dir /path/to/dataset --distribute False
```

### Validation

To validate the accuracy of the trained model, you can use `validate.py` and parse the checkpoint path with `--ckpt_path`.

```shell
python validate.py -c configs/pvt_v2/pvt_v2_b0_ascend.yaml --data_dir /path/to/imagenet --ckpt_path /path/to/ckpt
```

### Deployment

Please refer to the [deployment tutorial](https://github.com/mindspore-lab/mindcv/blob/main/tutorials/deployment.md) in MindCV.

## References

[1] Wang W, Xie E, Li X, et al. Pvt v2: Improved baselines with pyramid vision transformer[J]. Computational Visual Media, 2022, 8(3): 415-424.
