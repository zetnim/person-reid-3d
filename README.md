# Person Re-id in the 3D Space

![Python 3.6](https://img.shields.io/badge/python-3.6-green.svg)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

![](https://github.com/layumi/person-reid-3d/blob/master/imgs/demo-1.jpg)

Thanks for your attention. In this repo, we provide the code for the paper [[Person Re-identification in the 3D Space ]](https://arxiv.org/abs/2006.04569).

## News
- You may directly download my generated 3D data at [[OneDrive]](https://studentutsedu-my.sharepoint.com/:u:/g/personal/12639605_student_uts_edu_au/EQXEskhdd3xPjdFRxAUtB9cB7RkjAdzS5YXRH8QIf_TWAw?e=IhqNpi) or [[GoogleDrive]](https://drive.google.com/file/d/1ih-LrkdGUvNK3rEUNJIq4LTvcgVOXnnM/view?usp=sharing), and therefore you could skip the data preparation part.

## Prerequisites
- Python 3.6 or 3.7
- GPU Memory >= 4G (e.g., GTX1080)
- Pytorch = 1.4.0 (Not Latest. Latest version is incompatible, since it changes the C++ interfaces.)
- dgl 

## Install 
Here I use the cuda10.1 by default.
```setup
conda create --name OG python=3.7
conda activate OG
conda install pytorch=1.4.0 torchvision=0.5.0 cudatoolkit=10.1 -c pytorch
pip install dgl-cu101
pip install -r requirements.txt
```

## Prepare Data 
Download Market-1501, DukeMTMC-reID or MSMT17 and unzip them in the `../`

Split the dataset and arrange them in the folder of ID.
```bash 
python prepare_market.py
python prepare_duke.py
python prepare_MSMT.py
```

Link the 2DDataset 
```bash 
ln -s ../Market/pytorch  ./2DMarket
ln -s ../Duke/pytorch  ./2DDuke
ln -s ../MSMT/pytorch  ./2DMSMT
```

Generate the 3D data via the code at https://github.com/layumi/hmr 
(I modified the code from https://github.com/akanazawa/hmr)

## Training 
- Market-1501

**OG-Net**
```bash
python train_M.py --batch-size 8 --name ALL_Dense_b8_lr3.5_flip_slim0.5_warm5_scale_e0_d7+bg_adam_init768_clusterXYZRGB --slim 0.5 --flip --scale  --lrRate 3.5e-4 --gpu_ids 0 --warm_epoch 5  --erase 0  --droprate 0.7   --use_dense  --bg   --adam  --init 768  --cluster xyzrgb  --train_all
```
**OG-Net-Small**
```bash
python train_M.py --batch-size 8 --name ALL_SDense_b8_lr3.5_flip_slim0.5_warm5_scale_e0_d7+bg_adam_init768_clusterXYZRGB --slim 0.5 --flip --scale  --lrRate 3.5e-4 --gpu_ids 0 --warm_epoch 5  --erase 0  --droprate 0.7   --use_dense  --bg   --adam  --init 768  --cluster xyzrgb  --train_all     --feature_dims 48,96,192,384
```

- DukeMTMC-reID

**OG-Net**
```bash
python train_M.py --batch-size 8 --name ALL_Duke_Dense_b8_lr3.5_flip_slim0.5_warm5_scale_e0_d7+bg_adam_init768_clusterXYZRGB --slim 0.5 --flip --scale  --lrRate 3.5e-4 --gpu_ids 0 --warm_epoch 5  --erase 0  --droprate 0.7   --use_dense  --bg   --adam  --init 768  --cluster xyzrgb  --dataset-path 2DDuke  --train_all
```
**OG-Net-Small**
```bash
python train_M.py --batch-size 8 --name ALL_Duke_SDense_b8_lr3.5_flip_slim0.5_warm5_scale_e0_d7+bg_adam_init768_clusterXYZRGB --slim 0.5 --flip --scale  --lrRate 3.5e-4 --gpu_ids 0 --warm_epoch 5  --erase 0  --droprate 0.7   --use_dense  --bg   --adam  --init 768  --cluster xyzrgb  --train_all    --feature_dims 48,96,192,384 --dataset-path 2DDuke
```

- MSMT-17

**OG-Net**
```bash
python train_M.py --batch-size 8 --name MSMT_Dense_b8_lr3.5_flip_slim0.5_warm5_scale_e0_d7+bg_adam_init768_clusterXYZRGB --slim 0.5 --flip --scale  --lrRate 3.5e-4 --gpu_ids 0 --warm_epoch 5  --erase 0  --droprate 0.7   --use_dense  --bg   --adam  --init 768  --cluster xyzrgb  --dataset-path 2DMSMT
```
**OG-Net-Small**
```bash
python train_M.py --batch-size 8 --name ALL_MSMT_SDense_b8_lr3.5_flip_slim0.5_warm5_scale_e0_d5+bg_adam_init768_clusterXYZRGB --slim 0.5 --flip --scale  --lrRate 3.5e-4 --gpu_ids 0 --warm_epoch 5  --erase 0  --droprate 0.5   --use_dense  --bg   --adam  --init 768  --cluster xyzrgb  --dataset-path 2DMSMT  --train_all  --feature_dims 48,96,192,384
```

## Evaluation
- Market-1501
```bash 
python test_M.py  --name  ALL_Dense_b8_lr3.5_flip_slim0.5_warm5_scale_e0_d7+bg_adam_init768_clusterXYZRGB
```

- DukeMTMC-reID
```bash 
python test_M.py  --data 2DDuke --name  ALL_Duke_Dense_b8_lr3.5_flip_slim0.5_warm5_scale_e0_d7+bg_adam_init768_clusterXYZRGB
```

- MSMT-17
```bash 
python test_MSMT.py  --name MSMT_Dense_b8_lr3.5_flip_slim0.5_warm5_scale_e0_d7+bg_adam_init768_clusterXYZRGB
```

## Pre-trained Models
Since OG-Net is really small, I has included trained models in this github repo `./snapshot`. 

## Results
### [Person Re-ID Performance]
| Model name         | Market  | Duke | MSMT|
| ------------------ |---------------- | -------------- | -------------- |
| OG-Net-Small |  80.85(59.56) |  70.11(49.93)     |   34.87(14.57)   | 
| OG-Net   |    80.94(59.97)  |   71.77(50.81) |  36.37(15.74)    |

### [ModelNet Performance] 
I add OG-Net code to https://github.com/layumi/dgcnn  
Results on ModelNet are 92.02 Top1 Accuracy / 88.84 MeanClass Top1 Accuracy.


## Citation
You may cite it in your paper. Thanks a lot.
```bibtex
@article{zheng2020person,
  title={Person Re-identification in the 3D Space},
  author={Zhedong Zheng, Yi Yang},
  journal={arXiv 2006.04569},
  year={2020}
}
```

## Related Work
We thank the great works of DGL, DGCNN and PointNet++. You may check their code at
- https://github.com/dmlc/dgl/tree/master/examples/pytorch/pointcloud
- https://github.com/WangYueFt/dgcnn
- https://github.com/erikwijmans/Pointnet2_PyTorch
