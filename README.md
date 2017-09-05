# FCN-GoogLeNet
This is a repo for course project of [DD2424 Deep Learning in Data Science](https://www.kth.se/social/course/DD2424/) at KTH.

This project is a GoogLeNet Implementation of [Fully Convolutional Networks for Semantic Segmentation, CVPR 2015](https://github.com/shelhamer/fcn.berkeleyvision.org) in TensorFlow. Another Tensorflow implementation: [FCN.tensorflow](https://github.com/shekkizh/FCN.tensorflow). 

Our project is mainly based on these previous works and we performed several changes from them.

## Changes from previous work

- Pre-trained model: [VGG16](http://www.robots.ox.ac.uk/~vgg/research/very_deep/) -> [GoogLeNet (inception v3)](https://github.com/tensorflow/models/tree/master/slim)
- Framework: Caffe -> TensorFlow
- Datasets: [PASCAL VOC 2012](http://host.robots.ox.ac.uk/pascal/VOC/voc2012/) (20 classes) + [MIT Scene Parsing](http://sceneparsing.csail.mit.edu/) (150 classes)

## Pipeline

- Convolutionalize GoogLeNet into FCN-GoogLeNet
- Add upsampling layers on the top
- Fuse skip layers in network
- Fine-tune whole net from end to end

## Skip architectures

#### Skip FCNs
![skip FCNs](https://github.com/DeepSegment/FCN-GoogLeNet/blob/master/results/skip.png)

## How to run

### Requirements

- Python 3.5.0+
- TensorFlow 1.0+
- matplotlib 2.0.2+
- Cython 0.22+
- [PyDenseCRF](https://github.com/lucasb-eyer/pydensecrf) v2

### Visualize and test results 

First download model checkpoints we've trained and put it in folder /logs and replace any other checkpoints if exist. Note if directory /logs/all doesn't exist, please create it by ```mkdir FCN-GoogLeNet/logs/all```. Then change the flags ```tf.flags.DEFINE_string('mode', "visualize", "Mode: train/ test/ visualize")``` at the beginning of the script inception_FCN.py to set the mode to visulize or test results. After that, run ```python inception_FCN.py``` from terminal to start running.

### Fine-tune whole net

First delete all the files in /logs and /logs/all. After this, you need to provide the path to a checkpoint from which to fine-tune. You can download the checkpoint of inception v3 model and correspondingly change ```tf.flags.DEFINE_string('checkpoint_path', /path/to/checkpoint, 'The path to a checkpoint from which to fine-tune.')```. To avoid problems, it's better to directly copy the checkpoint of inception v3 model to /logs though this seems to be an unclever way. To train whole net, we need two steps:

(1) Add upsampling layer on the top of inception v3; freeze lower layers and just train the output layer of the pretrained model and the upsampling layers. To acheive this, change```tf.flags.DEFINE_string('checkpoint_exclude_scopes', ...)``` to be None and ```tf.flags.DEFINE_string('trainable_scopes', ...)``` to be ```'InceptionV3/Logits,InceptionV3/Upsampling'```. Make sure you've set the flag of skip_layers to the architecture you want. Set mode to train and run inception_FCN.py.

(2) Fine-tune all the variables: Change```tf.flags.DEFINE_string('checkpoint_exclude_scopes', ...)``` to be ```'InceptionV3/Logits,InceptionV3/Upsampling'``` and ```tf.flags.DEFINE_string('trainable_scopes', ...)``` to be None. Run inception_FCN.py again. If the code is planned to run on [PDC](https://www.pdc.kth.se/) clusters, run ```sbatch ./batchpyjobunix.sh``` to submit your job.

## Results

#### Training loss
<p align="center">
<img src="https://github.com/DeepSegment/FCN-GoogLeNet/blob/master/results/loss.png"/>
</p>

#### PASCAL VOC
<p align="center">
<img src="https://github.com/DeepSegment/FCN-GoogLeNet/blob/master/results/pic_0.png" width="430"/> <img src="https://github.com/DeepSegment/FCN-GoogLeNet/blob/master/results/pic_1.png" width="430">
</p>

#### MIT Scene Parsing
<p align="center">
  <img src="https://github.com/DeepSegment/FCN-GoogLeNet/blob/master/results/pic_2.png" width="390"/>   <img src="https://github.com/DeepSegment/FCN-GoogLeNet/blob/master/results/pic_3.png" width="400">
</p>

## Conclusions

- VGG16 net outperforms GoogLeNet when dealt with semantic segmentation tasks;
- A performance drop is expected when FCN is fed with a dataset containing large number of object classes;
- Objects which have more examples in the training set, e.g. vehicles, humans, are easier to be correctly identified.

## Related materials
### Here is the presentation given by the authors of the original paper.
http://techtalks.tv/talks/fully-convolutional-networks-for-semantic-segmentation/61606/

<!-- ## Notes from this presentation
- Step 1: reinterpret fully connected layer as conv layers with 1x1 output. (No weight changing)
- Step 2: add conv layer at the very end to do upsample.
- Step 3: put a pixelwise loss in the end

			along the way we have stack of features.

			closer to the input - higher resolution - shallow, local - where

			closer to the output - lower resolution - deep, global - what
- Step 4: skip to fuse layers. interpolate and sum.
- Step 5: Fine tune on per-pixel dataset, PASCAL

			I stopped at 8:30 in the video -->

### Extra Reading
#### This is about CONVERT fully connected layer to convolutional layer:
http://cs231n.github.io/convolutional-networks/#convert

#### This is someone basically did what we want to do
This guy's [Blog](http://warmspringwinds.github.io/blog/) and his [TensorFlow Image Segmentation](https://github.com/warmspringwinds/tf-image-segmentation) can be useful. 

#### This is a helpful tensorflow tutorial about transfer learning
https://github.com/Hvass-Labs/TensorFlow-Tutorials

<!-- Blog posts worth mentioning are: (some of this can also be found by the end of his project README)

[TFrecords Guide](http://warmspringwinds.github.io/tensorflow/tf-slim/2016/12/21/tfrecords-guide/)

[Convert Classification Network to FCN](http://warmspringwinds.github.io/tensorflow/tf-slim/2016/10/30/image-classification-and-segmentation-using-tensorflow-and-tf-slim/)

[His Implementation on FCN](http://warmspringwinds.github.io/tensorflow/tf-slim/2017/01/23/fully-convolutional-networks-(fcns)-for-image-segmentation/)

[About Upsampling](http://warmspringwinds.github.io/tensorflow/tf-slim/2016/11/22/upsampling-and-image-segmentation-with-tensorflow-and-tf-slim/)

#### Some links about previous people asking about this but with no success. LOL:
http://stackoverflow.com/questions/38536202/how-to-use-inception-v3-as-a-convolutional-network
http://stackoverflow.com/questions/38565497/tensorflow-transfer-learning-implementation-semantic-segmentation -->


