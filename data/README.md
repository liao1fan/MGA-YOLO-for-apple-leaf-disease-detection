# MGA-YOLO for apple leaf disease detection

## Dataset: Apple Leaf Disease Object Detection dataset (ALDOD)

## 1. Dataset structure

The structure of our dataset is as follows:

* data

  * images
    * 0.jpg
    * 1.jpg
    * ……
    * 8837.jpg
  * labels
    * 0.txt
    * 1.txt
    * ……
    * 8837.txt

  * train.txt
  * val.txt
  * test.txt
  * datasets.yaml
  * hyps



(1) 'images' contains 8838 images with complex environmental conditions.

(2) 'labels' contains 8838 annotation files, each corresponding to an image.

(3) 'train.txt' , 'val.txt' and 'test.txt' contains file names of all the images of training set, validation set and test set respectively. Each line of the file records an file name which corresponds to an image.

(4) 'datasets.yaml' is the dataset configuration file. It records the relative paths of the file 'train.txt', 'val.txt' and 'test.txt'. In addition, it records the number of classes of the images and class names. On ALDOD, the number of classes is 4 and class names are as follows:

```
names: ['apple_healthy','apple_black_rot','apple_scab'  ,'apple_rust' ]
```



(5) 'hyps' contains two configuration files of hyperparameters.

'hyp.scratch.yaml' records hyperparameters when using Mosaic image augmentation while 'hyp_without_DA.yaml' records hyperparameters when not using Mosaic.



## 2.Models

All the configuration files of YOLO networks  are included in directory 'Myhub'. We provide the structures of YOLO networks which we used in our ablation experiment. The result of the ablation experiment is as follows:

| Model                                            | mAP(%) | AP50(%) | parameters | FLOPs(G) | Weight(MB) |
| ------------------------------------------------ | ------ | ------- | ---------- | -------- | ---------- |
| YOLOv5s                                          | 90.3   | 95.6    | 7071633    | 16.4     | 14.064     |
| YOLOv5s-ghost                                    | 91.1   | 95.2    | 3703993    | 8.2      | 7.598      |
| YOLOv5s-ghost-C3MB                               | 92.2   | 95.8    | 5679145    | 11.3     | 11.466     |
| YOLOv5s-ghost-C3MB-SE                            | 91     | 95.8    | 8708233    | 11.4     | 17.391     |
| YOLOv5s-ghost-C3MB-CA                            | 91     | 95.6    | 6256381    | 11.4     | 12.617     |
| YOLOv5s-ghost-C3MB-CBAM                          | 92.5   | 96      | 6440256    | 11.4     | 12.946     |
| YOLOv5s-ghost-C3MB-CBAM-add_prediction_head      | 93.6   | 96.6    | 7641179    | 11.6     | 9.654      |
| YOLOv5s-ghost-C3MB-CBAM-add_prediction_head-GELU | 94     | 96.7    | 7641179    | 11.6     | 10.337     |

​		YOLOv5s-ghost-C3MB-CBAM-add_prediction_head-GELU denotes MGA-YOLO. The result shows that our proposed MGA-YOLO outperforms the baseline YOLOv5s.



(1) 'common.py' contains the common convolutional modules

(2) 'yolo.py' contains the YOLO modules. Running the main function in 'yolo.py' can view the structure of the whole network. In addition, parameters and FLOPs of the network are calculated and displayed on the console. 

## 3.Utils

'utils' contains all the tool functions to parse configuration files, define loss functions,  draw plots for results, etc.

All the codes in this section are from https://github.com/ultralytics/yolov5.



## 4.Model training and evaluation

### Training

'train.py' is referred to training models. On linux, we can run the command as follows:

```python train.py --batch 64 --epochs 200 --data ./data/datasets.yaml --cfg ./models/Myhub/yolov5s-ghost-C3MB-CBAM-add_prediction_head-GELU.yaml --hyp data/hyps/hyp.scratch.yaml --weights '' ```

The command above specifies the batch size as 64, the epochs as 200 and specifies the  configuration file of data, model and hyperparameters. Since we use the model configuration file to train the network instead of the pre-trained model, we specifies ```--weights``` as ```''```. Otherwise specifies the path to the pre-trained model on the parameter ```--weights```.

If don't specifies any parameters, 'train.py' will run with the default parameters.

The training results will save in the directory 'runs/train'. If you have never trained models, the directory './runs' will not exist.

### Evaluation

To evaluate the performance of the trained model, run the file 'val.py'. The results will save in the directory 'runs/val/'.

To detect apple leaf diseases using the trained model, run the file 'detect.py'. The results will save in the directory 'runs/detect/'.

Note that when we have finished training a model, we will get the results evaluated on the validation set. We need to use the 'val.py' file to obtain the results on the test set. The results on the test set illustrate the real performance of the model in practical applications.