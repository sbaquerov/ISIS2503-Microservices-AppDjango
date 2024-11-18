# Laboratorio de microservicios - ISIS2503

## Comandos por sección:

### 2.1 Desplegar la infraestructura
Clonar el repositorio:
```bash
git clone https://github.com/ISIS2503/ISIS2503-Microservices-AppDjango.git
```
Navegar al directorio del proyecto:
```bash	
cd ISIS2503-Microservices-AppDjango
```
Crear el despliegue con Google Cloud Deployment Manager:
```bash	
gcloud deployment-manager deployments create <nombre-del-despliegue> --config deployment.yaml
```
**Nota:** Reemplace `<nombre-del-despliegue>` por el nombre que desee.
Eliminar el despliegue:
```bash	
gcloud deployment-manager deployments delete <nombre-del-despliegue>
```
**Nota:** Reemplace `<nombre-del-despliegue>` por el nombre que desee.
Verificar los logs del servicio de scripts de inicio de Google:
```bash	
sudo journalctl -u google-startup-scripts.service
```

### 2.2 Configuración del API Gateway Kong
Navegar al directorio del proyecto:
```bash	
cd /home/labs/ISIS2503-Microservices-AppDjango
```
Editar el archivo de configuración de Kong:
```bash	
sudo nano kong.yaml
```
Reiniciar el contenedor de Kong:
```bash	
docker restart kong 
```

### 2.3 Ejecución del servicio de Variables
Navegar al directorio del servicio de Variables:
```bash	
cd /home/labs/ISIS2503-Microservices-AppDjango/variables 
```
Construir la imagen Docker del servicio de Variables:
```bash	
docker build -t variables-app .
```
Ejecutar el contenedor del servicio de Variables:
```bash	
docker run --name variables -it --rm -e VARIABLES_DB_HOST=<ip-variables-db> -p 8080:8080 variables-app
```
**Nota:** Reemplace `<ip-variables-db>` por la IP privada o interna de la base de datos de variables.

### 2.4 Ejecución del servicio de Measurements
Navegar al directorio del servicio de Measurements:
```bash	
cd /home/labs/ISIS2503-Microservices-AppDjango/measurements
```
Crear las migraciones de la base de datos:
```bash	
sudo python3 manage.py makemigrations
```
Aplicar las migraciones de la base de datos:
```bash	
sudo python3 manage.py migrate 
```
Ejecutar el servidor del servicio de Measurements:
```bash	
sudo python3 manage.py runserver 0.0.0.0:8080
```

### 2.5 Crear la función serverless - api-consumption
#### Variables de entorno
| Llave   | Valor                                                                 |
|---------|-----------------------------------------------------------------------|
| API_PATH | https://raw.githubusercontent.com/ISIS2503/ISIS2503-Microservices-AppDjango/master/data/temperatura.json |
| MS_PATH  | http://<IP_PUBLICA_MEASUREMENTS>:8080/createmeasurements             |

**Nota:** Reemplace `<IP_PUBLICA_MEASUREMENTS>` por la IP pública o externa del servicio de Measurements.

#### Código
```python
import requests
import json
import os

def hello_world(request):
    data = requests.get(os.environ.get('API_PATH'), headers={"Accept":"application/json"})
    json_data = data.json()
    response = requests.post(os.environ.get('MS_PATH'), json=json_data, headers={'Content-type': 'application/json', "charset": "utf-8"})
    return "The function was successfully executed" 
```

### 3. Entregables
#### Variable de entorno para la función serverless
| Llave   | Valor                                                                 |
|---------|-----------------------------------------------------------------------|
| API_PATH | https://raw.githubusercontent.com/ISIS2503/ISIS2503-Microservices-AppDjango/master/data/oxigeno.json |
| MS_PATH  | http://<IP_PUBLICA_MEASUREMENTS>:8080/createmeasurements             |

**Nota:** Reemplace `<IP_PUBLICA_MEASUREMENTS>` por la IP pública o externa del servicio de Measurements.

### 4.1. Material complementario - Microservicio Places
Navegar al directorio del servicio de Places:
```bash	
cd /home/labs/ISIS2503-Microservices-AppDjango/places
```
Ejecutar el servicio de Places:
```bash	
sudo python3.12 main.py
```