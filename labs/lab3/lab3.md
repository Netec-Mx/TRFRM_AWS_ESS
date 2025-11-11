---
layout: lab
title: "Práctica 3: Definición de recursos en Terraform"
permalink: /lab3/lab3/
images_base: /labs/lab3/img
duration: "15 minutos"
objective:
  - Definir en Terraform tres recursos básicos en AWS dentro de una VPC propia, un Security Group HTTP y una instancia EC2 en una subred pública. Se crean explícitamente VPC, Subred pública, Internet Gateway, Tabla de ruteo pública y Ruta por defecto. Con iniciales en nombres/etiquetas.
prerequisites:
  - Carpeta `TERRALABS` con `main.tf`.
  - Proveedor `aws` declarado correctamente en `main.tf`.
  - AWS CLI autenticado (`aws configure` o `aws sso login`).
introduction:
  - En lugar de depender de la VPC por defecto, crearás tu propia red mínima pública (VPC, Subred, IGW, Tabla y Ruta) y desplegarás una EC2 que expone Nginx en el puerto 80 usando un Security Group. Reemplaza manualmente `xxx` por tus iniciales en cada nombre/etiqueta.
slug: lab3
lab_number: 3
final_result: |
  - El archivo `main.tf` define:
    - VPC 10.50.0.0/16 y Subred pública 10.50.1.0/24 con salida a Internet.
    - Security Group con HTTP/80 abierto.
    - EC2 en la subred pública que levanta Nginx en contenedor.
  - Recursos listos para ejecutar en la siguiente práctica (`terraform init/plan/apply`).
notes:
  - Reemplaza **todas** las apariciones de `xxx` por tus iniciales.
  - Ajusta **región/Availability Zone** si no usas `us-east-1a`.
  - HTTP/80 abierto a `0.0.0.0/0` solo para laboratorio.
references:
  - text: VPC guide – Your first VPC
    url: https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html
  - text: Security groups for your VPC
    url: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html
  - text: Terraform AWS Provider – aws_instance
    url: https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance
prev: /lab2/lab2/
next: /lab4/lab4/
---

---

### Tarea 1. Crear la red pública mínima (VPC + Subnet + IGW + Route Table + Route)

Definiremos una **VPC 10.50.0.0/16**, una **subred pública 10.50.1.0/24**, un **Internet Gateway (IGW)**, una **tabla de ruteo pública** con **ruta 0.0.0.0/0** hacia el IGW, y la **asociación** con la subred. Por el momento sin variables.

#### Tarea 1.1. Abrir `main.tf`

- **Paso 1.** Abre **Visual Studio Code**.

- **Paso 2.** Ve a la carpeta `TERRALABS`.

- **Paso 3.** Abre el archivo `main.tf`.

#### Tarea 1.2. Agregar los recursos de red

- **Paso 4.** Pega el siguiente bloque **debajo** de la `definición del provider` (**en la linea 11**):

  > **IMPORTANTE:** El codigo viene con comentarios para que sea mas facil encontrar donde se deben ajustar los valores. **Siempre en minusculas**
  {: .lab-note .important .compact}

  ```hcl
  # VPC Personalizada
  resource "aws_vpc" "lab_vpc" {
    cidr_block           = "10.50.0.0/16"
    enable_dns_support   = true
    enable_dns_hostnames = true
    tags = {
      Name = "vpc-lab-xxx" # <-- reemplaza xxx por tus iniciales
    }
  }

  # Internet Gateway
  resource "aws_internet_gateway" "lab_igw" {
    vpc_id = aws_vpc.lab_vpc.id
    tags = {
      Name = "igw-lab-xxx" # <-- reemplaza xxx por tus iniciales
    }
  }

  # Subred pública
  resource "aws_subnet" "lab_public_subnet" {
    vpc_id                  = aws_vpc.lab_vpc.id
    cidr_block              = "10.50.1.0/24"
    availability_zone       = "us-west-2a"
    map_public_ip_on_launch = true
    tags = {
      Name = "subnet-public-lab-xxx" # <-- reemplaza xxx por tus iniciales
    }
  }

  # Tabla de ruteo pública
  resource "aws_route_table" "lab_public_rt" {
    vpc_id = aws_vpc.lab_vpc.id
    tags = {
      Name = "rt-public-lab-xxx" # <-- reemplaza xxx por tus iniciales
    }
  }

  # Ruta por defecto hacia Internet
  resource "aws_route" "lab_public_default_route" {
    route_table_id         = aws_route_table.lab_public_rt.id
    destination_cidr_block = "0.0.0.0/0"
    gateway_id             = aws_internet_gateway.lab_igw.id
  }

  # Asociar la subred a la tabla de ruteo pública
  resource "aws_route_table_association" "lab_public_rta" {
    subnet_id      = aws_subnet.lab_public_subnet.id
    route_table_id = aws_route_table.lab_public_rt.id
  }
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[0] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 2. Declarar un Security Group para HTTP

Abriremos **80/TCP** de entrada y todo el egreso para permitir el acceso de prueba.

#### Tarea 2.1. Agregar `aws_security_group`

- **Paso 5.** Ahora aproximadamente en la **linea 61** inserta el siguiente bloque para mandar a crear el SG.

  ```hcl
  resource "aws_security_group" "web_sg" {
    name        = "websg-xxx" # <-- reemplaza xxx por tus iniciales
    description = "Permite HTTP para laboratorio"
    vpc_id      = aws_vpc.lab_vpc.id

    ingress {
      description = "HTTP"
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
      Name = "websg-xxx" # <-- reemplaza xxx por tus iniciales
    }
  }
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[1] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 3. Declarar una instancia EC2 que ejecute Nginx en contenedor (sin variables)

La instancia usará Amazon Linux 2, instalará Docker en *user_data* y expondrá **HTTP/80**. Se ubicará en la **subred pública** creada.

#### Tarea 3.1. Obtener el AMI más reciente de Amazon Linux 2

- **Paso 6.** Aproximadamente en la **linea 83** debajo del ultimo bloque agregado añade el *data source* para leer la AMI.

  ```hcl
  data "aws_ami" "al2" {
    most_recent = true
    owners      = ["amazon"]

    filter {
      name   = "name"
      values = ["amzn2-ami-hvm-*-x86_64-gp2"]
    }
  }
  ```
  {% include step_image.html %}

#### Tarea 3.2. Crear `aws_instance` con *user_data*

- **Paso 7.** Al final del archivo agrega el bloque de codigo que define la instancia de EC2.

  ```hcl
  resource "aws_instance" "web_ec2" {
    ami                         = data.aws_ami.al2.id
    instance_type               = "t3.micro"
    subnet_id                   = aws_subnet.lab_public_subnet.id
    vpc_security_group_ids      = [aws_security_group.web_sg.id]
    associate_public_ip_address = true

    user_data = <<-EOF
                #!/bin/bash
                yum update -y
                amazon-linux-extras install docker -y || yum install -y docker
                systemctl enable docker
                systemctl start docker
                docker run -d --name hello -p 80:80 nginx:stable
                EOF

    tags = {
      Name = "ec2-web-xxx" # <-- reemplaza xxx por tus iniciales
    }
  }
  ```

{% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[2] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---
