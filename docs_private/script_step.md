# MOSIP WebSub Hub - Build and Run Guide

This document describes how to configure, build, and run the **MOSIP WebSub Hub** service locally using Docker.

The WebSub Hub requires a configuration file (`Config.toml`) that is downloaded during the Docker image build process.

---

# 1. Prepare the Configuration File

Copy the example configuration file into `/tmp` and rename it.

```bash
cp config/websub-hub-Config.example.toml /tmp/websub-hub-Config.toml


#2. Expose the Configuration File via HTTP
cd /tmp
python3 -m http.server 18080

`INFO`
Your configuration file will now be accessible at:
http://<HOST_IP>:18080/websub-hub-Config.toml
`Example`
http://10.0.9.67:18080/websub-hub-Config.toml

You can verify access:
curl http://10.0.9.67:18080/websub-hub-Config.toml

#3. Build the Docker Image
Navigate to the hub service directory:

cd hub

#Build the Docker image and pass the configuration URL as a build argument:
docker build \
  --build-arg hub_config_url="http://10.0.9.67:18080/websub-hub-Config.toml" \
  -t mosip-websub-hub:1.1.5.3 .

#success case
If the build completes successfully, Docker 
will output something similar to:

Successfully built <IMAGE_ID>
Successfully tagged mosip-websub-hub:1.1.5.3


#4. Run the WebSub Hub Container

docker run -d \
  --name mosip-websub-hub \
  -p 9191:9191 \
  mosip-websub-hub:1.1.5.3

#5. Check Container Logs

docker logs -f mosip-websub-hub
#Typical startup log:
HTTPS is recommended but using HTTP
This warning is normal when running the service without HTTPS.

#6. Test the Service
Test the health endpoint
curl -i http://localhost:9191/hub/actuator/health
Example response:

{
  "status": "DOWN",
  "details": {
    "diskSpace": {
      "status": "UP"
    },
    "consolidator": {
      "status": "DOWN"
    }
  }
}

#Test the hub endpoint

curl -v http://localhost:9191/hub


