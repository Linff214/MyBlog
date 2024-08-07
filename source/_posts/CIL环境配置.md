---
title: CIL环境配置
date: 2024-08-02 18:57:23
tags: 环境搭建
categories: 科研/论文
cover: /img/14.jpg
---
# cil环境

![image-20240426202048991](C:\Users\LXX\AppData\Roaming\Typora\typora-user-images\image-20240426202048991.png)

`import matplotlib.pyplot as plt`

安装步骤：

```
1. 先安装anaconda环境
2. 输入代码conda create --name cil -c conda-forge -c intel -c ccpi cil
3. 下载示例程序：
	https://github.com/TomographicImaging/Paper-2021-RSTA-CIL-Part-I
4. 运行配置各种包	
```

E:\TIGRE-master\MATLAB\PlaneCT

## 数据结构、几何形状和核心功能

CIL 为数据表示提供了两个基本类，即用于断层扫描数据的 AcquisitionData 和用于重建（或模拟）体积数据的 ImageData。



```python
    def create_Cone3D(source_position, detector_position, detector_direction_x=[1,0,0], detector_direction_y=[0,0,1], rotation_axis_position=[0,0,0], rotation_axis_direction=[0,0,1], units='units distance'):
        r'''This creates the AcquisitionGeometry for a cone beam 3D tomographic system

        :param source_position: A 3D vector describing the position of the source (x,y,z)
        :type source_position: list, tuple, ndarray, optional
        :param detector_position: A 3D vector describing the position of the centre of the detector (x,y,z)
        :type detector_position: list, tuple, ndarray, optional
        :param detector_direction_x: A 3D vector describing the direction of the detector_x (x,y,z)
        :type detector_direction_x: list, tuple, ndarray
        :param detector_direction_y: A 3D vector describing the direction of the detector_y (x,y,z)
        :type detector_direction_y: list, tuple, ndarray
        :param rotation_axis_position: A 3D vector describing the position of the axis of rotation (x,y,z)
        :type rotation_axis_position: list, tuple, ndarray, optional
        :param rotation_axis_direction: A 3D vector describing the direction of the axis of rotation (x,y,z)
        :type rotation_axis_direction: list, tuple, ndarray, optional
        :param units: Label the units of distance used for the configuration, these should be consistent for the geometry and panel
        :type units: string
        :return: returns a configured AcquisitionGeometry object
        :rtype: AcquisitionGeometry
        '''
        
        
        r'''为锥形束三维断层扫描系统创建采集几何图形

        参数 source_position： 描述光源位置（x,y,z）的三维向量
        :type source_position: list, tuple, ndarray, 可选
        :param detector_position： 描述探测器中心位置（x,y,z）的三维向量
        :type detector_position: list, tuple, ndarray, 可选
        :param detector_direction_x： 三维向量，描述探测器_x 的方向（x,y,z）
        :type detector_direction_x: list, tuple, ndarray
        :paramector_direction_y： 描述探测器_y（x,y,z）方向的三维向量
        :type detector_direction_y: list, tuple, ndarray
        :param rotation_axis_position： 描述旋转轴位置（x,y,z）的三维向量
        :type rotation_axis_position: list, tuple, ndarray, 可选
        :param rotation_axis_direction： 描述旋转轴方向（x,y,z）的三维向量
        :type rotation_axis_direction: list, tuple, ndarray, 可选
        :param units： 标注配置所使用的距离单位，这些单位应与几何体和面板一致
        :type units: string
        :return：返回已配置的采集几何体对象
        :rtype： 获取几何体
        '''

        AG = AcquisitionGeometry()
        AG.config = Configuration(units)
        AG.config.system = Cone3D(source_position, detector_position, detector_direction_x, detector_direction_y, rotation_axis_position, rotation_axis_direction, units)
        return AG
```

