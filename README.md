# Taller Guía, Configuración Docker

**Autor:** Juan Manuel Díaz Moreno  
**Código:** A00394477  

## Requisitos Previos

Antes de empezar, nos aseguramos de de:
- Tener **Docker instalado** en una versión menor a la 25.
- Si usas **WSL2**, algunas configuraciones se deben realizar en **Docker Desktop** en Windows, lo ideal sería realizarlo en Ubuntu nativo.

![image](https://github.com/user-attachments/assets/4a101d54-42c0-454c-a525-d0fd63e06ab8)

## 1. Ejecutar un contenedor "Hello World"

Para verificar que Docker está instalado y funcionando correctamente, ejecutamos:

```
docker run hello-world
```

Docker descargará la imagen si no está disponible y mostrará un mensaje confirmando la instalación correcta.

![image](https://github.com/user-attachments/assets/666f09c0-42f7-4c2e-8146-3956a627b359)


## 2. Modificación del Storage Driver

### Cambiar a `devicemapper`

Editamos el archivo de configuración de Docker:

```
sudo mkdir -p /etc/docker
sudo nano /etc/docker/daemon.json
```

![image](https://github.com/user-attachments/assets/f2f7b2f5-0afb-417e-926c-5114df9bf64b)


Añadimos el siguiente contenido:

```
{
  "storage-driver": "devicemapper"
}
```

![image](https://github.com/user-attachments/assets/c1d06a0f-b9cf-469e-8e51-86ffb20053cb)


Guardamos los cambios y reiniciamos Docker:

```
sudo systemctl restart docker
```

Para verificar el cambio:

```
docker info | grep "Storage Driver"
```



### Volver a `overlay2`

Si es necesario regresar a la configuración original, editamos el archivo de nuevo:

```
sudo nano /etc/docker/daemon.json
```

Sustituimos el contenido por:

```
{
  "storage-driver": "overlay2"
}
```

Reiniciamos el servicio:

```
sudo systemctl restart docker
```

Y verificamos el cambio:

```
docker info | grep "Storage Driver"
```

## 3. Ejecutar Nginx con una versión específica

Para iniciar un contenedor de **Nginx 1.18.0**, ejecutamos:

```
docker run nginx:1.18.0
```

Esto descargará la imagen si no está disponible y ejecutará el servidor en primer plano.

## 4. Ejecutar Nginx en segundo plano

Para ejecutarlo en **modo detached** y liberar la terminal:

```
docker run -d nginx:1.18.0
```

Podemos comprobar que el contenedor está corriendo con:

```
docker ps
```

## 5. Configurar Nginx con parámetros específicos

Ahora ejecutamos el contenedor con algunas configuraciones adicionales:

```
docker run -d \
  --name nginx18 \
  --restart on-failure \
  -p 443:80 \
  -m 250M \
  nginx:1.18.0
```

### Explicación de los parámetros:
- `--name nginx18`: Asigna un nombre personalizado al contenedor.
- `--restart on-failure`: Reinicia el contenedor si falla.
- `-p 443:80`: Redirige el tráfico del puerto 443 del host al 80 del contenedor.
- `-m 250M`: Limita la memoria del contenedor a 250 MB.

Podemos verificar su ejecución con:

```
docker ps
```

## 6. Cambiar el Logging Driver a `journald`

### Nota sobre WSL2
Si estás en **WSL2 con Docker Desktop**, este ajuste se hace en la interfaz gráfica. En Ubuntu nativo, seguimos estos pasos.

Editamos el archivo de configuración de Docker:

```
sudo nano /etc/docker/daemon.json
```

Añadimos o modificamos la configuración:

```json
{
  "storage-driver": "overlay2",
  "log-driver": "journald"
}
```

Guardamos los cambios y reiniciamos Docker:

```
sudo systemctl restart docker
```

Para verificar que el cambio se haya aplicado:

```
docker info | grep "Logging Driver"
```

