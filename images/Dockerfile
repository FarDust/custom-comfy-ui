FROM pytorch/pytorch:2.7.1-cuda12.8-cudnn9-runtime

ENV DEBIAN_FRONTEND=noninteractive PIP_PREFER_BINARY=1

RUN apt-get update && apt-get install -y git libgl1-mesa-glx build-essential cmake && apt-get clean

ENV ROOT=/stable-diffusion
RUN pip install uv
ENV PATH="/root/.local/bin:$PATH"
RUN uv tool install comfy-cli
RUN --mount=type=cache,target=/root/.cache/pip \
  git clone https://github.com/comfyanonymous/ComfyUI.git ${ROOT} && \
  cd ${ROOT} && \
  git checkout tags/v0.11.1 && \
  uv pip install --system -r requirements.txt pyav pyhocon sageattention diffusers bitsandbytes

WORKDIR ${ROOT}

# Copy the model paths configuration
COPY config/extra_model_paths.yaml ${ROOT}/extra_model_paths.yaml

ENV NVIDIA_VISIBLE_DEVICES=all PYTHONPATH="${PYTHONPATH}:${PWD}" CLI_ARGS=""

ENTRYPOINT ["python", "-u", "main.py", "--listen", "--port", "7860"]
