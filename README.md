# Taller Guía, Configuración Docker

**Autor:** Juan Manuel Díaz Moreno  
**Código:** A00394477  

## Previamente

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

![image](https://github.com/user-attachments/assets/4e999a74-a6d5-4e22-b621-4e715789bf1c)


### Volver a `overlay2`

![image](https://github.com/user-attachments/assets/a56d52a8-30c6-48fa-83ef-14f7b2c55be4)


## 3. Ejecutar Nginx con una versión específica

Para iniciar un contenedor de **Nginx 1.18.0**, ejecutamos:

```
docker run nginx:1.18.0
```

![image](https://github.com/user-attachments/assets/39423db8-eed4-42e2-b564-3d7f56d9d6dd)


## 4. Ejecutar Nginx en segundo plano

Para ejecutarlo en **modo detached** y liberar la terminal:

```
docker run -d nginx:1.18.0
```

![image](https://github.com/user-attachments/assets/33c7dc5c-848e-4402-8893-a944420bfc4a)

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

![image](https://github.com/user-attachments/assets/373f0dce-d29a-4fa7-ba7a-1e45a3dcab85)

- `--name nginx18`: Asigna un nombre personalizado al contenedor.
- `--restart on-failure`: Reinicia el contenedor si falla.
- `-p 443:80`: Redirige el tráfico del puerto 443 del host al 80 del contenedor.
- `-m 250M`: Limita la memoria del contenedor a 250 MB.

## 6. Cambiar el Logging Driver a `journald`

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

![image](https://github.com/user-attachments/assets/81d0ed19-32fa-469c-b6c1-ce246f13fc62)


Guardamos los cambios y reiniciamos Docker:

```
sudo systemctl restart docker
```

Para verificar que el cambio se haya aplicado:

```
docker info | grep "Logging Driver"
```

![image](https://github.com/user-attachments/assets/38a7ad9f-be5d-421a-8f28-5000c629a059)


