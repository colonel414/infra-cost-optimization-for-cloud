# Reducing Costs of Kubernetes Workloads and Cloud Infrastructure

This repository is a comprehensive guide on reducing the costs associated with Kubernetes workloads and cloud infrastructure. From optimizing your Kubernetes clusters to managing cloud resources efficiently, this guide provides best practices and real-world examples to help you reduce expenses without compromising performance.

## Table of Contents

1. [Introduction](#introduction)
2. [Why Optimize for Costs](#why-optimize-for-costs)
3. [Key Strategies for Kubernetes](#key-strategies-for-kubernetes)
    - [Autoscaling](#autoscaling)
    - [Resource Requests and Limits](#resource-requests-and-limits)
    - [Cluster Size Management](#cluster-size-management)
    - [Right-Sizing Nodes](#right-sizing-nodes)
4. [Cloud Infrastructure Cost Optimization](#cloud-infrastructure-cost-optimization)
    - [Use Spot Instances](#use-spot-instances)
    - [Rightsize Compute Resources](#rightsize-compute-resources)
    - [Storage Optimization](#storage-optimization)
    - [Monitor and Alert](#monitor-and-alert)
5. [Examples and Configurations](#examples-and-configurations)

## Introduction

Cloud infrastructure, including Kubernetes workloads, often becomes expensive as applications scale. However, there are numerous strategies you can employ to optimize your infrastructure and reduce unnecessary costs. This guide outlines some of the best practices for cost optimization in Kubernetes and cloud infrastructure.

---

## Why Optimize for Costs?

Cost optimization isn’t just about saving money—it’s about being efficient. The benefits include:

- **Lower operational expenses** without affecting performance.
- **Avoiding waste** in over-provisioned resources.
- **Gaining more control** over your infrastructure and budget.

---

## Key Strategies for Kubernetes

### 1. Autoscaling

Implementing **Horizontal Pod Autoscaling (HPA)** **Vertical Pod Autoscaling (VPA)** and **Cluster Autoscaler** ensures that your Kubernetes cluster scales based on actual demand.

- **Horizontal Pod Autoscaling:** Automatically scales the `number of pods` based on CPU or memory usage. You can configure it as follows:

```bash
kubectl autoscale deployment my-app --cpu-percent=50 --min=1 --max=10
```

This command automatically scales the my-app deployment to maintain an average CPU utilization of 50%. The minimum number of pods is set to 1, and the maximum is 10.

- **Vertical Pod Autoscaling (VPA)** Automatically scales the `allocated resources` based on CPU or memory usage.You can configure it as follows:

```bash
kubectl autoscale deployment my-app --min-cpu-percent=20 --max-cpu-percent=75
```

This command automatically scales the my-app deployment to maintain a minimum CPU utilization of 20%, and the maximum is 75%.

- **Cluster Autoscaler:** Adjusts the number of nodes in the cluster based on workload.

Learn more in our [autoscaling guide](kubernetes/autoscaling.md).

### 2. Resource Requests and Limits

Setting appropriate resource requests and limits for containers can prevent over-provisioning. Use these effectively to avoid paying for unused resources and unnecessary costs.

```yaml
apiversion: apps/v1
kind: Deployment
metadata:
  name: my-app
  spec:
    replicas: 3
    template:
        spec:
              containers:
                    - name: my-app-container
                      image: my-app-image
                      resources:
                                requests:
                                memory: "512Mi"
                                cpu: "250m"
                                limits:
                                memory: "1024Mi"
                                cpu: "500m"
```

This ensures that your application will use a minimum of 512Mi of memory and 250m of CPU, with an upper limit of 1024Mi of memory and 500m of CPU. By rightsizing your resource requests and limits, you avoid paying for resources that aren't being used.

### 3. Cluster Size Management

Analyze whether the cluster size is optimized for your workloads. Regularly review cluster resource utilization, especially for production environments.

### 4. Right-Sizing Nodes

In Kubernetes, the choice of node size (VM size) plays a significant role in cost. Over-provisioning nodes with excessive CPU/memory capacity results in wasted costs.

**Example**: Right-Sizing Nodes

In AWS, GCP, or Azure, choose smaller nodes with enough resources to handle your workload. A smaller instance like AWS t3.medium may be enough for a development environment, whereas a production environment may need m5.large instances.

```bash
# Example of resizing nodes in AWS EKS
eksctl scale nodegroup --cluster=my-cluster --name=my-nodegroup --nodes=3
```

By reducing the number of nodes or choosing smaller instance types, you can directly lower costs.

## Cloud Infrastructure Cost Optimization

### 1. Use Spot Instances

Spot instances (AWS), Preemptible VMs (GCP), and Spot VMs (Azure) allow you to run workloads at a significant discount compared to on-demand instances. They are suitable for fault-tolerant or batch-processing workloads.

**Example**: AWS Spot Instances

```bash
aws ec2 run-instances --instance-type t3.medium --spot-price "0.0123" --image-id ami-12345678
```

This command launches a Spot instance with a maximum price of $0.0123 per hour. Spot instances can save you up to 90% on compute costs.

### 2. Rightsize Compute Resources

Rightsizing refers to adjusting the resources **(e.g., CPU, memory, instance size)** to match the actual needs of your applications. Utilize cloud provider recommendations or cost analysis tools like AWS Trusted Advisor, GCP Recommender, or Azure Advisor.
You can use **AWS Cost Explorer** or **GCP Recommender** to view recommendations for rightsizing.

```bash
aws ce get-reservation-utilization
```

The above command helps you review your reserved instances' utilization and adjust where necessary.

### 3. Storage Optimization

Review your storage costs and apply lifecycle policies to move unused or rarely accessed data to cheaper storage tiers like AWS S3 Glacier.

### Example AWS S3 Lifecycle Policy

```json
{
  "Rules": [
    {
      "ID": "Move to Glacier",
      "Status": "Enabled",
      "Prefix": "",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "GLACIER"
        }
      ]
    }
  ]
}

```

This lifecycle policy moves objects in the S3 bucket to the Glacier storage class after 30 days, reducing long-term storage costs.

### 4. Monitor and Alert

Use monitoring tools such as Prometheus, Grafana, or cloud-native tools **(AWS CloudWatch, GCP Monitoring, Azure Monitor)** to track resource usage and set up alerts for unexpected cost spikes.

```bash
helm install prometheus prometheus-community/prometheus
helm install grafana grafana/grafana
```

## Examples and Configurations

In the [examples folder](examples/), you'll find:

- **Terraform configurations** to automate provisioning and cost-saving practices.
- **Bash scripts** to manage Kubernetes scaling and storage lifecycle policies.
- **Ansible configurations** to maintain cost-saving configurations and update when necessary
- **Azure Bicep scripts** to automate management of costs for azure instances

Example Terraform script to deploy Spot instances:

```hcl
resource "aws_instance" "spot" {
  ami = "ami-123456"
  instance_type = "t3.medium"
  spot_price = "0.0123"
  key_name = "my-key"
}
```

## Contributing

If you have suggestions or would like to contribute, feel free to submit a pull request.