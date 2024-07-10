#deploy 


form least change to more changed 

```shell
# Use the official python image as a parent image
FROM python:3.11-slim

# Set the working directory in the container
WORKDIR /app

# Copy requirements.txt in same directory as the Dockerfile on the host to /app/requirements.txt inside the container
COPY requirements.txt .

# Install any needed packages specified in requirements.txt inside the container
RUN pip install --no-cache -r requirements.txt

# --no-cache no save


# Copy files from the same directory as the Dockerfile on the host to the /app directory inside the container (directory /app/models/ and file /app/predict.py)
COPY models models
COPY predict.py .

ENTRYPOINT ["python3", "predict.py"]

```


```shell

docker run --entrypoint /bin/bash --rm -it language-detection-tool:0.1

```

--entrypoint /bin/bash : enter the container 

-it : interaction 



# Volumes




RESTAPI

SWAGGEWR

FASTAPI