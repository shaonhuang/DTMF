# DTMF

#### 摘 要

本文是初步matlab程序设计的验收进度报告。
为了实现了：
（1）1-9键双音多频信号的产生，同时设计GUI界面来输入响应，在按下键盘的同时播放输出的信号。

（2）根据具体按键，生成的时域和频域的双音多频信号，同时展现在GUI界面之中。
（3）根据时域和频域的双音多频信号曲线解码1-9

（4）设计出DTMF发生和接收界面



关键词：DTMF GUI MATLAB 双音多频



###### GitHub Markdown不支持LaTeX，其中公式部分需要Markdown LaTeX相关插件支持
#### 设计目标
（1）1-9键双音多频信号的产生，同时设计GUI界面来输入响应，在按下键盘的同时播放输出的信号。

（2）根据具体按键，生成的时域和频域的双音多频信号，同时展现在GUI界面之中。
（3）根据时域和频域的双音多频信号曲线解码1-9

（4）设计出DTMF发生和接收界面
#### 设计思路和设计步骤
双音多频（DTMF）信号的发生
#### 任务要求
学习并实现DTMF信号的产生，并绘制时域和频域的图线。


#### 原理简述

##### DTMF简介
双音多频 DTMF（Dual Tone Multi Frequency），双音多频，由高频群和低频群组成，高低频群各包含4个频率。一个高频信号和一个低频信号叠加组成一个组合信号，代表一个数字。DTMF信号有16个编码。利用DTMF信令可选择呼叫相应的对讲机。



|       | 高频区Hz | 1209 | 1336 | 1477 | 1633 |
|:-----:|:-----:|:----:|:----:|:----:|:----:|
| 低频区Hz | 697   | 1    | 2    | 3    | A    |
|       | 770   | 4    | 5    | 6    | B    |
|       | 852   | 7    | 8    | 9    | C    |
|       | 941   | *    | 0    | #    | D    |


#### DTMF的技术指标
对于连续信号的数字采样，在电信应用中的采样频率普遍采用8khz的大小，根据奈奎斯特取样定理，因为8khz的一半是4khz，我们所用到的最高频率是1633hz，小于4khz，完全足够我们完成对信号的频域分析。[1]
DTMF信号在传输时，每秒传输10个号码，或者每个号码100ms。每个号码传送过程中，信号存在时间至少45ms，且不多于55ms，50ms的其余时间是静音。 [1]
在每个频率点上允许有不超过±1.5%的频率误差。任何超过给定频率±3.5%的信号，均被认为是无效的，拒绝接收。 [1]
实现步骤
时间连续的DTMF信号为

<img src="svgs/ad2ab02b78d67b44a3ec9ef8164839da.svg?invert_in_darkmode" align=middle width=228.68893905pt height=24.657534pt/>

对信号进行取样就得到   

<img src="svgs/d9bf5c2851a24b1a758904c0e6597a7d.svg?invert_in_darkmode" align=middle width=259.63559325pt height=37.8085059pt/>


f_r为行的频率，f_c为列的频率

根据电信应用普遍参数设计为采样频率8khz，采样时长45-55 ms之间，可以获得最大范围点为 

<img src="svgs/fe69026b9d891c98889f50d70c7222b8.svg?invert_in_darkmode" align=middle width=314.17331715pt height=27.7756545pt/> 

一共是440个点


#### 时域数字信号生成的实现步骤：
（1）生成时域自变量x，x = [0:1/f : T];（f是采样频率,T是采样时长）
（2）生成时域值y , sin(2 * pi * x * rowF(i))+sin(2 * pi * x * columnF(j))（i=1..4,j=1..4）（rowF和columnF分别是行列的频率值）
#### 时域信号转换成频谱实现步骤：
（1）用fft函数对数字信号的y向量做快速傅里叶变换，再用fftshift对得到的结果进行调整，得到0频率分量在中央的函数图像fft_y。

<img src="svgs/5dbce0a91a02acaac591f0ed023e367c.svg?invert_in_darkmode" align=middle width=230.9076561pt height=24.657534pt/>;

