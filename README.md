Deploying a Vite ReactJS project on a Linux cloud virtual machine involves several steps, including setting up the server environment, building the project, and serving the static files. Here’s a step-by-step guide:

---

### **1. Prepare the Linux Cloud Virtual Machine**
1. **Update the system:**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
2. **Install Node.js and npm:**
   - Check if Node.js and npm are installed:
     ```bash
     node -v
     npm -v
     ```
   - If not installed, install them:
     ```bash
     curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
     sudo apt install -y nodejs
     ```
3. **Install a web server (e.g., Nginx or Apache):**
   ```bash
   sudo apt install nginx -y
   ```

---

### **2. Build the Vite Project**
1. **Navigate to the project directory:**
   ```bash
   cd /path/to/your/vite-project
   ```
2. **Install dependencies:**
   ```bash
   npm install
   ```
3. **Build the project:**
   ```bash
   npm run build
   ```
   This generates a `dist` folder containing the static files.

---

### **3. Transfer Files to the Server**
1. **If building locally:**
   - Compress the `dist` folder:
     ```bash
     tar -czvf dist.tar.gz dist/
     ```
   - Transfer to the server using `scp`:
     ```bash
     scp dist.tar.gz user@server_ip:/var/www
     ```
   - Extract on the server:
     ```bash
     ssh user@server_ip
     cd /var/www
     tar -xzvf dist.tar.gz
     ```

2. **If building on the server:**
   Copy the Vite project to the server using `scp`, and build it there:
   ```bash
   scp -r /path/to/your/vite-project user@server_ip:/var/www
   ssh user@server_ip
   cd /var/www/vite-project
   npm install
   npm run build
   ```

---

### **4. Configure the Web Server**
#### **For Nginx:**
1. **Edit the Nginx configuration file:**
   ```bash
   sudo nano /etc/nginx/sites-available/vite-app
   ```
2. **Add the configuration:**
   ```nginx
   server {
       listen 80;
       server_name your_domain_or_ip;

       root /var/www/dist;
       index index.html;

       location / {
           try_files $uri /index.html;
       }
   }
   ```
3. **Enable the configuration:**
   ```bash
   sudo ln -s /etc/nginx/sites-available/vite-app /etc/nginx/sites-enabled/
   sudo nginx -t
   sudo systemctl reload nginx
   ```

#### **For Apache:**
1. **Enable required modules:**
   ```bash
   sudo a2enmod rewrite
   sudo systemctl restart apache2
   ```
2. **Edit the Apache configuration:**
   ```bash
   sudo nano /etc/apache2/sites-available/vite-app.conf
   ```
3. **Add the configuration:**
   ```apache
   <VirtualHost *:80>
       ServerName your_domain_or_ip
       DocumentRoot /var/www/dist

       <Directory /var/www/dist>
           Options Indexes FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```
4. **Enable the site and restart Apache:**
   ```bash
   sudo a2ensite vite-app
   sudo systemctl reload apache2
   ```

---

### **5. Test the Deployment**
1. Access the application by navigating to your server's IP or domain in a browser:
   ```
   http://your_server_ip
   ```

2. If using a domain, ensure DNS records point to your server.

---

### **Optional: Secure with HTTPS**
1. Install **Certbot** for SSL:
   ```bash
   sudo apt install certbot python3-certbot-nginx
   ```
2. Obtain and apply a certificate:
   ```bash
   sudo certbot --nginx -d your_domain
   ```

Your Vite ReactJS project should now be live!
