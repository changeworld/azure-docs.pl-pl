---
title: Wdrażanie pamięci podręcznej platformy Azure dla programu Redis za pomocą usługi Azure Resource Manager
description: Dowiedz się, jak użyć szablonu usługi Azure Resource Manager do wdrożenia zasobu usługi Azure Cache for Redis. Szablony są dostarczane dla typowych scenariuszy.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 787edf662aa3a34e167db61b0a89dfc5c2944219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75412406"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Tworzenie pamięci podręcznej platformy Azure dla redis przy użyciu szablonu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie dowiesz się, jak utworzyć szablon usługi Azure Resource Manager, który wdraża pamięć podręczną platformy Azure dla programu Redis. Pamięć podręczna może służyć z istniejącym kontem magazynu do przechowywania danych diagnostycznych. Dowiesz się również, jak zdefiniować, które zasoby są wdrażane i jak zdefiniować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Obecnie ustawienia diagnostyczne są udostępniane dla wszystkich pamięci podręcznych w tym samym regionie dla subskrypcji. Aktualizowanie jednej pamięci podręcznej w regionie wpływa na wszystkie inne pamięci podręczne w regionie.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Aby dowiedzieć się więcej o składni JSON i właściwościach typów zasobów pamięci podręcznej, zobacz [Typy zasobów microsoft.cache](/azure/templates/microsoft.cache/allversions).

Aby uzyskać pełny szablon, zobacz [Szablon usługi Azure Cache for Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Dostępne są szablony Menedżera zasobów dla nowej [warstwy Premium.](cache-premium-tier-intro.md) 
> 
> * [Tworzenie pamięci podręcznej platformy Azure premium dla redis z klastrem](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Tworzenie pamięci podręcznej premium azure dla redis z trwałością danych](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Tworzenie pamięci podręcznej Premium Redis wdrożonej w sieci wirtualnej](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Aby sprawdzić dostępność najnowszych szablonów, zobacz [Szablony szybki start platformy Azure](https://azure.microsoft.com/documentation/templates/) i wyszukaj . `Azure Cache for Redis`
> 
> 

## <a name="what-you-will-deploy"></a>Co wdrożysz
W tym szablonie wdrożysz pamięć podręczną Azure Cache for Redis, która używa istniejącego konta magazynu dla danych diagnostycznych.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry
Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie Parametry, która zawiera wszystkie wartości parametrów.
Parametr powinien obejmować wartości, które różnią się w zależności od wdrażanego projektu lub środowiska, w którym odbywa się wdrożenie. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLokacja
Lokalizacja pamięci podręcznej azure dla redis. Aby uzyskać najlepszą wydajność, użyj tej samej lokalizacji co aplikacja, która ma być używana z pamięcią podręczną.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Nazwa istniejącego konta magazynu do użycia w diagnostyce. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>włącz portNonSslPort
Wartość logiczna wskazująca, czy zezwolić na dostęp za pośrednictwem portów innych niż SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnostykaStatus
Wartość wskazująca, czy diagnostyka jest włączona. Użyj on lub OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia
### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Tworzy pamięć podręczną azure dla redis.

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
