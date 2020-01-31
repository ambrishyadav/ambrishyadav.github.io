# 3. Jenkins Configuration

### Setup Maven, Git and JDK

## 3.1 Config Jenkins to use the local JDK

- On the Jenkins home page, click "Global Tool Configuration"
- Under the "JDK" section, click the button "Add JDK"
- Enter a name, e.g "Name: localJDK"
- Uncheck "Install Automatically" and give the path to your maven directory JAVA_HOME=C:\Users\<username>\Documents\JavaJDK


## 3.2 Config Jenkins to use Git

- On the Jenkins home page, click "Global Tool Configuration"
- Under the "JDK" section, click the button "Add Git" and select Git in drop down
- Enter a name, e.g "Name: localGit"
- As "Path to Git executable enter, "git.exe" and make sure that the "Install Automatically" checkbox is unchecked


## 3.3 Config Jenkins to use Maven

- On the Jenkins home page, click "Global Tool Configuration"
- Under the "Maven" section, click the button "Add Maven"
- Enter a name, e.g "Name: localMaven"
- Uncheck "Install Automatically" and give the path to your maven directory MAVEN_HOME=C:\Users\<username>\Documents\apache-maven-3.6.2\


### Install all neccessary Jenkins plugins (TODO)

~~NOTE: Add proxy in advanced tab~~
