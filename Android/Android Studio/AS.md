# ABI打包

根据不同CPU架构打包APK

```groovy
import com.android.build.OutputFile
android {
	...
    //ABI&NDK打包
    splits {
        abi {
            enable true
            reset()
            include 'armeabi-v7a','x86'
            universalApk false	//是否打包含全部架构APK
        }
    }
    android.applicationVariants.all { variant ->
        variant.outputs.all { output ->
            def time = releaseTime()
            def abiName = output.getFilter(OutputFile.ABI)
            outputFileName = "${variant.name}-${abiName}-${name_version}-${time}.apk"
        }
    }
}

//生成时间
def releaseTime() {
    return new Date().format("yyyy-MM-dd-HH-mm")
}
```

