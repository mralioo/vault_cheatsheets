#LLM #RAG #deploy 



# Large-Scale Production Deployment of RAG Pipelines

![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale.png)
The RAG Pipeline components are encapsulated within three Helm charts, distinguished by different colors:
- The NIM LLM Helm chart (light blue).
- The Retrieval Embedding NIM Helm chart (light red).
- The frontend, query router, and pgvector in a single Helm chart (light green).

Various storage volume paths are mounted from host to different pods within Kubernetes. To enable GPU utilization in Kubernetes, essential NVIDIA software components (including the driver, container runtime, device plugin, and monitoring) are deployed using the GPU Operator Helm chart. Prometheus and Grafana are employed for monitoring both the Kubernetes cluster and the RAG application.

Some pods use host-mounted volumes via Persistent Volumes and Persistent Volume Claims for data storage and AI models (LLM and embedder). Pods housing the LLM and embedder AI models are autoscaled using Horizontal Pod Autoscaler (HPA) based on custom metrics and exposed via a Kubernetes NodePort service. 

In this lab, Minikube is used for development purposes. In production, various on-prem or cloud-based solutions can be used such as [kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/), AWS's [EKS](https://aws.amazon.com/eks/), Azure's [AKS](https://azure.microsoft.com/en-us/products/kubernetes-service), or GCP's [GKE](https://cloud.google.com/kubernetes-engine/).


## NVIDIA Inference Microservices or NIM
![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-1.png)
NVIDIA Inference Microservices are containerized solutions designed to accelerate Generative AI deployment. NIMs are the key services deployed in the RAG application. The diagram below illustrates the high-level architecture of NIM and its components. Find out more at [NIM documentation](https://developer.nvidia.com/docs/nemo-microservices/inference/overview.html).

Let's have a look at the critical components of NIM:

- [NVIDIA Triton™ Inference Server](https://developer.nvidia.com/triton-inference-server) is an open-source software that standardizes AI model deployment and execution across every workload. Triton™ Inference Server is part of the NVIDIA AI platform and available with NVIDIA AI Enterprise.

- [TensorRT-LLM](https://nvidia.github.io/TensorRT-LLM/) is a toolkit to assemble optimized solutions to run Large Language Model (LLM) inference. It offers a Python API to define models and compile efficient TensorRT engines for NVIDIA GPUs. It also contains Python and C++ components to build runtimes to execute those engines as well as backends for the Triton Inference Server to easily create web-based services for LLMs. TensorRT-LLM supports multi-GPU and multi-node configurations (through MPI).

- [NIM's API endpoints](https://developer.nvidia.com/docs/nemo-microservices/inference/openai-api.html) allows users to invoke the NIM services within their application code. NIM exposes the hosted LLMs via NemoLLM API and OpenAI-compatible API for `/completions` and `/chat/completions/` endpoints. NemoLLM API provides additionally a `/customizations/completions` endpoint for a variety of LLM customization techniques such as P-tuning and Lora. By default, NIM exposes OpenAI API at port 8005 and NemoLLM api at port 8006.
##  NVIDIA GPU Cloud (NGC)

The NGC™ catalog is a hub of GPU-optimized AI, high-performance computing (HPC), and data analytics software that simplifies and accelerates end-to-end workflows. It includes enterprise-grade containers, pretrained AI models, and industry-specific SDKs.

> [!NOTE]
> **For training purpose, we are providing a pre-populated resource in this lab. If you want to reproduce it locally in your environment, you will need minimal changes to substitute some of the resources with the NGC-hosted one's. To do so, you'll need an NGC account and API key and for some assets NVAIE license.**

# Production Environment



| ![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-2.png) | ![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-4.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-5.png) | Available Interconnect Topology<br><br>RAG (Retrieval-Augmented Generation) applications are complex, involving several calls to various AI models, such as large language models (LLMs), that are memory and computationally intensive, requiring distributed deployment on multiple GPUs. As a consequence, the network interconnect topology is important in order to ensure efficient processing.<br><br>To check the available interconnect topology, we can use the `nvidia-smi topo --matrix` command.<br><br>```<br>        GPU0    GPU1    GPU2    GPU3    CPU Affinity    NUMA Affinity<br>GPU0     X      NV12    SYS     SYS     0-23            N/A<br>GPU1    NV12     X      SYS     SYS     24-47           N/A<br>GPU2    SYS     SYS      X      NV12    48-71           N/A<br>GPU3    SYS     SYS     NV12     X      72-95           N/A<br><br>Where X = Self and NV# = Connection traversing a bonded set of # NVLinks<br>    SYS = Connection traversing PCIe as well as the SMP interconnect between NUMA nodes (e.g., QPI/UPI)<br>```<br><br>[NVIDIA NVLink technology](https://www.nvidia.com/en-us/data-center/nvlink/) is a direct GPU-to-GPU interconnect providing high bandwidth and improving scalability for multi-GPU systems.<br> |

## Kubernetes Cluster Basics


The [NVIDIA GPU Operator](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/index.html) uses the operator framework within Kubernetes to automate the management of all NVIDIA software components needed to provision a GPU. These components include the NVIDIA drivers (to enable CUDA), the Kubernetes device plugin for GPUs, the NVIDIA Container Toolkit, automatic node labeling using GFD, DCGM based monitoring and others.


The `kubectl get` commands provide information about nodes, pods and services in your Kubernetes cluster. Learn more here about the [kubectl command line tool](https://kubernetes.io/docs/reference/kubectl/overview/)

In a Kubernetes cluster, there are 2 types of resources: 

- Nodes running the applications 
- control-plane coordinating the cluster. 

Let's have a look at the lab Kubernetes cluster by checking the status of nodes and pods.

`kubectl get nodes` fetches the status of all nodes in the Kubernetes cluster from the control plane node. 

In this lab, you should see the control plane node called `minikube` with the status `Ready`. 


### GPU application

> [!important] 
> - Interacted with K8s using `kubectl`
> 
> The `kubectl get` commands provide information about nodes, pods and services in your Kubernetes cluster. Learn more here about the [kubectl command line tool](https://kubernetes.io/docs/reference/kubectl/overview/)



We will use the [cuda-vectoradd](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/getting-started.html#cuda-vectoradd) toy application which randomly generates two very large vectors and adds them.

Let's print out the YAML configuration file needed to deploy this application:

``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-operator-test
spec:
  restartPolicy: OnFailure
  containers:
  - name: cuda-vector-add
    image: "nvidia/samples:vectoradd-cuda11.6.0"
    resources:
      limits:
         nvidia.com/gpu: 1

```

To deploy the application, execute the `kubectl apply` command, specifying the YAML configuration file with the `-f` file option.
``` sh
# Deploy the application (run the pod)
!kubectl apply -f kubernetes-config/gpu-pod.yaml
```



# The Retrieval Augmented Generation (RAG) Application Deployment

![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-7.png)


1. **LLM Embedding:** A service for vector representations (embeddings) of textual data preserving semantic meaning. We will use the **Retrieval Embedding NIM**, a GPU accelerated text embedding model, built on the NVIDIA software platform incorporating CUDA, TensorRT, and Triton Inference Server.

2. **LLM Inferencing:** A service for LLM Generator deployment. We will use **NIM for LLMs**, a GPU accelerated Large Language model, built on the NVIDIA software platform incorporating CUDA, TensorRT, TensorRT-LLM, and Triton Inference Server. The NIM will create an optimized LLM engine using TensorRT-LLM and serve it using NVIDIA Triton Server for high-performance, cost-effective, and low-latency inference. In this lab, we will use a Llama2-chat model.

3. **Vector Database:** A service for a vector data store and similarity search. We will use [pgvector](https://github.com/pgvector/pgvector), an open-source vector similarity search library, for document embeddings storage and similarity search.

4. **Chat Server:** A chain service interacting with the LLM Inferencing and Vector Database services to generate responses in a RAG workflow.

5. **Web UI:** A frontend service providing a Chat Web UI on top of the RAG application. The UI allows chatting with an LLM with and without retrieved documents, uploading/storing content in the vector database via a "Knowledge Base" tab and showing the retrieved contexts.



## Helm

- Helm is a tool that automates the creation, packaging, configuration, and deployment of Kubernetes applications by combining configuration files into a single reusable package. This reusable package is called a Helm chart.

### Helm chart 

- A Helm chart is a package that contains all the necessary resources to deploy an application to a Kubernetes cluster. This includes YAML configuration files for deployments, services, secrets, and configs.


![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-8.png)


- Helm pulls Helm charts from a Helm repository such as the NGC private registry. Users adjust a `values.yaml` file, which contains the actual configuration for each resource, depending on the environment and need. Then Helm resolves parameters within the Helm chart using `values.yaml` before deploying on the Kubernetes cluster.
### Helm Pipeline

- Helm Pipeline is used to manage multiple helm charts as a single resource for easier deployments. The following figure shows an example of the Helm pipeline, consisting of three Helm charts. Each Helm chart can be configured by updating the values under the `chartvalues` parameter.

![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-10.png)
![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-9.png)



## Configure Models and Database Storage

Our RAG application will require persistent storage for:

- an LLM Model (llama-2-7b-chat) used in the LLM NIM.
- Retrieval embeddings (NV-Embed-QA) in the Retrieval Embedding NIM.
- A vector database in the pgvector deployment.


![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-11.png)

As part of this lab, the AI models and the vector database are stored on the host file system. We will use a Kubernetes **P====ersistentVolume (PV)**==== to mount paths using the default `hostpath` storage class. 

> [!NOTE]
> A PV is a piece of storage in the cluster that can be provisioned manually by an administrator or dynamically provisioned using Storage Classes (for example, host-path, local-provisioner, or EBS). 

To access the PV within the pods, pods require a ====**PersistentVolumeClaim (PVC)**====, which represents a request to utilize storage. 

> [!NOTE]
> A PVC is a request for storage by a user or pod. Claims can request specific sizes and access modes (e.g., they can be mounted ReadWriteOnce, ReadOnlyMany, ReadWriteMany, or ReadWriteOncePod). PVCs mount the requested volume into the Pod’s file system, allowing the application to read and write files. 

In this section, we will create PVs and PVCs for the pods. Both *nemollm-inference* (the language NIM) and *nemollm-embedding* (the Retrieval Embedding NIM) PVCs are configured with **50Gi** while the *pgvector* is configured with only **5Gi**.

After creation of the persistent volumns : 
``` sh
NAME                    STATUS    VOLUME                 CAPACITY   ACCESS MODES   STORAGECLASS   AGE
nemollm-embedding-pvc   Bound     nemollm-embedding-pv   50Gi       RWO            manual         3s
nemollm-inference-pvc   Pending                                                    manual         3s
pgvector-pvc            Bound     pgvector-pv            5Gi        RWO            manual         3s

```

### Chain (*query-router*) Deployment Service Lookup

The Chain (query-router) service is a ====fastapi-based chat system==== that interacts with the LLM Inferencing and Vector Database services to generate responses in a RAG workflow.

The `query-router` server has 3 methods:

- `uploadDocument`: Uploads documents to the pgvector database after the splitting and vectorizing with the embedding service.
- `documentSearch`: Searches for the relevant documents in the pgvector database for a query.
- `generate`: Generates an answer from the provided prompt with an option for sourcing information from a vector database.

Next, we will upload a PDF document the vector database, search for relevant documents to a specific question and generate answers with and without the context.

We can simply use `curl` with hostname as the Kubernetes *ClonePlane IP* (`minikube_ip`) and port as the *NodePort* of the running query-router service (nodePort on port `8081`).

We can forward localhost ports to `query-router` pod using kubectl port-forward command. 

``` sh
# Check the query-router service status - note the PORT 
!kubectl get svc query-router -n rag-sample

```

``` python

# step 1 : Upload document 

# upload the pdf (takes about 1 minute)
import mimetypes

url=f"http://{minikube_ip}:{query_router_node_port}/uploadDocument"
headers = { 'accept': 'application/json'}

mime_type, _ = mimetypes.guess_type('llama2_paper.pdf')
files = { 'file': ('llama2_paper.pdf', open('llama2_paper.pdf', 'rb'), mime_type)}
response = requests.post(url, headers=headers, files=files)

response.text


# Step 2 : document search 

# Document Search Endpoint
url = f"http://{minikube_ip}:{query_router_node_port}/documentSearch"
headers = { 'accept': 'application/json'}

data = json.dumps({
  "content": "Can you talk about safety evaluation of llama2 chat?",
  "num_docs": 4
})

retrieved_documents=requests.request("POST", url, headers=headers, data=data)


# step 3 : generate 

# generate answer with use_knowledge_base = false
url = f"http://{minikube_ip}:{query_router_node_port}/generate"

data = {
  "question": "Can you talk about safety evaluation of llama2 chat?",
  "context": "You are a polite and respectful chatbot answering technical questions.",
  "use_knowledge_base": "true",
  "num_tokens": 50
}

with requests.post(url, stream=True, json=data) as r:
    for chunk in r.iter_content(16):
        print(chunk.decode("UTF-8"), end ="")



```


We can check the logs of the query-chain service.

Notice the prompt template used for the Retrieval Augmented Generation. You should see:

```
<s>[INST] <<SYS>>Use the following context to answer the user's question. If you don't know the answer,just say that you don't know, don't try to make up an answer.<</SYS>><s>[INST] Context: {context_str} Question: {query_str} Only return the helpful answer below and nothing else. Helpful answer:[/INST]"
```

### Database deployment service Lookup 

The network port for pgvector is 5432. 

It is possible to have a lookup into the psql database by connecting to the pod. 

```
$ kubectl exec -it  pgvector-0 -n rag-sample /bin/bash
```

To show tables in the vector database, you can run the following command: 
```
$ PGPASSWORD=password psql -h /var/run/postgresql -U postgres -p 5432 -d api -c "\dt"
```
You should see a table with a name `data_canonical-rag` with the number of rows.

```
               List of relations
 Schema |        Name        | Type  |  Owner   
--------+--------------------+-------+----------
 public | data_canonical-rag | table | postgres
(1 row)

```

You can count the number of elements, and access to each element using SQL command.


# Monitoring GPU within the Kubernetes Cluster

Monitoring systems include collecting/storing metrics, visualizing results, and alerting on specific observed conditions. DCGM is a suite of tools that includes active health monitoring, comprehensive diagnostics, system alerts, and governance policies for the GPU cluster. Metrics are collected with the open-source tool [Prometheus](https://prometheus.io/) and visualized with the [Grafana](https://grafana.com/) tool to create rich dashboards.

To gather GPU telemetry in Kubernetes, we will use the [dcgm-exporter](https://docs.nvidia.com/datacenter/cloud-native/kubernetes/dcgme2e.html#gpu-telemetry), which exposes GPU metrics in a format that can be scraped by Prometheus and visualized using Grafana.


## DCGM Exporter

To gather GPU telemetry in Kubernetes, its recommended to use DCGM Exporter. DCGM Exporter collects GPU telemetry data from DCGM for Prometheus and can be visualized using Grafana. DCGM Exporter is architected to take advantage of the `KubeletPodResources` API and exposes GPU metrics in a format that can be scraped by Prometheus.


![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-12.png)
## Prometheus & Grafana 

As `dcgm-exporter` depends on Prometheus, the first step is to deploy Prometheus to the cluster. If we do this out of order we will get an error.  

![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-13.png)

> [!note] 
> Prometheus is an open-source tool for monitoring the application and Kubernetes. 
> 
> Prometheus scrapes metrics from instrumented jobs, either directly or via an intermediary push gateway for short-lived jobs.
> 
> It stores all scraped samples locally and runs rules over this data to either aggregate and record new time series from existing data or generate alerts.
> 
> Prometheus targets represent how Prometheus extracts metrics from a different resources. 
> 
> In many cases, the metrics are exposed by the services themselves, such as Kubernetes. In this case, Prometheus collects metrics directly. But in some instances, like in unexposed services, Prometheus has to use exporters. 
> 
> Exporters are programs that extract data from a service and then convert them into Prometheus formats.

### Add the Prometheus repository to Helm pipeline


``` sh

# Add the prometheus-community repo
!helm repo add prometheus-community \
    https://prometheus-community.github.io/helm-charts \
    && helm repo update

# Use inspect to export the values YAML file
!helm inspect values prometheus-community/kube-prometheus-stack \
    --version 56.8 > ./kube-prometheus-stack.values

```

### Deploy Prometheus application

To access the Grafana webpage, the "domain", "root_url", and "hosts" parameters must point to your lab instance.

There is no precise solution because every student has a unique host URL.

Please, run the next two cells to find your current lab URL and use the output to populate the variable `domain_name` by replacing the `REPLACE_ME` value.


``` sh
!kubectl get pods  -n prometheus

NAME                                                        READY   STATUS            RESTARTS   AGE
alertmanager-kube-prometheus-stack-alertmanager-0           1/2     Running           0          83s
kube-prometheus-stack-grafana-799b9fdc58-kk4rn              3/3     Running           0          108s
kube-prometheus-stack-kube-state-metrics-5744bb9db6-bl4r6   1/1     Running           0          108s
kube-prometheus-stack-operator-8695d746f8-bp6jm             1/1     Running           0          108s
kube-prometheus-stack-prometheus-node-exporter-978gp        1/1     Running           0          108s
prometheus-kube-prometheus-stack-prometheus-0               0/2     PodInitializing   0          83s

```

- Currently, the Grafana pod type is `ClusterIP`. In order to expose Grafana on the class instance, we need to patch the configuration using the [kubectl patch](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/) command to specify the pod type to `NodePort`. This patch will override the previous settings.
``` sh

# List the Grafana patch

!cat kubernetes-config/grafana-patch.yaml

spec:
  type: NodePort
  ports:
    - port: 80
      nodePort: 31091
      name: grafana

```

In the next cell, we will apply the patch using `kubectl patch svc`.
``` sh

%%bash
# get the GRAFANA_NAME
GRAFANA_NAME=$(kubectl get svc --namespace prometheus -l app.kubernetes.io/name=grafana -o custom-columns=NAME:.metadata.name --no-headers)

# Apply the patch
kubectl patch svc $GRAFANA_NAME \
   -n prometheus \
   --patch "$(cat kubernetes-config/grafana-patch.yaml)"

```

====This will expose Grafana on the class instance====

``` sh
# Check the status again - note the TYPE and PORT changes
!kubectl get svc --namespace prometheus -l app.kubernetes.io/name=grafana 

NAME                            TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kube-prometheus-stack-grafana   NodePort   10.110.190.11   <none>        80:31091/TCP   4m


```

### Expose Prometheus

To access Prometheus on the class instance, we need to expose the service.

``` python
# get the Prometheus service name
PROM_NAME=!kubectl get svc --namespace prometheus -l app=kube-prometheus-stack-prometheus -o custom-columns=NAME:.metadata.name --no-headers
PROM_NAME=PROM_NAME[0]


# port forward
import subprocess
subprocess.Popen(["kubectl","-n","prometheus", "port-forward", "--address", "0.0.0.0", f"service/{PROM_NAME}", "30090:9090"])

```


### Expose Grafana

To access Grafana on the class instance, we need to expose the service.

``` python

# get the Grafana service name
GRAFANA_NAME=!kubectl get svc --namespace prometheus -l app.kubernetes.io/name=grafana -o custom-columns=NAME:.metadata.name --no-headers
GRAFANA_NAME=GRAFANA_NAME[0]
# port forward
import subprocess
subprocess.Popen(["kubectl","-n","prometheus", "port-forward", "--address", "0.0.0.0", f"service/{GRAFANA_NAME}", "31091:80"])

```


# Autoscaling the RAG application

## Horizontal Pod Autoscaling

utoscaling is a process to scale up or down workloads automatically based on the usage of a resource or multiples resources. In Kubernetes, the [Horizontal Pod Autoscaler - HPA](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) is responsible for scaling the pods horizontally based by default on memory or CPU utilization. Our objective is to enable autoscaling of the NIMs pods to efficiently handle high volumes of user requests as shown in below figure.

![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-14.png)In complex scenarios such as LLM deployment, we would use custom metrics for the scaling. This can be achieved by using Prometheus and Prometheus adapter.

Below is an overview diagram for using custom metrics based HPA scaling. 

> [!NOTE]
> The Prometheus operator scraps the metrics from the pods (1). The Prometheus adaptor pulls the metrics from Prometheus operator (2) and makes them available to custom-metrics API by registering them (3). The HPA looks at the custom-metrics API (4) and based on the rules specified in the HPA, it scales up or down the number of target pods in the replica set of the deployment (5).

![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-15.png)


> [!NOTE]
> Autoscale the NIMs based on the GPU Usage, by creating a custom rules to monitor the usage of GPU by Language NIM and Reterival Embedding NIM pods. These rules are then used by Prometheus adapter to register custom metrics. 
> The custom metrics are then used by HPAs (which we will create for Language NIM and Reterival Embedding NIM services) to scale up or down the pods.
>  We will stress the Language NIM service and monitor the autoscaling of pods.

## Create Custom Prometheus Recording Rules

Prometheus recording ====rules allows pre-computing frequently needed or computationally expensive expressions==== and saving their results as a new set of time series.

In our case, we will create custom Prometheus recording rules (====GPU Rules) to monitor the GPU usage==== of our inference and embedding pods. We will add the rules under the `gpu.rules` group.

We already have created the yaml [prometheus_rule_gpu.yaml](http://dli-018be4f4b11f-b2f3af.azure.labs.courses.nvidia.com/lab/lab/tree/kubernetes-config/prometheus_rule_gpu.yaml) for it but we need to update the labels to match with your current instance.

Our recording rules looks like the following:

- (Language NIM) The metric **nemollm_inference_gpu_avg**: `avg by (kubernetes_node, pod, namespace, gpu) (DCGM_FI_DEV_GPU_UTIL{pod=~"nemollm-inference-.*"})`
- (Reterival Embedding NIM) The metric **nemollm_embedding_gpu_avg**: `avg by (kubernetes_node, pod, namespace, gpu) (DCGM_FI_DEV_GPU_UTIL{pod=~"nemollm-embedding-.*"})`

Where we use `DCGM_FI_DEV_GPU_UTIL` for inference or embedding pod to create a custom rule. Avg function is used as an aggregator to get the value per pod of each type.

Fisrt, let's have a look at the created [prometheus_rule_gpu.yaml](http://dli-018be4f4b11f-b2f3af.azure.labs.courses.nvidia.com/lab/lab/tree/kubernetes-config/prometheus_rule_gpu.yaml) file to get an overview of the Prometheus rules.
``` yaml

apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  labels:
    app: kube-prometheus-stack
    app.kubernetes.io/instance: kube-prometheus-stack-1710254997
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/part-of: kube-prometheus-stack
    app.kubernetes.io/version: 56.8.2
    chart: kube-prometheus-stack-56.8.2
    heritage: Helm
    release: kube-prometheus-stack-1710254997
  name: kube-prometheus-stack-1709-gpu.rules
  namespace: prometheus
spec:
  groups:
  - name: gpu.rules
    rules:
    - expr: avg by (kubernetes_node, pod, namespace, gpu) (DCGM_FI_DEV_GPU_UTIL{pod=~"nemollm-inference-.*"})
      record: nemollm_inference_gpu_avg
    - expr: avg by (kubernetes_node, pod, namespace, gpu) (DCGM_FI_DEV_GPU_UTIL{pod=~"nemollm-embedding-.*"})
      record: nemollm_embedding_gpu_avg

```

Now, we can apply the updated yaml file to create custom prometheus recording rules.
``` sh

# apply the updated rule
!kubectl apply -f kubernetes-config/prometheus_rule_gpu_updated.yaml

```

### Prometheus Adapter

``` sh

# Install the Prometheus Adapter using the Helm Chart
!helm upgrade --install prometheus-adapter prometheus-community/prometheus-adapter --set prometheus.url="http://{PROM_NAME}.prometheus.svc.cluster.local"

```

Once the created recording rules are registered, you will see them in the Prometheus UI (take a few minutes for them to show up in the UI). You should be able to see those 2 rules defined:

![](../../figures/Deploying%20RAG%20Pipelines%20for%20Production%20at%20Scale-16.png)

You can also check the recorded metrics; values should yet all be at 0 as no requests where sent to those services:

- [Open the Inference Service GPU Utilization Metric on Prometheus!](http://dli-018be4f4b11f-b2f3af.azure.labs.courses.nvidia.com/prom/graph?g0.expr=nemollm_inference_gpu_avg)
- [Open the Embeddings Service GPU Utilization Metric on Prometheus!](http://dli-018be4f4b11f-b2f3af.azure.labs.courses.nvidia.com/prom/graph?g0.expr=nemollm_embedding_gpu_avg)


## Create HPAs

- In Kubernetes, a [HorizontalPodAutoscaler (HPA)](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) automatically updates a workload resource (such as a Deployment or StatefulSet), with the aim of autoscaling the workload to match the demand.
- Horizontal scaling means that the response to add or remove Pods is based on the demand.

- Kubernetes implements horizontal pod autoscaling as a control loop that runs intermittently. The default interval value is 15 seconds. Once during each period, the controller manager queries the resource utilization against the metrics specified in each HorizontalPodAutoscaler configuration. The controller manager finds the target resource defined by the `scaleTargetRef`, then selects the pods based on the target resource's `.spec.selector` labels, and obtains the metrics from the resource or metrics metrics API.

- In this lab, we have configured two HPAs:
	- one for the LLM inference
	- one for embedding recording rules.

For example, the specification of the inference HPA is below: 

``` yaml
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: StatefulSet
    name: nemollm-inference
  minReplicas: 1
  maxReplicas: 3
  metrics:
    - type: Pods
      pods:
        metric:
          name: nemollm_inference_gpu_avg
        target:
          type: AverageValue
          averageValue: 30
```

> [!NOTE]
> Here, we have specified the min and max replicas as `1` and `3` respectively. To scale, we specify the previously created metric `nemollm_inference_gpu_avg`. The target type is specified as `AverageValue`, which will take the average across all the pods and will see if the GPU usage is above or below the target value `30%` and scale up or down depending on it. The Embedding HPA definition is similar to inference one.
> 
> Our system only has 4 GPUs and three of them are occupied by the Language NIM, Retrieval Embedding NIM and Chat application. By default, we can only scale to one additional pod. Next, you will experience Kubernetes trying to scale the inference pods beyond 2 when the load is high. But the extra pods will be in the pending state since there will not be enough resources (GPUs).

First, let's check the definitions of the HPAs for both inference (language NIM) and embedding:
``` sh 
!cat kubernetes-config/hpa-inference.yaml

!cat kubernetes-config/hpa-embedding.yaml


# Create HPAs
!kubectl apply -f kubernetes-config/hpa-inference.yaml
!kubectl apply -f kubernetes-config/hpa-embedding.yaml

# check HPAs
!kubectl get hpa -A
>>
NAMESPACE    NAME                    REFERENCE                       TARGETS        MINPODS   MAXPODS   REPLICAS   AGE
rag-sample   nemollm-embedding-hpa   StatefulSet/nemollm-embedding   <unknown>/30   1         3         0          2s
rag-sample   nemollm-inference-hpa   StatefulSet/nemollm-inference   <unknown>/30   1         4         0          2s

# describe hpa 
!kubectl describe hpa nemollm-inference-hpa -n rag-sample
 
```

``` yaml
Name:                                   nemollm-inference-hpa
Namespace:                              rag-sample
Labels:                                 <none>
Annotations:                            <none>
CreationTimestamp:                      Tue, 03 Sep 2024 00:45:20 +0000
Reference:                              StatefulSet/nemollm-inference
Metrics:                                ( current / target )
  "nemollm_inference_gpu_avg" on pods:  0 / 30
Min replicas:                           1
Max replicas:                           4
StatefulSet pods:                       1 current / 1 desired
Conditions:
  Type            Status  Reason               Message
  ----            ------  ------               -------
  AbleToScale     True    ScaleDownStabilized  recent recommendations were higher than current one, applying the highest recent recommendation
  ScalingActive   True    ValidMetricFound     the HPA was able to successfully calculate a replica count from pods metric nemollm_inference_gpu_avg
  ScalingLimited  False   DesiredWithinRange   the desired count is within the acceptable range
Events:           <none>

```

### Load Test

We will use [locust](https://locust.io/) as a load testing tool. Locust is a Python-based testing tool used for load testing and user behavior simulation. In addition, it presents test results in a user-friendly dashboard.

In order to test the autoscaling, we will start stress load testing the Language NIM endpoint. We could also send the load directly to the frontend application. For demo purposes, we will only send requests to the inference endpoint.

### Configure the Stress Load Testing

- The Locust tool requires a python file for simulating the payload.

- We have already created a `locust/main.py` file in which we specify the payload in the `data` variable and post query to the endpoint `/v1/chat/completions`. Hostname is specified in the Locust tool, when we start it.
``` python 

#payload file 

from locust import HttpUser, TaskSet, task, between


class UserTasks(TaskSet):
    @task(1)
    def index(self):
        data = {
            "messages": [
                {
                    "content": "You are a polite and respectful and professional AI assistant helping people plan vacation. No emojis.",
                    "role": "system",
                },
                {"content": "What is the capital of France is called?", "role": "user"},
            ],
            "model": "llama-2-7b-chat",
            "temperature": 1,
            "max_tokens": 50,
            "n": 1,
            "stream": False,
            "stop": "\n",
            "frequency_penalty": 0.0,
        }

        response = self.client.post("/v1/chat/completions", json=data)
        json_var = response.json()
        print(json_var)


class WebsiteUser(HttpUser):
    tasks = [UserTasks]
    wait_time = between(2, 5)


```

Now, we need to deploy the Locust application in our K8s cluster.

Let's create the Locust namespace and deploy the Locust application as Helm Chart.

``` sh

# Create Locust namespace and configmap based on the above main.py file
!kubectl create ns locust
!kubectl create configmap rag-loadtest-locustfile --from-file locust/main.py -n locust

# Add the helm repo for the Locust
!helm repo add deliveryhero https://charts.deliveryhero.io/

# Install locust application
!helm upgrade --install locust deliveryhero/locust -n locust \
  --set loadtest.name=rag-loadtest \
  --set loadtest.locust_locustfile_configmap=rag-loadtest-locustfile \
  --set loadtest.locust_host="http://nemollm-inference.rag-sample.svc.cluster.local:8005"

# Wait until all the pods of locust are running. 
!kubectl get pods -n locust

```


# RAG Application Customization

In RAG applications, AI models are subject to changes. In this notebook, we will demonstrate the end-to-end process of ====replacing the live LLM (LLAMA 2)==== in the RAG application with another one. We hope this exercise offers a meaningful comprehension of the process for both system administrators looking to maintain their model state as well as engineers developing AI models.

- Demonstrate on the fly replacement of the `llama-2-7b-chat` deployed on a NIM with `mistral-7b-instruct`.
## LLM Substitution on the Deployed RAG Applications

- Suppose that a data science team developed and experimented with a new LLM to improve the end to end RAG accuracy and would like to replace the deployed LLM with the latest one.

- With NIM, we can deploy a variety of LLMs. Checkout the list of [supported LLMs](https://developer.nvidia.com/docs/nemo-microservices/inference/models.html).

- For the class scenario, we will deploy a `mistral-7b-instruct` model.

- Let's first check the current nemollm-inference (Language NIM) service status and query the name of the deployed LLM. It is available at the Minikube IP address at the port forward of `8005` that should be `30005`.,


``` sh

# Check the nemollm-inference service status - note the CLUSTER-IP and PORT 
!kubectl get svc nemollm-inference -n rag-sample 
> 
NAME                TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)                                                                      AGE
nemollm-inference   NodePort   10.102.57.33   <none>        8000:30570/TCP,8001:31005/TCP,8002:32458/TCP,8005:30005/TCP,8006:30959/TCP   76m
```

Let's now substitute the `llama-2-7b-chat` with `mistral-7b-instruct`. The Mistral model is already downloaded, optimized with TensorRT-LLM and available on the model's volume under model-store-mistral folder.

To deploy the new LLM, we will use the `helm upgrade` command passing the values `model.name` and `model.subPath`:

``` sh
! helm upgrade --install --plain-http  nemollm-inference oci://registry:5000/ohlfw0olaadg/ea-rag-examples/nemollm-inference \
   --version 0.1.0  \
   -n rag-sample \
   --set image.repository="{registry_ip}:5000/ohlfw0olaadg/ea-participants/nemollm-inference-ms" \
   --set model.name="mistral-7b-instruct" \
   --set model.subPath="model-store-mistral"

```

Check the deployment status 

``` sh

# check rag-sample status
! kubectl get pods -n rag-sample

NAME                            READY   STATUS        RESTARTS   AGE
frontend-5f776f4d85-bhhsx       1/1     Running       0          77m
nemollm-embedding-0             1/1     Running       0          77m
nemollm-inference-0             1/1     Terminating   0          77m
pgvector-0                      1/1     Running       0          77m
query-router-6f777b4fdc-mdnsh   1/1     Running       0          77m

```