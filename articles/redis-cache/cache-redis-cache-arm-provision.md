---
title: Zapewnij pamięci podręcznej Redis przy użyciu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Wdrażanie pamięć podręczna Redis Azure za pomocą szablonu usługi Azure Resource Manager.
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: b26116b974abbfe410b0a6ebc0186d73f4eea1bf
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910745"
---
# <a name="create-a-redis-cache-using-a-template"></a>Tworzenie pamięci podręcznej Redis przy użyciu szablonu
W tym temacie dowiesz sposób tworzenia szablonu usługi Azure Resource Manager, która wdraża pamięć podręczna Redis Azure. Pamięć podręczna może służyć przy użyciu istniejącego konta magazynu do przechowywania danych diagnostycznych. Dowiesz się również, jak do definiowania zasobów, do których są wdrażane i sposób definiowania parametrów, które są określone, gdy wdrożenie jest wykonywane. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Obecnie ustawień diagnostycznych są wspólne dla wszystkich usług pamięć podręczna w tym samym regionie, w ramach subskrypcji. Aktualizacja jednej pamięci podręcznej w regionie ma wpływ na wszystkie inne pamięci podręcznych w regionie.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Authoring Azure Resource Manager szablony](../azure-resource-manager/resource-group-authoring-templates.md).

Zakończenie szablonu, zobacz [szablonu pamięci podręcznej Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Szablony Menedżera zasobów dla nowej [warstwy Premium](cache-premium-tier-intro.md) są dostępne. 
> 
> * [Tworzenie pamięci podręcznej Redis Premium z klastra](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
> * [Tworzenie z trwałości danych pamięci podręcznej Redis w warstwie Premium](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [Tworzenie pamięci podręcznej Redis w warstwie Premium z sieci wirtualnej i opcjonalnie klastra](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> Aby sprawdzić najnowsze szablonów, zobacz [szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates/) i wyszukaj `Redis Cache`.
> 
> 

## <a name="what-you-will-deploy"></a>Zostanie wdrożona
W tym szablonie wdroży pamięć podręczna Redis Azure używającej istniejącego konta magazynu dla danych diagnostycznych.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry
Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcji parametrów zawierająca wszystkie wartości parametrów.
Parametr powinien obejmować wartości, które różnią się w zależności od wdrażanego projektu lub środowiska, w którym odbywa się wdrożenie. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Lokalizacja pamięci podręcznej Redis. Aby uzyskać najlepszą wydajność należy użyć tej samej lokalizacji co aplikacja ma być używany z pamięci podręcznej.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Nazwa istniejącego konta magazynu do użycia na potrzeby diagnostyki. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Wartość logiczna, która wskazuje, czy zezwolić na dostęp za pośrednictwem portów bez użycia protokołu SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Wartość, która wskazuje, czy włączono diagnostyki. Użyj wartości ON lub OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia
### <a name="redis-cache"></a>Pamięć podręczna Redis
Tworzy pamięć podręczna Azure Redis.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


