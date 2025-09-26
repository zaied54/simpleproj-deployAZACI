This repo contains sample code to test deploy docker hub images to Azure Container Instances
Nice little index.html, nginx serves it.

-- To Build Docker image local:
podman build --platform linux/amd64 --rm -f Dockerfile -t simplenodeproj:v2 .

-- To Tag local image before pushing to Docker Hub:
podman tag simplenodeproj:v2 docker.io/zaied112454/simplenodeproj:v2

-- To push local docker image to docker hub registry:
podman push docker.io/zaied112454/simplenodeproj:v2 


-- To create Azure Container Instance in Azure CLI:
az container create -g testcontainerinstancerg --name simplenodeproj --image zaied112454/simplenodeproj:v2 --os-type Linux --cpu 1 --memory 1 --ports 8080 8443

-- If ACI created in Azure portal make sure to have public ip and DNS Label created - simplenodeproj, simplenodeproj.c3f6f4gbbgbadfb8.canadacentral.azurecontainer.io
since the image was public no credential needed

Challenges: 

** Container was restaring again and again in Azure whereas local container was running fine
reason was 

OS Architecture Mismatch
If you built your image on an M1/M2 Mac, it may default to linux/arm64, which ACI doesn’t support.
✅ Fix: Rebuild your image with:
bash
docker build --platform linux/amd64 -t myimage .

** Port issue: 
Error: rootlessport cannot expose privileged port 443, you can add 'net.ipv4.ip_unprivileged_port_start=443' to /etc/sysctl.conf (currently 1024), or choose a larger port number (>= 1024): listen tcp 0.0.0.0:443: bind: permission denied
-> used 8443:443 and 8080:80


Having Below in Dockerfile is important,
ENTRYPOINT [ "nginx", "-g", "daemon off;" ] -> -g tells to run it in foreground not background, daemon off means same

tested localhost:8080 and simplenodeproj.c3f6f4gbbgbadfb8.canadacentral.azurecontainer.io -> brings up the text

