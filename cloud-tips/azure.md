# Azure Cost Optimization

## 1. Reserved Instances (RIs)
Azure Reserved Instances allow you to commit to a 1-year or 3-year term for significant savings, especially for virtual machines (VMs) and SQL databases.

- **Best Use Case:** RIs are ideal for workloads with predictable usage patterns, such as enterprise applications, production databases, and internal tools.

- **Actionable Steps:**

    1. Estimate your needs using the Azure Pricing Calculator.

    2. Select VM sizes you expect to use long-term. Monitor existing usage trends through Azure Monitor.

    3. Purchase RIs based on actual or forecasted demand.

This example is used to Purchase a 3-year Reserved Instance for a standard virtual machine:

```bash
az vm purchase-reserved-vm-instance \
--instance-type Standard_D4s_v3 \
--location eastus \
--term 3-year
```

- **Additional Tip:** Azure offers Exchange and Refund options for RIs, providing flexibility if your needs change.

## 2. Spot VMs
Spot VMs offer significant savings (up to 90%) on VMs that are subject to being interrupted when Azure needs the capacity.

- **Best Use Case:** Spot VMs are ideal for stateless applications, batch jobs, CI/CD pipelines, and machine learning workloads that can handle interruptions.

- **Actionable Steps:**

    1. Use Spot pricing in Azure when running workloads that can tolerate interruptions.

    2. Set maximum prices for Spot VMs to limit spending.

**Example**

```bash
az vm create \
--resource-group MyResourceGroup \
--name MySpotVM \
--image UbuntuLTS \
--size Standard_B2s \
--priority Spot \
--max-price 0.01
```

## 3. Auto-Scaling and Rightsizing

Auto-scaling ensures that your infrastructure adjusts dynamically to demand. Rightsizing ensures that resources are properly configured for their workloads, avoiding over provisioning.

- **Best Use Case:** Environments with fluctuating workloads, such as web applications, microservices, and cloud-based APIs.

- **Actionable Steps:**

    1. Use VM Scale Sets to automatically add/remove VMs based on metrics like CPU utilization.

    2. Use Azure Advisor to get insights on underutilized or oversized VMs.

**Example:** A simple auto-scaling configuration for a VM Scale Set based on CPU usage:

```yaml
apiversion: autoscale/v1
kind: VMScaleSet
properties:
  minSize: 2
  maxSize: 10
  rules:
  - metricTrigger:
      metricName: "Percentage CPU"
      operator: GreaterThan
      threshold: 75
  - scaleAction:
      type: ChangeCountBy
      value: 2
```

- **Additional Tip:** Regularly review performance using Azure Monitor to adjust VM sizes

## 4. Azure Hybrid Benefit

Azure Hybrid Benefit allows you to bring your on-premises Windows Server and SQL Server licenses to Azure, significantly reducing costs (up to 85%).

**Best Use Case:** Organizations that already own on-premises Microsoft licenses.

**Actionable Steps:**

    1. Assess your current license inventory.

    2. Migrate eligible workloads to Azure and apply Azure Hybrid Benefit.

    3. Convert existing pay-as-you-go VMs to Hybrid Benefit VMs.

**Example:**

```bash
az vm update \
--resource-group myResourceGroup \
--name myVM \
--set licenseType=Windows_Server
```

## 5. Managed Disks with Appropriate Tiers
Azure offers multiple disk storage tiers to match different performance needs: Standard HDD, Standard SSD, Premium SSD, and Ultra SSD.

**Best Use Case:** Development/test environments, production databases, and workloads requiring low latency.

**Actionable Steps:**

  1. Use Standard HDD for dev/test and workloads with lower IOPS.

  2. Use Premium SSD only for high IOPS production environments.

  3. Review disk performance in Azure Monitor and adjust tiers accordingly.

**Example:** Create a Standard HDD disk for a non-production workload:

```bash
az disk create \
--resource-group myResourceGroup \
--name myDisk \
--size 128 \
--sku Standard_LRS
```

## 6. Azure Policy for Cost Management

Use Azure Policy to enforce organizational cost-saving policies, such as limiting the deployment of specific high-cost resources, ensuring resources are tagged for cost tracking, and ensuring VMs are appropriately sized.

**Best Use Case:** Large organizations with multiple teams and Azure subscriptions.

**Actionable Steps:**

  1. Create policies to limit the creation of high-cost VMs.

  2. Ensure tagging of all resources for cost tracking and governance.

**Example:** Restricting the creation of large VMs to avoid high costs:

```bash
az policy assignment create \
--name "RestrictVMSize" \
--scope "/subscriptions/{subscriptionId}" \
--policy "{ 'if': { 'field': 'Microsoft.Compute/virtualMachines/sku.name', 'in': ['Standard_DS3_v2', 'Standard_E64_v3'] }, 'then': { 'effect': 'deny' } }"
```

## 7. Azure Cost Management + Billing

Azure provides detailed cost analysis, budgeting, and recommendations via Cost Management + Billing.

**Best Use Case:** Any organization looking to track and manage Azure spending effectively.

**Actionable Steps:**

  1. Set up cost alerts to receive notifications when costs exceed predefined limits.
  2. Create detailed budgets and analyze spending patterns to identify areas of improvement.