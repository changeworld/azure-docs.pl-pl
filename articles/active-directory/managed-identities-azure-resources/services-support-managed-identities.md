---
title: Usługi platformy Azure obsługujące zarządzane tożsamości dla zasobów platformy Azure
description: Lista usług, które obsługują zarządzane tożsamości dla zasobów platformy Azure i uwierzytelniania usługi Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5f271f75a4faa7067f8a52d63a34772780c6448
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959908"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Przy użyciu tożsamości zarządzanej można uwierzytelniać się w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. Jesteśmy w trakcie integrowania tożsamości zarządzanych dla zasobów platformy Azure i uwierzytelniania usługi Azure AD na platformie Azure. Sprawdzaj często aktualizacje.

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Usługi platformy Azure obsługujące tożsamości zarządzane dla zasobów platformy Azure

Następujące usługi platformy Azure obsługują tożsamości zarządzane dla zasobów platformy Azure:

### <a name="azure-virtual-machines"></a>Usługa Azure Virtual Machines

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza | 
| Przypisana przez użytkownika | Dostępne | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure Virtual Machines (w regionach, w których są dostępne):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets platformy Azure

|Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |
| Przypisana przez użytkownika | Dostępne | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure Virtual Machine Scale Sets (w regionach, w których są dostępne):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Usługa Azure App Service

| Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Dostępne | Dostępne | Dostępne |
| Przypisana przez użytkownika | Dostępne | Niedostępny | Niedostępny | Niedostępny |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure App Service (w regionach, w których są dostępne):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Dostępne | Niedostępny | Niedostępny |
| Przypisana przez użytkownika | Dostępne | Dostępne | Niedostępny | Niedostępny |

Zapoznaj się z poniższą listą, aby użyć zarządzanej tożsamości z [planami platformy Azure](../../governance/blueprints/overview.md):

- [Azure Portal — przypisanie strategii](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [Interfejs API REST — przypisanie planu](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Typ tożsamości zarządzanej |Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Dostępne | Dostępne | Dostępne |
| Przypisana przez użytkownika | Dostępne | Niedostępny | Niedostępny | Niedostępny |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Functions (w regionach, w których są dostępne):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Wersja zapoznawcza | Wersja zapoznawcza | Niedostępny | Wersja zapoznawcza |
| Przypisana przez użytkownika | Niedostępny | Niedostępny | Niedostępny | Niedostępny |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Logic Apps (w regionach, w których są dostępne):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Niedostępny | Niedostępny | Niedostępny |
| Przypisana przez użytkownika | Niedostępny | Niedostępny | Niedostępny | Niedostępny |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Data Factory v2 (w regionach, w których są dostępne):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [Program PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Dostępne | Niedostępny | Niedostępny |
| Przypisana przez użytkownika | Niedostępny | Niedostępny | Niedostępny | Niedostępny |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla usługi Azure API Management (w regionach, w których są dostępne):

- [Szablon usługi Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | W systemie Linux: Wersja zapoznawcza<br>W systemie Windows: Niedostępny | Niedostępny | Niedostępny | Niedostępny |
| Przypisana przez użytkownika | W systemie Linux: Wersja zapoznawcza<br>W systemie Windows: Niedostępny | Niedostępny | Niedostępny | Niedostępny |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Container Instances (w regionach, w których są dostępne):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Szablon usługi Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Usługa Azure Container Registry Tasks

Typ tożsamości zarządzanej | Wszystkie ogólnie dostępne<br>Globalne regiony platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Niedostępny | Niedostępny | Niedostępny |
| Przypisana przez użytkownika | Wersja zapoznawcza | Niedostępny | Niedostępny | Niedostępny |

Zapoznaj się z poniższą listą, aby skonfigurować tożsamość zarządzaną dla Azure Container Registry zadań (w regionach, w których są dostępne):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD

Poniższe usługi obsługują uwierzytelnianie usługi Azure AD i zostały przetestowane przy użyciu usług klienckich, które korzystają z tożsamości zarządzanych dla zasobów platformy Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Zapoznaj się z poniższą listą, aby skonfigurować dostęp do Azure Resource Manager:

- [Przypisz dostęp za pośrednictwem Azure Portal](howto-assign-access-portal.md)
- [Przypisywanie dostępu za pomocą programu PowerShell](howto-assign-access-powershell.md)
- [Przypisywanie dostępu za pośrednictwem interfejsu wiersza polecenia platformy Azure](howto-assign-access-CLI.md)
- [Przypisywanie dostępu za pomocą szablonu Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Chmura | Identyfikator zasobu | State |
|--------|------------|--------|
| Globalne platformy Azure | `https://management.azure.com/`| Dostępne |
| Azure Government | `https://management.usgovcloudapi.net/` | Dostępne |
| Azure (Niemcy) | `https://management.microsoftazure.de/` | Dostępne |
| Azure w Chinach — 21Vianet | `https://management.chinacloudapi.cn` | Dostępne |

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

| Chmura | Identyfikator zasobu | State |
|--------|------------|--------|
| Globalne platformy Azure | `https://vault.azure.net`| Dostępne |
| Azure Government | `https://vault.usgovcloudapi.net` | Dostępne |
| Azure (Niemcy) |  `https://vault.microsoftazure.de` | Dostępne |
| Azure w Chinach — 21Vianet | `https://vault.azure.cn` | Dostępne |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Chmura | Identyfikator zasobu | State |
|--------|------------|--------|
| Globalne platformy Azure | `https://datalake.azure.net/` | Dostępne |
| Azure Government |  | Niedostępna |
| Azure (Niemcy) |   | Niedostępna |
| Azure w Chinach — 21Vianet |  | Niedostępna |

### <a name="azure-sql"></a>SQL Azure 

| Chmura | Identyfikator zasobu | State |
|--------|------------|--------|
| Globalne platformy Azure | `https://database.windows.net/` | Dostępne |
| Azure Government | `https://database.usgovcloudapi.net/` | Dostępne |
| Azure (Niemcy) | `https://database.cloudapi.de/` | Dostępne |
| Azure w Chinach — 21Vianet | `https://database.chinacloudapi.cn/` | Dostępne |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Chmura | Identyfikator zasobu | State |
|--------|------------|--------|
| Globalne platformy Azure | `https://eventhubs.azure.net` | Dostępne |
| Azure Government |  | Niedostępna |
| Azure (Niemcy) |   | Niedostępna |
| Azure w Chinach — 21Vianet |  | Niedostępna |

### <a name="azure-service-bus"></a>Magistrala usług Azure

| Chmura | Identyfikator zasobu | State |
|--------|------------|--------|
| Globalne platformy Azure | `https://servicebus.azure.net`  | Dostępne |
| Azure Government |  | Niedostępna |
| Azure (Niemcy) |   | Niedostępna |
| Azure w Chinach — 21Vianet |  | Niedostępna |

### <a name="azure-storage-blobs-and-queues"></a>Obiekty blob i kolejki usługi Azure Storage

| Chmura | Identyfikator zasobu | State |
|--------|------------|--------|
| Globalne platformy Azure | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Dostępne |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Dostępne |
| Azure (Niemcy) | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Dostępne |
| Azure w Chinach — 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Dostępne |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Chmura | Identyfikator zasobu | State |
|--------|------------|--------|
| Globalne platformy Azure | `https://*.asazure.windows.net` | Dostępne |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Dostępne |
| Azure (Niemcy) | `https://*.asazure.cloudapi.de` | Dostępne |
| Azure w Chinach — 21Vianet | `https://*.asazure.chinacloudapi.cn` | Dostępne |
