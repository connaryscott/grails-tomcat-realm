grails-tomcat-realm
=============


test grails application with a tomcat realm


Assumptions:
=============

CentOS 6.2

    [chuck@centos-62-64-vm3 grails-tomcat-realm]$ cat /etc/redhat-release 
    CentOS release 6.2 (Final)

Prereqisites:
=============

Tomcat v7 

    CATALINA_HOME=/home/chuck/workspace/apache-tomcat-7.0.29
    CATALINA_BASE=/home/chuck/workspace/apache-tomcat-7.0.29

Java/JVM 1.6

    JAVA_HOME=/usr/lib/jvm/java-1.6.0-openjdk-1.6.0.0.x86_64

Grails v2.0.4

    GRAILS_HOME=/home/chuck/workspace/grails-2.0.4
    [chuck@centos-62-64-vm3 grails-tomcat-realm]$ which grails
    ~/workspace/grails-2.0.4/bin/grails

Create a trivial Hello World Grails App
=============
Per hello world instructions, http://grails.org/doc/2.0.x/guide/gettingStarted.html#aHelloWorldExample, create a hello world app

    [chuck@centos-62-64-vm3 grails-tomcat-realm]$ grails create-app helloworld
    | Created Grails Application at /home/chuck/workspace/grails-tomcat-realm/helloworld
    [chuck@centos-62-64-vm3 grails-tomcat-realm]$ cd helloworld/
    [chuck@centos-62-64-vm3 helloworld]$ grails create-controller hello
    | Compiling 38 source files

    | Created file grails-app/controllers/helloworld/HelloController.groovy
    | Created file grails-app/views/hello
    | Created file test/unit/helloworld/HelloControllerTests.groovy

    [chuck@centos-62-64-vm3 helloworld]$ vi grails-app/controllers/helloworld/HelloController.groovy 

    [chuck@centos-62-64-vm3 helloworld]$ cat grails-app/controllers/helloworld/HelloController.groovy 
    package helloworld

    class HelloController {

        def index() { 
            render "Hello World!"
        }
    }

Verify Hello World App Runs 
=============
    [chuck@centos-62-64-vm3 helloworld]$ grails run-app
    | Compiling 38 source files

    | Server running. Browse to http://localhost:8080/helloworld



