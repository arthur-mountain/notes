# Gradle config 介紹

## 1. **`plugins` 套件配置**

```groovy
plugins {
    id 'java'
    id 'application'
}
```

- **用途**：指定項目使用的套件，以擴展 Gradle 的功能。 例如:

  - `java` 套件添加了構建 Java 項目所需的任務，

  - `application` 套件允許你運行可執行的 Java 應用程序。

## 2. **`group` 和 `version` 項目屬性**

```groovy
group = 'com.example'
version = '1.0.0'
```

- **用途**：定義項目的組和版本號，這在發布庫或組件時非常有用，可以唯一標識項目。

## 3. **`repositories` 庫倉庫配置**

```groovy
repositories {
    mavenCentral()
    google()
    jcenter()
}
```

- **用途**：指定項目從哪裡獲取依賴庫。常用的庫倉庫包括 `mavenCentral()`、`jcenter()` 和 `google()`。

## 4. **`dependencies` 依賴配置**

```groovy
dependencies {
    implementation 'com.google.guava:guava:31.0.1-jre'
    testImplementation 'junit:junit:4.13.2'
}
```

- **用途**：定義項目所需的依賴庫。依賴可以分為不同的配置，如：

  - **`implementation`**：運行時所需的依賴，但不暴露給其他項目。

  - **`api`**：運行時所需的依賴，並暴露給其他項目。

  - **`testImplementation`**：僅在測試時使用的依賴。

## 5. **`application` 套件配置**

```groovy
application {
    mainClassName = 'com.example.Main'
}
```

- **用途**：指定應用程序的入口點，即主類。這在使用 `application` 套件時需要，以便運行應用程序。

## 6. **`sourceCompatibility` 和 `targetCompatibility`**

```groovy
sourceCompatibility = JavaVersion.VERSION_11
targetCompatibility = JavaVersion.VERSION_11
```

- **用途**：指定 Java 源碼和字節碼的版本，確保程式與特定的 Java 版本兼容。

## 7. **`tasks` 任務定義**

```groovy
task hello {
    doLast {
        println 'Hello, Gradle!'
    }
}
```

- **用途**：自定義任務，執行特定的構建操作。`doLast` 塊中的程式會在任務執行時運行。

## 8. **`configurations` 配置**

```groovy
configurations {
    customConfig
}

dependencies {
    customConfig 'org.example:custom-lib:1.0.0'
}
```

- **用途**：創建自定義的依賴配置，可在特定任務中使用。

## 9. **`buildscript` 配置**

```groovy
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:7.0.2'
    }
}
```

- **用途**：為構建腳本本身添加依賴和庫倉庫配置，通常用於添加第三方套件。

## 10. **`ext` 擴展屬性**

```groovy
ext {
    springVersion = '5.3.9'
}

dependencies {
    implementation "org.springframework:spring-core:${springVersion}"
}
```

- **用途**：定義全局變量，方便在構建腳本中重用，減少硬編碼的版本號。

## 11. **`allprojects` 和 `subprojects`**

```groovy
allprojects {
    repositories {
        mavenCentral()
    }
}

subprojects {
    apply plugin: 'java'
}
```

- **用途**：在多模組項目中，`allprojects` 和 `subprojects` 塊可用於統一配置所有子項目的屬性和套件。

## 12. **`java` 套件配置**

```groovy
java {
    sourceSets {
        main {
            java {
                srcDirs = ['src/main/java']
            }
            resources {
                srcDirs = ['src/main/resources']
            }
        }
    }
}
```

- **用途**：自定義 Java 套件的源碼和資源目錄結構。

## 13. **`publishing` 發佈配置**

```groovy
publishing {
    publications {
        mavenJava(MavenPublication) {
            from components.java
            groupId = 'com.example'
            artifactId = 'my-library'
            version = '1.0.0'
        }
    }
    repositories {
        maven {
            url = uri('https://repo.example.com/maven2')
        }
    }
}
```

- **用途**：配置項目的發佈，包括如何打包和將庫發佈到遠程庫倉庫。

## 14. **`test` 測試配置**

```groovy
test {
    useJUnitPlatform()
    testLogging {
        events "passed", "skipped", "failed"
    }
}
```

- **用途**：配置測試任務的行為，如使用的測試框架和日誌輸出級別。

## 15. **`wrapper` 配置**

```groovy
wrapper {
    gradleVersion = '7.2'
    distributionType = Wrapper.DistributionType.ALL
}
```

- **用途**：配置 Gradle Wrapper，確保團隊中的所有人都使用相同的 Gradle 版本。

## 16. **`repositories` 的變體**

- **本地庫倉庫**：

  ```groovy
  repositories {
      maven {
          url uri('file://local-repo')
      }
  }
  ```

- **用途**：指定本地庫倉庫位置，從本地文件系統獲取依賴。

## 17. **`sourceSets` 自定義**

```groovy
sourceSets {
    integrationTest {
        java {
            srcDir 'src/integrationTest/java'
        }
        resources {
            srcDir 'src/integrationTest/resources'
        }
        compileClasspath += sourceSets.main.output + configurations.testRuntimeClasspath
        runtimeClasspath += output + compileClasspath
    }
}
```

- **用途**：創建自定義的源碼集(如集成測試)，以組織不同類型的程式。

## 18. **`configurations` 的屬性**

```groovy
configurations {
    implementation {
        exclude group: 'org.unwanted', module: 'unwanted-lib'
    }
}
```

- **用途**：在依賴配置中排除特定的庫，避免依賴衝突或不需要的庫。

## 19. **`jacoco` 測試覆蓋率配置**

```groovy
plugins {
    id 'jacoco'
}

jacocoTestReport {
    reports {
        xml.enabled true
        html.enabled true
    }
}
```

- **用途**：使用 JaCoCo 套件生成測試覆蓋率報告。

## 20. **`checkstyle` 程式規範檢查**

```groovy
plugins {
    id 'checkstyle'
}

checkstyle {
    configFile = file('config/checkstyle/checkstyle.xml')
}

tasks.withType(Checkstyle) {
    reports {
        xml.enabled false
        html.enabled true
    }
}
```

- **用途**：配置 Checkstyle 套件，檢查程式是否符合指定的程式規範。

## **總結**

`build.gradle` 文件提供了一個靈活的方式來定義項目的構建過程。通過了解各個配置項的用途：

- **管理依賴**：添加、排除和配置項目所需的庫。

- **定制構建邏輯**：創建自定義任務和源碼集，以滿足特定的構建需求。

- **配置套件**：使用各種套件來擴展 Gradle 的功能，如測試、覆蓋率報告和程式質量檢查。

- **統一項目設置**：在多模組項目中，共享配置和依賴，簡化管理。

## **參考資料**

- [Gradle docs](https://docs.gradle.org/current/userguide/userguide.html)

- [Gradle DSL](https://docs.gradle.org/current/dsl/index.html)

- [Gradle plugins](https://plugins.gradle.org/)
