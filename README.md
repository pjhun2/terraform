# terraform

* install
brew install terraform

* check
$ terraform version

* 버전 관리  
$ brew install tfenv  
* 특정 버전 설치  
$ tfenv install 0.12.23  
$ tfenv use 0.12.23  
$ terraform version  

1단계 - 먼저 아마존 웹 서비스 계정을 준비하고, API 키를 설정합니다.  
2단계 스텝 1 - 인프라스트럭처를 정의하는 HCL 언어로 필요한 리소스를 선언합니다.  
2단계 스텝 2 - 선언된 리소스들이 생성가능한지 계획(Plan)을 확인합니다.  
2단계 스텝 3 - 선언된 리소스들을 아마존 웹 서비스에 적용(Apply)합니다.  
3단계 - 웹 애플리케이션을 배포합니다.

기본 개념  
프로비저닝Provisioning  
어떤 프로세스나 서비스를 실행하기 위한 준비 단계를 프로비저닝이라고 이야기합니다. 프로비저닝에는 크게 네트워크나 컴퓨팅 자원을 준비하는 작업과 준비된 컴퓨팅 자원에 사이트 패키지나 애플리케이션 의존성을 준비하는 단계로 나뉘어집니다. 명확한 경계는 불분명하지만 테라폼은 전자를 주로 다루는 도구입니다.  

프로바이더Provider  
테라폼과 외부 서비스를 연결해주는 기능을 하는 모듈입니다. 예를 들어 테라폼으로 AWS 서비스의 컴퓨팅 자원을 생성하기 위해서는 aws 프로바이더를 먼저 셋업해야합니다. 프로바이더로는 AWS, 구글 클라우드 플랫폼Google Cloud Platform, 마이크로소프트 애저Microsoft Azure와 같은 범용 클라우드 서비스를 비롯해 깃허브Github, 데이터도그Datadog, DNSimple과 같은 특정 기능을 제공하는 서비스, MySQL, 레빗MQRabbitMQ, 도커Docker와 같은 로컬 서비스 등을 지원합니다. 전체 목록은 테라폼 프로바이더 문서에서 찾아볼 수 있습니다.  

리소스(자원)Resource  
리소스란 특정 프로바이더가 제공해주는 조작 가능한 대상의 최소 단위입니다. 예를 들어 AWS 프로바이더는 aws_instance 리소스 타입을 제공하고, 이 리소스 타입을 사용해 Amazon EC2의 가상 머신 리소스를 선언하고 조작하는 것이 가능합니다. EC2 인스턴스, 시큐리티 그룹, 키 페어 모두 aws 프로바이더가 제공해주는 리소스 타입입니다.  

HCLHashicorp Configuration Language  
HCL은 테라폼에서 사용하는 설정 언어입니다. 테라폼에서 모든 설정과 리소스 선언은 HCL을 사용해 이루어집니다. 테라폼에서 HCL 파일의 확장자는 .tf를 사용합니다.  

계획Plan  
테라폼 프로젝트 디렉터리 아래의 모든 .tf 파일의 내용을 실제로 적용 가능한지 확인하는 작업을 계획이라고 합니다. 테라폼은 이를 terraform plan 명령어로 제공하며, 이 명령어를 실행하면 어떤 리소스가 생성되고, 수정되고, 삭제될지 계획을 보여줍니다.  

적용Apply  
테라폼 프로젝트 디렉터리 아래의 모든 .tf 파일의 내용대로 리소스를 생성, 수정, 삭제하는 일을 적용이라고 합니다. 테라폼은 이를 terraform apply 명령어로 제공합니다. 이 명령어를 실행하기 전에 변경 예정 사항은 plan 명령어를 사용해 확인할 수 있습니다. 적용하기 전에도 플랜의 결과를 보여줍니다.  

