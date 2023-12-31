# DivNEDS: Diverse Naturalistic Edge Driving Scene Dataset for Autonomous Vehicle Scene Understanding
DivNEDS comprises **11,084** scenarios comprising a wide range of diverse naturalistic edge situations annotated with **203,000** descriptive captions. These scenarios comprise edge situations such as unpredictable maneuvers by other drivers, impending accidents, erratic movement of pedestrians, cyclist and motorcyclists, animal crossings, construction zones and cyclists using hand signals.

### Embedded Hierarchical Dense Captioning Strategy
We use hierarchical annotations with the lowest level approximating object detection annotations with attributes embedded within middle-level annotations. In Figure 1, the low-level bounding box is captioned, "cyclist in black shirt" and is embedded within a middle-level bounding box with the caption, "cyclist in black shirt riding a bicycle signaling right turn". Middle-level annotations describe relationships and actions of objects in the road scene. The highest annotation layer embeds low-level and middle-level annotations and describes scenes captured within images. As illustrated in Figure 1, the highest level scene caption, "cyclist in black shirt riding a bicycle signaling right turn in front of gray car changing lane", embeds all relevant low and middle-level information describing the scene. This approach allows for the definition of multiple contextual feature spaces within each image, leading to a reduced dataset comprising a relatively smaller number of images while maintaining comparable feature space. Each level of annotation contributes to a different aspect of the scene understanding, creating various layers of context. The outcome is a feature-rich space with 203,000 descriptive annotations that highlight salient regions in all 11,085 images.

<p align="center">
  <img src='https://user-images.githubusercontent.com/67676957/283935089-c95c2110-29fa-4310-9004-a2bd2b854877.png' align="center">
</p>
<i>Figure 1: Deconstruction of embedded hierarchical dense captioning strategy showing three (3) levels of annotations.</i>
<br></br>
DivNEDS's scenes involve sudden and unpredictable maneuvers by other road users, impending accidents, animals and debris within right of way, hand signals from cyclists, and unusual interactions involving pedestrians, cyclists, and motorcyclists in right-hand and left-hand traffic. The data was captured in various locations, including New York, San Francisco, Seattle, Minneapolis (United States), Toronto (Canada), Madhepur, Mumbai (India), Johannesburg (South Africa), Jakarta (Indonesia), Melbourne (Australia), London (England), and Lagos (Nigeria) to capture variations in road standards and behaviors. 

<p align="center">
<table>
  <tr>
    <td><img src='https://user-images.githubusercontent.com/67676957/283936625-6f908bba-f378-4e8f-9ab2-0cb911b66a31.png' width="400"></td>  
    <td><img src='https://user-images.githubusercontent.com/67676957/283940431-6f4b2cbe-2bdc-47ea-a45d-d39b9f951322.png' width="400"></td>
  </tr>
</table>
</p>

## Installation
Make sure the environment has the following dependencies installed:
~~~
conda create --name DivNEDS python=3.8 -y
conda activate DivNEDS
pip install torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio==0.9.0 -f https://download.pytorch.org/whl/torch_stable.html
~~~
In the current directory, clone and install *detectron2* with the following commands:
~~~
git clone https://github.com/facebookresearch/detectron2.git
cd detectron2
git checkout cc87e7ec
pip install -e .
~~~
Change directory to DivNEDS and install specific requirements of DivNEDS
~~~
cd .. && cd DivNEDS
pip install -r requirements.txt
~~~
**NB:** If conflicts exist between dependencies install the following packages in this specific order:
~~~
pip install mpi4py
pip install --upgrade pip
pip install setuptools==59.5.0
~~~


## Data
### Download Data
In the root directory,
~~~
 cd datasets
~~~
Download DivNEDS (prepared in COCO format) into datasets directory with the command below
~~~
 wget https://divneds.blob.core.windows.net/divneds/divneds.zip
