---
title: CL-to-CT代码
date: 2024-08-02 19:32:35
tags: tran_CL_CT
categories: 科研/论文
cover: /img/16.jpg
---
# Tran_RCL

## GenerateCLProjection.m

```matlab
clear;clc;close all;
%% Initialize
clear;
close all;
addpath(genpath('E:\2022-TIGRE-master'))
addpath(genpath('E:\astra-toolbox-2.1.0-matlab-win-x64'))
%% Geometry
geo=defaultGeometry();  %几何采用默认几何
%% Define angles of projection and load phatom image
% shepp_type='yu-ye-wang'; 
% shepp_type='Shepp-Logan'; 
shepp_type='Modified Shepp-Logan';  % (default).
shepp=sheppLogan3D([256,256,256],shepp_type); % Default are 128^3 and Modified shepp-logan
head=headPhantom([256,256,256]);
% angles=[anglesZ1;anglesY;anglesZ2];
numProjs=180;
theta=pi/4;
%angles=[linspace(0,2*pi,numProjs)];
angles=[linspace(0,2*pi,numProjs);zeros(1,numProjs).*theta;zeros(1,numProjs).*theta];
%angles1=[linspace(0,2*pi,numProjs);ones(1,numProjs).*theta;zeros(1,numProjs).*theta];

%projections 是计算得到的投影数据，维度为 (height, width, numProjs)。
projections=Ax(shepp,geo,angles);
%projections1=Ax(shepp,geo,angles1);
%plotImg([projections projections1],'Dim',3);

%调用 plotImg 函数来显示投影数据 projections，沿第3维（投影角度）显示。
plotImg([projections],'Dim',3);
plotProj(projections,(1:numProjs)*2*pi/180); % angle information not right in the title

imwrite(projections(:,:,45),'pro45.png');
abc=projections(:,:,45);
```

## ProjectionTransformation.m

```matlab
%--------------------------------------------------------------------------
%% Initialize
% Note:      Written by Lin
% Date:      2024-07
% clear;clc;
% close all;

%load projections.mat
ProjectionNum=size(projections,3);
geo=defaultGeometry(); 
theta=pi/4;
alpha=pi/2-abs(theta);
FDD=geo.DSD;
FDDCT=FDD*tan(theta);
DetectorOffsetXCT=FDD;                             

%获取探测器的宽度和高度，以像素为单位
width=geo.nDetector(1);
height=geo.nDetector(2);
%获取每个像素的大小，以毫米为单位。
Psize=geo.dDetector(1);
Fi=width/2;
Fj=height/2;
%初始化一个大小为 ProjectionNum x 1 的单元数组 NewProjections，用于存储转换后的投影数据。
NewProjections=cell(ProjectionNum,1);
% NewProjections=zeros(ProjectionNum,1334,1747,'uint16');
%% 投影转换

for num=1:ProjectionNum
    %     将CL投影坐标四角转为空间坐标
    XA=TransformToSpatialPosition(1,1,Fi,Fj,FDD,Psize,theta);
    XB=TransformToSpatialPosition(width,1,Fi,Fj,FDD,Psize,theta);
    XC=TransformToSpatialPosition(width,height,Fi,Fj,FDD,Psize,theta);
    XD=TransformToSpatialPosition(1,height,Fi,Fj,FDD,Psize,theta);
    %     将四角空间坐标转为CT投影坐标
    XA1=TransformToCTProjection(XA,FDD,theta);
    XB1=TransformToCTProjection(XB,FDD,theta);
    XC1=TransformToCTProjection(XC,FDD,theta);
    XD1=TransformToCTProjection(XD,FDD,theta);
    %     获取CT投影坐标范围
    xP=min(XA1(1),XD1(1));
    xQ=max(XB1(1),XC1(1));
    % PQRS投影面在i轴的像素尺寸，生成从 xP 到 xQ 的等间距坐标，步长为 Psize
    xt=linspace(xP,xQ,abs((xP-xQ)./Psize));
    yP=XA1(2);
    yS=XD1(2);
    % PQRS投影面在j轴的像素尺寸，生成从 yP 到 yS 的等间距坐标 yt，步长为 Psize / cos(theta)。
    yt=linspace(yP,yS,abs((yS-yP)./Psize./cos(theta)));
    zt=(yt.*sin(theta)-FDD.*tan(theta))./cos(theta);
    %获取第num个投影图像·img
    img=projections(:,:,num);
    NewProjection=zeros(size(zt,2),size(xt,2),'uint16');
    % 对所有 (i, j) 对进行操作。
    parfor iter=1:(size(zt,2)*size(xt,2))
        [j,i]=ind2sub([size(zt,2),size(xt,2)],iter);
%     for i=1:size(xt,2)
%         for j=1:size(zt,2)
            %                         获取CT投影坐标在CL投影上强度一致的坐标
            gamma = theta - atan(zt(j)./yt(j));
            index=[ FDD.*sec(gamma).*yt(j)./sqrt(yt(j).^2+zt(j).^2).*xt(i)./yt(j),
                    FDD.*sec(gamma).*yt(j)./sqrt(yt(j).^2+zt(j).^2),
                    FDD.*sec(gamma).*yt(j)./sqrt(yt(j).^2+zt(j).^2).*zt(j)./yt(j)];
            %                         在CL投影上对应的像素坐标
            indexInt=[index(1)./Psize+Fi,Fj-index(3)./Psize./cos(theta)];
%             indexInt=[index(1),index(3)]./Psize;
            NewProjection(iter)=InstensityByInterpolation(indexInt,img);
%         end
%     end
    end
    NewProjections{num,1}=NewProjection;
    %     subplot(1,2,1)
    %     imagesc(NewProjections{16,1})
    %     subplot(1,2,2)
    %     imagesc(img)
end
CTProjections=zeros(259,293,180,'single');
for i=1:180
    CTProjections(:,:,i)=NewProjections{i};
end
%% 保存投影
save('ProjectionTransformation.mat','CTProjections','FDDCT','DetectorOffsetXCT');



%% 子函数

function instensity=InstensityByInterpolation(indexInt,img)
indexFloor=floor(indexInt);
indexFloor(indexFloor<=0)=1;
if indexFloor(1)<2 ||indexFloor(2)<2 || indexFloor(1)>(size(img,2)-1)|| indexFloor(2)>(size(img,1)-1)
    instensity=0;
    return;
end

x=indexFloor(1);
z=indexFloor(2);
u=indexInt(1)-indexFloor(1);
v=indexInt(2)-indexFloor(2);
instensity=u*v*img(z,x)+(1-u)*v*img(z+1,x)+u*(1-v)*img(z,x+1)+(1-u)*(1-v)*img(z+1,x+1);
return;
end

function Xw1=TransformToCTProjection(Xw,FDD,theta)
Xw1=FDD.*tan(theta)./(Xw(2).*sin(theta)-Xw(3).*cos(theta)).*Xw;
end
function Xw=TransformToSpatialPosition(i,j,Fi,Fj,FDD,Psize,theta)
Xw=[(i-Fi).*Psize,FDD.*sec(theta)+(j-Fj).*sin(theta).*Psize,(Fj-j).*cos(theta).*Psize];
end
 
```

