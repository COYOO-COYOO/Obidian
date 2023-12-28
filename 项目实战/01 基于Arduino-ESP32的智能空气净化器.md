### 1、功能需求
- 空气净化 - 控制风扇和紫外杀菌
- SU-03T语音识别
- OLED显示屏 - 显示甲醛浓度、二氧化碳浓度和设备开关情况
- 编码器 - 调节风扇风速
- SGP30传感器 - 采集甲醛浓度和二氧化碳浓度
### 2、原理图
![1.png](项目实战/01%20基于Arduino-ESP32的智能空气净化器/1.png)
### 3、PCB 3D图
![2.png](项目实战/01%20基于Arduino-ESP32的智能空气净化器/2.png)
### 4、实物图
![3.png](项目实战/01%20基于Arduino-ESP32的智能空气净化器/3.png)
### 5、程序设计
``` c
#include "OneButton.h"

#include <Wire.h>
#include "Adafruit_SGP30.h"
#include <U8g2lib.h>


// 存储串口接收到的数据
byte data[4];

// SPG30、OLED初始配置
Adafruit_SGP30 sgp;
U8G2_SSD1306_128X64_NONAME_F_SW_I2C u8g2(U8G2_R0, /*SCL*/22, /*SDA*/21,  U8X8_PIN_NONE);  

  
// 编码器按键、风扇控制引脚
const int btn_pin = 18;
const int Fan_Pin = 25;


// 风扇PWM配置
const int freq = 5000;
const int ledChannel = 0;
const int resolution = 8;


// 编码器计数
int counter = 0;

uint16_t TVOC, eCO2;      // 定义变量
uint16_t dutyCycle = 200;

  
bool fan_state = 1;       // 风扇状态
// unsigned long currentMillis, previousMillis;

  
const int8_t Encoder_IOA = 5;
const int8_t Encoder_IOB = 14;

int16_t Encoder_Count = 20;    // 编码器计数

// 编码器上一次值和当前值
int encoder_num_c = 0, encoder_num_p = 0;

// OneButton button(btn_pin, true);

// void singleClick() {
//     Serial.println("singleClick() detected.");
// }


void setup(){

    Serial.begin(115200);
    Serial2.begin(115200);

    u8g2.begin();     //OLED 初始化

    if (! sgp.begin()){
        Serial.println("Sensor not found :(");
        while (1);
    }

    Serial.println("Starting work!");
    ledcSetup(ledChannel, freq, resolution);
    ledcAttachPin(Fan_Pin, ledChannel);
  
    // A B引脚  上拉输入
    pinMode(Encoder_IOA, INPUT_PULLUP);
    pinMode(Encoder_IOB, INPUT_PULLUP);
    button.attachClick(singleClick);

    attachInterrupt(digitalPinToInterrupt(Encoder_IOA), IOA_attachInterrupt, FALLING);
    attachInterrupt(digitalPinToInterrupt(Encoder_IOB), IOB_attachInterrupt, FALLING);
}

void loop() {
    // 语音识别处理 串口
    if (Serial2.available() > 0) {  // 如果有数据可读
        Serial2.readBytes(data, 4); // 读取4个字节的数据
        for(int i = 0; i < 4; i++)
        {
            Serial.print(data[i]);
        }

        Serial.println(" ");
        if (data[0] == 0 && data[1] == 0 && data[2] == 0 && data[3] == 1) {
            // 如果接收到的数据是"00 00 00 01"，执行相应的操作
            Serial.println("空气净化器已打开");
            fan_state = 1;
            dutyCycle = 250;
        }

        if (data[0] == 0 && data[1] == 0 && data[2] == 0 && data[3] == 2) {
            // 如果接收到的数据是"00 00 00 02"，执行相应的操作
            Serial.println("空气净化器已关闭");
            fan_state = 0;
            dutyCycle = 0;
        }
        if (data[0] == 0 && data[1] == 0 && data[2] == 0 && data[3] == 3) {
            // 如果接收到的数据是"00 00 00 02"，执行相应的操作
            Serial.println("风速已调大");
            if(dutyCycle < 230) {
                dutyCycle = dutyCycle + 20;
            }
        }
        if (data[0] == 0 && data[1] == 0 && data[2] == 0 && data[3] == 4) {
            // 如果接收到的数据是"00 00 00 02"，执行相应的操作
            Serial.println("风速已调小");
            if(dutyCycle >= 20) {
                dutyCycle = dutyCycle - 20;
            }
        }
    }

    button.tick();
    // currentMillis = millis();
    // if (currentMillis - previousMillis >= 5000) {
    //     previousMillis = currentMillis;
    // }

  
    /**
    * @brief  SGP30传感器
    * @param  
    * @retval
    */
    // delay(1000);
    if (! sgp.IAQmeasure()) {
        Serial.println("Measurement failed");
        return;
    }
    
    TVOC = sgp.TVOC;
    eCO2 = sgp.eCO2;
    Serial.print("TVOC "); Serial.print(TVOC); Serial.print(" ppb\t");
    Serial.print("eCO2 "); Serial.print(eCO2); Serial.println(" ppm");

    // if (! sgp.IAQmeasureRaw()) {
    //     Serial.println("Raw Measurement failed");
    //     return;
    // }

    // for(int dutyCycle = 0; dutyCycle <= 255; dutyCycle++){      
    //     ledcWrite(ledChannel, dutyCycle);
    //     delay(15);
    // }

    // for(int dutyCycle = 255; dutyCycle >= 0; dutyCycle--){
    //     ledcWrite(ledChannel, dutyCycle);  
    //     delay(15);
    // }
    ledcWrite(ledChannel, dutyCycle);

    // OLED显示
    u8g2.firstPage();
    do {
        u8g2.setFont(u8g2_font_cu12_tr);
        u8g2.drawStr(16*0, 16*1, "TVOC: ");
        u8g2.drawStr(16*0, 16*2, "eCO2: ");
        u8g2.drawStr(16*5, 16*1, "ppb");
        u8g2.drawStr(16*5, 16*2, "ppm");
        u8g2.setCursor(16*3, 16*1);
        u8g2.print(TVOC);
        u8g2.setCursor(16*3, 16*2);
        u8g2.print(eCO2);

        u8g2.drawStr(16*0, 16*3, "Device: ");
        if(fan_state == 0) {
            u8g2.drawStr(16*5, 16*3, "Close");
        }
        if(fan_state == 1) {
            u8g2.drawStr(16*5, 16*3, "Open");
        }
    } while(u8g2.nextPage());
}

/**
  * @brief  IOA_attachInterrupt --- Encoder_IOA中断调用函数
  * @param  无
  * @retval 无
  */
void IOA_attachInterrupt() {
    if(digitalRead(Encoder_IOA) == 0) {
        if(digitalRead(Encoder_IOB) == 0) {
            // Encoder_Count--;
            // encoder_num_c = Encoder_Count;
            // Serial.println(Encoder_Count);
            if(dutyCycle > 20) {
                dutyCycle = dutyCycle - 10;
                Serial.println(dutyCycle);
            }
        }
    }
}

/**
  * @brief  IOB_attachInterrupt --- Encoder_IOB中断调用函数
  * @param  无
  * @retval 无
  */
void IOB_attachInterrupt() {
    if(digitalRead(Encoder_IOB) == 0) {
        if(digitalRead(Encoder_IOA) == 0) {
            // Encoder_Count++;
            // encoder_num_c = Encoder_Count;
            // Serial.println(Encoder_Count);
            if(dutyCycle < 240) {
                dutyCycle = dutyCycle + 10;
                Serial.println(dutyCycle);
            }
        }
    }
}


// void click1() {
//     Serial.println("Button 1 click.");
// }
```