~~~
Decompress DivNEDS with 
~~~
tar -xzf divneds.zip
~~~
Dataset strcture should look like:
~~~
${DivNEDS_Root}
|-- datasets
`-- |-- divneds
    |-- |-- images/
    |-- |-- annotations/
    |-- |-- |-- train.json
    |-- |-- |-- valid.json
    |-- |-- |-- test.json
~~~

## Model
## Training of DivNET
We utilize DeepSpeed for training because it allows activation checkpointing in distributed training. DeepSpeed is most suited for NVIDIA: Pascal, Volta, Ampere, and Hopper architectures and AMD: MI100 and MI200. We recommend training in Azure Machine Learning Studio on at least 8 x NVIDIA T4 GPUs. We recommend Microsoft Azure Machine Learning Studio for training.

To train on multiple machine nodes run the following command in the root directory:
~~~
python train_deepspeed.py --num-machines 4 --num-gpus-per-machine 8 --config-file configs/DivNET.yaml --output-dir-name ./output/divnet
~~~

You can download our pre-trained DivNET with the following command:
~~~
 wget https://divneds.blob.core.windows.net/divneds/divnet_baseline.pth
~~~

## Evaluation
Our evaluation heavily relies on Johnson et al., 2016 work on fully convolutional localization networks for dense captioning. Kindly cite this paper whenever you evaluate DivNET:
~~~
@inproceedings{johnson2016densecap,
  title={Densecap: Fully convolutional localization networks for dense captioning},
  author={Johnson, Justin and Karpathy, Andrej and Fei-Fei, Li},
  booktitle={Proceedings of the IEEE conference on computer vision and pattern recognition},
  pages={4565--4574},
  year={2016}
}
~~~
The code in these files is concerned with computing the Dense Captioning metric. The metric is inspired by the meap Average Precision in object detection literature in that it uses a set of thresholds, for each threshold it computes average precision, and the final number is the mean. In ordinary object detection one would sweep through IoU (Intersection over Union) overlaps at thresholds of 0.3, 0.4, 0.5, 0.6, 0.7 (the latest recommendation is to use all of these thresholds instead of only using 0.5, as done previously), and consider a candidate detection to be correct if it satisfied the desired overlap. In Dense Captioning we don't only evaluate localization but also captioning. Therefore, we also compute the METEOR score for every candidate detection and consider a candidate detection correct if it is both above a localization threshold and a *METEOR* score threshold. This is then interpreted as both a correctly localized and correctly described prediction.

The *METEOR* thresholds we use are 0, 0.05, 0.1, 0.15, 0.2, 0.25, so the total number of pairwise thresholds we have to compute the score over is 5 x 6 = 30 (5 for IoU overlap thresholds 6 for *METEOR* thresholds). Note that the 0 threshold isn't a mistake or a "catchall" because we want the score to be strictly greater.


#### Requirements & Dependencies
Evaluation code requires **Java**, **Python 2.7** and **Lua**. Thus we recommend creating a different environment and installing the following dependencies:
~~~
luarocks install torch
luarocks install nn
luarocks install image
luarocks install lua-cjson
luarocks install https://raw.githubusercontent.com/qassemoquab/stnbhwd/master/stnbhwd-scm-1.rockspec
luarocks install https://raw.githubusercontent.com/jcjohnson/torch-rnn/master/torch-rnn-scm-1.rockspec
~~~

For GPU acceleration, you will need to install the following:
~~~
luarocks install cutorch
luarocks install cunn
luarocks install cudnn
~~~
You will need to setup the METEOR code with the following commands:
~~~
mkdir eval
cd eval
wget http://www.cs.cmu.edu/~alavie/METEOR/download/meteor-1.5.tar.gz
tar -xzvf meteor-1.5.tar.gz
cp meteor-1.5/meteor-1.5.jar .
mkdir data
cp meteor-1.5/data/paraphrase-en.gz data/
rm -r meteor-1.5
rm meteor-1.5.tar.gz
cd ..
~~~

The most important entry point to evaluation is inside `eval_utils.lua`, and it's the `DenseCaptioningEvaluator` class. You'll notice that it has an `addResult()` function, and most of the heavy lifting of the evaluation is in the `evaluate()` function.

One important and perhaps non-trivial point to mention is that we use `merge_boxes()` to merge ground truth boxes in the DivNEDS data that heavily overlap (>=0.7 IoU) into a single ground truth box with multiple reference captions.


## Inference
To test on DivNEDS test data set, run:
~~~
python train_net.py --num-gpus-per-machine 8 --config-file configs/DivNET.yaml --output-dir-name ./output/divnet --eval-only MODEL.WEIGHTS models/divnet_baseline.pth
~~~

#### Sample Predictions
<p align="left"> <img src='https://user-images.githubusercontent.com/67676957/283942144-410c3b85-c499-4634-a8a8-daa905278a6c.png' align="center" height="300px"> </p>
<p align="left"> <img src='https://user-images.githubusercontent.com/67676957/283945258-d5ce9ffb-9a8a-4825-a66d-f7c7e61b638f.png' align="center" height="300px"> </p>
<p align="left"> <img src='https://user-images.githubusercontent.com/67676957/283944585-1ce42446-d6bf-40aa-93eb-77da42d772e8.png' align="center" height="300px"> </p>
<i align="left">Zoom in for best viewing.</i>

## Architecture of DivNET (Diverse Naturalistic Edge Driving Scenes Transformer)
![Hierarchical Embedded Dense Captioning Strategy](https://user-images.githubusercontent.com/67676957/283937567-5af2020d-9a48-43c5-9944-12ae0f49e4c6.png)

## Acknowledgement
Our code is in part based on [GRiT](https://github.com/JialianW/GRiT), [DenseCap](https://github.com/jcjohnson/densecap) [Detic](https://github.com/facebookresearch/Detic), [CenterNet2](https://github.com/xingyizhou/CenterNet2),
[detectron2](https://github.com/facebookresearch/detectron2),
[GIT](https://github.com/microsoft/GenerativeImage2Text), and
[transformers](https://github.com/huggingface/transformers), [DenseCap](https://github.com/jcjohnson/densecap). 
We thank the authors and appreciate their great works!




