# Deploying RabbitMQ to Render

## ⚡ Quick Deploy

### Option 1: Using Docker Image from Docker Hub (Recommended)

1. Go to [Render Dashboard](https://dashboard.render.com)
2. Click **+ New**
3. Select **Web Service**
4. Choose **Deploy an existing image**
5. Enter the image URL: `mrengineer09/rabbitmq:plugins`
6. Configure:
   - **Name**: `rabbitmq`
   - **Runtime**: `docker`
   - **Port**: `5672`
   - **Instance Type**: `Standard` (minimum for RabbitMQ)

### Option 2: Using GitHub Repository (with docker-compose)

1. Connect your GitHub repository
2. Point to the `/RabbitMQ` directory
3. Render will auto-detect `docker-compose.yml`
4. Set environment variables in Render dashboard

## 🔐 Environment Variables

Add these to Render dashboard:

```
RABBITMQ_USER=tesis
RABBITMQ_PASS=tesis
```

## 🔧 Health Check Configuration

Render needs to know how to check if RabbitMQ is healthy. Since RabbitMQ uses AMQP (port 5672) for connections and HTTP (port 15672) for the Management UI:

**Health Check Settings:**
- **Protocol**: HTTP
- **Path**: `/api/alarms`
- **Port**: `15672`
- **Timeout**: `10` seconds
- **Start Period**: `60` seconds
- **Interval**: `30` seconds

This ensures Render pings the Management API instead of trying to connect via AMQP.

## 🚀 Ports Exposed

- **5672**: AMQP (RabbitMQ protocol)
- **15672**: Management UI (HTTP)
- **25672**: Clustering (internal, don't expose)

## 📦 Disk/Storage

For production, set up persistent storage:

- **Size**: At least 1GB
- **Mount Path**: `/var/lib/rabbitmq`

This persists RabbitMQ data between restarts.

## ✅ After Deployment

1. **Access Management UI**:
   - URL: `https://<your-render-service>.onrender.com:15672`
   - Username: `tesis`
   - Password: `tesis`

2. **Connect via AMQP**:
   - Connection String: `amqp://tesis:tesis@<your-render-service>.onrender.com:5672/`

3. **Verify Topology**:
   - Check Management UI for all 4 exchanges
   - Check for all 16 queues

## 🐛 Troubleshooting

### Container won't start
- Check logs in Render dashboard
- Ensure environment variables are set
- Verify image is available on Docker Hub

### Health check keeps failing
- Make sure port `15672` is accessible
- Check that Management UI is responding
- Verify the API endpoint: `/api/alarms`

### Can't connect via AMQP
- Ensure port `5672` is exposed
- Use correct credentials: `tesis:tesis`
- Check firewall/network policies

## 📝 Monitoring

Once deployed, you can:

1. **View Metrics**: Check RabbitMQ's Prometheus endpoint (if needed)
2. **Monitor Queue Depth**: Via Management UI
3. **Check Connection Status**: Via API or Management UI

## 🔄 Updates

To update the RabbitMQ deployment:

1. Rebuild and push image to Docker Hub:
   ```bash
   docker build -t mrengineer09/rabbitmq:plugins .
   docker push mrengineer09/rabbitmq:plugins
   ```

2. In Render dashboard, click **Manual Deploy** to redeploy

## 📞 Support

For issues:
- Check RabbitMQ logs in Render dashboard
- Review the local README.md for configuration details
- Verify all image assets were pushed correctly to Docker Hub

---

**Last Updated**: November 16, 2025  
**Image**: `mrengineer09/rabbitmq:plugins`  
**Version**: RabbitMQ 3.13 with Management UI
