# Ejercicio de Simulación en Gazebo con PX4: Enjambre de 4 usando MAVLink

## Objetivo

Simular un enjambre de 4 drones utilizando MAVLink y PX4 en Gazebo, observando el 
comportamiento del sistema y entendiendo los límites de comunicación y sincronización.

---

## Requisitos Previos

1. Fedora con PX4 y Gazebo instalados.
2. Red local activa.
3. `MAVSDK` instalado (se usa MAVSDK en este ejercicio).
4. Al menos 4 núcleos de CPU y 4 GB de RAM (incrementar según cantidad drones).
5. MAVSDK-Python instalado `pip install mavsdk`.

---

## Procedimiento

### 📦 Paso 1: Ingresar al contenedor Docker y compilar

```bash
cd ~/PX4-Autopilot

docker run -it \
  -v $(pwd):/src/PX4-Autopilot:z \
  -w /src/PX4-Autopilot \
  px4io/px4-dev:v1.16.0-rc1-258-g0369abd556 \
  /bin/bash

make px4_sitl_default
```

### 📦 Paso 2: Generando script (en otra pestaña o terminal)

Creando carpeta para el script:
`mkdir -p ~/PX4-Autopilot/scripts`

Ingresar a la carpeta:
`cd ~/PX4-Autopilot/scripts`

En el siguiente script es muy relevante la ubicación del archivo iris.world.
Script para lanzar los drones:

```bash
#!/bin/bash
# launch_swarm.sh

NUM_DRONES=3
BASE_PORT=14540
BASE_ID=0

PX4_DIR=~/PX4-Autopilot
BUILD_DIR=$PX4_DIR/build/px4_sitl_default
WORLD=$PX4_DIR/Tools/sitl_gazebo/worlds/iris.world
PX4_DIR=~/PX4-Autopilot
BUILD_DIR=$PX4_DIR/build/px4_sitl_default
#WORLD=$PX4_DIR/Tools/sitl_gazebo/worlds/iris.world
#WORLD=$PX4_DIR/Tools/simulation/gazebo-classic/sitl_gazebo-classic/worlds/iris.world
WORLD=$PX4_DIR/Tools/simulation/gazebo-classic/sitl_gazebo-classic/worlds/iris_irlock.world


for i in $(seq 0 $((NUM_DRONES - 1))); do
  INSTANCE_DIR=/tmp/px4_instance_$i
  mkdir -p $INSTANCE_DIR

  export PX4_SIM_HOST_ADDR=127.0.0.1
  export SITL_INSTANCE=$i
  export MAVLINK_PORT=$((BASE_PORT + i))
  export ID=$((BASE_ID + i))

  echo "Lanzando instancia $i en puerto $MAVLINK_PORT..."

  $BUILD_DIR/bin/px4 \
    -i $ID \
    -d \
    -s $PX4_DIR/ROMFS/px4fmu_common/init.d-posix/rcS \
    --instance $i \
    > $INSTANCE_DIR/px4_$i.log 2>&1 &

  sleep 2
done

gazebo --verbose $WORLD &

```

Guardar el script y luego otorgar permisos de ejecución:
`chmod +x ~/PX4-Autopilot/scripts/launch_swarm.sh`


### 📦 Paso 3: Ejecución de script

Ir al directorio raíz de PX4:
`~/PX4-Autopilot/scripts/launch_swarm.sh`


### 📦 Paso 4: Configuración de modelos locales en Gazebo

Detalle de procedimiento:

```bash
cd ~
git clone https://github.com/leonhartyao/gazebo_models_worlds_collection.git
```

Verificar que los modelos están ya disponibles:
`ls ~/gazebo_models_worlds_collection/models/`

Editar el archivo ...:
`nano ~/.bashrc`

Agregar al path las variables de entorno:
`export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:$HOME/gazebo_models_worlds_collection/models`
`export GAZEBO_RESOURCE_PATH=$GAZEBO_RESOURCE_PATH:$HOME/gazebo_models_worlds_collection/worlds`


Verificar variable activa:
`echo $GAZEBO_MODEL_PATH`

Desactivar búsqueda de modelos vía web:
`export GAZEBO_MODEL_DATABASE_URI=""`

Editar el archivo ...:
`nano ~/.bashrc`

Direccionando modelos al PATH:
`mkdir -p ~/.gazebo/empty_models`

Agregar al path las variables de entorno:
`export GAZEBO_MODEL_PATH=$HOME/.gazebo/empty_models:$GAZEBO_MODEL_PATH`
`export GAZEBO_MODEL_PATH=$HOME/.gazebo/empty_models:$HOME/gazebo_models_worlds_collection/models`

Luego recargar el entorno ...:
`source ~/.bashrc`

---

## Ejecución de gazebo

En un terminal ejecutar la compilación de PX4 en el docker:
```bash
cd ~/PX4-Autopilot

docker run -it \
  -v $(pwd):/src/PX4-Autopilot:z \
  -w /src/PX4-Autopilot \
  px4io/px4-dev:v1.16.0-rc1-258-g0369abd556 \
  /bin/bash

make px4_sitl_default
```


### 📦 Ejecución del script de emjambre

Verificación (no debiera arrojar errores):
`WORLD=$PX4_DIR/Tools/simulation/gazebo-classic/sitl_gazebo-classic/worlds/iris_irlock.world`

Crear el script:
`nano ~/PX4-Autopilot/Tools/simulation/gazebo-classic/sitl_gazebo-classic/worlds/iris_swarm.world`

Contenido del archivo:
```python
<?xml version="1.0" ?>
<sdf version="1.6">
  <world name="iris_swarm_world">

    <!-- Cielo, física y terreno -->
    <include>
      <uri>model://sun</uri>
    </include>

    <include>
      <uri>model://ground_plane</uri>
    </include>

    <!-- Dron 0 -->
    <include>
      <uri>model://iris</uri>
      <name>iris_0</name>
      <pose>0 0 0.1 0 0 0</pose>
    </include>

    <!-- Dron 1 -->
    <include>
      <uri>model://iris</uri>
      <name>iris_1</name>
      <pose>2 0 0.1 0 0 0</pose>
    </include>

    <!-- Dron 2 -->
    <include>
      <uri>model://iris</uri>
      <name>iris_2</name>
      <pose>4 0 0.1 0 0 0</pose>
    </include>

  </world>
</sdf>
```
Modificando archivo launch_swarm.sh
Cambiar ubicación **WORLD** por la siguiente línea:
`WORLD=$PX4_DIR/Tools/simulation/gazebo-classic/sitl_gazebo-classic/worlds/iris_swarm.world`

mkdir -p /tmp/gazebo-$USER-rtshaderlibcache
chmod u+w /tmp/gazebo-$USER-rtshaderlibcache
pkill -f gzserver
pkill -f gzclient
cd ~/PX4-Autopilot/Tools/simulation/gazebo-classic
rm -rf sitl_gazebo-classic
git submodule update --init --recursive --force
cd ~/PX4-Autopilot/Tools/simulation/gazebo-classic
rm -rf sitl_gazebo-classic
cd ~/PX4-Autopilot/Tools/simulation/gazebo-classic
git clone git@github.com:PX4/PX4-SITL_gazebo-classic.git
~/PX4-Autopilot/scripts/launch_swarm.sh

Lanzar el enjambre:
`~/PX4-Autopilot/scripts/launch_swarm.sh`