## Reconstruction.m

```matlab
%clear;close all;clc;
%load CLprojection.mat
%% CLFDK重建
theta=pi/4;
geo=defaultGeometry();
numProjs=90;
angles=[linspace(0,2*pi,numProjs)];%ones(1,numProjs).*theta;zeros(1,numProjs).*theta];
CLFDK=FDK(projections(:,:,1:2:180),geo,angles); %选择 projections 矩阵的第一个和第二个维度的所有元素
% clear projections 
%% 投影转换FDK重建
load ProjectionTransformation.mat
% plotImg(CTProjections,'Dim','Z')

alpha=pi/2-abs(theta);
geo1=geo;
geo1.DSD = geo.DSD*tan(theta);                       % Distance Source Detector      (mm)
geo1.DSO = geo.DSO*tan(theta);                        % Distance Source Origin        (mm)
% geo1.offDetector=[0;FDD*sin(alpha)]; 

% Detector parameters
geo1.nDetector=[293;259];					% number of pixels              (px)
geo1.sDetector=[293*0.8;259*0.8];            % total size of the detector    (mm)
geo1.dDetector=geo1.sDetector./geo1.nDetector;					% size of each pixel            (mm)
geo1.nVoxel=[256;256;256];                          % number of voxels              (vx)
geo1.sVoxel=[128;128;128];                   % total size of the image       (mm)
geo1.dVoxel=geo1.sVoxel./geo1.nVoxel;          % size of each voxel            (mm)

CTFDK=FDK(CTProjections(:,:,1:2:180),geo1,linspace(0,2*pi,numProjs));
% CTMax=max(CTFDK,[],'all');
% CTMin=min(CTFDK,[],'all');
% plotImg(CTFDK,'Dim','Z')

% figure
% imagesc(CLFDK(:,:,128));
% axis equal
% figure
% imagesc(CTFDK(:,:,128));
% axis equal

%% ASD-POCS投影转换重建
epsilon=im3Dnorm(Ax(FDK(CTProjections,geo1,linspace(0,2*pi,numProjs)),geo1,linspace(0,2*pi,numProjs))-CTProjections,'L2')*0.15;
alpha=0.002;
ng=25;
lambda=1;
lambdared=0.9999;
alpha_red=0.95;
ratio=0.94;
qualmeas={'RMSE'};
verb=true;
targetGpuName = 'NVIDIA GeForce RTX 3080';
gpuids = GpuIds(targetGpuName);

[CTASDPOCS,qualMeasOut]=ASD_POCS(CTProjections,geo1,linspace(0,2*pi,numProjs),50,...
                    'TViter',ng,'maxL2err',epsilon,'alpha',alpha,... % these are very important
                     'lambda',lambda,'lambda_red',lambdared,'Ratio',ratio,'Verbose',verb,'gpuids', gpuids,'QualMeas',qualmeas); % less important.
save CTASDPOCS.mat CTASDPOCS
 plotImg(CTASDPOCS,'Dim','Z')
% qualMeasossart=Measure_Quality(head,imgOSSART,{'RMSE','MSSIM','CC','UQI','error_norm'})
%% PTAR-POCS投影转换重建

[CTPTARPOCS,qualMeasOut]=ASD_POCS(CTProjections,geo1,linspace(0,2*pi,numProjs),50,...
                    'TViter',ng,'maxL2err',epsilon,'alpha',alpha,... % these are very important
                     'lambda',lambda,'lambda_red',lambdared,'Ratio',ratio,'Verbose',verb,'gpuids', gpuids,'QualMeas',qualmeas); % less important.
save CTPTARPOCS.mat CTPTARPOCS
 plotImg(CLFDK,'Dim','Z')


[CLPTARPOCS,qualMeasOut]=ASD_POCS(projections,geo,angles,50,...
                    'TViter',ng,'maxL2err',epsilon,'alpha',alpha,... % these are very important
                     'lambda',lambda,'lambda_red',lambdared,'Ratio',ratio,'Verbose',verb,'gpuids', gpuids,'QualMeas',qualmeas); % less important.
save CLPTARPOCS.mat CLPTARPOCS







shepp_type='Modified Shepp-Logan';  % (default).
shepp=sheppLogan3D([256,256,256],shepp_type); % Default are 128^3 and Modified shepp-logan
CLFDKMeasossart=Measure_Quality(shepp(:,:,128),CLFDK(:,:,128),{'RMSE','MSSIM','CC','UQI','error_norm'});
CTFDKMeasossart=Measure_Quality(shepp(:,:,128),CTFDK(:,:,128),{'RMSE','MSSIM','CC','UQI','error_norm'});
CTASDPOCSMeasossart=Measure_Quality(shepp(:,:,128),CTASDPOCS(:,:,128),{'RMSE','MSSIM','CC','UQI','error_norm'});
CTPTARPOCSMeasossart=Measure_Quality(shepp(:,:,128),CTPTARPOCS(:,:,128),{'RMSE','MSSIM','CC','UQI','error_norm'});
CLPTARPOCSSMeasossart=Measure_Quality(shepp(:,:,128),CLPTARPOCS(:,:,128),{'RMSE','MSSIM','CC','UQI','error_norm'});
Measossart=[CLFDKMeasossart;CTFDKMeasossart;CTASDPOCSMeasossart;CTPTARPOCSMeasossart;CLPTARPOCSSMeasossart];
% 
% [0.043239966,5.7951517e-08,0.97391754,0.97190255,177.11090]
% [0.37232536,1.2802381e-08,0.40903839,0.21237694,1525.0448]
% [0.41519678,1.0290297e-08,0.36071393,0.17049415,1700.6467]
% [0.36632201,1.2440876e-08,0.39973879,0.20624949,1500.4551]
% [0.13420469,4.2803784e-08,0.72072893,0.71410239,549.70239]
% 
% 
CTMax=max(CTFDK,[],'all');
CTMin=min(CTFDK,[],'all');
imwrite(uint16((CTFDK(:,:,1)-CTMin)./(CTMax-CTMin)*65535),'CTFDK.tif')
for i=2:256
    imwrite(uint16((CTFDK(:,:,i)-CTMin)./(CTMax-CTMin)*65535),'CTFDK.tif','WriteMode','append');
end

```

