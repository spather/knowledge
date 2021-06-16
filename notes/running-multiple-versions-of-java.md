---
tags: java programming
---

# Running Multiple Versions of Java
[Why Java does not have anything like virtualenv](https://stackoverflow.com/questions/7300148/is-there-anything-like-virtualenv-for-java)
* tl;dr: because it just uses `CLASSPATH` to load dependencies

Installing a specific version of Java (JDK?) on MacOS:
* Based on [this article](https://knasmueller.net/how-to-install-java-openjdk-15-on-macos-catalina).
* Downloaded `openjdk-15.0.2_osx-x64_bin.tar.gz` from https://jdk.java.net/archive/, then:
```
sudo mv ~/Downloads/openjdk-15.0.2_osx-x64_bin.tar.gz /Library/Java/JavaVirtualMachines/

cd /Library/Java/JavaVirtualMachines/

sudo tar -xzf openjdk-15.0.2_osx-x64_bin.tar.gz

sudo rm openjdk-15.0.2_osx-x64_bin.tar.gz
```
* Tested that this worked with:
```
$ /usr/libexec/java_home -v15
/Library/Java/JavaVirtualMachines/jdk-15.0.2.jdk/Contents/Home
```
* Had v16 installed by default before, so checked that's still there:
```
$ /usr/libexec/java_home -v16
/Library/Java/JavaVirtualMachines/jdk-16.jdk/Contents/Home
```
* Checked current version, noting that `JAVA_HOME` is not set in my shell:
```
[spather@MacBook-Pro ~]
$ echo $JAVA_HOME

[spather@MacBook-Pro ~]
$ java --version
java 16 2021-03-16
Java(TM) SE Runtime Environment (build 16+36-2231)
Java HotSpot(TM) 64-Bit Server VM (build 16+36-2231, mixed mode, sharing)
[spather@MacBook-Pro ~]
```

* Added some functions to .bashrc to enable switching:
```bash
# Java Stuff
use_default_java() {
   unset JAVA_HOME
}

use_java15() {
   export JAVA_HOME=$(/usr/libexec/java_home -v15)
}
```
* Test this out:

```
[spather@MacBook-Pro ~]
$ source .bashrc
[spather@MacBook-Pro ~]
$ echo $JAVA_HOME

[spather@MacBook-Pro ~]
$ java --version
java 16 2021-03-16
Java(TM) SE Runtime Environment (build 16+36-2231)
Java HotSpot(TM) 64-Bit Server VM (build 16+36-2231, mixed mode, sharing)
[spather@MacBook-Pro ~]
$ use_java15
[spather@MacBook-Pro ~]
$ java --version
openjdk 15.0.2 2021-01-19
OpenJDK Runtime Environment (build 15.0.2+7-27)
OpenJDK 64-Bit Server VM (build 15.0.2+7-27, mixed mode, sharing)
[spather@MacBook-Pro ~]
$ use_default_java
[spather@MacBook-Pro ~]
$ java --version
java 16 2021-03-16
Java(TM) SE Runtime Environment (build 16+36-2231)
Java HotSpot(TM) 64-Bit Server VM (build 16+36-2231, mixed mode, sharing)
[spather@MacBook-Pro ~]
```
