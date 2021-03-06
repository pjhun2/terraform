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

  //aws_key_pair.web_admin will be created  
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

프로비저닝된 인프라스트럭쳐 일괄 종료  
mv web_infra.tf /tmp/  

--------------------------------------------
admin@192 web_infra % terraform plan
aws_key_pair.web_admin: Refreshing state... [id=web_admin]
aws_security_group.ssh: Refreshing state... [id=sg-04e3de0e0596bca77]
aws_db_instance.web_db: Refreshing state... [id=terraform-20211206030208071100000001]
aws_instance.web: Refreshing state... [id=i-0d75d3077d64d203a]

  aws_db_instance.web_db will be destroyed
  - resource "aws_db_instance" "web_db" {


  aws_instance.web will be destroyed

    }

  aws_key_pair.web_admin will be destroyed
  - resource "aws_key_pair" "web_admin" {

    }

  aws_security_group.ssh will be destroyed
  - resource "aws_security_group" "ssh" {

    }
Plan: 0 to add, 0 to change, 4 to destroy.
-----------------------------------------------

현재 상태  
![image](https://user-images.githubusercontent.com/74689088/144787780-86e0a752-dcd5-4f17-af91-3efd4f2221e1.png)  

복원  
mv /tmp/web_infra.tf .  
plan시 no changes 상태  


파일을 옮기지 않고 현재까지 생성한 인스턴스 및 기타 삭제  

----------------------------------------  
$ terraform plan -destroy  
...  
  - resource "aws_db_instance" "web_db" {  
...  
  - resource "aws_instance" "web" {  
...  
  - resource "aws_key_pair" "web_admin" {  
...  
  - resource "aws_security_group" "ssh" {  

Plan: 0 to add, 0 to change, 4 to destroy.  

----------------------------------------  
$ terraform destroy
...
  - resource "aws_db_instance" "web_db" {
...
  - resource "aws_instance" "web" {
...
  - resource "aws_key_pair" "web_admin" {
...
  - resource "aws_security_group" "ssh" {

Plan: 0 to add, 0 to change, 4 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes
  
  --------------------------------------  
  
  인스턴스 및 기타 그룹 삭제  
  ![image](https://user-images.githubusercontent.com/74689088/144788504-38a1330b-441d-4985-8357-efc221bcc793.png)  
  
  ![image](https://user-images.githubusercontent.com/74689088/144788694-2c45319f-5ece-4029-ae69-1ebf08813fd6.png) 
  
  ![image](https://user-images.githubusercontent.com/74689088/144788907-660ed66c-ce87-4f8e-8de5-d673a698fbdc.png)  
  
  ![image](https://user-images.githubusercontent.com/74689088/144788939-f1b4e2c6-4b0b-45d2-9450-111d0363dbcf.png)  
  
  모든 리소스 다시 프로비저닝 하기  
  terraform plan  
  terraform apply  
 
테라폼 사용 이유  
그럼에도 테라폼을 사용하면 좋은 점들이 있습니다. 먼저 웹 콘솔을 사용해 리소스들을 관리하면서 점차 리소스가 많아지기 시작하면 더 이상 전체 리소스를 파악할 수 없는 시점이 옵니다. 업무용으로 사용하는 경우 명시적인 리소스와 비명시적인 리소스를 포함해 수백 수천개의 리소스를 다루는 일이 일반적입니다. 언제 왜 만들었는지 알 수 없는 리소스들이 점점 쌓여나가고, 레거시로 남습니다. 테라폼을 사용하면 프로비저닝하고자 하는 상태를 코드로 명확히 기록해두기 때문에 웹 콘솔만 사용할 때보다 파악이 쉬워지고 세심한 관리가 가능해집니다.  
  
또한 테라폼은 코드로서의 인프라스터럭처Infrastructure as Code를 지향하는 도구로서, 코드를 작성할 때 누리던 생태계의 이점을 그대로 이용할 수 있습니다. 저장소에서 이력 추적을 할 수도 있고, 깃허브Github에서 팀원들과 코드 리뷰를 진행할 수도 있습니다. 이 과정에서 누가 어떤 리소스를 왜 추가했는지, 투명성은 자동적으로 얻어집니다. 좀 더 잘 활용한다면 CI를 사용해 코드 리뷰가 된 사항을 자동적으로 플랜 및 적용하는 것도 가능합니다.  
   
