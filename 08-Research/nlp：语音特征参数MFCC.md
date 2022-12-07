在语音识别（SpeechRecognition）和话者识别（SpeakerRecognition）方面，最常用到的语音特征就是**梅尔倒谱系数**（Mel-scaleFrequency Cepstral Coefficients，简称**MFCC**）。根据人耳听觉机理的研究发现，人耳对不同频率的声波有不同的听觉敏感度。从200Hz到5000Hz的语音信号对语音的清晰度影响对大。两个响度不等的声音作用于人耳时，则响度较高的频率成分的存在会影响到对响度较低的频率成分的感受，使其变得不易察觉，这种现象称为**掩蔽效应**。由于频率较低的声音在内耳蜗基底膜上行波传递的距离（速度）大于频率较高的声音，故一般来说，低音容易掩蔽高音，而高音掩蔽低音较困难。在低频处的声音掩蔽的临界带宽较高频要小。所以，**人们从低频到高频这一段频带内按临界带宽的大小由密到疏安排一组带通滤波器，对输入信号进行滤波。**将每个带通滤波器输出的信号能量作为信号的基本特征，对此特征经过进一步处理后就可以作为语音的输入特征。由于这种特征不依赖于信号的性质，对输入信号不做任何的假设和限制，又利用了听觉模型的研究成果。因此，这种参数比基于声道模型的LPCC相比具有更好的鲁邦性，更符合人耳的听觉特性，而且当信噪比降低时仍然具有较好的识别性能。

MFCC是在Mel标度频率域提取出来的倒谱参数，Mel标度描述了人耳频率的非线性特性，它与频率的关系可用下式近似表示：

$Mel(f)=2585 \times log(1+\frac{f}{700})$

其中 $f$为频率，单位Hz。下图展示了Mel频率与线性频率之间的关系：

