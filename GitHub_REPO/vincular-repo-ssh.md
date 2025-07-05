# Procedimiento: Vincular carpeta local a repositorio GitHub usando SSH

## Objetivo

Establecer una conexión segura entre una carpeta local y un repositorio 
remoto en GitHub, utilizando autenticación por clave SSH, evitando el 
uso de usuario/contraseña.

---

## 🧰 Requisitos previos

- Tener una cuenta en GitHub.
- Tener Git instalado en tu sistema.
- Tener una carpeta local ya creada con contenido.

---

## 🪪 Paso 1: Verificar y generar clave SSH

```bash
ls ~/.ssh #revisión de claves#
ssh-keygen -t ed2559 -C "josepoblbezas@gmail.com" #generando nueva#
eval "$(ssh-agent -s)" #añadir la clave nueva#
ssh-add ~/.ssh/id_ed2559
cat ~/.ssh/id_ed2559.pub #para copiar la clave nueva pública#
```bash

## 🪪 Paso 2: Agregar clave en GitHub

Ir a https://github.com/settings/profile
luego en: SSH and GPG keys → New SSH key.
Tipear un nombre de clave y pegar la clave generada anteriormente.
Ahora en mi caso, pide tercera clave para validar.
Luego indica clave aceptada (y se reporta vía correo).

## 🪪 Paso 3: Inicio de git

echo "# HandbuchX" >> README.md #aquí es sólo crear el archivo#
git init
git add README.md
git commit -m "Verfahrensübernahme" #para poner un nombre al aporte#
git remote add origin https://github.com/jotaefepece/Unterlagen.git
#si se escribió mal el nombre se puede corregir con#
git remote set-url origin git@github.com:jotaefepece/NombreCorrecto.git
git branch -M main #ya para subir el contenido#
git push -u origin main

## 🪪 Paso 4: Verificación

```bash
ssh -T git@github.com #aquí se va a mostrar un saludo#

