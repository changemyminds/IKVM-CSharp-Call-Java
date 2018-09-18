# IKVM C# Call Java Class.

## 前言：
IKVM.NET開源碼是基于.NET CLR的Java虛擬機。
基于.NET的Java虛擬機意味著我們可以讓Java程式跑在.NET上，可以通過虛擬機這個中介讓Java程式與.NET應用程式一起協同工作。

這篇文章主要是講解C# Call Java的所有流程(包含Jar的產生)。

## 基本觀念：
- Visaul Studio IDE、C#的使用。
- IntelliJ IDEA、Java的使用。
- Windows cmd的操作。

## 建置環境：
Windows 10 (64bit)

### 下載檔案及安裝
C# IDE： [Visaul Studio 2013](https://docs.microsoft.com/zh-tw/visualstudio/productinfo/vs2013-sysrequirements-vs)<br>
IKVM  ： [ikvmbin-8.1.5717.0](http://weblog.ikvm.net/2015/08/26/IKVMNET81ReleaseCandidate0.aspx)<br>

Java IDE   ： [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download/#section=windows)<br>
Java SDK 8 ： [jdk-8u181-windows-x64(64bit)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)<br>
Java jre 8 ： [jre-8u181-windows-x64(64bit)](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)<br>

## 設定環境變數：
### Java
#### JAVA_HOME的設定
步驟：電腦 -> (右鍵)內容 -> 進階系統設定 -> 進階 -> 環境變數 -> (查看)系統變數。<br>
確認是否有```JAVA_HOME```的系統變數，若沒有的話，請點選新增，並輸入下列內容：<br>
變數名稱: ```JAVA_HOME```<br>
變數值: ```C:\Program Files\Java\jdk1.8.0_181``` (注意這邊是你的Java JDK安裝路徑)<br>

#### Path的設定
同```JAVA_HOME```的步驟。 <br>
確認Path內是否有Java jdk的路徑，若沒有的話，請輸入下列內容：<br>
編輯環境變數 <br>
新增 ```C:\Program Files\Java\jdk1.8.0_181\bin``` <br>
除了上面的寫法，也可以寫成 ```%JAVA_HOME%\bin``` <br>

### IKVM
#### IKVM_HOME的設定
同```JAVA_HOME```的步驟。 <br>
確認是否有```IKVM_HOME```的系統變數，若沒有的話，請點選新增，並輸入下列內容：<br>
變數名稱: ```IKVM_HOME```<br>
變數值: ```D:\ikvm\ikvm-8.1.5717.0``` (注意這邊是你IKVM的下載路徑)<br>

#### Path的設定
同```JAVA_HOME```的步驟。 <br>
確認Path內是否有IKVM的路徑，若沒有的話，請輸入下列內容：<br>
編輯環境變數 <br>
新增 ```%IKVM_HOME%\bin``` <br>
新增 ```%IKVM_HOME%\bin\IKVM.OpenJDK.Core.dll``` <br>
新增 ```%IKVM_HOME%\bin\IKVM.Runtime.dll``` <br>
新增 ```%IKVM_HOME%\bin\IKVM.Runtime.JNI.dll``` <br>
新增四個環境變數。

## 開始建立：
### Java的部分
使用Intelij建立一個Java專案，並將我們要用的Java Class建置成Jar檔案，步驟如下： <br>
在Intelij下，創建ForCSharpSample專案 <br>
在src內創建```JavaHelloWorld.class```並且建立一個方法```public String getHelloWorld``` <br>
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/java0.PNG)

接著要來設定Jar的部分 <br>
- File -> Project Structure -> Artifacts -> (點選+的符號) -> JAR -> From modules with dependencies... <br>
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/java1.PNG)

Create JAR from Modules -> (圈選)copy to the output directory and link via manifest -> click OK <br>
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/java2.PNG)

勾選 Show content of elements -> click OK <br>
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/java3.PNG)

上方的狀態列 -> Build -> Build Artifacts... -> Build Artifact -> ForCSharpSample.jar -> Build <br>
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/java4.PNG)
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/java5.PNG)

確認Jar是否產生成功。 <br>
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/java6.PNG)	

### C#的部分
使用IKVM將Java Jar檔案編譯成dll檔案，並在Visaul Studio使用C#去呼叫，步驟如下： <br>
將ForCSharpSample.jar檔案複製到你想要的目錄底下，例如```D:\Sample\ForCSharpSample.jar``` <br>
使用命令```ikvmc -out:JavaHelloWorld.dll ForCSharpSample.jar``` <br>
上述命令會將```ForCSharpSample.jar```輸出成```JavaHelloWorld.dll```，並確認dll檔案是否有建立。 <br>
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/ikvm0.PNG) <br>

在Visual Studio底下，創建IKVMSample專案 <br>
並在專案目錄下，創建DLLs資料夾 <br>
將需要用到的檔案，```JavaHelloWorld.dll```、```IKVM.OpenJDK.Core.dll```複製到DLLs資料夾底下，並加入到參考中。<br>
補充：根據你所需要使用到的Java類別，來添加```IKVM.XXXX.dll``` (IKVM的dll檔案位於下載的```ikvm-8.1.5717.0\bin```底下)<br>
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/CSharp0.PNG) <br>

確認加入參考是否正確。<br>
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/CSharp1.PNG) <br>

撰寫C#程式碼，並執行。<br>
![image](https://github.com/changemyminds/IKVM_CSharp_Call_Java/blob/master/picture/CSharp2.PNG) <br>

## 參考資料
- [IKVM.NET介紹](https://www.itsfun.com.tw/IKVM.NET/wiki-0737404-5694583)
- [IKVM官網](https://www.ikvm.net/)
























