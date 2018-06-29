---
title: Usług Azure, które obsługują zarządzane tożsamości usługi
description: Lista usług, które obsługują zarządzane usługi tożsamości i uwierzytelniania usługi Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: reference
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: b39d5144eacfe6d621dae54da66b1306db516476
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060158"
---
# <a name="services-that-support-managed-service-identity"></a>Usługi obsługujące tożsamość usługi zarządzania 

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Za pomocą tożsamości zarządzanych, można uwierzytelniać do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD bez poświadczeń w kodzie. Trwa integrowanie uwierzytelniania MSI i Azure AD na platformie Azure. Sprawdź wstecz często dla aktualizacji.

## <a name="azure-services-that-support-managed-service-identity"></a>Usług Azure, które obsługują zarządzane tożsamości usługi

Następujących usług platformy Azure obsługuje zarządzane tożsamości usługi.

| Usługa | Stan | Date | Konfigurowanie | Uzyskaj token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Wersja zapoznawcza | 2017 września | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[Program PowerShell](qs-configure-powershell-windows-vm.md)<br>[Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)<br>[Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[Program PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Dostępne | 2018 czerwca | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interfejs wiersza polecenia platformy Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Szablon usługi Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[Program PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | Dostępne | 2018 czerwca | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interfejs wiersza polecenia platformy Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Szablon usługi Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[Program PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | Wersja zapoznawcza | 2017 listopada | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[Program PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure usługi uwierzytelniania pomocy technicznej usługi Azure AD

Następujące usługi obsługi uwierzytelniania usługi Azure AD i zostały przetestowane z klienta usług, które korzystają zarządzane tożsamości usługi.

| Usługa | Identyfikator zasobu | Stan | Date | Przypisywanie dostępu |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Dostępne | 2017 września | [Azure Portal](howto-assign-access-portal.md) <br>[Program PowerShell](howto-assign-access-powershell.md) <br>[Interfejs wiersza polecenia platformy Azure](howto-assign-access-CLI.md) |
| W usłudze Azure Key Vault | https://vault.azure.net | Dostępne | 2017 września | |
| Azure Data Lake | https://datalake.azure.net/ | Dostępne | 2017 września | |
| Azure SQL | https://database.windows.net/ | Dostępne | 2017 października | |
| Azure Event Hubs | https://eventhubs.azure.net | Dostępne | 2017 grudnia | |
| Azure Service Bus | https://servicebus.azure.net | Dostępne | 2017 grudnia | |
| Azure Storage | https://storage.azure.com/ | Wersja zapoznawcza | Maj 2018 r. | |
