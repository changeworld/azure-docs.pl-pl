---
title: How to troubleshoot Azure Functions Runtime is unreachable.
description: Learn how to troubleshoot an invalid storage account.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 212f10bd33479e5a9f7244d5b2090c0324f937c2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226759"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>How to troubleshoot "functions runtime is unreachable"


## <a name="error-text"></a>Error text
This doc is intended to troubleshoot the following error when displayed in the Functions portal.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Podsumowanie
This issue occurs when the Azure Functions Runtime cannot start. The most common reason for this error to occur is the function app losing access to its storage account. [Read more about the storage account requirements here](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Rozwiązywanie problemów
We'll walk through the four most common error cases, how to identify, and how to resolve each case.

1. Storage Account deleted
1. Storage Account application settings deleted
1. Storage Account credentials invalid
1. Storage Account Inaccessible
1. Daily Execution Quota Full

## <a name="storage-account-deleted"></a>Storage account deleted

Every function app requires a storage account to operate. If that account is deleted your Function will not work.

### <a name="how-to-find-your-storage-account"></a>How to find your storage account

Start by looking up your storage account name in your Application Settings. Either `AzureWebJobsStorage` or `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` will contain the name of your storage account wrapped up in a connection string. Read more specifics at the [application setting reference here](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Search for your storage account in the Azure portal to see if it still exists. If it has been deleted, you will need to recreate a storage account and replace your storage connection strings. Your function code is lost and you will need to redeploy it again.

## <a name="storage-account-application-settings-deleted"></a>Storage account application settings deleted

In the previous step, if you did not have a storage account connection string they were likely deleted or overwritten. Deleting app settings is most commonly done when using deployment slots or Azure Resource Manager scripts to set application settings.

### <a name="required-application-settings"></a>Required application settings

* Wymagane
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Required for Consumption Plan Functions
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Read about these application settings here](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Wskazówka

* Do not check "slot setting" for any of these settings. When you swap deployment slots the Function will break.
* Do not modify these settings as part of automated deployments.
* These settings must be provided and valid at creation time. An automated deployment that does not contain these settings will result in a non-functional App, even if the settings are added after the fact.

## <a name="storage-account-credentials-invalid"></a>Storage account credentials invalid

The above Storage Account connection strings must be updated if you regenerate storage keys. [Read more about storage key management here](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Storage account inaccessible

Your Function App must be able to access the storage account. Common issues that block a Functions access to a storage account are:

* Function Apps deployed to App Service Environments without the correct network rules to allow traffic to and from the storage account
* The storage account firewall is enabled and not configured to allow traffic to and from Functions. [Read more about storage account firewall configuration here](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Daily Execution Quota Full

If you have a Daily Execution Quota configured, your Function App will be temporarily disabled and many of the portal controls will become unavailable. 

* To verify, check open Platform Features > Function App Settings in the portal. You will see the following message if you are over quota
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Remove the quota and restart your app to resolve the issue.

## <a name="next-steps"></a>Następne kroki

Now that your Function App is back and operational take a look at our quickstarts and developer references to get up and running again!

* [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-azure-function.md)  
  Od razu utwórz swoją pierwszą funkcję przy użyciu opcji szybkiego startu usługi Azure Functions. 
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)  
  Dalsze informacje techniczne na temat środowiska uruchomieniowego usługi Azure Functions, a także dokumentacja dotycząca kodowania funkcji oraz definiowania wyzwalaczy i powiązań.
* [Testowanie usługi Azure Functions](functions-test-a-function.md)  
  Opis różnych narzędzi i technik testowania funkcji.
* [Jak skalować usługę Azure Functions](functions-scale.md)  
  Omówienie planów usług dostępnych w środowisku Azure Functions, w tym planu hostingowego zużycia, oraz sposobu wybierania właściwego planu. 
* [Dowiedz się więcej o usłudze Azure App Service](../app-service/overview.md)  
  Środowisko Azure Functions korzysta z platformy Azure App Service na potrzeby funkcji podstawowych, takich jak wdrożenia, zmienne środowiskowe i diagnostyka. 
