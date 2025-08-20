# Guía Completa para Implementar n8n en Producción con Docker Compose

Esta guía explica cómo instalar y configurar n8n en producción usando Docker Compose, Nginx Proxy Manager y un `.env` seguro.

---

## 1. Instalar Docker

Ejecuta el siguiente comando para instalar Docker en tu servidor:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh
```

Verifica la instalación:

`docker --version docker compose version`

---

## 2. Crear directorio del proyecto

`mkdir -p ~/n8n_production cd ~/n8n_production`

---

## 3. Crear archivos de configuración

1. Copia tu `docker-compose.yml` al directorio:
    

`cp /ruta/al/docker-compose.yml .`

2. Crea el archivo de variables de entorno:
    

`touch .env`

3. Pega dentro de `.env` tus configuraciones de producción (ver ejemplo más abajo).
    

---

## 4. Configurar dominio y DNS

- Apunta tu dominio a la IP pública del servidor donde se desplegará n8n.
    
- Por ejemplo: `n8n.tudominio.com` → IP de tu servidor.
    
- Configura los registros `A` (y opcionalmente `AAAA` si usas IPv6).
    

---

## 5. Generar claves seguras

Usa OpenSSL para generar claves robustas:

`openssl rand -hex 32`

Usa estos valores para:

- `N8N_ENCRYPTION_KEY` → clave para cifrado de credenciales.
    
- `N8N_USER_MANAGEMENT_JWT_SECRET` → clave para JWT de sesiones.
    

## 6. Levantar n8n con Docker Compose

`docker compose up -d`

- Verifica que los contenedores estén corriendo:
    

`docker ps`

- Revisa los logs del contenedor n8n:
    

`docker logs -f n8n`

---

## 7. Crear SSL con Nginx Proxy Manager

1. Accede al panel de Nginx Proxy Manager: `http://<IP>:81`
    
2. Crea un proxy host apuntando a tu contenedor n8n:
    
    - Domain Names: `n8n.tudominio.com`
        
    - Forward Hostname / IP: `n8n`
        
    - Forward Port: `5678`
        
3. Solicita un certificado SSL Let’s Encrypt y habilita HTTPS.
    

---

## 8. Actualizar y bajar el stack

- Para bajar los contenedores:
    

`docker compose down -v`

> Si hay contenedores que no se detienen, usa:

`docker kill <nombre_contenedor> docker rm -f <nombre_contenedor> docker network rm n8n-network`

- Para actualizar contenedores a la versión más reciente (`latest`):
    

`docker compose pull docker compose up -d`

> Asegúrate de que el worker use la misma versión que la instancia principal de n8n.

---

## 9. Verificación final

- Accede a `https://n8n.tudominio.com` con tu usuario y contraseña.
    
- Prueba ejecutar un workflow simple.
    
- Verifica que la cola de tareas y los runners funcionen correctamente.