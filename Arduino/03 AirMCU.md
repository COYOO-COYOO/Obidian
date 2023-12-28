- 参考手册：https://arduino.luatos.com/
- 参考文档：https://wiki.luatos.com/chips/air001/mcu.html#id4
### 1、刷机烧录教程
#### 1、使用串口下载
- 备注：
- 在Arduino中，推荐使用AirISP通过串口进行下载 ，AirISP工具也可单独使用，使用文档：[AirISP工具文档](https://arduino.luatos.com/airisp/)
串口下载的接线为串口模块的`RX`接Air001的`TXD`（`PA2`），串口模块的`TX`接Air001的`RXD`（`PA3`），如果您希望能够自动下载的话，应当把`DTR`和`RTS`连接到串口模块上的对应引脚，芯片或者开发板的`GND`与串口模块的`GND`相连。
如果没有自动下载，那么在每次下载前需要手动进入 bootloader：
- 先按下 BOOT 按键不放（即拉高 `BOOT0` 引脚）
- 按一下RST按键
- 松开 BOOT 按键
- 下载完成后，可能需要手动按一下 RST 按键以复位正常运行

#### 2、使用 SWD 调试/下载
使用 SWD 调试/下载的接线为调试器的`SWDIO`接Air001的`SWDIO`（`PA13`），调试器的`SWCLK`接Air001的`SWCLK`（`PA14`），芯片或者开发板的`GND`与调试器的`GND`相连。

- 警告：SWD烧录时的Max Clock需要手动到100KHz或更低，否则无法识别芯片。

### 2、开发板原理图
![1.png](Arduino/03%20AirMCU/1.png)

### 3、开发板引脚图
![2.png](Arduino/03%20AirMCU/2.png)

### 4、开发环境搭建
`其它开发板管理器地址`中输入AirMCU的地址 `https://arduino.luatos.com/package_air_cn_index.json`  
（海外用户使用`https://github.com/Air-duino/Arduino-pack-json-ci/releases/download/Nightly/package_air_index.json`）
![3.png](Arduino/03%20AirMCU/3.png)

安装开发板：
![4.png](Arduino/03%20AirMCU/4.png)


