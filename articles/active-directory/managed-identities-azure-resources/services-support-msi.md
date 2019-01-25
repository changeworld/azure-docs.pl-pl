---
title: Usługi platformy Azure, które obsługują zarządzanych tożsamości dla zasobów platformy Azure
description: Lista usług, które obsługują zarządzanych tożsamości do uwierzytelniania usługi Azure AD i zasobów platformy Azure
services: active-directory
author: priyamohanram
ms.author: priyamo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: daveba
ms.openlocfilehash: e27d1cf6cbff01ff3268e250ef2816a0b74b1a33
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889029"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Usługi obsługujące zarządzanych tożsamości dla zasobów platformy Azure

Zarządzanych tożsamości dla zasobów platformy Azure dostarczanie usług platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Za pomocą tożsamości zarządzanej, można wybrać metodę uwierzytelniania dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. Na platformie Azure jesteśmy w trakcie zintegrowanie zarządzanych tożsamości do uwierzytelniania usługi Azure AD i zasobów platformy Azure. Sprawdź tu często, aby aktualizacje.

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Usługi platformy Azure obsługujące tożsamości zarządzane dla zasobów platformy Azure

Następujących usług platformy Azure obsługują zarządzanych tożsamości dla zasobów platformy Azure:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

|Typ tożsamości zarządzanych |  Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |
| Przypisana przez użytkownika | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza

Można znaleźć na poniższej liście do konfigurowania tożsamości zarządzanej usługi Azure Virtual Machines (w regionach gdzie są dostępne):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych platformy Azure

|Typ tożsamości zarządzanych |  Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza |
| Przypisana przez użytkownika | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza | Wersja zapoznawcza

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure Virtual Machine Scale Sets (w regionach gdzie są dostępne):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [Program PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)
- [Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

|Typ tożsamości zarządzanych |  Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Dostępne | Dostępne | Dostępne |
| Przypisana przez użytkownika | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure App Service (w regionach gdzie są dostępne):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Azure Functions

Typ tożsamości zarządzanych |  Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Dostępne | Dostępne | Dostępne |
| Przypisana przez użytkownika | Wersja zapoznawcza | Niedostępne | Niedostępne | Niedostępne

Można znaleźć na poniższej liście do konfigurowania tożsamości zarządzanej dla usługi Azure Functions (w regionach gdzie są dostępne):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ tożsamości zarządzanych |  Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Dostępne | Dostępne | Dostępne |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure Logic Apps (w regionach gdzie są dostępne):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Szablon usługi Azure Resource Manager](/azure/app-service/overview-managed-identity#deployment-template)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ tożsamości zarządzanych |  Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure Data Factory V2 (w regionach gdzie są dostępne):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)
- [Program PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk)

### <a name="azure-api-management"></a>Usługa Azure API Management

Typ tożsamości zarządzanych |  Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| Przypisana przez system | Dostępne | Dostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | Niedostępne | Niedostępne | Niedostępne | Niedostępne

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure API Management (w regionach gdzie są dostępne):

- [Szablon usługi Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Typ tożsamości zarządzanych |  Wszystkie ogólnie dostępne<br>Globalnych regionów platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| Przypisana przez system | W systemie Linux: Wersja zapoznawcza<br>W systemie Windows: Niedostępne | Niedostępne | Niedostępne | Niedostępne |
| Przypisana przez użytkownika | W systemie Linux: Wersja zapoznawcza<br>W systemie Windows: Niedostępne | Niedostępne | Niedostępne | Niedostępne

Można znaleźć na poniższej liście, można skonfigurować tożsamości zarządzanej dla usługi Azure Container Instances (w regionach gdzie są dostępne):

- [Interfejs wiersza polecenia platformy Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Szablon usługi Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


## <a name="azure-services-that-support-azure-ad-authentication"></a>Usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD

Następujące usługi obsługuje uwierzytelnianie w usłudze Azure AD i zostały przetestowane za pomocą usługi klienta, które korzystają z zarządzanych tożsamości dla zasobów platformy Azure.

| Usługa | Identyfikator zasobu | Stan | Przypisywanie dostępu |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | Dostępne | [Azure Portal](howto-assign-access-portal.md) <br>[Program PowerShell](howto-assign-access-powershell.md) <br>[Interfejs wiersza polecenia platformy Azure](howto-assign-access-CLI.md) <br>[Szablon usługi Azure Resource Manager](../../role-based-access-control/role-assignments-template.md) |
| W usłudze Azure Key Vault | `https://vault.azure.net` | Dostępne |  
| Azure Data Lake | `https://datalake.azure.net/` | Dostępne |
| Azure SQL | `https://database.windows.net/` | Dostępne |
| Azure Event Hubs | `https://eventhubs.azure.net` | Wersja zapoznawcza |
| Azure Service Bus | `https://servicebus.azure.net` | Wersja zapoznawcza |
| Azure Storage | `https://storage.azure.com/` | Wersja zapoznawcza |