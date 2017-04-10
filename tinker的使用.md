##1. 依赖引入
dependencies {

    //可选，用于生成application类
    provided('com.tencent.tinker:tinker-android-anno:1.7.7')
    //tinker的核心库
    compile('com.tencent.tinker:tinker-android-lib:1.7.7')
}
##2. tinker 推荐application写法
	@DefaultLifeCycle(application = ".SimpleTinkerInApplication",
        flags = ShareConstants.TINKER_ENABLE_ALL,
        loadVerifyFlag = false)
	public class SimpleTinkerInApplicationLike extends ApplicationLike {
	    public SimpleTinkerInApplicationLike(Application application, int tinkerFlags, boolean tinkerLoadVerifyFlag, long applicationStartElapsedTime, long applicationStartMillisTime, Intent tinkerResultIntent) {
	        super(application, tinkerFlags, tinkerLoadVerifyFlag, applicationStartElapsedTime, applicationStartMillisTime, tinkerResultIntent);
	    }
	
	    @Override
	    public void onBaseContextAttached(Context base) {
	        super.onBaseContextAttached(base);
	    }
	
	    @Override
	    public void onCreate() {
	        super.onCreate();
	        TinkerInstaller.install(this);
	    }
    }
##3.修改AndroidManifest
	3.1 权限
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	3.2 修改application
	<application
        android:name=".SimpleTinkerInApplication"
        .../>
	3.3 添加tinker_id方便加载对应的版本
	<application>
  		<meta-data
            android:name="TINKER_ID"
            android:value="tinker_id_6235657" />
    </application>
注意修改过后会报错，需要重新构建一下

##4.通过命令生成差异文件
	java -jar tinker-patch-cli-1.7.7.jar -old old.apk -new new.apk -config tinker_config.xml -out output

    修改tinker_config.xml中的还有签名密码
	<loader value="com.zhy.tinkersimplein.SimpleTinkerInApplication"/>
	差异包导入到手中
	adb push ./app/build/outputs/tinkerPatch/debug/patch_signed_7zip.apk /storage/sdcard0/
##5. 添加mapping.txt文件
在第一次打出apk的时候，保留下生成的mapping文件，在/build/outputs/mapping/release/mapping.txt。

可以copy到与proguard-rules.pro同目录，同时在第二次打修复包的时候，在proguard-rules.pro中添加上：

	-applymapping mapping.txt

保证后续的打包与线上包使用的是同一个mapping文件。

##6. 添加tinker包的混淆
	-keepattributes *Annotation*
	-dontwarn com.tencent.tinker.anno.AnnotationProcessor
	-keep @com.tencent.tinker.anno.DefaultLifeCycle public class *
	-keep public class * extends android.app.Application {
	    *;
	}
	
	-keep public class com.tencent.tinker.loader.app.ApplicationLifeCycle {
	    *;
	}
	-keep public class * implements com.tencent.tinker.loader.app.ApplicationLifeCycle {
	    *;
	}
	
	-keep public class com.tencent.tinker.loader.TinkerLoader {
	    *;
	}
	-keep public class * extends com.tencent.tinker.loader.TinkerLoader {
	    *;
	}
	
	-keep public class com.tencent.tinker.loader.TinkerTestDexLoad {
	    *;
	}
	
	#your dex.loader pattern here
	-keep class com.tencent.tinker.loader.**
	#
	-keep class 包名.SimpleTinkerInApplication
   

