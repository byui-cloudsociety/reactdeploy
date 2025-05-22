## Step 1: Launch an EC2 Instance

1. Log into the AWS Management Console
2. Navigate to EC2 Dashboard
3. Click "Launch Instance"
4. Configure your instance:
   - **Name**: Give your instance a descriptive name (e.g., "React-App-Server")
   - **AMI**: Select "Ubuntu Server 22.04 LTS" (free tier eligible)
   - **Instance Type**: Choose "t2.micro" (free tier eligible)
   - **Key Pair**: Select existing one
     - Click the dropdown and select "vockey"
   - **Network Settings**: 
     - Allow SSH traffic from your IP
     - Allow HTTP traffic from the internet
     - Allow HTTPS traffic from the internet
   - **Storage**: 8 GB gp3 is sufficient
5. Click "Launch Instance"

## Step 2: Connect to Your EC2 Instance

1. Wait for instance state to show "Running"
2. Click the checkbox next to your EC2 name
3. Click the "Actions" dropdown then press "Connect"
4. Press Connect on the "EC2 Instance Connect" tab

## Step 3: Install Required Software

Once connected to your EC2 instance, run the following commands:

### Update the system
```bash
sudo apt update
sudo apt upgrade -y
```

### Install Node.js and npm
```bash
sudo apt-get install -y nodejs
sudo apt-get install -y npm

# Verify installation
node --version
npm --version
```

### Install Git
```bash
sudo apt install git -y
```

### Install Nginx (web server)
```bash
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

## Step 4: Clone and Build the React App

### Clone the repository
```bash
cd ~
git clone https://github.com/byui-cloudsociety/reactdeploy.git
cd reactdeploy
```

### Install dependencies
```bash
npm install
```

### Build the production version
```bash
npm run build
```

### Move Build Files
```bash
sudo cp -r /home/ubuntu/reactdeploy/build /var/www/react-app
sudo chown -R www-data:www-data /var/www/react-app
```

## Step 5: Configure Nginx

### Create Nginx configuration
```bash
sudo nano /etc/nginx/sites-available/react-app
```

Add the following configuration:
```nginx
server {
    listen 80;
    server_name your-instance-public-ip;
    
    root /var/www/react-app;
    index index.html;
    
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

### Enable the site
```bash
sudo ln -s /etc/nginx/sites-available/react-app /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
```

### Test and reload Nginx
```bash
sudo nginx -t
sudo systemctl reload nginx
```
