# Embedding system HW2

Name : 徐華佑
學號：R11921018

## 執行方式
可以直接將這整個專案匯入Mbed OS，選擇" Link to an existing shared Mbed OS instance" 並貼上下列網址
https://github.com/HenryShyuHui/embedding_hw2_socket

執行平台: B-L475E-IOT01A -2C

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


參考資料:
https://zhuanlan.zhihu.com/p/611568999?utm_id=0


![](./resources/official_armmbed_example_badge.png)
# Socket Example

This example shows usage of [network-socket API](https://os.mbed.com/docs/mbed-os/latest/apis/network-socket.html).

The program brings up an underlying network interface and if it's Wifi also scans for access points.
It creates a TCPSocket and performs an HTTP transaction targeting the website in the `mbed_app.json` config.

The example can be configured to use a TLSSocket. This works only on devices that support TRNG.

## Selecting the network interface

This application is able to use any network interface it finds.

The interface selections is done through weak functions that are overridden by your selected target or any additional
component that provides a network interface.

If more than one interface is provided the target configuration `target.network-default-interface-type`
selects the type provided as the default one. This is usually the Ethernet so building on Ethernet enabled boards,
you do not need any further configuration.

### Configuring mbedtls

By default the examples uses a TCP socket. To enable TLS edit the mbed_app.json to turn on the `use-tls-socket` option:

```
        "use-tls-socket": {
            "value": true
        }
```

It might be necessary to configure the mbedtls library with appropriate macros in mbed_app.json file. Some boards
(like UBLOX_EVK_ODIN_W2) will work fine without any additional configuration and some of them might require some minimal
adjustment. For example K64F requires at least the following macro added:


```
        "K64F": {
            "target.macros_add" : ["MBEDTLS_SHA1_C"]
        }
```

See
[mbedtls configuration guidelines](https://github.com/ARMmbed/mbed-os/tree/master/connectivity/mbedtls#configuring-mbed-tls-features)
for more details.

Also see the API Documentation [TLSSocket](https://os.mbed.com/docs/mbed-os/latest/apis/tlssocket.html).

### WiFi

If you want to use WiFi you need to provide SSID, password and security settings in `mbed_app.json`.

If your board doesn't provide WiFi as the default interface because it has multiple interfaces you need to specify that
you want WiFi in `mbed_app.json`.

```
{
    "target_overrides": {
        "*": {
            "target.network-default-interface-type": "WIFI",
        }
    }
}
```

For more information about Wi-Fi APIs, please visit the
[Mbed OS Wi-Fi](https://os.mbed.com/docs/mbed-os/latest/apis/wi-fi.html)
documentation.

### Supported WiFi hardware

* All Mbed OS boards with build-in Wi-Fi module such as:
    * [ST DISCO IOT board](https://os.mbed.com/platforms/ST-Discovery-L475E-IOT01A/) with integrated
      [ISM43362 WiFi Inventek module](https://github.com/ARMmbed/wifi-ism43362).
    * [ST DISCO_F413ZH board](https://os.mbed.com/platforms/ST-Discovery-F413H/) with integrated
      [ISM43362 WiFi Inventek module](https://github.com/ARMmbed/wifi-ism43362).
* Boards with external WiFi shields such as:
    * [NUCLEO-F429ZI](https://os.mbed.com/platforms/ST-Nucleo-F429ZI/) with ESP8266-01

## Building and flashing the example

### Mbed OS build tools

#### Mbed CLI 2

Starting with version 6.5, Mbed OS uses Mbed CLI 2. It uses Ninja as a build system, and CMake to generate the build environment and manage the build process in a compiler-independent manner. If you are working with Mbed OS version prior to 6.5 then check the section [Mbed CLI 1](#mbed-cli-1).
1. [Install Mbed CLI 2](https://os.mbed.com/docs/mbed-os/latest/build-tools/install-or-upgrade.html).
1. From the command-line, import the example: `mbed-tools import mbed-os-example-sockets`
1. Change the current directory to where the project was imported.

#### Mbed CLI 1
1. [Install Mbed CLI 1](https://os.mbed.com/docs/mbed-os/latest/quick-start/offline-with-mbed-cli.html).
1. From the command-line, import the example: `mbed import mbed-os-example-sockets`
1. Change the current directory to where the project was imported.

### To build the example

1. Connect a USB cable between the USB port on the board and the host computer.
1. Run the following command to build the example project and program the microcontroller flash memory:

    * Mbed CLI 2

    ```bash
    $ mbed-tools compile -m <TARGET> -t <TOOLCHAIN> --flash --sterm
    ```

    * Mbed CLI 1

    ```bash
    $ mbed compile -m <TARGET> -t <TOOLCHAIN> --flash --sterm
    ```

Your PC may take a few minutes to compile your code.

The binary is located at:
* **Mbed CLI 2** - `./cmake_build/<TARGET>/<PROFILE>/<TOOLCHAIN>/mbed-os-example-sockets.bin`</br>
* **Mbed CLI 1** - `./BUILD/<TARGET>/<TOOLCHAIN>/mbed-os-example-sockets.bin`

Alternatively, you can manually copy the binary to the board, which you mount on the host computer over USB.

You can also open a serial terminal separately, rather than using the `--sterm` option, with the following command:

* Mbed CLI 2

```bash
$ mbed-tools sterm
```

* Mbed CLI 1

```bash
$ mbed sterm
```

### Expected output

(Assuming you are using a wifi interface, otherwise the scanning will be skipped)

```
Starting socket demo

2 networks available:
Network: Virgin Media secured: Unknown BSSID: 2A:35:D1:ba:c7:41 RSSI: -79 Ch: 6
Network: VM4392164 secured: WPA2 BSSID: 18:35:D1:ba:c7:41 RSSI: -79 Ch: 6

Connecting to the network...
IP address: 192.168.0.27
Netmask: 255.255.255.0
Gateway: 192.168.0.1

Resolve hostname ifconfig.io
ifconfig.io address is 104.24.122.146

sent 52 bytes: 
GET / HTTP/1.1
Host: ifconfig.io
Connection: close

received 256 bytes:
HTTP/1.1 200 OK

Demo concluded successfully 
```

## License and contributions

The software is provided under Apache-2.0 license. Contributions to this project are accepted under the same license.
Please see [contributing.md](CONTRIBUTING.md) for more info.

This project contains code from other projects. The original license text is included in those source files.
They must comply with our license guide
