# MobileNet-Caffe-with-DW_conv
Use Depthwise Convolution instead of Group Convolution to accelerate training MobileNet in Caffe.  

当我参考大佬的[mobilenet-caffe](https://github.com/Linchunhui/MobileNet-Caffe-with-DW_conv)训练的时候，发现训练速度特别慢，
后来发现是caffe里没有**Depthwise Convolution**,实现的时候是利用**Group Convolution**，令**group**和**chanel**数相等,就是`Xception`中
所用到的，每个`group`都只负责一个`channel`,这样也实现了Depthwise Convolution,但是当group数特别大的时候，速度就非常慢。  
  
重新编译depthwise_conv，可以极快的加快网络的速度。之前训练`100iter`需要**280s**，现在只需要**28s**，将近加快了10倍。

## Usage