## Tran_Rec_RCL_3D

```matlab
% ---------------------------------------------------------------------------------------
% Funcation: This file is part of the TIGRE Toolbox for RCL(圆周CL) reconstruction
% Note:      Written by Tan Chuandong 、Lin
% Date:      2023-09
%---------------------------------------------------------------------------------------
%% Initialize
clear;
close all;
clear all;
addpath(genpath('E:\2022-TIGRE-master'))
addpath(genpath('E:\astra-toolbox-2.1.0-matlab-win-x64'))

%% Define Geometry
% Geometic parameters
geo.DSD = 1536;                                     % Distance Source Detector      (mm)
geo.DSO = 1000;                                      % Distance Source Origin        (mm)
% Detector parameters
geo.nDetector = [512;512];					       % Number of pixels              (px)
geo.dDetector = [0.1;0.1]; 				           % Size of each pixel            (mm)
geo.sDetector = geo.nDetector.*geo.dDetector;      % Total size of the detector    (mm)
% RCL parameters

view = 180;
angle_ave = linspace(0,view-1,view)/view*2*pi;
theta = 90;                                         % 倾斜角度(theta)  重要参数
R1 = geo.DSO*tan((theta/2)/180*pi);
R2 = geo.DSD*tan((theta/2)/180*pi);
% Detector position
det_coor(:,1) = (R2)*cos(angle_ave);
det_coor(:,2) = (R2)*sin(angle_ave);
% Object position
obj_coor(:,1) = (R1)*cos(angle_ave);
obj_coor(:,2) = (R1)*sin(angle_ave);
% Image parameters
geo.nVoxel = [31;512;512];                         % number of voxels像素          (vx)
geo.dVoxel = [0.05;0.05;0.05];               % size of each voxel            (mm)
geo.sVoxel = geo.nVoxel.* geo.dVoxel;                            % total size of the image       (mm)

% Offsets ,geo.offOrigin =[0;0;0];表示图像中心位置的偏移量为零，即基坐标系的原点O没有偏移，位于物体的中心。
geo.offOrigin = zeros(3,view);                     % Offset of image from origin   (mm)              
geo.offOrigin(2:3,:) = obj_coor';

geo.offDetector = det_coor';                       % Offset of Detector            (mm)
% Detector rotation
% y = [0,22.5, 45, 22.5, 0,-22.5,-45, -22.5]/180*pi;
% z = [-45,-22.5, 0,22.5, 45, 22.5, 0, 22.5]/180*pi;
% geo.rotDetector = zeros(3,view); 
% geo.rotDetector(2:2,:) = y';
% geo.rotDetector(3:3,:) = z';                       % Rotation of the detector, by 
                                                   % X,Y and Z axis respectively. (rad)
geo.rotDetector = [0;0;0];
% Auxiliary 
geo.accuracy = 0.5;                                % Accuracy of FWD proj          (vx/sample)
geo.mode = 'cone';

% parallel or cone

%% angle
angles = linspace(0,0,view);                       %旋转轴的转动

%% Load data and generate projections 
load('cube3.mat')
cube=single(cube3);
% shepp_type='Modified Shepp-Logan';                    % (default).
% shepp=sheppLogan3D([512,512,512],shepp_type);         % Default are 128^3 and Modified shepp-logan
% shepp =shepp(206:236,:,:);
projections = Ax(cube,geo,angles);
plotImg([projections],'Dim',3);

%% Reconsturction 
imgOSSART1 = OS_SART(projections,geo,angles,20);
% % imgSIRT = SIRT(projections,geo,angles,20);
% % imgSARTTV=SART_TV(projections,geo,angles,20,'TViter',50,'TVlambda',50);
% figure(); imshow(squeeze(imgOSSART(17,:,:)),[0 1]);
rmse1 = RMSE(cube,imgOSSART1);
ssim1 =ssim(cube,imgOSSART1);
psnr1 = psnr(cube,imgOSSART1);

```

