# Understanding TLS & Issuing a Certificate with Let's Encrypt

TLS (Transport Layer Security) is a cryptographic protocol that secures communication between clients and servers.

## What is TLS?

**TLS** is a cryptographic protocol that encrypts communication between two systems, typically between:

- A **client** (e.g., a web browser)
- A **server** (e.g., website)

TLS ensures:

- 🔒 **Confidentiality** — nobody can read the content in transit
- 🧮 **Integrity** — nobody can tamper with it unnoticed
- 🔐 **Authentication** — the client is sure it's talking to the real server

Modern TLS uses **asymmetric encryption** to negotiate a secure session and then switches to **symmetric encryption** for performance.

## What is Let's Encrypt?

[Let's Encrypt](https://letsencrypt.org/) is a free, automated, and open Certificate Authority (CA) backed by major sponsors (including Mozilla, EFF, and Cisco).

It provides:

- ✅ Free TLS/SSL certificates
- ⚙️ Automation via the **ACME** protocol
- 🔄 Short-lived certs (90 days) with renewal support

## Certbot Challenge Types

Let’s Encrypt uses **challenges** to verify that you control a domain. Certbot supports:

### 👨‍💻 HTTP-01 Challenge (Default)

Certbot creates a temporary file on your web server at:

    http://example.com/.well-known/acme-challenge/...

Let’s Encrypt accesses that file to confirm domain control.

- Requires port 80 to be open  
- Works with: `--standalone`, `--nginx`, `--apache`  
- **Most common and easiest**

### 📦 DNS-01 Challenge

You create a special TXT record in your DNS zone:

    _acme-challenge.example.com

Let’s Encrypt checks the record to validate domain ownership.

- Required for wildcard certificates (e.g., `*.example.com`)
- Useful for servers **not publicly accessible**
- Use: `--manual` or DNS plugins (e.g., `--dns-cloudflare`)

### ✉️ TLS-ALPN-01 Challenge (Advanced)

Let’s Encrypt connects over port 443 and expects a special TLS extension.

- Works only with certain configurations and clients
- Use case: advanced load balancers or environments without port 80
- Certbot requires the `--preferred-challenges tls-alpn` flag

## Step-by-Step: Issue a Certificate with Certbot

### 1. Install Certbot

Certbot is the recommended ACME client by the EFF.

#### With system package manager (older version)

```bash
sudo apt update
sudo apt install certbot
```

#### With python & pip (latest version)

``` bash
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
sudo /opt/certbot/bin/pip install certbot
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
```

#### With docker (for containerized environment)

``` bash
sudo docker pull certbot/certbot
```

### 2. Choose a plugin and issue a certificate

- Option A: With running webserver:
  - `sudo certbot --nginx`
  - Certbot auto-detects Nginx config and updates it.
- Option B: Standalone (no web server running):
  - `sudo certbot certonly --standalone -d domain.com`
  - Certbot runs a temporary web server to complete HTTP validation.
- Option C: Dockerized environment:

  - ``` bash
    sudo docker run -it --rm --name certbot \
    -v "/etc/letsencrypt:/etc/letsencrypt" \
    -v "/var/lib/letsencrypt:/var/lib/letsencrypt" \
    certbot/certbot certonly
    ```

- For a wildcard cert:
  - Choose right plugin for your DNS provider
    - [Cloudflare](https://pypi.org/project/certbot-dns-cloudflare/)
    - [AWS](https://pypi.org/project/certbot-dns-route53/)
    - [Digital Ocean](https://pypi.org/project/certbot-dns-digitalocean/)
    - etc. More info here <https://eff-certbot.readthedocs.io/en/stable/packaging.html>

### 3. Verify Certificate Location

Certificates are typically stored in:

    /etc/letsencrypt/live/example.com/

Files:

- fullchain.pem: the certificate + chain
- privkey.pem: your private key

### 4. Test renewal

    ``` bash
    sudo certbot renew --dry-run
    ```

### 5. Setup cron or systemd timer

Let’s Encrypt certificates are valid for 90 days. Certbot sets up automatic renewal.

#### Tips

- Certs expire every 90 days
- Always open ports 80/443 for validation
- Use staging (--staging) for tests

## Common Troubleshooting

| Problem              | Solution                                 |
|----------------------|------------------------------------------|
| 🔴 Port 80 closed    | Make sure HTTP is open for validation    |
| ❗ Too many requests | Use --staging to avoid rate limits       |
| 🔒 Permission denied | Run Certbot with sudo                    |

## Pro Tips

- Use DNS-01 challenge for wildcard certs or private networks
- Combine with firewall rules and automatic reload
- Always monitor your logs at /var/log/letsencrypt/

## References

- 🔗 [Let’s Encrypt Documentation](https://letsencrypt.org/docs/)
- 🛠️ [Certbot Instructions per OS/web server](https://certbot.eff.org/)
- 🧩 [Certbot Docs on Read The Docs](https://eff-certbot.readthedocs.io/en/stable/intro.html)
- 📘 [TLS Deep Dive (Cloudflare)](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake/)
