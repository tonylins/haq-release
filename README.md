
# HAQ: Hardware-Aware Automated Quantization with Mixed Precision

## Introduction

This repo contains PyTorch implementation for paper [HAQ: Hardware-Aware Automated Quantization with Mixed Precision](http://openaccess.thecvf.com/content_CVPR_2019/papers/Wang_HAQ_Hardware-Aware_Automated_Quantization_With_Mixed_Precision_CVPR_2019_paper.pdf) (CVPR2019, oral)

![overview](https://hanlab.mit.edu/projects/haq/images/overview.png)

```
@inproceedings{haq,
author = {Wang, Kuan and Liu, Zhijian and Lin, Yujun and Lin, Ji and Han, Song},
title = {HAQ: Hardware-Aware Automated Quantization With Mixed Precision},
booktitle = {IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
year = {2019}
}
```

Other papers related to automated model design:
- AMC: AutoML for Model Compression and Acceleration on Mobile Devices ([ECCV 2018](https://arxiv.org/abs/1802.03494))

- ProxylessNAS: Direct Neural Architecture Search on Target Task and Hardware ([ICLR 2019](https://arxiv.org/abs/1812.00332))

## Dependencies
We evaluate this code with Pytorch 1.1 (cuda10) and torchvision 0.3.0, you can install pytorch with conda:
```
# install pytorch
conda install -y pytorch torchvision cudatoolkit=10.0 -c pytorch
```
And you can use the following command to set up the environment:
```
# install packages
bash run/setup.sh
```
Current code base is tested under following environment:
1. Python         3.7.3
2. PyTorch        1.1
3. torchvision    0.3.0
4. numpy          1.14
5. matplotlib     3.0.1
6. scikit-learn   0.21.0
7. easydict       1.8
8. progress       1.4
9. tensorboardX   1.7

## Dataset
If you already have the ImageNet dataset for pytorch, you could create a link to data folder and use it:
```
# prepare dataset, change the path to your own
ln -s /path/to/imagenet/ data/
```
If you do not have the ImageNet yet, you can download the ImageNet dataset and move validation images to labeled subfolders. To do this, you can use the following script: 
[https://raw.githubusercontent.com/soumith/imagenetloader.torch/master/valprep.sh](https://raw.githubusercontent.com/soumith/imagenetloader.torch/master/valprep.sh)


## Reinforcement learning search
- You can run the bash file as following to search the quantization strategy for specific model.
```
bash run/run_search.sh
```
- Usage details
```
python rl_quantize.py --help
```

## Finetune Policy
- After searching, you can get the quantization strategy list, and you can replace the strategy list in **finetune.py** to finetune and evaluate the performance on ImageNet dataset.
- We set the default quantization strategy searched under preserve ratio = 0.1 like:
```
# preserve ratio 10%
strategy = [6, 6, 5, 5, 5, 5, 4, 5, 5, 4, 5, 5, 5, 5, 5, 5, 3, 5, 4, 3, 5, 4, 3, 4, 4, 4, 2, 5, 4, 3, 3, 5, 3, 2, 5, 3, 2, 4, 3, 2, 5, 3, 2, 5, 3, 4, 2, 5, 2, 3, 4, 2, 3, 4]
```
You can follow the following bash file to finetune the quantized model to get a better performance:
```
bash run/run_finetune.sh
```
- Usage details
```
python finetune.py --help
```
## Evaluate
You can download the pretrained quantized model and evaluate it.
```
# download checkpoint
mkdir -p checkpoints/resnet50/
cd checkpoints/resnet50/
wget https://hanlab.mit.edu/files/haq/resnet50_0.1_75.48.pth.tar
cd ../..
# evaluate 
bash run/run_eval.sh
```

| Models                   | preserve ratio | Top1 Acc (%) | Top5 Acc (%) |
| ------------------------ | -------------- | ------------ | ------------ |
| resnet50 (original)   |       1.0      |     76.15    |    92.87     |
| resnet50 (compress10x)|       0.1      |     75.48    |    92.42     |
