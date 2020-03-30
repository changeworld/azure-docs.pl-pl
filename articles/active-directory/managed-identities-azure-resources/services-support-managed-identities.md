---
title: Usługi platformy Azure obsługujące tożsamości zarządzane — usługa Azure AD
description: Lista usług obsługujących tożsamości zarządzane dla zasobów platformy Azure i uwierzytelniania usługi Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 03/13/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9e01f1f5abfc0f76926ce503fae058c196c6e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282107"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Usługi obsługujące tożsamości zarządzane dla zasobów platformy Azure

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Przy użyciu tożsamości zarządzanej można uwierzytelnić do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. Jesteśmy w trakcie integrowania tożsamości zarządzanych dla zasobów platformy Azure i uwierzytelniania usługi Azure AD na platformie Azure. Często sprawdzaj aktualizacje.

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Usługi platformy Azure obsługujące tożsamości zarządzane dla zasobów platformy Azure

Następujące usługi platformy Azure obsługują tożsamości zarządzane dla zasobów platformy Azure:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Wersja zapoznawcza | Wersja zapoznawcza | 
| Użytkownik przypisany | ![Dostępne][check] | ![Dostępne][check] | Wersja zapoznawcza | Wersja zapoznawcza |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla maszyn wirtualnych platformy Azure (w regionach, jeśli są dostępne):

- [Portal Azure](qs-configure-portal-windows-vm.md)
- [Powershell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)
- [Reszta](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |
| Użytkownik przypisany | ![Dostępne][check] | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla zestawów skalowania maszyny wirtualnej platformy Azure (w regionach, jeśli są dostępne):

- [Portal Azure](qs-configure-portal-windows-vm.md)
- [Powershell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)
- [Reszta](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |
| Użytkownik przypisany | ![Dostępne][check] | ![Dostępne][check]  | ![Dostępne][check]  | ![Dostępne][check] |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure App Service (w regionach, jeśli są dostępne):

- [Portal Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | Niedostępne |
| Użytkownik przypisany | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby użyć tożsamości zarządzanej za pomocą [planów platformy Azure:](../../governance/blueprints/overview.md)

- [Portal Azure — przypisanie planu](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [INTERFEJS API REST — przypisanie planu](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Typ tożsamości zarządzanej |Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] | ![Dostępne][check] |
| Użytkownik przypisany | ![Dostępne][check] | ![Dostępne][check]  | ![Dostępne][check]  | ![Dostępne][check]  |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure Functions (w regionach, jeśli są dostępne):

- [Portal Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Użytkownik przypisany | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |


Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure Logic Apps (w regionach, jeśli są dostępne):

- [Portal Azure](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | ![Dostępne][check] |
| Użytkownik przypisany | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure Data Factory V2 (w regionach, jeśli są dostępne):

- [Portal Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [Powershell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [Reszta](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Sdk](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Usługa Azure API Management

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | ![Dostępne][check] | Niedostępne | Niedostępne |
| Użytkownik przypisany | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure API Management (w regionach, jeśli są dostępne):

- [Szablon usługi Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | Linux: Wersja zapoznawcza<br>Windows: Niedostępne | Niedostępne | Niedostępne | Niedostępne |
| Użytkownik przypisany | Linux: Wersja zapoznawcza<br>Windows: Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla wystąpień kontenerów platformy Azure (w regionach, jeśli są dostępne):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Szablon usługi Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Usługa Azure Container Registry Tasks

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | Niedostępne | Niedostępne | Niedostępne |
| Użytkownik przypisany | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla zadań rejestru kontenerów platformy Azure (w regionach, jeśli są dostępne):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Tożsamość zarządzana dla aplikacji sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) jest w wersji zapoznawczej i jest dostępna we wszystkich regionach.

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Przypisany system | ![Dostępne][check] | Niedostępne | Niedostępne | niedostępne |
| Użytkownik przypisany | ![Dostępne][check] | Niedostępne | Niedostępne |Niedostępne |

Zapoznaj się z następującą listą, aby skonfigurować tożsamość zarządzaną dla aplikacji sieci szkieletowej usług Azure we wszystkich regionach:
- [Szablon usługi Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Usługi platformy Azure obsługujące uwierzytelnianie usługi Azure AD

Następujące usługi obsługują uwierzytelnianie usługi Azure AD i zostały przetestowane przy użyciu usług klienckich, które używają tożsamości zarządzanych dla zasobów platformy Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Zapoznaj się z następującą listą, aby skonfigurować dostęp do usługi Azure Resource Manager:

- [Przypisywanie dostępu za pośrednictwem portalu Azure](howto-assign-access-portal.md)
- [Przypisywanie dostępu za pośrednictwem programu PowerShell](howto-assign-access-powershell.md)
- [Przypisywanie dostępu za pośrednictwem interfejsu wiersza polecenia platformy Azure](howto-assign-access-CLI.md)
- [Przypisywanie dostępu za pomocą szablonu usługi Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Dostępne][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Dostępne][check] |
| Azure (Niemcy) | `https://management.microsoftazure.de/` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://management.chinacloudapi.cn` | ![Dostępne][check] |

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Dostępne][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Dostępne][check] |
| Azure (Niemcy) |  `https://vault.microsoftazure.de` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://vault.azure.cn` | ![Dostępne][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Dostępne][check] |
| Azure Government |  | Niedostępne |
| Azure (Niemcy) |   | Niedostępne |
| Azure w Chinach — 21Vianet |  | Niedostępne |

### <a name="azure-sql"></a>Azure SQL 

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Dostępne][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Dostępne][check] |
| Azure (Niemcy) | `https://database.cloudapi.de/` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://database.chinacloudapi.cn/` | ![Dostępne][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Dostępne][check] |
| Azure Government |  | Niedostępne |
| Azure (Niemcy) |   | Niedostępne |
| Azure w Chinach — 21Vianet |  | Niedostępne |

### <a name="azure-service-bus"></a>Azure Service Bus

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Dostępne][check] |
| Azure Government |  | ![Dostępne][check] |
| Azure (Niemcy) |   | Niedostępne |
| Azure w Chinach — 21Vianet |  | Niedostępne |









### <a name="azure-storage-blobs-and-queues"></a>Obiekty blob i kolejki usługi Azure Storage

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Dostępne][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Dostępne][check] |
| Azure (Niemcy) | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Dostępne][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Dostępne][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Dostępne][check] |
| Azure (Niemcy) | `https://*.asazure.cloudapi.de` | ![Dostępne][check] |
| Azure w Chinach — 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Dostępne][check] |


[check]: media/services-support-managed-identities/check.png "Dostępne"
