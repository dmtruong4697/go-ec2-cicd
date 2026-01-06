# Go EC2 CI/CD Example

A minimal example of **CI/CD for a Golang application deployed to AWS EC2 using GitHub Actions**.

Every time code is merged or pushed to the `main` branch, the application is automatically built and deployed to the EC2 instance.

---

## 🛠 Tech Stack

- **Go** - Backend application
- **GitHub Actions** - CI/CD automation
- **AWS EC2** - Deployment target
- **systemd** - Service management

---

## 📁 Project Structure

```
.
├── main.go
├── go.mod
├── .github/
│   └── workflows/
│       └── deploy.yml
└── README.md
```

---

## 🚀 Run Locally

**Requirements:** Go 1.20+

```bash
go run main.go
```

Open in browser:
```
http://localhost:8080
```

---

## 🔄 CI/CD Flow

```
Push or merge to main
    ↓
GitHub Actions runner clones the repo
    ↓
Build Linux Go binary
    ↓
Copy binary to EC2 via SSH
    ↓
Restart systemd service
```

> **Note:** The EC2 instance does not pull the repository. It only runs the deployed binary.

---

## ☁️ EC2 Setup

### Prerequisites

- **OS:** Amazon Linux or Ubuntu
- **Security Group:** Open port `8080`

### Create Application Directory

```bash
sudo mkdir -p /var/www/go-app
sudo chown -R ec2-user:ec2-user /var/www/go-app
```

---

## ⚙️ systemd Service

### Create Service File

Create `/etc/systemd/system/go-app.service`:

```ini
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
```

### Enable the Service

```bash
sudo systemctl daemon-reload
sudo systemctl enable go-app
sudo systemctl start go-app
```

---

## 🔐 GitHub Secrets

Configure the following repository secrets in **Settings → Secrets and variables → Actions**:

| Name | Description |
|------|-------------|
| `EC2_HOST` | EC2 public IP or public DNS |
| `EC2_USER` | SSH user (e.g., `ec2-user` or `ubuntu`) |
| `EC2_SSH_KEY` | Private SSH key (`.pem` file content) |

---

## 📦 Deployment

After pushing or merging code into the `main` branch:

1. GitHub Actions runs automatically
2. The service is restarted with the new binary
3. Access the application at:

```
http://<EC2_PUBLIC_IP>:8080
```

---

## ⚠️ Security Notes

- **Never** commit private keys or secrets to the repository
- GitHub Secrets are encrypted and not exposed in logs
- Public repositories are safe as long as secrets are properly configured

---

## 📝 License

This project is open source and available under the MIT License.

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!