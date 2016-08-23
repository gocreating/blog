title: 撰寫Android Google Map應用程式
date: 2015-03-28 20:13:19
categories:
  - Android
  - Development
---

首先我必須說明，這篇文章肯定是有時效性的，因為Android和Google的各種API服務都會經常性地更新，所以本篇教學能適用到何時，我也無從得知。這學期修了Android Programming的課程，Project必須使用到Google Map和雲端服務，Map的使用看似容易，實際上開發者們很容易落入各種陷阱中，因此本文將針對Map應用程式，一步步帶您建立一個簡單應用

<!--more-->

PS. 文中使用的環境是Android Studio

# 設定Android Studio

### 建立空白專案

先簡單建立一個Google Map應用程式的空殼專案

### 安裝Google Play Services SDK

打開Android SDK Manager，安裝`Extra/Google Play services`

![](Install-Google-Play-Service.png)

### 編輯Gradle

先將模式切換到`Android`，打開`build.gradle`，切記要選擇`Module`那一個，不是`Project`那一個

![](Edit-Gradle.png)

在dependencies的區塊新增一條Rule

```
apply plugin: 'com.android.application'
...

dependencies {
    compile 'com.android.support:appcompat-v7:21.0.3'
    compile 'com.google.android.gms:play-services:7.0.0' // 新增此行
}
```

存檔後重新同步Gradle

![](Sync.png)

### 編輯Manifest

打開`AndroidManifest.xml`，在`<application>`標籤內插入子元素:

```
<meta-data android:name="com.google.android.gms.version"
           android:value="@integer/google_play_services_version" />
```

存檔後就可以開始使用Google Play Service的API囉!

# 申請Google Maps Android API v2金鑰

打開cmd，進入Java執行檔目錄（ex: `C:\Program Files\Java\jdk1.8.0_40\bin`），接下來的步驟是要取得以SHA1編碼的Certificate，但是開發用和發行用的Certificate會不同，因此下方分開討論

### Debug版/開發版Certificate

請將下面指令中的[使用者名稱]改成自己的

`keytool -list -v -keystore "C:\Users\[使用者名稱]\.android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

![](SHA1-debug.png)

### Release版/發行版Certificate

Android Studio工具列->`Build`->`Generate Signed APK...`

填寫完密碼等資料後，記住產生的`keystore.ejs`路徑，將以下指令中的[keystore路徑]置換成自己產生金鑰時填寫的路徑

`keytool -list -v -keystore [keystore路徑]`

![](key1.png)

> 如果keystore.jks檔和keytool程式在不同磁碟下，這項操作會失敗，所以務必先把jks檔複製到同一個磁碟下!

無論是開發版或是發行版的作法，執行指令的結果都會產生`SHA1`碼，請將它複製起來

### 申請API金鑰

登入[Google Developers Console](https://console.developers.google.com/project)->`建立專案`->填寫專案名稱->`建立`

![](New-Project.png)

->`啟用API`->`Google Maps Android API`->`啟用API`

![](Enable-API.png)

![](Google-Maps-Android-API.png)

左方導覽列`憑證`->`建立新的金鑰`->`Android金鑰`

![](New-Key.png)

然後把剛才複製起來的SHA1碼貼上，後面接上分號`;`和`package name`->`建立`

![](Android-Key.png)

順利取得API金鑰

![](API-Key.png)

如果你同時把開發版和發行版的Certificates都拿來申請，那麼應該會像我一樣同時持有兩組API Key，一組開發用，一組發行用:

![](API-Keys.jpg)

# 撰寫應用程式

### AndroidManifest.xml

#### 設定API Key

在`<application>`標籤內新增子元素，`android:value`的值請套用自己的API Key，這邊設定的Key值會攸關程式運作正常與否，如果你是想要接上usb線來進行開發，請填上開發版的API Key；如果是要發行apk檔案，請使用發行用的API Key

```
<meta-data android:name="com.google.android.maps.v2.API_KEY"
           android:value="(your API_key)" />
```

#### 設定權限

在`<manifest>`標籤內，`<application>`標籤的上方新增權限的設定:

```
<uses-feature android:glEsVersion="0x00020000" android:required="true"/>
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="com.google.android.providers.gsf.permission.READ_SERVICES"/>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
```

### activity_main.xml

使用Fragment元件作為Map的容器

```
<?xml version="1.0" encoding="utf-8"?>
<fragment xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/map"
    android:layout_width="fill_parent"
    android:layout_height="match_parent"
    class="com.google.android.gms.maps.MapFragment" />
```

### MainActivity.java

```
public class MainActivity extends ActionBarActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // get google map object
        GoogleMap gmap = ((MapFragment)getFragmentManager().
                          findFragmentById(R.id.map)).getMap();
        LatLng taipei101 = new LatLng(25.033611, 121.565000);
        float zoom = 17;
        gmap.animateCamera(CameraUpdateFactory.newLatLngZoom(taipei101, zoom));
    }
}
```

### 實機執行

執行時記得打開Wifi或行動網路，執行結果如下圖

![](Run.png?middle)

# 結論

細節太多了，申請流程也是機巴的要死，再加上Android Studio低落的Rerun效率，要跑出來真的只能自求多福...

# 參考資料

- [Setting Up Google Play Services](http://developer.android.com/google/play-services/setup.html)
- 蔡神的[Google Maps Android API v2 開發金鑰](https://docs.google.com/document/d/1mVcH_NAXBUtWQzjY9sglivVZMZxvUxUi08_pLEwFyYs/edit?usp=sharing)
- 陳神的[建立 Goole Maps](https://hackpad.com/-Goole-Maps-58B5cSG1aWJ)