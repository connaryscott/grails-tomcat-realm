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

Tomcat Configured with: 

    JAAS Login Module: $CATALINA_HOME/lib/JaasTutorial.jar
    JAAS Configuration: $CATALINA_BASE/conf/sample_jaas.config 
    helloworld context configuration:  $CATALINA_BASE/conf/Catalina/localhost/helloworld.xml 

    CATALINA_OPTS=-Djava.security.auth.login.config=${CATALINA_BASE}/conf/sample_jaas.config

 see: https://github.com/connaryscott/JaasDev


OpenLDAP Setup, see: https://github.com/connaryscott/JaasDev

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


Install dreaded Spring Security
=============

    [chuck@centos-62-64-vm3 helloworld]$ vi grails-app/conf/BuildConfig.groovy 

And add the spring-security-core plugin 

        plugins {
            runtime ":hibernate:$grailsVersion"
            runtime ":jquery:1.7.1"
            runtime ":resources:1.1.6"

            compile ":spring-security-core:1.2.7.1"


Compile and trigger new dependency downloads

    [chuck@centos-62-64-vm3 helloworld]$ vi grails-app/conf/BuildConfig.groovy

    Resolving plugin JAR dependencies

    *******************************************************
    * You've installed the Spring Security Core plugin.   *
    *                                                     *
    * Next run the "s2-quickstart" script to initialize   *
    * Spring Security and create your domain classes.     *
    *                                                     *
    *******************************************************
    | Compiling 76 source files

    | Compiling 9 source files.

Run the s2-quickstart script with User/Role domain classes

    [chuck@centos-62-64-vm3 helloworld]$ grails s2-quickstart helloworld User Role

    | 
    *******************************************************
    * Created domain classes, controllers, and GSPs. Your *
    * grails-app/conf/Config.groovy has been updated with *
    * the class names of the configured domain classes;   *
    * please verify that the values are correct.          *
    *******************************************************

    [chuck@centos-62-64-vm3 helloworld]$ find grails-app/domain 
    grails-app/domain
    grails-app/domain/helloworld
    grails-app/domain/helloworld/Role.groovy
    grails-app/domain/helloworld/UserRole.groovy
    grails-app/domain/helloworld/User.groovy
    
Configure Hello World Grails Templates 
=============

    [chuck@centos-62-64-vm3 helloworld]$ grails install-templates
    | Templates installed successfully

modify the source web.xml for the ROLE_YANA_USER role

    [chuck@centos-62-64-vm3 helloworld]$ vi src/templates/war/web.xml 

    <security-constraint>
            <web-resource-collection>
                    <web-resource-name>web</web-resource-name>
                    <url-pattern>/*</url-pattern>
            </web-resource-collection>
            <auth-constraint>
                    <role-name>*</role-name>
           </auth-constraint>
    </security-constraint>
    <security-role>
            <role-name>ROLE_YANA_USER</role-name>
    </security-role>


Generate the Hello World war and deploy to Tomcat

    [chuck@centos-62-64-vm3 helloworld]$ grails war
    | Done creating WAR target/helloworld-0.1.war

    [chuck@centos-62-64-vm3 helloworld]$ rm -rf ${CATALINA_BASE}/webapps/helloworld*
    [chuck@centos-62-64-vm3 helloworld]$ cp target/helloworld-0.1.war ${CATALINA_BASE}/webapps/helloworld.war
    [chuck@centos-62-64-vm3 helloworld]$ $CATALINA_BASE/bin/startup.sh
    Using CATALINA_BASE:   /home/chuck/workspace/apache-tomcat-7.0.29
    Using CATALINA_HOME:   /home/chuck/workspace/apache-tomcat-7.0.29
    Using CATALINA_TMPDIR: /home/chuck/workspace/apache-tomcat-7.0.29/temp
    Using JRE_HOME:        /usr/lib/jvm/java-1.6.0-openjdk-1.6.0.0.x86_64
    Using CLASSPATH:       /home/chuck/workspace/apache-tomcat-7.0.29/bin/bootstrap.jar:/home/chuck/workspace/apache-tomcat-7.0.29/bin/tomcat-juli.jar

When connecting to http://<yourHost>:8080/helloworld/ with the following credendials:

    username:  rfirefly
    password:  Gr0uch0M@rx

Which shows that we authenticated in the catalina.out

    sample.module.SampleLoginModule DEBUG: hostname: localhost
    sample.module.SampleLoginModule DEBUG: _port: 389
    sample.module.SampleLoginModule DEBUG: providerUrl: ldap://localhost
    sample.module.SampleLoginModule DEBUG: contextFactory: com.sun.jndi.ldap.LdapCtxFactory
    sample.module.SampleLoginModule DEBUG: userBaseDn: ou=People,dc=dtolabs,dc=com
    sample.module.SampleLoginModule DEBUG: roleBaseDn: ou=roles,dc=dtolabs,dc=com
    sample.module.SampleLoginModule DEBUG: userObjectClass: posixAccount
    sample.module.SampleLoginModule DEBUG: userRdnAttribute: uid
    sample.module.SampleLoginModule DEBUG: userIdAttribute: uid
    sample.module.SampleLoginModule DEBUG: userPasswordAttribute: userPassword
    sample.module.SampleLoginModule DEBUG: roleObjectClass: posixGroup
    sample.module.SampleLoginModule DEBUG: roleMemberAttribute: memberUid
    sample.module.SampleLoginModule DEBUG: roleUsernameMemberAttribute: memberUid
    sample.module.SampleLoginModule DEBUG: roleNameAttribute: cn
    sample.module.SampleLoginModule DEBUG: username: rfirefly
    sample.module.SampleLoginModule DEBUG: Role for user uid=rfirefly,ou=People,dc=dtolabs,dc=com: admin
    sample.module.SampleLoginModule DEBUG: Role for user uid=rfirefly,ou=People,dc=dtolabs,dc=com: user
    sample.module.SampleLoginModule DEBUG: Role for user uid=rfirefly,ou=People,dc=dtolabs,dc=com: YANA_USER
    sample.module.SampleLoginModule DEBUG: Role for user uid=rfirefly,ou=People,dc=dtolabs,dc=com: YANA_ADMIN
    sample.module.SampleLoginModule DEBUG: Role for user uid=rfirefly,ou=People,dc=dtolabs,dc=com: ROLE_YANA_ARCHITECT
    sample.module.SampleLoginModule DEBUG: Role for user uid=rfirefly,ou=People,dc=dtolabs,dc=com: ROLE_YANA_USER
    sample.module.SampleLoginModule DEBUG: Role for user uid=rfirefly,ou=People,dc=dtolabs,dc=com: ROLE_YANA_ADMIN
    sample.module.SampleLoginModule INFO: JettyCachingLdapLoginModule: User 'rfirefly' has roles: [admin, user, YANA_USER, YANA_ADMIN, ROLE_YANA_ARCHITECT, ROLE_YANA_USER, ROLE_YANA_ADMIN]
    sample.module.SampleLoginModule DEBUG: checking role: admin
    sample.module.SampleLoginModule DEBUG: adding role: admin to user: rfirefly
    sample.module.SampleLoginModule DEBUG: checking role: user
    sample.module.SampleLoginModule DEBUG: adding role: user to user: rfirefly
    sample.module.SampleLoginModule DEBUG: checking role: YANA_USER
    sample.module.SampleLoginModule DEBUG: adding role: YANA_USER to user: rfirefly
    sample.module.SampleLoginModule DEBUG: checking role: YANA_ADMIN
    sample.module.SampleLoginModule DEBUG: adding role: YANA_ADMIN to user: rfirefly
    sample.module.SampleLoginModule DEBUG: checking role: ROLE_YANA_ARCHITECT
    sample.module.SampleLoginModule DEBUG: adding role: ROLE_YANA_ARCHITECT to user: rfirefly
    sample.module.SampleLoginModule DEBUG: checking role: ROLE_YANA_USER
    sample.module.SampleLoginModule DEBUG: adding role: ROLE_YANA_USER to user: rfirefly
    sample.module.SampleLoginModule DEBUG: checking role: ROLE_YANA_ADMIN
    sample.module.SampleLoginModule DEBUG: adding role: ROLE_YANA_ADMIN to user: rfirefly

