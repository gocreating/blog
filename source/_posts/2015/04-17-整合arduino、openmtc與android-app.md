title: 整合Arduino、OpenMTC與Android App
date: 2015-04-17 07:08:31
categories:
  - Android
  - Development
  - IoT
  - Nodejs
---

為期一年的專題即將告一段落，就用本文說明如何整合Arduino、OpenMTC與Android App，建構一個完整的物連網系統

<!--more-->

## Overview

#### Idea

假如今天我們身在停車位一位難求的台北街頭，是否有方便的APP能夠指引車主快速找到車位呢？借助物聯網的力量，我們可以在停車位布置紅外線感測器，並且收集車位資料向伺服器呈報，再透過手機App檢視視覺化的資訊，解決尋找車位的需求

#### Device List

實作此專案所需的設備列於下表

|設備|型號|數量|單價(網路價+運費)|
|---|---|---|---|
|Arduino|[UNO R3](http://goods.ruten.com.tw/item/qa?21404182225562)|1|270+60|
|紅外線距離感測器|[GP2Y0A02YK0F](http://goods.ruten.com.tw/item/show?21402145277353)[^1]|3|320+?|
|Arduino Wifi Shield|[HLK-RMO4](http://www.ledsee.com/datasheet_ledsee/arduino%20start%20manual.pdf)[^2][^3]|1|?+?
|Wifi無線基地台|[BRC65c](http://www.sunfar.com.tw/ecsunfar/product/product.aspx?prodSeq=289035)[^4][^5]|1|959|

#### Architecture

整個專案遵循下圖中的架構，每一個獨立的區塊皆已標上數字標籤，以下將依序說明每一個區塊的設定步驟與程式碼

![](Overview.png)

## Arduino - ①~③

#### Config Wifi Shield

先將Wifi Shield和Arduino主板相接，`切記主板與Wifi Shield皆須供電`，等候約30~60秒待Wifi Shield開機完成，連接至SSID為`HI-LINK_XXXX`的AP，預設連線密碼是`12345678`

打開瀏覽器進入AP設定頁面`192.168.16.254`，需要做basic auth登入，帳號是`admin`，密碼也是`admin`

- `NetMode`設定為`WIFI(CLIENT)-SERIAL`
- `SSID`、`Encrypt Type`、`Password`自訂，之後在分享器上使用同一組設定即可
- `Network Mode`設定為`Client`
- `Remote Server Domein/IP`、`Local/Remote Port Number`設定為DA的Host和Port，以此專案為例，設定為系計中的`linux1.cs.nctu.edu.tw`的Port `9999`

其餘設定皆保持為預設值，按下`Apply`後，設定頁就不會再回應了，因為Wifi Shield的模式已經被我們所更動，如果想要再次進入設定頁，必須長按Wifi Shield上的Reset鈕約5秒，之後就會回到預設模式了

#### Circuit Diagram

電路的配置十分簡潔，每個IR Sensor只有三個腳位，一條電源、一條地線、一條訊號線，將三條訊號線分別接到A0、A1與A3三個ADC孔，完整配置請見下圖

![](Circuit-Diagram.png)

> ###### PS
> 由於主板上面已經套上Wifi Shield，所以線路都是接到Wifi Shield板上的線孔，而不是主板上的

#### Code

程式碼的部分，我們設計成每隔4秒就讀取一次IR Sensor的資料，轉換為實際距離，並包裝成HTTP封包上傳至DA

```
#include <string.h>
 
void setup () {
    pinMode (9, INPUT);
    Serial.begin (115200);
}
 
void loop () {
    delay (1000) ; // delay 1S
    float sensorValue0, sensorValue1,sensorValue2, inches ;
    sensorValue0 = analogRead(A0);  //sensor0 接在 port A0
    sensorValue1 = analogRead(A1);  //sensor1 接在 port A1
    sensorValue2 = analogRead(A2);  //sensor2 接在 port A2
   
    // 把從sensor讀到的資料轉成以公分為單位
    int cm0 = 10650.08 * pow(sensorValue0,-0.935) - 10;
    int cm1 = 10650.08 * pow(sensorValue1,-0.935) - 10;
    int cm2 = 10650.08 * pow(sensorValue2,-0.935) - 10;
 
    while (Serial.available ()) {
        Serial.write (Serial.read ()) ;
    }
   
    String buff0 = String(cm0);
    String buff1 = String(cm1);
    String buff2 = String(cm2);
   
    int len0 = buff0.length();
    int len1 = buff1.length();
    int len2 = buff2.length();
   
    // 把所有資訊以HTTP封包格式傳至server
    Serial.write ("POST /project HTTP/1.0rn");
    Serial.write ("Content-Type: application/x-www-form-urlencodedrn");
    Serial.write ("Content-Length: ");
    
    Serial.print (len0 + len1 + len2 + 32);
    Serial.write ("rn");
    Serial.write ("rn");
    Serial.write ("distance0=");
    Serial.print (cm0);
    Serial.write ("&distance1=");
    Serial.print (cm1);
    Serial.write ("&distance2=");
    Serial.print (cm2);
 
    Serial.write ("rn");
 
    delay (3000);
}
```

## Access Point - ④

這部分是一般的AP設置，先拉一條網路線讓AP連上Internet，然後Encrypt Type、SSID及Password與Wifi Shield上的設置一致即可

## DA - ⑤

在DA上，我們會將Wifi Shield上傳的資料以RESTful的形式轉送至OpenMTC上保存，當然，也可以在DA上直接對原始資料做處理，不過在此專案中，我們只單純地向GSCL註冊Application和Container，並將原始資料以Base64編碼後上傳，完整程式碼如下:

> ###### PS
> 目前Code沒有整理過，所以還滿凌亂的...

```
var appId = 'tttttttest';
var containerId = 'bbbbbbbbbbbla';

var request = require('request');
var express = require('express');
// var expressLayouts = require('express-ejs-layouts');
var bodyParser = require('body-parser');
var app = express();

request.post({
        url: 'http://nscl.ngrok.io/m2m/applications/',
        form: {
                application: {
              appId: 'tttttttest',
              aPoC: "http://localhost:1334",
              aPoCPaths: {
                aPoCPath: [
                  {
                    path: "(/public);(/restricted)"
                  }
                ]
              },
              announceTo: { sclList: { reference: [ ]} },
              searchStrings: {
                searchString: ["SmartMeter", "GA", "Home"]
              }
            }
        }
}, function(err,httpResponse,body){
        console.log('=========== BUILD APP LOL!!!!!!! ===========');
        console.log(body);
        console.log('============================================');





        request.post({
                url: 'http://nscl.ngrok.io/m2m/applications/' + appId + '/containers',
                form: {
                        container: {
                        id: containerId
                    }
                }
        }, function(err,httpResponse,body){
                console.log('=========== BUILD CONTAINER LOL!!!!!!! ===========');
                console.log(body);
                console.log('==================================================');
        });

});

// app.set('view engine', 'ejs');
// app.set('layout', 'mylayout');
// app.use(expressLayouts);

app.use(bodyParser.urlencoded({
    extended: true
}));

app.use(bodyParser.json());

var d_0, d_1, d_2;

app.get('/', function (req, res) {
    // res.render('demo', {
    //     d0: d_0,
    //     d1: d_1,
    //     d2: d_2
    // });
        res.end('zz');
});

app.post('/', function (req, res) {
    var data = req.body;
    d_0 = data.distance0;
    d_1 = data.distance1;
    d_2 = data.distance2;

    console.log(data);

        var postdata = {
                d0: d_0,
                d1: d_1,
                d2: d_2
        };

    request.post({
                url: 'http://nscl.ngrok.io/m2m/applications/' + appId + '/containers/' + containerId + '/contentInstances',
                form: {
                        contentInstance: {
                                content: {
                                        $t: new Buffer(JSON.stringify(postdata)).toString('base64'),
                                        contentType: 'application/json'
                                }
                        }
                }
        }, function(err,httpResponse,body){
                console.log('=========== CREATE CONTENT LOL!!!!!!! ===========');
        console.log(data.distance0, 't', data.distance1, 't', data.distance2);
                console.log(body);
                console.log('==================================================');
        res.send('success!');
        });
});

app.listen(9999);
```

## VMware & OpenMTC - ⑥~⑦

由於手邊沒有實體Linux環境，所以將OpenMTC安裝在Ubuntu的VM上，安裝步驟請見[物聯網平台–OpenMTC](http://gocreating.github.io/2015/03/27/%E7%89%A9%E8%81%AF%E7%B6%B2%E5%B9%B3%E5%8F%B0-openmtc/)

使用VM的後遺症就是網路環境不好設置，為了使GSCL和NSCL能夠從外部Access，有幾個方法可以使用:

1. 使用NAT Network，設置Port forwarding
2. 使用Bridge Network
3. 使用軟體建立Reverse Proxy

評估過後我們最終採用第三種方法，使用`ngrok`這套軟體

#### Bootstrap OpenMTC

開啟NSCL
```
$ sudo ./openmtc-NSCL/bin/nscl
```

開啟GSCL
```
$ sudo ./openmtc-GSCL/bin/gscl
```

#### Open Tunnel

```
./ngrok authtoken 5suiwG37irUxDJnmaxEpV_5vt7DoLh9m93dh8gViYEG
./ngrok http -subdomain=nscl localhost:5000
./ngrok authtoken 3NzoiHNuap3Trs92z9Fbe_6f3C2EpjeFXCFeZtA9RmL
./ngrok http -subdomain=gscl localhost:15000
```

## NA - ⑧

整個Data Flow的最末端`DA`通常是以Web或是行動裝置上的App來呈現，我們的終極目的是要在駕車時尋找空車位，因此撰寫手機應用程式是最理想的選擇

我們使用Android系統，完整的Android Studio專案與程式碼請見[Github連結](https://github.com/WakeupTsai/IOT_Projects)

## Conclusion

一年的時間看似很長，但要學習的技術可說是跨足了各領域，從硬體的電路、大型平台的操作到應用程式的撰寫，原本以為題目訂的過於簡單，大不了幾個星期就能搞定，但是最終還是耗上了整整兩個學期，真的是與其用頭腦空想不如實際走一回，才發現很多原本預期的行為，實際上並非如此，光是要天時地利本來就不容易，要人和更是難上加難吧!

[^1]: [夏普 SHARP 原裝 GP2Y0A02YK0F 紅外線測距 紅外線距離感測器](http://goods.ruten.com.tw/item/show?21402145277353)
[^2]: [HLK-RMO4(Wifi Shield) Manual](http://www.ledsee.com/datasheet_ledsee/arduino%20start%20manual.pdf)
[^3]: [HLK-RMO4(Wifi Shield) Spec](http://www.hlktech.net/product_detail.php?ProId=39)
[^4]: [Sapido BRC65c 11AC雙頻無線分享器750M](http://www.sunfar.com.tw/ecsunfar/product/product.aspx?prodSeq=289035)
[^5]: [BRC65c Manual](http://www.sapido.com.tw/TC/images/product/BRC65c/UM_BRC65c.pdf)