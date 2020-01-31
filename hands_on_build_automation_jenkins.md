# Hands-on build automation with jenkins

1. [ Tool Setup ](#tool_setup)
2. [ Final Check ](#final_check)
3. [ Jenkins Configuration ](#jenkins_config)
4. [ Lessons ](#lessons)

<a name="tool_setup"></a>
## 1. Tool setup
---
NOTE: Please replace <username> with your actual LDAP username

### 1.1 Java
Execute the following on your windows command prompt

```C:\Users\<username>\Documents\>java -version```

    C:\Users\<username>\Documents\>java -version

    java version "1.8.0_201"
    Java(TM) SE Runtime Environment (build 1.8.0_201-b09)
    Java HotSpot(TM) Client VM (build 25.201-b09, mixed mode)
	
Typically java should be installed by default on your laptop. If not
- Search and download zip/tar file (e.g. jre-8u241-windows-x64.tar) and unzip jre to 
    
    ```e.g. C:\Users\<username>\Documents\jre1.8.0_241```
- set path manually

        C:\Users\<username>\Documents\>set JAVA_HOME=C:\Users\<username>\Documents\jre1.8.0_241

- retry 

        C:\Users\<username>\Documents\>java -version

        java version "1.8.0_241"
        Java(TM) SE Runtime Environment (build 1.8.0_241-b07)
        Java HotSpot(TM) 64-Bit Server VM (build 25.241-b07, mixed mode)

### 1.2 Jenkins
Since you cannot install Jenkins on your local machine with the windows installer I would suggest downloaing the "Generic Java package"

```http://mirrors.jenkins.io/war-stable/latest/jenkins.war```
	
- Create a folder under your documents directory e.g. 

    ```C:\Users\<username>\Documents\Jenkins```
	
- Test that jenkins can be started using command line prompt

	    C:\Users\<username>\Documents\Jenkins>java -jar jenkins.war

    On the console you should see something like

        2020-01-17 11:02:28.453+0000 [id=19] INFO  hudson.WebAppMain$3#run: Jenkins is fully up and running
	
- Check jenkins UI Access and follow the instructions to complete the installation

	```http://localhost:8080```
		
	**Viola!** You have jenkins up and running.
	
- Login\
    user: admin\
    password (initial password stored in this file): ```C:\Users\<username>\.jenkins\secrets\initialAdminPassword```

**Viola!** You are logged in. Now change your admin password.

### 1.3 Java JDK

JDK is necessary for Maven and may not be installed on your laptop and you may also not have rights to install it. Follow the steps mentioned in the link below to install JDK without admin rights.

```https://www.whitebyte.info/programming/java/how-to-install-a-portable-jdk-in-windows-without-admin-rights```

It is recommended to create a "JDK" directory under your "Documents" folder.

```e.g. C:\Users\<username>\Documents\JavaJDK```

After you completed the steps mentioned in the link above check if jdk works,

    C:\Users\<username>\Documents\JavaJDK\bin>javac -version 
    javac 1.8.0_241

### 1.4 Maven

- Search online and download (zip/tar file e.g. apache-maven-3.6.2-bin) and unzip to your documents directory e.g.
    
    ```e.g. C:\Users\<username>\Documents\apache-maven```

- Check if maven works

        C:\Users\<username>\Documents\apache-maven-3.6.2\bin>mvn -version

        Apache Maven 3.6.2 (40f52333136460af0dc0d7232c0dc0bcf0d9e117; 2019-08-27T17:06:16+02:00)
        Maven home: C:\Users\<username>\Documents\apache-maven-3.6.2\bin\..
        Java version: 1.8.0_201, vendor: Oracle Corporation, runtime: C:\Program Files (x86)\Java\jre1.8.0_201
        Default locale: en_US, platform encoding: Cp1252
        OS name: "windows 10", version: "10.0", arch: "x86", family: "windows"

~~- Adding Maven to the Environment Path
    Add both M2_HOME and MAVEN_HOME variables or configure in jenkins??~~

- You need to specify a proxy for maven (needed for jenkins job when maven tries to download required plugins)

    - Open and edit file ```C:\Users\<username>\Documents\apache-maven-3.6.2\conf```

    - Find the <proxies> element in that file and add your LDAP user and password along with proxy URL and port for both hhtp and https and save the file.
    See example below

    ```xml
        <proxies>
            <proxy>
                <id>optional</id>
                <active>true</active>
                <protocol>http</protocol>
                <username>myuser</username>
                <password>mypass</password>
                <host>globalproxy.goc.dhl.com</host>
                <port>8080</port>
                <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
            </proxy>
            <proxy>
                <id>optional</id>
                <active>true</active>
                <protocol>https</protocol>
                <username>myuser</username>
                <password>mypass</password>
                <host>globalproxy.goc.dhl.com</host>
                <port>8080</port>
                <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
            </proxy>
        </proxies>
    ```
    - Setup Maven to use the local JDK

        - Open and edit the follwing file

            ```C:\Users\<username>\Documents\apache-maven-3.6.2\bin\mvn.cmd```

        - Add line (without linebreaks and somwhere in the beginning of the file after most of the @REM comments)

            set "JAVA_HOME=C:\Users\<username>\Documents\JavaJDK"
        
        - Save the file

### 1.5 Git

- No admin rights on Windows? Use the portable edition. Don’t forget to add the path with git.exe to your PATH variable

    ```https://sourceforge.net/projects/gitportable/```

- Double click to extract to 

    ```C:\Users\<username>\Documents\Git```

- Double click the portable installer and install the program. I assume it will be install under

    ```C:\Program Files\PortableGit```

- git set path

    Open command prompt and execute the following

        setx path "%path%;C:\Program Files\PortableGit\bin"


- git proxy settings

    If you are behind a corporate proxy set the HTTP_PROXY and HTTPS_PROXY variables or set the proxy directly in your git config.

    Please replace the USERNAME and PASSWORD with your LDAP user and password.

        C:\Users\<username>\Documents\>git config --global http.proxy "http://USERNAME:PASSWORD@globalproxy.goc.dhl.com:8080"

        C:\Users\<username>\Documents\>git config --global https.proxy "http://USERNAME:PASSWORD@globalproxy.goc.dhl.com:8080"

    Tip: if you need to remove this config, try the following

        C:\Users\<username>\Documents\>git config --global --list

        C:\Users\<username>\Documents\>git config --global --unset https.proxy

        C:\Users\<username>\Documents\>git config --global --unset http.proxy

    OR

    Edit the C:\Users\<username>\.gitconfig file and delete the contents manually

### 1.6 Tomcat

We need tomcat webserver to deploy our test app. This can be skipped for now but is necessary for chapter 4.4.

- Search online and download (zip/tar file e.g. ) and unzip tomcat to your documents directory e.g.
    
    ```e.g. C:\Users\<username>\Documents\apache-tomcat```

- tomcat configuration

    - set JAVA_HOME

            Open
            C:\Users\<username>\Documents\apache-tomcat\bin\catalina.bat

            Add line
            "set JAVA_HOME=C:\Users\<username>\Documents\JavaJDK"

            Save catalina.bat

    - set port

        Since jenkins is already using port 8080, we need to specify another port for tomcat server.

            Go to folder C:\Users\<username>\Documents\apache-tomcat\conf

            Edit file server.xml

            Search "Connector port"

            Replace "8080" by "9090"

            Save file server.xml


- Test that tomcat can be started using command line prompt

	```C:\Users\<username>\Documents\apache-tomcat\bin>startup```

    
- Check tomcat UI Access and follow the instructions to complete the installation

	```http://localhost:9090```
		
	**Viola!** You have tomcat up and running.



<a name="final_check"></a>
## 2. Final Check

You have installed the following tools under your ```C:\Users\<username>\Documents``` directory

```C:\Users\<username>\Documents\jre1.8.0_241```\
```C:\Users\<username>\Documents\Jenkins```\
```C:\Users\<username>\Documents\apache-maven-3.6.2```\
```C:\Users\<username>\Documents\JavaJDK```\
```C:\Users\<username>\Documents\Git```\
```C:\Users\<username>\Documents\apache-tomcat``` (optional but required for chapter 4.4)

- Is Jenkins up and running on port 8080?
- Maven, Git and JDK in path?
- (Optional) Is tomcat up and running 9090?
- (Optional) Github account?

<a name="jenkins_config"></a>
## 3. Jenkins Configuration
---

#### Setup Maven, Git and JDK

### 3.1 Config Jenkins to use the local JDK

- On the Jenkins home page, click "Global Tool Configuration"
- Under the "JDK" section, click the button "Add JDK"
- Enter a name, e.g "Name: localJDK"
- Uncheck "Install Automatically" and give the path to your maven directory JAVA_HOME=C:\Users\<username>\Documents\JavaJDK


### 3.2 Config Jenkins to use Git

- On the Jenkins home page, click "Global Tool Configuration"
- Under the "JDK" section, click the button "Add Git" and select Git in drop down
- Enter a name, e.g "Name: localGit"
- As "Path to Git executable enter, "git.exe" and make sure that the "Install Automatically" checkbox is unchecked

~~file:////C:/Users/<username>/Documents/ambi_workspace/maven-project~~


### 3.3 Config Jenkins to use Maven

- On the Jenkins home page, click "Global Tool Configuration"
- Under the "Maven" section, click the button "Add Maven"
- Enter a name, e.g "Name: localMaven"
- Uncheck "Install Automatically" and give the path to your maven directory MAVEN_HOME=C:\Users\<username>\Documents\apache-maven-3.6.2\


~~#### Install all neccessary Jenkins plugins~~

~~NOTE: Add proxy in advanced tab~~

~~#### Get a test project~~

<a name="lessons"></a>
## 4. Lessons
---

### 4.1 First jenkins job

#### Create a jenkins job

- On the Jenkins home page, click "New Item" and enter item name e.g. "first-jenkins-job".
- Select "Freestyle project" adn clcik ok.
- You should now see the jenkins job description page.
- Add description as "This is my first jenkins job".
- "Source Code Management" is set to "None"
- All Build Triggers options are unchecked
- In the "Build" section select "Execute shell" and add the following command
        
        echo "hello Jenkins"
- Click "Save"
- Go back to the Jenkins Dashboard and your newly created jenkins job should be displayed

#### Run jenkins job

- Given that the jenkins job is displayed on the dashboard you should see an icon on the righmost corner (clock with a play button)
- Click this icon to trigger your first build OR mouseover on the job name to reveal a dropdown menu where you can select "Build Now" OR click ont he job itself to go to the job details page and on the left handside click "Build Now"
- On doing so, your build will start and status should be displayed on the left hand side under the "Build history" panel.
- Assuming that the build is successful (blue traffic light) you can now click on that build number to reveal details about the build
- Click "Console Output" where you should see 

        "hello Jenkins"
    followed by 
    
        "Finished: SUCCESS"

**Viola!** You have successfully created and executed your 1st jenkins job.


### 4.2 First maven based jenkins project (github) - TODO

#### Create a free account on Github

#### Create a jenkins job

#### Run jenkins job

### 4.3 First maven based jenkins project (local git) - TODO

#### Create a jenkins job

#### Run jenkins job

### 4.4 Deploy Test App - TODO

#### Create a jenkins job

#### Run jenkins job

### 4.5 Integrate Code Quality and Code Metrics within a jenkins job - TODO

#### Create a jenkins job

#### Run jenkins job

