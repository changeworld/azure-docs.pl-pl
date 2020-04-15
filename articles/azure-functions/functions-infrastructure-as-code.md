---
title: Automatyzacja wdrażania zasobów aplikacji funkcji na platformie Azure
description: Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager, który wdraża aplikację funkcji.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 7155a3fa9481ef5f2da62d85d4a932ad5e8e8ab1
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382521"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatyzacja wdrażania zasobów dla aplikacji funkcji w usłudze Azure Functions

Za pomocą szablonu usługi Azure Resource Manager można wdrożyć aplikację funkcji. W tym artykule opisano wymagane zasoby i parametry w tym celu. Może być konieczne wdrożenie dodatkowych zasobów, w zależności od [wyzwalaczy i powiązań](functions-triggers-bindings.md) w aplikacji funkcji.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Przykładowe szablony można znaleźć w:
- [Aplikacja funkcyjna na planie zużycia]
- [Aplikacja funkcji w planie usługi Azure App Service]

## <a name="required-resources"></a>Wymagane zasoby

Wdrożenie usługi Azure Functions zazwyczaj składa się z tych zasobów:

| Zasób                                                                           | Wymaganie | Odwołanie do składni i właściwości                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Aplikacja funkcji                                                                     | Wymagany    | [Microsoft.Web/witryny](/azure/templates/microsoft.web/sites)                             |   |
| Konto [usługi Azure Storage](../storage/index.yml)                                   | Wymagany    | [Microsoft.Storage/storageKondyje](/azure/templates/microsoft.storage/storageaccounts) |   |
| Składnik [Usługi Application Insights](../azure-monitor/app/app-insights-overview.md) | Optional (Opcjonalność)    | [Microsoft.Insights/składniki](/azure/templates/microsoft.insights/components)         |   |
| [Plan hostingowy](./functions-scale.md)                                             | Opcjonalnie<sup>1</sup>    | [Microsoft.Web/farmy serwerów](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1.</sup> Plan hostingowy jest wymagany tylko wtedy, gdy zdecydujesz się uruchomić aplikację funkcyjną w [planie Premium](./functions-premium-plan.md) lub w planie usługi [App Service.](../app-service/overview-hosting-plans.md)

> [!TIP]
> Chociaż nie jest to wymagane, zdecydowanie zaleca się skonfigurowanie usługi Application Insights dla aplikacji.

<a name="storage"></a>
### <a name="storage-account"></a>Konto magazynu

Konto magazynu platformy Azure jest wymagane dla aplikacji funkcji. Potrzebne jest konto ogólnego przeznaczenia, które obsługuje obiekty BLOB, tabele, kolejki i pliki. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące konta usługi Azure Functions storage](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Ponadto właściwość `AzureWebJobsStorage` musi być określona jako ustawienie aplikacji w konfiguracji lokacji. Jeśli aplikacja funkcji nie używa usługi Application Insights do `AzureWebJobsDashboard` monitorowania, należy również określić jako ustawienie aplikacji.

Środowisko uruchomieniowe usługi `AzureWebJobsStorage` Azure Functions używa ciągu połączenia do tworzenia kolejek wewnętrznych.  Gdy usługa Application Insights nie jest włączona, środowisko wykonawcze używa ciągu `AzureWebJobsDashboard` połączenia do logowania się do magazynu tabel platformy Azure i zasilania karty **Monitor** w portalu.

Te właściwości są określone `appSettings` w `siteConfig` kolekcji w obiekcie:

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

Usługa Application Insights jest zalecana do monitorowania aplikacji funkcyjnych. Zasób usługi Application Insights jest definiowany za pomocą typu **Microsoft.Insights/components** i **rodzaju sieci Web:**

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
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Ponadto klucz instrumentacji musi być dostarczony do aplikacji `APPINSIGHTS_INSTRUMENTATIONKEY` funkcji przy użyciu ustawienia aplikacji. Ta właściwość jest `appSettings` określona `siteConfig` w kolekcji w obiekcie:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plan hostingu

Definicja planu hostingowego jest różna i może być jedną z następujących elementów:
* [Plan zużycia](#consumption) (domyślnie)
* [Plan Premium](#premium)
* [Plan usługi aplikacji](#app-service-plan)

### <a name="function-app"></a>Aplikacja funkcji

Zasób aplikacji funkcji jest definiowany przy użyciu zasobu typu **Microsoft.Web/sites** i kind **functionapp:**

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
}
```

> [!IMPORTANT]
> Jeśli jawnie definiujesz plan hostingu, dodatkowy element będzie potrzebny w tablicy dependsOn:`"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Aplikacja funkcji musi zawierać następujące ustawienia aplikacji:

| Nazwa ustawienia                 | Opis                                                                               | Przykładowe wartości                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Parametry połączenia z kontem magazynu używanym przez środowisko wykonawcze Functions do kolejkowania wewnętrznego | Zobacz [konto magazynu](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Wersja środowiska wykonawczego usługi Azure Functions                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Stos języka, który ma być używany do funkcji w tej aplikacji                                   | `dotnet`, `node` `java`, `python`, , lub`powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Jest to konieczne `node` tylko w przypadku korzystania ze stosu języków, określa wersję, której chcesz użyć              | `10.14.1`                             |

Te właściwości są określone `appSettings` w `siteConfig` kolekcji we właściwości:

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

## <a name="deploy-on-consumption-plan"></a>Wdrażanie w planie zużycia

Plan zużycia automatycznie przydziela moc obliczeniową, gdy kod jest uruchomiony, skaluje się w poziomie niezbędnym do obsługi obciążenia, a następnie skaluje się, gdy kod nie jest uruchomiony. Nie musisz płacić za bezczynne maszyny wirtualne i nie musisz rezerwować zdolności produkcyjnych z wyprzedzeniem. Aby dowiedzieć się więcej, zobacz [Skala i hosting usług Azure Functions](functions-scale.md#consumption-plan).

Aby uzyskać przykładowy szablon Usługi Azure Resource Manager, zobacz [Aplikacja funkcji w planie zużycia].

### <a name="create-a-consumption-plan"></a>Tworzenie planu zużycia

Plan zużycia nie musi być zdefiniowany. Jeden zostanie automatycznie utworzony lub wybrany na podstawie regionu podczas tworzenia samego zasobu aplikacji funkcji.

Plan zużycia jest specjalnym typem zasobu "serverfarm". W systemie Windows można go `Dynamic` określić `computeMode` przy `sku` użyciu wartości i właściwości:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Plan zużycia nie może być jawnie zdefiniowany dla systemu Linux. Zostanie utworzony automatycznie.

Jeśli jawnie zdefiniujesz plan zużycia, należy `serverFarmId` ustawić właściwość w aplikacji, tak aby wskazuje identyfikator zasobu planu. Należy upewnić się, że `dependsOn` aplikacja funkcji ma ustawienie dla planu, jak również.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

#### <a name="windows"></a>Windows

W systemie Windows plan zużycia wymaga dwóch dodatkowych `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` `WEBSITE_CONTENTSHARE`ustawień w konfiguracji lokacji: i . Te właściwości skonfigurować konto magazynu i ścieżkę pliku, gdzie kod aplikacji funkcji i konfiguracji są przechowywane.

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

W systemie Linux aplikacja funkcji `kind` musi `functionapp,linux`mieć ustawioną `reserved` na , `true`i musi mieć właściwość ustawioną na:

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

## <a name="deploy-on-premium-plan"></a>Wdrażanie w planie Premium

Plan Premium oferuje takie samo skalowanie jak plan zużycia, ale zawiera dedykowane zasoby i dodatkowe możliwości. Aby dowiedzieć się więcej, zobacz [Azure Functions Premium Plan](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Tworzenie planu Premium

Plan Premium jest specjalnym typem zasobu "serverfarm". Można go określić za `EP1` `EP2`pomocą `EP3` opcji `Name` , lub `sku` wartości właściwości w [obiekcie opisu](https://docs.microsoft.com/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Aplikacja funkcji na planie Premium `serverFarmId` musi mieć właściwość ustawioną na identyfikator zasobu planu utworzonego wcześniej. Ponadto plan Premium wymaga dwóch dodatkowych ustawień w `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` `WEBSITE_CONTENTSHARE`konfiguracji witryny: i . Te właściwości skonfigurować konto magazynu i ścieżkę pliku, gdzie kod aplikacji funkcji i konfiguracji są przechowywane.

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

## <a name="deploy-on-app-service-plan"></a>Wdrażanie w planie usługi aplikacji

W planie usługi app service aplikacja funkcji działa na dedykowanych maszynach wirtualnych na jednostkach SKU basic, Standard i Premium, podobnie jak aplikacje sieci web. Aby uzyskać szczegółowe informacje na temat działania planu usługi app service, zobacz [szczegółowe omówienie planów usługi Azure App Service.](../app-service/overview-hosting-plans.md)

Aby uzyskać przykładowy szablon usługi Azure Resource Manager, zobacz [Aplikacja funkcji w planie usługi Azure App Service].

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

Plan usługi aplikacji jest zdefiniowany przez zasób "farma serwerowa".

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Aby uruchomić aplikację na `kind` Linuksie, `Linux`należy również ustawić na:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Aplikacja funkcji w planie usługi app `serverFarmId` service musi mieć właściwość ustawioną na identyfikator zasobu planu utworzonego wcześniej.

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

Aplikacje systemu Linux `linuxFxVersion` powinny `siteConfig`również zawierać właściwość w obszarze . Jeśli po prostu wdrażasz kod, wartość tego jest określana przez żądany stos środowiska wykonawczego:

| Stos            | Przykładowa wartość                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
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

W przypadku [wdrażania niestandardowego obrazu kontenera](./functions-create-function-linux-custom-image.md)należy `linuxFxVersion` określić go za pomocą konfiguracji umożliwiającej pobranie obrazu, tak jak w [aplikacji Web App for Containers](/azure/app-service/containers). Ponadto, `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustaw `false`, ponieważ zawartość aplikacji znajduje się w samym kontenerze:

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

Aplikacja funkcji ma wiele zasobów podrzędnych, które można użyć w wdrożeniu, w tym ustawienia aplikacji i opcje kontroli źródła. Można również usunąć **źródłokontroli podrzędnego** zasobu i zamiast tego użyć innej [opcji wdrażania.](functions-continuous-deployment.md)

> [!IMPORTANT]
> Aby pomyślnie wdrożyć aplikację przy użyciu usługi Azure Resource Manager, ważne jest, aby zrozumieć, jak zasoby są wdrażane na platformie Azure. W poniższym przykładzie konfiguracje najwyższego poziomu są stosowane przy użyciu **siteConfig**. Ważne jest, aby ustawić te konfiguracje na najwyższym poziomie, ponieważ przekazują informacje do środowiska uruchomieniowego funkcji i aparatu wdrażania. Informacje najwyższego poziomu są wymagane przed zastosowaniem **podrzędnych źródełkontroli/zasobu sieci web.** Chociaż jest możliwe, aby skonfigurować te ustawienia w zasobie **config/appSettings** na poziomie podrzędnym, w niektórych przypadkach aplikacja funkcji musi zostać wdrożona *przed* **zastosowaniem config/appSettings.** Na przykład podczas korzystania z funkcji z [aplikacjami logiki,](../logic-apps/index.yml)funkcje są zależnością innego zasobu.

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
> Ten szablon używa wartości ustawień aplikacji [projektu,](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) która ustawia katalog podstawowy, w którym aparat wdrażania funkcji (Kudu) szuka kodu do wdrożenia. W naszym repozytorium nasze funkcje znajdują się w podfolderze folderu **src.** Tak więc w poprzednim przykładzie ustawiamy wartość `src`ustawień aplikacji na . Jeśli funkcje znajdują się w katalogu głównym repozytorium lub jeśli nie wdrażasz z kontroli źródła, możesz usunąć tę wartość ustawień aplikacji.

## <a name="deploy-your-template"></a>Wdrażanie szablonu

Można użyć dowolnego z następujących sposobów wdrożenia szablonu:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)
* [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
* [Interfejs API REST](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Przycisk Wdróż na platformie Azure

Zastąp ```<url-encoded-path-to-azuredeploy-json>``` wersją pliku [zakodowaną w adresie URL](https://www.bing.com/search?q=url+encode) w usłudze `azuredeploy.json` GitHub.

Oto przykład, który używa znaczników:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Oto przykład, który używa html:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Następujące polecenia programu PowerShell tworzą grupę zasobów i wdrażają szablon, który tworzy aplikację funkcji z wymaganymi zasobami. Aby uruchomić lokalnie, musisz mieć zainstalowany [program Azure PowerShell.](/powershell/azure/install-az-ps) Uruchom, [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) aby się zalogować.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Aby przetestować to wdrożenie, można użyć [szablonu takiego jak ten,](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) który tworzy aplikację funkcji w systemie Windows w planie zużycia. Zamień `<function-app-name>` unikatową nazwę aplikacji funkcyjnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o opracowywaniu i konfigurowaniu funkcji platformy Azure.

* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Jak skonfigurować ustawienia aplikacji funkcji platformy Azure](functions-how-to-use-azure-function-app-settings.md)
* [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplikacja funkcyjna na planie zużycia]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplikacja funkcji w planie usługi Azure App Service]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
