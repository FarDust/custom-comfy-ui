# Custom ComfyUI Docker Image

Docker Swarm-optimized ComfyUI image with automated semantic releases.

## 🐳 Image

```
us-central1-docker.pkg.dev/landing-fardust/public-docker/default:latest
```

## 🚀 Quick Start

```bash
docker run -d \
  --name comfyui \
  -p 7860:7860 \
  -v /host/path/to/data:/data \
  -v /host/path/to/output:/output \
  us-central1-docker.pkg.dev/landing-fardust/public-docker/default:latest
```

## 📁 Directory Structure

```
/data/
├── .cache/                    # Model cache (symlinked from /root/.cache)
├── config/comfy/
│   ├── custom_nodes/          # Custom nodes
│   ├── input/                 # Input files
│   └── startup.sh             # Optional startup script
└── models/                    # Model files

/output/
└── comfy/                     # Generated images
```

## 🐋 Docker Swarm

```yaml
version: '3.8'
services:
  comfyui:
    image: us-central1-docker.pkg.dev/landing-fardust/public-docker/default:latest
    ports:
      - "7860:7860"
    volumes:
      - comfyui_data:/data
      - comfyui_output:/output
      - comfyui_cache:/root/.cache
    environment:
      - NVIDIA_VISIBLE_DEVICES=all
      - CLI_ARGS=--listen --port 7860

volumes:
  comfyui_data:
    driver: local
  comfyui_output:
    driver: local
  comfyui_cache:
    driver: local
```

## 🔧 Configuration

Override model paths using secrets:

```bash
docker secret create comfyui_config ./config/extra_model_paths.yaml
```

```yaml
secrets:
  - source: comfyui_config
    target: /stable-diffusion/extra_model_paths.yaml
    mode: 0444
```

## 🎛️ CLI Arguments

```yaml
environment:
  - CLI_ARGS=--listen --port 7860 --enable-cors-header --cpu --lowvram
```

## 📊 Requirements

- **Docker:** NVIDIA Container Runtime as default

## 🔄 Versioning

```bash
# Latest
docker pull us-central1-docker.pkg.dev/landing-fardust/public-docker/default:latest

# Specific version
docker pull us-central1-docker.pkg.dev/landing-fardust/public-docker/default:1.2.3
```

## 🐛 Troubleshooting

```bash
# Check logs
docker service logs comfyui_comfyui

# Verify GPU
nvidia-smi

# Check volumes
docker volume ls
``` 