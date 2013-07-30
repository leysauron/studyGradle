#..本例子作为学习gradle练习

   gradle配置：

    allprojects {
    apply plugin: 'maven'
    apply plugin: 'java'
    repositories {
        maven {
            url "http://127.0.0.1:8081/nexus/content/groups/public/"
        }
    }

    uploadArchives {
        repositories {
            mavenDeployer {
                repository(url: "http://127.0.0.1:8081/nexus/content/repositories/releases/") {
                    authentication(userName: "larry.li", password: "larry")
                }
                snapshotRepository(url: "http://127.0.0.1:8081/nexus/content/repositories/snapshots/") {
                    authentication(userName: "larry.li", password: "larry")
                }
            }
        }
    }
    task proJavadoc(type: Jar, dependsOn: 'javadoc') {
	from javadoc.destinationDir
	classifier = 'javadoc'
    }
    task proSources(type: Jar) {
	from sourceSets.main.allSource
	classifier = 'sources'
    }
    artifacts {
	archives proJavadoc
	archives proSources
    }
}

gradle.afterProject { proj ->
        proj.tasks.withType(Compile) {
            options.encoding = "UTF-8"
        }
        proj.tasks.withType(Javadoc) {
            options.encoding = "UTF-8"
        }
}

setting.gradle 放在gradle init.d 目录下 作为全局设置


uploadArchives设置为将自己的相关工程发布到nexus maven的私服上

下文的task proSources 和 proJavadoc 是上传源码和doc文档
gradle.afterProject 在windows下面Compile和Javadoc 默认使用gbk,通过设置将指定编码
===============================================================================
引入gradle模板 templates.gradle
gradle.beforeProject { prj ->
    prj.apply from: 'http://www.tellurianring.com/projects/gradle-plugins/gradle-templates/apply.groovy'
}

===================================================================
可以通过命令 gradle tasks查看创建的任务