```python
# parameters are from the original paper/author clarification
src_to_det = 967.3209839		# 源到探测器的距离
src_to_object = 295				# 源到物体的距离				
tilt = 30. * np.pi / 180.		# 倾斜角，以弧度（radian）表示。
centre_of_rotation = 0.254 * 6.	# 旋转中心的位置，以毫米（mm）为单位。

mag = src_to_det / src_to_object #放大倍数
object_offset_x = centre_of_rotation / mag	#物体的偏移量，以像素为单位

source_pos_y = -src_to_object
detector_pos_y = src_to_det-src_to_object
angles_list = -np.linspace(0, 360, num=data.shape[0], endpoint=False) 
#角度列表，表示投影采集时的旋转角度。num为采样点数量，endpoint=False表示不包括终点。
num_pixels_x = data.sh
num_pixels_y = data.shape[1]
pixel_size_xy = 0.254*bins #像素的尺寸，以毫米（mm）为单位

ag = AcquisitionGeometry.create_Cone3D( source_position=[0.0,source_pos_y,0.0], \
                                        detector_position=[0.0,detector_pos_y,0.0],\
                                        rotation_axis_position=[object_offset_x,0,0],\
                                        rotation_axis_direction=[0,-np.sin(tilt), np.cos(tilt)] ) \
                        .set_angles(angles=angles_list, angle_unit='degree')\
                        .set_panel( num_pixels=[num_pixels_x, num_pixels_y], \
                                    pixel_size=pixel_size_xy,\
                                    origin='top-left')
print(ag)
```

