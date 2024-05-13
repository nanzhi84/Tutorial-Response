先附上指导PDF[Photometric Stereo Tutorial.pdf](https://www.yuque.com/attachments/yuque/0/2024/pdf/43925519/1715513195048-98f8aa6e-dcc6-445d-8fb5-8f8a79036dcb.pdf?_lake_card=%7B%22src%22%3A%22https%3A%2F%2Fwww.yuque.com%2Fattachments%2Fyuque%2F0%2F2024%2Fpdf%2F43925519%2F1715513195048-98f8aa6e-dcc6-445d-8fb5-8f8a79036dcb.pdf%22%2C%22name%22%3A%22Photometric%20Stereo%20Tutorial.pdf%22%2C%22size%22%3A1402051%2C%22ext%22%3A%22pdf%22%2C%22source%22%3A%22%22%2C%22status%22%3A%22done%22%2C%22download%22%3Atrue%2C%22taskId%22%3A%22u03a9a981-8912-43f2-adb3-e8c0a43fd32%22%2C%22taskType%22%3A%22upload%22%2C%22type%22%3A%22application%2Fpdf%22%2C%22__spacing%22%3A%22both%22%2C%22mode%22%3A%22title%22%2C%22id%22%3A%22u4ce46324%22%2C%22margin%22%3A%7B%22top%22%3Atrue%2C%22bottom%22%3Atrue%7D%2C%22card%22%3A%22file%22%7D)
<a name="e4568847"></a>
## 引言

三维重建是计算机视觉的研究热点，旨在通过相机拍摄的图像获取场景的三维形状。近年来，许多智能手机如苹果 iPhone、三星 Galaxy Note、华为 P30 Pro 等都集成了三维重建功能。

<a name="667bca08"></a>
## 基于几何方法的三维重建

多视角立体视觉（Multiview Stereo）是一种经典的基于几何的三维重建方法。通过从不同视角拍摄多张图像，利用视差信息计算场景的三维结构。下图展示了一个多视角立体视觉的重建结果 。<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715513417909-1d85d39a-7037-486f-961d-380f50c47132.png#averageHue=%23a5a3a0&clientId=ucf014f98-9a89-4&from=paste&height=421&id=ua99c6978&originHeight=632&originWidth=1210&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=323427&status=done&style=none&taskId=u4242108f-d1e3-4543-a15c-069af8ddd81&title=&width=806.6666666666666)
<a name="91fb873e"></a>
## 光度法三维重建

光度法三维重建（Photometric 3D Modeling）是另一类重要的三维重建技术。其输入是在不同光照条件下拍摄的多张图像，输出是场景的三维形状。下图展示了使用光度法重建美国总统奥巴马的三维模型 。<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715513450108-12cb5ee0-4d0b-42c1-aa3e-4310d0ae2912.png#averageHue=%234f3715&clientId=ucf014f98-9a89-4&from=paste&height=453&id=uf1b927c8&originHeight=679&originWidth=1231&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=709477&status=done&style=none&taskId=u8b7a4f38-97b6-4a34-80e9-350b9bebea1&title=&width=820.6666666666666)

<a name="5d307bc7"></a>
## 几何法 vs. 光度法

几何法与光度法是三维重建的两大类方法，其差异如下：

- 几何法以多视角图像为输入，擅长恢复完整的场景结构，但难以重建精细的表面细节。
- 光度法以多光照图像为输入，对同一视角下物体表面细节的重建更为擅长，但难以获得完整闭合的三维模型。

![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715513463960-21e31568-1d55-4321-b3b5-f62ceac8f8c3.png#averageHue=%23c0bcb4&clientId=ucf014f98-9a89-4&from=paste&height=406&id=u4e7b74c1&originHeight=609&originWidth=1188&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=126568&status=done&style=none&taskId=u76a35055-c03c-4487-8048-5b764361fad&title=&width=792)
<a name="81f5dd46"></a>
## 预备知识

<a name="d47fa201"></a>
### 表面法线

曲面上某点的法线 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bn%7D#card=math&code=%5Cmathbf%7Bn%7D&id=bWqMt) 是垂直于该点切平面的单位向量，即 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bn%7D%20%5Cin%20S%5E2%20%5Csubset%20%5Cmathbb%7BR%7D%5E3#card=math&code=%5Cmathbf%7Bn%7D%20%5Cin%20S%5E2%20%5Csubset%20%5Cmathbb%7BR%7D%5E3&id=ITDad)，![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5C%7C%5Cmathbf%7Bn%7D%5C%7C%20%3D%201#card=math&code=%5C%7C%5Cmathbf%7Bn%7D%5C%7C%20%3D%201&id=DINGw)。其中 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bn%7D%20%3D%20%5Bn_x%2C%20n_y%2C%20n_z%5D%5E%5Ctop#card=math&code=%5Cmathbf%7Bn%7D%20%3D%20%5Bn_x%2C%20n_y%2C%20n_z%5D%5E%5Ctop&id=ytjAX)。

曲面的法线分布通常可以用`伪彩色图`（normal map）来可视化，即 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bn%7D_s%20%3D%20%5Cfrac%7B%5Cmathbf%7Bn%7D%2B1%7D%7B2%7D#card=math&code=%5Cmathbf%7Bn%7D_s%20%3D%20%5Cfrac%7B%5Cmathbf%7Bn%7D%2B1%7D%7B2%7D&id=EBTez)。伪彩色编码：![](https://www.yuque.com/api/services/graph/generate_redirect/latex?x#card=math&code=x&id=Y08Gm) 方向（红色）、![](https://www.yuque.com/api/services/graph/generate_redirect/latex?y#card=math&code=y&id=svu8Y) 方向（绿色）、![](https://www.yuque.com/api/services/graph/generate_redirect/latex?z#card=math&code=z&id=BME8f) 方向（蓝色）。

![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715513487232-02d27ac0-2cd2-4718-b826-6c90b6a6688e.png#averageHue=%23f8f7f7&clientId=ucf014f98-9a89-4&from=paste&height=409&id=u4c9b4868&originHeight=613&originWidth=1152&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=176981&status=done&style=none&taskId=ub99315e3-17ae-454c-a9cf-adbca9c5fea&title=&width=768)

<a name="8ccc9a1d"></a>
### 平行光源

在现实世界中，很多光源可以看作平行光，例如无穷远处的点光源。平行光具有以下特点：

- 场景中所有点接收到的光照方向一致，用单位向量 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bl%7D%20%5Cin%20S%5E2%20%5Csubset%20%5Cmathbb%7BR%7D%5E3#card=math&code=%5Cmathbf%7Bl%7D%20%5Cin%20S%5E2%20%5Csubset%20%5Cmathbb%7BR%7D%5E3&id=ZHNDf) 表示，其中 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bl%7D%20%3D%20%5Bl_x%2C%20l_y%2C%20l_z%5D%5E%5Ctop#card=math&code=%5Cmathbf%7Bl%7D%20%3D%20%5Bl_x%2C%20l_y%2C%20l_z%5D%5E%5Ctop&id=xhHN3)。
- 假设环境中不存在其他光源（即拍摄在暗室中进行）。

![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715513520659-93d6f9c1-97d9-47cb-9a75-3dee2359d49c.png#averageHue=%23fbfbfa&clientId=ucf014f98-9a89-4&from=paste&height=389&id=u4a8f0b5b&originHeight=584&originWidth=1195&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=109194&status=done&style=none&taskId=u22688390-edf5-4097-ada5-20e49ea3154&title=&width=796.6666666666666)
<a name="e71c5f52"></a>
### 朗伯反射

朗伯反射描述了理想漫反射表面的反射特性：

- 反射光强度与视角无关，各个方向观察到的亮度一致。
- 反射光强正比于表面法线 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bn%7D#card=math&code=%5Cmathbf%7Bn%7D&id=g4kDT) 与光源方向 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bl%7D#card=math&code=%5Cmathbf%7Bl%7D&id=WGMme) 的点积，即 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bn%7D%5E%5Ctop%20%5Cmathbf%7Bl%7D%20%3D%20%5Ccos%20%5Ctheta#card=math&code=%5Cmathbf%7Bn%7D%5E%5Ctop%20%5Cmathbf%7Bl%7D%20%3D%20%5Ccos%20%5Ctheta&id=apKkw)。其中 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Ctheta#card=math&code=%5Ctheta&id=rgvMD) 是法线与光源方向的夹角。

数学表达：<br />对于一个入射光强度为![](https://cdn.nlark.com/yuque/__latex/df53f70d20a6a901dfd6da9f6986b470.svg#card=math&code=I&id=OM8UY)的光线，其入射角为![](https://cdn.nlark.com/yuque/__latex/8ce05d6340cd93268f5b3b6b9385bf2a.svg#card=math&code=%5Ctheta%20_i&id=t5iLo)，反射光在特定观察角度![](https://cdn.nlark.com/yuque/__latex/717820818e55e73061cbe08e51ec610e.svg#card=math&code=%5Ctheta%20_r&id=SOsSN)的亮度![](https://cdn.nlark.com/yuque/__latex/54f5fb1b07a88521e7b036e3bc7a5e33.svg#card=math&code=B&id=eEr1y)可以用以下公式表示:

![](https://cdn.nlark.com/yuque/__latex/a83e5018241654634b219b8aeb071fd0.svg#card=math&code=B%3D%5Crho%20%5Cfrac%7BIcos%5Ctheta%20_i%7D%7B%5Cpi%7D%20&id=MUiqZ)

其中，![](https://cdn.nlark.com/yuque/__latex/d7dccfe80f15d1d2e2f37d8d47a9378b.svg#card=math&code=%5Crho&id=TzcXp)是表面的反射率，![](https://cdn.nlark.com/yuque/__latex/8ce05d6340cd93268f5b3b6b9385bf2a.svg#card=math&code=%5Ctheta%20_i&id=NgyVW)是光线与表面法线之间的角度。<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715514187598-76c2a0d5-6a0e-4834-a2aa-42fb6eda6434.png#averageHue=%23f1f0ef&clientId=ucf014f98-9a89-4&from=paste&height=439&id=uf3bc125d&originHeight=659&originWidth=1196&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=347919&status=done&style=none&taskId=uf2c5278e-3c8a-47a8-a321-0d00a8068f7&title=&width=797.3333333333334)
<a name="d5f2945c"></a>
## 朗伯成像模型

在朗伯反射的假设下，成像过程可以描述为：

![](https://cdn.nlark.com/yuque/__latex/064a7a82585dc25b51331efb7b596fc6.svg#card=math&code=I%20%5Cpropto%20e%5Crho%20%5Cmathbf%7Bn%7D%5E%5Ctop%20%5Cmathbf%7Bl%7D%20%3D%20e%5Crho%20%5Cbegin%7Bbmatrix%7D%20n_x%20%26%20n_y%20%26%20n_z%20%5Cend%7Bbmatrix%7D%20%5Cbegin%7Bbmatrix%7D%20l_x%20%5C%5C%20l_y%20%5C%5C%20l_z%20%5Cend%7Bbmatrix%7D%20%3D%20e%5Crho%20cos%5Ctheta_i&id=MCfou)

其中：

- ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?I%20%5Cin%20%5Cmathbb%7BR%7D_%2B#card=math&code=I%20%5Cin%20%5Cmathbb%7BR%7D_%2B&id=ESeP2)：观测到的像素灰度值
- ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?e%20%5Cin%20%5Cmathbb%7BR%7D_%2B#card=math&code=e%20%5Cin%20%5Cmathbb%7BR%7D_%2B&id=nzmzo)：光源辐射强度
- ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Crho%20%5Cin%20%5Cmathbb%7BR%7D_%2B#card=math&code=%5Crho%20%5Cin%20%5Cmathbb%7BR%7D_%2B&id=mf6nb)：表面材质的朗伯反射率
- ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bn%7D%20%3D%20%5Bn_x%2C%20n_y%2C%20n_z%5D%5E%5Ctop#card=math&code=%5Cmathbf%7Bn%7D%20%3D%20%5Bn_x%2C%20n_y%2C%20n_z%5D%5E%5Ctop&id=s5L3G)：表面法线方向（单位向量）
- ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bl%7D%20%3D%20%5Bl_x%2C%20l_y%2C%20l_z%5D%5E%5Ctop#card=math&code=%5Cmathbf%7Bl%7D%20%3D%20%5Bl_x%2C%20l_y%2C%20l_z%5D%5E%5Ctop&id=TiSR8)：光源方向（单位向量）
- ![](https://cdn.nlark.com/yuque/__latex/8ce05d6340cd93268f5b3b6b9385bf2a.svg#card=math&code=%5Ctheta%20_i&id=F2RuB)：光线与表面法线之间的角度。

为简化起见，我们做两点假设：

1. 光源强度 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?e%3D1#card=math&code=e%3D1&id=xu5Ae)
2. 反射率 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Crho#card=math&code=%5Crho&id=egePV) 的影响可以忽略

于是成像模型简化为：

![](https://www.yuque.com/api/services/graph/generate_redirect/latex?I%20%3D%20%5Cmathbf%7Bn%7D%5E%5Ctop%20%5Cmathbf%7Bl%7D%20%3D%20%5Cmathbf%7Bn%7D%20%5Ccdot%20%5Cmathbf%7Bl%7D%0A#card=math&code=I%20%3D%20%5Cmathbf%7Bn%7D%5E%5Ctop%20%5Cmathbf%7Bl%7D%20%3D%20%5Cmathbf%7Bn%7D%20%5Ccdot%20%5Cmathbf%7Bl%7D%0A&id=wm01y)

即像素亮度正比于表面法线和光源方向的内积。<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715514202038-4212c319-8a7c-4279-862e-afb39f162493.png#averageHue=%23f6f4f2&clientId=ucf014f98-9a89-4&from=paste&height=431&id=u2b4542ce&originHeight=647&originWidth=1183&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=167037&status=done&style=none&taskId=u3f72c955-b93d-4c79-be9b-7c5d2acba26&title=&width=788.6666666666666)
<a name="fc2ad2c5"></a>
## 光度立体视觉

光度立体视觉（Photometric Stereo）的目标是：在物体表面反射率已知（通常假设为朗伯反射）的前提下，根据多张不同光照条件下（至少3张）的图像，估计每个像素位置的表面法线。<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715515356410-2ef2603e-b0cc-4703-982b-5eb33e48ca2a.png#averageHue=%23ead0c3&clientId=ucf014f98-9a89-4&from=paste&height=417&id=u6ca64893&originHeight=626&originWidth=1183&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=267622&status=done&style=none&taskId=u23b8842c-d352-4278-8127-669cbb63396&title=&width=788.6666666666666)

基本思路如下：

考虑单个像素点，其对应的表面法线为 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bn%7D#card=math&code=%5Cmathbf%7Bn%7D&id=PudUB)。假设总共拍摄了 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?f#card=math&code=f&id=oMCre) 张图像，每张图像对应不同的平行光源 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bl%7D_1%2C%20%5Cmathbf%7Bl%7D_2%2C%20%5Ccdots%2C%20%5Cmathbf%7Bl%7D_f#card=math&code=%5Cmathbf%7Bl%7D_1%2C%20%5Cmathbf%7Bl%7D_2%2C%20%5Ccdots%2C%20%5Cmathbf%7Bl%7D_f&id=cUrJ8)。则该像素在各个图像中的亮度分别为：

![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cbegin%7Baligned%7D%0AI_1%20%26%3D%20%5Cmathbf%7Bl%7D_1%5E%5Ctop%20%5Cmathbf%7Bn%7D%20%5C%5C%0AI_2%20%26%3D%20%5Cmathbf%7Bl%7D_2%5E%5Ctop%20%5Cmathbf%7Bn%7D%20%5C%5C%0A%26%20%5Cvdots%20%20%5C%5C%0AI_f%20%26%3D%20%5Cmathbf%7Bl%7D_f%5E%5Ctop%20%5Cmathbf%7Bn%7D%0A%5Cend%7Baligned%7D%0A#card=math&code=%5Cbegin%7Baligned%7D%0AI_1%20%26%3D%20%5Cmathbf%7Bl%7D_1%5E%5Ctop%20%5Cmathbf%7Bn%7D%20%5C%5C%0AI_2%20%26%3D%20%5Cmathbf%7Bl%7D_2%5E%5Ctop%20%5Cmathbf%7Bn%7D%20%5C%5C%0A%26%20%5Cvdots%20%20%5C%5C%0AI_f%20%26%3D%20%5Cmathbf%7Bl%7D_f%5E%5Ctop%20%5Cmathbf%7Bn%7D%0A%5Cend%7Baligned%7D%0A&id=UMZzb)

写成矩阵形式有：

![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cunderbrace%7B%5Cbegin%7Bbmatrix%7D%0AI_1%20%5C%5C%20I_2%20%5C%5C%20%5Cvdots%20%5C%5C%20I_f%0A%5Cend%7Bbmatrix%7D%7D_%7B%5Cmathbf%7BI%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bf%7D%7D%20%20%3D%20%0A%5Cunderbrace%7B%5Cbegin%7Bbmatrix%7D%0Al_%7B1x%7D%20%26%20l_%7B1y%7D%20%26%20l_%7B1z%7D%20%5C%5C%0Al_%7B2x%7D%20%26%20l_%7B2y%7D%20%26%20l_%7B2z%7D%20%5C%5C%0A%5Cvdots%20%26%20%5Cvdots%20%26%20%5Cvdots%20%5C%5C%0Al_%7Bfx%7D%20%26%20l_%7Bfy%7D%20%26%20l_%7Bfz%7D%0A%5Cend%7Bbmatrix%7D%7D_%7B%5Cmathbf%7BL%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bf%20%5Ctimes%203%7D%7D%20%5Cunderbrace%7B%5Cbegin%7Bbmatrix%7D%0An_x%20%5C%5C%20n_y%20%5C%5C%20n_z%0A%5Cend%7Bbmatrix%7D%7D_%7B%5Cmathbf%7Bn%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E3%7D%0A#card=math&code=%5Cunderbrace%7B%5Cbegin%7Bbmatrix%7D%0AI_1%20%5C%5C%20I_2%20%5C%5C%20%5Cvdots%20%5C%5C%20I_f%0A%5Cend%7Bbmatrix%7D%7D_%7B%5Cmathbf%7BI%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bf%7D%7D%20%20%3D%20%0A%5Cunderbrace%7B%5Cbegin%7Bbmatrix%7D%0Al_%7B1x%7D%20%26%20l_%7B1y%7D%20%26%20l_%7B1z%7D%20%5C%5C%0Al_%7B2x%7D%20%26%20l_%7B2y%7D%20%26%20l_%7B2z%7D%20%5C%5C%0A%5Cvdots%20%26%20%5Cvdots%20%26%20%5Cvdots%20%5C%5C%0Al_%7Bfx%7D%20%26%20l_%7Bfy%7D%20%26%20l_%7Bfz%7D%0A%5Cend%7Bbmatrix%7D%7D_%7B%5Cmathbf%7BL%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bf%20%5Ctimes%203%7D%7D%20%5Cunderbrace%7B%5Cbegin%7Bbmatrix%7D%0An_x%20%5C%5C%20n_y%20%5C%5C%20n_z%0A%5Cend%7Bbmatrix%7D%7D_%7B%5Cmathbf%7Bn%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E3%7D%0A&id=OOgBO)

即 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BI%7D%20%3D%20%5Cmathbf%7BL%7D%20%5Cmathbf%7Bn%7D#card=math&code=%5Cmathbf%7BI%7D%20%3D%20%5Cmathbf%7BL%7D%20%5Cmathbf%7Bn%7D&id=WjPNQ)。![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BL%7D#card=math&code=%5Cmathbf%7BL%7D&id=uPCE1) 称为光源矩阵，每一行对应一个光源方向。![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BI%7D#card=math&code=%5Cmathbf%7BI%7D&id=y1PaV) 称为亮度向量。<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715515384579-81bc8c10-1b34-4f2e-9a68-1cf4bbb674f4.png#averageHue=%23eae5dd&clientId=ucf014f98-9a89-4&from=paste&height=407&id=ua8a4caa0&originHeight=610&originWidth=1174&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=223474&status=done&style=none&taskId=u632c1857-8156-4e40-a115-b0f8680ddea&title=&width=782.6666666666666)

将成像模型扩展到整个图像，每个像素都有一个亮度向量和法向量。设图像尺寸为 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?p#card=math&code=p&id=LNJTN) 像素，![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BI%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bp%20%5Ctimes%20f%7D#card=math&code=%5Cmathbf%7BI%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bp%20%5Ctimes%20f%7D&id=Gl1NU), ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BN%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bp%20%5Ctimes%203%7D#card=math&code=%5Cmathbf%7BN%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bp%20%5Ctimes%203%7D&id=Wxvug)，则成像过程可以统一表示为：

![](https://cdn.nlark.com/yuque/__latex/8eae203f603c50d06795b061d0eeb9b1.svg#card=math&code=%5Cmathbf%7BI%7D_%7Bf%20%5Ctimes%20p%7D%20%3D%20%5Cmathbf%7BL%7D_%7Bf%20%5Ctimes%203%7D%5Cmathbf%7BN%7D_%7B3%20%5Ctimes%20p%7D%20%0A&id=VWFce)<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715515447904-00a892bd-4e51-49b3-8ccf-50d8037f7fca.png#averageHue=%23ece7e1&clientId=ucf014f98-9a89-4&from=paste&height=430&id=ud12b3e88&originHeight=645&originWidth=1221&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=207733&status=done&style=none&taskId=ucb472c87-9df5-4b83-9489-dd74e6887a6&title=&width=814)<br />其中 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BN%7D#card=math&code=%5Cmathbf%7BN%7D&id=KVL4V) 是整幅图像的法向量场。可以看到，光度立体恢复表面法线的过程，本质上是一个线性系统 ![](https://cdn.nlark.com/yuque/__latex/4ab1894a220338a5f7963f3913b3c049.svg#card=math&code=%5Cmathbf%7BI%7D%20%3D%20%5Cmathbf%7BL%7D%20%5Cmathbf%7BN%7D&id=fqyC0) 的求解过程。若 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BL%7D#card=math&code=%5Cmathbf%7BL%7D&id=E7MVv) 已知，则可直接通过最小二乘法求解：

![](https://cdn.nlark.com/yuque/__latex/c7770b0b78915a0d4817ac94b6ad8951.svg#card=math&code=%5Cmathbf%7BN%7D%20%3D%20%5Cmathbf%7BL%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BI%7D%20%0A&id=oIcyg)

其中 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BL%7D%5E%7B%5Cdagger%7D#card=math&code=%5Cmathbf%7BL%7D%5E%7B%5Cdagger%7D&id=X4zit) 表示矩阵 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BL%7D#card=math&code=%5Cmathbf%7BL%7D&id=j81eb) 的伪逆。要保证 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BL%7D#card=math&code=%5Cmathbf%7BL%7D&id=wsofK) 列满秩，需要至少3个线性无关的光照方向（即 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?f%20%5Cgeq%203#card=math&code=f%20%5Cgeq%203&id=mKl6L)）。<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715515916374-f4989f15-2bd5-43cd-b382-7f6816ebd30f.png#averageHue=%23ece6df&clientId=ucf014f98-9a89-4&from=paste&height=431&id=uce783d20&originHeight=647&originWidth=1197&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=194168&status=done&style=none&taskId=u6c91f7ca-b573-493b-abbc-a4893580279&title=&width=798)

<a name="aCrgY"></a>
### 伪逆的求解过程：
考虑线性系统 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20%3D%20%5Cmathbf%7Bb%7D#card=math&code=%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20%3D%20%5Cmathbf%7Bb%7D&id=awOjq)，其中 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bm%20%5Ctimes%20n%7D#card=math&code=%5Cmathbf%7BA%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bm%20%5Ctimes%20n%7D&id=UWvCC), ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bx%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bn%7D#card=math&code=%5Cmathbf%7Bx%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bn%7D&id=S239E), ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bb%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bm%7D#card=math&code=%5Cmathbf%7Bb%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bm%7D&id=DfU0p)。我们希望找到 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bx%7D#card=math&code=%5Cmathbf%7Bx%7D&id=pPhdx) 的最小二乘解，即求解以下优化问题：

![](https://cdn.nlark.com/yuque/__latex/660dbb688f0348523964db6a0b3cc18e.svg#card=math&code=%5Cmin_%7B%5Cmathbf%7Bx%7D%7D%20%5C%7C%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20-%20%5Cmathbf%7Bb%7D%5C%7C_2%5E2%0A&id=E8Zkg)

其中 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5C%7C%5Ccdot%5C%7C_2#card=math&code=%5C%7C%5Ccdot%5C%7C_2&id=EMGtZ) 表示欧几里得范数（L2范数）。

将目标函数展开：

![](https://cdn.nlark.com/yuque/__latex/4de85672e8ee4954ac0f3679cf9fa309.svg#card=math&code=%5C%7C%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20-%20%5Cmathbf%7Bb%7D%5C%7C_2%5E2%20%3D%20%28%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20-%20%5Cmathbf%7Bb%7D%29%5E%5Ctop%28%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20-%20%5Cmathbf%7Bb%7D%29%20%3D%20%5Cmathbf%7Bx%7D%5E%5Ctop%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20-%202%5Cmathbf%7Bb%7D%5E%5Ctop%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20%2B%20%5Cmathbf%7Bb%7D%5E%5Ctop%5Cmathbf%7Bb%7D%0A&id=n3Muy)

对 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bx%7D#card=math&code=%5Cmathbf%7Bx%7D&id=rQPQu) 求导，并令导数为0：

![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cfrac%7B%5Cpartial%7D%7B%5Cpartial%20%5Cmathbf%7Bx%7D%7D%20%5C%7C%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20-%20%5Cmathbf%7Bb%7D%5C%7C_2%5E2%20%3D%202%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20-%202%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7Bb%7D%20%3D%200%0A#card=math&code=%5Cfrac%7B%5Cpartial%7D%7B%5Cpartial%20%5Cmathbf%7Bx%7D%7D%20%5C%7C%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20-%20%5Cmathbf%7Bb%7D%5C%7C_2%5E2%20%3D%202%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20-%202%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7Bb%7D%20%3D%200%0A&id=DAXN8)

整理得到：

![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20%3D%20%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7Bb%7D%0A#card=math&code=%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D%5Cmathbf%7Bx%7D%20%3D%20%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7Bb%7D%0A&id=XUkGK)

这被称为`法方程`（normal equation）。

当 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D&id=uZfKv) 列满秩时，![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D&id=p3CIW) 可逆，此时 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bx%7D#card=math&code=%5Cmathbf%7Bx%7D&id=gW0lj) 的解为：

![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bx%7D%20%3D%20(%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D)%5E%7B-1%7D%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7Bb%7D%0A#card=math&code=%5Cmathbf%7Bx%7D%20%3D%20%28%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D%29%5E%7B-1%7D%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7Bb%7D%0A&id=jTxnX)

令 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%3D%20(%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D)%5E%7B-1%7D%5Cmathbf%7BA%7D%5E%5Ctop#card=math&code=%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%3D%20%28%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D%29%5E%7B-1%7D%5Cmathbf%7BA%7D%5E%5Ctop&id=lhf8F)，则 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7Bx%7D%20%3D%20%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%5Cmathbf%7Bb%7D#card=math&code=%5Cmathbf%7Bx%7D%20%3D%20%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%5Cmathbf%7Bb%7D&id=PfAvR)。这里的 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D#card=math&code=%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D&id=VAbKq) 就是 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D&id=QNrnH) 的伪逆。

实际上，伪逆的定义更加广泛，适用于任意矩阵（包括列不满秩的情况）。对于矩阵 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bm%20%5Ctimes%20n%7D#card=math&code=%5Cmathbf%7BA%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bm%20%5Ctimes%20n%7D&id=Fj85g)，其伪逆 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bn%20%5Ctimes%20m%7D#card=math&code=%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bn%20%5Ctimes%20m%7D&id=QDQHn) 满足以下性质：

1. ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BA%7D%20%3D%20%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BA%7D%20%3D%20%5Cmathbf%7BA%7D&id=Ezo6Y)
2. ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BA%7D%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%3D%20%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D#card=math&code=%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BA%7D%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%3D%20%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D&id=HS9wM)
3. ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?(%5Cmathbf%7BA%7D%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D)%5E%5Ctop%20%3D%20%5Cmathbf%7BA%7D%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D#card=math&code=%28%5Cmathbf%7BA%7D%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%29%5E%5Ctop%20%3D%20%5Cmathbf%7BA%7D%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D&id=jFIbr)
4. ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?(%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BA%7D)%5E%5Ctop%20%3D%20%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BA%7D#card=math&code=%28%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BA%7D%29%5E%5Ctop%20%3D%20%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BA%7D&id=UwELI)

可以证明，对于任意矩阵 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D&id=iP0EX)，其伪逆 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D#card=math&code=%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D&id=ipnMI) 存在且唯一。

当 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D&id=iiUVu) 列满秩时，![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%3D%20(%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D)%5E%7B-1%7D%5Cmathbf%7BA%7D%5E%5Ctop#card=math&code=%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%3D%20%28%5Cmathbf%7BA%7D%5E%5Ctop%5Cmathbf%7BA%7D%29%5E%7B-1%7D%5Cmathbf%7BA%7D%5E%5Ctop&id=cdAUZ)；当 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D&id=c1JKU) 行满秩时，![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%3D%20%5Cmathbf%7BA%7D%5E%5Ctop(%5Cmathbf%7BA%7D%5Cmathbf%7BA%7D%5E%5Ctop)%5E%7B-1%7D#card=math&code=%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%3D%20%5Cmathbf%7BA%7D%5E%5Ctop%28%5Cmathbf%7BA%7D%5Cmathbf%7BA%7D%5E%5Ctop%29%5E%7B-1%7D&id=qxBNH)。

对于一般情况，![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D&id=wKDS5) 的伪逆可以通过`奇异值分解`（SVD）来计算。设 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D&id=sRtBt) 的SVD分解为 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%20%3D%20%5Cmathbf%7BU%7D%5Cmathbf%7B%5CSigma%7D%5Cmathbf%7BV%7D%5E%5Ctop#card=math&code=%5Cmathbf%7BA%7D%20%3D%20%5Cmathbf%7BU%7D%5Cmathbf%7B%5CSigma%7D%5Cmathbf%7BV%7D%5E%5Ctop&id=AQRHj)，其中 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BU%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bm%20%5Ctimes%20m%7D#card=math&code=%5Cmathbf%7BU%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bm%20%5Ctimes%20m%7D&id=akDSO), ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BV%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bn%20%5Ctimes%20n%7D#card=math&code=%5Cmathbf%7BV%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bn%20%5Ctimes%20n%7D&id=Be4yx) 都是正交矩阵，![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7B%5CSigma%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bm%20%5Ctimes%20n%7D#card=math&code=%5Cmathbf%7B%5CSigma%7D%20%5Cin%20%5Cmathbb%7BR%7D%5E%7Bm%20%5Ctimes%20n%7D&id=N3EKs) 是一个对角矩阵，对角线上的元素为 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D&id=LpdyH) 的奇异值。那么 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D#card=math&code=%5Cmathbf%7BA%7D&id=uB0er) 的伪逆为：

![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%3D%20%5Cmathbf%7BV%7D%5Cmathbf%7B%5CSigma%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BU%7D%5E%5Ctop%0A#card=math&code=%5Cmathbf%7BA%7D%5E%7B%5Cdagger%7D%20%3D%20%5Cmathbf%7BV%7D%5Cmathbf%7B%5CSigma%7D%5E%7B%5Cdagger%7D%5Cmathbf%7BU%7D%5E%5Ctop%0A&id=XcxAX)

其中 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7B%5CSigma%7D%5E%7B%5Cdagger%7D#card=math&code=%5Cmathbf%7B%5CSigma%7D%5E%7B%5Cdagger%7D&id=XoqOj) 是由 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7B%5CSigma%7D#card=math&code=%5Cmathbf%7B%5CSigma%7D&id=FOqUw) 转置并将非零奇异值取倒数得到的。

在数值计算中，通常利用SVD方法计算伪逆，而不是直接求解法方程，因为SVD方法更加稳定和通用。很多数值计算库如numpy、MATLAB都提供了现成的函数用于计算矩阵的伪逆。
<a name="b3f2a456"></a>
## 光度立体的实现流程

以下是光度立体视觉的一个典型实现流程：

1. 读取多光照图像：读取拍摄的多张图像（至少3张），并将其堆叠为亮度矩阵 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BI%7D#card=math&code=%5Cmathbf%7BI%7D&id=vj7zm)。
2. 标定光源方向：测量每一张图像对应的光源方向，并存储为光源矩阵 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BL%7D#card=math&code=%5Cmathbf%7BL%7D&id=w5UqQ)。
3. 法线估计：利用最小二乘法 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BN%7D%20%3D%20%5Cmathbf%7BI%7D%20%5Cmathbf%7BL%7D%5E%7B%5Cdagger%7D#card=math&code=%5Cmathbf%7BN%7D%20%3D%20%5Cmathbf%7BI%7D%20%5Cmathbf%7BL%7D%5E%7B%5Cdagger%7D&id=nIuyN) 求解每个像素的法线。
4. 可视化结果：将估计的表面法线场 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BN%7D#card=math&code=%5Cmathbf%7BN%7D&id=EE3RS) 可视化，得到物体的法线贴图。

下面是光度立体方法的一个Python实现：

```python
import numpy as np
import cv2
import glob
from sklearn.preprocessing import normalize

# 1. 读取图像
M = []
for fname in sorted(glob.glob('data/cat/*.png')):
    im = cv2.imread(fname, 0)  # 以灰度模式读取图像
    if M == []:
        height, width = im.shape
        M = im.reshape((-1, 1))  # 将图像拉伸为一列
    else:
        M = np.append(M, im.reshape((-1,1)), axis=1)  # 多张图像按列拼接

# 2. 读取光源方向
L = np.loadtxt('data/cat/lights.txt').T  # 光源方向按行存储，需要转置

# 3. 光度立体法向量估计（最小二乘解）
N = np.linalg.pinv(L) @ M  # 最小二乘解 N = L^† M
N = normalize(N, axis=0)  # 将法向量归一化为单位向量

# 4. 可视化
N = np.reshape(N.T, (height, width, 3))  # 将法向量矩阵重整为图像的形状
N = cv2.cvtColor(N, cv2.COLOR_BGR2RGB)  # 交换R和B通道，适应伪彩色可视化的习惯
# N[:,:,0],N[:,:,2]=N[:,:,2],N[:,:,0].copy() 另一种方法
N = (N + 1.0) / 2.0  # 将法向量的范围从[-1, 1]映射到[0, 1]
cv2.imshow('normal map', N)
cv2.waitKey()
cv2.destroyAllWindows()
```

以上代码的核心部分是第3步，使用numpy的`pinv()`函数直接求解伪逆，得到法向量矩阵的最小二乘解。然后对法向量进行归一化处理。

第4步将求解得到的法向量矩阵可视化。为了便于观察，将法向量的取值范围从![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5B-1%2C1%5D#card=math&code=%5B-1%2C1%5D&id=ReyFE)映射到![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5B0%2C1%5D#card=math&code=%5B0%2C1%5D&id=tRoW6)，并将BGR通道顺序调整为RGB，以适应常见的伪彩色编码习惯。最后使用`cv2.imshow()`显示法线贴图。
<a name="6d382b46"></a>
## 经典光度立体存在的问题

经典的光度立体方法存在以下问题，这些也是目前光度立体的研究热点：

1. 非标定光源：经典方法假设光源方向已知，但在很多场景下光照方向难以精确标定。因此需要研究非标定或半标定光照下的光度立体方法。
2. 鲁棒性：经典方法对阴影、高光、噪声等非朗伯因素敏感，重建质量易受影响。需要设计鲁棒的优化模型，如稀疏回归等。
3. 复杂反射模型：大部分真实材质表面不满足理想的朗伯反射，往往具有各向异性、次表面散射等复杂反射特性。考虑更真实的反射模型十分必要。
4. 自然光照：传统方法依赖受控实验环境，无法适应日常场景的复杂光照条件。在自然光照下进行三维重建面临巨大挑战。
5. 任意材质+任意光照：在未知光照、未知材质的情况下进行三维重建是极富挑战的问题。需要研究无监督、自监督的学习方法。

![image.png](https://cdn.nlark.com/yuque/0/2024/png/43925519/1715516176219-fd3cd52f-dc9f-410a-a29b-c9f2e43ab65a.png#averageHue=%23f2f0ed&clientId=ucf014f98-9a89-4&from=paste&height=421&id=uc2838904&originHeight=632&originWidth=1161&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=238429&status=done&style=none&taskId=uf2e6ab09-e13a-4d6e-9850-af4882b31bd&title=&width=774)
<a name="5db9fd7c"></a>
## 小结

- 几何法和光度法是三维重建的两类经典方法。光度法以不同光照下的图像为输入，擅长重建表面细节。
- 表面法线、平行光、朗伯反射是理解光度法的三个关键概念。其中朗伯反射描述了漫反射表面的成像过程。
- 在朗伯反射模型下，相机观测到的像素亮度正比于表面法线和光源方向的内积。
- 光度立体视觉通过求解线性系统 ![](https://www.yuque.com/api/services/graph/generate_redirect/latex?%5Cmathbf%7BI%7D%20%3D%20%5Cmathbf%7BN%7D%20%5Cmathbf%7BL%7D%5E%5Ctop#card=math&code=%5Cmathbf%7BI%7D%20%3D%20%5Cmathbf%7BN%7D%20%5Cmathbf%7BL%7D%5E%5Ctop&id=Yuz7N)，估计每个像素的法向量，从而得到整幅图像的法线场。
- 光度立体的一般流程包括：读取图像、标定光源、估计法线、可视化结果。
- 经典光度立体方法的局限性，也是目前的研究热点，包括：非标定光源、鲁棒性、复杂反射、自然光照、任意材质与光照等。

