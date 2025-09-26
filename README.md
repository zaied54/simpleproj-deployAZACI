# 🚀 Azure Container Instance Deployment Sample

This repository contains sample code to test deploying Docker Hub images to **Azure Container Instances (ACI)**.  
A simple `index.html` is served using **Nginx**.

---

## 🛠️ Local Docker Image Workflow

### 🔧 Build Docker Image Locally
```bash
podman build --platform linux/amd64 --rm -f Dockerfile -t simplenodeproj:v2 .

🏷️ Tag Local Image for Docker Hub

podman tag simplenodeproj:v2 docker.io/zaied112454/simplenodeproj:v2

🚀 Push Image to Docker Hub

podman push docker.io/zaied112454/simplenodeproj:v2

☁️ Deploy to Azure Container Instance

Using Azure CLI

az container create -g testcontainerinstancerg \
  --name simplenodeproj \
  --image zaied112454/simplenodeproj:v2 \
  --os-type Linux \
  --cpu 1 \
  --memory 1 \
  --ports 8080 8443

If Using Azure Portal

Ensure Public IP and DNS Label are configured.

Example DNS:simplenodeproj.c3f6f4gbbgbadfb8.canadacentral.azurecontainer.io

No credentials needed since the image is public.

⚠️ Challenges Faced

🔁 Container Restarting in Azure

Even though the container ran fine locally, it kept restarting in ACI.

🧬 OS Architecture Mismatch

If built on an M1/M2 Mac, the image may default to linux/arm64, which ACI doesn’t support.

✅ Fix:

docker build --platform linux/amd64 -t myimage .

🔒 Port Binding Issue

Error:

rootlessport cannot expose privileged port 443...
bind: permission denied

✅ Fix: Used non-privileged ports:

8443:443

8080:80

📦 Dockerfile Essentials

Make sure to include:

ENTRYPOINT [ "nginx", "-g", "daemon off;" ]

-g runs Nginx in the foreground

daemon off; disables background mode

✅ Tested URLs

Localhost: http://localhost:8080

Azure: http://simplenodeproj.c3f6f4gbbgbadfb8.canadacentral.azurecontainer.io

Both successfully serve the sample text.