![](https://dracula-images.oss-cn-beijing.aliyuncs.com/draculav2-b5d2429a0ed09f8fe601e4f796d8617d_b.jpg)

附：

```python
import numpy as np
import matplotlib.pyplot as plt

x = np.arange(8001)
y = 2595 * np.log10(1+x/700)

plt.plot(x, y, color='blue', linewidth=3)

plt.xlabel("f", fontsize=17)
plt.ylabel("Mel(f)", fontsize=17)
plt.xlim(0,x[-1])
plt.ylim(0,y[-1])

plt.savefig('mel_f.png', dpi=500)
```

## 二、MFCC提取过程

**基本流程：**

> 连续语音 $\rightarrow$\\rightarrow 预加重 $\rightarrow$\\rightarrow 分帧 $\rightarrow$\\rightarrow 加窗 $\rightarrow$\\rightarrow FFT $\rightarrow$\\rightarrow Mel滤波器组 $\rightarrow$\\rightarrow 对数运算 $\rightarrow$\\rightarrow DCT

**0\. 连续语音**

我们这里截取了3.5s的原始声音数据

```python
import numpy as np
import scipy.io.wavfile
from matplotlib import pyplot as plt
from scipy.fftpack import dct

# 原始数据,读取前3.5s 的数据
sample_rate, signal = scipy.io.wavfile.read('OSR_us_000_0010_8k.wav')
original_signal = signal[0:int(3.5*sample_rate)]

signal_num = np.arange(len(signal))
sample_num = np.arange(len(original_signal))

# 绘图 01
plt.figure(figsize=(11,7), dpi=500)

plt.subplot(211)
plt.plot(signal_num/sample_rate, signal, color='black')
plt.plot(sample_num/sample_rate, original_signal, color='blue')
plt.ylabel("Amplitude")
plt.title("signal of Voice")

plt.subplot(212)
plt.plot(sample_num/sample_rate, original_signal, color='blue')
plt.xlabel("Time (sec)")
plt.ylabel("Amplitude") 
plt.title("3.5s signal of Voice ")

plt.savefig('mfcc_01.png')
```

![](https://pic1.zhimg.com/v2-4dca2d7f64339dff9e374f0da3d1dd58_b.jpg)

1.  **预加重**

> 预加重处理其实是将语音信号通过一个高通滤波器：  
> $H(z)=1-\mu z^{-1}$H(z)  
> 其中 $\mu=0.9～1$，我们通常取为**0.97**。预加重的目的是**提升高频部分**，使信号的频谱变得平坦，保持在低频到高频的整个频带中，能用同样的信噪比求频谱。同时，也是为了消除发生过程中声带和嘴唇的效应，来补偿语音信号受到发音系统所抑制的高频部分，也为了突出高频的共振峰。

$y(t)=x(t)-\alpha x(t-1)$y(t)

```python
# 预加重
pre_emphasis = 0.97
emphasized_signal = np.append(original_signal[0], original_signal[1:] - pre_emphasis * original_signal[:-1])
emphasized_signal_num = np.arange(len(emphasized_signal))

# 绘图 02
plt.figure(figsize=(11,4), dpi=500)

plt.plot(emphasized_signal_num/sample_rate, emphasized_signal, color='blue')
plt.xlabel("Time (sec)", fontsize=14)
plt.ylabel("Amplitude", fontsize=14)
plt.title("emphasized signal of Voice", fontsize=14)

plt.savefig('mfcc_02.png')
```

![](https://pic2.zhimg.com/v2-a25cdff1be9325cff1b76592e7cd211d_b.jpg)

**2\. 分帧**

> 先将N个采样点集合成一个观测单位，称为**帧**。通常情况下N的值为256或512，涵盖的时间约为20~30ms左右。为了避免相邻两帧的变化过大，因此会让两相邻帧之间有一段重叠区域，此重叠区域包含了M个取样点，通常M的值约为N的1/2或1/3。**通常语音识别所采用语音信号的采样频率为8KHz或16KHz，以8KHz来说，若帧长度为256个采样点，则对应的时间长度是256/8000 1000=32ms。**

预加重后，我们需要将信号分成短时帧。这一步背后的基本原理是**信号中的频率随时间而变化，所以在大多数情况下，对整个信号进行傅立叶变换是没有意义的**，因为我们会随着时间的推移丢失信号的频率轮廓。为了避免这种情况，我们可以安全地假设信号中的频率在很短的时间内是平稳的。因此，通过在这个短时间帧内进行傅里叶变换，我们可以通过连接相邻帧来获得信号的频率轮廓的良好近似。

语音处理范围内的典型帧大小范围为20毫秒到40毫秒，连续帧之间重叠50％（+/- 10％）。流行设置25毫秒的帧大小，frame\_size = 0.025和-10毫秒的步幅（15毫秒重叠）， frame\_stride = 0.01。

```python
# 分帧
frame_size = 0.025
frame_stride = 0.1
frame_length = int(round(frame_size*sample_rate))
frame_step = int(round(frame_stride*sample_rate)) 
signal_length = len(emphasized_signal)
num_frames = int(np.ceil(float(np.abs(signal_length-frame_length))/frame_step))

pad_signal_length = num_frames * frame_step + frame_length
pad_signal = np.append(emphasized_signal, np.zeros((pad_signal_length - signal_length)))

indices = np.tile(np.arange(0,frame_length),(num_frames,1))+np.tile(np.arange(0,num_frames*frame_step,frame_step), (frame_length, 1)).T
frames = pad_signal[np.mat(indices).astype(np.int32, copy=False)]

# 绘图 03
plt.figure(figsize=(11,4), dpi=500)

pad_signal_num = np.arange(len(pad_signal))
plt.plot(pad_signal_num/sample_rate, pad_signal, color='blue')
plt.xlabel("Time (sec)", fontsize=14)
plt.ylabel("Amplitude", fontsize=14)
plt.title("pad signal of Voice", fontsize=14)

plt.savefig('mfcc_03.png')
```

![](https://pic1.zhimg.com/v2-9c47a55869345858296b5bd110d143c4_b.jpg)

**3\. 加窗**

> 将每一帧乘以**汉明窗**，以增加帧左端和右端的连续性。假设分帧后的信号为S(n), n=0,1,…,N-1, N为帧的大小，那么乘上汉明窗后  
> $S^{'}(n)=S(n) \times W(n)$ 
> $W(n)$W(n) 形式如下：  
> $W(n,a)=(1-a)-a \times cos(\frac{2 \pi n}{N-1})$, $0\leq n \leq N-1$不同的 $a$a 值会产生不同的汉明窗，一般情况下取为**0.46**。

```python
# 汉明窗
N = 200
x = np.arange(N)
y = 0.54 * np.ones(N) - 0.46 * np.cos(2*np.pi*x/(N-1))

plt.plot(x, y, label='Hamming')
plt.xlabel("Samples")
plt.ylabel("Amplitude") 
plt.legend()
plt.savefig('hamming.png', dpi=500)
```

![](https://pic3.zhimg.com/v2-ba41bb735d460c435fb43bb710f8513a_b.jpg)

在将信号切分为帧后，我们将一个窗口函数（如Hamming窗口）应用于每个帧。有几个原因需要对窗框应用窗函数，特别是**为了抵消FFT所假设的数据是无限的，并减少频谱泄漏**。

```python
# 加汉明窗
frames *= np.hamming(frame_length)
# Explicit Implementation
# frames *= 0.54 - 0.46 * np.cos((2 * np.pi * n) / (frame_length - 1))
```

**4\. 快速傅里叶变换 FFT**

> 由于信号在时域上的变换通常很难看出信号的特性，所以通常将它转换为频域上的能量分布来观察，不同的能量分布，就能代表不同语音的特性。所以在乘上汉明窗后，每帧还必须再经过快速傅里叶变换以得到在频谱上的能量分布。对分帧加窗后的各帧信号进行快速傅里叶变换得到各帧的频谱。并对语音信号的频谱取模平方得到语音信号的功率谱。设语音信号的DFT为：  
> $X_a(k)=\sum_{n=0}^{N-1}{x(n)e^{-2 \pi i k/N}}$X\_a(k)=\\sum\_{n=0}^{N-1}{x(n)e^{-2 \\pi i k/N}} ， $0 \leq k \leq N$0 \\leq k \\leq N  
> 式中 $x(n)$x(n) 为输入的语音信号， $N$N 表示傅里叶变换的点数。

```python
# 傅里叶变换和功率谱
NFFT = 512
mag_frames = np.absolute(np.fft.rfft(frames, NFFT))  # Magnitude of the FFT
pow_frames = (1.0 / NFFT) * (mag_frames ** 2)
```

**5\. 三角带通滤波器 （Mel滤波）**

> 将能量谱通过一组Mel尺度的三角形滤波器组，定义一个有M个滤波器的滤波器组（滤波器的个数和临界带的个数相近），采用的滤波器为三角滤波器，中心频率为 。M通常取22-26。各f(m)之间的间隔随着m值的减小而缩小，随着m值的增大而增宽，如图所示

![](https://pic1.zhimg.com/v2-803610a243bb3f7d07692d914625ebc8_b.jpg)

> 三角滤波器的频率响应定义为:  
> $\begin{equation}   \begin{aligned} H_m(k)=\left\{                \begin{array}{**lr**}                &0, & & {k<f(m-1)}  \\                &\frac{2(k-f(m-1))}{(f(m+1)-f(m-1))(f(m)-f(m-1))}, & &{f(m-1)<k<f(m)} \\                &\frac{2(f(m+1)-k)}{(f(m+1)-f(m-1))(f(m)-f(m-1))},& &{f(m)<k<f(m+1)} \\             &0, & &k>f(m+1)                 \end{array}   \right.   \end{aligned} \end{equation}$\\begin{equation} \\begin{aligned} H\_m(k)=\\left\\{ \\begin{array}{\*\*lr\*\*} &0, & & {k<f(m-1)} \\\\ &\\frac{2(k-f(m-1))}{(f(m+1)-f(m-1))(f(m)-f(m-1))}, & &{f(m-1)<k<f(m)} \\\\ &\\frac{2(f(m+1)-k)}{(f(m+1)-f(m-1))(f(m)-f(m-1))},& &{f(m)<k<f(m+1)} \\\\ &0, & &k>f(m+1) \\end{array} \\right. \\end{aligned} \\end{equation}  
> 式中， $\sum_{m=0}^{M-1}{H_m(k)} = 1$\\sum\_{m=0}^{M-1}{H\_m(k)} = 1  
> 三角带通滤波器有两个主要目的：**对频谱进行平滑化，并消除谐波的作用，突显原先语音的共振峰。**（因此一段语音的音调或音高，是不会呈现在MFCC 参数内，换句话说，以MFCC 为特征的语音辨识系统，并不会受到输入语音的音调不同而有所影响）此外，还可以降低运算量。

**6\. 计算每个滤波器组输出的对数能量为**

> $s(m)=ln\left( \sum_{k=0}^{N-1}{|X_a(k)|^2 H_m(k)} \right)$s(m)=ln\\left( \\sum\_{k=0}^{N-1}{|X\_a(k)|^2 H\_m(k)} \\right) ， $0\leq m \leq M$0\\leq m \\leq M

```python
# 将频率转换为Mel频率
low_freq_mel = 0

nfilt = 40
high_freq_mel = (2595 * np.log10(1 + (sample_rate / 2) / 700))
mel_points = np.linspace(low_freq_mel, high_freq_mel, nfilt + 2)  # Equally spaced in Mel scale
hz_points = (700 * (10**(mel_points / 2595) - 1))  # Convert Mel to Hz

bin = np.floor((NFFT + 1) * hz_points / sample_rate)

fbank = np.zeros((nfilt, int(np.floor(NFFT / 2 + 1))))

for m in range(1, nfilt + 1):
    f_m_minus = int(bin[m - 1])   # left
    f_m = int(bin[m])             # center
    f_m_plus = int(bin[m + 1])    # right
    for k in range(f_m_minus, f_m):
        fbank[m - 1, k] = (k - bin[m - 1]) / (bin[m] - bin[m - 1])
    for k in range(f_m, f_m_plus):
        fbank[m - 1, k] = (bin[m + 1] - k) / (bin[m + 1] - bin[m])
filter_banks = np.dot(pow_frames, fbank.T)
filter_banks = np.where(filter_banks == 0, np.finfo(float).eps, filter_banks)  # Numerical Stability
filter_banks = 20 * np.log10(filter_banks)  # dB
```

**7\. 经离散余弦变换（DCT）得到MFCC系数**

> $C(n)=\sum_{m=0}^{N-1}{s(m)cos\left( \frac{\pi n(m-0.5)}{M} \right)}$C(n)=\\sum\_{m=0}^{N-1}{s(m)cos\\left( \\frac{\\pi n(m-0.5)}{M} \\right)} ， $n=1,2,...,L$n=1,2,...,L  
> 将上述的对数能量带入离散余弦变换，求出L阶的Mel-scale Cepstrum参数。L阶指MFCC系数阶数，通常取**12-16**。这里M是三角滤波器个数。

事实证明，前一步计算出的滤波器组系数高度相关，这在某些机器学习算法中可能存在问题。因此，我们可以应用离散余弦变换（DCT）去相关滤波器组系数并产生滤波器组的压缩表示。通常，对于自动语音识别（ASR），所得到的倒谱系数2-13被保留，其余的被丢弃; num\_ceps = 12。丢弃其他系数的原因是它们表示滤波器组系数的快速变化，并且这些细节不会有助于自动语音识别（ASR）。

```python
num_ceps = 12
mfcc = dct(filter_banks, type=2, axis=1, norm='ortho')[:, 1 : (num_ceps + 1)]
(nframes, ncoeff) = mfcc.shape
```

可以将正弦升降1应用于MFCC以降低已被声称在噪声信号中改善语音识别的较高MFCC.

```python
n = np.arange(ncoeff)
cep_lifter =22
lift = 1 + (cep_lifter / 2) * np.sin(np.pi * n / cep_lifter)
mfcc *= lift
```

如前所述，为了平衡频谱并改善信噪比（SNR），我们可以简单地从所有帧中减去每个系数的平均值。平均归一化滤波器组：

```python
filter_banks -= (np.mean(filter_banks, axis=0) + 1e-8)
```

对于MFCC也是如此，平均归一化MFCC：

```python
mfcc -= (np.mean(mfcc, axis=0) + 1e-8)
```

最后结果为

```
# 绘图 04
plt.figure(figsize=(11,7), dpi=500)

plt.subplot(211)
plt.imshow(np.flipud(filter_banks.T), cmap=plt.cm.jet, aspect=0.2, extent=[0,filter_banks.shape[1],0,filter_banks.shape[0]]) #画热力图
plt.title("MFCC")

plt.subplot(212)
plt.imshow(np.flipud(mfcc.T), cmap=plt.cm.jet, aspect=0.2, extent=[0,mfcc.shape[0],0,mfcc.shape[1]])#热力图
plt.title("MFCC")

plt.savefig('mfcc_04.png')
```

![](https://pic2.zhimg.com/v2-c9486b92ca610075083b9f600a707c8d_b.jpg)

**8\. 对数能量**

> 此外，一帧的音量（即能量），也是语音的重要特征，而且非常容易计算。因此，通常再加上一帧的对数能量（定义：一帧内信号的平方和，再取以10为底的对数值，再乘以10）使得每一帧基本的语音特征就多了一维，包括一个对数能量和剩下的倒频谱参数。  
> 注：若要加入其它语音特征以测试识别率，也可以在此阶段加入，这些常用的其它语音特征包含音高、过零率以及共振峰等。

**9.动态查分参数的提取（包括一阶差分和二阶差分）**

> 标准的倒谱参数MFCC只反映了语音参数的静态特性，语音的动态特性可以用这些静态特征的差分谱来描述。实验证明：把动、静态特征结合起来才能有效提高系统的识别性能。差分参数的计算可以采用下面的公式：  
> $f(x)=\left\{ \begin{aligned} &C(t+1)-C(t),& &t<K \\ &\frac{\sum_{k=1}^{K}{k(c_{t+k}-c_{t-k})}}{\sqrt{2\sum_{k=1}^{K}{k^2}}} ,& &Others \\ &C(t1)-C(t-1), & &t \geq Q-K  \end{aligned} \right.$f(x)=\\left\\{ \\begin{aligned} &C(t+1)-C(t),& &t<K \\\\ &\\frac{\\sum\_{k=1}^{K}{k(c\_{t+k}-c\_{t-k})}}{\\sqrt{2\\sum\_{k=1}^{K}{k^2}}} ,& &Others \\\\ &C(t1)-C(t-1), & &t \\geq Q-K \\end{aligned} \\right.  
> 式中,dt表示第t个一阶差分；Ct表示第t个倒谱系数；Q表示倒谱系数的阶数；K表示一阶导数的时间差，可取1或2。将上式中结果再代入就可以得到二阶差分的参数。

> **总结：**因此，MFCC的全部组成其实是由：N维MFCC参数（N/3MFCC系数+ N/3一阶差分参数+ N/3二阶差分参数）+帧能量（此项可根据需求替换）。

## 参考

1.  [^](https://zhuanlan.zhihu.com/p/88625876#ref_1_0)语音特征参数MFCC提取过程详解 [https://blog.csdn.net/jojozhangju/article/details/18678861](https://blog.csdn.net/jojozhangju/article/details/18678861)
2.  [^](https://zhuanlan.zhihu.com/p/88625876#ref_2_0)python 实现MFCC [https://blog.csdn.net/TH\_NUM/article/details/80597495](https://blog.csdn.net/TH_NUM/article/details/80597495)