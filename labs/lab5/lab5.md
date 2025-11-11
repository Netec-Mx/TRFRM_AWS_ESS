---
layout: lab
title: "Práctica 5: Creación de outputs en Terraform"
permalink: /lab5/lab5/
images_base: /labs/lab5/img
duration: "10 minutos"
objective:
  - Declarar salidas (`output`) en `main.tf` para exponer valores clave tras una ejecución IDs de red y seguridad, y datos públicos de la instancia EC2 (IP/DNS/URL).
prerequisites:
  - Haber completado la Práctica 4 (variables y `locals` en AWS).
  - AWS CLI autenticado (`aws configure`).
introduction:
  - Agregarás bloques `output` al final de `main.tf` para mostrar información útil tras una ejecución. Esto facilita validar el despliegue y reutilizar valores (por ejemplo, consumir la IP pública desde otros módulos o scripts).
slug: lab5
lab_number: 5
final_result: |
  - El archivo `main.tf` contiene outputs listos para mostrar:
    - `vpc_id`, `public_subnet_id`
    - `security_group_id`
    - `instance_id`, `instance_public_ip`, `instance_public_dns`
    - `nginx_url`
  - Al aplicar, Terraform imprimirá estos valores en la terminal.
notes:
  - No se ejecutará `terraform apply` en esta práctica.
  - Puedes inspeccionar salidas con `terraform output` (si ya existe estado aplicado).
  - El comando `terraform output <nombre>` devuelve un valor específico.
references:
  - text: Terraform – Output Values
    url: https://developer.hashicorp.com/terraform/language/values/outputs
  - text: Terraform AWS Provider – aws_instance (attributes)
    url: https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance
  - text: Terraform AWS Provider – aws_vpc
    url: https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc
prev: /lab4/lab4/
next: /lab6/lab6/
---

---

### Tarea 1. Declarar salidas (`outputs`) en el archivo `main.tf`

Añadiremos bloques `output` al final del archivo `main.tf` para mostrar información clave del entorno creado en la práctica 4.

#### Tarea 1.1. Abrir el archivo `main.tf`

- **Paso 1.** Abre **Visual Studio Code**.

- **Paso 2.** Abre la carpeta `TERRALABS`.

- **Paso 3.** Abre el archivo `main.tf`.

#### Tarea 1.2. Añadir los bloques `output` al final del archivo

- **Paso 4.** Desplázate al **final** del archivo `main.tf` y agrega los siguientes bloques.

  ```hcl
  # --- Red ---
  output "vpc_id" {
    description = "ID de la VPC creada"
    value       = aws_vpc.lab_vpc.id
  }

  output "public_subnet_id" {
    description = "ID de la subred pública"
    value       = aws_subnet.lab_public_subnet.id
  }

  # --- Seguridad ---
  output "security_group_id" {
    description = "ID del Security Group HTTP"
    value       = aws_security_group.web_sg.id
  }

  # --- Instancia EC2 ---
  output "instance_id" {
    description = "ID de la instancia EC2"
    value       = aws_instance.web_ec2.id
  }

  output "instance_public_ip" {
    description = "IP pública de la instancia EC2"
    value       = aws_instance.web_ec2.public_ip
  }

  output "instance_public_dns" {
    description = "DNS público de la instancia EC2"
    value       = aws_instance.web_ec2.public_dns
  }

  # URL de prueba para Nginx (puerto 80)
  output "nginx_url" {
    description = "URL HTTP para validar Nginx"
    value       = "http://${aws_instance.web_ec2.public_ip}"
  }
  ```

  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[0] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}