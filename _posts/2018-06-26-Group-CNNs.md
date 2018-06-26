---
title: Group Equivariant Convolutional Networks
description: Paper Summary
header: Group Equivariant Convolutional Networks
duration: 5 minutes read
categories: blog
---

### Introduction

Group Equivariant Convolutional Networks generalizes convolutional networks to be equivariant to a larger group of transformations (normal CNNs are only translational equivariant).


### What is Equivariance?

Consider a transformation $$g \in G$$ where $$G$$ is a [group] of transformations, and a function $$\Phi : X \to Y$$  mapping inputs $$x \in X$$ to outputs $$y \in Y$$. The function $$\Phi$$ is equivariant if:

$$\Phi(T_g x) = T_g' \Phi(x)$$

i.e. transforming the input $$x$$ by $$g$$ (giving $$T_g x$$), then passing it through the function $$\Phi$$ gives the same result as passing it through the function $$\Phi$$ first then transforming the representation. Note that $$T_g$$ and $$T_g'$$ need not be the same, but have to be linear functions of $$g$$.

A special case of equivariance is invariance, where $$T_g'$$ is identity for all $$g$$, i.e.:

$$\Phi(T_{g} x) = \Phi(x)$$

In this case, even when the input is transformed, the output feature map remains the same. As information about the transform $$g$$ is lost, general equivariance may be more useful in some cases than invariance.

Tranformations can be as simple as translations, i.e. $$T_s f(x) = f(x-s)$$, or can be more complicated, such as involving rotations.

#### Groups $$p4$$ and $$p4m$$

This work focuses on the achieving equivariance on the groups  $$p4$$ and $$p4m$$. The group $$p4$$ consists of all compositions of translations and rotations by 90 degrees; the group $$p4m$$ includes mirror reflections in addition to those in $$p4$$.


### Is Regular Convolution Equivariant?

The paper then provides mathematical proofs that regular planar convolution is only equivariant to translations but not to rotations. 

The author first defines functions on groups. Images and stacks of feature maps in a conventional CNN can be modeled as functions $$f : \mathbb{Z}^2 \to \mathbb{R}^K$$, where $$K$$ denotes the number of channels. Let $$L_g$$ is a instantiation of the transformation operator $$T_g$$ defined earlier, i.e.  $$L_{g}f$$ denote input feature map $$f$$ transformed by transformation $$g$$.

$$
[L_{g}f](x) = [f \circ g^{-1}](x) = f(g^{-1}x)
$$

This means that the value of $$L_gf$$ at position x can be obtained as the value of f at position $$g^{-1}x$$. This is intuitive if we think of the case where g is a translation.

Going back to convolution. Regular convolutions are defined as follows:

$$
[f * \psi ](x) = \sum_{y \in \mathbb{Z^2}} \sum_{k=1}^{K} f_k(y) \psi_j (y-x)
$$

The details can be found in the paper, but for translations, translation followed by a correlation is the same as correlation followed by a translation, i.e. equivariant to translations.

$$
[[L_t f] * \psi](x) = [L_t [f * \psi]](x)
$$

However, for rotations, the correlation of a rotated image $$L_r f$$ with a filter $$\psi$$ is the same as the rotation by $$r$$ of (the original image $$f$$ convoled with the inverse-rotated filter $$L_{r^{-1}} \psi$$. That is, it's not equivariant.

$$
[[L_r f] * \psi](x) = [L_r [f * L_{r^{-1}} \psi]](x)
$$


### From Planar Convolutions to Group-Convolutions
How to achieve equivariance to other transformations? The solution is group-convolutions. Group convolution are defined as:

$$
[f * \psi ](g) = \sum_{h \in G} \sum_{k=1}^{K} f_k(y) \psi_j (g^{-1} x)
$$

The authors show that group-convolution are equivariant to transformations $$g \in G$$.


### Group-Convolutions for Group $$p4$$

#### First layer

In the first group-convolution layer, we simply convolve the image with rotated versions of the same filter. e.g. a $$N \times N$$ input feature will give $$N \times N \times 4$$ output feature map per output channel.

![Screenshot from 2018-06-26 17-06-53](/blog_images/2018-06-26_17-06-53.png)

Notice that if the input image is rotated, it corresponds to a permutation of the pink 3x3 output features.

#### Subsequent Layers

Things are a bit more tricky in the subsequent layers. Notice that in the first layer, the inputs $$f$$ and $$\psi$$ are functions of $$\mathbb{Z}^2$$ , while the outputs are functions on $$G$$. However in subsequent layers, both inputs and outputs are on $$G$$.

Actually it's not as complicated as it seems. As an example, a 90 degree rotation $$r$$ will result in 1) moving in the direction of the red arrow, and 2) rotation of the feature map by $$r$$, as shown in Figure 1.

![Screenshot from 2018-06-26 17-43-08](/blog_images/2018-06-26_17-43-08.png)

### Misc Things to Note

The paper also shows that non-linearities (e.g. ReLUs) are equivariant. In addition, pooling can be slightly modified to become equivariant.

This means that the common convolutional networks can be modified to be group-equivariant.

### Experiments

The work is evaluated on Rotated MNIST and CIFAR-10 datasets, and is shown to improve performance while keeping the number of trainable parameters fixed.

\[[paper] (ICML 2006)\]


[group]:https://en.wikipedia.org/wiki/Group_(mathematics)
[paper]: https://arxiv.org/abs/1602.07576


