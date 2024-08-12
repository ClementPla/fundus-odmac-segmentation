![header](imgs/header.png)


# The Fundus ODMAC Toolkit

This project aims to evaluate the performance of different models for the segmentation of optic disk and macula in fundus images. The reported perfomance metrics are not always consistent in the literature. Our goal is to provide a fair comparison between different models using the same datasets and evaluation protocol.


## Installation

```bash
pip install .
```

or
```bash
pip install -e .
```

## Usage

To use these models, please consult the [example notebook](notebooks/test_models.ipynb). You don't have to download the models. We rely on HuggingFace for that.
```python

from fundus_data_toolkit.functional import open_image
from fundus_odmac_toolkit.models.segmentation import segment

filepath = 'my_folder/my_image.(jpeg/png/...)'
image = open_image(filepath)
pred = segment(image) # You don't need to resize, crop or normalize the image as it will be done automatically.

# pred is a post-softmax output of size 3xHxW. First channel: Background score, Second channel: Optic Disk score, Last channel: Macula score.

```

# Benchmarck
You can find below the benchmark obtained with the different models. In short summary, we recommend using the UNet with the maxvit_small_tf_512 encoder (which are therefore set as default values).
## IDRiD Segmentation Challenge (Optic Disk)

*Note that we didn't use the segmentation mask provided by the IDRiD's team for training.*

### With Test Time Augmentation
Transforms: Horizontal Flip and Rotations (0, 90, 180 and 270)

| Architecture 	|        Encoder        	| Jaccard Index 	|
|:------------:	|:---------------------:	|:-------------:	|
| UNet         	| seresnet50            	| 92.74%        	|
| UNet         	| mobilevitv2_100       	| 92.24%        	|
| UNet         	| mobilenetv3_small_050 	| 84.29%        	|
| UNet         	| maxvit_tiny_tf_512    	| 91.80%        	|
| UNet         	| maxvit_small_tf_512   	| **93.17%**    	|
| UNet         	| maxvit_base_tf_512    	| 87.20%        	|
| UNet++       	| seresnet50            	| 90.75%        	|
| UNet++       	| mobilenetv3_small_050 	| 86.04%        	|


The UNet-maxvit_small_tf_512 would rank second in the [official leaderboard](https://idrid.grand-challenge.org/Leaderboard/)

### Without Test Time Augmentation

| Architecture 	|        Encoder        	| Jaccard Index 	|
|:------------:	|:---------------------:	|:-------------:	|
| UNet         	| seresnet50            	| 92.03%        	|
| UNet         	| mobilevitv2_100       	| 92.07%        	|
| UNet         	| mobilenetv3_small_050 	| 89.13%        	|
| UNet         	| maxvit_tiny_tf_512    	| 92.27%            |
| UNet         	| maxvit_small_tf_512   	| **92.84%**        |
| UNet         	| maxvit_base_tf_512    	| 88.19%        	|
| UNet++       	| seresnet50            	| 88.92%        	|
| UNet++       	| mobilenetv3_small_050 	| 85.86%        	|

## IDRiD Localization Challenge (Optic Disk)


| Architecture 	|        Encoder        	|  MSE  	|
|:------------:	|:---------------------:	|:-----:	|
| UNet         	| seresnet50            	| **25.73**	|
| UNet         	| mobilevitv2_100       	| 28.26 	|
| UNet         	| mobilenetv3_small_050 	| 34.72 	|
| UNet         	| maxvit_tiny_tf_512    	| 30.17 	|
| UNet         	| maxvit_small_tf_512   	| 29.07 	|
| UNet         	| maxvit_base_tf_512    	| 30.09 	|
| UNet++       	| seresnet50            	| 28.43 	|
| UNet++       	| mobilenetv3_small_050 	| 42.44 	|


## IDRiD Localization Challenge (Fovea)


The UNet-maxvit_small_tf_512 obtains a MSE of **47.10** (48.12 with TTA), which ranks first in the [official leaderboard](https://idrid.grand-challenge.org/Leaderboard/).
However, we filter images where the macula was not detected (8/103), which artificially boost our performance (see the associated [notebook](notebooks/idrid_eval.ipynb))
