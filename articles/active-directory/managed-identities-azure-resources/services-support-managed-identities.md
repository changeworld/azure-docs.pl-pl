---
title: Usługi platformy Azure, które obsługują zarządzanych tożsamości dla zasobów platformy Azure
description: Lista usług, które obsługują zarządzanych tożsamości do uwierzytelniania usługi Azure AD i zasobów platformy Azure
services: active-directory
author: MarkusVi
ms.author: priyamo
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c67f1dad55d5d8710a997394e247793943cd21
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293498"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Usługi obsługujące zarządzanych tożsamości dla zasobów platformy Azure

Zarządzanych tożsamości dla zasobów platformy Azure dostarczanie usług platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Za pomocą tożsamości zarządzanej, można wybrać metodę uwierzytelniania dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. Na platformie Azure jesteśmy w trakcie zintegrowanie zarządzanych tożsamości do uwierzytelniania usługi Azure AD i zasobów platformy Azure. Sprawdź tu często, aby aktualizacje.

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Usługi platformy Azure obsługujące tożsamości zarządzane dla zasobów platformy Azure

Następujących usług platformy Azure obsługują zarządzanych tożsamości dla zasobów platformy Azure:

### <a name="azure-virtual-machines"></a>Usługa Azure Virtual Machines

| Typ tożsamości zarządzanych | Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisanej w systemie | Dostępne | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza | 
| Przypisane przez użytkownika | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |

Można znaleźć na poniższej liście do konfigurowania tożsamości zarządzanej usługi Azure Virtual Machines (w regionach gdzie są dostępne):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych platformy Azure

|Typ tożsamości zarządzanych | Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisanej w systemie | Dostępne | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |
| Przypisane przez użytkownika | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure Virtual Machine Scale Sets (w regionach gdzie są dostępne):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Usługa Azure App Service

| Typ tożsamości zarządzanych | Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisanej w systemie | Dostępne | Dostępne | Dostępne | Dostępne |
| Przypisane przez użytkownika | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure App Service (w regionach gdzie są dostępne):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Typ tożsamości zarządzanych | Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisanej w systemie | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |
| Przypisane przez użytkownika | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |

Można znaleźć na poniższej liście do korzystania z tożsamości zarządzanej przy użyciu [schematy Azure](../../governance/blueprints/overview.md):

