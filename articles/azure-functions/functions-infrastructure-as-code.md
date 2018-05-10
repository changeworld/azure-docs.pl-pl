---
title: Automatyzacji wdrażania zasobów dla aplikacji funkcji w funkcji Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager, która wdraża aplikację funkcji.
services: Functions
documtationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure funkcje, funkcje, niekorzystającą architektury infrastruktury jako usługi kodu usługi azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 28b2f5aba69e5c058feb7119eb31352220922998
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatyzacji wdrażania zasobów dla aplikacji funkcja na usługi Azure Functions

Szablonu usługi Azure Resource Manager umożliwia wdrażanie aplikacji funkcji. W tym artykule opisano wymaganych zasobów i parametrów w ten sposób. Może być konieczne wdrażanie dodatkowych zasobów, w zależności od [wyzwalaczy i powiązań](functions-triggers-bindings.md) w funkcji aplikacji.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [szablonów Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Na przykład szablony Zobacz:
- [funkcji aplikacji w planie zużycie]
- [funkcji aplikacji w planie usługi aplikacji Azure]

## <a name="required-resources"></a>Wymagane zasoby

Aplikacja funkcji wymaga tych zasobów:

* [Usługi Azure Storage](../storage/index.yml) konta
* Plan hostingu (użycie planu lub plan usługi aplikacji)
* Aplikacja — funkcja 

### <a name="storage-account"></a>Konto magazynu

Konto magazynu platformy Azure jest wymagany dla funkcji aplikacji. Musisz mieć konto ogólnego przeznaczenia, który obsługuje obiekty BLOB, tabel, kolejek i plików. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące konta magazynu usługi Azure Functions](functions-create-function-app-portal.md#storage-account-requirements).

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

Ponadto właściwość `AzureWebJobsStorage` muszą być określone jako ustawienie aplikacji w konfiguracji lokacji. Jeśli funkcja aplikacji nie korzysta z usługi Application Insights do monitorowania, powinien on także zawierać `AzureWebJobsDashboard` jako ustawienie aplikacji.

Środowisko wykonawcze usługi Azure Functions używa `AzureWebJobsStorage` ciągu połączenia służącego do tworzenia kolejek wewnętrznych.  W przypadku usługi Application Insights nie jest włączone, używa środowiska wykonawczego `AzureWebJobsDashboard` parametry połączenia do logowania do magazynu tabel Azure i zasilania **Monitor** kartę w portalu.

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

Definicję planu obsługi może być różna w zależności od tego, czy używasz planu zużycia lub usługi aplikacji. Zobacz [wdrażanie aplikacji funkcji w planie zużycie](#consumption) i [wdrażanie aplikacji na plan usługi aplikacji — funkcja](#app-service-plan).

### <a name="function-app"></a>Aplikacja funkcji

Funkcja zasób aplikacji jest definiowana za pomocą zasobu typu **Microsoft.Web/Site** i rodzaj **functionapp**:

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

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Wdrażanie aplikacji w planie zużycia — funkcja

Można uruchomić aplikacji funkcji w dwóch różnych trybach: plan zużycia i plan usługi aplikacji. Plan zużycie automatycznie przydzieli moc obliczeniową, gdy kod działa, skaluje się wymagane do obsługi obciążenia, a następnie skalowany w dół, gdy kodu nie jest uruchomiona. Tak nie trzeba płacić za maszyny wirtualne w stanie bezczynności, a nie trzeba wydajność rezerwowa z wyprzedzeniem. Aby dowiedzieć się więcej o hostingu planów, zobacz [planów Azure — użytek funkcje i usługi aplikacji](functions-scale.md).

Aby uzyskać przykładowy szablon usługi Azure Resource Manager, zobacz [funkcji aplikacji w planie zużycie].

### <a name="create-a-consumption-plan"></a>Tworzenie planu zużycie

Plan zużycie jest specjalny typ zasobu "serverfarm". Należy określić przy użyciu `Dynamic` wartość `computeMode` i `sku` właściwości:

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

Ponadto planu zużycie wymaga dwóch dodatkowych ustawień konfiguracji witryny: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` i `WEBSITE_CONTENTSHARE`. Te właściwości skonfigurować ścieżkę konta i pliku magazynu przechowywania kod aplikacji funkcji i konfiguracji.

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

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Wdrażanie aplikacji na plan usługi aplikacji — funkcja

W planie usługi aplikacji — Aplikacja funkcja będzie działać na dedykowanych maszynach wirtualnych na podstawowa, standardowa i Premium SKU, podobnie jak aplikacje sieci web. Aby uzyskać szczegółowe informacje dotyczące sposobu działania plan usługi aplikacji, zobacz [szczegółowe omówienie planów usługi aplikacji Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Aby uzyskać przykładowy szablon usługi Azure Resource Manager, zobacz [funkcji aplikacji w planie usługi aplikacji Azure].

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

Po wybraniu opcji skalowania tworzenia aplikacji funkcji. Aplikacja jest kontener, który zawiera wszystkie funkcje.

Aplikacja funkcji ma wiele zasoby podrzędne używane w danym wdrożeniu, w tym ustawienia aplikacji i opcje kontroli źródła. Możesz również wybrać do usunięcia **sourcecontrols** zasobu podrzędnego i użyj innego [opcji wdrażania](functions-continuous-deployment.md) zamiast tego.

> [!IMPORTANT]
> Pomyślnie wdrażania aplikacji za pomocą usługi Azure Resource Manager, ważne jest zrozumienie, jak zasoby są wdrażane na platformie Azure. W poniższym przykładzie najwyższego poziomu konfiguracji są stosowane przy użyciu **siteConfig**. Jest ważne, aby ustawić te konfiguracje na najwyższym poziomie, ponieważ ich przekazywania informacji do funkcji aparatu środowiska uruchomieniowego i wdrożenia. Informacje o najwyższego poziomu jest wymagana przed elementem podrzędnym **sourcecontrols/sieci web** zasobów są stosowane. Chociaż można skonfigurować te ustawienia w poziomie podrzędnym **config/appSettings** zasobów, w niektórych przypadkach należy wdrożyć aplikację funkcja *przed* **config/appSettings** została zastosowana. Na przykład w przypadku używania funkcji z [Logic Apps](../logic-apps/index.yml), funkcji są zależność innego zasobu.

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
> Ten szablon używa [projektu](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) wartość ustawienia aplikacji, która ustawia podstawowego katalogu, w którym aparat wdrażania funkcji (Kudu) wyszukuje kodu do wdrożenia. W naszym repozytorium naszej funkcji znajdują się w podfolderze **src** folderu. Tak, w tym przykładzie firma Microsoft ustawioną wartość ustawienia aplikacji `src`. Jeśli funkcji znajdują się w folderze głównym repozytorium, lub jeśli nie są wdrażane z kontroli źródła, możesz usunąć tę wartość ustawienia aplikacji.

## <a name="deploy-your-template"></a>Wdrażanie szablonu

Można użyć dowolnej z poniższych metod wdrażania szablonu:

* [Program PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Interfejs wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [Interfejs API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Wdrażanie na przycisku Azure

Zastąp ```<url-encoded-path-to-azuredeploy-json>``` z [zakodowane w adresie URL](https://www.bing.com/search?q=url+encode) wersji nieprzetworzona ścieżka Twojej `azuredeploy.json` pliku w witrynie GitHub.

Oto przykład, który używa języka znaczników markdown:

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Oto przykład, który używa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o tworzeniu i konfigurowanie usługi Azure Functions.

* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Jak skonfigurować ustawienia aplikacji Azure — funkcja](functions-how-to-use-azure-function-app-settings.md)
* [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[funkcji aplikacji w planie zużycie]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[funkcji aplikacji w planie usługi aplikacji Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
