---
layout: lab
title: "Práctica 8: Crear archivo .tfvars con claves y etiqueta sensible"
permalink: /lab8/lab8/
images_base: /labs/lab8/img
duration: "20 minutos"
objective:
  - Separar valores en `terraform.tfvars` y usar variables sensibles (`sensitive = true`) para ocultar secretos en la salida. Se aplicará a los recursos de AWS definidos en prácticas previas (VPC/Subred/IGW/Tabla/Ruta, SG y EC2).
prerequisites:
  - Haber completado las prácticas 4 y 5 (variables/locals y outputs).
  - Carpeta `TERRALABS` con `main.tf` funcional.
  - Recursos en `main.tf` `aws_vpc`, `aws_subnet`, `aws_internet_gateway`, `aws_route_table`, `aws_route`, `aws_route_table_association`, `aws_security_group`, `aws_instance`.
introduction:
  - Externalizarás valores en `terraform.tfvars`, añadirás una variable sensible que simula una clave privada y comprobarás cómo Terraform oculta su valor en los `outputs`.
slug: lab8
lab_number: 8
final_result: |
  - `main.tf` declara variables (incluida una sensible).
  - `terraform.tfvars` contiene valores (iniciales, entorno, etiqueta y clave secreta).
  - Los recursos usan etiquetas dinámicas según las variables.
  - Al aplicar, la clave sensible aparece como `<sensitive>` en la salida.
notes:
  - Terraform carga automáticamente `terraform.tfvars`.
  - No subas `terraform.tfvars` a Git; añádelo a `.gitignore`.
  - Para verificar un output sensible `terraform output clave_api` → `<sensitive>`.
references:
  - text: Terraform – Input Variables
    url: https://developer.hashicorp.com/terraform/language/values/variables
  - text: Terraform – Variable Definitions (.tfvars)
    url: https://developer.hashicorp.com/terraform/language/values/variables#variable-definitions-files
  - text: Terraform – Output Values (sensitive)
    url: https://developer.hashicorp.com/terraform/language/values/outputs#sensitive-outputs
prev: /lab7/lab7/
next: /lab9/lab9/
---

---

### Tarea 1. Declarar nuevas variables en `main.tf`

Se añadirán definiciones de variables que serán cargadas desde `terraform.tfvars`.

#### Tarea 1.1. Abrir y modificar `main.tf`

- **Paso 1.** Abre el archivo `main.tf` en Visual Studio Code.

- **Paso 2.** Añade las siguientes variables al inicio (sustituyendo también la variable `initials`).

  > **IMPORTANTE:** No borrar el resto de las variables. Puedes apoyarte de la imagen. 
  {: .lab-note .important .compact}

  ```hcl
  variable "initials" {
    description = "Iniciales del estudiante"
    type        = string
  }

  variable "env" {
    description = "Entorno de despliegue (dev, test, prod)"
    type        = string
  }

  variable "custom_tag" {
    description = "Etiqueta personalizada para los recursos"
    type        = string
  }

  variable "secreto_api" {
    description = "Simulación de una clave sensible"
    type        = string
    sensitive   = true
  }
  ```
  {% include step_image.html %}

#### Tarea 1.2. Confirmar/ajustar `locals` (opcional)

- **Paso 3.** Si usas nombres estandarizados con iniciales, asegúrate de tener algo similar (ajústalo a tu diseño):

  > **NOTA:** Estas variables ya las tienes, solo puedes verificar que esten correctas.
  {: .lab-note .info .compact}

  ```hcl
  locals {
    vpc_name = "vpc-lab-${var.initials}"
    sn_name  = "subnet-public-lab-${var.initials}"
    igw_name = "igw-lab-${var.initials}"
    rt_name  = "rt-public-lab-${var.initials}"
    sg_name  = "websg-${var.initials}"
    ec2_name = "ec2-web-${var.initials}"
  }
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[0] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 2. Crear y configurar el archivo `terraform.tfvars`

Este archivo contendrá los valores de las variables anteriores, incluidas las sensibles.

#### Tarea 2.1. Crear `terraform.tfvars` en `TERRALABS`

- **Paso 4.** Dentro de la carpeta `TERRALABS` crea un archivo llamado `terraform.tfvars`.

  ```bash
  terraform.tfvars
  ```
  {% include step_image.html %}

- **Paso 5.** Copia y pega el siguiente bloque (sustituye `xxx` por tus iniciales):

  > **NOTA:** Pueden ser las mismas iniciales que has usado en todo el curso.
  {: .lab-note .info .compact}

  ```hcl
  initials    = "xxx"
  env         = "dev"
  custom_tag  = "Terraform_AWS_Lab"
  secreto_api = "ClaveSuperPrivada123!"
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[1] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 3. Usar las variables y proteger la clave secreta

Aplicaremos las variables a las etiquetas de recursos y agregaremos un `output` sensible.

#### Tarea 3.1. Usar variables en los recursos

- **Paso 6.** En cada recurso (`aws_vpc`, `aws_subnet`, `aws_internet_gateway`, `aws_route_table`, `aws_security_group`, `aws_instance`) agrega/ajusta un bloque `tags` como:

  > **IMPORTANTE:** Recuerda que ya tienes la sección de **tags** en cada recurso solo es agregar las 2 etiquetas **environment** y **proyecto** con la informacion. Con mucho cuidado puedes editarlo.
  {: .lab-note .important .compact}

  ```hcl
  tags = {
    environment = var.env
    proyecto    = var.custom_tag
  }
  ```
  {% include step_image.html %}

#### Tarea 3.2. Crear output sensible

- **Paso 7.** Al final del archivo `main.tf`, añade el `output` sensible:

  ```hcl
  output "clave_api" {
    description = "Este valor es sensible y no debe mostrarse en CLI"
    value       = var.secreto_api
    sensitive   = true
  }
  ```
  {% include step_image.html %}

#### Tarea 3.3. Probar con plan/apply

- **Paso 8.** Ejecuta el siguiente comando para validar las modificaciones.

  ```bash
  terraform plan
  ```

- **Paso 9.** Revisa las etiquetas dinámicas en los recursos y verifica que `clave_api` NO muestre su contenido (aparecerá como `<sensitive>`).

  > **IMPORTANTE:** No es necesario realizar el **apply** ya que con el **plan** podemos analizar todo.
  {: .lab-note .important .compact}

  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[2] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}