```py
"""
This example requires data from https://zenodo.org/record/2540509

https://zenodo.org/record/2540509/files/CLProjectionData.zip
https://zenodo.org/record/2540509/files/CLShadingCorrection.zip

Once downloaded update `path_common` to run the script.

This script normalises the data and sets up the laminography parameters using CIL's geometry definitions

It then reconstructs the dataset with:
 - FDK
 - LeastSqaures using FISTA
 - TotalVariation with a non-negativity constraint using FISTA

 Access to a GPU is necessary to run this example.
 Please note this script could take 2-3 hours to run in full depending on hardware constraints.
conda
"""
#请注意，这个脚本完整运行可能需要2-3小时，具体取决于硬件限制。
# %% imports
# 从CIL框架中导入采样几何（AcquisitionGeometry）和采样数据（AcquisitionData）类。
# 从CIL优化函数模块中导入ZeroFunction（零函数）和LeastSquares（最小二乘）函数，用于定义优化问题的函数部分。
# 从CIL优化算法模块中导入FISTA算法，用于解决优化问题。

from cil.framework import AcquisitionGeometry, AcquisitionData
from cil.optimisation.algorithms import FISTA
from cil.optimisation.functions import ZeroFunction, LeastSquares

# 从TIGRE插件中导入ProjectionOperator（投影算子），用于定义投影操作符。
# 从TIGRE插件中导入FBP（滤波后投影）算法。
# 从CIL的CCPi正则化插件中导入FGP_TV函数，用于定义总变差正则化函数。
from cil.plugins.tigre import ProjectionOperator  
from cil.plugins.tigre import FBP	
from cil.plugins.ccpi_regularisation.functions import FGP_TV	

from cil.utilities.display import show2D	#用于显示二维图像。
from cil.utilities.jupyter import islicer	#导入islicer函数，用于在Jupyter Notebook中进行交互式切片显示。
from cil.io import TIFFStackReader	#导入TIFFStackReader类，用于读取TIFF格式的图像堆栈。

import numpy as np  #导入NumPy库，并使用np作为别名，用于数值计算。
import matplotlib	#导入matplotlib库，用于绘制图形。
matplotlib.use('TkAgg')
import matplotlib.pyplot as plt		 

import math
import os		#导入Python的os模块，用于与操作系统进行交互，例如文件路径操作。


# %% read in all Tiff stack from directory
#定义了变量path_common，指定了TIFF图像堆栈所在的共同路径。这个路径应该是包含所有TIFF图像堆栈的上级目录。
#定义了变量path，指定了要读取的TIFF图像堆栈的相对路径。这个路径是相对于共同路径的。

#定义了变量bins，指定了在读取图像时的每个轴上的子采样因子。在这里，每个轴上的子采样因子都是2，意味着沿每个轴的图像尺寸将减少一半。
😵#定义了一个字典roi，指定了每个轴上感兴趣的区域（Region of Interest，ROI）。在这里，将整个轴上的数据保留，并且每个轴上的每个像素都将与前面定义的bins变量所指定的子采样因子进行子采样。

#创建了一个TIFFStackReader对象reader，用于读取TIFF图像堆栈。file_name参数指定了要读取的TIFF图像堆栈的完整路径，通过os.path.join()函数将共同路径和相对路径连接起来。roi参数指定了ROI的设置，以进行部分数据处理。
#调用reader对象的read()方法，读取TIFF图像堆栈，并将结果存储在变量data中。


path_common = r'F:\04Code\python\CL reconstruction'
path = r'CLProjectionData\Lego_Lamino30deg_XTH'

bins = 2
roi = {'axis_1': (None, None, bins), 
       'axis_2': (None, None, bins)}

reader = TIFFStackReader(file_name=os.path.join(path_common, path), roi=roi)
data = reader.read()

# %% Read in the flat-field and dark-field radiographs and apply shading correction to the data
⛔# %% 这段代码是用来读取两个额外的TIFF图像，执行阴影校正，然后对主要数据进行归一化处理的。
#创建了一个包含两个额外TIFF图像文件路径的列表。这些图像用于阴影校正。第一个路径是指向阴影校正的黑场图像，第二个路径是指向阴影校正的白场图像。
#创建了一个新的TIFFStackReader对象reader，用于读取两个额外的TIFF图像文件。
#调用reader对象的read()方法，读取两个额外的TIFF图像，并将结果存储在变量SC中。这些图像将用于进行阴影校正。
# 对主要数据进行了归一化处理。首先，从主要数据中减去黑场图像（SC[0]），然后将结果除以白场图像和黑场图像之间的差值（SC[1]-SC[0]）。这样做可以消除由于不均匀灯光和探测器响应等因素引起的阴影。最终的归一化数据存储在变量data中。

tiffs = [os.path.join(path_common,'Lego_Lamino30deg_ShadingCorrection_XTH/Dark_80kV85uA.tif'),
            os.path.join(path_common,'Lego_Lamino30deg_ShadingCorrection_XTH/Flat_80kV85uA.tif') ]
967.32
reader = TIFFStackReader(file_name=tiffs, roi=roi)
SC = reader.read()
data = (data-SC[0]) / (SC[1]-SC[0])

# %% set up the geometry of the AcquisitionData
# 用来设置采集数据几何
# parameters are from the original paper/author clarification

src_to_det = 967.3209839		#源到探测器的距离，相当于r2
src_to_object = 295				#源到物体的距离，相当于r1
tilt = 30. * np.pi / 180.		#倾斜角，将30度转换为弧度
centre_of_rotation = 0.254 * 6. #旋转中心的位置

mag = src_to_det / src_to_object 				# 放大率
object_offset_x = centre_of_rotation / mag		# 物体的x方向偏移。

source_pos_y = -src_to_object		  			# 源的y方向位置。
detector_pos_y = src_to_det-src_to_object		# 探测器的y方向位置。
angles_list = -np.linspace(0, 360, num=data.shape[0], endpoint=False)	#角度列表，从0度到	360度，步长由数据的数量决定，endpoint=False表示不包括360度。num=data.shape[0]表示数据集中的数据数量或者图像的帧数
num_pixels_x = data.shape[2]					# 数据的x和y方向的像素数量。
num_pixels_y = data.shape[1]
pixel_size_xy = 0.254*bins						#像素的大小，乘以之前定义的子采样因子。

#创建了一个三维锥形采集几何对象，并对其进行了一系列设置
ag = AcquisitionGeometry.create_Cone3D( source_position=[0.0,source_pos_y,0.0], \
                                        detector_position=[0.0,detector_pos_y,0.0],\
                                        rotation_axis_position=[object_offset_x,0,0],\
                                        rotation_axis_direction=[0,-np.sin(tilt), np.cos(tilt)] ) \		#指定了旋转轴的方向向量，其中 np.sin(tilt) 和 np.cos(tilt) 分别表示倾斜角 tilt 的正弦值和余弦值，而 x 方向上的方向向量设置为 0。
                        .set_angles(angles=angles_list, angle_unit='degree')\
                        .set_panel( num_pixels=[num_pixels_x, num_pixels_y], \
                                    pixel_size=pixel_size_xy,\
                                    origin='top-left')
print(ag)			#打印创建的采集几何对象。


# %% create AcquisitonData (data + geometry)
#使用采集数据 data 和先前定义的几何参数 ag，创建了一个原始的采集数据对象 aq_data_raw。参数 False 表示数据不需要进行重新排列。
aq_data_raw = AcquisitionData(data, False, geometry=ag)

# %% convert to attenuation
#将原始采集数据转换为衰减数据的。

aq_data = aq_data_raw.log()	#将原始采集数据对象 aq_data_raw 转换为对数空间，以获得衰减数据。
aq_data *= -1				#将转换后的衰减数据取负，以保持数据的正方向。

# %% view data
ag = aq_data.geometry		#获取处理后的采集数据的几何参数，并将其存储在 ag 变量中。
islicer(aq_data,direction='angle')		# 使用 islicer 函数查看处理后的采集数据，direction='angle' 指定了切片的方向为角度方向。

# %% Set up reconstruction volume设置重建体
#get_ImageGeometry() 创建了一个与采集几何相对应的图像几何对象 ig。这个图像几何对象将用于设置重建体的几何参数。
#
#
ig = ag.get_ImageGeometry()
ig.voxel_num_x = int(num_pixels_x - 200/bins)
ig.voxel_num_y = int(num_pixels_x - 600/bins)
ig.voxel_num_z = int(400//bins)
print(ig)	# 打印重建体的图像几何对象 ig 的信息。
 
# %% Reconstruct with FDK
fbp = FBP(ig, ag)		# 创建了一个 FBP（Filtered Back Projection）对象 fbp，并传入重构体的图像几何 ig 和采集几何 ag
fbp.set_input(aq_data)	#将处理后的衰减数据 aq_data 设置为 FBP 对象的输入。
FBP_3D_gpu = fbp.get_output()	#调用 FBP 对象的 get_output() 方法，执行 FDK 算法，得到重构后的三维图像数据，并将其存储在 FBP_3D_gpu 变量中。

show2D(FBP_3D_gpu,slice_list=[('vertical',204//bins),('horizontal_y',570//bins)], title="FBP reconstruction", fix_range=(-0.02,0.07))




# %% Setup least sqaures and force pre-calculation of Lipschitz constant
# Projector = ProjectionOperator(ig, ag)
# LS = LeastSquares(A=Projector, b=aq_data)
# print("Lipschitz constant =", LS.L)
#
# # %% Setup FISTA to solve for least squares
# fista = FISTA(x_init=ig.allocate(0), f=LS, g=ZeroFunction(), max_iteration=1000)
# fista.update_objective_interval = 10
#
# # %% Run FISTA
# fista.run(300)
# LS_reco = fista.get_output()
# show2D(LS_reco,slice_list=[('vertical',204//bins),('horizontal_y',570//bins)], title="LS reconstruction", fix_range=(-0.02,0.07))
#
# # %%
# plt.figure()
# plt.semilogy(fista.objective)
# plt.title('FISTA LS criterion')
# plt.show()
#
# # %% Setup total-variation (TV) with a non-negativity (NN) contraint
# alpha = 1
# TV = alpha*FGP_TV(isotropic=True, device='gpu')
#
# # %% Setup FISTA to solve for LS with TV+NN
# fista = FISTA(x_init=ig.allocate(0), f=LS, g=TV, max_iteration=1000)
# fista.update_objective_interval = 10
#
# # %% Run FISTA
# fista.run(300)
# TV_NN_reco_isotropic = fista.get_output()
# show2D(TV_NN_reco_isotropic,slice_list=[('vertical',204//bins),('horizontal_y',570//bins)], title="TV_NN 100it reconstruction", fix_range=(-0.02,0.07))
#
# # %%
# plt.figure()
# plt.semilogy(fista.objective)
# plt.title('FISTA criterion')
# plt.show()
#
# # %% Setup total-variation (TV) with a non-negativity (NN) contraint
# alpha = 1
# TV = alpha*FGP_TV(isotropic=False, device='gpu')
#
# # %% Setup FISTA to solve for LS with TV+NN
# fista = FISTA(x_init=ig.allocate(0), f=LS, g=TV, max_iteration=1000)
# fista.update_objective_interval = 10
#
# # %% Run FISTA
# fista.run(300)
# TV_NN_reco_anisotropic = fista.get_output()
# show2D(TV_NN_reco_anisotropic,slice_list=[('vertical',204//bins),('horizontal_y',570//bins)], title="TV_NN 100it reconstruction", fix_range=(-0.02,0.07))
#
#
# # %%write to file
# # FBP_3D_gpu.array.astype(np.float32).tofile("FBP_vol.raw")
# # LS_reco.array.astype(np.float32).tofile("LS_vol.raw")
# # TV_NN_reco_isotropic.array.astype(np.float32).tofile("TV_NN_reco_isotropic_500.raw")
# # TV_NN_reco_anisotropic.array.astype(np.float32).tofile("TV_NN_reco_anisotropic_500.raw")
#
# print("fin")
# # %%

```

