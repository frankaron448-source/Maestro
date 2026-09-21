FROM python:3.10.9-slim

ENV DEBIAN_FRONTEND=noninteractive
ENV HF_HUB_ENABLE_HF_TRANSFER=1
ENV PYTHONUNBUFFERED=1
ENV PIP_NO_CACHE_DIR=1

RUN apt-get update && apt-get install -y \
    git ffmpeg libsndfile1 libgl1 libglib2.0-0 build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Torch primero con index CUDA (separado de requirements para evitar extra-index-url)
RUN pip install --upgrade pip && \
    pip install torch==2.6.0 torchvision==0.21.0 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu124

COPY requirements.txt .
RUN pip install -r requirements.txt

# xformers y sageattention solo si hay GPU, opcional, no falla si no compila
RUN pip install xformers==0.0.28.post3 || echo "xformers skip on CPU" && \
    pip install sageattention==1.0.6 || echo "sageattention skip"

COPY . .

EXPOSE 7860

CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "7860", "--workers", "1"]
