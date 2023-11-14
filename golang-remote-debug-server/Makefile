all: test-run

IMAGE_NAME = remote-debug-env
IMAGE_VER = t1
CONTAINER_NAME = remote-debug-server
MAPPING_SSH_PORT = 10012
DOCKERFILE_PATH = $(PWD)/Dockerfile
DOCKER_BUILD_DIR = $(PWD)
SLEEP_SECS=2
DAEMONAPP_PATH = $(PWD)/daemon-app
BUILD_DAEMONAPP_OUTPUT_PATH/debug_daemon = $(PWD)

test-run:
         echo "test run"

docker-build:
             sudo docker build -f $(DOCKERFILE_PATH) -t $(IMAGE_NAME):$(IMAGE_VER) $(DOCKER_BUILD_DIR)

docker-ps:
          sudo docker ps -f name=$(CONTAINER_NAME)

docker-run:
           sudo docker run -d -p $(MAPPING_SSH_PORT):22 --privileged=true --name $(CONTAINER_NAME) $(IMAGE_NAME):$(IMAGE_VER)
           sleep $(SLEEP_SECS)
           sudo docker ps -f name=$(CONTAINER_NAME)

docker-start:
             sudo docker start $(CONTAINER_NAME)
             sleep $(SLEEP_SECS)
             sudo docker ps -f name=$(CONTAINER_NAME)

docker-stop:
            sudo docker stop $(CONTAINER_NAME)

docker-rm:
          make docker-stop
          sudo docker rm $(CONTAINER_NAME)

build-daemon-app:
                 go version
                 go build -o $(BUILD_DAEMONAPP_OUTPUT_PATH) $(DAEMONAPP_PATH)/main.go



.PHONY: docker-build docker-ps docker-run docker-start docker-stop docker-rm