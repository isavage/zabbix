# Zabbix Docker Compose Setup

This Docker Compose setup installs Zabbix using the official Zabbix Docker images with PostgreSQL to monitor your VPS performance.

## Components

- **PostgreSQL Database**: Stores Zabbix data.
- **Zabbix Server**: Core monitoring server.
- **Zabbix Web UI**: Web interface accessible via reverse proxy.
- **Zabbix Agent**: Monitors the host VPS system.

## Configuration

Sensitive information is stored in the `.env` file. Update it as needed.

## Running the Setup

1. Navigate to this directory.
2. Run `docker-compose up -d` to start the services.
3. The Zabbix web UI is not exposed directly; configure your reverse proxy to connect to the `zabbix-web` container on port 8080 within the `zabbix-net` network.
4. Default login: Admin / zabbix.

## Nginx Reverse Proxy

To access the Zabbix web UI via server.varunrs.in using your Nginx reverse proxy container:

1. Ensure your Nginx container uses the `zabbix-net` network as an external network.
2. Add the following server block to your Nginx config:

```
server {
    listen 80;
    server_name server.varunrs.in;

    location / {
        proxy_pass http://zabbix-web:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

For HTTPS, add SSL certificates and listen on 443.

## Security Notes

- Change default passwords in the `.env` file.
- Consider using Docker secrets for production.
- Ensure the agent has necessary permissions to monitor the host.

## Monitoring VPS

The Zabbix agent is configured with network_mode: host to monitor the VPS directly. It will collect CPU, memory, disk, network, etc., metrics.
