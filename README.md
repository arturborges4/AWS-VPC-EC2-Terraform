<h1>IaC para AWS - Criando VPC e uma Instância EC2 com Terraform</h1>
Projeto para provisionar uma Rede Virtual VPC e uma máquina virtual EC2 na AWS usando IaC (Terraform)

<h2>Objetivo</h2>

Demonstrar a eficiência no uso de Infraestructure-as-Code para o provisionamento de recursos em núvem. 

<h2>Resumo</h2>
O Terraform é uma ferramente de IaC desenvolvida pela HashiCorp que utiliza sua própria linguagem declarativa chamada HCL (HashiCorp Configuration Language). Com ela, podemos codificar todos os recursos que desejamos provisionar no nosso CSP. O Terraform faz a leitura de um diretório inteiro e executa todo o código presente, portanto podemos dividir os blocos ao invés de colocar tudo em um arquivo só e isso irá nos possibilitar um melhor controle sobre os recursos que serão criados. 

<h3>📜Passo a passo</h3>
Primeiramente vamos fazer a criação do diretório e abrir com um editor de código (Visual Studio).
O primeiro arquivo que vamos criar será o "main.tf", onde vamos declarar os nosso <i>Provider</i>. O terraform pode ser usado com vários provedores diferentes, e eles são tratados como parte do código. Na documentação oficial podemos encontrar o bloco referente ao <i>provider</i> da AWS.

![Provider_2](https://github.com/user-attachments/assets/461a433f-7c14-4991-9ecc-ea9c785458b5)


Vamos colar o código em nosso arquivo "main.tf" e fazer apenas algumas modificações. Devemos escolher uma região da AWS onde nossos recursos serão alocados, nesse caso irei usar "us-east-1" como padrão. 
```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "5.67.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

```

Após configurar o <i>provider</i> podemos seguir para o próximo arquivo, que será a configuração da nossa VPC. 
Para esse experimento, vamos criar uma rede privada que tem acesso à internet para que nossa instância consiga "pingar" para fora, ou seja, liberar o trafego ICMP. 
Vamos precisar de alguns recursos adicionais dentro da nossa VPC, que possibilitarão o acesso para fora. Esse recursos são uma Subnet com mapeamento de IP publico, um Internet Gateway associoado à nossa VPC, uma Route Table e uma Route Table Association para associar à nossa subnet pública e possibilitar o acesso à internet. 

```
resource "aws_vpc" "vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "vpc-terraform"
  }
}
resource "aws_subnet" "subnet" {
  vpc_id     = aws_vpc.vpc.id
  cidr_block = "10.0.1.0/24"
  map_public_ip_on_launch = true

  tags = {
    Name = "subnet-terraform"
  }
}
resource "aws_internet_gateway" "internet_gateway" {
  vpc_id = aws_vpc.vpc.id

  tags = {
    Name = "internet-gateway-terraform"
  }
}
resource "aws_route_table" "route_table" {
  vpc_id = aws_vpc.vpc.id

  route {
    cidr_block             = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.internet_gateway.id
  }

  tags = {
    Name = "route-table-terraform"
  }
}
resource "aws_route_table_association" "rta" {
  subnet_id      = aws_subnet.subnet.id
  route_table_id = aws_route_table.route_table.id
}

```

Para criar uma camada de proteção, vamos adiconar um <i>Security Group</i> à nossa VPC para limitar o trafego e liberar apenas o acesso necessário (acesso SSH (porta 22) e HTTP (porta 80)). 

```
resource "aws_security_group" "allow_ssh_http" {
  vpc_id = aws_vpc.vpc.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "allow_ssh_http"
  }
}

```
Finalmente, iremos criar nossa instância EC2 associada à subnet. Também devemos associar uma "key-pair" para fazer o acesso via SSH. 

