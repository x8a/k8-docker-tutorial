# Working with Docker images in Kubernetes

We run our applications in Docker containers in Kubernetes clusters. In this tutorial, we'll go over how to create a Docker image for a sample application and deploy it to [Docker Hub](https://hub.docker.com/). To do so, you need to:

1. Set up your project
2. Set up your application for Docker
3. Build your Docker image
4. Deploy your Docker image

## Prerequisites

- Install [Docker in your environment](https://docs.docker.com/get-started/get-docker/).

- Have a working repository on [Docker Hub](https://hub.docker.com/).

## Docker installation and project setup

Create the folders for the Docker project by running:

```cmd
mkdir quickstart_docker
mkdir quickstart_docker/application
mkdir quickstart_docker/docker
mkdir quickstart_docker/docker/application
```

Your project will have the following structure:

```
quickstart_docker       # Main application folder
├── application/        # Folder for our application code
├── docker/             # Main folder for docker
    ├── application/    # Folder for the application code for Docker
```

## Application setup

After your project is ready, you need to set up your application so you can create a Docker image out of it.

1. Inside the `quickstart_docker/application` folder, create an `application.py` file with the following content:

    ```py
    import http.server
    import socketserver

    PORT = 8000

    Handler = http.server.SimpleHTTPRequestHandler

    httpd = socketserver.TCPServer(("", PORT), Handler)

    print("serving at port", PORT)
    httpd.serve_forever()
    ```

2. Inside the `quickstart_docker/application` folder, create a file called `Dockerfile` with the following content:

    ```dockerfile
    # Use base image from the registry
    FROM python:3.5

    # Set the working directory to /app
    WORKDIR /app

    # Copy the 'application' directory contents into the container at /app
    COPY ./application /app

    # Make port 8000 available to the world outside this container
    EXPOSE 8000

    # Execute 'python /app/application.py' when container launches
    CMD ["python", "/app/application.py"]
    ```

    Your project should look similar to:

     ```
    quickstart_docker           # Main application folder
    ├── application/            # Folder for our application code
        ├── application.py       # The code for our application 
        ├── Dockerfile           # Docker setup file
    ├── docker/                 # Main folder for docker
        ├── application/         # Folder for the application code for Docker
    ```

> 💡 To learn more about what you can do with the `Dockerfile`, see [Docker's documentation](https://docs.docker.com/reference/dockerfile/).

## Build your Docker image

Once the application is setup, you can build a Docker image for it.

1. In the root folder of your application, run:

    ```cmd
    docker build . -f-docker/application/Dockerfile -t exampleapp
    ```
    This command consists of:
    - `.`: This argument sets the application context.
    - `-f docker/application/Dockerfile`: This argument references your `Dockerfile`.
    - `-t exampleapp`: This argument tags the image, so it's easier to identify.

If you want to see a list of all your Docker images, run:

```cmd
docker images
```

You'll get a list similar to the following:

```
REPOSITORY             TAG             IMAGE ID            CREATED             SIZE
exampleapp             latest          83ioe0edc28a        2 seconds ago       154MB
python                 3.6             05stv8636w3f        6 weeks ago         154MB

```

## Deploy your Docker image

Now that the Docker image is ready, upload it to [Docker Hub](https://hub.docker.com/) so everyone in your team has access to it.

1. In the root folder of your application, run:

    ```cmd
    docker push YOUR-DOCKER-ID/exampleapp
    ```
