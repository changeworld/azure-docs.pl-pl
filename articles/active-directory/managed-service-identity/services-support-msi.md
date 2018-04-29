---
title: Usług Azure, które obsługują zarządzane tożsamości usługi
description: Lista usług, które obsługują zarządzane usługi tożsamości i uwierzytelniania usługi Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 03/28/2018
ms.topic: reference
ms.service: active-directory
manager: mtillman
ms.openlocfilehash: c78ba7b6c48ffddd44ef35340e9eb69a3ed32f40
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="services-that-support-managed-service-identity"></a>Usługi obsługujące tożsamość usługi zarządzania 

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Za pomocą tożsamości zarządzanych, można uwierzytelniać do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD bez poświadczeń w kodzie. Trwa integrowanie uwierzytelniania MSI i Azure AD na platformie Azure. Sprawdź wstecz często dla aktualizacji.

## <a name="azure-services-that-support-managed-service-identity"></a>Usług Azure, które obsługują zarządzane tożsamości usługi

Następujących usług platformy Azure obsługuje zarządzane tożsamości usługi.

| Usługa | Stan | Date | Konfigurowanie | Uzyskaj token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Wersja zapoznawcza | 2017 września | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[Program PowerShell](qs-configure-powershell-windows-vm.md)<br>[Interfejs wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)<br>[Szablony usługi Azure Resource Manager](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[Program PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Wersja zapoznawcza | 2017 września | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Szablon usługi Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | Wersja zapoznawcza | 2017 września | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Szablon usługi Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Wersja zapoznawcza | 2017 listopada | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[Program PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure usługi uwierzytelniania pomocy technicznej usługi Azure AD

Następujące usługi obsługi uwierzytelniania usługi Azure AD i zostały przetestowane z klienta usług, które korzystają zarządzane tożsamości usługi.

| Usługa | Identyfikator zasobu | Stan | Date | Przypisywanie dostępu |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com | Dostępna | 2017 września | [Azure Portal](howto-assign-access-portal.md) <br>[Program PowerShell](howto-assign-access-powershell.md) <br>[Interfejs wiersza polecenia platformy Azure](howto-assign-access-CLI.md) |
| W usłudze Azure Key Vault | https://vault.azure.net | Dostępna | 2017 września | |
| Azure Data Lake | https://datalake.azure.net | Dostępna | 2017 września | |
| Azure SQL | https://database.windows.net | Dostępna | 2017 października | |
| Azure Event Hubs | https://eventhubs.azure.net | Dostępna | 2017 grudnia | |
| Azure Service Bus | https://servicebus.azure.net | Dostępna | 2017 grudnia | |