（2）计算fft_y对应的频率下标，Len代表取样的点数。
设N为取样点数，根据第n点对应频率
<img src="svgs/2408c50e1a2d085ac6bae040e05bedcd.svg?invert_in_darkmode" align=middle width=76.40556pt height=30.648288pt/>。
得<img src="svgs/39e75066cb430c0f2bbb93cb38a3e84e.svg?invert_in_darkmode" align=middle width=240.6337989pt height=24.657534pt/>;
#### 双音多频（DTMF）信号的单音解码FFT算法
##### 任务要求
由低频和高频时域信号通过FFT运算得到频域谱线，通过谱线中对应的信号进行单音解码，得到解码后的信号频率。推算出输入数字。
##### 原理简述
对我们生成的信号序列进行快速傅里叶变换，由原来的时域波形转化为频域谱线，原信号对应的频率会在频谱上分离出来。通过分离的谱线呈现出的高峰来确定这个信号所含的频率分量。
我的算法在对测算频率高峰的时候，通过快速傅里叶变换得到的频率会与定义的标准值有一定误差，通过观察发现误差都基本在15以内，所以我设置一个算法过滤后，得到FFT变换后的低频和高频另个分量。
我们对数字信号fft得到的结果与FFT的设置的点数有关，不具有普适性，对幅度大小进行归一化，根据相关知识，对FFT的过程进行改造得到
<img src="svgs/a1e4842f9797e977e33bd7a683b95f6b.svg?invert_in_darkmode" align=middle width=363.41132685pt height=24.657534pt/>为采样长度

对于不同的采样点数，幅值的大小也可以保持一致，为代码复用打下基础。


#### 实现步骤
在获得时域与频域的数据后，有两个目的，一个是实现频点的检测，找出频域中高能频率分量，一个为低频和一个高频的频率；另一个是根据频率的获取得到他们与标准频率的误差和在满足最小误差后判定为匹配成功输出结果。

获取高能频率的具体步骤：
（1）在这里我们发现我们只需要取最大值幅度（y坐标代表幅度），对应的频率（x坐标代表频率）就是我们FFT变换后得到的低频和高频。
由对称关系，我们得到其中一半的频率，同时取最大幅度的频率index1和第二大幅度的频率index2，判断abs(index1)与abs(index2)的大小，使得返回值低频在前，高频在后。

代码如下：

        function [fLen,freqs] = getTargetFreqs(~,y)
                    [~,index1]=max(y(1:length(y)/2));
                    y(index1)=0;
                    [~,index2]=max(y(1:length(y)/2));
                    x_tmp=-3999:20:4000;
                    if abs(x_tmp(index1))>abs(x_tmp(index2))
                        tmp=x_tmp(index1);
                        x_tmp(index1)=x_tmp(index2);
                        x_tmp(index2)=tmp;
                    end
                    freqs=[abs(x_tmp(index1)),abs(x_tmp(index2))];
                    fLen = length(freqs);  
                end
        
        
获取频率所对应按键的具体步骤：
	由上一个函数得到2个值，如果过程出错则，输出没有找到isFind=0
	在getFreqs中存储着1-16个数的一对标准频率。
	低频与在标准频率误差小与等于15和高频与标准频率误差小于等于15的同时，向target变量输出找到的数字。

    function [isFind,target,error1,error2] = findTargetBtn(app,fLen,freqs)
                isFind = 0;
                target = '';
                error1 = inf; 
                error2 = inf;
                num=1;
                if fLen==2                
                    for i=1:4
                        for j=1:4
                            [low,high]=getFreqs(app,num);
                            
                            if abs(freqs(1)-low)<=15 && abs(freqs(2)-high)<=15
                                if num<10
                                    target=num2str(num);
                                else
                                    switch num
                                    case 10
                                        target=  '0';
                                    case 11
                                        target=  '*';
                                    case 12
                                        target=  '#';
                                    case 13
                                        target=  'A';
                                    case 14
                                        target = 'B';
                                    case 15
                                        target = 'C';
                                    case 16
                                        target = 'D';
                                    otherwise 
                                        isFind=0;
                                    end
                                end
                            else
                            isFind=0;
                            end
                            num=num+1;
                        end
                    end
                    error1=abs(freqs(1)-low)/low;
                    error2=abs(freqs(2)-high)/high;
                end
            
            end
#### 双音多频（DTMF）信号的单音解码Goertzel算法
##### 任务要求
利用傅里叶算法的简化算法——Goertzel算法，对单音解码算法进行设计。


#### 原理简述
##### Goertzel算法原理
对于直接计算离散傅里叶变换DFT，计算公式是

<img src="svgs/acdfce49da42ccdc018b4886bff27007.svg?invert_in_darkmode" align=middle width=313.2860016pt height=32.2560084pt/>

