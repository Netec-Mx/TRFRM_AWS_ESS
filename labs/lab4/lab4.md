---
layout: lab
title: "Práctica 4: Creación y Asignación de Variables en Terraform"
permalink: /lab4/lab4/
images_base: /labs/lab4/img
duration: "15 minutos"
objective:
  - Refactorizar `main.tf` para usar variables y `locals` que parametrizan la red (VPC/Subred/IGW/Tabla/Ruta), el Security Group y la instancia EC2, manteniendo la funcionalidad de la práctica 3.
prerequisites:
  - Haber completado la Práctica 3 (VPC pública propia, SG HTTP y EC2 con Nginx).
  - Proveedor `aws` declarado correctamente en `main.tf`.
  - AWS CLI autenticado (`aws configure`).
  - Carpeta `TERRALABS` con `main.tf`.
introduction:
  - Convertirás los valores fijos de la práctica 3 en variables y `locals` para facilitar la reutilización de CIDRs, zona, nombres con iniciales, tipo de instancia. Mantendrás el mismo resultado final, pero con un `main.tf` más limpio y flexible.
slug: lab4
lab_number: 4
final_result: |
  - El archivo `main.tf` contiene:
    - Variables con valores por defecto y `locals` para nombres.
    - Recursos de red (VPC/Subred/IGW/Tabla/Ruta) referenciando variables.
    - Security Group y EC2 parametrizados (tipo, nombres).
  - Código listo para reutilizar cambiando solo las variables.
notes:
  - Puedes sobrescribir variables con `-var="clave=valor"` al aplicar.
  - Usa tus iniciales en `var.initials` para evitar colisiones.
  - Ajusta `var.az` y `var.vpc_cidr`/`var.public_subnet_cidr` según tu región y diseño.
references:
  - text: Input Variables – Terraform
    url: https://developer.hashicorp.com/terraform/language/values/variables
  - text: Local Values – Terraform
    url: https://developer.hashicorp.com/terraform/language/values/locals
  - text: Terraform AWS Provider – aws_vpc
    url: https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc
prev: /lab3/lab3/
next: /lab5/lab5/
---

---

### Tarea 1. Declarar variables y `locals` en `main.tf`

Crearemos variables para iniciales, red, zona y tipo de instancia; y `locals` para estandarizar nombres/etiquetas.

#### Tarea 1.1. Abrir `main.tf`

- **Paso 1.** Abre **Visual Studio Code**.

- **Paso 2.** Ve a la carpeta `TERRALABS` y abre `main.tf`.

#### Tarea 1.2. Añadir bloques de variables y `locals`

