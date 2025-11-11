---
layout: lab
title: "Práctica 9: Estructurar un Proyecto Básico en Terraform"
permalink: /lab9/lab9/
images_base: /labs/lab9/img
duration: "20 minutos"
objective:
  - Reorganizar el proyecto `TERRALABS` separando configuración en `main.tf`, `provider.tf`, `variables.tf`, `outputs.tf` y `terraform.tfvars` para mejorar mantenibilidad y reutilización en AWS.
prerequisites:
  - Haber completado las prácticas anteriores con recursos creados en AWS.
  - Carpeta `TERRALABS` operativa (con o sin `terraform init` previo).
introduction:
  - Aplicarás buenas prácticas de organización en Terraform proveedor y versiones aislados, variables y salidas centralizadas, y recursos limpios en `main.tf`. Mantendremos la VPC pública mínima, SG HTTP y EC2 con Nginx que vienes usando.
slug: lab9
lab_number: 9
final_result: |
  - Proyecto organizado con archivos separados por función.
  - `terraform validate/plan/apply` ejecuta correctamente con la nueva estructura.
notes:
  - Ejecuta `terraform fmt` tras mover código para formatear.
  - Actualiza `.gitignore` para evitar subir estado/secretos.
references:
  - text: Terraform – Project Structure Tips
    url: https://developer.hashicorp.com/terraform/language/files
  - text: Terraform AWS Provider – Docs
    url: https://registry.terraform.io/providers/hashicorp/aws/latest/docs
  - text: Terraform – Variables & Outputs
    url: https://developer.hashicorp.com/terraform/language/values/variables
prev: /lab8/lab8/
next: /lab10/lab10/
---

---

## Instrucciones

### Tarea 1. Crear estructura de archivos organizada 

Se crearán archivos separados para definir cada parte lógica del proyecto.

#### Tarea 1.1. Creación de los archivos

- **Paso 1.** En Visual Studio Code, dentro de `TERRALABS`, asegúrate de tener los siguientes archivos:

  > **IMPORTANTE:** El archivo que no tengas creado de la lista. Crealo
  {: .lab-note .important .compact}

  ```
  main.tf
  provider.tf
  terraform.tfvars
  variables.tf
  outputs.tf
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[0] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 2. Mover el proveedor a `provider.tf`

Colocaremos la definición del proveedor **AWS** y la versión mínima requerida de Terraform.

#### Tarea 2.1. Definir el archivo `provider.tf`

- **Paso 2.** Corta la sección del proveedor desde `main.tf` y pégala en `provider.tf` (asegúrate de eliminarla de `main.tf`).

  > **NOTA:** Puedes apoyarte de la imagen que muestra que ya no existe en el archivo **main.tf** y ahora esta en **provider.tf**
  {: .lab-note .info .compact}

  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[1] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 3. Mover variables a `variables.tf`

Todas las variables utilizadas deben definirse en este archivo (incluida la sensible).

#### Tarea 3.1. Definir el archivo `variables.tf`

- **Paso 3.** Corta la sección de variables desde `main.tf` y pégala en `variables.tf` (No deben de existir en `main.tf`).

  > **NOTA:** Puedes apoyarte de la imagen que muestra que ya no existe en el archivo **main.tf** y ahora esta en **variables.tf**
  {: .lab-note .info .compact}
  > **IMPORTANTE:** No te preocupes si notas algunos resaltados en rojo como errores, es normal en lo que pasas las variables.
  {: .lab-note .important .compact}

  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[2] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 4. Mover outputs a `outputs.tf`

Centralizaremos los bloques `output` en un solo archivo.

#### Tarea 4.1. Definir el archivo `outputs.tf`

- **Paso 4.** Corta la sección de outputs de `main.tf` y pégala en `outputs.tf` (elíminala de `main.tf`).

  > **NOTA:** Puedes apoyarte de la imagen que muestra que ya no existe en el archivo **main.tf** y ahora esta en **outputs.tf**
  {: .lab-note .info .compact}
  > **IMPORTANTE:** Los outpues deben estar de bajo del ultimo recurso que es la **Instancia de EC2**
  {: .lab-note .important .compact}


  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[3] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 5. Dejar solo `locals` y recursos en `main.tf`

`main.tf` se enfocará en nombres/etiquetas (`locals`) y recursos (VPC, Subred, IGW, Tabla, Ruta, SG, EC2).

#### Tarea 5.1. Definir el archivo `main.tf`

- **Paso 5.** Mantén únicamente `locals` y los recursos (ajusta a tu diseño si ya los tenías):

  {% include step_image.html %}
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[4] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 6. Confirmar ejecución con estructura modular

Verifica que Terraform reconoce la nueva estructura.

#### Tarea 6.1. Ejecuta en la terminal

- **Paso 6.** Abre la terminal y reformatea archivos para mantener una limpieza:

  > **NOTA:** Puede haber mas de un archivo reformateado o puede que no detecte alguno.
  {: .lab-note .info .compact}

  ```bash
  terraform fmt
  ```
  {% include step_image.html %}

- **Paso 7.** Valida la sintaxis de los archivos con el siguiente comando.

  ```bash
  terraform validate
  ```
  {% include step_image.html %}

- **Paso 8.** Previsualiza cambios (si es que existe alguno) con el siguiente comando.

  > **NOTA:** Recuerda que la salida es extensa, la imagen representa una parte pero no debe haber errores.
  {: .lab-note .info .compact}

  ```bash
  terraform plan
  ```
  {% include step_image.html %}

- **Paso 9.** Aplica los cambios de la infraestructura para reconfirmar que todo sigue de la misma manera.

  ```bash
  terraform apply -auto-approve
  ```
  {% include step_image.html %}

- **Paso 10.** Espera unos segundos y verifica la URL de **NGINX** que responde la pagina princial del servicio.

  {% include step_image.html %}

- **Paso 11.** Cuando hayas terminado de analiza, ejecuta el siguiente comando para destruir la infraestructura.

  ```bash
  terraform destroy -auto-approve
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[5] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}