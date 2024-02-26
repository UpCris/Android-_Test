## 普通型格式  `adb + command`	
<small>一般只能在电脑上运行</small>  
### 1.`adb devices` 显示已连接设备  
- 只有一台设备连接时，可直接执行命令  
- 超过一台设备时必须使用`adb -s + 设备序列号`的方式来指定设备,  
  否则会提示`more than one device/emulator`  

### 2. `adb install` 安装app  
- 若需要安装的apk在adb目录下, 可直接输入文件名安装  
(例如`adb install test.apk`)  
- 若不在同一个目录下，可直接将apk拖到命令行里，自动填充绝对路径  
(例如`adb install C:\Users\Upcris\Desktop\test.apk`)  
- 若路径中含有空格，请给路径添加英文引导  
(例如`adb install C:\Users\Upcris\Desktop\test files\test.apk`会把空格前面的路径作为apk的路径) 
#### options:
`adb install + options + apk path`  

`-r` 替换已存在的应用程序，强制安装  
`-d` 允许降级安装  
`-t` 允许安装debug版测试包  
`-l` 锁定应用程序  
`-s` 将应用程序安装到sd卡上  
`-g` 安装后自动授予所有权限  

### 3. `adb uninstall + package` 卸载app  
*所谓包名才是app真正的名字*  

#### options:
`adb uninstall + options + package`   
- `-k` 保留数据卸载  

#### 4. `adb push/pull` 推送/接收文件  
*前者传文件到手机，后者手机端文件传到电脑*  
- `adb push/pull + current path + target path`  
*若文件名含有中文，需在手机端路径中补全中文，否则推送后会丢失后缀名*  

### 5. `adb reboot` 重启到指定模式     
- `adb reboot` 重启手机  
- `adb reboot recovery` 重启到恢复模式  
- `adb reboot bootloader` 重启到fastboot模式  
*与VAB分区无关, 全部可重启到fastboot模式, 通常刷boot、super等*  
- `adb reboot fastboot` 重启到fastbootd模式(VAB分区机型)  
*对于VAB分区机型, 可重启到fastboot模式, 此模式下可刷Super小分区: 如system、vendor等*  
- `adb reboot edl` 重启到9008串口模式  
- `adb reboot download` 重启到挖煤模式  
- `adb reboot -p` 关机  
  
## shell型格式  `adb shell + command`   
*可在电脑(adb shell + command)或手机(adb shell + command)运行*  
*但手机运行需ROOT权限或第三方工具提取*  
*因此没有ROOT情况下，使用电脑执行可获得更高的权限*  
*通过调用手机中的shell apk实现通信，在设备的shell环境中执行命令*  

###  非交互式  
- `adb shell + command` 执行一条特定命令后退出`adb shell`  
###  交互式  
- `adb shell --> #` 进入交互式shell环境  

### 1. `adb shell am` 活动管理器am(Activity Manager)  
*Activity可理解为app的各个界面的名称, 类似Windows中的窗口, Android软件可通过Activity直接跳转到指定的界面
而无需经过启动界面*  

- `am start + package/activity` 启动activty  
(例如`am start com.Upcris.test/com.Upcris.test.MainActivity`)  
(如果活动名中含有包名, 包名也可使用点代替)  
(例如`am start com.Upcris.test/.MainActivity`)  

- `am startService + package/service` 启动service
- `am force-stop + package` 强制结束正在运行的软件
- `am broadcast -a + action` 发送broadcast 

### 2. `adb shell pm` 包管理器pm(Package Manager)
*包管理器命令pm可实现对系统软件/第三方软件高权限控制*
*例如禁用(冻结、停用)、卸载、清除数据、授予权限、获取路径等*

- `pm disable-user + package` 强制禁用软件(系统/第三方软件)
(禁用后的软件失去所有权限且无法启动、图标从桌面消失)
(如果想继续使用可以在应用管理界面点击使用)
- `pm path + package` 查找apk所在路径 
- `pm list package | grep + 包名中的部分` 获取所有apk中相关的包名
(例如 `adb shell pm list package | findstr android.apps`, 
其中`findstr`是Windows函数, 只用于非交互式shell;
若使用交互式shell使用grep)

- `pm clear + package` 清理应用数据  
- `pm dump + package > package.dump` (包含权限，版本号、更新时间来源等)  

### 3. `adb shell wm` 窗口管理器wm(Window Manager)

- `wm size + xPx(宽度像素) + 'x' + yPx(高度像素)` 修改分辨率
- `wm density + VALUE` 修改DPI, dp = 160*px/dpi
(例如, 一部手机的分辨率是1080x2340, 此时设定DPI=400, 则最小宽度需要填写432)

### 4. `adb shell input` 可模拟各种触摸 机身按键等 适合自动化

- `input tap x y` 模拟点击屏(指针位置来确定当前触摸坐标)
- `input swipe x1 y1 x2 y2 d` 模拟滑动屏幕(x1y1为起始点坐标 x2y2为终点坐标 d为滑动时长ms)
- `input keyevent + KEYVALUE` 模拟按键(例如26锁屏)
- `input text + WORDS` 输入文字(不能发送中文字符或全角字符 只能发送英文 数字和半角)

### 5. `adb shell logcat` 显示设备上的实时日志
#### options:
- `logcat > C:\Users\Upcris\Desktop\log.log` 保存日志
- `logcat -s + TAG` 仅显示包含指定标签的日志
- `logcat -d` 显示设别上的最近系统日志, 并立即退出
- `logcat -e + TEXT` 仅显示包含指定文本的日志条目
- `logcat -f + FILE` 将日志输出到指定文件中
- `logcat -v + FORMAT` 指定输出格式
- `logcat -t + NUM` 显示最近的指定数量的日志条目
- `logcat -c` 清楚日志缓冲区
