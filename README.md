# Quisin - Restaurant Management System

A modern, full-stack restaurant management system built with React, TypeScript, and Node.js.

## System Requirements

- Node.js 18.x or higher
- NPM 9.x or higher
- PostgreSQL 14.x or higher
- Nginx (for reverse proxy)
- PM2 (for process management)

## Server Setup

1. Update and upgrade your system:
```bash
sudo apt update && sudo apt upgrade -y
```

2. Install Node.js and NPM:
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
```

3. Install PostgreSQL:
```bash
sudo apt install postgresql postgresql-contrib -y
```

4. Install Nginx:
```bash
sudo apt install nginx -y
```

5. Install PM2 globally:
```bash
sudo npm install -g pm2
```

## Database Setup

1. Create a new PostgreSQL database:
```bash
sudo -u postgres psql
CREATE DATABASE quisin;
CREATE USER quisin_user WITH ENCRYPTED PASSWORD 'your_password';
GRANT ALL PRIVILEGES ON DATABASE quisin TO quisin_user;
\q
```

## Application Deployment

1. Clone the repository:
```bash
git clone <your-repository-url>
cd quisin
```

2. Install dependencies:
```bash
npm install
```

3. Create a `.env` file in the project root:
```env
NODE_ENV=production
PORT=3000
DATABASE_URL=postgresql://quisin_user:your_password@localhost:5432/quisin
JWT_SECRET=your_secure_jwt_secret
```

4. Build the application:
```bash
npm run build
```

5. Start the application with PM2:
```bash
pm2 start npm --name "quisin" -- start
pm2 save
pm2 startup
```

## Nginx Configuration

1. Create a new Nginx configuration file:
```bash
sudo nano /etc/nginx/sites-available/quisin
```

2. Add the following configuration:
```nginx
server {
    listen 80;
    server_name your_domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

3. Enable the site and restart Nginx:
```bash
sudo ln -s /etc/nginx/sites-available/quisin /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## SSL Configuration (Optional but Recommended)

1. Install Certbot:
```bash
sudo apt install certbot python3-certbot-nginx -y
```

2. Obtain SSL certificate:
```bash
sudo certbot --nginx -d your_domain.com
```

## Security Considerations

1. Configure UFW firewall:
```bash
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
```

2. Set up fail2ban:
```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

## Monitoring and Maintenance

1. Monitor application logs:
```bash
pm2 logs quisin
```

2. Monitor system resources:
```bash
pm2 monit
```

3. View application status:
```bash
pm2 status
```

4. Update application:
```bash
git pull
npm install
npm run build
pm2 restart quisin
```

## Backup Strategy

1. Database backup (create a cron job):
```bash
sudo -u postgres pg_dump quisin > /path/to/backups/quisin_$(date +%Y%m%d).sql
```

2. Application backup:
```bash
tar -czf /path/to/backups/quisin_$(date +%Y%m%d).tar.gz /path/to/quisin
```

## Troubleshooting

1. Check application logs:
```bash
pm2 logs quisin
```

2. Check Nginx logs:
```bash
sudo tail -f /var/log/nginx/error.log
sudo tail -f /var/log/nginx/access.log
```

3. Check system logs:
```bash
journalctl -u nginx
journalctl -u postgresql
```

## Performance Optimization

1. Enable Nginx caching:
```nginx
location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
    expires 30d;
    add_header Cache-Control "public, no-transform";
}
```

2. Configure PM2 cluster mode:
```bash
pm2 start npm --name "quisin" -i max -- start
```

## Support

For issues and support:
- Create an issue in the repository
- Contact the development team
- Check the documentation

## License

[Your License] - See LICENSE file for details