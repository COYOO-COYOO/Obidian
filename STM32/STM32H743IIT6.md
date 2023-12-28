
### 前言
`STM32CubeMX 配置工程一般步骤`
1）工程初步建立和保存
2）RCC 设置
3）时钟系统（时钟树）配置
4） GPIO 功能引脚配置
5）生成工程源码
6） 户代码

### 1、GPIO操作
`GPIO配置如下`
![1.png](STM32/STM32H743IIT6/1.png)
 
`RCC配置`
- 选项 High Speed Clock（HSE）用来配置 HSE
- 选项 Low Speed Clock（LSE）用来配置 LSE
- 选项 Master Clock Output 1 用选择是否使能 MCO1 引脚时钟输出
- 选项 Master Clock Output 2 用来选择是否使能 MCO2 引脚时钟输出
- 选项 Audio Clock Input（I2S_CKIN）用来选择是否从 I2S_CKIN(PC9)输入 I2S 时钟

`注意`：因为选项 Master Clock Output 2 和选项 Audio Clock Input（I2S_CKIN）都是使用的 PC9 引脚，所以如果我们使能了其中一个，那么另一个选项会自动显示为红色，也就是不允许配置，这就是 STM32CubeMX 的自动冲突检测功能。

本小节使用到 HSE，设置选项 High Speed Clock（HSE）的值为Crystal/Ceramic Resonator（使用晶振/陶瓷振荡器）即可。

这里还需要说明一下，值 Bypass Clock Source 的意思是旁路时钟源，也就是不使用使用晶振/陶瓷振荡器，直接通过外部提供一个可靠的 4-26MHz 时钟作为 HSE。配置好的 RCC 配置选项如下图 ：
![2.png](STM32/STM32H743IIT6/2.png)

`时钟系统配置`
- 配置的主要是外部晶振大小，分频系数，倍频系数以及选择器。
- 主要的配置部分分两部分：第一部分是配置系统时钟，第二部分是配置 SYSTICK、AHB、APB1、APB2、APB3和 APB4 的分频系数。
`第一部分配置：`
![3.png](STM32/STM32H743IIT6/3.png)

① 时钟源参数设置：HSE 或者 HSI 配置。这里选择 HSE 为时钟源，所以之前必须在 RCC 配置中我们开启 HSE。
② 时钟源选择：HSE 还是 HSI。这里配置选择器选择 HSE 即可。
③ PLL 分频系数 M 配置。分频系数 M 设置为 5。
④ 主 PLL 倍频系数 N 配置。倍频系数 N 设置为 160。
⑤ 主 PLL 分频系数 P 配置。分频系数 P 配置为 2。
⑥ 系统时钟时钟源选择：PLL,HSI 还是 HSE。选择 PLL，选择器选择 PLLCLK即可。
⑦ 经过上面配置以后此时 SYSCLK=400Mhz。

`第二部分配置：`
上述过程会生成标准的 400MHz 系统时钟，配置 AHB，APB1、APB2、APB3、APB4 和 Systick 的分频系数。
![4.png](STM32/STM32H743IIT6/4.png)

AHB,APB1、APB2、APB3 和 APB 总线时钟以及 Systick 时钟的最终来源都是系统时钟SYSCLK。其中 AHB 总线时钟 HCLK 是由 SYSCLK 经过 AHB 预分频器之后的来，若要设置HCLK为200MHz(最大也就200Mhz)，只需要配置图中标号8的地方为2即可。得到 HCLK 之后，接下来我们将在图标号 9~12 处同样的方法依次配置 APB3、APB1、APB2和 APB4 分频系数分别为 2，2、2 和 2 即可。

`注意！`systick 固定为 400MHz，配置完成之后，那么 HCLK=200MHZ，Systic=400MHz，PCLK1=100MHz，PCLK2=100MHz，PCLK3=100Mhz,
PCLK4=100MHz。

`Cortex-M7 内核基本配置`
该界面一共有两个配置栏目。第一个配置栏目 Cortex Interface Settings 下面有两个配置
项：
1) CPU ICache：使能 I-Cache。
2) CPU DCache: 使能 D-Cache。
第二个配置栏目 Cortex Memory Protection Unit，是用来配置内存保护单元 MPU。
![5.png](STM32/STM32H743IIT6/5.png)

`生成工程代码：`
![6.png](STM32/STM32H743IIT6/6.png)

![7.png](STM32/STM32H743IIT6/7.png)











