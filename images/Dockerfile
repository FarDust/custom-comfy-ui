FROM pytorch/pytorch:2.7.1-cuda12.8-cudnn9-runtime

ENV DEBIAN_FRONTEND=noninteractive \
    PIP_PREFER_BINARY=1

RUN apt-get update && apt-get install -y \
    git \
    libgl1-mesa-glx \
    libglib2.0-0 \
    build-essential \
    cmake \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# --- Fix para "cannot find -lcuda" sin scripts de entrypoint ---
# Creamos un "linker script" llamado libcuda.so que apunta a libcuda.so.1
# (libcuda.so.1 lo aporta el driver del host vía NVIDIA Container Toolkit / WSL).
RUN mkdir -p /usr/local/lib && \
    printf "/* GNU ld script */\nGROUP ( libcuda.so.1 )\n" > /usr/local/lib/libcuda.so

# Asegura que gcc/ld consideren /usr/local/lib al linkear
ENV LIBRARY_PATH="/usr/local/lib:${LIBRARY_PATH}"

ENV ROOT=/stable-diffusion

RUN pip install --no-cache-dir uv
ENV PATH="/root/.local/bin:$PATH"
RUN uv tool install comfy-cli

RUN --mount=type=cache,target=/root/.cache/pip \
    git clone https://github.com/comfyanonymous/ComfyUI.git ${ROOT} && \
    cd ${ROOT} && \
    git checkout tags/v0.11.1 && \
    uv pip install --system \
      -r requirements.txt \
      av pyhocon sageattention diffusers bitsandbytes \
      omegaconf opencv-python-headless \
      plyfile ftfy scikit-image


WORKDIR ${ROOT}

COPY config/extra_model_paths.yaml ${ROOT}/extra_model_paths.yaml

ENV NVIDIA_VISIBLE_DEVICES=all \
    PYTHONPATH="${PYTHONPATH}:${ROOT}" \
    CLI_ARGS=""

ENTRYPOINT ["python", "-u", "main.py", "--listen", "--port", "7860"]