# 2024/07/14 20:47

## GenerateCLProjection.m

```matlab
clear;clc;close all;
%% Initialize
clear;
close all;
%% Geometry
geo=defaultGeometry();
%% Define angles of projection and load phatom image


% shepp_type='yu-ye-wang'; 
% shepp_type='Shepp-Logan'; 
shepp_type='Modified Shepp-Logan';  % (default).
shepp=sheppLogan3D([256,256,256],shepp_type); % Default are 128^3 and Modified shepp-logan
head=headPhantom([256,256,256]);
% angles=[anglesZ1;anglesY;anglesZ2];
numProjs=180;
theta=pi/4;
angles=[linspace(0,2*pi,numProjs);zeros(1,numProjs).*theta;zeros(1,numProjs).*theta];
projections=Ax(shepp,geo,angles);
% plotProj(projections,(1:numProjs)*2*pi/180); % angle information not right in the title

imwrite(projections(:,:,45),'pro45.png');
abc=projections(:,:,45);
```

```matlab
clear;clc;close all;
%% Initialize
clear;
close all;
addpath(genpath('E:\2022-TIGRE-master'))
addpath(genpath('E:\astra-toolbox-2.1.0-matlab-win-x64'))
%% Geometry
geo=defaultGeometry();  %几何采用默认几何
%% Define angles of projection and load phatom image
% shepp_type='yu-ye-wang'; 
% shepp_type='Shepp-Logan'; 
shepp_type='Modified Shepp-Logan';  % (default).
shepp=sheppLogan3D([256,256,256],shepp_type); % Default are 128^3 and Modified shepp-logan
head=headPhantom([256,256,256]);
% angles=[anglesZ1;anglesY;anglesZ2];
numProjs=180;
theta=pi/4;
%angles=[linspace(0,2*pi,numProjs)];
angles=[linspace(0,2*pi,numProjs);zeros(1,numProjs).*theta;zeros(1,numProjs).*theta];
%angles1=[linspace(0,2*pi,numProjs);ones(1,numProjs).*theta;zeros(1,numProjs).*theta];

%projections 是计算得到的投影数据，维度为 (height, width, numProjs)。
projections=Ax(shepp,geo,angles);
%projections1=Ax(shepp,geo,angles1);
%plotImg([projections projections1],'Dim',3);

%调用 plotImg 函数来显示投影数据 projections，沿第3维（投影角度）显示。
plotImg([projections],'Dim',3);
plotProj(projections,(1:numProjs)*2*pi/180); % angle information not right in the title

imwrite(projections(:,:,45),'pro45.png');
abc=projections(:,:,45);
```



