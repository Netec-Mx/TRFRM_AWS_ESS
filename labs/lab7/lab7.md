---
layout: lab
title: "Práctica 7: Leer e interpretar el archivo `.tfstate` generado"
permalink: /lab7/lab7/
images_base: /labs/lab7/img
duration: "20 minutos"
objective:
  - Comprender la función y estructura del archivo `terraform.tfstate`, visualizar su contenido e interpretar cómo Terraform rastrea los recursos creados (VPC, Subnet, SG, EC2) en AWS.
prerequisites:
  - Haber aplicado previamente una configuración con `terraform apply` (Práctica 6).
  - Contar con `terraform.tfstate` en la carpeta `TERRALABS`.
  - Entorno funcional con Visual Studio Code.
introduction:
  - El archivo de estado mantiene el mapeo entre tu configuración y los recursos reales en AWS. Lo inspeccionarás desde VS Code y con comandos de Terraform para entender su estructura y buenas prácticas de protección.
slug: lab7
lab_number: 7
final_result: |
  - Identificaste y abriste `terraform.tfstate`.
  - Mostraste el estado con `terraform show` y listaste objetos con `terraform state list`.
  - Aplicaste buenas prácticas de respaldo y exclusión en `.gitignore`.
notes:
  - No edites manualmente el `.tfstate` salvo recuperación guiada.
  - Para equipos/CI usa **estado remoto** (S3 + DynamoDB) en lugar de local.
references:
  - text: Terraform – State
    url: https://developer.hashicorp.com/terraform/language/state
  - text: Remote State (S3 backend)
    url: https://developer.hashicorp.com/terraform/language/settings/backends/s3
  - text: terraform state commands
    url: https://developer.hashicorp.com/terraform/cli/commands/state
prev: /lab6/lab6/
next: /lab8/lab8/
---

---

### Tarea 1. Ubicar el archivo `terraform.tfstate`

El archivo `.tfstate` se genera automáticamente después de `terraform apply` en el directorio del proyecto.

#### Tarea 1.1. Verificar su existencia

- **Paso 1.** En Visual Studio Code, abre la carpeta `TERRALABS`.

- **Paso 2.** Confirma que exista el archivo: **`terraform.tfstate`**

  > **NOTA:** El archivo registra el estado actual de los recursos administrados por Terraform (IDs reales en AWS, atributos, dependencias).
  {: .lab-note .info .compact}

  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[0] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 2. Visualizar el contenido del estado

Inspecciona el estado desde VS Code y la CLI para entender su estructura (JSON) y la representación legible.

#### Tarea 2.1. Abrir el archivo en VS Code

- **Paso 3.** Abre `terraform.tfstate` desde el explorador de VSC.

  {% include step_image.html %}

- **Paso 4.** Notaras que el archivo se encuentra vacio ya que no hay infraestructura aplicada, ejecuta el siguiente comando en la terminal con el archivo **tfstate** abierto.

  > **NOTA:** El comando se ejecuta desde el directorio **TERRALABS**
  {: .lab-note .info .compact}

  ```bash
  terraform apply -auto-approve
  ```
  {% include step_image.html %}

- **Paso 5.** Examina la estructura general: `outputs`, `resources` (verás entradas para `aws_vpc`, `aws_subnet`, `aws_security_group`, `aws_instance`, etc.).

  > **NOTA:** El archivo puede ser extenso.
  {: .lab-note .info .compact}

  {% include step_image.html %}

#### Tarea 2.2. Mostrar el estado con `terraform show`

- **Paso 6.** En la terminal, ejecuta el siguiente comando:

  > **NOTA:** Este comando presenta el estado de los recursos en un formato legible, incluyendo nombres, tipos y propiedades.
  {: .lab-note .info .compact}

  > **IMPORTANTE:** La imagen solo muestra una pequeña parte de la salida ya que es muy extensa.
  {: .lab-note .important .compact}

  ```bash
  terraform show
  ```
  {% include step_image.html %}

- **Paso 7.** Puedes listar los objetos rastreados con el siguiente comando.

  ```bash
  terraform state list
  ```
  {% include step_image.html %}

- **Paso 8.** Puedes ver atributos de un recurso específico, por ejemplo la Amazon VPC, con el siguiente comando.

  ```bash
  terraform state show aws_vpc.lab_vpc
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[1] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 3. Recomendación: Seguridad y respaldo del archivo `.tfstate`

Esta tarea se enfoca en concientizar sobre la importancia de **proteger y respaldar correctamente** el archivo `terraform.tfstate`.

#### Tarea 3.1. Evitar exponer el archivo

- **Paso 9.** No subas `terraform.tfstate` a repositorios públicos.

- **Paso 10.** Evita compartirlo por correo o medios inseguros.

#### Tarea 3.2. Hacer respaldos locales (si trabajas en local)

- **Paso 11.** Copia el archivo manualmente antes de operaciones destructivas:

  > **NOTA:** Cambia la fecha si es necesario o pon una aleatoria. El archivo se copiara al escritorio solo como ejemplo, puedes borrarlo si es necesario.
  {: .lab-note .info .compact}

  ```bash
  cp terraform.tfstate ../terraform.tfstate.backup.2025-0x-xx
  ```
  {% include step_image.html %}

#### Tarea 3.3. Ignorar en Git

- **Paso 12.** Crea el archivo llamado `.gitignore`:

  > **NOTA:** El comando se ejecuta dentro del directorio **TERRALABS**
  {: .lab-note .info .compact}

  ```bash
  touch .gitignore
  ```

- **Paso 13.** Añade estas entradas al archivo `.gitignore`:

  > **NOTA:** Este es un ejemplo de lo que no se deberia de cargar al repositorio, en caso de que exista. Recomendación para equipos/CI: usar **estado remoto en S3** con bloqueo en **DynamoDB**.
  {: .lab-note .info .compact}

  ```
  terraform.tfstate
  terraform.tfstate.backup
  .terraform/
  crash.log
  crash.*.log
  ```
  {% include step_image.html %}

- **Paso 14.** Ejecuta el siguiente comando para limpiar la infraestructura.

  ```hcl
  terraform destroy -auto-approve
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[2] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}