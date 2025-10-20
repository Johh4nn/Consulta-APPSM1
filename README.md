# Guía completa: Crear APK con Ionic + Angular (ícono personalizado, Splash Screen, AndroidManifest.xml)

**Resumen:**
Esta guía paso a paso explica cómo crear un proyecto Ionic Angular (template *blank*), añadir Capacitor, generar íconos y splash screens con `@capacitor/assets`, editar `AndroidManifest.xml`, generar y firmar un APK y subir todo a un repositorio de GitHub (incluyendo el archivo `app-release.apk`).

---

## Requisitos previos

* Node.js (>=18 recomendado)
* npm o pnpm
* Ionic CLI (`npm install -g @ionic/cli`)
* Java JDK (11 o superior) — para `keytool` y build
* Android Studio (SDK + herramientas de línea de comando)
* Git
* (Opcional) Familiaridad básica con Angular

> Nota: en esta guía uso `npm` y comandos que funcionan en macOS/Linux/Windows (PowerShell o WSL). Ajusta rutas si usas Windows sin WSL.

---

## 1) Crear el proyecto Ionic

Elegimos la plantilla `blank --type=angular` (mínima y ampliamente usada).

```bash
# crear proyecto
ionic start miAppIonic blank --type=angular --capacitor
cd miAppIonic
```

Explicación corta:

* `--capacitor` prepara el proyecto para usar Capacitor (te añadirá la configuración mínima).

---

## 2) Añadir Capacitor (si no lo hiciste con --capacitor)

```bash
npm install @capacitor/core @capacitor/cli --save
npx cap init "Mi App" com.miempresa.miapp
```

* `npx cap init <appName> <appId>` crea `capacitor.config.*` con el id de paquete.

---

## 3) Construir la web app y agregar la plataforma Android

```bash
# 1. construir assets web
ionic build

# 2. añadir platform android
npx cap add android

# 3. sincronizar cambios (copia web assets->android)
npx cap sync android
```

> Alternativa: `ionic cap build android` que ejecuta `ionic build` + `npx cap copy` + `npx cap open android`.

---

## 4) Ícono personalizado y Splash Screen (recomendado: @capacitor/assets)

Instalamos la herramienta oficial para generar automáticamente iconos y pantallas de inicio.

```bash
npm install --save-dev @capacitor/assets
```

Prepara imágenes base en `assets/` (recomiendo SVG o PNG de alta resolución):

```
assets/
├── icon.png         # 1024x1024 (o mayor) -- recomendado png
└── splash.png.         # 2732x2732 recomendado (centra el contenido)
```

Generar recursos (comando ejemplo):

```bash
# desde la raíz del proyecto
npx capacitor-assets generate
```

Esto generará y colocará los recursos apropiados en `android/app/src/main/res/` y en iOS si existe.

**Notas importantes:**

* Si usas Adaptive Icons en Android, proporciona `icon-foreground.png` y `icon-background.png`.
* Revisa `android/app/src/main/res/mipmap-*` y `drawable-*` para confirmar.

---

## 5) Editar AndroidManifest.xml

Ruta típica del archivo generado por Capacitor:

```
android/app/src/main/AndroidManifest.xml
```

### Ejemplos de cambios comunes

1. **Permisos** (añadir camera, internet, etc.)

```xml
<manifest ...>
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.CAMERA" />
  <!-- otros permisos -->

  <application ...>
    <!-- ... -->
  </application>
</manifest>
```

4. **Evitar que manifest se sobrescriba**

* `AndroidManifest.xml` puede ser regenerado si reinstalas plataformas. Edita el archivo en `android/app/src/main/` **no** en carpetas temporales.
* Para cambios programáticos, usa `android/` como fuente única o automatiza un parche en tu pipeline.

---

## 6) Probar en emulador o dispositivo

```bash
# abrir android studio con el proyecto nativo
npx cap open android
```

En Android Studio:

* Selecciona un emulador o dispositivo físico.
* Run (▶) para instalar debug APK.


## 7) Construir APK o AAB para release

###  Usar Android Studio (recomendado para principiantes)

1. `npx cap open android` → Android Studio
2. Menu: **Build > Generate Signed Bundle / APK...**
3. Sigue el asistente, selecciona `release-keystore.jks`, alias, contraseñas.
4. Elige `APK` o `AAB` (Google Play recomienda `AAB`).
5. Resultado en: `android/app/build/outputs/apk/release/` o `bundle/`.



## 8) Verificar APK y compartir

* Prueba el APK.
* Revisa permisos, ícono y splash.

## Ejemplo rápido de comandos (resumen)

```bash
# crear proyecto
ionic start miAppIonic blank --type=angular --capacitor
cd miAppIonic

# build web + añadir android
ionic build
npx cap add android
npx cap sync android

# generar icon/splash
npm install --save-dev @capacitor/assets
npx capacitor-assets generate

# abrir Android Studio
npx cap open android


# en Android Studio -> Build -> Generate Signed Bundle / APK

```
