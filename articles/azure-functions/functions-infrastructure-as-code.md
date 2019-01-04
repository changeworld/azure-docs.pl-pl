---
title: Automatyzowanie wdrażania zasobów dla aplikacji funkcji w usłudze Azure Functions | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager, która wdraża aplikację funkcji.
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funkcje, architektura bezserwerowa, infrastruktury jako usługi kodu usługi azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 488b3797c7e18855a60b84a77a05e4e0a5654475
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023669"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatyzowanie wdrażania zasobów dla aplikacji funkcji w usłudze Azure Functions

Aby wdrożyć aplikację funkcji, można użyć szablonu usługi Azure Resource Manager. W tym artykule przedstawiono wymagane zasoby i parametry, aby to zrobić. Może być konieczne wdrażanie dodatkowych zasobów, w zależności od [wyzwalaczy i powiązań](functions-triggers-bindings.md) w aplikacji funkcji.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [tworzenia usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Aby uzyskać przykładowe szablony Zobacz:
- [Aplikacji funkcji w planie zużycie]
- [Aplikacja funkcji planu usługi Azure App Service]

## <a name="required-resources"></a>Wymagane zasoby

Aplikacja funkcji wymaga tych zasobów:

* [Usługi Azure Storage](../storage/index.yml) konta
* Plan hostingu (plan zużycie i plan usługi App Service)
* Aplikacja funkcji 

Aby uzyskać składnię JSON i właściwości dla tych zasobów Zobacz:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)

### <a name="storage-account"></a>Konto magazynu