## Tran_Rec_RCL_3D.m

```matlab
% ---------------------------------------------------------------------------------------
% Funcation: This file is part of the TIGRE Toolbox for RCL(圆周CL) reconstruction
% Note:      Written by Tan Chuandong 、Lin
% Date:      2023-09
%---------------------------------------------------------------------------------------
%% Initialize
clear;
close all;
clear all;
addpath(genpath('E:\2022-TIGRE-master'))
addpath(genpath('E:\astra-toolbox-2.1.0-matlab-win-x64'))

%% Define Geometry
% Geometic parameters
geo.DSD = 1536;                                     % Distance Source Detector      (mm)
geo.DSO = 1000;                                      % Distance Source Origin        (mm)
% Detector parameters
geo.nDetector = [512;512];					       % Number of pixels              (px)
geo.dDetector = [0.1;0.1]; 				           % Size of each pixel            (mm)
geo.sDetector = geo.nDetector.*geo.dDetector;      % Total size of the detector    (mm)
% RCL parameters

numProjs = 180;
angle_ave = linspace(0,numProjs-1,numProjs)/numProjs*2*pi;
theta = 90;                                         % 倾斜角度(theta)  重要参数
R1 = geo.DSO*tan((theta/2)/180*pi);
R2 = geo.DSD*tan((theta/2)/180*pi);
% Detector position
det_coor(:,1) = (R2)*cos(angle_ave);
det_coor(:,2) = (R2)*sin(angle_ave);
% Object position
obj_coor(:,1) = (R1)*cos(angle_ave);
obj_coor(:,2) = (R1)*sin(angle_ave);
% Image parameters
geo.nVoxel = [31;512;512];                         % number of voxels像素          (vx)
geo.dVoxel = [0.05;0.05;0.05];               % size of each voxel            (mm)
geo.sVoxel = geo.nVoxel.* geo.dVoxel;                            % total size of the image       (mm)

% Offsets ,geo.offOrigin =[0;0;0];表示图像中心位置的偏移量为零，即基坐标系的原点O没有偏移，位于物体的中心。
geo.offOrigin = zeros(3,numProjs);                     % Offset of image from origin   (mm)              
geo.offOrigin(2:3,:) = obj_coor';

geo.offDetector = det_coor';                       % Offset of Detector            (mm)
% Detector rotation
% y = [0,22.5, 45, 22.5, 0,-22.5,-45, -22.5]/180*pi;
% z = [-45,-22.5, 0,22.5, 45, 22.5, 0, 22.5]/180*pi;
% geo.rotDetector = zeros(3,numProjs); 
% geo.rotDetector(2:2,:) = y';
% geo.rotDetector(3:3,:) = z';                       % Rotation of the detector, by 
                                                   % X,Y and Z axis respectively. (rad)
geo.rotDetector = [0;0;0];
% Auxiliary 
geo.accuracy = 0.5;                                % Accuracy of FWD proj          (vx/sample)
geo.mode = 'cone';

% parallel or cone

%% angle
angles = linspace(0,0,numProjs);                       %旋转轴的转动

%% Load data and generate projections 
load('cube3.mat')
cube=single(cube3);
% shepp_type='Modified Shepp-Logan';                    % (default).
% shepp=sheppLogan3D([512,512,512],shepp_type);         % Default are 128^3 and Modified shepp-logan
% shepp =shepp(206:236,:,:);
projections = Ax(cube,geo,angles);
plotImg([projections],'Dim',3);

%% Reconsturction 
imgOSSART1 = OS_SART(projections,geo,angles,20);
% % imgSIRT = SIRT(projections,geo,angles,20);
% % imgSARTTV=SART_TV(projections,geo,angles,20,'TViter',50,'TVlambda',50);
% figure(); imshow(squeeze(imgOSSART(17,:,:)),[0 1]);
rmse1 = RMSE(cube,imgOSSART1);
ssim1 =ssim(cube,imgOSSART1);
psnr1 = psnr(cube,imgOSSART1);

```