계정 생성  
IAM 계정 생성  
access_key와 secret_key를 갖도록 생성  
![image](https://user-images.githubusercontent.com/74689088/144781389-372fdb88-636d-4297-b3d8-e53ba8da9178.png)  


provider 정의  
provider "aws" {  
  access_key = "<AWS_ACCESS_KEY>"  
  secret_key = "<AWS_SECRET_KEY>"  
  region = "ap-northeast-2"  
}  

이 때 <AWS_ACCESS_KEY>와 <AWS_SECRET_KEY>는 앞서 생성한 terraform 사용자의 인증 정보로 대체해줍니다. 
region은 리소스를 정의할 AWS 리전을 설정합니다.   
여기서 사용한 ap-northeast-2는 AWS의 서울 리전을 의미합니다.  

프로젝트 초기화  
$ terraform init  

리소스 정의
resource "aws_key_pair" "web_admin" {  
  key_name = "web_admin"  
  public_key = file("~/.ssh/web_admin.pub")  
}  

적용 확인  
terraform plan    
...  
Terraform will perform the following actions:  

  # aws_key_pair.web_admin will be created  
  + resource "aws_key_pair" "web_admin" {  
      + fingerprint = (known after apply)  
      + id          = (known after apply)  
      + key_name    = "web_admin"  
      + key_pair_id = (known after apply)  
      + public_key  = "ssh-rsa ...."  

Plan: 1 to add, 0 to change, 0 to destroy.  

plan 시 상태  
![image](https://user-images.githubusercontent.com/74689088/144783228-3cfe7207-e9ee-416f-aef3-19518ed9f8de.png)  

$ terraform apply  
...  
Terraform will perform the following actions:  

  # aws_key_pair.web_admin will be created  
  + resource "aws_key_pair" "web_admin" {  
      + fingerprint = (known after apply)  
      + id          = (known after apply)  
      + key_name    = "web_admin"  
      + key_pair_id = (known after apply)  
      + public_key  = "ssh-rsa ..."  
    }  

Plan: 1 to add, 0 to change, 0 to destroy.  

Do you want to perform these actions?  
  Terraform will perform the actions described above.  
  Only 'yes' will be accepted to approve.  

  Enter a value:  

apply 시 상태  
![image](https://user-images.githubusercontent.com/74689088/144783273-62b8a7f5-e419-464b-9460-145cace41d51.png)  

생성된 키 페어  
![image](https://user-images.githubusercontent.com/74689088/144783818-d507632a-00ad-40ae-b57c-0367b2f879ca.png)  

SSH 허용을 위한 보안그룹  
resource "aws_security_group" "ssh" {  
  name = "allow_ssh_from_all"  
  description = "Allow SSH port from all"  
  ingress {  
    from_port = 22  
    to_port = 22  
    protocol = "tcp"  
    cidr_blocks = ["0.0.0.0/0"]  
  }  
}  

적용 시 화면  
![image](https://user-images.githubusercontent.com/74689088/144784510-092793d1-2a98-4561-860f-6d2272146692.png)  

인스턴스 생성  
resource "aws_instance" "web" {  
  ami = "ami-0a93a08544874b3b7" # amzn2-ami-hvm-2.0.20200207.1-x86_64-gp2  
  instance_type = "t2.micro"  
  key_name = aws_key_pair.web_admin.key_name  
  vpc_security_group_ids = [  
    aws_security_group.ssh.id,  
    data.aws_security_group.default.id  
  ]  
}  

![image](https://user-images.githubusercontent.com/74689088/144784861-3797d1bb-0c6f-410a-92b3-690abc1f31f5.png)  

콘솔에서 IP 조회  
$ terraform console  
> aws_instance.web.public_ip  

SSH 연결  
ssh -i ~/.ssh/web_admin ec2-user@{Server_IP}  

       __|  __|_  )  
       _|  (     /   Amazon Linux 2 AMI  
      ___|\___|___|  

https://aws.amazon.com/amazon-linux-2/  

RDS 생성  
resource "aws_db_instance" "web_db" {  
  allocated_storage = 8  
  engine = "mysql"  
  engine_version = "5.6.35"  
  instance_class = "db.t2.micro"  
  username = "admin"  
  password = "<DB_PASSWORD>"  
  skip_final_snapshot = true  
}  

![image](https://user-images.githubusercontent.com/74689088/144785510-83a8ef3c-c083-4daa-b701-3a823f8d0279.png)  

Database EndPoint 확인  
$ terraform console  
> aws_db_instance.web_db.endpoint  

![image](https://user-images.githubusercontent.com/74689088/144785708-01766ffa-5e87-4908-b036-54f5ffb54a03.png)  
