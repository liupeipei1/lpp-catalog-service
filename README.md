# lpp-catalog-service
1.用于统一管理每一个项目的jar的版本，方便控制版本升级
参考文档：https://docs.gradle.org/current/userguide/platforms.html
https://docs.gradle.org/current/userguide/platforms.html#sub:conventional-dependencies-toml

**方法1：用原始的setting.gradle文件定义catalog**

**step1: 保证gradle版本是7.4以及以上版本才支持catalog   gradle.property文件**
distributionUrl=https\://services.gradle.org/distributions/gradle-7.4-bin.zip

**step2: setting.gradle**
//别名必须由一系列标识符组成，这些标识符由短划线（推荐）、下划线 （） 或点 （） 分隔。标识符本身必须由 ascii 字符（最好是小写）组成，后跟数字。-_.

_dependencyResolutionManagement {
versionCatalogs {

`lpps {
    library('groovy.core', 'org.codehaus.groovy:groovy:3.0.5')
    library('groovy-json', 'org.codehaus.groovy:groovy-json:3.0.5')
    library('groovy-nio', 'org.codehaus.groovy:groovy-nio:3.0.5')
    library('commons-lang3', 'org.apache.commons', 'commons-lang3').version {
    strictly '[3.8, 4.0['  //这里是强制版本，但是prefer话会选择3.9
    prefer '3.9'
      }
 }`

`lpps2 {
          library('lombok', 'org.projectlombok:lombok:1.18.20')
          plugin('jmh', 'me.champeau.jmh').version('0.6.5')
      }`

`testLibs {
def junit5 = version('junit5', '5.7.1')
library('junit-api', 'org.junit.jupiter', 'junit-jupiter-api').version(junit5)
library('junit-engine', 'org.junit.jupiter', 'junit-jupiter-engine').version(junit5)
      }
   }`
}

**step3: gradle.property文件使用,其中前缀来自版本目录名称。lpps,lpps2 **


`plugins {
    id 'java-library'
    id 'checkstyle'
    alias(libs.plugins.jmh)
}
dependencies {
implementation(lpps.groovy.core)
implementation(lpps.commons.lang3)
implementation(lpps2.lombok)
implementation lpps2.bundles.groovy
}
`
**方法2：用原始的  libs.versions.tomllibs**
The TOML file consists of 4 major sections:

**the section is used to declare versions which can be referenced by dependencies[versions]**
version的使用方法：version(String alias, String version)
example1:  version('groovy', '3.0.5')
example2: libs.version.toml 中versions 定义 my-lib = { strictly = "[1.0, 2.0[", prefer = "1.2" }
example3  libs.version.toml 中versions 定义  groovy = "3.0.5"

**the section is used to declare the aliases to coordinates[libraries]**
example1: my-lib = "com.mycompany:mylib:1.4"
example2: my-lib1 = { group = "com.mycompany", name="mylib", version.ref="some" }
example3:  my-lib2 = { module = "com.mycompany:mylib",version.ref="some" }

the section is used to declare dependency bundles[bundles]
example:my-lib-bundle= ['my-lib','my-lib1','my-lib2']

**the section is used to declare plugins[plugins]  方法 plugin(String alias, String id)**
short-notation = "some.plugin.id:1.4"
long-notation = { id = "some.plugin.id", version = "1.4" }

**step1: 创建libs.versions.toml文件在gradle文件夹下**
settings.gradle 目前可以支持多个toml
`dependencyResolutionManagement {
    versionCatalogs {
        libs {
          from(files("../gradle/libs.versions.toml"))
        }
    }
}`

or 在build. gradle文件加上去添加catalog
`plugins {
    id 'java'
    id 'version-catalog'
}`
在build. gradle文件 添加文件引入
`catalog{
    versionCatalog{
        libs {
        from(files("../gradle/libs.versions.toml"))
        }
    }
}`






