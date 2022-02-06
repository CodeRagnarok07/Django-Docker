
1.- Crea un archivo manage.py vacio
2.- shif + p *docker add python* selecciona el archivo manage.py creado
3.- shif + p *add devcontainer* > from docker-compose.yml 



Rquerimientos:

1. instalar docker Destokc [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
2. Intalar los plugins de vscode  
    1. [https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)
    2. [https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

1. en el entorno de desarrollo crea una carpeta `.devcontainer` que contendrá los siguientes archivos
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bfe47cec-d31f-44fa-bd55-af50d7bedccb/Untitled.png)
    
2. El archivo `devcontainer.json` contiene la inicialización del contenedor por medio de vscode
    
    ```json
    {
    	"name": "Existing Docker Compose (Extend)",
    	"dockerComposeFile": [
    		"docker-compose.yml",		
    	],
    	"service": "backend_django",
    	"workspaceFolder": "/workspace",
    
    	"settings": { 		
    		"python.linting.enabled": true,
    		"python.linting.pylintEnabled": true,
    		"python.linting.pylintPath": "/usr/local/bin/pylint"
    	},
    
    	"extensions": [
    		"ms-python.python"
    	],
    	
    }
    ```
    
3. El archivo `docker-compose.debug.yml` no es necesario de momento
    
    ```
    version: '3.4'
    
    services:
      backend_django:
        image: backend_django
        build:
          context: .
          dockerfile: ../backend/Dockerfile
        command: ["sh", "-c", "pip install debugpy -t /tmp && python /tmp/debugpy --wait-for-client --listen 0.0.0.0:5678 manage.py runserver 0.0.0.0:8000 --nothreading --noreload"]
        ports:
          #- 3000:3000
          - 8000:8000
          - 5678:5678
    ```
    
4. configuración del archivo `docker-compose.yml`
    
    ```
    version: '3.4'
    
    services:
      backend_django:
        image: backend_django
        build:
          context: .
          dockerfile: ../backend/Dockerfile
        # Overrides default command so things don't shut down after the process ends.
        command: sleep infinity   
        #command: python manage.py runserver 0.0.0.0:8000
        ports:
          - "8000:8000"
        environment:
          - POSTGRES_NAME=postgres
          - POSTGRES_USER=postgres
          - POSTGRES_PASSWORD=postgres
          - ALLOWED_HOSTS=127.0.0.1, localhost
    
        volumes:
          - ..:/workspace
        depends_on:
          - postgresdb
    
      postgresdb:    
        image: postgres
        restart: unless-stopped
        volumes:
          - ./data/db:/var/lib/postgresql/data
        ports: 
          - 5432:5432     
        environment:
          - POSTGRES_NAME=postgres
          - POSTGRES_USER=postgres
          - POSTGRES_PASSWORD=postgres
    ```
    
    aquí definimos 2 imágenes y cuales son los parámetros de estas,
    
    imagen: la imagen que usara como base
    
    build: la configuración de Dockerfile
    
    volumes: los archivos que copiara en tiempo real desde nuestro entorno al container
    
    ports: los puertos usados dentro del contenedor y que estarán disponibles en local
    
    depends_on o links : dependencias a las cuales será linkeadas para que trabaje en conjunto
    
5. configuramos el archivo `Dockerfile` antes mencionado en la carpeta que contendendra el archivo manage.py
    
    Directorio de trabajo
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/80cf4e99-7a8d-4c5a-b49f-086fec11d9dc/Untitled.png)
    
    contenido del archivo `Dockerfile`
    
    ```
    # For more information, please refer to https://aka.ms/vscode-docker-python
    FROM python:3.9
    
    EXPOSE 8000
    
    # Keeps Python from generating .pyc files in the container
    ENV PYTHONDONTWRITEBYTECODE=1
    
    # Turns off buffering for easier container logging
    ENV PYTHONUNBUFFERED=1
    
    # Install pip requirements 
    # COPY $(pwd)/../requirements.txt /code/
    # RUN python -m pip install -r requirements.txt
    
    WORKDIR /code
    COPY . /code/
    
    CMD ["gunicorn", "--bind", "0.0.0.0:8000", "pythonPath.to.wsgi"]
    ```
    
    Desactive la instalación de los requerimients para agilizar el desarrollo manual luego deben ser instalado
    
6. deberá cerrar el vscode y volverlo a abrir y utilizar la siguiente opción 
    
    Reopen in Container
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5e35b0d7-f744-4682-b20e-85c819fde17b/Untitled.png)
    
7. ahora hay que realizar las configuraciones de la base de datos en sttings.py una ves creado el proyecto
    
    ```python
    
    import os
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': os.environ.get('POSTGRES_NAME'),
            'USER': os.environ.get('POSTGRES_USER'),
            'PASSWORD': os.environ.get('POSTGRES_PASSWORD'),
            'HOST': 'postgresdb', # name utilizado en docker-compose
            'PORT': 5432,
        }
    }
    ```
    
8. .

### Referencias:

[Dockerize your Django and React App](https://medium.com/@gagansh7171/dockerize-your-django-and-react-app-68a7b73ab6e9)

[GitHub - 18F/docker-compose-django-react: A strawman set up for using both Django and React in a new app](https://github.com/18F/docker-compose-django-react)

[GitHub - qubitron/django-react-devcontainer: Tutorial + sample code for setting up a django + react +postgres development container](https://github.com/qubitron/django-react-devcontainer)