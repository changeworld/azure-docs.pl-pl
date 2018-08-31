---
title: Usługi platformy Azure, które obsługują tożsamość usługi zarządzanej
description: Listę usług, które obsługują tożsamość usługi zarządzanej i uwierzytelniania usługi Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: d738abc225e2148460012d9fe200131882b1e698
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306171"
---
# <a name="services-that-support-managed-service-identity"></a>Usługi obsługujące tożsamości usługi zarządzanej 

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Za pomocą tożsamości zarządzanej, można wybrać metodę uwierzytelniania dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. Jesteśmy w trakcie integrowanie uwierzytelniania tożsamości usługi zarządzanej i usługi Azure AD na platformie Azure. Sprawdź tu często, aby aktualizacje.

## <a name="azure-services-that-support-managed-service-identity"></a>Usługi platformy Azure, które obsługują tożsamość usługi zarządzanej

Następujących usług platformy Azure obsługuje tożsamości usługi zarządzanej.

| Usługa | Stan systemu | Użytkownik, któremu przypisano stanu| Konfigurowanie | Pobierz token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Wersja zapoznawcza | Wersja zapoznawcza | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[Program PowerShell](qs-configure-powershell-windows-vm.md)<br>[Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)<br>[Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[Program PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Zestawy skali maszyn wirtualnych | Wersja zapoznawcza | Wersja zapoznawcza | [Azure Portal](qs-configure-portal-windows-vmss.md)<br>[Program PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vmss.md)<br>[Szablony usługi Azure Resource Manager](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[Program PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | Dostępne | Niedostępne | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interfejs wiersza polecenia platformy Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Szablon usługi Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[Program PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | Dostępne | Niedostępne | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interfejs wiersza polecenia platformy Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Szablon usługi Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[Program PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | Wersja zapoznawcza | Niedostępne | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[Program PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Usługa Azure API Management | Dostępne | Niedostępne | [Szablon usługi Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity) | 


## <a name="azure-services-that-support-azure-ad-authentication"></a>Usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD

Następujące usługi obsługuje uwierzytelnianie w usłudze Azure AD i zostały przetestowane za pomocą usługi klienta, które korzystają z tożsamości usługi zarządzanej.

| Usługa | Identyfikator zasobu | Stan | Date | Przypisywanie dostępu |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Dostępne | Września 2017 r. | [Azure Portal](howto-assign-access-portal.md) <br>[Program PowerShell](howto-assign-access-powershell.md) <br>[Interfejs wiersza polecenia platformy Azure](howto-assign-access-CLI.md) |
| W usłudze Azure Key Vault | https://vault.azure.net | Dostępne | Września 2017 r. | |
| Azure Data Lake | https://datalake.azure.net/ | Dostępne | Września 2017 r. | |
| Azure SQL | https://database.windows.net/ | Dostępne | Października 2017 r. | |
| Azure Event Hubs | https://eventhubs.azure.net | Dostępne | Grudnia 2017 r. | |
| Azure Service Bus | https://servicebus.azure.net | Dostępne | Grudnia 2017 r. | |
| Azure Storage | https://storage.azure.com/ | Wersja zapoznawcza | Maj 2018 r. | |