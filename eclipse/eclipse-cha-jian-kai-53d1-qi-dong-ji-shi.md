# eclipse 插件开发--启动计时
> eclipse 插件开发入门还算是比较简单的, 但是想要开发出比较实用的插件, 还是需要些时间去研究eclipse 插件相关的API的. 笔者的入门程序为记录eclipse 的启动时间.


## 1. eclipse 插件项目开发

### 1.1 新建项目: File -> New -> Plugin-in Project

![](/assets/eclpse_2017-07-19_084822.png)

### 1.2 输入ID 和 供应商名称Vendor

![](/assets/eclipse_2017-07-19_084939.png)

### 1.3 选择模板, 笔者选择自定义

![](/assets/eclipse_2017-07-19_085051.png)

### 1.4 选择模板, 笔者不选择任何模板

![](/assets/eclipse_2017-07-19_085123.png)

### 1.5 添加Console 包
* 点击MANIFEST.MF文件, 选择Dependencies 选项卡, 点击Required Plug-ins 面板中的Add, 输入Console, 选择or.eclipse.ui.console

![](/assets/eclipse_2017-07-19_085408.png)


## 2. 相关文件:

### 2.1 计时类,需实现IStartup 接口

```java
package org.zgf.eclipse.startup;

import java.util.Map;
import java.util.Map.Entry;
import java.util.Set;

import org.eclipse.swt.widgets.Display;
import org.eclipse.ui.IStartup;
import org.zgf.eclipse.startup.util.ConsoleUtil;

/**
 * 显示Eclipse 启动时间
 * @ClassName: ShowTime.java
 * @author zonggf
 * @date 2017年7月18日-下午4:04:03
 */
public class ShowTime implements IStartup{
	
	@Override
	public void earlyStartup() {
		
		Display.getDefault().syncExec(new Runnable() {
			
			@Override
			public void run() {
				
				long start = Long.parseLong(System.getProperty("eclipse.startTime"));
				long cost = System.currentTimeMillis() - start;
				
				String tips = "Eclipse started cost: " + cost + " ms";
				
				ConsoleUtil.printLog(tips);
				
				//MessageDialog.openInformation(Display.getDefault().getActiveShell(), "Eclipse", tips);
			}
		});
		
	}
	
	public static void main(String[] args) {
		
		Map<String, String> env = System.getenv();
		Set<Entry<String, String>> entrySet = env.entrySet();
		for (Entry<String, String> entry : entrySet) {
			System.out.println(entry.getKey() + " -- " + entry.getValue());
		}
		
		
	}

}
```

### 2.2 工具类, 向Console 输出信息

```java
package org.zgf.eclipse.startup.util;

import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;

import org.eclipse.ui.console.ConsolePlugin;
import org.eclipse.ui.console.IConsole;
import org.eclipse.ui.console.IConsoleManager;
import org.eclipse.ui.console.MessageConsole;
import org.eclipse.ui.console.MessageConsoleStream;

/**
 * 控制台输出启动时间
 * @ClassName: ConsoleUtil.java
 * @author zonggf
 * @date 2017年7月18日-下午4:31:39
 */
public class ConsoleUtil {
	
	
	/**
	 * @Description 获取控制台输出对象
	 * @return 控制台输出流
	 * @author zonggf
	 * @date 2017年7月18日-下午4:27:14
	 */
    private static MessageConsoleStream getMsgConsoleStream() {  
    	IConsoleManager consoleManager = ConsolePlugin.getDefault().getConsoleManager();
    	
    	MessageConsole messageConsole;
    	
        IConsole[] consoles = consoleManager.getConsoles();  
        if(consoles.length > 0){  
        	messageConsole = (MessageConsole)consoles[0];  
        } else{  
        	messageConsole = new MessageConsole("Console", null);  
            consoleManager.addConsoles(new IConsole[] { messageConsole });  
        }  
        consoleManager.showConsoleView(messageConsole);
        
        return messageConsole.newMessageStream();
    }  
    
    /**
     * @Description 向控制台输出一行字符串
     * @param line 字符串
     * @author zonggf
     * @date 2017年7月18日-下午4:27:43
     */
    public static void print(String line){
    	MessageConsoleStream mcs = getMsgConsoleStream(); 
    	mcs.print(line);
    }
    
    /**
     * @Description 向控制台输出日志
     * @param message 日志信息
     * @author zonggf
     * @date 2017年7月18日-下午4:35:49
     */
    public static void printLog(String line){
    	MessageConsoleStream mcs = getMsgConsoleStream(); 

    	SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    	String timeStr = sdf.format(new Date());
    	
    	mcs.print("[" + timeStr + "] " + line);
    }
    
    /**
     * @Description 关闭控制台输出流
     * @param mcs
     * @author zonggf
     * @date 2017年7月18日-下午4:28:03
     */
    public static void close(MessageConsoleStream mcs){
    	try {
			mcs.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
    }

}

```

### 2.3 配置文件: plugins.xml, 需新建, 根路径下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?eclipse version="3.4"?>
<plugin>

   <extension
         point="org.eclipse.ui.startup">
         <startup class="org.zgf.eclipse.startup.ShowTime" />
   </extension>

</plugin>

```

### 2.4 MAINIFEST.MF
```
Manifest-Version: 1.0
Bundle-ManifestVersion: 2
Bundle-Name: Timer
Bundle-SymbolicName: org.zgf.eclipse.startup;singleton:=true
Bundle-Version: 1.0.0.qualifier
Bundle-Activator: org.zgf.eclipse.startup.Activator
Bundle-Vendor: zongf
Require-Bundle: org.eclipse.ui,
 org.eclipse.core.runtime,
 org.eclipse.ui.console;bundle-version="3.5.300"
Bundle-RequiredExecutionEnvironment: JavaSE-1.7
Bundle-ActivationPolicy: lazy
```


## 3. 测试:
* 点击MAINIFEST.MF overview 选项卡, 点击右测Launch an Eclipse application

![](/assets/eclipse_2017-07-19_085503.png)

## 4. 打包为插件jar包
### 4.1  点击MAINIFEST.MF overview 选项卡, 点击右上角按钮

![](/assets/eclipse_2017-07-20_100637.png)

### 4.2 选择目录
![](/assets/eclipse_2017-07-20_100908.png)

## 4.3 选择使用本地已经编译好的class 打包
![](/assets/eclipse_2017-07-20_100943.png)

## 4.4 将生成的jar包拷贝到eclipse的plugins 目录,重启即可
* jar包在你选择的目录下的plugins 目录下

## 4.5 重启eclipse
* 会发现控制台有以下输出

```
[2017-07-20 10:13:40] Eclipse started cost: 25730 ms
```






