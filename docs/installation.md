## Docker Compose (Recommended)

TODO: just reference the existing compose file in the repo
=== "CUDA"

    ```yaml
    # https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html
    services:
      faster-whisper-server:
        image: fedirz/faster-whisper-server:latest-cuda
        name: faster-whisper-server
        restart: unless-stopped
        ports:
          - 8000:8000
        volumes:
          - hugging_face_cache:/root/.cache/huggingface
        deploy:
          resources:
            reservations:
              devices:
                - capabilities: ["gpu"]
    volumes:
      hugging_face_cache:
    ```

=== "CUDA (with CDI feature enabled)"

    ```yaml
    # https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html
    services:
      faster-whisper-server:
        image: fedirz/faster-whisper-server:latest-cuda
        name: faster-whisper-server
        restart: unless-stopped
        ports:
          - 8000:8000
        volumes:
          - hugging_face_cache:/root/.cache/huggingface
        deploy:
          resources:
            reservations:
              # https://docs.docker.com/reference/cli/dockerd/#enable-cdi-devices
              # https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/cdi-support.html
              devices:
                - driver: cdi
                  device_ids:
                  - nvidia.com/gpu=all
    volumes:
      hugging_face_cache:
    ```

=== "CPU"

    ```yaml
    services:
      faster-whisper-server:
        image: fedirz/faster-whisper-server:latest-cpu
        name: faster-whisper-server
        restart: unless-stopped
        ports:
          - 8000:8000
        volumes:
          - hugging_face_cache:/root/.cache/huggingface
    volumes:
      hugging_face_cache:
    ```

## Docker

=== "CUDA"

    ```bash
    docker run --rm --detach --publish 8000:8000 --name faster-whisper-server --volume hugging_face_cache:/root/.cache/huggingface --gpus=all fedirz/faster-whisper-server:latest-cuda
    ```

=== "CUDA (with CDI feature enabled)"

    ```bash
    docker run --rm --detach --publish 8000:8000 --name faster-whisper-server --volume hugging_face_cache:/root/.cache/huggingface --device=nvidia.com/gpu=all fedirz/faster-whisper-server:latest-cuda
    ```

=== "CPU"

    ```bash
    docker run --rm --detach --publish 8000:8000 --name faster-whisper-server --volume hugging_face_cache:/root/.cache/huggingface fedirz/faster-whisper-server:latest-cpu
    ```

## Kubernetes
WARNING: it was written few months ago and may be outdated.
Please refer to this [blog post](https://substratus.ai/blog/deploying-faster-whisper-on-k8s)

## Python (requires Python 3.12+)

```bash
git clone https://github.com/fedirz/faster-whisper-server.git
cd faster-whisper-server
uv venv
sourve .venv/bin/activate
uv sync --all-extras
uvicorn --factory --host 0.0.0.0 faster_whisper_server.main:create_app
```
