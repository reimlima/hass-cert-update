# Home Assistant SSL Certificate Update Script

![Bash](https://img.shields.io/badge/Bash-5.2.21-blue)
![ShellCheck](https://img.shields.io/badge/ShellCheck-passing-brightgreen)

This script automates the process of updating SSL certificates in a Home Assistant instance running in a Docker container. It works by copying the latest SSL certificates from an Nginx Proxy Manager container to the Home Assistant container.

## Important Notes

- This script has been tested with all containers running on the same host as the script
- The Home Assistant instance must have the following configuration in its `configuration.yaml`:
  ```yaml
  http:
    ssl_certificate: /config/fullchain.pem
    ssl_key: /config/privkey.pem
  ```
- All paths in the script are set to the default values as they come in the container images. If you have a custom installation, you'll need to update these paths in the script.

## Requirements

- Bash 5.2.21 or higher
- Docker
- Nginx Proxy Manager container
- Home Assistant container
- ShellCheck (for linting)

## Usage

```bash
./hass-cert-update --host your.domain.com
```

### Parameters

- `-h, --host`: The hostname to be used as reference for SSL check (required)
- `--help`: Show help message

## Cron Job Setup

To set up the cron job:

1. Copy the `hass-cert-update.cron` file to `/etc/cron.d/`:
   ```bash
   sudo cp hass-cert-update.cron /etc/cron.d/
   ```

2. Edit the cron file to match your setup:
   ```bash
   sudo nano /etc/cron.d/hass-cert-update.cron
   ```

3. Update the path to the script and your domain:
   ```
   */5 * * * * root /absolute/path/to/hass-cert-update --host your.domain.com
   ```

4. Make sure the script is executable:
   ```bash
   sudo chmod +x /path/to/hass-cert-update
   ```

## How It Works

1. The script downloads the current certificate from the Home Assistant container
2. It compares the expiration date of the Home Assistant certificate with the online certificate from Nginx Proxy Manager
3. If the certificates have different expiration dates, it:
   - Identifies the Home Assistant and Nginx Proxy Manager containers
   - Copies the latest certificate files from Nginx Proxy Manager to Home Assistant
   - Updates the certificate files in the Home Assistant container

## Default Paths

- Home Assistant container config path: `/config`
- Nginx Proxy Manager container cert path: `/etc/letsencrypt/archive/npm-7`

If your installation uses different paths, you'll need to modify these values in the script. 