# Lab 5: Containerizing with Docker

In this lab, you'll learn how to containerize a Flask application, train and deploy a machine learning model using Docker and Docker Compose. By the end of the lab, you'll have a Flask API running in a Docker container that can serve predictions using a machine learning model trained on the Iris dataset. You will also learn how to debug and check logs in a Docker container.

Clone the code from [this](https://github.com/kp10-x/mlip-docker-lab-f25) repository.

## Deliverables

 - [ ] Create a docker for the training script by writing a `train.py` script inside a Docker image that produces a model file. Use the template Dockefile and train script provided. Explain why Docker is useful in this scenario.
 - [ ] Similarly, create a docker for the inference service. Update the provided Flask App for inference and containerize it, ensuring it can load the trained model and serve predictions on port 8080.
 - [ ] Finally create a `docker-compose.yml` file to set up training and inference, sharing the model on a configured volume, and exposing the inference service on port 8080. Verify with logs that training completes and inference serves requests. Explain how storage is handled between multiple container services and how this builds the overall ML pipeline.

## Prerequisite: Setup Docker

- Install Docker on your system
- Verify Docker installation

Follow the instructions for your operating system to install Docker. Then test your installation with:

```bash
docker run hello-world
```

## Deliverable 1 - Containerize training the Machine Learning Model

Create a docker container for the training code. Then launched, the container should run the training script and store the model file to a shared volume. You can use the partially completed Dockerfile and code in directory train

Complete the training dockerfile
- Copy `requirements.txt` and install dependencies
- Copy `train.py` to the working directory (update the `train.py` to save model)
- Store model in a shared volume
- Set the command to run `train.py`

### Build, run, and verify

Build the training image from the repo root:

```bash
docker build -t mlip-training -f docker/training/Dockerfile .
```

Run training and write the model to a named volume mounted at `/app/models`:

```bash
docker run --rm -v model_storage:/app/models mlip-training
```

## Deliverable 2 - Containerize the Flask App for inference

Create a docker container that serves the model, with the model stored within the container or mounted through a shared drive. You can use the provided partial implementation of `infer/server.py` but will need to create your own Docker file from scratch.

### Step 1: Implement the predict() Function

In the `server.py` file, there is a function called predict() where you need to:
- Load the trained machine learning model.
- Run inference using an input sent through a `POST` request.
- Return the prediction as the response.


### Step 2: Complete the inference dockerfile
- Create the dockerfile in the inference directory
- Set application directory
- Copy `requirements.txt`, install dependencies and mount the shared folder
- Setup server on port 8080 (or any other port)

### Build, run, and test

Build the inference image from the repo root:

```bash
docker build -t mlip-inference -f docker/inference/Dockerfile .
```

Run inference, mounting the same named volume and exposing port 8080:

```bash
docker run --rm -p 8080:8080 -v model_storage:/app/models mlip-inference
```

Test the endpoint:

```bash
curl -X POST http://localhost:8080/predict -H 'Content-Type: application/json' -d '{"input":[6.3,3.3,6,2.5]}'
```

## Deliverable 3 - Completing the Docker Compose

Finally create a `docker-compose.yml` file to set up training and inference, sharing the model on a configured volume, and exposing the inference service on port 8080.

### Docker Compose Overview
Docker Compose is a tool that allows you to define and manage multi-container Docker applications. It uses a `docker-compose.yml` file to configure the application's services, networks, and volumes. In this lab, you'll use Docker Compose to set up two services:

- Training Service: This service will train a machine learning model using the Iris dataset.
- Inference Service: Once the model is trained, the inference service will load the trained model and serve predictions via a Flask API.
  
Fill in the TODO sections:
- Set the training service and the inference service.
- Use a shared volume to store the trained model between the services.
- Expose port 8080 (or any other port) for the Flask app in the inference service.
- Ensure the inference service starts only after the training service completes (depends_on).
- Declare the model_storage volume at the end for both services to access the trained model.

### Running the Entire Setup

- Build and run services with Docker Compose
- Verify both services are running correctly and check logs (try with bad testcase as well)

Use the following command to start your services:

```bash
docker-compose up --build
```

## Testing the Prediction Endpoint
### Calling a POST Request
For this assignment, run the following CURL command to test your flask setup.

```
curl --location --request POST 'localhost:8080/predict' \
--header 'Content-Type: application/json' \
--data '{
    "input": [6.3, 3.3, 6 , 2.5]
}'
```
Alternatively, you can test this on Postman as well - ensure that your body is JSON with the following data
```json
{
    "input": [6.3, 3.3, 6 , 2.5]
}
```

Try a similar bad request as well!

Check logs: https://docs.docker.com/reference/cli/docker/container/logs/
Run the command to follow and get logs on a running container

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