## ProjectionTransformation.m

```matlab
%--------------------------------------------------------------------------
%% Initialize
% Note:      Written by Lin
% Date:      2024-07
% clear;clc;
% close all;

%load projections.mat
ProjectionNum=size(projections,3);  %180;
%geo=defaultGeometry(); 
theta=pi/4;
%alpha=pi/2-abs(theta);
FDD=geo.DSD;    
FDDCT=FDD*tan(theta);   %FDDCT = FDD = 1536;
DetectorOffsetXCT=FDD;                             

%获取探测器的宽度和高度，以像素为单位
width=geo.nDetector(1);     %512
height=geo.nDetector(2);    %512
%获取每个像素的大小，以毫米为单位。
Psize=geo.dDetector(1);     %0.1
Fi=width/2;
Fj=height/2;
%初始化一个大小为 ProjectionNum x 1 的单元数组 NewProjections，用于存储转换后的投影数据。
NewProjections=cell(ProjectionNum,1);
% NewProjections=zeros(ProjectionNum,1334,1747,'uint16');
%% 投影转换

for num=1:ProjectionNum
    %     将CL投影坐标四角转为空间坐标
    XA=TransformToSpatialPosition(1,1,Fi,Fj,FDD,Psize,theta);
    XB=TransformToSpatialPosition(width,1,Fi,Fj,FDD,Psize,theta);
    XC=TransformToSpatialPosition(width,height,Fi,Fj,FDD,Psize,theta);
    XD=TransformToSpatialPosition(1,height,Fi,Fj,FDD,Psize,theta);
    %     将四角空间坐标转为CT投影坐标
    XA1=TransformToCTProjection(XA,FDD,theta);
    XB1=TransformToCTProjection(XB,FDD,theta);
    XC1=TransformToCTProjection(XC,FDD,theta);
    XD1=TransformToCTProjection(XD,FDD,theta);
    %     获取CT投影坐标范围
    xP=min(XA1(1),XD1(1));
    xQ=max(XB1(1),XC1(1));
    % PQRS投影面在i轴的像素尺寸，生成从 xP 到 xQ 的等间距坐标，步长为 Psize
    xt=linspace(xP,xQ,abs((xP-xQ)./Psize));
    yP=XA1(2);
    yS=XD1(2);
    % PQRS投影面在j轴的像素尺寸，生成从 yP 到 yS 的等间距坐标 yt，步长为 Psize / cos(theta)。
    yt=linspace(yP,yS,abs((yS-yP)./Psize./cos(theta)));
    zt=(yt.*sin(theta)-FDD.*tan(theta))./cos(theta);
    %获取第num个投影图像·img
    img=projections(:,:,num);
    NewProjection=zeros(size(zt,2),size(xt,2),'uint16');
    % 对所有 (i, j) 对进行操作。
    parfor iter=1:(size(zt,2)*size(xt,2))
        [j,i]=ind2sub([size(zt,2),size(xt,2)],iter);
%     for i=1:size(xt,2)
%         for j=1:size(zt,2)
            %                         获取CT投影坐标在CL投影上强度一致的坐标
            gamma = theta - atan(zt(j)./yt(j));
            index=[ FDD.*sec(gamma).*yt(j)./sqrt(yt(j).^2+zt(j).^2).*xt(i)./yt(j),
                    FDD.*sec(gamma).*yt(j)./sqrt(yt(j).^2+zt(j).^2),
                    FDD.*sec(gamma).*yt(j)./sqrt(yt(j).^2+zt(j).^2).*zt(j)./yt(j)];
            %                         在CL投影上对应的像素坐标
            indexInt=[index(1)./Psize+Fi,Fj-index(3)./Psize./cos(theta)];
%             indexInt=[index(1),index(3)]./Psize;
            NewProjection(iter)=InstensityByInterpolation(indexInt,img);
%         end
%     end
    end
    NewProjections{num,1}=NewProjection;
    %     subplot(1,2,1)
    %     imagesc(NewProjections{16,1})
    %     subplot(1,2,2)
    %     imagesc(img)
end
CTProjections=zeros(511,519,180,'single');
for i=1:180
    CTProjections(:,:,i)=NewProjections{i};
end
%% 保存投影
save('ProjectionTransformation.mat','CTProjections','FDDCT','DetectorOffsetXCT');



%% 子函数

function instensity=InstensityByInterpolation(indexInt,img)
indexFloor=floor(indexInt);
indexFloor(indexFloor<=0)=1;
if indexFloor(1)<2 ||indexFloor(2)<2 || indexFloor(1)>(size(img,2)-1)|| indexFloor(2)>(size(img,1)-1)
    instensity=0;
    return;
end

x=indexFloor(1);
z=indexFloor(2);
u=indexInt(1)-indexFloor(1);
v=indexInt(2)-indexFloor(2);
instensity=u*v*img(z,x)+(1-u)*v*img(z+1,x)+u*(1-v)*img(z,x+1)+(1-u)*(1-v)*img(z+1,x+1);
return;
end

function Xw1=TransformToCTProjection(Xw,FDD,theta)
Xw1=FDD.*tan(theta)./(Xw(2).*sin(theta)-Xw(3).*cos(theta)).*Xw;
end
function Xw=TransformToSpatialPosition(i,j,Fi,Fj,FDD,Psize,theta)
Xw=[(i-Fi).*Psize,FDD.*sec(theta)+(j-Fj).*sin(theta).*Psize,(Fj-j).*cos(theta).*Psize];
end
 
```

