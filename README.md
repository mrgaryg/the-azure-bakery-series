# The Azure Bakery series

Welcome to 'The Azure Bakery'. In this series, I'll show you how to bake your own delicious Azure cake.

The idea behind this series is to approach Azure in an easy-to-understand and fun way. The goal is to engineer and deploy solutions in the best way possible. At the same time, we're trying to keep it simple.

[Click here](https://itnext.io/the-azure-bakery-series-introduction-277be6b7cdd3?source=friends_link&sk=5a0f506c63bd47be964728d462ace005 "The Azure Bakery series: Introduction") for the introduction to the series.

![An Azure cake](https://github.com/smorenburg/the-azure-bakery-series/blob/main/images/the-azure-bakery-series-cake.png?raw=true)

## Design recipe

The environment's design recipe is based on the enterprise-scale landing zone architecture framework, illustrated in the diagram below.

![The landing zone and workload design](https://github.com/smorenburg/the-azure-bakery-series/blob/main/images/the-azure-bakery-series-landing-zone-design.png?raw=true)

The management subscription contains a Log Analytics workspace for centralizing the logs, metrics, and audit data. And the landing zone subscription includes the web application based on an App Service app with an Azure SQL Database as a backend.

The App Service app uses a system-assigned managed identity to access and retrieve the Azure SQL Server's connection string from the Azure Key Vault. There's an access policy with the object's identifier of the managed identity to access the key vault's secrets.

And there's an auto-scaling rule configured that will scale the App Service app based on the CPU load. Two deployment slots allow you to deploy into staging and swapping this into production. And four Azure Monitor alert rules to monitor the web application.

## Deployment steps

```bash
az login
```

### Management

```bash
az deployment sub create \
    --location <location> \
    --template-file source/management/mgmt-rg.deploy.json \
    --parameters location=<location> \
    --subscription <mgmtSubscriptionId>
```

```bash
az deployment group create \
    --resource-group mgmt-rg \
    --template-file source/management/mgmt.deploy.json \
    --parameters uniqueId=<uniqueId> \
    --subscription <mgmtSubscriptionId>
```

### Landing zone

```bash
az deployment sub create \
    --location <location> \
    --template-file source/landing-zone/app-rg.deploy.json \
    --parameters location=<location> \
    --subscription <lzSubscriptionId>
```

```bash
az deployment group create \
    --resource-group app-rg \
    --template-file source/landing-zone/app.deploy.json \
    --parameters uniqueId=<uniqueId> \
                 mgmtSubscriptionId=<mgmtSubscriptionId> \
    --subscription <lzSubscriptionId>
```

### Remove delete locks and resource groups

```bash
az lock delete \
    --name rgCanNotDelete \
    --resource-group app-rg \
    --subscription <lzSubscriptionId>

az group delete \
    --name app-rg \
    --subscription <lzSubscriptionId>

az lock delete \
    --name rgCanNotDelete \
    --resource-group mgmt-rg \
    --subscription <mgmtSubscriptionId>

az group delete \
    --name mgmt-rg \
    --subscription <mgmtSubscriptionId>
```
