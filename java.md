---
id: z4u731x1jna6hkufsco2ewk
title: Java
desc: ''
updated: 1727275295988
created: 1656927387459
---

# Azul

[Azul](https://www.azul.com) - The Java platform

## Install

### Debian/Ubuntu

https://docs.azul.com/core/zulu-openjdk/install/debian#install-from-azul-apt-repository

# install the necessary dependencies
sudo apt-get -q update
sudo apt-get -yq install gnupg curl 

# add Azul's public key
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 0xB1998361219BD9C9

$ sudo apt-key export 219BD9C9 | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/azul_systems.gpg
$ sudo apt-key del 219BD9C9
$ sudo mv /etc/apt/trusted.gpg.d/azul_systems.gpg~ /etc/apt/trusted.gpg.d/azul_systems.gpg

# download and install the package that adds 
# the Azul APT repository to the list of sources 
curl -O https://cdn.azul.com/zulu/bin/zulu-repo_1.0.0-3_all.deb

# install the package
sudo apt install ./zulu-repo_1.0.0-3_all.deb && rm ./zulu-repo_1.0.0-3_all.deb

# update the package sources
sudo apt-get update

# install Azul Zulu JDK 11
sudo apt-get install zulu11-jdk

 
$ java -version
openjdk version "11.0.10" 2021-01-19 LTS
OpenJDK Runtime Environment Zulu11.45+27-CA (build 11.0.10+9-LTS)
OpenJDK 64-Bit Server VM Zulu11.45+27-CA (build 11.0.10+9-LTS, mixed mode)

## Azul Mission Control

[Install and Run Azul Mission Control](https://docs.azul.com/azul-mission-control/install)

Download from https://www.azul.com/products/components/zulu-mission-control/

Extract the downloaded package.

tar -xzvf zmc<version>-ca-linux_x64.tar.gz



Run Azul Mission Control as shown below:

 
$ zmc<version>-ca-linux_x64/Azul\ Mission\ Control/zmc

# Gradle

## Build for given Java version

* [Guide to Find the Java .class Version](https://www.baeldung.com/java-find-class-version)
* [How do I specify the required Java version in a Gradle build?](https://stackoverflow.com/questions/27861658/how-do-i-specify-the-required-java-version-in-a-gradle-build)