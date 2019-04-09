---
title: Automatyzowanie wdrażania zasobów dla aplikacji funkcji w usłudze Azure Functions | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager, która wdraża aplikację funkcji.
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funkcje, architektura bezserwerowa, infrastruktury jako usługi kodu usługi azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 5d028768c062ef7df74d48f83ccc4e27a506f1ac
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270907"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatyzowanie wdrażania zasobów dla aplikacji funkcji w usłudze Azure Functions

Aby wdrożyć aplikację funkcji, można użyć szablonu usługi Azure Resource Manager. W tym artykule przedstawiono wymagane zasoby i parametry, aby to zrobić. Może być konieczne wdrażanie dodatkowych zasobów, w zależności od [wyzwalaczy i powiązań](functions-triggers-bindings.md) w aplikacji funkcji.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [tworzenia usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Aby uzyskać przykładowe szablony Zobacz:
- [Aplikacji funkcji w planie zużycie]
- [Aplikacja funkcji planu usługi Azure App Service]

> [!NOTE]
> Plan w warstwie Premium do obsługi usługi Azure Functions jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [planu usługi Azure Functions w wersji Premium](functions-premium-plan.md).

## <a name="required-resources"></a>Wymagane zasoby

Wdrożenie usługi Azure Functions zwykle składa się z tymi zasobami:

| Zasób                                                                           | Wymaganie | Dokumentacja składni i właściwości                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Aplikacja funkcji                                                                     | Wymagane    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| [Usługi Azure Storage](../storage/index.yml) konta                                   | Wymagane    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| [Usługi Application Insights](../azure-monitor/app/app-insights-overview.md) składnika | Optional (Opcjonalność)    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| A [plan hostingu](./functions-scale.md)                                             | Opcjonalnie<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>planu hostingu jest tylko wymagany, gdy użytkownik chce uruchomić swoją aplikację funkcji na [plan w warstwie Premium](./functions-premium-plan.md) (w wersji zapoznawczej) lub na [planu usługi App Service](../app-service/overview-hosting-plans.md).

> [!TIP]
> Chociaż nie jest to wymagane, zdecydowanie zaleca się konfigurowania usługi Application Insights dla aplikacji.

<a name="storage"></a>
### <a name="storage-account"></a>Konto magazynu

Konto magazynu platformy Azure jest wymagana dla aplikacji funkcji. Wymagane jest konto ogólnego przeznaczenia, która obsługuje obiekty BLOB, tabel, kolejek i plików. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące konta magazynu usługi Azure Functions](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
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
]
```

### <a name="application-insights"></a>Application Insights

Usługa Application Insights jest zalecana w przypadku monitorowania aplikacji funkcji. Zasób usługi Application Insights jest zdefiniowana z typem **Microsoft.Insights/components** i rodzaj **web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

Ponadto klucz Instrumentacji musi zostać dostarczona do aplikacji funkcji przy użyciu `APPINSIGHTS_INSTRUMENTATIONKEY` ustawienia aplikacji. Ta właściwość jest określona w `appSettings` kolekcji w `siteConfig` obiektu:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plan hostingu

Definicja planu hostingu różni się i może być jedną z następujących czynności:
* [Plan zużycie](#consumption) (ustawienie domyślne)
* [Plan w warstwie Premium](#premium) (w wersji zapoznawczej)
* [Plan usługi App Service](#app-service-plan)

### <a name="function-app"></a>Aplikacja funkcji

Zasób aplikacji funkcji jest definiowana za pomocą zasobu typu **Microsoft.Web/sites** i rodzaj **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
```

> [!IMPORTANT]
> Jeżeli są jawnie definiowane planu hostingu, potrzebne dodatkowe elementu w tablicy dependsOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Aplikacja funkcji musi zawierać te ustawienia aplikacji:

