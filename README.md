# Simple HTML Application with NGINX Reverse Proxy and SSL Configuration

This guide will walk you through the process of creating and deploying a simple HTML application using Docker on three ports (3001, 3002, 3003), configuring NGINX as a reverse proxy, and securing the application with SSL certificates to make it HTTPS.

---

## Prerequisites

- Docker installed on your machine.
- NGINX installed on your system (for local development or server).
- A basic understanding of Docker, NGINX, and SSL certificates.
- SSL certificate files (i.e., `.crt` and `.key`) for HTTPS setup.

---

## Steps to Complete the Task

### 1. Create the Simple HTML Application

1. **Create a directory** for your project (e.g., `simple-html-app`).
2. **Create an `index.html` file** that contains a simple HTML structure, including text and an image.
3. **Add an image** (e.g., `image.jpg`) to your project directory that will be displayed on the page.

---

### 2. Create a Dockerfile for the Application

1. **Write a Dockerfile** in the project directory that:
   - Uses an official NGINX image.
   - Copies your `index.html` and image into the container.
  
2. **Write a Docker-compose** file for the application
   - Exposes ports `3001`, `3002`, and `3003`.

2. **Build the Docker image** using Docker CLI:
   - Run `docker build -t simple-html-app .` from the project directory.

---

### 3. Deploy the Application to Docker on 3 Different Ports

Now that your Docker image is built, we can deploy the application on **three different ports**: `3001`, `3002`, and `3003`.

#### Step 1: Build, Tag, and Push the Docker Image

To ensure that your Docker image is ready for deployment and can be shared, you need to **build, tag, and push it to a container registry** (e.g., Docker Hub).

1. **Build the Docker image**:
   - Navigate to your project directory (where the `Dockerfile` is located).
   - Run the following command to build the Docker image:
     ```bash
     docker build -t yourusername/simple-html-app .
     ```
     This command builds the image and tags it with the name `yourusername/simple-html-app` (replace `yourusername` with your Docker Hub username).

2. **Log in to Docker Hub** (if you're using Docker Hub):
   - If you haven't logged in to Docker Hub yet, run the following command:
     ```bash
     docker login
     ```
   - Enter your Docker Hub credentials when prompted.

3. **Tag the Docker image**:
   - Tag the image with the appropriate version (e.g., `v1.0.0`):
     ```bash
     docker tag yourusername/simple-html-app yourusername/simple-html-app:v1.0.0
     ```

4. **Push the Docker image to Docker Hub**:
   - Once tagged, push the image to Docker Hub with the following command:
     ```bash
     docker push yourusername/simple-html-app:v1.0.0
     ```
   - This uploads your image to Docker Hub under the specified tag (`yourusername/simple-html-app:v1.0.0`).

#### Step 2: Run the Docker Containers on Different Ports

Now that the image is built and pushed, you can run it on **multiple ports**.

To run your application on multiple ports, follow these steps:

1. **Run the first container on port 3001**:
   - Open a terminal and run the following command:
     ```bash
     docker run -d -p 3001:80 yourusername/simple-html-app:v1.0.0
     ```
     This command tells Docker to:
     - Start the container in detached mode (`-d`).
     - Map the container's internal port `80` (default for NGINX) to the external port `3001` on your machine.

2. **Run the second container on port 3002**:
   - Run the following command:
     ```bash
     docker run -d -p 3002:80 yourusername/simple-html-app:v1.0.0
     ```
     This will start another container and bind it to port `3002` on your machine.

3. **Run the third container on port 3003**:
   - Run the following command:
     ```bash
     docker run -d -p 3003:80 yourusername/simple-html-app:v1.0.0
     ```
     Similarly, this command will start the third container on port `3003`.

After running these commands, your application will be accessible on **three different ports**:
- `http://localhost:3001`
- `http://localhost:3002`
- `http://localhost:3003`

You can verify that each container is running by accessing the URLs in your browser.

---

### 4. Configure NGINX Reverse Proxy

In this section, you will configure NGINX as a reverse proxy to direct traffic to the running Docker containers. The `upstream` directive will help you define a backend group, and the `include mime.types` ensures that the proper MIME types are applied for different file types (e.g., images, HTML, CSS).

1. **Install NGINX** on your local server or virtual machine if it is not already installed.
   - Install the zip file from https://nginx.org/en/download.html.
   - Unzip the file and run the nginx application.

2. **Edit the NGINX configuration file** (`nginx.conf`):
   - This file will define a reverse proxy setup to handle traffic from different paths (`/app1`, `/app2`, `/app3`) and distribute it to different backend Docker containers running on different ports (3001, 3002, 3003).

   **Explanation of Key Configuration Elements**:
   
   - **`upstream nodejs_cluster`**:  
     The `upstream` directive is used to define a **backend cluster** of servers (in your case, Docker containers running on different ports). When you define the `nodejs_cluster`, you are creating a group of backend servers that NGINX can load balance between.  
     This allows NGINX to distribute incoming requests among the backend Docker containers running on ports `3001`, `3002`, and `3003`.  
     By using `upstream nodejs_cluster`, NGINX will proxy requests to one of the available containers based on load balancing (typically in a round-robin fashion).

   - **`include mime.types`**:  
     The `include mime.types` directive ensures that the appropriate **MIME types** are set for the content served by NGINX. This is critical for handling various types of files like images, JavaScript, CSS, and HTML, ensuring that the correct content type (e.g., `image/jpeg`, `text/html`) is sent in the response headers.  
     Without this directive, NGINX may fail to serve files correctly, especially non-HTML files like images and stylesheets.

3. **Test the NGINX configuration**:
   - Test the configuration with `sudo nginx -t`.
   - If no errors appear, restart NGINX with `sudo systemctl restart nginx`.

---

### 5. Verify Application Functionality

1. Open a web browser and go to:
   - `http://localhost/app1`.
   - `http://localhost/app2`.
   - `http://localhost/app3`.

2. Ensure the application is correctly routed via the reverse proxy set up in NGINX.

---

### 6. Secure the Application with SSL

1. **Obtain SSL certificates**:
   - If you don't have SSL certificates, you can use various websites to download free SSL certificates or generate your own certificates.

2. **Configure SSL in NGINX**:
   - Update your `nginx.conf` file to enable SSL and add the SSL certificate (`.crt`) and key (`.key`) files:
     - Set up SSL with the `ssl_certificate` and `ssl_certificate_key` directives.
     - Change the `listen` directive to `443 ssl` for HTTPS.

3. **Restart NGINX** after making the SSL changes:
   - Run `nginx restart` to apply the SSL configuration.

4. **Test the HTTPS connection**:
   - Open a browser and go to `https://localhost/app1`, `https://localhost/app2`, and `https://localhost/app3`.
   - Ensure the application loads over HTTPS with a valid SSL certificate.

---

## Conclusion

By following these steps, you have successfully created a simple HTML application, deployed it on Docker with multiple ports, set up NGINX as a reverse proxy using `upstream` for load balancing, and secured the application with SSL certificates to enable HTTPS.

---

