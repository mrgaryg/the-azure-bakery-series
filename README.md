# The Azure Bakery series

Welcome to 'The Azure Bakery'. In this series, I'll show you how to bake your own delicious Azure cake.

The idea behind this series is to approach Azure in an easy-to-understand and fun way. The goal is to engineer and deploy solutions in the best way possible. At the same time, we're trying to keep it simple.

[Click here](https://itnext.io/the-azure-bakery-series-introduction-277be6b7cdd3?source=friends_link&sk=5a0f506c63bd47be964728d462ace005 "The Azure Bakery series: Introduction") for the introduction to the series.

![An Azure cake](https://github.com/smorenburg/the-azure-bakery-series/blob/main/images/the-azure-bakery-series-cake.png?raw=true)

## Design

The design of the environment is based on the enterprise-scale landing zone architecture framework. The 'Management' subscription contains a Log Analytics workspace for centralizing the logs, metrics, and audit data for this and future workloads. The 'Landing zone' subscription includes the web application based on an App Service app with an Azure SQL Database as a backend.

The App Service app uses a system-assigned managed identity to access and retrieve the Azure SQL Server's connection string. The identity has an active access policy within the key vault. The App Service app will automatically scale based on the CPU load and has two deployment slots. There are four basic Azure Monitor alert rules configured to monitor the web application.

![The landing zone and workload design](https://github.com/smorenburg/the-azure-bakery-series/blob/main/images/the-azure-bakery-series-landing-zone-design.png?raw=true)

## Deployment steps

### Management

`az login`

`az account set -s <managementSubscriptionId>`

`az deployment sub create -l <location> -f source/management/mgmt-rg.deploy.json`

`az deployment group create -g mgmt-rg -f source/management/mgmt.deploy.json`

### Landing zone

`az login`

`az account set -s <landingZoneSubscriptionId>`

`az deployment sub create -l <location> -f source/landing-zone/app-rg.deploy.json`

`az deployment group create -g app-rg -f source/landing-zone/app.deploy.json`
