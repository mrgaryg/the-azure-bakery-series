# The Azure Bakery series

Welcome to 'The Azure Bakery'. In this series, I'll show you how to bake your own delicious Azure cake.

The idea behind this series is to approach Azure in an easy-to-understand and fun way. The goal is to engineer and deploy solutions in the best way possible. At the same time, we're trying to keep it simple.

## Deployment steps

### Management

`az login`
`az account set -s <mgmtSubscriptionId>`
`az deployment sub create -l <location> -f source/management/mgmt-rg.deploy.json`
`az deployment group create -l <location> -f source/management/mgmt.deploy.json`

### Landing zone

`az login`
`az account set -s <landingZoneSubscriptionId>`
`az deployment sub create -l <location> -f source/landing-zone/webapp-rg.deploy.json`
`az deployment group create -l <location> -f source/landing-zone/webapp.deploy.json`
