# Vincular carpeta local a repositorio GitHub usando SSH

## Objetivo

Establecer una conexión segura entre una carpeta local y un repositorio 
remoto en GitHub, utilizando autenticación por clave SSH, evitando el 
uso de usuario/contraseña.

---


## Requisitos previos

- Tener una cuenta en GitHub.

- Tener Git instalado en tu sistema. Ref: `sudo apt install git`

- Tener una carpeta local ya creada con contenido.


---


## 📦 Paso 1: Verificar y generar clave SSH 

Seguir las claves y cuentas propias (aquí están modificadas).

**Revisión de claves**
`ls ~/.ssh`

**Generando clave nueva**
`ssh-keygen -t edd2559 -C "jpoblbeas@gmail.com"`

**Añadir la clave nueva**
`eval "$(ssh-agent -s)"`

`ssh-add ~/.ssh/id_edd2559`

**Para copiar la clave nueva pública**
`cat ~/.ssh/id_edd2559.pub`


---


## 📦 Paso 2: Agregar clave en GitHub

Ir a https://github.com/settings/profile

luego en: SSH and GPG keys → New SSH key.

Tipear un nombre de clave y pegar la clave generada anteriormente.

Ahora en mi caso, pide tercera clave para validar.

Luego indica clave aceptada (y se reporta vía correo).


---


## 📦 Paso 3: Inicio de git

**Aquí es sólo crear el archivo**
`echo "# HandbuchX" >> README.md`

`git init`

`git add README.md`

**Para poner un nombre al aporte**
`git commit -m "Verfahrensübernahme"`

`git remote add origin https://github.com/jpece/Unterlagen.git`

**Si se escribió mal el nombre se puede corregir con**
`git remote set-url origin git@github.com:jotaefe/NCorrecto.git`

**Ya para subir el contenido**
`git branch -M main`

`git push -u origin main`


---


## 📦 Paso 4: Verificación y agregando contenido

**Aquí se va a mostrar un saludo**
`ssh -T git@github.com`

**Para agregar todo el contenido incluyendo subcarpetas**
`git add .`

**Sólo para agregar una subcarpeta**
`git add nombre-de-la-subcarpeta/`

**Para chequear que se está listo para subir (dentro de la carpeta)**
`git status`

**Poner nombre a la actualización a subir**
`git commit -m "Poner-nombre-de-actualización-GitHub"`

**Para subir la actualización**
`git push`

**Verificar que no hay pendientes para subir (dentro de la carpeta)**
`git status`


---


## 📦 Paso 5: Actualización de contenido

**Verificar que no hay pendientes para subir**
`git status`

**Ejemplo de agregar una nueva rama**
`git add GitHub_REPO/vincular-repo-ssh.md`

**Poner nombre a la actualización a subir**
`git commit -m "Actualizo procedimiento vincular-repo-ssh.md"`

**Para subir la actualización**
`git push`

**Verificar que no hay pendientes para subir**
`git status`

**Cargando archivo nuevo en nueva carpeta**
`git add nueva_sub_carpeta/nuevainfo.md`

**Nombre de nueva actualización subir**
`git commit -m "subiendo contenido a nueva sub carpeta"`

**Para subir la actualización**
`git push`

