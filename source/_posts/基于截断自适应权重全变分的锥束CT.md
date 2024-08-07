---
title: 基于截断自适应权重全变分的锥束CT
date: 2024-05-22 23:45:32
tags: L0
categories: 科研/论文
cover: /img/4.jpg
---

Cone-beam computed tomography based on truncated adaptive-weight total variation

# 基于截断自适应权重全变分的锥束CT

### 💟摘要

​        为计算机层析成像扫描获得高质量的重建图像具有挑战性。由于CL扫描得到的**投影不完整**，CL图像往往存在层间混叠和模糊现象。为了解决这个问题，我们提出了一种截断自适应权重全变分( TAwTV )约束的迭代重建算法。具体来说，首先根据一个阈值对图像梯度幅值进行截断，然后设计一个截断梯度幅值的余弦非线性函数来自适应地调整截断梯度，从而使截断自适应权重全变分在惩罚较大梯度幅值和各向同性属性时能够克服过度平滑。在模拟3D印制电路板、模拟工件和Shepp - Logan体模上进行了实验

![image-20240523143354124](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240523143354124.png)

#### 疑问的参考文献

* 还有其他的层析成像扫描几何图形，详见文献。[3] Cai Yufang, Li Pingyi, et al. Recent advances in computed laminography for nondestructive testing of plate-shell objects. Chin J Sci Instrum 2020;41:11–25. 01.
* CL扫描得到的**投影不完整**[29] Heang K, Tuy. An inversion formula for cone-beam reconstruction. SIAM J Appl Math 1983;43(3). [30] Smith BD. Image reconstruction from cone-beam projections: necessary and sufficient conditions and reconstruction methods. IEEE Trans Med Imag March 1985;4(1):14–25.

##### 💚单词

`inter-slices aliasing——切片间混叠`

`runcated adaptive-weight total variation (TAwTV)——截尾自适应权重总变差( TAwTV )`

`gradient amplitude——梯度幅值`

`nondestructive testing (NDT) technique——无损检测( NDT )技术`

`attenuates——衰减`

`incident obliquely——斜入射`

`tilt angle——倾斜角`

`aliasing artifacts——混叠伪影`

`post-processing——后处理`

`Bandpass filters——带通滤波器`
