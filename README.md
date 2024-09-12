# MLOps Engineer Assignment (G42)

# Theoretical Component

1. Key Considerations for Setting Up a CI/CD Pipeline for a Machine Learning Project
Automated Testing: Ensure robust unit, integration, and regression testing for data preprocessing, model training, and inference.
Data Versioning: Implement version control for datasets using tools like DVC (Data Version Control).
Model Versioning: Track different versions of models using model registries like MLflow.
Infrastructure as Code: Use tools like Terraform for reproducible infrastructure setups.
Environment Management: Maintain consistent environments using containerization (Docker) and environment specification files.
Continuous Monitoring: Set up monitoring for model performance and data drift detection.

2. Benefits of Containerization in Machine Learning Deployment
Consistency: Containers ensure that the application runs the same in development, testing, and production environments.
Portability: Containers can be easily moved across different environments and cloud providers.
Scalability: Containers can be scaled up or down quickly, allowing for efficient resource management.
Isolation: Each container runs in isolation, reducing conflicts between dependencies and improving security.
Reproducibility: Using container images guarantees that the deployed application is identical to what was tested.

3. Scenario for Using Kubernetes in Machine Learning Application Lifecycle Management
Consider a machine learning application that involves multiple stages: data preprocessing, model training, and inference serving. Kubernetes can manage the entire lifecycle:

Data Preprocessing: Use Kubernetes jobs to process large datasets in parallel.
Model Training: Schedule distributed training jobs using Kubernetes, leveraging GPU nodes for accelerated training.
Inference Serving: Deploy the trained model using Kubernetes deployments and services to ensure high availability and load balancing.
Auto-scaling: Automatically scale inference services based on traffic using Kubernetes Horizontal Pod Autoscaler.
Resource Management: Efficiently allocate resources for different stages, ensuring optimal utilization.

4. Open-Source Tools for Monitoring and Logging in a Machine Learning Context
Prometheus: A powerful monitoring system that collects metrics from various services and applications, providing real-time insights.
Grafana: A visualization tool that works with Prometheus to create interactive dashboards for monitoring metrics.
ELK Stack (Elasticsearch, Logstash, Kibana): Used for centralized logging, enabling the collection, processing, and visualization of log data.
Fluentd: A log collector that unifies data collection and consumption for better log management.
MLflow: A platform for managing the ML lifecycle, including experimentation, reproducibility, and deployment.

#  Steps to run the project

1. Github `Ci-CD` working fine 
2. Kubernets API working with `FastAPI`

# Steps:
1. `minikube start` + `minikube addons enable ingress` (for routing)
1. Create and apply `secrets.yaml`
2. Create and apply `config.yaml` 
3. Create your `FastApi` app inside `main.py` referencing the db and collection above
4. Create a `Docker` image of your app and push to Hub
5. Lastly create and apply `fastapi` deployment and service files
6. Now you can hit `FastAPI` app at `http://192.168.52.5:9000` as it is running at Port 9000 (defined in `fastapi-service.yaml` file)
7. Edit `/etc/hosts` to map `my-deployed-app.com` to where your `FastAPI` ia running by using:  `echo '192.168.52.5 my-deployed-app.com' | sudo tee -a /etc/` or `echo "$(minikube ip) service.express.mongo" | sudo tee -a /etc/hosts` hosts (For Local system only)
8. Apply `ingress.yaml` to reroute the traffic


# Some Helpful Info & Tips:
1. [Tag Docker images before pushing](https://stackoverflow.com/questions/41984399/denied-requested-access-to-the-resource-is-denied-docker)
2. `kubectl get deployment fastapi-app-deployment -o yaml > fastapi-etcd-cluster-status.yaml` Gives you the status of cluster for the Deployment (which has replicas or pods) named `fastapi-app-deployment` in a file named `fastapi-etcd-cluster-status.yaml` where you can cross check your `deployment.yaml` and `service.yaml` to know how the clusters are managed according to the specifications (`spec` field in files). This info is stored in `etcd` which supervises all the failures and auto creation of pods cross checking with this file
3. Create and apply the `ConfigMap` and `Secrets` files first and then apply the Service and Deployment else it'll result in the error if any of the service uses those secrets and configurations
4. If there is a `Service` running inside cluster, it is allocated **INTERNAL IP ADDRESS** so the system knows that at which particular address the service is running. So in case we want to access the data from that service, we use **THAT** internal address to communicate. So in other terms, How and where to connect with an app is defined in it's `service.yaml` file and whenever we want to know the address like `MongoDB` url, we refer to that service instead of static url.