- [Witryna Azure portal — przypisanie planu](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [Interfejs API REST — przypisanie planu](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Typ tożsamości zarządzanych |Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisanej w systemie | Dostępne | Dostępne | Dostępne | Dostępne |
| Przypisane przez użytkownika | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |

Można znaleźć na poniższej liście do konfigurowania tożsamości zarządzanej dla usługi Azure Functions (w regionach gdzie są dostępne):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ tożsamości zarządzanych | Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisanej w systemie | Wersja zapoznawcza | Wersja zapoznawcza | Niedostępne | Wersja zapoznawcza |
| Przypisane przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure Logic Apps (w regionach gdzie są dostępne):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ tożsamości zarządzanych | Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisanej w systemie | Dostępne | Niedostępne | Niedostępne | Niedostępne |
| Przypisane przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure Data Factory V2 (w regionach gdzie są dostępne):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [Program PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Usługa Azure API Management

Typ tożsamości zarządzanych | Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisanej w systemie | Dostępne | Dostępne | Niedostępne | Niedostępne |
| Przypisane przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure API Management (w regionach gdzie są dostępne):

- [Szablon usługi Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Typ tożsamości zarządzanych | Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisanej w systemie | W systemie Linux: Wersja zapoznawcza<br>W systemie Windows: Niedostępne | Niedostępne | Niedostępne | Niedostępne |
| Przypisane przez użytkownika | W systemie Linux: Wersja zapoznawcza<br>W systemie Windows: Niedostępne | Niedostępne | Niedostępne | Niedostępne |

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure Container Instances (w regionach gdzie są dostępne):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Szablon usługi Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Usługa Azure Container Registry Tasks

Typ tożsamości zarządzanych | Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government | Azure (Niemcy) | Azure w Chinach — 21Vianet |
| --- | --- | --- | --- | --- |
| Przypisanej w systemie | Dostępne | Niedostępne | Niedostępne | Niedostępne |
| Przypisane przez użytkownika | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne |

Zobacz poniżej, aby skonfigurować tożsamość zarządzaną zadań rejestru kontenera platformy Azure (w regionach gdzie są dostępne):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD

Następujące usługi obsługuje uwierzytelnianie w usłudze Azure AD i zostały przetestowane za pomocą usługi klienta, które korzystają z zarządzanych tożsamości dla zasobów platformy Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Można znaleźć w poniższej liście, aby skonfigurować dostęp do usługi Azure Resource Manager:

- [Przypisywanie dostępu za pośrednictwem witryny Azure portal](howto-assign-access-portal.md)
- [Przypisywanie dostępu za pomocą programu Powershell](howto-assign-access-powershell.md)
- [Przypisywanie dostępu przy użyciu wiersza polecenia platformy Azure](howto-assign-access-CLI.md)
- [Przypisywanie dostępu przy użyciu szablonu usługi Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Globalna platforma Azure | `https://management.azure.com/`| Dostępne |
| Azure Government | `https://management.usgovcloudapi.net/` | Dostępne |
| Azure (Niemcy) | `https://management.microsoftazure.de/` | Dostępne |
| Azure w Chinach — 21Vianet | `https://management.chinacloudapi.cn` | Dostępne |

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Globalna platforma Azure | `https://vault.azure.net`| Dostępne |
| Azure Government | `https://vault.usgovcloudapi.net` | Dostępne |
| Azure (Niemcy) |  `https://vault.microsoftazure.de` | Dostępne |
| Azure w Chinach — 21Vianet | `https://vault.azure.cn` | Dostępne |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Globalna platforma Azure | `https://datalake.azure.net/` | Dostępne |
| Azure Government |  | Niedostępne |
| Azure (Niemcy) |   | Niedostępne |
| Azure w Chinach — 21Vianet |  | Niedostępne |

### <a name="azure-sql"></a>Azure SQL 

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Globalna platforma Azure | `https://database.windows.net/` | Dostępne |
| Azure Government | `https://database.usgovcloudapi.net/` | Dostępne |
| Azure (Niemcy) | `https://database.cloudapi.de/` | Dostępne |
| Azure w Chinach — 21Vianet | `https://database.chinacloudapi.cn/` | Dostępne |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Globalna platforma Azure | `https://eventhubs.azure.net` | Wersja zapoznawcza |
| Azure Government |  | Niedostępne |
| Azure (Niemcy) |   | Niedostępne |
| Azure w Chinach — 21Vianet |  | Niedostępne |

### <a name="azure-service-bus"></a>Azure Service Bus

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Globalna platforma Azure | `https://servicebus.azure.net`  | Wersja zapoznawcza |
| Azure Government |  | Niedostępne |
| Azure (Niemcy) |   | Niedostępne |
| Azure w Chinach — 21Vianet |  | Niedostępne |

### <a name="azure-storage-blobs-and-queues"></a>Usługa Azure Storage blob i kolejki

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Globalna platforma Azure | `https://storage.azure.com/` | Dostępne |
| Azure Government | `https://storage.azure.com/` | Dostępne |
| Azure (Niemcy) | `https://storage.azure.com/` | Dostępne |
| Azure w Chinach — 21Vianet | `https://storage.azure.com/` | Dostępne |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Chmura | Identyfikator zasobu | Stan |
|--------|------------|--------|
| Globalna platforma Azure | `https://*.asazure.windows.net` | Dostępne |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Dostępne |
| Azure (Niemcy) | `https://*.asazure.cloudapi.de` | Dostępne |
| Azure w Chinach — 21Vianet | `https://*.asazure.chinacloudapi.cn` | Dostępne |
