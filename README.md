# mongodb_mongoexpress_k8s_project
Project Overview

![Screenshot (1663)](https://github.com/user-attachments/assets/cefb88ad-4030-4de3-a9e4-f4cbea6c71d8)
![Screenshot (1662)](https://github.com/user-attachments/assets/ae13deff-6859-4818-9d91-2c5f87b8fbd4)
![Screenshot (1661)](https://github.com/user-attachments/assets/428eb638-1aa8-47e7-a6c1-34833929fc78)
![Screenshot (1659)](https://github.com/user-attachments/assets/21136716-4d3c-4b5e-b067-dd84c60b5ef1)
![Screenshot (1657)](https://github.com/user-attachments/assets/0a7fe9ff-482f-4048-a2fa-5e8320589caf)

This project demonstrates how to deploy MongoDB and Mongo Express on Kubernetes using persistent storage (via AWS EBS), secrets for authentication, and a ConfigMap to configure the Mongo Express application.

We use the following components:

    Kubernetes Secret for securely storing MongoDB credentials.
    StorageClass for configuring persistent storage using AWS EBS.
    PersistentVolumeClaim for requesting persistent storage for MongoDB.
    Deployment for deploying MongoDB and Mongo Express.
    Service for exposing the MongoDB and Mongo Express applications.
    ConfigMap for storing MongoDB connection information used by Mongo Express.

Components Breakdown

    MongoDB Secret (mongodb-secret):
        This Kubernetes Secret stores MongoDB credentials, such as the username and password. The credentials are base64-encoded.
        Example base64 encoding:
            username = bW9uZ29kYi11c2VybmFtZQ== (this decodes to "mongodb-username")
            password = bW9uZ29kYi1wYXNzd29yZA== (this decodes to "mongodb-password")

    StorageClass (mongodb-sc):
        The StorageClass defines how persistent volumes will be provisioned using AWS EBS (Elastic Block Store) with the CSI driver (ebs.csi.aws.com). It uses WaitForFirstConsumer to bind the volume only when a consumer pod is scheduled.

    PersistentVolumeClaim (mongodb-pvc):
        The PersistentVolumeClaim requests 5Gi of storage using the mongodb-sc storage class. This claim allows the MongoDB deployment to store data persistently.

    MongoDB Deployment (mongodb-deploy):
        This deployment runs MongoDB in a container, specifying the image (mongodb). It mounts the persistent volume and uses the credentials stored in the mongodb-secret.

    MongoDB Service (mongodb-svc):
        The Service exposes the MongoDB container on port 27017 so that other pods in the Kubernetes cluster can access the MongoDB instance.

    ConfigMap (mongodb-cm):
        This ConfigMap stores the URL of the MongoDB service (mongodb-svc). The mongo-express app will use this to connect to MongoDB.

    Mongo Express Deployment (mongoexpress-deploy):
        This deployment runs the mongo-express container, a web-based interface for MongoDB. It uses the credentials from the mongodb-secret and the database URL from the mongodb-cm to connect to MongoDB.

    Mongo Express Service (mongoexpress-svc):
        This Service exposes mongo-express on port 8081. It's of type LoadBalancer to make it accessible externally, with a node port of 31000.

Step-by-Step Guide to Deploy

Follow the steps below to deploy MongoDB and Mongo Express on Kubernetes:
Prerequisites:

    A Kubernetes cluster (can be hosted on a cloud provider like AWS, GCP, or Azure).
    AWS CLI and IAM permissions to manage EBS volumes (if using AWS as the cloud provider).
    kubectl configured to interact with your Kubernetes cluster.

1. Create the Secret:

First, create the mongodb-secret to securely store MongoDB credentials (mongodb-secret.yaml)

Replace <base64-encoded-username> and <base64-encoded-password> with your own encoded values.
2. Define the StorageClass:

Define the StorageClass to provision AWS EBS volumes (mongodb-sc.yaml)

3. Create the PersistentVolumeClaim:

Create the PersistentVolumeClaim to request 5Gi of storage for MongoDB (mongodb-pvc.yaml)

4. Deploy MongoDB:

Now, create the Deployment for MongoDB using the secret for credentials (mongodb-deploy.yaml)

5. Expose MongoDB Service:

Create a Service to expose MongoDB on port 27017 (mongodb-svc.yaml)

6. Configure Mongo Express:

Create a ConfigMap with the MongoDB service URL (mongoexpress-cm.yaml)

7. Deploy Mongo Express:

Now, deploy the Mongo Express web interface (mongoexpress-deploy.yaml)


8. Expose Mongo Express Service:

Finally, expose Mongo Express via a LoadBalancer service (mongoexpress-svc.yaml)


