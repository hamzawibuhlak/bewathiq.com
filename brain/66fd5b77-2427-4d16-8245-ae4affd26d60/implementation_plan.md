# HTTPS Implementation Plan

## Goal
Enable HTTPS for `bewathiq.com` and `www.bewathiq.com` to ensure secure communication.

## Steps

### 1. Certificate Generation (On VPS)
*   **Action**: SSH into VPS and run Certbot.
*   **Method**: Use standalone mode (requires stopping Nginx temporarily) to avoid complex webroot mapping initially.
*   **Command**: `certbot certonly --standalone -d bewathiq.com -d www.bewathiq.com`

### 2. Configuration Updates (Local -> Deploy)
*   **`docker-compose.prod.yml`**: Update the `frontend` service to mount the Let's Encrypt directory.
    ```yaml
    volumes:
      - /etc/letsencrypt:/etc/letsencrypt:ro
    ```
*   **`frontend/nginx.conf`**:
    *   Add a new `server` block listening on 443 ssl.
    *   Configure SSL certificate paths:
        *   Certificate: `/etc/letsencrypt/live/bewathiq.com/fullchain.pem`
        *   Key: `/etc/letsencrypt/live/bewathiq.com/privkey.pem`
    *   Add a redirection server block on port 80 to redirect all traffic to HTTPS.

### 3. Deployment
*   Upload updated configuration files.
*   Recreate the frontend container.

## Verification
*   Visit `https://bewathiq.com` -> Should load securely.
*   Visit `http://bewathiq.com` -> Should redirect to HTTPS.
