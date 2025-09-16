# n8n Docker Compose Setup

This setup provides a complete n8n instance with PostgreSQL database, IPv6 support, and HTTPS configuration for use with Cloudflare SSL.

## Features

- **n8n**: Latest version with PostgreSQL backend
- **PostgreSQL**: Persistent database storage
- **IPv6 Support**: Enabled in Docker network
- **HTTPS Ready**: Configured for Cloudflare SSL termination
- **Production Ready**: Optimized for production use

## Quick Start

1. **Clone and Navigate**
   ```bash
   cd /home/saim/Documents/Dev/docker/n8n
   ```

2. **Configure Environment**
   ```bash
   cp .env.example .env
   nano .env  # Edit with your configuration
   ```

3. **Important: Update Security Settings**
   - Change `N8N_ENCRYPTION_KEY` to a secure random key (minimum 32 characters)
   - Update `N8N_HOST` to your domain name
   - Change database passwords
   - Update `WEBHOOK_URL` to your domain

4. **Generate Secure Encryption Key**
   ```bash
   openssl rand -hex 32
   ```

5. **Start Services**
   ```bash
   docker-compose up -d
   ```

6. **Check Status**
   ```bash
   docker-compose ps
   docker-compose logs -f n8n
   ```

## Configuration

### Environment Variables

Key variables to configure in `.env`:

- `N8N_HOST`: Your domain name (without https://)
- `N8N_ENCRYPTION_KEY`: Secure random key for encryption
- `POSTGRES_PASSWORD`: Secure database password
- `WEBHOOK_URL`: Full webhook URL with your domain

### Cloudflare Setup

1. **DNS Configuration**
   - Point your domain to your server's IPv4 and IPv6 addresses
   - Enable Cloudflare proxy (orange cloud)

2. **SSL/TLS Settings**
   - Set SSL/TLS encryption mode to "Full" or "Full (Strict)"
   - Enable "Always Use HTTPS"

3. **Port Configuration**
   - Ensure port 5678 is accessible from your server
   - Configure Cloudflare rules if needed

## Network Configuration

The setup creates a custom bridge network with IPv6 support:
- IPv4 subnet: `172.20.0.0/16`
- IPv6 subnet: `fd00:db8:a::/64`

## Data Persistence

Data is stored in Docker volumes:
- `n8n_data`: n8n workflows and settings
- `postgres_data`: Database data

## Management Commands

```bash
# Start services
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs -f n8n
docker-compose logs -f postgres

# Update to latest version
docker-compose pull
docker-compose up -d

# Backup database
docker-compose exec postgres pg_dump -U n8n n8n > backup.sql

# Restore database
cat backup.sql | docker-compose exec -T postgres psql -U n8n -d n8n
```

## Security Considerations

1. **Change Default Passwords**: Update all passwords in `.env`
2. **Firewall**: Only expose port 5678 to Cloudflare IPs
3. **Updates**: Regularly update the Docker images
4. **Monitoring**: Monitor logs for suspicious activity

## Troubleshooting

### Common Issues

1. **Connection Refused**
   - Check if ports are properly exposed
   - Verify Cloudflare proxy settings

2. **Database Connection Issues**
   - Ensure PostgreSQL is running: `docker-compose ps`
   - Check database logs: `docker-compose logs postgres`

3. **IPv6 Issues**
   - Verify Docker daemon has IPv6 enabled
   - Check system IPv6 configuration

### Health Checks

```bash
# Check if n8n is responding
curl -I http://localhost:5678/healthz

# Check database connection
docker-compose exec postgres pg_isready -U n8n
```

## Ports

- **5678**: n8n web interface (HTTP, will be proxied through Cloudflare)
- **5432**: PostgreSQL (internal only, not exposed)

## Support

For n8n specific issues, refer to the [official n8n documentation](https://docs.n8n.io/).
