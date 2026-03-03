<div align="center">

# 🔐 Huawei Decrypt Config

<img src="https://img.shields.io/badge/Huawei-Decrypt%20Config-00d4ff?style=for-the-badge&logo=huawei&logoColor=white" alt="badge">
<img src="https://img.shields.io/badge/versión-1.0.0-22c55e?style=for-the-badge" alt="version">
<img src="https://img.shields.io/badge/licencia-MIT-3b82f6?style=for-the-badge" alt="license">
<img src="https://img.shields.io/badge/100%25-sin%20servidor-f97316?style=for-the-badge" alt="client-side">
<img src="https://img.shields.io/badge/JavaScript-ES2020-eab308?style=for-the-badge&logo=javascript&logoColor=black" alt="js">

<br><br>

**Herramienta profesional basada en navegador para parsear, desencriptar y visualizar archivos de backup de routers Huawei (`hw_ctree_*.html`).**

<br>

[**🚀 Demo en vivo**](https://noxivo1.github.io/Huawei-Decrypt-Config) &nbsp;·&nbsp; [**📖 Writeup técnico**](https://blog.fayaru.me/posts/huawei_router_config/) &nbsp;·&nbsp; [**🐛 Reportar bug**](../../issues/new?template=bug_report.md) &nbsp;·&nbsp; [**✨ Solicitar función**](../../issues/new?template=feature_request.md)

<br>

```
📦  Arrastra tu archivo hw_ctree_*.html
              ↓
🔓  Desencriptado AES-128-CBC en el navegador
              ↓
📊  Dashboard completo con toda la configuración
```

</div>

---

## ¿Qué es esto?

Los routers Huawei (serie EchoLife, HG8xxx, HS8xxx) guardan su configuración en archivos de backup con extensión `.html`. Cuando el campo `DBEncrypt="1"` está presente, todos los valores sensibles — contraseñas WiFi, credenciales PPPoE, usuarios del panel admin — están cifrados con **AES-128-CBC** usando un esquema de codificación propietario.

Esta herramienta implementa el algoritmo de desencriptado completo directamente en el navegador, sin enviar ningún dato a ningún servidor.

---

## ✨ Funcionalidades

| Sección | Descripción |
|---|---|
| 🔓 **Desencriptado AES-CBC completo** | Descifra todos los valores `$2...$` usando la clave interna de Huawei |
| 📶 **Redes WiFi** | SSID, contraseñas descifradas, canal, modo de seguridad, potencia TX |
| 🌐 **PPPoE / Credenciales ISP** | Usuario y contraseña descifrada de la conexión al proveedor |
| 🔑 **Usuarios del sistema** | Usuarios web y CLI con su modo de hash (reversible vs irreversible) |
| 🔌 **LAN / DHCP** | Rangos de IP, gateway, DNS, MTU, configuración de acceso ACL |
| 📱 **Dispositivos conectados** | Historial completo de todas las MACs que se han conectado |
| 🖥️ **TR-069 / ACS** | Credenciales del servidor de gestión remota (descifradas) |
| ⚙️ **Info del dispositivo** | Versión de firmware, estadísticas de reinicios, operador/ISP |
| 📄 **XML original** | Visualización y descarga del archivo de configuración sin modificar |
| 💻 **100% local** | Sin servidor, sin uploads, sin rastreo — todo corre en tu navegador |

---

## 🔐 Detalles del cifrado

Huawei cifra los campos sensibles con **AES-128-CBC** + un esquema de codificación personalizado llamado `HW_AES_AscUnvisible`. El proceso completo:

```
Formato del valor cifrado:  $2<datos_codificados>$

Pasos de desencriptado:
  1. Eliminar prefijo "$2" y sufijo "$"
  2. Decodificar con HW_AES_AscUnvisible
     → cada byte se representa como dos chars con offset 0x23
  3. Últimos 20 bytes → IV de 16 bytes (XOR fold via HW_AES_PlainToBin)
  4. Clave AES: "Df7!ui%s9(lmV1L8"  (hardcodeada en libhw_ssp_basic.so)
  5. Descifrar AES-128-CBC con padding PKCS7
```

> Esta clave **no es un secreto** — está hardcodeada en el firmware de Huawei y fue documentada públicamente por investigadores de seguridad.

### Modos de hash para contraseñas de usuarios

Las contraseñas de cuentas de usuario del sistema usan hashing unidireccional. Esta herramienta los identifica y avisa cuáles son reversibles y cuáles no:

| Modo | Algoritmo | ¿Reversible? |
|---|---|---|
| `0` | Texto plano | ✅ Sí |
| `1` | `MD5(contraseña)` | ❌ No |
| `2` | `SHA256(MD5(contraseña))` | ❌ No |
| `3` | `PBKDF2-SHA256` (5000 iteraciones + salt) | ❌ No |
| `$2...$` | AES-128-CBC cifrado | ✅ **Sí** |

---

## 🚀 Uso

### Opción 1 — GitHub Pages (recomendado)

Abre directamente en el navegador, sin instalar nada:

```
https://noxivo1.github.io/Huawei-Decrypt-Config
```

### Opción 2 — Archivo local (sin instalación)

Descarga el archivo [`huawei-config-viewer.html`](huawei-config-viewer.html) y ábrelo directamente en cualquier navegador moderno. No necesita internet ni instalación.

### Opción 3 — Clonar el repositorio

```bash
git clone https://github.com/noxivo1/Huawei-Decrypt-Config.git
cd Huawei-Decrypt-Config
# Abrir huawei-config-viewer.html en el navegador
```

### ¿Cómo obtengo el archivo de backup?

1. Inicia sesión en la interfaz web del router (normalmente `192.168.1.1` o `192.168.100.1`)
2. Ve a **Herramientas del sistema** → **Archivo de configuración** → **Copia de seguridad**
3. El archivo se descargará con nombre similar a `hw_ctree_<fecha>.html`

> ⚠️ Algunos ISPs bloquean el acceso a esta sección. En ese caso se requiere acceso por consola serie o volcado de flash.

---

## 📁 Estructura del proyecto

```
Huawei-Decrypt-Config/
├── huawei-config-viewer.html   ← Aplicación principal (autocontenida)
├── README.md                   ← Este archivo
├── SECURITY.md                 ← Política de divulgación responsable
├── CHANGELOG.md                ← Historial de versiones
├── CONTRIBUTING.md             ← Guía para contribuidores
├── LICENSE                     ← Licencia MIT
├── .gitignore                  ← Excluye archivos de backup reales
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md       ← Plantilla para bugs
│   │   └── feature_request.md ← Plantilla para sugerencias
│   └── workflows/
│       └── deploy.yml          ← Auto-deploy a GitHub Pages
└── docs/
    ├── encryption.md           ← Análisis técnico del cifrado Huawei
    └── compatibility.md        ← Modelos de router compatibles
```

---

## 🛡️ Dispositivos compatibles

Probado con archivos de backup de los siguientes modelos:

| Modelo | Firmware | ISP / Región | Estado |
|---|---|---|---|
| EchoLife HG8247H | V500R019C00 | Múltiples | ✅ Completo |
| EchoLife HG8247Q | V500R019C00 | Vodafone PT | ✅ Completo |
| EchoLife HS8247W | V300R016C10 | Vodafone PT | ✅ Completo |
| EchoLife HG8245H | V300R018C10 | Múltiples | ✅ Completo |
| EchoLife EG8145V5 | V500R021 | UNE — Colombia | ✅ Completo |
| EchoLife HG8145V | V300R016C10 | Múltiples | ⚠️ Parcial |

> ¿Tienes un modelo no listado? [Abre un issue](../../issues/new) con tus resultados.

---

## 🏗️ Stack técnico

- **JavaScript vanilla** (ES2020) — sin dependencias de framework
- **CryptoJS 4.1.1** — desencriptado AES (CDN)
- **Google Fonts** — Syne + JetBrains Mono
- **DOMParser API** — parseo del XML de configuración
- **Clipboard API** — copiar valores con un clic

Todo el código está en un único archivo `.html` autocontenido — sin bundlers, sin npm, sin build steps.

---

## ⚠️ Aviso legal y ético

Esta herramienta está destinada a:

- Administradores de red que gestionan sus propios equipos
- Investigadores de seguridad con autorización explícita
- Técnicos de ISP en diagnóstico de equipos autorizados
- Aprendizaje y uso educativo

**No utilices esta herramienta para acceder a dispositivos que no son de tu propiedad o sin autorización. El acceso no autorizado a equipos de red es ilegal en la mayoría de jurisdicciones.**

---

## 🤝 Contribuciones

¡Las contribuciones son bienvenidas! Lee [CONTRIBUTING.md](CONTRIBUTING.md) para los detalles.

```bash
# Flujo estándar
git fork
git checkout -b feat/mi-mejora
git commit -m 'feat: descripción del cambio'
git push origin feat/mi-mejora
# → Abrir Pull Request
```

---

## 📄 Licencia

Distribuido bajo la licencia MIT. Ver [`LICENSE`](LICENSE) para más información.

---

## 🙏 Créditos

- Investigación del cifrado por [André Brandão (@andreluis034)](https://github.com/andreluis034) — [writeup original](https://blog.fayaru.me/posts/huawei_router_config/)
- Implementación de referencia: [huawei-utility-page](https://github.com/andreluis034/huawei-utility-page)
- Cifrado AES via [CryptoJS](https://github.com/brix/crypto-js)

---

<div align="center">
Hecho para ingenieros de red e investigadores de seguridad 🔐
<br>
<a href="https://github.com/noxivo1/Huawei-Decrypt-Config">github.com/noxivo1/Huawei-Decrypt-Config</a>
</div>
