
## Prerequisite

Ensure that you have Docker installed and running on your system. Verify your installation by running `docker --version` in your terminal.



## Method 1: Quick Interactive Setup

If you need a temporary environment to test code or run a quick script, you can pull the official Anaconda image and run it interactively.

1. **Pull the official Anaconda image:**
Download the image from Docker Hub.

```bash
docker pull continuumio/anaconda3

```


2. **Run the container:**
Start a container and open an interactive bash shell inside it.

```bash
docker run -it continuumio/anaconda3 /bin/bash

```


3. **Verify the installation:**
Once inside the container, verify that Python and Conda are working. You can now use standard `conda` commands to install packages.

```bash
python --version
conda --version

```




## Method 2: Persistent Setup using a Dockerfile (Recommended)

For actual development or deployment, you should create a `Dockerfile`. This automates your environment setup so it builds exactly the same way every time.

1. **Define your dependencies:**
Create an `environment.yml` file in your project directory to define your conda environment.

```yaml
name: myenv
channels:
  - defaults
  - conda-forge
dependencies:
  - python=3.10
  - pandas
  - numpy
  - scikit-learn

```


2. **Create the Dockerfile:**
In the same directory, create a file named `Dockerfile` (with no file extension) and add the following configuration:

```dockerfile
# Start with the official Anaconda base image
FROM continuumio/anaconda3

# Set the working directory inside the container
WORKDIR /app

# Copy the environment file into the container
COPY environment.yml .

# Create the Conda environment
RUN conda env create -f environment.yml

# Ensure the environment is activated automatically when the container starts
SHELL ["conda", "run", "-n", "myenv", "/bin/bash", "-c"]

# Copy your application code into the container
COPY . .

# Set the default command to execute your main script
ENTRYPOINT ["conda", "run", "--no-capture-output", "-n", "myenv", "python", "main.py"]

```


3. **Build and run your image:**
Open your terminal in the project directory, build the image, and then run it.

```bash
# Build the image and name it "my-anaconda-app"
docker build -t my-anaconda-app .

# Run the container
docker run my-anaconda-app

```




## Bonus: Running Jupyter Notebook

If you want to use Jupyter Notebook via the Anaconda container, you need to map a port from the container to your local machine.

Run this single command in your terminal:

```bash
docker run -i -t -p 8888:8888 continuumio/anaconda3 /bin/bash -c "\
    conda install jupyter -y --quiet && \
    mkdir /opt/notebooks && \
    jupyter notebook --notebook-dir=/opt/notebooks --ip='*' --port=8888 --no-browser --allow-root"

```

Once the server starts, it will output a URL (e.g., `[http://127.0.0.1:8888/?token=](http://127.0.0.1:8888/?token=)...`). Copy and paste that URL into your local web browser to access the Jupyter interface.


