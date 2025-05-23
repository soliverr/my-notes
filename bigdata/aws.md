---
id: c4gz1nx5ttp780jbaff9gp4
title: Aws
desc: 
updated: 1680858399353
created: 1654594777906
mindmap-plugin: basic
---
## AWS CLI
- Sub title
	- [AWS CLI S3 reference](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/index.html)
	- [Amazon S3 Tools](https://s3tools.org/s3cmd)
		- https://github.com/s3tools/s3cmd
- Sub title
	- Linux shells – Use common shell programs such as bash, zsh, and tcsh to run commands in Linux or macOS.
	- Windows command line – On Windows, run commands at the Windows command prompt or in PowerShell.
	- Remotely – Run commands on Amazon Elastic Compute Cloud (Amazon EC2) instances through a remote terminal program such as PuTTY or SSH, or with AWS Systems Manager.
- Install

	-
	  ```
	  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
	  unzip awscliv2.zip
	  sudo ./aws/install
	  ```

- Update

	-
	  ```
	  sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update
	  ```

- Использование AWS CLI
	- Version

		-
		  ```sh
		  $ aws --version
		  aws-cli/2.7.5 Python/3.9.11 Linux/5.15.0-35-generic exe/x86_64.ubuntu.22 prompt/off
		  ```

	- Подключение в AWS

		-
		  ```sh
		  $ aws configure sso
		  ```

	- SSO

		-
		  ```sh
		  $ aws sso login [--profile <name>]
		  ```

	- Configure
		- Sub title
			- [Configure](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/configure/index.html#cli-aws-configure)

		-
		  ```
		  $ aws configure
		  
		  $ cat ~/.aws/config
		  ```


		-
		  ```sh
		  $ aws configure list
		  Name                    Value             Type    Location
		  ----                    -----             ----    --------
		  profile                <not set>             None    None
		  access_key                <not set>             None    None
		  secret_key                <not set>             None    None
		  region                <not set>             None    None
		  
		  $ aws configure list-profiles
		  DeveloperRO_Access-418337671376
		  
		  aws configure set default.region eu-west-1
		  
		  ```

		- Выбор профиля
		- Добавить профиль для доступа в новый кластер

			-
			  ```
			  $ aws eks --region eu-central-1 update-kubeconfig --name prod-eu-central-1 --profile <свой профиль>
			  ```


## S3
- Sub title
	- [AWS Simple Storage Service](https://aws.amazon.com/s3/)
	- [s3](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/index.html)
	- [s3api](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/index.html)
- Buckets
	- https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-buckets-s3.html
- Directories

## IAM

## ECR
- Amazon ECR Docker Credential Helper
	- Sub title
		- [Use AWS ECR Credential Helper with Multiple AWS Accounts](https://wadehuang36.medium.com/use-aws-ecr-credential-helper-with-multiple-accounts-4c38b7ee37d)
		- https://github.com/awslabs/amazon-ecr-credential-helper
	- Install
		- Ubuntu

			-
			  ```
			  $ sudo apt install amazon-ecr-credential-helper
			  [sudo] пароль для oliver:
			  Чтение списков пакетов… Готово
			  Построение дерева зависимостей… Готово
			  Чтение информации о состоянии… Готово
			  Будут установлены следующие дополнительные пакеты:
			  bridge-utils containerd docker.io pigz runc ubuntu-fan
			  Предлагаемые пакеты:
			  ifupdown aufs-tools btrfs-progs cgroupfs-mount | cgroup-lite debootstrap docker-doc rinse zfs-fuse | zfsutils
			  Следующие НОВЫЕ пакеты будут установлены:
			  amazon-ecr-credential-helper bridge-utils containerd docker.io pigz runc ubuntu-fan
			  
			  ...
			  $ ps ax | grep -i docker
			  
			  29347 ?        Ssl    0:00 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
			  ```

		- Binary
			- Sub title
				- https://github.com/awslabs/amazon-ecr-credential-helper/releases

			-
			  ```
			  $ sudo curl https://amazon-ecr-credential-helper-releases.s3.us-east-2.amazonaws.com/0.6.0/linux-amd64/docker-credential-ecr-login -o /usr/local/bin/docker-credential-ecr-login && sudo chmod +x /usr/local/bin/docker-credential-ecr-login
			  ```