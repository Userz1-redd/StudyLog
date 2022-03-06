# KotlinDSL

기본적으로 AndroidStudio에서는 Gradle이 Groovy로 구성되어 있습니다. 이것을 Kotlin DSL으로 변경할 수 있습니다.

Gradle Groovy DSL의 기본 확장자는 .gradle/ .build.gradle / settings.gradle이지만
Gradle Kotlin DSL의 기본 확장자는 뒤에 .kts가 추가로 붙는 형태입니다. ex) .gradle.kts

## 사용방법

New -> Directory를 통해 buildSrc라는 이름의 디렉토리를 생성해줍니다.

생성된 build.Src안에 build.gradle.kts를 생성합니다.

또한 생성된 build.Src안에 src/main/java 디렉토리를 생성하고 그 하위에 Dependencies.kt를 생성합니다.

<img width="363" alt="image" src="https://open.oss.navercorp.com/storage/user/900/files/2abec300-787e-11ec-9ed5-658cfc2f2872">

build.gradle.kts
```
plugins {
    `kotlin-dsl`
}
repositories {
    jcenter()
}
```

Dependencies.kt
```
object Apps {
    const val compileSdk = 29
    const val minSdk = 21
    const val targetSdk = 29
    const val versionCode = 1
    const val versionName = "1.0.0"
}
 
object Versions {
    const val gradle = "3.5.2"
    const val kotlin = "1.3.50"
    const val appcompat = "1.0.2"
 
    const val junit = "4.12"
}
 
object Libs {
    const val kotlin = "org.jetbrains.kotlin:kotlin-stdlib-jdk7:${Versions.kotlin}"
    const val appcompat = "androidx.appcompat:appcompat:${Versions.appcompat}"
}
 
object TestLibs {
    const val junit = "junit:junit:${Versions.junit}"
}

```

이후 settings.gradle 의 이름을 settings.gradle.kts로 이름을 변경합니다.

내부의 작은 따옴표를 큰 따옴표로 바꾸고 괄호를 추가해야 합니다.


다음으로 프로젝트 수준의 build.gradle을 변경합니다.

build.gradle -> build.gradle.kts로 변경합니다.
```
    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.2'
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
    task clean(type: Delete) {
      delete rootProject.buildDir
    }
    repositories {
        google()
        jcenter()
    }
    ==> 이렇게 변경
    
    dependencies {
      classpath("com.android.tools.build:gradle:${Versions.gradle}")
      classpath("org.jetbrains.kotlin:kotlin-gradle-plugin:${Versions.kotlin}")
    }
    
    
    tasks.register("clean",Delete::class){
      delete(rootProject.buildDir)
    }
    repositories {
        google()
        jcenter()
        maven { url = uri("https://jitpack.io") }
   }
    
    
    
 ```

마지막으로 앱수준 gradle을 변경합니다.

build.gradle -> build.gradle.kts로 변경합니다.

```
apply plugin: 'com.android.application'
 
apply plugin: 'kotlin-android'
 
apply plugin: 'kotlin-android-extensions'

android {
    compileSdkVersion 29
    buildToolsVersion "29.0.2"
    defaultConfig {
        applicationId "com.sean.kotlindsl"
        minSdkVersion 19
        targetSdkVersion 29
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation"org.jetbrains.kotlin:kotlin-stdlib-jdk7:$kotlin_version"
    implementation 'androidx.appcompat:appcompat:1.1.0'
    implementation 'androidx.core:core-ktx:1.1.0'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test.ext:junit:1.1.1'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
}


==> 변경


plugins {
    id("com.android.application")
    kotlin("android")
    kotlin("android.extensions")
    kotlin("kapt")
}


android {
    compileSdkVersion(Apps.compileSdk)
    defaultConfig {
        minSdkVersion(Apps.minSdk)
        targetSdkVersion(Apps.targetSdk)
        versionCode = Apps.versionCode
        versionName = Apps.versionName
        multiDexEnabled = true
        setProperty("archivesBaseName", "$applicationId-v$versionName($versionCode)")
        resConfigs("en")
        ndk {
            abiFilters("armeabi-v7a", "x86", "arm64-v8a", "x86_64")
        }
        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }
 
    buildTypes {
        getByName("debug") {
            isMinifyEnabled = false
            isDebuggable = true
            proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"), "proguard-rules.pro")
        }
        getByName("release") {
            isMinifyEnabled = false
            proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"), "proguard-rules.pro")
        }
    }
 
    dataBinding {
 
        isEnabled = true
 
    }
}


dependencies {
    implementation(fileTree(mapOf("dir" to "libs", "include" to listOf("*.jar"))))
    implementation(Libs.kotlin)
    implementation(Libs.appcompat)
    implementation(Libs.Permission.permission)
 
 
/* ------------------------------------------------------------------------------------------------ */
 사용자에 따라 추가적인 implementaion 추가 , Dependencies.kt에 작성 후 적용 
 
    implementation(Libs.Glide.glide)
    kapt(Libs.Glide.compiler)
    implementation(Libs.Glide.okhttp3Intergration)
 
    implementation(Libs.Retrofit.retrofit)
    implementation(Libs.Retrofit.converterGson)
    implementation(Libs.Retrofit.mock)
    implementation(Libs.Retrofit.rxjava2)
 
    implementation(Libs.OkHttp.loggingInterceptor)
 
    implementation(Libs.Firebase.core)
   implementation(Libs.Firebase.messaging)
 
/* ------------------------------------------------------------------------------------------------ */
 
    implementation(Libs.Jakewharton.Timber.common)
    testImplementation(TestLibs.junit)
    implementation ("androidx.core:core-ktx:1.1.0")
    implementation("androidx.constraintlayout:constraintlayout:1.1.3")
    implementation ("com.android.support:design:28.0.0")
    androidTestImplementation ("androidx.test.ext:junit:1.1.1")
    androidTestImplementation ("androidx.test.espresso:espresso-core:3.2.0")
}
```
위에서 작성했던 Dependencies.kt에 값을 사용하여 코드를 깔끔하게 작성할 수 있습니다. 
 
 
