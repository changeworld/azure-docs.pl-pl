---
title: Azure Services that support managed identities - Azure AD
description: List of services that support managed identities for Azure resources and Azure AD authentication
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ead9b53f530a309d6bdb3bd384c29650bf5c8e6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224304"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services that support managed identities for Azure resources

Managed identities for Azure resources provide Azure services with an automatically managed identity in Azure Active Directory. Using a managed identity, you can authenticate to any service that supports Azure AD authentication without having credentials in your code. We are in the process of integrating managed identities for Azure resources and Azure AD authentication across Azure. Check back often for updates.

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Usługi platformy Azure obsługujące tożsamości zarządzane dla zasobów platformy Azure

The following Azure services support managed identities for Azure resources:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Managed identity type | All Generally Available<br>Global Azure Regions | Platforma Azure dla instytucji rządowych | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostępna | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza | 
| User assigned | Dostępna | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |

Refer to the following list to configure managed identity for Azure Virtual Machines (in regions where available):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager templates](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Managed identity type | All Generally Available<br>Global Azure Regions | Platforma Azure dla instytucji rządowych | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostępna | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |
| User assigned | Dostępna | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |

Refer to the following list to configure managed identity for Azure Virtual Machine Scale Sets (in regions where available):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager templates](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Managed identity type | All Generally Available<br>Global Azure Regions | Platforma Azure dla instytucji rządowych | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostępna | Dostępna | Dostępna | Dostępna |
| User assigned | Dostępna | Niedostępne | Niedostępne | Niedostępne |

Refer to the following list to configure managed identity for Azure App Service (in regions where available):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Managed identity type | All Generally Available<br>Global Azure Regions | Platforma Azure dla instytucji rządowych | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostępna | Dostępna | Niedostępne | Niedostępne |
| User assigned | Dostępna | Dostępna | Niedostępne | Niedostępne |

Refer to the following list to use a managed identity with [Azure Blueprints](../../governance/blueprints/overview.md):

- [Azure portal - blueprint assignment](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - blueprint assignment](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Stan usługi Funkcje Azure

Managed identity type |All Generally Available<br>Global Azure Regions | Platforma Azure dla instytucji rządowych | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostępna | Dostępna | Dostępna | Dostępna |
| User assigned | Dostępna | Niedostępne | Niedostępne | Niedostępne |

Refer to the following list to configure managed identity for Azure Functions (in regions where available):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Managed identity type | All Generally Available<br>Global Azure Regions | Platforma Azure dla instytucji rządowych | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Wersja zapoznawcza | Wersja zapoznawcza | Niedostępne | Wersja zapoznawcza |
| User assigned | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Refer to the following list to configure managed identity for Azure Logic Apps (in regions where available):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal-system-logic-app)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Managed identity type | All Generally Available<br>Global Azure Regions | Platforma Azure dla instytucji rządowych | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostępna | Niedostępne | Niedostępne | Niedostępne |
| User assigned | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Refer to the following list to configure managed identity for Azure Data Factory V2 (in regions where available):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [Program PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Usługa Azure API Management

Managed identity type | All Generally Available<br>Global Azure Regions | Platforma Azure dla instytucji rządowych | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostępna | Dostępna | Niedostępne | Niedostępne |
| User assigned | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Refer to the following list to configure managed identity for Azure API Management (in regions where available):

- [Szablon usługi Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Managed identity type | All Generally Available<br>Global Azure Regions | Platforma Azure dla instytucji rządowych | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Linux: Preview<br>Windows: Not available | Niedostępne | Niedostępne | Niedostępne |
| User assigned | Linux: Preview<br>Windows: Not available | Niedostępne | Niedostępne | Niedostępne |

Refer to the following list to configure managed identity for Azure Container Instances (in regions where available):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Szablon usługi Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Usługa Azure Container Registry Tasks

Managed identity type | All Generally Available<br>Global Azure Regions | Platforma Azure dla instytucji rządowych | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostępna | Niedostępne | Niedostępne | Niedostępne |
| User assigned | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |

Refer to the following list to configure managed identity for Azure Container Registry Tasks (in regions where available):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure services that support Azure AD authentication

The following services support Azure AD authentication, and have been tested with client services that use managed identities for Azure resources.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Refer to the following list to configure access to Azure Resource Manager:

- [Assign access via Azure portal](howto-assign-access-portal.md)
- [Assign access via Powershell](howto-assign-access-powershell.md)
- [Assign access via Azure CLI](howto-assign-access-CLI.md)
- [Assign access via Azure Resource Manager template](../../role-based-access-control/role-assignments-template.md)

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Dostępna |
| Platforma Azure dla instytucji rządowych | `https://management.usgovcloudapi.net/` | Dostępna |
| Azure (Niemcy) | `https://management.microsoftazure.de/` | Dostępna |
| Azure w Chinach — 21Vianet | `https://management.chinacloudapi.cn` | Dostępna |

### <a name="azure-key-vault"></a>Azure Key Vault

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Dostępna |
| Platforma Azure dla instytucji rządowych | `https://vault.usgovcloudapi.net` | Dostępna |
| Azure (Niemcy) |  `https://vault.microsoftazure.de` | Dostępna |
| Azure w Chinach — 21Vianet | `https://vault.azure.cn` | Dostępna |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Dostępna |
| Platforma Azure dla instytucji rządowych |  | Niedostępne |
| Azure (Niemcy) |   | Niedostępne |
| Azure w Chinach — 21Vianet |  | Niedostępne |

### <a name="azure-sql"></a>Azure SQL 

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Dostępna |
| Platforma Azure dla instytucji rządowych | `https://database.usgovcloudapi.net/` | Dostępna |
| Azure (Niemcy) | `https://database.cloudapi.de/` | Dostępna |
| Azure w Chinach — 21Vianet | `https://database.chinacloudapi.cn/` | Dostępna |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Dostępna |
| Platforma Azure dla instytucji rządowych |  | Niedostępne |
| Azure (Niemcy) |   | Niedostępne |
| Azure w Chinach — 21Vianet |  | Niedostępne |

### <a name="azure-service-bus"></a>Azure Service Bus

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Dostępna |
| Platforma Azure dla instytucji rządowych |  | Dostępna |
| Azure (Niemcy) |   | Niedostępne |
| Azure w Chinach — 21Vianet |  | Niedostępne |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobs and queues

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Dostępna |
| Platforma Azure dla instytucji rządowych | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Dostępna |
| Azure (Niemcy) | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Dostępna |
| Azure w Chinach — 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Dostępna |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Dostępna |
| Platforma Azure dla instytucji rządowych | `https://*.asazure.usgovcloudapi.net` | Dostępna |
| Azure (Niemcy) | `https://*.asazure.cloudapi.de` | Dostępna |
| Azure w Chinach — 21Vianet | `https://*.asazure.chinacloudapi.cn` | Dostępna |
