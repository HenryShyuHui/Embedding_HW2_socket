# Embedding system HW2

Name : 徐華佑
學號：R11921018

## 執行方式
可以直接將這整個專案匯入Mbed OS，選擇" Link to an existing shared Mbed OS instance" 並貼上下列網址
https://github.com/HenryShyuHui/embedding_hw2_socket

執行平台: B-L475E-IOT01A -2C
server code 網址: https://github.com/HenryShyuHui/embedding_hw2_socket_modified_file/blob/main/server_cube.py

1. 點入專案，在"mbed_app.json"中，修改hostname->value為自己電腦的IP位置；wifi-ssid修改為要連接網路的名稱；wifi-passward輸入該網路的密碼。
2. 將檔案build入stm32並執行。
3. 即可連上網，並且開始傳送資料
4. 將另一份提供的server.py檔下載，並且也將最上面的 HOST = '填入電腦的IP位置' 
5. 先執行server.py檔，再執行stm32，即可完成傳輸

ps. 若無法執行成功，可以從下方來進行排除
1. 顯示單位需要改成課堂提及的"std"
2. 所使用網路須為2.4G
3. 若在最後compile main.cpp檔案無法成功，出現沒有.h檔案時，需另外下載 "BSP_B-L475E-IOT01"放入專案資料夾中
4. 若以上都無法解決，請點選: https://github.com/HenryShyuHui/embedding_hw2_socket_modified_file.git

## 實作內容
傳輸內容: 
在embedding_hw2_socket/source的main.cpp中，讀取出板子上的加速度及角速度的資訊，實作6-axis的IMU可視化。
在靈敏度上，加速度計選擇±2G，角速度選擇±2000dps，所以需要乘上一個參數來轉換所量測出的資訊，這個數值在"lsm6dsl.h"中得到並匯入。
利用Complementary Filter來減少誤差後得到的roll, pitch, yaw數值並傳給server。
server.py檔在接收到這三個數值之後會轉化成可視化的資訊，可以看到目前stm32的姿態，在跟隨stm32的姿態做出反應。

## 實作結果
一般IMU若實作在9-axis下會較為準確，而本次目標是要用socket來傳輸資料，故無再向下延伸；且因為一般在執行IMU前，會需要進行校正，以減少誤差，此實驗也並沒有執行這部分，因此在可視化上會有誤差，並且在經由積分轉換成位置之後會有誤差累積的產生，導致可視化會有傾斜的現象，且因為沒有經過校正，無法校正z軸的部分，所以會導致可是化圖形不斷旋轉。 

影片: (https://youtu.be/S3Whsx9Urhw)


參考資料:
https://zhuanlan.zhihu.com/p/611568999?utm_id=0



