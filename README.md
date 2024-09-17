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