## Reconstruction.m

```matlab
%clear;close all;clc;
%load CLprojection.mat
%% CLFDK重建
theta=pi/4;
%geo=defaultGeometry();
numProjs=180;
angles=[linspace(0,2*pi,numProjs)];%ones(1,numProjs).*theta;zeros(1,numProjs).*theta];
CLFDK=FDK(projections(:,:,1:1:180),geo,angles); %选择 projections 矩阵的第一个和第二个维度的所有元素
% clear projections 
%% 投影转换FDK重建
load ProjectionTransformation.mat
% plotImg(CTProjections,'Dim','Z')

alpha=pi/2-abs(theta);
geo1=geo;
geo1.DSD = geo.DSD*tan(theta);                       % Distance Source Detector      (mm)
geo1.DSO = geo.DSO*tan(theta);                        % Distance Source Origin        (mm)
% geo1.offDetector=[0;FDD*sin(alpha)]; 

% Detector parameters
geo1.nDetector=[511;519];					% number of pixels              (px)
geo1.sDetector=[511*0.8;519*0.8];            % total size of the detector    (mm)
geo1.dDetector=geo1.sDetector./geo1.nDetector;					% size of each pixel            (mm)
geo1.nVoxel=[31;512;512];                          % number of voxels              (vx)
geo.dVoxel = [0.05;0.05;0.05];               % size of each voxel            (mm)
geo.sVoxel = geo.nVoxel.* geo.dVoxel;            % size of each voxel            (mm)

CTFDK=FDK(CTProjections(:,:,1:1:180),geo1,linspace(0,2*pi,numProjs));
% CTMax=max(CTFDK,[],'all');
% CTMin=min(CTFDK,[],'all');
% plotImg(CTFDK,'Dim','Z')

% figure
% imagesc(CLFDK(:,:,128));
% axis equal
% figure
% imagesc(CTFDK(:,:,128));
% axis equal
```

## Rec_RCL

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
addpath(genpath('E:\2022-TIGRE-master'))
addpath(genpath('E:\astra-toolbox-2.1.0-matlab-win-x64'))

%% Define Geometry
% Geometic parameters
geo.DSD = 1536;                                     % Distance Source Detector      (mm)
geo.DSO = 1000;                                      % Distance Source Origin        (mm)
% Detector parameters
geo.nDetector = [512;512];					       % Number of pixels              (px)
geo.dDetector = [0.1;0.1]; 				           % Size of each pixel            (mm)
geo.sDetector = geo.nDetector.*geo.dDetector;      % Total size of the detector    (mm)
% RCL parameters

view = 8;
angle_ave = linspace(0,view-1,view)/view*2*pi;
theta = 90;                                         % 倾斜角度(theta)  重要参数
R1 = geo.DSO*tan((theta/2)/180*pi);
R2 = geo.DSD*tan((theta/2)/180*pi);
% Detector position
det_coor(:,1) = (R2)*cos(angle_ave);
det_coor(:,2) = (R2)*sin(angle_ave);
% Object position
obj_coor(:,1) = (R1)*cos(angle_ave);
obj_coor(:,2) = (R1)*sin(angle_ave);
% Image parameters
geo.nVoxel = [31;512;512];                         % number of voxels像素          (vx)
geo.sVoxel = geo.nVoxel.*[0.05;0.05;0.05];         % total size of the image       (mm)
geo.dVoxel = geo.sVoxel./geo.nVoxel;               % size of each voxel            (mm)
% Offsets ,geo.offOrigin =[0;0;0];表示图像中心位置的偏移量为零，即基坐标系的原点O没有偏移，位于物体的中心。
geo.offOrigin = zeros(3,view);                     % Offset of image from origin   (mm)              
geo.offOrigin(2:3,:) = obj_coor';

