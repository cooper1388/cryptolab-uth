# CryptoLab UTH

Aplicación web educativa para **criptografía aplicada** que funciona **100% en el navegador** (sin servidor), desarrollada como **proyecto colaborativo** de la **Maestría en Ciberseguridad de la UTH (Universidad Tecnológica de Honduras)**.

> Enfoque: laboratorio/demostración. No está pensada como herramienta de producción.

---

## Tabla de contenido

- [Descripción](#descripción)
- [Características](#características)
- [Tecnologías](#tecnologías)
- [Cómo funciona](#cómo-funciona)
- [Módulos de la aplicación](#módulos-de-la-aplicación)
  - [1) Generación de par de llaves](#1-generación-de-par-de-llaves)
  - [2) Extracción de llave pública](#2-extracción-de-llave-pública)
  - [3) Firmas digitales y verificación](#3-firmas-digitales-y-verificación)
  - [4) Cifrado/descifrado simétrico (AES)](#4-cifradorescifrado-simétrico-aes)
  - [5) Validación de certificados TLS/SSL](#5-validación-de-certificados-tlsssl)
- [Ejecución (cómo correrlo)](#ejecución-cómo-correrlo)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Seguridad, privacidad y limitaciones](#seguridad-privacidad-y-limitaciones)
- [Contribución (proyecto colaborativo UTH)](#contribución-proyecto-colaborativo-uth)
- [Créditos](#créditos)

---

## Descripción

**UTH CryptoLab** es una **aplicación web estática** (un solo `index.html`) que integra interfaz, estilos y lógica en un único archivo: **HTML + CSS + JavaScript**.

La app permite ejecutar operaciones criptográficas de forma interactiva, mostrando resultados (PEM/Base64) y, en algunos casos, comandos equivalentes en **OpenSSL** para fines didácticos.

---

## Características

- Interfaz por **paneles** (expandibles) para organizar los laboratorios.
- Ejecución local: utiliza **Web Crypto API** (`crypto.subtle`) para operaciones soportadas.
- Exportación de llaves a **PEM** (PKCS#8 privada / SPKI pública) y resultados en **Base64**.
- Botones de **copiar al portapapeles** y **descargar** llaves en `.pem`.
- Sección educativa de certificados con:
  - validación “en vivo” (pruebas desde el navegador),
  - verificación de cadena (parsing simplificado y guía de OpenSSL).

---

## Tecnologías

- **HTML5 / CSS3 / JavaScript**
- **Web Crypto API**
- Tipografías web (Google Fonts)

No requiere:
- Node.js
- dependencias
- build tools
- backend

---

## Cómo funciona

### Arquitectura (alto nivel)

1. **UI (HTML)**: formularios y salidas por módulo.
2. **Estado (JS)**: variables globales mínimas (p. ej. `currentKeyType`).
3. **Criptografía (Web Crypto API)**:
   - generación de claves,
   - importación/exportación,
   - cifrado/descifrado,
   - firma/verificación,
   - derivación de claves para AES con PBKDF2.
4. **Presentación de resultados**:
   - PEM para llaves,
   - Base64 para firmas/cifrados,
   - mensajes de estado (info/success/error).

### Formatos usados

- **Private Key**: PKCS#8 (`-----BEGIN PRIVATE KEY-----`)
- **Public Key**: SPKI (`-----BEGIN PUBLIC KEY-----`)
- **Firmas / ciphertext**: Base64
- **AES**: formato `iv:ciphertext` en Base64

---

## Módulos de la aplicación

### 1) Generación de par de llaves

Permite seleccionar el tipo de llave:

- **RSA**: 2048 / 3072 / 4096 bits  
- **EC (Curvas elípticas)**: P-256 / P-384 / P-521  
- **ML-KEM** (post-cuántico) y **ML-DSA** (post-cuántico):  
  se **simulan** porque actualmente no hay soporte nativo en navegadores vía Web Crypto API.

Salida:
- muestra privada + pública en PEM
- permite copiar y descargar el `.pem`

También se muestra un **comando OpenSSL equivalente** (con fines de referencia/estudio).

---

### 2) Extracción de llave pública

Permite pegar una llave privada (PEM) e intentar obtener la pública.

**Limitación importante (educativa):** Web Crypto API no garantiza poder extraer la pública desde cualquier PKCS#8 importado. La app funciona mejor cuando la llave fue generada dentro de la misma sesión (porque conserva referencias internas).

---

### 3) Firmas digitales y verificación

**Firmar**
- Entrada: mensaje + llave privada PEM
- Hash: SHA-256 / SHA-384 / SHA-512
- Formato: PKCS#1 v1.5 o RSA-PSS
- Salida: firma en Base64

**Verificar**
- Entrada: mensaje original + firma Base64 + llave pública PEM
- Salida: válido / inválido (con mensaje explicativo)

---

### 4) Cifrado/descifrado simétrico (AES)

La app incluye cifrado simétrico usando una contraseña:

- Deriva una llave con **PBKDF2**
- Soporta modos según UI (por ejemplo **AES-GCM** / **AES-CBC**)
- Salida cifrada en formato:
  - `Base64(iv):Base64(ciphertext)`

---

### 5) Validación de certificados TLS/SSL

Incluye dos enfoques:

**A) Live Validation**
- Entrada: hostname + puerto + opciones
- Genera un comando OpenSSL sugerido:
  - `openssl s_client -connect host:port ...`
- Ejecuta pruebas desde el navegador para inferir confiabilidad/conectividad.

**B) Chain Verification**
- Entrada:
  - Certificado del servidor (PEM)
  - Cadena CA (PEM)
  - Hostname opcional para validar CN/SAN
- Realiza parsing **simplificado** de X.509 con fines educativos (no reemplaza a un verificador criptográfico completo).
- Emite un reporte y recomienda el comando de OpenSSL para verificación real.

---

## Ejecución (cómo correrlo)

No necesitas instalar nada ni usar un servidor local.

1. Clona o descarga este repositorio.
2. Abre el archivo en tu navegador:

- `webapp/index.html`

> Recomendación: usar un navegador moderno (Chrome/Edge/Firefox) para asegurar compatibilidad con Web Crypto API.

---

## Estructura del repositorio

- `webapp/index.html` — aplicación completa (HTML + CSS + JavaScript)

---

## Seguridad, privacidad y limitaciones

- **Privacidad:** la intención del proyecto es ejecutar operaciones localmente en el navegador (sin backend).
- **Post-cuántico:** ML-KEM/ML-DSA están como **simulación/demostración**.
- **Certificados:** la verificación desde navegador es limitada; para auditoría real usar OpenSSL/SSL Labs.
- **Uso educativo:** no se recomienda para flujos de producción ni manejo de secretos reales.

---

## Contribución (proyecto colaborativo UTH)

Este repositorio forma parte de un **proyecto colaborativo** de la **Maestría en Ciberseguridad de la UTH**, por lo que las contribuciones son bienvenidas.

Sugerencia de flujo:
1. Crear un **Issue** con descripción y criterios de aceptación.
2. Crear rama:
```bash
git checkout -b feature/nombre-corto
```
3. Hacer commits claros y abrir **Pull Request** para revisión por pares.

---


## Créditos

Proyecto colaborativo — **Maestría en Ciberseguridad, UTH**.