| Nazwa ustawienia                 | Opis                                                                               | Przykładowe wartości                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Parametry połączenia do magazynu konta, które środowisko uruchomieniowe usługi Functions dla wewnętrznej kolejki | Zobacz [konta magazynu](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Wersja środowiska uruchomieniowego usługi Azure Functions                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Stos języka, który ma być używany dla funkcji w tej aplikacji                                   | `dotnet`, `node`, `java`, lub `python` |
| WEBSITE_NODE_DEFAULT_VERSION | Wymagane tylko, jeśli za pomocą `node` stos języka Określa wersję do użycia              | `10.14.1`                             |

Te właściwości są określone w `appSettings` kolekcji w `siteConfig` właściwości:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Wdrażanie na plan zużycie

Plan zużycie automatycznie przydziela moc obliczeniową, gdy kod jest uruchomiony, skaluje się, co jest niezbędne do obsługi obciążenia i następnie umożliwia skalowanie w dół, gdy kod nie jest uruchomiony. Nie trzeba płacić za bezczynnych maszyn wirtualnych i nie trzeba zarezerwować pojemności z wyprzedzeniem. Aby dowiedzieć się więcej, zobacz [usługi Azure Functions, skalowanie i hosting](functions-scale.md#consumption-plan).

Aby uzyskać przykładowy szablon usługi Azure Resource Manager, zobacz [aplikacji funkcji w planie zużycie].

### <a name="create-a-consumption-plan"></a>Utwórz plan zużycie

Planu zużycie nie musi być zdefiniowany. Jeden będzie można utworzony automatycznie lub wybrane na podstawie regionu po utworzeniu samego zasobu aplikacji funkcji.

Plan zużycie to specjalny rodzaj zasobu "farmy serwerów". Dla Windows, możesz je określić za pomocą `Dynamic` wartość `computeMode` i `sku` właściwości:

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

> [!NOTE]
> Plan zużycie nie można jawnie zdefiniowany dla systemu Linux. Zostanie on utworzony automatycznie.

Jeśli zdefiniujesz jawnie planie zużycie, musisz ustawić `serverFarmId` właściwości aplikacji, tak że wskazuje identyfikator zasobu planu. Należy upewnić się, że aplikacja funkcji ma `dependsOn` ustawienie dla tego planu, jak również.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

#### <a name="windows"></a>Windows

W Windows, plan zużycie są wymagane dwa dodatkowe ustawienia w konfiguracji lokacji: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` i `WEBSITE_CONTENTSHARE`. Te właściwości skonfiguruj ścieżki konta i pliku magazynu przechowywania kod aplikacji funkcji i konfiguracji.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
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
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

W systemie Linux, aplikacja funkcji musi mieć jej `kind` równa `functionapp,linux`, i musi mieć `reserved` właściwością `true`:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Wdrażanie na plan w warstwie Premium

Plan w warstwie Premium oferuje skalę tego samego planu zużycie, ale obejmuje dodatkowe funkcje i dedykowanych zasobów. Aby dowiedzieć się więcej, zobacz [usługi Azure Functions planu w warstwie Premium (wersja zapoznawcza)](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Utwórz plan w warstwie Premium

Plan w warstwie Premium to specjalny rodzaj zasobu "farmy serwerów". Możesz je określić za pomocą `EP1`, `EP2`, lub `EP3` dla `sku` wartości właściwości.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Musi mieć aplikację funkcji w ramach planu Premium `serverFarmId` właściwość jest ustawiona na identyfikator zasobu planu utworzone wcześniej. Ponadto plan w warstwie Premium wymaga dwa dodatkowe ustawienia w konfiguracji lokacji: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` i `WEBSITE_CONTENTSHARE`. Te właściwości skonfiguruj ścieżki konta i pliku magazynu przechowywania kod aplikacji funkcji i konfiguracji.

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a> 

## <a name="deploy-on-app-service-plan"></a>Wdrażanie na plan usługi App Service

W planie usługi App Service aplikacja funkcji zostanie uruchomiona na dedykowanych maszynach wirtualnych na podstawowa, standardowa i jednostki SKU Premium, podobne do aplikacji sieci web. Aby uzyskać szczegółowe informacje dotyczące sposobu działania planu usługi App Service, zobacz [szczegółowe omówienie planów usługi Azure App Service](../app-service/overview-hosting-plans.md).

Aby uzyskać przykładowy szablon usługi Azure Resource Manager, zobacz [aplikacji funkcji w usłudze Azure App Service plan].

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

Plan usługi App Service jest definiowany przez zasób "farmy serwerów".

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

Aby uruchomić aplikację w systemie Linux, należy także ustawić `kind` do `Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
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

Aplikację funkcji w planie usługi App Service musi mieć `serverFarmId` właściwość jest ustawiona na identyfikator zasobu planu utworzone wcześniej.

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Aplikacje systemu Linux powinny również obejmować `linuxFxVersion` właściwości `siteConfig`. Jeśli po prostu wdrażasz kod, wartość ta jest określana przez stos środowiska uruchomieniowego żądane:

| Stos            | Przykładowa wartość                                         |
|------------------|-------------------------------------------------------|
| Python (wersja zapoznawcza) | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Jeśli jesteś [wdrażanie obrazu niestandardowego kontenera](./functions-create-function-linux-custom-image.md), należy określić go za pomocą `linuxFxVersion` i obejmują konfigurację, która umożliwia obraz zostać pobrane w [Web App for Containers](/azure/app-service/containers). Ponadto należy ustawić `WEBSITES_ENABLE_APP_SERVICE_STORAGE` do `false`, ponieważ zawartość aplikacji znajduje się w samej kontenera:

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Dostosowywanie wdrożenia

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
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
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
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
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

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
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
