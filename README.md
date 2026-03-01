# UTH CryptoLab

**UTH CryptoLab** es un **proyecto colaborativo** desarrollado como parte de la **Maestría en Ciberseguridad de la UTH (Universidad Tecnológica de Honduras)**.

Consiste en una **aplicación web estática** (un único `index.html`) que integra **interfaz + estilos + lógica** (HTML/CSS/JavaScript) para ejecutar laboratorios y demostraciones de conceptos de criptografía **directamente en el navegador**, apoyándose en la **Web Crypto API**.

---

## Tabla de contenido

- [Acerca del proyecto](#acerca-del-proyecto)
- [Características principales](#características-principales)
- [Tecnologías](#tecnologías)
- [Cómo funciona (arquitectura)](#cómo-funciona-arquitectura)
- [Módulos / funcionalidades](#módulos--funcionalidades)
  - [Generación de pares de claves](#1-generación-de-pares-de-claves)
  - [Extracción de clave pública](#2-extracción-de-clave-pública)
  - [Firmas digitales y verificación](#3-firmas-digitales-y-verificación)
  - [Criptografía simétrica (AES)](#4-criptografía-simétrica-aes)
  - [Validación de certificados TLS/SSL](#5-validación-de-certificados-tlsssl)
- [Ejecución](#ejecución)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Seguridad, alcance y limitaciones](#seguridad-alcance-y-limitaciones)
- [Contribución (Maestría UTH)](#contribución-maestría-uth)
- [Créditos](#créditos)
- [Licencia](#licencia)

---

## Acerca del proyecto

Este repositorio tiene como objetivo servir como **laboratorio práctico** para:
- comprender conceptos de criptografía aplicada,
- experimentar con generación de claves, cifrado, firmas,
- y reforzar fundamentos de PKI/certificados.

Está orientado a fines **educativos** y a trabajo en equipo (issues, mejoras incrementales, revisión por pares).

---

## Características principales

- Interfaz tipo “paneles” para ejecutar distintas operaciones criptográficas.
- Funciona **sin backend**: solo abres el HTML y lo utilizas.
- Uso de **Web Crypto API** para operaciones soportadas por el navegador.
- Sección educativa para **verificación de cadena de certificados** y **validación de hostname**.
- Permite **copiar resultados** y **descargar llaves** (PEM) generadas.

---

## Tecnologías

- **HTML5 + CSS3 + JavaScript** en un solo archivo.
- **Web Crypto API** (`crypto.subtle`) para:
  - generación de claves (RSA y EC),
  - cifrado/descifrado (RSA-OAEP),
  - firma/verificación (RSASSA-PKCS1-v1_5, RSA-PSS),
  - derivación de claves con PBKDF2,
  - cifrado simétrico AES (GCM/CBC).
- Sin frameworks, sin dependencias de build.

---

## Cómo funciona (arquitectura)

La aplicación vive en un único archivo:

- **UI (HTML)**: paneles para cada módulo.
- **Estilos (CSS interno)**: tema “hacker/terminal” con componentes reutilizables.
- **Lógica (JS interno)**:
  - mantiene estado global mínimo (por ejemplo, tipo de clave actual),
  - invoca Web Crypto API,
  - exporta/importa llaves en formatos estándar (PKCS#8 para privadas, SPKI para públicas),
  - convierte resultados a **PEM** o **Base64** según corresponda,
  - muestra estados (info/success/error) y permite copiar/descargar.

La app enfatiza que:
- muchas operaciones corren “localmente” en el navegador,
- y se muestran comandos equivalentes de **OpenSSL** con fines educativos (para reproducir en terminal).

---

## Módulos / funcionalidades

### 1) Generación de pares de claves

Permite elegir el tipo de clave desde pestañas:

- **RSA** (clásico): tamaños 2048/3072/4096
- **EC / Elliptic Curves** (clásico): P-256 / P-384 / P-521
- **ML-KEM** (post-cuántico) y **ML-DSA** (post-cuántico):  
  Actualmente **se simulan** porque los navegadores (Web Crypto API) aún no soportan estos algoritmos de forma nativa.

Salida:
- exporta y muestra **Private Key** y **Public Key** en formato **PEM**.
- opción para **descargar** un `.pem`.

> Nota: el panel también muestra un “comando OpenSSL” equivalente como referencia, aunque la generación real en el navegador usa Web Crypto API (y no ejecuta OpenSSL).

---

### 2) Extracción de clave pública

Intenta obtener la clave pública a partir de una privada pegada en el formulario.

Limitación importante (educativa):
- En Web Crypto API, **no siempre es posible derivar/reconstruir** la clave pública únicamente desde un PKCS#8 “crudo” importado.
- La app permite la extracción si la clave corresponde a una clave generada **en esa misma sesión** (usa referencias guardadas).

---

### 3) Firmas digitales y verificación

Firma:
- firma un mensaje con una **clave privada PEM**.
- soporta:
  - **PKCS#1 v1.5**
  - **RSA-PSS**
- hashes disponibles: **SHA-256 / SHA-384 / SHA-512**
- salida: firma en **Base64**

Verificación:
- verifica la firma con el mensaje original + firma Base64 + clave pública PEM.
- muestra resultado (válida / inválida) en la barra de estado.

---

### 4) Criptografía simétrica (AES)

La app incluye cifrado/descifrado simétrico usando una contraseña:
- deriva una clave con **PBKDF2** (con salt fijo educativo e iteraciones).
- soporta **AES-GCM** o **AES-CBC** (según lo expuesto por la UI).
- formato de salida para cifrado: `iv:ciphertext` en Base64.

---

### 5) Validación de certificados TLS/SSL

Incluye un panel educativo con dos enfoques:

**A. Live Validation (validación “en vivo”)**
- recibe `hostname` y `puerto`.
- muestra un comando equivalente de OpenSSL del tipo:
  - `openssl s_client -connect host:port ...`
- realiza pruebas desde el navegador para inferir si el certificado es confiable (limitado por políticas del navegador/CORS y el tipo de prueba).

**B. Chain Verification (verificación de cadena)**
- permite pegar:
  - certificado del servidor (PEM),
  - cadena CA (PEM),
  - hostname (opcional, para validar CN/SAN)
- hace parsing **simplificado** de X.509 (educativo) y genera un reporte:
  - fechas de validez,
  - CN/SAN (heurístico),
  - estructura básica de issuer/subject,
  - recordatorio de comando OpenSSL real para verificación criptográfica completa.

---

## Ejecución

Como es una app estática, tienes varias opciones:

### Opción 1: Abrir el archivo directamente
1. Clona el repo
2. Abre `webapp/index.html` en tu navegador

### Opción 2: Servidor local (recomendado)
Esto evita algunos problemas de permisos/origen en el navegador.

Con Python:
```bash
cd webapp
python -m http.server 8080
```

Luego abre:
- http://localhost:8080

---

## Estructura del repositorio

- `webapp/index.html`: Aplicación completa (HTML + CSS + JS en un solo archivo)

> Si el repo tiene más archivos además de `webapp/`, esta sección se puede extender.

---

## Seguridad, alcance y limitaciones

- Proyecto con propósito **académico/educativo** (Maestría UTH), no orientado a producción.
- Las operaciones criptográficas soportadas se ejecutan con **Web Crypto API**.
- Algunos componentes (por ejemplo **ML-KEM / ML-DSA**) se muestran como **simulación/demostración** debido a limitaciones actuales del navegador.
- La validación de certificados desde navegador no sustituye herramientas de auditoría completas:
  - para verificación profunda se recomienda usar OpenSSL/SSL Labs y herramientas CLI.

---

## Contribución (Maestría UTH)

Este es un **proyecto colaborativo** de la **Maestría en Ciberseguridad de la UTH**. Sugerencias para contribuir:

1. Crear un **Issue** con:
   - objetivo,
   - descripción,
   - criterios de aceptación,
   - evidencia esperada (capturas o salida).
2. Trabajar en una rama:
```bash
git checkout -b feature/nombre-corto
```
3. Abrir Pull Request para revisión por pares.

---

## Créditos

- Estudiantes colaboradores — **Maestría en Ciberseguridad (UTH)**
- Grupo: “Grupo #2” (según el footer de la app)

---

## Licencia

Pendiente de definir (agrega aquí MIT/Apache-2.0/GPL-3.0 u otra).
