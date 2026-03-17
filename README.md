# Remix

React Router v7 (Remix) app deployed to EC2 via GitHub Actions.

## Development

```bash
pnpm install
pnpm dev
```

Open [http://localhost:5173](http://localhost:5173).

## Production Build

```bash
pnpm build
pnpm start   # serves on port 3000
```

## EC2 Server Setup

Run these once on a fresh Ubuntu EC2 instance:

```bash
# Install Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# Install pnpm
corepack enable && corepack prepare pnpm@latest --activate

# Install nginx
sudo apt-get install -y nginx

# Create app directory
sudo mkdir -p /opt/remix
sudo chown ubuntu:ubuntu /opt/remix

# Install systemd service
sudo cp deploy/remix.service /etc/systemd/system/remix.service
sudo systemctl daemon-reload
sudo systemctl enable remix

# Install nginx config
sudo cp deploy/nginx.conf /etc/nginx/sites-available/remix
sudo ln -sf /etc/nginx/sites-available/remix /etc/nginx/sites-enabled/default
sudo nginx -t && sudo systemctl restart nginx
```

## GitHub Actions Secrets

Add these secrets in your repo settings (**Settings > Secrets and variables > Actions**):

| Secret         | Description                              |
| -------------- | ---------------------------------------- |
| `EC2_HOST`     | Public IP or hostname of the EC2 instance |
| `EC2_USER`     | SSH user (typically `ubuntu`)            |
| `EC2_SSH_KEY`  | Private SSH key for the EC2 instance     |

Pushes to `main` will automatically build and deploy.

## Project Structure

```
deploy/
  remix.service     # systemd unit file
  nginx.conf        # nginx reverse proxy config
.github/workflows/
  deploy.yml        # GitHub Actions CI/CD
app/
  root.tsx          # root layout
  routes/           # file-based routes
  app.css           # global styles (Tailwind)
```
