# RabbitMQ Configuration - Latacunga Backend

Custom RabbitMQ Docker image con topología predefinida, plugins y configuración lista para producción.

## 📋 Características

- **RabbitMQ 3.13 Management** con interfaz web integrada
- **Plugins habilitados**: 
  - `rabbitmq_management` - Web UI y API REST
  - `rabbitmq_prometheus` - Métricas Prometheus
- **Topología predefinida**:
  - 4 Exchanges (direct y fanout)
  - 16 Queues (incluyendo Dead Letter Queues)
  - 17 Bindings configurados automáticamente
- **Seguridad**: Usuario `tesis` con contraseña y permisos administrativos
- **Auto-importación**: Definiciones cargadas automáticamente al iniciar

## 🚀 Uso Rápido

### Opción 1: Desde Docker Hub (recomendado)

```bash
docker-compose up -d
```

El archivo `docker-compose.yml` ya está configurado para usar la imagen publicada en Docker Hub.

### Opción 2: Construir localmente

```bash
docker build -t mrengineer09/rabbitmq:plugins .
docker-compose up -d
```

## 🔐 Credenciales

- **Usuario**: `tesis`
- **Contraseña**: `tesis` (definida en `.env`)
- **Management UI**: http://localhost:15672
- **AMQP Port**: 5672

## 📊 Topología

### Exchanges
- `incidente.cmd` (direct)
- `incidente.validado.fanout` (fanout)
- `incidente.rechazado.fanout` (fanout)
- `dlx.direct` (Dead Letter Exchange)

### Queues
**Principales:**
- validacion.q
- incidentes.q
- tareas.q
- ubicacion.q
- horarios.q
- acopios.q
- rutas.q
- auditoria.q

**Dead Letter Queues (DLQ):**
- validacion.dlq
- incidentes.dlq
- tareas.dlq
- ubicacion.dlq
- horarios.dlq
- acopios.dlq
- rutas.dlq
- auditoria.dlq

## 📁 Estructura de Archivos

```
.
├── Dockerfile                    # Image definition
├── docker-compose.yml           # Docker Compose configuration
├── .env                         # Environment variables (credenciales)
├── .dockerignore                # Files excluded from build
├── .gitignore                   # Files excluded from git
├── enabled_plugins              # RabbitMQ plugins to enable
├── rabbitmq.conf               # RabbitMQ configuration
├── rabbitmq-definitions.json   # Topology (exchanges, queues, bindings)
└── README.md                   # This file
```

## 🔧 Configuración

### Variables de Entorno (.env)

```env
RABBITMQ_USER=tesis
RABBITMQ_PASS=tesis
```

Modifica según tus necesidades de seguridad.

### Plugins Habilitados (enabled_plugins)

```
[rabbitmq_management,rabbitmq_prometheus].
```

### RabbitMQ Configuration (rabbitmq.conf)

```conf
management.load_definitions = /etc/rabbitmq/definitions.json
default_vhost = /
loopback_users.guest = false
```

## 🐳 Comandos Útiles

### Listar usuarios
```bash
docker exec rabbitmq rabbitmqctl list_users
```

### Listar exchanges
```bash
docker exec rabbitmq rabbitmqctl list_exchanges
```

### Listar queues
```bash
docker exec rabbitmq rabbitmqctl list_queues
```

### Ver logs
```bash
docker logs -f rabbitmq
```

### Reiniciar contenedor
```bash
docker-compose restart
```

## 📦 Imágenes en Docker Hub

```bash
# Usar cualquiera de estos tags
docker pull mrengineer09/rabbitmq:plugins
docker pull mrengineer09/rabbitmq:3.13-plugins
```

**Repositorio**: https://hub.docker.com/r/mrengineer09/rabbitmq

## 🔄 Actualizar Topología

Para modificar exchanges, queues o bindings:

1. Edita `rabbitmq-definitions.json`
2. Reconstruye la imagen: `docker build -t mrengineer09/rabbitmq:plugins .`
3. Reinicia el contenedor: `docker-compose down && docker-compose up -d`

**Nota**: Las definiciones se cargan automáticamente al iniciar el contenedor.

## 📝 Notas

- El usuario `guest` está deshabilitado por seguridad
- Las definiciones se importan automáticamente en el arranque
- Los volúmenes persisten datos de RabbitMQ
- El contenedor reinicia automáticamente a menos que se detenga manualmente

## 🛠️ Solución de Problemas

### Contenedor no inicia
```bash
docker logs rabbitmq
```

### No puedo acceder al Management UI
- Verifica que el puerto 15672 esté disponible
- Usa las credenciales correctas (tesis/tesis)
- Comprueba que el .env tenga los valores correctos

### Queues/Exchanges no se crean
- Verifica que `rabbitmq-definitions.json` sea válido JSON
- Revisa los logs: `docker logs rabbitmq | grep Importing`

## 📄 Licencia

RabbitMQ está bajo MPL 2.0 License.

---

**Creado para**: Tesis - Backend Latacunga Clean  
**Fecha**: Noviembre 2025  
**Autor**: Andres09xZ
