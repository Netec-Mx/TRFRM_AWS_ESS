---
layout: lab
title: "Práctica 2: Autenticación de un proveedor"
permalink: /lab2/lab2/
images_base: /labs/lab2/img
duration: "15 minutos"
objective:
  - Configurar la autenticación con el proveedor de AWS en Terraform utilizando AWS CLI y un archivo de configuración `main.tf`, para permitir el despliegue de recursos en la nube desde un entorno local en Windows con Visual Studio Code.
prerequisites:
  - Terraform instalado y funcional en Windows.
  - Visual Studio Code instalado.
  - Git Bash instalado y configurado como terminal por defecto en VS Code.
  - AWS CLI instalado.
  - Tener una cuenta activa en AWS.
introduction:
  - En esta práctica prepararás tu entorno local para trabajar con Terraform sobre AWS iniciarás sesión en AWS CLI (con un perfil por defecto o nombrado), validarás tu identidad, establecerás una región, y crearás un main.tf mínimo para el proveedor aws. Con esto podrás ejecutar terraform init/plan/apply en laboratorios posteriores.
slug: lab2 
lab_number: 2 
final_result: | 
  - AWS CLI autenticado correctamente en tu cuenta de AWS.  
  - Región de trabajo configurada (por defecto).  
  - Proyecto creado en una carpeta organizada.  
  - Archivo `main.tf` configurado con el proveedor `aws` e inicializado con `terraform init`.  
  - Visual Studio Code configurado como entorno de desarrollo para Terraform.
notes:
  - No guardes credenciales en el repositorio; usa perfiles (`~/.aws/credentials`) o variables de entorno.  
  - Para CI/CD, prefiere **roles** y **STS** sobre claves estáticas.  
  - Con **SSO/MFA**, renueva la sesión con `aws sso login` cuando expire.  
  - Verifica identidad y región antes de aplicar `aws sts get-caller-identity` y `aws configure get region`.  
  - Usa perfiles nombrados si trabajas con varias cuentas/regiones.  
references:
  - text: Setting up the AWS CLI
    url: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html
  - text: get-caller-identity
    url: https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html
  - text: Create AWS infrastructure
    url: https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-create
prev: /lab1/lab1/       
next: /lab3/lab3/
---


---

### Tarea 1. Autenticarse con AWS CLI

Se usará **AWS CLI** desde Git Bash para configurar credenciales, seleccionar región y validar la identidad de la sesión. Puedes usar el perfil por defecto o crear un perfil nombrado.

#### Tarea 1.1. Iniciar sesión / Configurar credenciales

- **Paso 1.** Abre **Visual Studio Code** y abre una terminal integrada con Git Bash: `Terminal > New Terminal` o `CTRL + ñ`.

  {% include step_image.html %}

- **Paso 2.** Ejecuta el asistente de configuración de AWS CLI (perfil por defecto):

  > **IMPORTANTE:** La autenticación debe ser con las credenciales asignadas al curso. **La cuenta y región pueden ser diferentes** a tu entorno personal.
  {: .lab-note .important .compact}

  ```bash
  aws configure
  ```

  Ingresa los datos provistos para el curso cuando te lo solicite:
  - `AWS Access Key ID` 
  - `AWS Secret Access Key`
  - `Default region name` (`us-west-2`)
  - `Default output format` (`json`)

  {% include step_image.html %}

- **Paso 3.** Valida tu identidad (debe devolver tu `Account` y `Arn`):

  > **IMPORTANTE:** Por seguridad la imagen oculta ciertos valores. En tu caso debes comparar el resultado con los valores asignados al curso.
  {: .lab-note .important .compact}


  ```bash
  aws sts get-caller-identity
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[0] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 2. Crear archivo de configuración de proveedor `main.tf`

En esta tarea se creará un directorio de proyecto y se escribirá un archivo `main.tf` que contiene la configuración mínima del proveedor `aws`.

#### Tarea 2.1. Crear la estructura del proyecto

- **Paso 4.** En la terminal de **Git Bash**, crea la carpeta para el proyecto llamada **TERRALABS**:

  > **NOTA:** Si es necesario, ajusta el nombre de la carpeta según tu ruta.
  {: .lab-note .info .compact}

  ```bash
  cd ~/Desktop
  mkdir -p TERRALABS
  cd TERRALABS
  ```
  {% include step_image.html %}

- **Paso 5.** Abre esta carpeta desde VS Code: **`File > Open Folder... > Selecciona Desktop y luego TERRALABS`** y confirma la ventana emergente.

  {% include step_image.html %}
  {% include step_image.html %}

#### Tarea 2.2. Crear y editar el archivo `main.tf`

- **Paso 6.** En el explorador de VS Code, crea un nuevo archivo llamado `main.tf`.

  {% include step_image.html %}

- **Paso 7.** Copia y pega el siguiente contenido pra declarar el proveedor AWS mínimo.

  ```hcl
  terraform {
    required_providers {
      aws = {
        source  = "hashicorp/aws"
        version = "~> 5.0"
      }
    }
    required_version = ">= 1.5.0"
  }
  ```
  {% include step_image.html %}

- **Paso 8.** Abre la terminal de **GitBash** (sino la tienes abierta) Inicializa el directorio de trabajo de Terraform para descargar el proveedor de AWS.

  > **NOTA:** Asegurate de estar dentro de la carpeta de **TERRALABS**
  {: .lab-note .info .compact}

  ```bash
  terraform init
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[1] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 3. Extensión de Terraform en VSC

En esta tarea se instalará la extensión oficial de Terraform en Visual Studio Code para facilitar el desarrollo con soporte de sintaxis, autocompletado y validación en archivos `.tf`.

#### Tarea 3.1. Abrir el Marketplace de extensiones

- **Paso 9.** Da clic en el ícono de **Extensiones** del menú lateral izquierdo (o presiona `CTRL + SHIFT + X`).

  {% include step_image.html %}

#### Tarea 3.2. Buscar la extensión oficial

- **Paso 10.** En la barra de búsqueda escribe `Terraform` y da **Enter**.

- **Paso 11.** Localiza la extensión llamada **Terraform** publicada por **HashiCorp**.

  {% include step_image.html %}

#### Tarea 3.3. Instalar la extensión

- **Paso 12.** Da clic en el botón **Install**.

  {% include step_image.html %}

- **Paso 13.** Confirma la ventana emergente clic en **Trust Publisher & Install**.

  > **NOTA:** Sino aparece la ventana emergente, no hay problema pueden continuar.
  {: .lab-note .info .compact}

- **Paso 14.** Espera a que finalice la instalación.

#### Tarea 3.4. Validar que funcione

- **Paso 15.** Abre tu archivo `main.tf`.

- **Paso 16.** Verifica que aparezca **resaltado de sintaxis (colores)** y que se activen sugerencias automáticas (`CTRL + SPACE`).

  > **NOTA:** En caso de que las sugerencias no aparezcan no hay problema puedes continuar. Y si aparecen puedes omitirlas solo es para verificar que este todo correcto.
  {: .lab-note .info .compact}

  {% include step_image.html %}

- **Paso 17.** Por el momento no ejecutamos nada más.

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[2] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}