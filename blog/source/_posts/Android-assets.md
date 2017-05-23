---
title: Android assets
date: 2017-05-20 17:49:39
tags:
---

Android 中资源分为两种,一种是res下可编译的资源文件, 这种资源文件系统会在R.Java里面自动生成该资源文件的ID，访问也很简单,只需要调用R.XXX.id即可;第二种就是放在assets文件夹下面的原生资源文件,放在这个文件夹下面的文件不会被R文件编译,所以不能像第一种那样直接使用.Android提供了一个工具类,方便我们操作获取assets文件下的文件:AssetManager

```java
String[] list(String path);//列出该目录下的下级文件和文件夹名称  
InputStream open(String fileName);//以顺序读取模式打开文件，默认模式为ACCESS_STREAMING  
  
InputStream open(String fileName, int accessMode);
//以指定模式打开文件。读取模式有以下几种：  
ACCESS_UNKNOWN : 未指定具体的读取模式  
ACCESS_RANDOM : 随机读取  
ACCESS_STREAMING : 顺序读取  
ACCESS_BUFFER : 缓存读取  
void close()//关闭AssetManager实例  
```

## 使用

### 1.读取txt文件


```java
/** 
 * 从assets中读取txt 
 */  
private void readFromAssets() {  
    try {  
        InputStream is = getAssets().open("qq.txt");  
        String text = readTextFromSDcard(is);  
        textView.setText(text);  
    } catch (Exception e) {  
        // TODO Auto-generated catch block  
        e.printStackTrace();  
    }  
}
```
```java
/** 
 * 从raw中读取txt 
 */  
private void readFromRaw() {  
    try {  
        InputStream is = getResources().openRawResource(R.raw.qq);  
        String text = readTextFromSDcard(is);  
        textView.setText(text);  
    } catch (Exception e) {  
        // TODO Auto-generated catch block  
        e.printStackTrace();  
    }  
}
```
```java
/** 
 * 按行读取txt 
 *  
 * @param is 
 * @return 
 * @throws Exception 
 */  
 
 ## 按行读取txt
private String readTextFromSDcard(InputStream is) throws Exception {  
    InputStreamReader reader = new InputStreamReader(is);  
    BufferedReader bufferedReader = new BufferedReader(reader);  
    StringBuffer buffer = new StringBuffer("");  
    String str;  
    while ((str = bufferedReader.readLine()) != null) {  
        buffer.append(str);  
        buffer.append("\n");  
    }  
    return buffer.toString();  
} 
```
### 2.读取html文件

`webView.loadUrl("file:///android_asset/html/index.htmll"); ` 

### 3.加载assets目录下的图片资源

```java
InputStream is = getAssets().open(fileName);    
bitmap = BitmapFactory.decodeStream(is);   
ivImg.setImageBitmap(bitmap);  
```
### 4.加载assets目录下音乐
```java
// 打开指定音乐文件,获取assets目录下指定文件的AssetFileDescriptor对象    
AssetFileDescriptor afd = am.openFd(music);    
mPlayer.reset();    
// 使用MediaPlayer加载指定的声音文件。    
mPlayer.setDataSource(afd.getFileDescriptor(),    
    afd.getStartOffset(), afd.getLength());    
// 准备声音    
mPlayer.prepare();    
// 播放    
mPlayer.start();  
```
> Android中还有另外一个文件夹raw,和assets差不多,也不会被R文件编译,但是raw下不能在建文件夹,assets文件下是可以在建文件夹的,下面是获取raw文件夹下资源的方法:

`InputStream is = getResources().openRawResource(R.id.filename);  `

>assets下的文件复制到SD卡中

``` java
/**  
 *  从assets目录中复制整个文件夹内容  
 *  @param  context  Context 使用CopyFiles类的Activity 
 *  @param  oldPath  String  原文件路径  如：/aa  
 *  @param  newPath  String  复制后路径  如：xx:/bb/cc  
 */   
public void copyFilesFassets(Context context,String oldPath,String newPath) {                      
         try {  
        String fileNames[] = context.getAssets().list(oldPath);//获取assets目录下的所有文件及目录名  
        if (fileNames.length > 0) {//如果是目录  
            File file = new File(newPath);  
            file.mkdirs();//如果文件夹不存在，则递归  
            for (String fileName : fileNames) {  
               copyFilesFassets(context,oldPath + "/" + fileName,newPath+"/"+fileName);  
            }  
        } else {//如果是文件  
            InputStream is = context.getAssets().open(oldPath);  
            FileOutputStream fos = new FileOutputStream(new File(newPath));  
            byte[] buffer = new byte[1024];  
            int byteCount=0;                 
            while((byteCount=is.read(buffer))!=-1) {//循环从输入流读取 buffer字节          
                fos.write(buffer, 0, byteCount);//将读取的输入流写入到输出流  
            }  
            fos.flush();//刷新缓冲区  
            is.close();  
            fos.close();  
        }  
    } catch (Exception e) {  
        // TODO Auto-generated catch block  
        e.printStackTrace();  
        //如果捕捉到错误则通知UI线程  
                   MainActivity.handler.sendEmptyMessage(COPY_FALSE);  
    }                             
}  
```


