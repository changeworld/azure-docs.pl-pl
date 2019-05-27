---
title: Inicjowanie obsługi administracyjnej Azure pamięci podręcznej dla pamięci podręcznej Redis przy użyciu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Szablon usługi Azure Resource Manager umożliwia wdrażanie pamięć podręczna systemu Azure dla usługi Redis.
services: app-service
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: 5bdad61df732f0aeb1a758aacb5844204387e19b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66132813"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Tworzenie pamięci podręcznej Azure dla pamięci podręcznej Redis przy użyciu szablonu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie dowiesz się, jak utworzyć szablon usługi Azure Resource Manager, która wdraża pamięć podręczna systemu Azure dla usługi Redis. Pamięć podręczna może służyć za pomocą istniejącego konta magazynu do przechowywania danych diagnostycznych. Dowiesz się również, jak zdefiniować zasoby, które są wdrażane i jak zdefiniować parametry, które są określone, gdy wdrożenie jest wykonywane. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Obecnie ustawienia diagnostyczne są wspólne dla wszystkich pamięci podręcznych, w tym samym regionie, w ramach subskrypcji. Trwa aktualizacja co pamięci podręcznej w regionie ma wpływ na wszystkie inne pamięci podręczne w regionie.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Resource Manager platformy Azure](../azure-resource-manager/resource-group-authoring-templates.md). Aby dowiedzieć się więcej na temat składni JSON i właściwości dla typów zasobów pamięci podręcznej, zobacz [typów zasobów Microsoft.Cache](/azure/templates/microsoft.cache/allversions).

Aby uzyskać kompletny szablon, zobacz [pamięci podręcznej Azure Redis szablonu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Szablony usługi Resource Manager dla nowego [w warstwie Premium](cache-premium-tier-intro.md) są dostępne. 
> 
> * [Tworzenie pamięci podręcznej Azure — wersja Premium dla usługi Redis z klastrowaniem](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Tworzenie usługi Azure Cache w warstwie Premium dla usługi Redis dzięki funkcji trwałości danych](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Tworzenie usługi Redis Cache w warstwie Premium wdrożone w sieci wirtualnej](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Aby sprawdzić najnowsze szablony, zobacz [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/) i wyszukaj `Azure Cache for Redis`.
> 
> 

## <a name="what-you-will-deploy"></a>Zostanie wdrożona
W tym szablonie wdroży pamięć podręczna systemu Azure dla usługi Redis, który korzysta z istniejącego konta magazynu dla danych diagnostycznych.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry
Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie parametrów, który zawiera wszystkie wartości parametrów.
Parametr powinien obejmować wartości, które różnią się w zależności od wdrażanego projektu lub środowiska, w którym odbywa się wdrożenie. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Lokalizacja pamięci podręcznej Azure dla usługi Redis. Aby uzyskać najlepszą wydajność należy użyć tej samej lokalizacji co aplikacja ma być używany z pamięcią podręczną.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Nazwa istniejącego konta magazynu na potrzeby diagnostyki. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Wartość logiczna, która wskazuje, czy zezwalać na dostęp za pośrednictwem portów protokołu SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Wartość, która wskazuje, czy włączono diagnostyki. Użyj wł. lub Wył.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia
### <a name="azure-cache-for-redis"></a>Usługa Azure Cache for Redis
Tworzy pamięć podręczna systemu Azure dla usługi Redis.

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
          "apiVersion": "2017-05-01-preview",
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

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


