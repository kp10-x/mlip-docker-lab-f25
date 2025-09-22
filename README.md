# Lab 5: Containerizing with Docker


## Step 0: Setup Docker

- Install Docker on your system
- Verify Docker installation

Follow the instructions for your operating system to install Docker. Then test your installation with:

```bash
docker run hello-world
```

## Step 1: Containerize training the Machine Learning Model

Create a docker container for the training code. Then launched, the container should run the training script and store the model file to a shared volume. You can use the partially completed Dockerfile and code in directory train

The dockerfile is already nearly complete. Ensure it:
- Copy `requirements.txt` and install dependencies
- Copy `train.py` to the working directory (update the `train.py` to save model)
- Store model in a shared volume
- Set the command to run `train.py`

Build and run the container.

If you want to keep the model file, you need to mount a local folder, e.g., `docker run --rm -v model_storage:/app/models mlip-training`

## Step 2: Containerize the Flask App for inference

Create a docker container that serves the model, with the model stored within the container or mounted through a shared drive. You can use the provided partial implementation of `infer/server.py` but will need to create your own Dockerfile from scratch.

Complete the server implementation by loading the trained machine learning model, running inference, and returning predictions as responses.

Build and run the container. When running it, you will need to expose a port, e.g., `docker run --rm -p 8080:8080 -v model_storage:/app/models mlip-inference`


Test that it works by sending a request to the running container, e.g., use Postman or `curl -X POST http://localhost:8080/predict -H 'Content-Type: application/json' -d '{"input":[6.3,3.3,6,2.5]}'`

Try to send a bad request too, to test error handling.

## Step 3: Docker Compose

Finally create a `docker-compose.yml` file to set up training and inference, sharing the model on a configured volume, and exposing the inference service on port 8080.

Docker Compose is a tool that allows you to define and manage multi-container Docker applications, without long command line parameters. It uses a `docker-compose.yml` file to configure the application's services, networks, and volumes. 

We provide a template for a `docker-compose.yml` file for the two containers. Set it up so that both services start, share the model file through a shared volume, and exposes a port for inference. You should be able to run the entire thing with `docker compose up --build`.


## Step 4: Logs

Check logs of the running containers, see https://docs.docker.com/reference/cli/docker/container/logs/

You should be able to see results from successful and failed requests.


## Additional resources 
1. [Docker For Beginners](https://docker-curriculum.com/)
2. [Build and Deploy Flask Applications with Docker](https://www.digitalocean.com/community/tutorials/how-to-build-and-deploy-a-flask-application-using-docker-on-ubuntu-20-04)
3. [Models on Docker](https://towardsdatascience.com/build-and-run-a-docker-container-for-your-machine-learning-model-60209c2d7a7f)

## Troubleshooting

If you encounter issues:
- Check Docker daemon status
- Verify port availability
- Review service logs with `docker-compose logs`
- Ensure the training service completes before the inference service starts
- Try running docker on the team's VM if running into local issues

