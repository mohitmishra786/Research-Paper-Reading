# Swin Transformer: Hierarchical Vision Transformer using Shifted Windows

Created: August 8, 2022 7:25 AM
Last Edited Time: August 9, 2022 10:38 AM
Status: In Progress 🙌
Type: Vision Based

## Abstract

- This paper presents a new vision transformer called Swin transformers, capable for serving as a general purpose backbone for computer vision.
- Challenges for adapting transformers from languages to vision arises from the differences between the two domain. Vision has the data which contains huge information in pixels and even small portion of the image can change the information that we want to get from it.
- To address the issue of this difference between vision and languages, here we will use Shifter Window Mechanism.
- The shifted window scheme bring greater efficiency by limiting self attention computation to the non overlapping local windows and also allowing for cross window connection.
- The hierarchical design and the shifted window approach also proves beneficial for the all MLP architectures.

## Information

- In Vision based model till now most of the works has been done on CNN based architecture. CNN has also shown remarkable results and has been very well successful.
- Beginning of the vision based modelling was with ALexNet which was remarkable with ImageNet Classification Challenge and the performance was revolutionary.

 ![image](https://user-images.githubusercontent.com/71754779/183719220-2a3b66ab-7af8-4753-8ba7-f77fea7e9676.png)

- CNN has been serving as the backbone for the vision based works.
- On the other hand Language based model has been using architectures based on Transformers. Transformers has a greater mechanism of attention which helps the model for the long-range dependencies. These architectures are designed for the sequence modelling and the transduction task.
- This great success of the transformer in one field led the researchers use it somehow in the field of vision which also gave remarkable results specially image classification and joint vision language modeling.
- This paper focus primarily on making transformers based vision modelling as the backbone for the vision models like NLP has transformers and CNNs of vision models. But the observed challenge between the two field has been the difference between the two modalities.
- One of these difference includes scale. Unlike the word tokens that act as the basic elements of the processing in the language modelling, visual elements can vary substantially in scale,
- Another difference is the high resolution of pixels in the image as compared to the text.
- One big difference between other Vision Transformers and Swin Transformers is that Swin Transformers produce hierarchical maps and linear computational complexity to image size. With these hierarchical mapping Swin transformers can easily leverage the advance techniques like Feature Pyramid Network and U-Net as these requires dense prediction.
- The linear complexity is acquired via computing self-attention locally within non-overlapping windows. The number of patches in each window is fixed and thus the complexity becomes linear.
- Other Vision transformers has feature maps of single resolution with quadratic complexity.
- One of the belief of the author was that if we will have same architecture for both the image and vision models then it will be much more beneficial as it will help for joint modelling of visual and textual signals.
- Instead of using sliding windows we propose to use shift windows between consecutive layers, which allows for more efficient implementation in general hardware.
- The results of the ViT on image classification is encouraging but we cannot use it for the general purpose as it does not perform well with the high resolution images, due to its low resolution feature maps and quadratic increase in the complexity.
- Positional embeddings that are directly added to the vector representations of the tokens are not working as well as the relative positions bias in self attentions.

## Method

- Constructs hierarchical maps

![image](https://user-images.githubusercontent.com/71754779/183720215-69071faa-971a-4bdb-93f2-9e3fe7bdfaeb.png)

- In this method window size is fixed and self attention is applied locally within the red block region which will not be more than linear complexity with respect to image size.
- Linear Computational complexity with respect to image size.

## Architecture

Overview of Swin transformer architecture is shown below

![image](https://user-images.githubusercontent.com/71754779/183720127-f443e6d1-56a6-4a6e-ac1f-e0901c99b0e8.png)

As we can see that our RGB image firstly get passed through **Patch Partition** layer, which only does the work of splitting the images into patches same as we do in ***ViT*** model. Each patch is treated as a token. In this paper they had use 4 x 4 patches and thus the feature dimension will be 4 x 4 x 3 = 48.

Then the Linear Embedding layer gets applied to these tokens to convert these into arbitrary dimension denoted as **C**. This architecture contains several Swin Transformer Block with modified Self Attention computation. There are total of 4 stages in this architecture which jointly produces a hierarchical representation, with the same feature maps resolution as that of those typical convolutional networks. Leaving first stage every stage has a Patch Merging Layer before the Swin Transformer Block which just merges the dimension of resolution.

![image](https://user-images.githubusercontent.com/71754779/183721040-23edd299-172c-4178-855c-ca72373b290e.png)

- In Patch Merging layer we has a linear layer which is nothing other than vanilla neural network. As we are using linear layer so we have a power of defining the size of output we want.

### Swin Transformer Block

![image](https://user-images.githubusercontent.com/71754779/183721183-4d890214-4676-407b-aea9-3ff23a2fa256.png)

Swin Transformer Block is made up by replacing standard multi head attention layer with module based on a shifted windows with all the other layers being kept same. A Layer Normalization is applied before every MSA/ MLP module. After every MSA layer there has been 2 MLP layers with GELU non-linearity in between.

### Shifted Window Based Self Attention

Standard transformers architecture and its adaptation for the image classification both computes the self attention globally where the relationship between token and all other tokens is computed. This leads to the quadratic complexity as we compute globally. Due to this we are unable to use these transformer architectures and its adaptation into the work where we have higher resolution of images and where we have very high number of tokens. 

So this paper provides a better solution for this problem by ***Self attention in non-overlapping windows.*** So what they did was to calculate the self attention in the local windows. These windows are partitioned in such a way that they are not overlapping. I have also attached the complexity of both MSA and W-MSA below :-

![image](https://user-images.githubusercontent.com/71754779/183721360-94655c73-416c-443f-a0b0-cfe6ab8f039b.png)

![image](https://user-images.githubusercontent.com/71754779/183721418-a9dc703e-f9cd-4797-ae26-376c4602668c.png)

Here in above shown pic h x w is the dimension of the images and M is the window size of partition of images (M x M). Mostly people choose M to be 7. We can simply see that the Global Self attention computation is really not affordable if h and w will be high.

### Shifted Window Partitioning in Successive Blocks

The window based self attention module lacks connection between the layers which limits the modelling powers. So the solution for this issue is to do Shifted Window self attention approach alternatively with normal window based self attention. With the shited window based approach, consecutive Swin transformers blocks are computed as :-

![image](https://user-images.githubusercontent.com/71754779/183721942-6747ae8f-299c-49da-91b2-c17e04dce8f1.png)

The shifted window partitioning approach introduces connection between the neighboring non overlapping windows in the previous layer and found to be effective in image classification, object detection and semantic segmentation. The results are written below with experiments.

### Efficient batch computation for Shifted Configuration

An issue with the shifted window partitioning is that it will result in more windows and sometimes small windows than the size of M x M. So the solution for this issue is simple cyclic shifting  as shown in image below :-

![image](https://user-images.githubusercontent.com/71754779/183721703-8296926f-3d0e-4068-9404-f4db27408419.png)

### Relative Positional Bias

For the computation part of the self attention we follow by including relative positional bias to each head for computing similarity.

![image](https://user-images.githubusercontent.com/71754779/183721586-5a96ffa7-5838-45e5-be75-749c8364d6e4.png)

Here Q, K and V are *query*, *key* and *value* respectively. *d* is the $query/key$ dimension, and $M^2$   is the patches in a window. 

### Architecture Variants

Here Swin-B model is build up to have of model size and computation complexity similar of ViT-B/DeiT-B. Researchers alspo has introduced Swin-T, Swin-S and Swin-L.

![image](https://user-images.githubusercontent.com/71754779/183721531-8008c771-167a-4549-b22e-92b1762bd59c.png)
