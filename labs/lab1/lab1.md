---
layout: lab
title: "Práctica 1: Instalación y verificación de Terraform"
permalink: /lab1/lab1/
images_base: /labs/lab1/img
duration: "25 minutos"
objective:
  - Instalar Terraform en Windows y validar su instalación correctamente. Además, preparar el entorno para trabajar con AWS usando AWS CLI.
prerequisites:
  - Sistema operativo Windows 10/11 (64 bits).
  - Acceso a una cuenta con privilegios de administrador.
  - Acceso a Internet.
  - Cuenta activa en AWS.
introduction:
  - En esta practica instalarás Terraform en Windows, configurarás la variable de entorno `PATH` para ejecutarlo desde cualquier terminal y validarás la instalación desde VS Code utilizando Git Bash como perfil por defecto.
slug: lab1 
lab_number: 1 
final_result: > 
  Terraform quedo instalado y operativo en Windows, accesible desde cualquier terminal. El entorno esta completamente listo para comenzar las siguientes prácticas de Infraestructura como Código en AWS.
notes: 
  - Descarga la versión **Windows AMD64 (.zip)** desde el sitio oficial.
  - Tras modificar el `PATH`, **cierra y reabre** VS Code/terminal para aplicar cambios.
  - Si usas antivirus/EDR, permite la ejecución de `terraform.exe`.
references:
  - text: Instalar Terraform (Windows)
    url: https://developer.hashicorp.com/terraform/install#windows
  - text: Configurar perfiles de terminal en VS Code
    url: https://code.visualstudio.com/docs/terminal/basics#_default-profile
  - text: Terraform CLI – Comandos
    url: https://developer.hashicorp.com/terraform/cli
prev: /lab10/lab10/       
next: /lab2/lab2/
---


---

### Tarea 1. Descargar e Instalar Terraform

En esta sección se descargará Terraform desde la fuente oficial, se instalará y se configurará la variable de entorno `PATH` para poder utilizarlo desde cualquier terminal.

#### Tarea 1.1. Descargar Terraform desde el sitio oficial

- **Paso 1.** Abre tu navegador web y visita el siguiente link: [**Descarga Terraform Aquí**](https://developer.hashicorp.com/terraform/install#windows)

- **Paso 2.** Selecciona el sistema operativo **Windows** y descarga la versión de 64-bit (**AMD64**) en formato `.zip`.

  {% include step_image.html %}

#### Tarea 1.2. Extraer el archivo ZIP

- **Paso 3.** Una vez descargado el archivo, haz clic derecho sobre el archivo `.zip` y selecciona **"Extraer todo..."**.

- **Paso 4.** Extrae el contenido en una carpeta de fácil acceso, en el disco local **`C:\`** 

  > **NOTA:** Si la carpeta `Terraform` no existe, creala en el directorio `C:\`
  {: .lab-note .info .compact}

  ```
  C:\Terraform
  ```
  {% include step_image.html %}

#### Tarea 1.3. Agregar Terraform al PATH del sistema

- **Paso 5.** Presiona `Win + R`, escribe `sysdm.cpl` y presiona **Enter** para abrir las **Propiedades del sistema.**

- **Paso 6.** Ve a la pestaña **"Opciones avanzadas"** o **"Avanzadas"** y haz clic en **"Variables de entorno..."**.

  {% include step_image.html %}

- **Paso 7.** En la sección **"Variables del sistema"**, busca la variable llamada `Path` y haz clic en **Editar**.

  {% include step_image.html %}

- **Paso 8.** En la ventana que aparece, haz clic en **"Nuevo"** y agrega la ruta donde descomprimiste Terraform, por ejemplo:

  ```
  C:\Terraform
  ```
  {% include step_image.html %}

- **Paso 9.** Haz clic en **OK** o **Aceptar** en todas las ventanas para guardar los cambios.

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[0] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 2. Verificar la Instalación de Terraform

Se comprobará que Terraform esté disponible en la terminal de Git Bash integrada en VS Code.

#### Tarea 2.1. Abrir Visual Studio Code

- **Paso 10.** Abre **Visual Studio Code**, puede estar como acceso directo en el escritorio o desde las **aplicaciones** del sistema operativo de **Windows**.

- **Paso 11.** Abre la terminal integrada de Visual Studio Code **(menú `Terminal > New Terminal`)** o la combinacion de teclas **`CTRL + ñ`**, tambien puedes dar clic en el botón como lo muestra la imagen.

  {% include step_image.html %}

- **Paso 12.** En la terminal abierta da clic en la pestaña y luego en **Select Default Profile**

  {% include step_image.html %}

- **Paso 13.** En la ventana superior selecciona la opción **Git Bash**.

  {% include step_image.html %}

- **Paso 14.** Ahora cierra el software de **Visual Studio Code** y vuelvelo abrir para que tomen efectos los cambios y **repite el paso 11**.

#### Tarea 2.2. Verificar versión instalada

- **Paso 15.** Con la Terminal abierta ejecuta el siguiente comando:

  ```bash
  terraform -version
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[1] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}