这个式子在输入整个序列后才可以进行运算，而一个复数乘法需要四次乘法和两次加法，计算X(k)的运算量是4N次乘法和2N+2(N-1)=4N-2次实数加法。可以说相比Goertzel算法运算量要大得多。
Goertzel利用了W_N的旋转周期性，因为W_N^{-kN}=1，在X(k)左右分别乘上这个系数计算公式仍然成立：
<img src="svgs/05572357fc15f33f5ad2411cee2744ea.svg?invert_in_darkmode" align=middle width=417.4160958pt height=34.3378431pt/><img src="svgs/33ad2764fb0729786d18363ae4b12797.svg?invert_in_darkmode" align=middle width=193.0245306pt height=24.657534pt/>

由这个公式我们可以得到两个信号的序列卷积和，于是设

<img src="svgs/73522918234ab62211d6ddb140a185b2.svg?invert_in_darkmode" align=middle width=298.9922925pt height=34.3378431pt/>

当N=n时，

<img src="svgs/73522918234ab62211d6ddb140a185b2.svg?invert_in_darkmode" align=middle width=298.9922925pt height=34.3378431pt/>

接着可以对这个离散卷积和形式的式子做z变换，得出频率响应

<img src="svgs/0180c5ad60222d7139ace075e3b949cb.svg?invert_in_darkmode" align=middle width=131.9044386pt height=27.7756545pt/>

由

<img src="svgs/e035c350f671dea2262d5c591e78413a.svg?invert_in_darkmode" align=middle width=91.48401405pt height=33.2053986pt/>

可得

<img src="svgs/3bf1279d8f7037b96493df79ff594141.svg?invert_in_darkmode" align=middle width=217.1746434pt height=30.0456453pt/>

可以得到差分方程的形式

<img src="svgs/ea754d3f349a16b78293e521e0a8742c.svg?invert_in_darkmode" align=middle width=224.35503915pt height=30.0456453pt/>
此时Goertzel算法如上述公式所示，比起FFT而言最大的优势是可以在输入的同时进行运算。

#### 实现步骤
枚举识别所需的八种频率
对每种频率进行递推方程的计算
得到每种频率对应的Goertzel计算结果
再进行挑选就得到一对标准频率
再查表就可以得到具体输入的数字
#### 代码如下：

    f = [697 770 852 941 1209 1336 1477 1633];
                    freq_indices = round(f/8000*400) + 1;   
                    dft_data = goertzel(app.y_sequence,freq_indices);
                    
                    [index1,index2]=getMaxTowNum(app,abs(dft_data));
                    freqs=[f(index1),f(index2)];        
                    [~,target,~,~]=findTargetBtn(app,2,freqs);

#### 双音多频（DTMF）信号序列的生成
##### 任务要求
用matlab生成wav文件或者文件，并写入计算机的硬盘中。
##### 原理简述
采用一定规定的数据形式来对拨号信进行拼接。
根据采样率为8000，一个音的长度为100ms,其中有声部分是45-55ms，我们设置为50ms，这样我们就得到400个点，在序列生成前后各加上200个点，将这800个点依次拼接其来，再将每个信号拼接起来，我们就得到有声部分和静默部分的一个总合集。
使用audiowrite('file1.wav',app.y_sequence,8000)可以输出wav文件，来保存波形数据。其中name是波形文件的名称，y是波形时域的数据，fs是音频采样率。
其次在我研究matlab 的读写函数后，我也设计成可以保存为文件格式，其中的数据排列。

##### 实现步骤
##### 生成序列的具体步骤：
根据拨号的个数创建数组
对每个拨号音构造出静默200点，有声400点，再静默200点的数据
拼接构造的数据

##### 生成一段拨号序列
    function generateOneSequence(app,y_TimeDome)
               y_TimeDome=[zeros(1,200),y_TimeDome,zeros(1,200)];
               app.y_sequence=[app.y_sequence,y_TimeDome];
               
            end
#### 双音多频（DTMF）信号序列的端点切割
##### 任务要求
基于生成的序列进行端点切割。
##### 原理简述
由我们生成的信号序列的规律可以进行端点切割，先将数据分成按拨号数量的数据组，其次是我们知道一个数据是由200+400+200点集的组合，所以我们可以在截取400点集的集合再调用我们之前分装的FFT和Goertzel函数来分个检测出拨号数字并添加到数组中。
##### 实现步骤
##### 端点切割的具体步骤：
确定生成的信号序列的排列
通过计算总信号序列个数除以采样点个数得到拨号数量
For循环进行切割，得到处理的数据

