---
title: Usługi platformy Azure, które obsługują zarządzanych tożsamości dla zasobów platformy Azure
description: Lista usług, które obsługują zarządzanych tożsamości do uwierzytelniania usługi Azure AD i zasobów platformy Azure
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 4f7e672aa75d8344f0c4ae35ff2f573d1f48a0ea
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337151"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Usługi obsługujące zarządzanych tożsamości dla zasobów platformy Azure

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Za pomocą tożsamości zarządzanej, można wybrać metodę uwierzytelniania dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. Na platformie Azure jesteśmy w trakcie zintegrowanie zarządzanych tożsamości do uwierzytelniania usługi Azure AD i zasobów platformy Azure. Sprawdź tu często, aby aktualizacje.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Usługi platformy Azure, które obsługują zarządzanych tożsamości dla zasobów platformy Azure

Następujących usług platformy Azure obsługują zarządzanych tożsamości dla zasobów platformy Azure:

| Usługa | Przypisana przez system stanu | Użytkownik przypisany stan| Konfiguracja | Pobierz token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Wersja zapoznawcza | Wersja zapoznawcza | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[Program PowerShell](qs-configure-powershell-windows-vm.md)<br>[Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)<br>[Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[Program PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Virtual Machine Scale Sets | Wersja zapoznawcza | Wersja zapoznawcza | [Azure Portal](qs-configure-portal-windows-vmss.md)<br>[Program PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vmss.md)<br>[Szablony usługi Azure Resource Manager](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[Program PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | Dostępna | Niedostępne | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interfejs wiersza polecenia platformy Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Szablon usługi Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[Program PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Stan usługi Funkcje Azure | Dostępna | Niedostępne | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interfejs wiersza polecenia platformy Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Szablon usługi Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[Program PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | Wersja zapoznawcza | Niedostępne | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[Program PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Azure API Management | Dostępna | Niedostępne | [Szablon usługi Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity) | 


## <a name="azure-services-that-support-azure-ad-authentication"></a>Usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD

Następujące usługi obsługuje uwierzytelnianie w usłudze Azure AD i zostały przetestowane za pomocą usługi klienta, które korzystają z zarządzanych tożsamości dla zasobów platformy Azure.

| Usługa | Identyfikator zasobu | Stan | Data | Przypisywanie dostępu |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Dostępna | Września 2017 r. | [Azure Portal](howto-assign-access-portal.md) <br>[Program PowerShell](howto-assign-access-powershell.md) <br>[Interfejs wiersza polecenia platformy Azure](howto-assign-access-CLI.md) |
| W usłudze Azure Key Vault | https://vault.azure.net | Dostępna | Września 2017 r. | |
| Azure Data Lake | https://datalake.azure.net/ | Dostępna | Września 2017 r. | |
| SQL Azure | https://database.windows.net/ | Dostępna | Października 2017 r. | |
| Azure Event Hubs | https://eventhubs.azure.net | Dostępna | Grudnia 2017 r. | |
| Azure Service Bus | https://servicebus.azure.net | Dostępna | Grudnia 2017 r. | |
| Azure Storage | https://storage.azure.com/ | Wersja zapoznawcza | Maj 2018 r. | |