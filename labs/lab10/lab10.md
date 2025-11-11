---
layout: lab
title: "Ejemplos adicionales de Proyectos en Terraform y Buenas Prácticas de Organización"
permalink: /lab10/lab10/
images_base: /labs/lab10/img
duration: "Sin tiempo"
objective:
  - Presentar y comparar tres estructuras de proyecto en Terraform (simple, por entornos y modular) con buenas prácticas de organización, versionado y reutilización de código aplicables a AWS.
prerequisites:
  - Haber completado las prácticas anteriores y comprender el flujo básico (init/plan/apply/destroy).
  - Carpeta `TERRALABS` operativa (opcional) para replicar los ejemplos.
introduction:
  - En esta guía revisarás tres patrones de organización de proyectos Terraform monolítico simple, separación por entornos y arquitectura modular con reuso de módulos.
slug: lab10
lab_number: 10
final_result: |
  - Tres plantillas de estructura de carpetas listas para adoptar:
    1) Proyecto simple (monolítico),
    2) por entornos (`dev/test/prod`),
    3) modular + reutilizable.
  - Criterios claros para elegir la estructura adecuada y migrar desde proyectos pequeños a diseños modulares.
notes:
  - Usa estado remoto (S3 + DynamoDB) para trabajo en equipo; evita commitear archivos de estado/secretos.
  - Estandariza nombres (tags, prefijos/sufijos) y formatea con `terraform fmt`.
references:
  - text: Terraform – Files & Directory Layout
    url: https://developer.hashicorp.com/terraform/language/files
  - text: Terraform – Modules (Design & Reuse)
    url: https://developer.hashicorp.com/terraform/language/modules
  - text: Backend S3 + DynamoDB (Remote State & Locking)
    url: https://developer.hashicorp.com/terraform/language/settings/backends/s3
prev: /lab9/lab9/
next: /lab1/lab1/
---

---

# Ejemplos adicionales de Proyectos en Terraform y Buenas Prácticas de Organización

  > **IMPORTANTE:** LA SIGUIENTE INFORMACIÓN ES SOLO DE REFERENCIA NO ES UNA PRACTICA.
  {: .lab-note .important .compact}

## Estructura 1: Proyecto Simple (Monolítico)

### 🔹 Usos: proyectos individuales, pruebas, infraestructura puntual.

  ```bash
  simple-proyecto/
  ├── main.tf
  ├── variables.tf
  ├── outputs.tf
  ├── terraform.tfvars
  ├── .gitignore
  └── README.md
  ```

  ---

## Estructura 2: Por Entornos (`dev`, `test`, `prod`)

### 🔹 Usos: cuando necesitas mantener configuraciones separadas por entorno.

  ```bash
  entornos-proyecto/
  ├── modules/
  │   └── red/
  │       ├── main.tf
  │       ├── variables.tf
  │       └── outputs.tf
  ├── dev/
  │   ├── main.tf
  │   ├── terraform.tfvars
  │   └── backend.tf
  ├── test/
  │   ├── main.tf
  │   ├── terraform.tfvars
  │   └── backend.tf
  ├── prod/
  │   ├── main.tf
  │   ├── terraform.tfvars
  │   └── backend.tf
  └── .gitignore
  ```

---

## Estructura 3: Modular y Escalable (con reuso de módulos)

### 🔹 Usos: para infraestructura compleja, reutilizable o multiplataforma.

  ```bash
  modular-proyecto/
  ├── modules/
  │   ├── network/
  │   │   ├── main.tf
  │   │   ├── variables.tf
  │   │   └── outputs.tf
  │   └── compute/
  │       ├── main.tf
  │       ├── variables.tf
  │       └── outputs.tf
  ├── environments/
  │   └── dev/
  │       ├── main.tf
  │       ├── terraform.tfvars
  │       └── backend.tf
  ├── .gitignore
  └── README.md
  ```