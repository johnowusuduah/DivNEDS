# DivNEDS: Diverse Naturalistic Edge Driving Scene Dataset for Autonomous Vehicle Scene Understanding
DivNEDS comprises **11,085** scenarios comprising a wide range of diverse naturalistic edge situations annotated with **203,000** descriptive captions. These scenarios involve sudden and unpredictable maneuvers by other road users, impending accidents, animals and debris within right of way, hand signals from cyclists, and unusual interactions involving pedestrians, cyclists, and motorcyclists in right-hand and left-hand traffic. The data was captured in various locations, including New York, San Francisco, Seattle, Minneapolis (United States), Toronto (Canada), Madhepur, Mumbai (India), Johannesburg (South Africa), Jakarta (Indonesia), Melbourne (Australia), London (England), and Lagos (Nigeria) to capture variations in road standards and behaviors. 


<p align="center"> <img src='[docs/chatgpt.png](https://user-images.githubusercontent.com/67676957/283936625-6f908bba-f378-4e8f-9ab2-0cb911b66a31.png)' align="center" width="300" height="300> </p>

## Data
### Hierarchical Embedded Dense Captioning Strategy
We use hierarchical annotations with the lowest level approximating object detection annotations with attributes embedded within middle-level annotations. In Figure 1, the low-level bounding box is captioned, "cyclist in black shirt" and is embedded within a middle-level bounding box with the caption, "cyclist in black shirt riding a bicycle signaling right turn". Middle-level annotations describe relationships and actions of objects in the road scene. The highest annotation layer embeds low-level and middle-level annotations and describes scenes captured within images. As illustrated in Figure 1, the highest level scene caption, "cyclist in black shirt riding a bicycle signaling right turn in front of gray car changing lane", embeds all relevant low and middle-level information describing the scene. This approach allows for the definition of multiple contextual feature spaces within each image, leading to a reduced dataset comprising a relatively smaller number of images while maintaining comparable feature space. Each level of annotation contributes to a different aspect of the scene understanding, creating various layers of context. The outcome is a feature-rich space with 203,000 descriptive annotations that highlight salient regions in all 11,085 images.

![Hierarchical Embedded Dense Captioning Strategy](https://user-images.githubusercontent.com/67676957/283935089-c95c2110-29fa-4310-9004-a2bd2b854877.png)


### Download Data
Download data into ..... folder with the command below after you clone this repository:

[insert wget command]

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

## Architecture of DivNET (Diverse Naturalistic Edge Driving Scenes Transformer)
![Hierarchical Embedded Dense Captioning Strategy](https://user-images.githubusercontent.com/67676957/283937567-5af2020d-9a48-43c5-9944-12ae0f49e4c6.png)

## Predictions


## Training


## Benchmark Inference and Evaluation


## Acknowledgement




