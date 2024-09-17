<h1>IaC para AWS - Criando VPC e uma Instância EC2 com Terraform</h1>
Projeto para criar Redes Virtuais VPC na AWS usando IaC (Terraform)

<h2>Objetivo</h2>

Demonstrar a eficiência no uso de Infraestructure-as-Code para o provisionamento de recursos em núvem. 

<h2>Resumo</h2>
Vamos usar o Terraform, uma ferramente de IaC desenvolvida pela HashiCorp que utiliza sua própria linguagem declarativa chamada HCL (HashiCorp Configuration Language). Com ela, podemos codificar todos os recursos que desejamos provisionar no nosso CSP (AWS). O Terraform faz a leitura de um diretório inteiro e executa todo o código presente, portanto podemos dividir os blocos ao invés de colocar tudo em um arquivo só e isso irá nos possibilitar um melhor controle sobre os recursos que serão criados. 

<h3>📜Passo a passo</h3>
Primeiramente vamos fazer a criação do diretório e abrir o mesmo com um editor de código (Visual Studio).
O primeiro arquivo que vamos criar será o "main.tf", onde vamos declarar os nosso <i>Provider</i>. O terraform pode ser usado com vários provedores diferentes, e eles são tratados como parte do código. Na documentação oficial podemos encontrar o bloco da AWS. 
![image](https://github.com/user-attachments/assets/ec2aee8a-1ffa-4e0a-b4ea-2683ccf90ecb)
