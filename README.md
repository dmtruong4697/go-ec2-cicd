# Go EC2 CI/CD Example

A minimal example of **CI/CD for a Golang application deployed to AWS EC2 using GitHub Actions**.

Every time code is merged or pushed to the `main` branch, the application is automatically built and deployed to the EC2 instance.

---

## Tech Stack
- Go
- GitHub Actions
- AWS EC2
- systemd

---

## Project Structure
.
├── main.go
├── go.mod
├── .github/workflows/deploy.yml
└── README.md

yaml
Copy code

---

## Run Locally
Requirements: Go 1.20+

```bash
go run main.go
Open in browser:

arduino
Copy code
http://localhost:8080
CI/CD Flow
sql
Copy code
Push or merge to main
→ GitHub Actions runner clones the repo
→ Build Linux Go binary
→ Copy binary to EC2 via SSH
→ Restart systemd service
The EC2 instance does not pull the repository. It only runs the deployed binary.

EC2 Setup
OS: Amazon Linux or Ubuntu

Open port 8080 in the Security Group

Create application directory:

bash
Copy code
sudo mkdir -p /var/www/go-app
sudo chown -R ec2-user:ec2-user /var/www/go-app
systemd Service
Create the service file:

ini
Copy code
# /etc/systemd/system/go-app.service
[Unit]
Description=Go App
After=network.target

[Service]
User=ec2-user
WorkingDirectory=/var/www/go-app
ExecStart=/var/www/go-app/app
Restart=always

[Install]
WantedBy=multi-user.target
Enable the service:

bash
Copy code
sudo systemctl daemon-reload
sudo systemctl enable go-app
GitHub Secrets
Configure the following repository secrets:

Name	Description
EC2_HOST	EC2 public IP or public DNS
EC2_USER	SSH user (e.g. ec2-user or ubuntu)
EC2_SSH_KEY	Private SSH key (.pem content)

Deployment
After pushing or merging code into the main branch:

GitHub Actions runs automatically

The service is restarted with the new binary

Access the application:

cpp
Copy code
http://<EC2_PUBLIC_IP>:8080
Notes
Do not commit private keys or secrets to the repository

GitHub Secrets are not exposed in logs

Public repositories are safe as long as secrets are not committed