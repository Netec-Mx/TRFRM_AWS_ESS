---
layout: lab
title: "Práctica 6: Implementación del Ciclo de Vida con Terraform CLI"
permalink: /lab6/lab6/
images_base: /labs/lab6/img
duration: "35 minutos"
objective:
  - Ejecutar el ciclo de vida completo con Terraform CLI en AWS `init`, `plan`, `apply`, consulta de `outputs` y `destroy`, utilizando el `main.tf` de las prácticas anteriores.
prerequisites:
  - Carpeta `TERRALABS` con `main.tf` configurado.
  - Proveedor `aws`
  - Variables y `locals` (Práctica 4)
  - Red propia `aws_vpc`, `aws_subnet` (pública), `aws_internet_gateway`, `aws_route_table`, `aws_route`, `aws_route_table_association`
  - Seguridad `aws_security_group` (HTTP/80)
  - Cómputo `aws_instance` (EC2 con Nginx)
  - Bloques `output` (Práctica 5) `vpc_id`, `public_subnet_id`, `security_group_id`, `instance_id`, `instance_public_ip`, `instance_public_dns`, `nginx_url`
  - Variable `initials` configurada (por defecto en `locals`/`variables` o via CLI).
  - Terraform CLI instalado y AWS CLI autenticado (`aws configure` o `aws sso login`).
introduction:
  - Completarás el flujo típico de Terraform en AWS inicializar, planear, aplicar, consultar salidas y destruir. Esto validará que tu definición sea reproducible de principio a fin.
slug: lab6
lab_number: 6
final_result: |
  - Se ejecuta el ciclo de vida: `init` → `plan` (con y sin override de variables) → `apply` → `output` → `destroy` (opcional).
  - Verás en consola los `outputs` de tu infraestructura (IP/DNS/URL de Nginx).
notes:
  - Usa `terraform validate` para chequear errores antes de `plan`.
  - El comando `terraform show` muestra el estado actual y atributos.
  - El comando `terraform state list` lista los recursos administrados por Terraform.
references:
  - text: Terraform CLI – Commands
    url: https://developer.hashicorp.com/terraform/cli
  - text: Terraform – Output Values
    url: https://developer.hashicorp.com/terraform/language/values/outputs
  - text: Terraform – Variables
    url: https://developer.hashicorp.com/terraform/language/values/variables
prev: /lab5/lab5/
next: /lab7/lab7/
---

---

### Tarea 1. Inicializar el proyecto

Prepara Terraform para usar los proveedores y descargar dependencias.

#### Tarea 1.1. Ejecutar `terraform init`

- **Paso 1.** Abre terminal en el directorio `TERRALABS` y ejecuta el siguiente comando:

  ```bash
  terraform init
  ```

- **Paso 2.** Revisa la salida hasta ver el mensaje similar a: `Terraform has been successfully initialized!`

  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[0] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 2. Revisar el plan de ejecución

Observa lo que Terraform creará/modificará sin aplicar aún.

#### Tarea 2.1. Ejecutar `terraform plan`

- **Paso 3.** Genera un plan base:

  ```bash
  terraform plan
  ```

- **Paso 4.** Revisa el resumen (add/change/destroy).

  > **IMPORTANTE:** El **plan** es muy extenso las imagenes representan el inicio de cada bloque que se creara. Tu puedes analizar el contenido.
  {: .lab-note .important .compact}

  {% include step_image.html %}
  {% include step_image.html %}
  {% include step_image.html %}
  {% include step_image.html %}
  {% include step_image.html %}
  {% include step_image.html %}
  {% include step_image.html %}
  {% include step_image.html %}

- **Paso 5.** (Opcional) Forzar un valor de variable desde CLI (por ejemplo, cambiar `initials`):

  > **NOTA:** Reemplaza las `xxx` por un valor distinto para ver cambios en nombres/etiquetas. No es necesario aplicarlo, es una manera de inyectar valores sin alterar las variables.
  {: .lab-note .info .compact}

  ```bash
  terraform plan -var="initials=xxx"
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[1] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 3. Aplicar los cambios

Crearás la VPC pública mínima, el SG HTTP y la EC2 con Nginx.

#### Tarea 3.1. Ejecutar `terraform apply`

- **Paso 6.** Aplica la configuración de la infraestructura escribe el siguiente comando:

  > **NOTA:** Una alternativa no interactiva es usar el comando **`terraform apply -auto-approve`** 
  {: .lab-note .info .compact}

  ```bash
  terraform apply
  ```

- **Paso 7.** Revisa el plan mostrado (similar a `terraform plan`).

- **Paso 8.** Confirma con `yes` cuando se solicite y ejecuta **Enter**.

  {% include step_image.html %}

- **Paso 9.** Observa los logs de creación y espera el **Apply complete!**

  > **NOTA:** No debe generarte errores.
  {: .lab-note .info .compact}

  {% include step_image.html %}

- **Paso 10.** Revisa los `outputs` en consola al finalizar.

  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[2] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 4. Consultar los outputs

Puedes consultar las salidas sin volver a aplicar.

#### Tarea 4.1. Ejecutar `terraform output`

- **Paso 11.** Ejecuta el siguiente comando para mostrar todas las salidas:

  > **NOTA:** Siempre y cuando la infraestructura ya este aplicada.
  {: .lab-note .info .compact}

  ```bash
  terraform output
  ```
  {% include step_image.html %}

#### Tarea 4.2. Consultar una salida específica (opcional)

- **Paso 12.** Por ejemplo, obtener la URL de Nginx:

  ```bash
  terraform output nginx_url
  ```
  {% include step_image.html %}

- **Paso 13.** Abre la URL de **nginx_url** en tu navegador para validar la página por defecto de Nginx.

  > **IMPORTANTE:** Verifica que la URL se abra en **http** y no **https**.
  {: .lab-note .important .compact}

  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[3] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}

---

### Tarea 5. Destruir los recursos (opcional)

Elimina toda la infraestructura creada para limpiar el entorno.

#### Tarea 5.1. Ejecutar `terraform destroy`

- **Paso 14.** Destruye todo lo administrado por Terraform ejecuta el siguiente comando.

  > **NOTA:** Si prefieres confirmación manual, omite `-auto-approve`.
  {: .lab-note .info .compact}

  ```bash
  terraform destroy -auto-approve
  ```
  {% include step_image.html %}

{% assign results = site.data.task-results[page.slug].results %}
{% capture r1 %}{{ results[4] }}{% endcapture %}
{% include task-result.html title="Tarea finalizada" content=r1 %}