代码如下：

    app.num_of_all=ceil(length(app.y_sequence_All)/800);
                tic
                for i=1:app.num_of_all               
                    tmp=app.y_sequence_All(1+800*(i-1):800*i);
                    app.y_sequence=tmp(201:600);
                    app.y_sequence=FFT(app,app.y_sequence);
                    [fLen,freqs]=getTargetFreqs(app,app.y_sequence);
                    [~,target,~,~]=findTargetBtn(app,fLen,freqs);
                    app.DecodingOutPut_str=sprintf('%s%s',app.DecodingOutPut_str,target);
                end
#### 双音多频（DTMF）信号序列的解码
#### 任务要求
对序列切割出的区间进行端点解码。
#### 原理简述
对信号序列的解码思路和之前的单音解码方式一样，在FFT变换后，或者Goertzel算法后，求出频域能量最大的两个点，得到一对解码后频率。对比标准频率后，即可求出拨号数字。
实现步骤
#### FFT算法的解码具体步骤：
得到每个拨号音区间
进行FFT变换
提取频谱中范围在误差以内的频率，得到拨号数字。

    function FFTButtonValueChanged(app, event)
                app.DecodingOutPut_str='';
                app.FFT_time.Text='计算时间:';
                app.num_of_all=ceil(length(app.y_sequence_All)/800);
                tic
                for i=1:app.num_of_all               
                    tmp=app.y_sequence_All(1+800*(i-1):800*i);
                    app.y_sequence=tmp(201:600);
                    app.y_sequence=FFT(app,app.y_sequence);
                    [fLen,freqs]=getTargetFreqs(app,app.y_sequence);
                    [~,target,~,~]=findTargetBtn(app,fLen,freqs);
                    app.DecodingOutPut_str=sprintf('%s%s',app.DecodingOutPut_str,target);
                
                end
                t1=toc;
                app.FFT_time.Text=strcat(app.FFT_time.Text,num2str(t1));
                app.FFT_decoded_num.Text=app.DecodingOutPut_str;
                
            end

#### 双音多频（DTMF）应用的界面设计
#### 任务要求
设计DTMF发生和接收的界面，有良好的交互性，使得符合输入输出交互模式。
#### 原理简述
利用AppDesigner进行UI的拖拽设计。
用面向对象的设计思路进行回调函数的编程。
#### 实现步骤
设计上主要分为输入数字区和展示区，左侧是输入数字，与用户交互拨号，就像是用户正常拨号界面，解码输出的是接收端接受用户的输入的数字。在播放数字时，也有声音进行反馈。其次我在设计的时候参考手机拨号输出界面。设计了情况和退格，我在考虑到清空和退格应该也有音效进行反馈，比较跟手。右侧是渲染图标。
#### 运行结果及分析
##### 单音的产生和识别
 
图片见PDF报告
图 1 按键"159D#08*AB"的识别结果和误差


#### 信号序列的生成

图片见PDF报告
图2发生器

这里的文件名可以更改，如果选择wav格式，文件名也会随之改变，默认的解码文件名也会随着定义生成信号的文件名的改变而改变。
发生器会获取主界面输入的信号序列，并以时域波形的形式展现出来，当我们在主界面更改信号序列的时候，该界面也会随着改变。播放可以播放当前波形图。

#### 信号序列的解码
因为是默认获取刚刚生成的文件，文件名也是自己刚刚设置的文件名，点击读取就可以通过文件获得这份信号序列，点击播放就可以听到该波形图。点击FFT或者G解码就可以得到解码后的结果以及他们的计算时间。
 
图片见PDF报告
图 5接收器


部分GUI界面设计图
图片见PDF报告
图 6拨号器
图片见PDF报告
图 7 wav格式选择

#### 参考资料
[1]张雅琪,基于Matlab的双音多频信号仿真[J],DataBase数据库
[2]徐阿勇,基于MATLAB的DTMF技术计算机模拟[J],温州师范学院学报(自然科学版),2005
	[3] DTMF百度定义
（https://baike.baidu.com/item/DTMF/3106215?fr=aladdin）
	[4]MATLABA官网查询函数使用
(https://ww2.mathworks.cn/?s_tid=gn_logo)