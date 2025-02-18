# self-hosted-ai-starter-kit-N8N-Ngrok

# Instalar Docker Desktop y N8N con Ollama, Qdrant, Postgres y Ngrok

- Instalar `docker desktop`
    - Instalar `WPL` (con instalación docker desktop)
        - Desde terminal actualizar con:
        
        ```bash
        wsl --update
        
        #Ejecuta PowerShell como administrador y fuerza la actualización (si es necesario)  con:
        
        wsl --update --web-download
        
        #Si falla, prueba reinstalar WSL con estos comandos en PowerShell (administrador):
        
        wsl --unregister docker-desktop
        wsl --unregister docker-desktop-data
        wsl --install
        
        #Verifica con:
        
        docker info
        docker compose version
        docker run hello-world
        
        #Para reiniciar WSL y Docker en PowerShell:
        
        wsl --shutdown
        net stop wslservice
        net start wslservice
        
        ```
        

- Instalar `N8N`:
    - Crear carpeta en C:\Users\tu-usuario\n8n (por ejemplo) o en otro directorio
    - Copiar archivos a la carpeta creada        

- Instalar `Ngrok`:
    - Ir a https://ngrok.com/
    - Crear cuenta o iniciar sesión
    - Descargar ngrok y copiar el archivo ngrok.exe en la carpeta de n8n ya creada
    - Crear dominio estático gratis en domain
    - Copiar Authtoken de ngrok
    - Comprobar que se ha creado el dominio

- Editar el archivo `.env` con al `authtoken` de ngrok y el dominio estático ya creado sin HTTPS://

```bash
NGROK_AUTHTOKEN=         # Token en ngrok
NGROK_DOMAIN=            # Dominio ngrok sin https://
```

Las variables de Postgres en el archivo `.env` se pueden modificar o dejar así de momento.

- Abrir powershell y navegar hasta el directorio de n8n ya creado y seguir los pasos:

```bash
cd C:\Users\tu-usuario\n8n

# Luego levantar el contenedor para gráficas Nvidia con:

docker compose --profile gpu-nvidia up

# Levantar el contenedor para gráficas AMD en LINUX:

docker compose --profile gpu-amd up

# Para todos (CPU):

docker compose --profile cpu up

#Para levantar contenedor en segundo plano utilidad -d

docker compose --profile cpu up -d

docker compose --profile gpu-nvidia up -d
# No incluir -d si se quiere lanza en primer plano
```

- Comprobar en Docker Desktop que todos los contenedores están corriendo
- Comprobar en https://hoppscotch.io/ la url que nos da al ingresar en [localhost:4040](http://localhost:4040) y dentro de n8n copiamos la url y la introducimos en la web, tiene que dar ok
- También de puede acceder en [localhost:5678](http://localhost:5678) a n8n

```bash
#Para tirar contenedor sin borrar datos:
docker-compose down

# O también:

docker compose --profile cpu down # O el perfil que uses, como nvidia

# Para actualizar con Nvidia:

docker compose --profile gpu-nvidia pull

docker compose create
docker compose --profile gpu-nvidia up -d

# Para actualizar normal (CPU):

docker compose --profile cpu pull
docker compose create 
docker compose --profile cpu up -d

# Para crear una copia de seguridad de todo los volumenes y base de datos (creará un archivo backup en el directorio que estés de n8n):

docker run --rm -v n8n_storage:/data -v ${PWD}:/backup busybox tar cvf /backup/n8n_backup.tar /data

#Restaurar copia seguridad en otro PC, en el mismo directorio de n8n donde esta el docker compose, copiar el archivo anterior y restaurar con:

docker run --rm -v n8n_storage:/data -v ${PWD}:/backup busybox tar xvf /backup/n8n_backup.tar -C /

```

- Ejemplo de restaurar en una nueva máquina:

**Ejemplo práctivo para Migrar N8N a una Nueva Máquina**

---

### **Pasos Clave:**

1. **Instalar Docker y Docker Compose** en la nueva máquina.
2. **Copiar los archivos del proyecto** (`docker-compose.yml`, `.env`, `n8n_backup.tar`) a una carpeta idéntica.
3. **Crear volúmenes iniciales** (ejecutar primero):
    
    ```bash
    docker compose --profile cpu up -d
    
    # O también:
     
    docker compose --profile gpu-nvidia up -d
     
    
    ```
    
4. **Detener contenedores:**
    
    ```bash
    docker compose down
    
    # O también:
    
    docker compose --profile cpu down
    
    # O también:
    
    docker compose --profile gpu-nvidia down
    ```
    
5. **Restaurar backup** (ejecutar desde la carpeta del proyecto):
    - **PowerShell (Windows):**
        
        ```bash
        docker run --rm -v n8n_storage:/data -v ${PWD}:/backup busybox tar xvf /backup/n8n_backup.tar -C /
        
        ```
        
    - **Linux:**
        
        ```bash
        docker run --rm -v n8n_storage:/data -v $(pwd):/backup busybox tar xvf /backup/n8n_backup.tar -C /
        
        ```
        
6. **Levantar contenedores con datos restaurados:**
    
    ```bash
    docker compose --profile cpu up -d
    
    ```
    

### **Verificación Final:**

- Accede a `http://localhost:5678` para confirmar que tus flujos y configuraciones están presentes.
- Repo Oficial: https://github.com/BuilderMind/self-hosted-ai-starter-kit-N8N