geo.offDetector = det_coor';                       % Offset of Detector            (mm)
% Detector rotation
y = [0,22.5, 45, 22.5, 0,-22.5,-45, -22.5]/180*pi;
z = [-45,-22.5, 0,22.5, 45, 22.5, 0, 22.5]/180*pi;
geo.rotDetector = zeros(3,view); 
geo.rotDetector(2:2,:) = y';
geo.rotDetector(3:3,:) = z';                       % Rotation of the detector, by 
                                                   % X,Y and Z axis respectively. (rad)

% Auxiliary 
geo.accuracy = 0.5;                                % Accuracy of FWD proj          (vx/sample)
geo.mode = 'cone';

% parallel or cone

%% angle
angles = linspace(0,0,view);                       %旋转轴的转动

%% Load data and generate projections 
load('cube3.mat')
cube=single(cube3);
% shepp_type='Modified Shepp-Logan';                    % (default).
% shepp=sheppLogan3D([512,512,512],shepp_type);         % Default are 128^3 and Modified shepp-logan
% shepp =shepp(206:236,:,:);
projections = Ax(cube,geo,angles);
plotImg([projections],'Dim',3);

%% Reconsturction 
imgOSSART1 = OS_SART(projections,geo,angles,20);
% % imgSIRT = SIRT(projections,geo,angles,20);
% % imgSARTTV=SART_TV(projections,geo,angles,20,'TViter',50,'TVlambda',50);
% figure(); imshow(squeeze(imgOSSART(17,:,:)),[0 1]);
rmse1 = RMSE(cube,imgOSSART1);
ssim1 =ssim(cube,imgOSSART1);
psnr1 = psnr(cube,imgOSSART1);
%% FFT分析
% 每个投影进行FFT2
width = 1024;
view = N;
fftproj=zeros(1,width,view);         % 矩阵维度：[列 view 行]
for i = 1:view
    tempproj =projections(:,:,i);
    test=squeeze(tempproj);
    ffttest=fft(test,width,2);
%     ffttest = fftshift(ffttest,2);
%     ffttest=fft2(test);
%     ffttest = fftshift(ffttest);
%     figure(1),area(ffttest(256,:))
    fftproj(:,:,i)=(ffttest(256,:));
end

Clcenter = zeros(width,view);
for i=1:view
    Clcenter(:,i)=fftproj(1,:,i);
end

thetaset = linspace(0,360-360/view,view);
SR = zeros(width+1);
% Accumuration of slice lines in frequency domain
SS = zeros(width+1);
% Zero array
S0 = zeros(width+1);
S1 = S0;
S1(1,:) = 1;
for iSlice = 1:length(thetaset)/2
    theta = thetaset(iSlice);
    Si = S0;
    Si(1,:) = [Clcenter(:,iSlice).' 0];
%     Si(20,:) = Si(1,:);
%     Si(1,:) = 0;
    SR = SR + imrotate(fftshift(Si),theta,'crop');
    figure(1),imshow(SR,[0 1]);   
end

fin = ifft2((SR));

%%
midCL=squeeze(fftproj(:,:,11));
for i=1:51
    temp=squeeze(fftproj(:,i,:));
    temp1=midCL+temp(:,300:300);
end
imgifft=ifft(fftshift(temp1,2),600,2);

testimg=cube(:,:,16);
testimg=squeeze(testimg);
ffttestimg=fft(testimg,600,2);
ffttest=fftshift(ffttestimg);
imgifft=ifft(fftshift(ffttest),600,2);
imtool(imgifft);




%% SAA + RCL   仅计算焦平面层图像
sum = zeros(geo.nDetector(1),geo.nDetector(2));
for i = 1:view
    sum = sum + projections(:,:,i);
end
[dx,dy] = gradient(sum);
add = abs(dx)+abs(dy);
add(add>0.15)=1;
add(add<0.15)=0;
figure(),imshow(add,[])

Om=im2bw(add);%二值化
ms=imfill(Om,'holes'); % mask结果

%% Result 
% plotImg([imgOSSART],'Dim',1); 
mode = cube;
Eval = zeros(3,31);
for i=1:31
    Eval(1,i)= PSNR(imgOSSART(i,:,:),mode(i,:,:));
    Eval(2,i)= RMSE(imgOSSART(i,:,:),mode(i,:,:));
    Eval(3,i)= ssim(imgOSSART(i,:,:),mode(i,:,:));
end
figure,plot(1:31, Eval(1,:),'m.-','LineWidth',2), hold on;   
figure,plot(1:31, Eval(2,:),'r.-','LineWidth',2), hold on; 
figure,plot(1:31, Eval(3,:),'k:','LineWidth',2), hold on;
```



