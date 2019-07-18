# MobileNet-Caffe-with-DW_conv
Use Depthwise Convolution instead of Group Convolution to accelerate training MobileNet in Caffe.  

当我参考大佬的[mobilenet-caffe](https://github.com/Linchunhui/MobileNet-Caffe-with-DW_conv)训练的时候，发现训练速度特别慢，
后来发现是caffe里没有**Depthwise Convolution**,实现的时候是利用**Group Convolution**，令**group**和**chanel**数相等,就是`Xception`中
所用到的，每个`group`都只负责一个`channel`,这样也实现了Depthwise Convolution,但是当group数特别大的时候，速度就非常慢。  
  
[yonghenglh6](https://github.com/yonghenglh6/DepthwiseConvolution)基于`cuda kernel`的重新实现的`Depthwise_conv`，可以极快的加快网络的速度。之前训练`100iter`需要**280s**，现在只需要**28s**，将近加快了10倍。

## Usage
将[dw-conv](https://github.com/Linchunhui/MobileNet-Caffe-with-DW_conv/tree/master/dw-conv)文件夹中
的文件拷贝到caffe工程里，重新编译。  
具体的：
```
depthwise_conv_layer.hpp   拷贝到  ./caffe/include/caffe/layers/
depthwise_conv_layer.cpp   拷贝到  ./caffe/src/caffe/layers/
depthwise_conv_layer.cu    拷贝到  ./caffe/src/caffe/layers/
```

将prototxt中对应的**Convlution**改为**DepthwiseConvolution**，具体可以看group哈，group和num_out相等的就是`dw_conv`  
例如
```
layer {
  name: "conv2_1/dw"
  type: "Convolution"
  bottom: "conv1"
  top: "conv2_1/dw"
  param {
    lr_mult: 1
    decay_mult: 1
  }
  convolution_param {
    num_output: 32
    bias_term: false
    pad: 1
    kernel_size: 3
    group: 32
    engine: CAFFE
    stride: 1
    weight_filler {
      type: "msra"
    }
  }
}
```
改为
```
layer {
  name: "conv2_1/dw"
  type: "DepthwiseConvolution"
  bottom: "conv1"
  top: "conv2_1/dw"
  param {
    lr_mult: 1
    decay_mult: 1
  }
  convolution_param {
    num_output: 32
    bias_term: false
    pad: 1
    kernel_size: 3
	group: 32
    stride: 1
    weight_filler {
      type: "msra"
    }
  }
}
```
MobileNetv1、MobileNetv2网络的配置文件以及训练的配置文件都在[MobileNet-Caffe-with-DW](https://github.com/Linchunhui/MobileNet-Caffe-with-DW_conv/tree/master/MobileNet-Caffe-dw_conv),可以参考一下。

记得BN参数`use_global_stats`训练时设为`True`,测试时设为`False`。









