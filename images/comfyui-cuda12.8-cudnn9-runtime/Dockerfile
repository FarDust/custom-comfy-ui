FROM pytorch/pytorch:2.7.1-cuda12.8-cudnn9-runtime

ENV DEBIAN_FRONTEND=noninteractive PIP_PREFER_BINARY=1

RUN apt-get update && apt-get install -y git && apt-get clean

ENV ROOT=/stable-diffusion
RUN pip install uv
ENV PATH="/root/.local/bin:$PATH"
RUN uv pip install --system comfy-cli
RUN --mount=type=cache,target=/root/.cache/pip \
  git clone https://github.com/comfyanonymous/ComfyUI.git ${ROOT} && \
  cd ${ROOT} && \
  git checkout master && \
  pip install -r requirements.txt

RUN comfy node registry-install comfyui-model-manager

WORKDIR ${ROOT}

# Copy the model paths configuration
COPY config/extra_model_paths.yaml ${ROOT}/extra_model_paths.yaml

ENV NVIDIA_VISIBLE_DEVICES=all PYTHONPATH="${PYTHONPATH}:${PWD}" CLI_ARGS=""

ENTRYPOINT ["python", "-u", "main.py", "--listen", "--port", "7860"]
