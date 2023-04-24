# ESP-01

## 建議

透過 arduino 的 tx、rx 執行 at commnads 時，先將空白程式 upload 至 arduino 板，否則監控程式會看到不相干訊息。

## 常用 AT commands

- AT **(確認連接)**
    
    ``` 
    OK 
    ```

- AT+RST **(重新啟動)**

- AT+GMR **(確認韌體版本)**

    ```
    AT version:1.1.0.0(May 11 2016 18:09:56)                                        
    SDK version:1.5.4(baaeaebb)                                                     
    compile time:May 20 2016 > 15:08:19                                               
    OK
    ```

- AT+UART=9600,8,1,0,0 **(切換 baud rate 至 9600)**

    ```
    AT+UART=9600,8,1,0,0                                                            
                                                                                
    OK
    ```

    參數: baudrate, databits, stopbits, parity , flow control

- AT+CWMODE? **(查詢目前工作模式)**

    ```
    +CWMODE:2
    ```

    目前是模式 2 (AP 基地台模式), 1=STA 網卡模式, 3=BOTH (AP+STA).

- AT+CWMODE=3 **(切換至模式 3)**

    ```
    OK
    ```

- AT+CIFSR **(查詢 IP 位址)**

    ```
    +CIFSR:APIP,"192.168.4.1"                                                       
    +CIFSR:APMAC,"ca:2b:96:2f:15:88"                                                
    +CIFSR:STAIP,"0.0.0.0"                                                          
    +CIFSR:STAMAC,"c8:2b:96:2f:15:88"                                               
                                                                                    
    OK
    ```
    STA 與 AP 模式有各自的 IP, 在 STA 模式下未連線前 IP 為預設之 0.0.0.0, 連線後基地台會配發一個 192.168.X.X 的 IP; 改為 AP 或 BOTH 模式後, ESP8266 會自行配發 192.168.4.1 的 AP IP, 故 BOTH 模式下會有兩個 IP.

- AT+CWJAP="ssid","password" **(連線指定之基地台: ssid, password)**

    ```
    WIFI CONNECTED                                                                  
    WIFI GOT IP                                                                     
                                                                                    
    OK
    ```
    連線後，即使重啟 esp01、切換至模式 2 再切回模式1、3，都會自動連上該 wifi

- AT+CWJAP? **(查詢目前連線到哪一個基地台)**

    ```
    +CWJAP:"TP-LINK_E4D58E","10:fe:ed:e4:d5:8e",9,-51                               
                                                                                
    OK
    ```

-  AT+CWQAP **(離開目前連線的基地台)**

    ```
    OK                                                                              
    WIFI DISCONNECT
    ```

- AT+RESTORE **(恢復原廠設置)**
