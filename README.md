# Silhouette

## Overview
![Overview](https://user-images.githubusercontent.com/30234176/145958278-ad3e777e-9a3a-4336-a86b-5adf5f59b736.PNG)  
Above figure shows the overview of project.  
We install `Auxiliary Network` at arbitrary layer of `Main Network`(such as ResNet) architecture. (but highly recommended to install at the beginning of the main network so that many layers can perform region-based quantization)  
We referred [Attention Branch Network](https://arxiv.org/pdf/1812.10025.pdf)'s module design, so `Auxiliary Network` is consist of 1 channel 1x1 convolution - batch normalization - sigmoid (they used as activation instead of ReLU because this showed better accuracy).  
In detail, at forward path, flowing feature maps (size: B * C * H * W) at arbitrary layer are passed into `Auxiliary Network` and these are processed as `Global Silhouette` (size: B * 1 * H * W), and these are referred at behind layers.

![Region-based Quantization](https://user-images.githubusercontent.com/30234176/145961837-ae7bd5bd-dc4f-489c-b890-73b58a413197.PNG)  
Extracted `Global Silhouette` are interpolated to fit the size of each behind layer's feature map.  
Interpolated `Global Silhouette` are converted into `Region Separating Bit-mask` with preset policy. For example, '2bit 50%, 3bit 30%, 4bit 20%' in above figure. This means lowest 50% value region(violet) of **feature map** are quantized into 2bit, and highest 20% value region(yellow) of **feature map** are quantized into 4bit, and remained 30% value region(green) of **feature map** are quantized into 3bit. This policy is customizable by level of quantization.  
All other parts are same as original main network's architecture, except the activation function has changed [PACT](https://arxiv.org/pdf/1805.06085.pdf) to maintain the accuracy with extremely low bit-precision.