Konto magazynu platformy Azure jest wymagana dla aplikacji funkcji. Wymagane jest konto ogólnego przeznaczenia, która obsługuje obiekty BLOB, tabel, kolejek i plików. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące konta magazynu usługi Azure Functions](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

Ponadto właściwość `AzureWebJobsStorage` muszą być określone jako ustawienia aplikacji w konfiguracji lokacji. Jeśli funkcja nie korzysta z usługi Application Insights do monitorowania, on powinny również określać `AzureWebJobsDashboard` jako ustawienia aplikacji.

Środowisko uruchomieniowe usługi Azure Functions używa `AzureWebJobsStorage` parametry połączenia do tworzenia wewnętrznego kolejek.  Jeśli nie włączono usługi Application Insights, środowisko wykonawcze używa `AzureWebJobsDashboard` parametry połączenia do logowania do usługi Azure Table storage i power **Monitor** karty w portalu.

Te właściwości są określone w `appSettings` kolekcji w `siteConfig` obiektu:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
```    

### <a name="hosting-plan"></a>Plan hostingu

Definicja planu hostingu różni się w zależności od tego, czy korzystasz z planu zużycia lub usługi App Service. Zobacz [wdrażanie aplikacji funkcji w planie zużycie](#consumption) i [wdrażanie aplikacji funkcji w planie usługi App Service](#app-service-plan).

### <a name="function-app"></a>Aplikacja funkcji

Zasób aplikacji funkcji jest definiowana za pomocą zasobu typu **Microsoft.Web/Site** i rodzaj **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Wdrażanie aplikacji funkcji w planie zużycie

Można uruchomić aplikacji funkcji w dwóch różnych trybach: plan zużycie i plan usługi App Service. Plan zużycie automatycznie przydziela moc obliczeniową, gdy kod jest uruchomiony, skaluje się, co jest niezbędne do obsługi obciążenia i następnie umożliwia skalowanie w dół, gdy kod nie jest uruchomiony. Tak nie trzeba płacić za bezczynnych maszyn wirtualnych i nie trzeba zarezerwować pojemności z wyprzedzeniem. Aby dowiedzieć się więcej o planach hostingu, zobacz [plany użycia funkcji platformy Azure i usługi App Service](functions-scale.md).

Aby uzyskać przykładowy szablon usługi Azure Resource Manager, zobacz [aplikacji funkcji w planie zużycie].

### <a name="create-a-consumption-plan"></a>Utwórz plan zużycie

Plan zużycie to specjalny rodzaj zasobu "farmy serwerów". Należy określić za pomocą `Dynamic` wartość `computeMode` i `sku` właściwości:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Ponadto planu zużycie wymaga dwa dodatkowe ustawienia w konfiguracji lokacji: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` i `WEBSITE_CONTENTSHARE`. Te właściwości skonfiguruj ścieżki konta i pliku magazynu przechowywania kod aplikacji funkcji i konfiguracji.

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Wdrażanie aplikacji funkcji w planie usługi App Service

W planie usługi App Service aplikacja funkcji zostanie uruchomiona na dedykowanych maszynach wirtualnych na podstawowa, standardowa i jednostki SKU Premium, podobne do aplikacji sieci web. Aby uzyskać szczegółowe informacje dotyczące sposobu działania planu usługi App Service, zobacz [szczegółowe omówienie planów usługi Azure App Service](../app-service/overview-hosting-plans.md). 

Aby uzyskać przykładowy szablon usługi Azure Resource Manager, zobacz [Aplikacja funkcji planu usługi Azure App Service].

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji 

Po wybraniu opcji skalowania, Utwórz aplikację funkcji. Aplikacja jest kontener, który zawiera wszystkie funkcje.

Aplikacja funkcji ma wielu zasobów podrzędnych, które można użyć w danym wdrożeniu, w tym ustawień aplikacji i różnych opcji kontroli źródła. Możesz również wybrać do usunięcia **sourcecontrols** zasobu podrzędnego i użyj innego [opcji wdrożenia](functions-continuous-deployment.md) zamiast tego.

> [!IMPORTANT]
> Aby pomyślnie wdrożyć aplikację za pomocą usługi Azure Resource Manager, ważne jest zrozumienie, jak zasoby są wdrażane na platformie Azure. W poniższym przykładzie konfiguracji najwyższego poziomu są stosowane przy użyciu **siteConfig**. Jest ważne, aby ustawić te konfiguracje na najwyższym poziomie, ponieważ ich przekazywania informacji do funkcji aparatu środowiska uruchomieniowego i wdrożenia. Najwyższego poziomu informacji jest wymagane przed elementem podrzędnym **sourcecontrols/sieci web** zasób stosowany. Chociaż istnieje możliwość skonfigurowania tych ustawień w poziomie podrzędnym **config/appSettings** zasobów, w niektórych przypadkach należy wdrożyć swoją aplikację funkcji *przed* **config/appSettings**  jest stosowany. Na przykład, w którym używasz funkcji z [Logic Apps](../logic-apps/index.yml), funkcje są zależność inny zasób.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Ten szablon używa [projektu](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) wartość ustawienia aplikacji, która ustawia podstawowego katalogu, w którym aparat wdrażania funkcji (Kudu) wyszukuje kodu do wdrożenia. W naszym repozytorium naszych funkcji znajdują się w podfolderze **src** folderu. Dlatego w poprzednim przykładzie, możemy ustawić wartość ustawienia aplikacji na `src`. Jeśli funkcji znajdują się w katalogu głównym repozytorium, lub jeśli nie są wdrażane z kontroli źródła, możesz usunąć tę wartość ustawienia aplikacji.

## <a name="deploy-your-template"></a>Wdrażanie szablonu

Można użyć dowolnej z następujących sposobów, aby wdrożyć szablon:

* [Program PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Interfejs wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [Interfejs API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Wdrażanie do przycisku platformy Azure

Zastąp ```<url-encoded-path-to-azuredeploy-json>``` z [zakodowane w adresie URL](https://www.bing.com/search?q=url+encode) wersję pierwotnych ścieżkę usługi `azuredeploy.json` pliku w usłudze GitHub.

Oto przykład, który używa języka znaczników markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Oto przykład, który korzysta z kodu HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o tym, jak tworzenie i konfigurowanie usługi Azure Functions.

* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Jak skonfigurować ustawienia aplikacji funkcji platformy Azure](functions-how-to-use-azure-function-app-settings.md)
* [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplikacji funkcji w planie zużycie]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplikacja funkcji planu usługi Azure App Service]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