- **Paso 3.** Copia y pega **antes** de los recursos (después del `provider "aws"`).

  > **NOTA:** Aproximadamente en la **linea 11**.
  {: .lab-note .info .compact}

  ```hcl
  # === Variables ===
  variable "initials" {
    description = "Iniciales para los nombres"
    type        = string
    default     = "xxx" # <-- reemplaza por tus iniciales
  }

  variable "vpc_cidr" {
    description = "CIDR de la VPC"
    type        = string
    default     = "10.50.0.0/16"
  }

  variable "public_subnet_cidr" {
    description = "CIDR de la subred pública"
    type        = string
    default     = "10.50.1.0/24"
  }

  variable "az" {
    description = "Availability Zone para la subred pública"
    type        = string
    default     = "us-west-2a"
  }

  variable "instance_type" {
    description = "Tipo de instancia EC2"
    type        = string
    default     = "t3.micro"
  }

  # === Locals (nombres y etiquetas) ===
  locals {
    vpc_name  = "vpc-lab-${var.initials}"
    igw_name  = "igw-lab-${var.initials}"
    sn_name   = "subnet-public-lab-${var.initials}"
    rt_name   = "rt-public-lab-${var.initials}"
    sg_name   = "websg-${var.initials}"
    ec2_name  = "ec2-web-${var.initials}"
  }
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[0] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 2. Refactorizar la red para usar variables/locals

Actualizaremos VPC, Subred, IGW, Tabla y Ruta por defecto para que usen `var.*` y `local.*`.

#### Tarea 2.1. Sustituir los bloques de red

- **Paso 4.** Reemplaza/ajusta tus recursos de red por los siguientes:

  > **NOTA:** Puedes ajustar solo las siguientes lineas:
  - `cidr_block = var.vpc_cidr`
  - `Name = local.vpc_name`
  - `Name = local.igw_name`
  - `cidr_block = var.public_subnet_cidr`
  - `availability_zone = var.az`
  - `Name = local.sn_name`
  - `Name = local.rt_name`
  {: .lab-note .info .compact}

  > **IMPORTANTE:** Tambien puedes sustituir todo el codigo completo, pero con mucho cuidado.
  {: .lab-note .important .compact}

  ```hcl
  # VPC propia
  resource "aws_vpc" "lab_vpc" {
    cidr_block           = var.vpc_cidr
    enable_dns_support   = true
    enable_dns_hostnames = true

    tags = {
      Name = local.vpc_name
    }
  }

  # Internet Gateway
  resource "aws_internet_gateway" "lab_igw" {
    vpc_id = aws_vpc.lab_vpc.id

    tags = {
      Name = local.igw_name
    }
  }

  # Subred pública
  resource "aws_subnet" "lab_public_subnet" {
    vpc_id                  = aws_vpc.lab_vpc.id
    cidr_block              = var.public_subnet_cidr
    availability_zone       = var.az
    map_public_ip_on_launch = true

    tags = {
      Name = local.sn_name
    }
  }

  # Tabla de ruteo pública
  resource "aws_route_table" "lab_public_rt" {
    vpc_id = aws_vpc.lab_vpc.id

    tags = {
      Name = local.rt_name
    }
  }

  # Ruta por defecto hacia Internet
  resource "aws_route" "lab_public_default_route" {
    route_table_id         = aws_route_table.lab_public_rt.id
    destination_cidr_block = "0.0.0.0/0"
    gateway_id             = aws_internet_gateway.lab_igw.id
  }

  # Asociación de subred a tabla pública
  resource "aws_route_table_association" "lab_public_rta" {
    subnet_id      = aws_subnet.lab_public_subnet.id
    route_table_id = aws_route_table.lab_public_rt.id
  }
  ```

  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[1] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 3. Refactorizar Security Group para usar locals

Ajustaremos el SG para HTTP/80 usando `local.sg_name`.

#### Tarea 3.1. Sustituir el bloque del Security Group

- **Paso 5.** Ahora actualiza el grupo de seguridad de AWS, con las variables correspondientes.

  > **NOTA:** Puedes ajustar solo las siguientes lineas:
  - `name = local.sg_name`
  - `Name = local.sg_name`
  {: .lab-note .info .compact}

  > **IMPORTANTE:** Tambien puedes sustituir todo el codigo completo, pero con mucho cuidado.
  {: .lab-note .important .compact}

  ```hcl
  resource "aws_security_group" "web_sg" {
    name        = local.sg_name
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
      Name = local.sg_name
    }
  }
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[2] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 4. Refactorizar la instancia EC2 (tipo/nombres vía variables/locals)

Usaremos `var.instance_type` para la EC2 y `local.ec2_name` para nombres/etiquetas.

#### Tarea 4.1. Mantener el AMI dinámico (data source)

- **Paso 6.** Conserva o pega el *data source* para Amazon Linux 2 más reciente:

  > **IMPORTANTE:** Este bloque no se modifica, solo verifica que siga configurado correctamente.
  {: .lab-note .important .compact}

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

#### Tarea 4.2. Sustituir `aws_instance` para usar variables/locals

- **Paso 7.** Actualiza el bloque del recurso de la Instancia de Amazon EC2.

  > **NOTA:** Puedes ajustar solo las siguientes lineas:
  - `instance_type = var.instance_type`
  - `Name = local.ec2_name`
  {: .lab-note .info .compact}

  > **IMPORTANTE:** Tambien puedes sustituir todo el codigo completo, pero con mucho cuidado.
  {: .lab-note .important .compact}

  ```hcl
  resource "aws_instance" "web_ec2" {
    ami                         = data.aws_ami.al2.id
    instance_type               = var.instance_type
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
      Name = local.ec2_name
    }
  }
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[3] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---