```
#高光谱.mat数据转为.tif格式--python代码
import scipy.io
import numpy as np
import tifffile
 
# 读取MAT文件
mat_data = scipy.io.loadmat('hyperspectral_data.mat')
 
# 从MAT文件中提取高光谱数据（假设数据存储在'hyperspectral_data'变量中）
hyperspectral_data = mat_data['hyperspectral_data']
 
# 获取数据维度
num_rows, num_cols, num_bands = hyperspectral_data.shape
 
# 初始化一个空的三维数组来保存转换后的数据
tif_data = np.empty((num_bands, num_rows, num_cols), dtype=np.uint16)
 
# 将数据重新排列，使得波段成为第一个维度
for band in range(num_bands):
    tif_data[band, :, :] = hyperspectral_data[:, :, band]
 
# 保存为TIFF文件
  tifffile.imsave(save_path, tif_data)
```

[mat数据存为tif - CSDN文库](https://wenku.csdn.net/answer/5f18bi4pqg)

F:\04Code\python\pythonDemo\RCL_CIL\RCL_Data

![image-20240508143246239](C:\Users\LXX\AppData\Roaming\Typora\typora-user-images\image-20240508143246239.png)

![image-20240508143309345](C:\Users\LXX\AppData\Roaming\Typora\typora-user-images\image-20240508143309345.png)

![image-20240508144131429](C:\Users\LXX\AppData\Roaming\Typora\typora-user-images\image-20240508144131429.png)

[学位论文-复合扫描计算机分层方法研究.pdf](file:///F:/02_研究工作目录/01 CL重建paper/学位论文-复合扫描计算机分层方法研究.pdf)

用于断层扫描图像处理的软件库已经存在，例如**TomoPy [[5](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C5)]、ASTRA [[6](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C6)]、TIGRE [[7](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C7)]、Savu [[8](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C8)]、AIR Tools II [[9](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C9)]和CASToR [[10](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C10)]**。同样，有许多Matlab和Python工具箱可用于指定和求解与成像相关的优化问题，包括**FOM [[11](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C11)]、GlobalBioIm [[12](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C12)]、ODL [[13](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C13)]、ProxImaL [[14](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C14)]和TFOCS [[15](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0192#RSTA20200192C15)]。**

```matlab
% ---------------------------------------------------------------------------------------
% Funcation: This file is part of the TIGRE Toolbox for RCL(圆周CL) reconstruction
% Note:      Written by Tan Chuandong
% Date:      2023-09
%---------------------------------------------------------------------------------------
%% Initialize
clear;
close all;
clear all;
addpath(genpath('E:\TIGRE-master'))
addpath(genpath('E:\astra-toolbox-2.1.0-matlab-win-x64'))

%% Define Geometry
% Geometic parameters
geo.DSD = 200;                                     % Distance Source Detector      (mm)
geo.DSO = 50;                                      % Distance Source Origin        (mm)
% Detector parameters
geo.nDetector = [512;512];					       % Number of pixels              (px)
geo.dDetector = [0.1;0.1]; 				           % Size of each pixel            (mm)
geo.sDetector = geo.nDetector.*geo.dDetector;      % Total size of the detector    (mm)
% RCL parameters
view = 50;
angle_ave = linspace(0,view-1,view)/view*2*pi;
theta = 90;                                         % 倾斜角度(theta)  重要参数
R1 = geo.DSO*tan((theta/2)/180*pi);
R2 = geo.DSD*tan((theta/2)/180*pi);
% Detector position
det_coor(:,1) = (R2)*cos(angle_ave);
det_coor(:,2) = (R2)*sin(angle_ave);
% source position
ori_coor(:,1) = (R1)*cos(angle_ave);
ori_coor(:,2) = (R1)*sin(angle_ave);
% Image parameters
geo.nVoxel = [31;512;512];                         % number of voxels              (vx)
geo.sVoxel = geo.nVoxel.*[0.02;0.02;0.02];         % total size of the image       (mm)
geo.dVoxel = geo.sVoxel./geo.nVoxel;               % size of each voxel            (mm)
% Offsets 
geo.offOrigin = zeros(3,view);                     % Offset of image from origin   (mm)              
geo.offOrigin(2:3,:) = ori_coor';
geo.offDetector = det_coor';                       % Offset of Detector            (mm)
% Detector rotation
geo.rotDetector=[0;0;0];                           % Rotation of the detector, by 
                                                   % X,Y and Z axis respectively. (rad)
% Auxiliary 
geo.accuracy = 0.5;                                % Accuracy of FWD proj          (vx/sample)
geo.mode = 'cone';                                 % parallel or cone

%% angle
angles = linspace(0,0,view);

%% Load data and generate projections 
load('cube3.mat')
cube=single(cube3);
% shepp_type='Modified Shepp-Logan';                    % (default).
% shepp=sheppLogan3D([512,512,512],shepp_type);         % Default are 128^3 and Modified shepp-logan
% shepp =shepp(206:236,:,:);
projections = Ax(cube,geo,angles);
plotImg([projections],'Dim',3);

%% Reconsturction 
% imgOSSART = OS_SART(projections,geo,angles,300);
% % imgSIRT = SIRT(projections,geo,angles,20);
% imgSARTTV=SART_TV(projections,geo,angles,20,'TViter',50,'TVlambda',50);
% figure(); imshow(squeeze(imgOSSART(17,:,:)),[0 1]);
imgFDK=FDK(projections,geo,angles);

```

```                                           matlab
>> Rec_RCL_3D
>> plotImg([projections],'Dim',3);
>> imgFDK=FDK(projections,geo,angles);
警告: Wang weights: varying offDetector detected, Wang weights not being applied 
> In apply_wang_weights (line 3)
  In redundancy_weighting (line 20)
  In FDK (line 50) 
警告: Scanning angles smaller than pi+cone_angle. This is limited angle tomgraphy, there is no sufficient data, thus weighting for data
redundancy is not required. 
> In ParkerWeight (line 32)
  In filtering (line 24)
  In FDK (line 76) 
>> figure(); imshow(squeeze(imgOSSART(17,:,:)),[0 1]);
>> figure(); imshow(squeeze(imgFDK(17,:,:)),[]);
>> figure(); imshow(squeeze(imgFDK(17,:,:)),[]);
>> max(max(max(imgFDK)))

ans =

  single

     0

>> 5%
```

![image-20240508200132829](C:\Users\LXX\AppData\Roaming\Typora\typora-user-images\image-20240508200132829.png)

```
% Detector position
%det_coor(:,2) = 0;
% source position
%ori_coor(:,1) = R1;
%ori_coor(:,2) = 0;
% Image parameters
geo.nVoxel = [31;512;512];                         % number of voxels              (vx)
geo.sVoxel = geo.nVoxel.*[0.02;0.02;0.02];         % total size of the image       (mm)
geo.dVoxel = geo.sVoxel./geo.nVoxel;               % size of each voxel            (mm)
% Offsets
%ori_coor_rep = repmat(ori_coor', [1, view]);
%geo.offOrigin = zeros(3,view);                     % Offset of image from origin   (mm)              
%geo.offOrigin(2:3,:) = ori_coor_rep;
%geo.offDetector = det_coor';                       % Offset of Detector            (mm)
geo.offOrigin = [0, -R2, 0];
geo.offDetector = [0, R1, 0];
```

```matlab
% ---------------------------------------------------------------------------------------
% Funcation: This file is part of the TIGRE Toolbox for RCL(圆周CL) reconstruction
% Note:      Written by Tan Chuandong
% Date:      2024-5  试错
%---------------------------------------------------------------------------------------
%% Initialize
clear;
close all;
clear all;
addpath(genpath('E:\TIGRE-master'))
addpath(genpath('E:\astra-toolbox-2.1.0-matlab-win-x64'))

%% Define Geometry
% Geometic parameters
geo.DSD = 200;                                     % Distance Source Detector      (mm)
geo.DSO = 50;                                      % Distance Source Origin        (mm)
% Detector parameters
geo.nDetector = [512;512];					       % Number of pixels              (px)
geo.dDetector = [0.1;0.1]; 				           % Size of each pixel            (mm)
geo.sDetector = geo.nDetector.*geo.dDetector;      % Total size of the detector    (mm)
% RCL parameters
view = 50;
angle_ave = linspace(0,view-1,view)/view*2*pi;
theta = 90;                                         % 倾斜角度(theta)  重要参数
R1 = geo.DSO*tan((theta/2)/180*pi);
R2 = geo.DSD*tan((theta/2)/180*pi);
% Detector position
%det_coor(:,2) = 0;
% source position
%ori_coor(:,1) = R1;
%ori_coor(:,2) = 0;
% Image parameters
geo.nVoxel = [31;512;512];                         % number of voxels              (vx)
geo.sVoxel = geo.nVoxel.*[0.02;0.02;0.02];         % total size of the image       (mm)
geo.dVoxel = geo.sVoxel./geo.nVoxel;               % size of each voxel            (mm)
% Offsets
%ori_coor_rep = repmat(ori_coor', [1, view]);
%geo.offOrigin = zeros(3,view);                     % Offset of image from origin   (mm)              
%geo.offOrigin(2:3,:) = ori_coor_rep;
%geo.offDetector = det_coor';                       % Offset of Detector            (mm)
geo.offOrigin = [0;-R2;0];
geo.offDetector = [R1;0];

% Detector rotation.

geo.rotDetector=[0;0;0];                           % Rotation of the detector, by 
                                                   % X,Y and Z axis respectively. (rad)
% Auxiliary 
geo.accuracy = 0.5;                                % Accuracy of FWD proj          (vx/sample)
geo.mode = 'cone';                                 % parallel or cone

%% angle
angles = linspace(0,2*pi,view);

%% Load data and generate projections 
load('cube3.mat')
cube=single(cube3);
% shepp_type='Modified Shepp-Logan';                    % (default).
% shepp=sheppLogan3D([512,512,512],shepp_type);         % Default are 128^3 and Modified shepp-logan
% shepp =shepp(206:236,:,:);
projections = Ax(cube,geo,angles);
plotImg([projections],'Dim',3);

%% Reconsturction 
% imgOSSART = OS_SART(projections,geo,angles,300);
% % imgSIRT = SIRT(projections,geo,angles,20);
% imgSARTTV=SART_TV(projections,geo,angles,20,'TViter',50,'TVlambda',50);
% figure(); imshow(squeeze(imgOSSART(17,:,:)),[0 1]);
imgFDK=FDK(projections,geo,angles);
```

## cli环境配置

```c
1、创建环境
conda create --name cil -c conda-forge -c https://software.repos.intel.com/python/conda -c ccpi cil=23.0.0
2、numpy库
删除numpy库、重新安装
pip uninstall numpy
conda install numpy
3、安装tigre库
conda install -c ccpi tigre
4、安装opencv库
pip install opencv-python  
5、安装scikit-image
conda install scikit-image
6、安装ccpi-regulariser
conda install -c ccpi ccpi-regulariser
7、安装ipywidgets
pip install ipywidgets



conda create --name learn -c conda-forge -c intel -c ccpi cil=23.0.0
```

