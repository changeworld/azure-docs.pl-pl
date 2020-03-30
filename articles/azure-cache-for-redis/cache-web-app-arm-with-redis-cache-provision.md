---
title: Aprowizuj aplikację sieci Web za pomocą pamięci podręcznej azure dla redis
description: Użyj szablonu usługi Azure Resource Manager, aby wdrożyć aplikację sieci Web z pamięcią podręczną Azure dla programu Redis.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: 11c854491ab030394eb61964979cb04a5a4b489b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433393"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Tworzenie aplikacji sieci Web i pamięci podręcznej platformy Azure dla programu Redis przy użyciu szablonu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie dowiesz się, jak utworzyć szablon usługi Azure Resource Manager, który wdraża aplikację Azure Web App z usługą Azure Cache for Redis. Dowiesz się, jak zdefiniować, które zasoby są wdrażane i jak zdefiniować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Aby dowiedzieć się więcej o składni JSON i właściwościach typów zasobów pamięci podręcznej, zobacz [Typy zasobów microsoft.cache](/azure/templates/microsoft.cache/allversions).

Aby uzyskać pełny szablon, zobacz [Aplikacja sieci Web z szablonem Usługi Azure Cache for Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Co wdrożysz
W tym szablonie zostanie wdrożony:

* Aplikacja internetowa platformy Azure
* Azure Cache for Redis

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Parametry do określenia
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Zmienne dla nazw
Ten szablon używa zmiennych do konstruowania nazw zasobów. Używa [uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) funkcji do konstruowania wartości na podstawie identyfikatora grupy zasobów.

    "variables": {
      "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
      "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
      "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
    },


## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Tworzy pamięć podręczną Azure dla redis, która jest używana z aplikacją sieci web. Nazwa pamięci podręcznej jest określona w **zmiennej cacheName.**

Szablon tworzy pamięć podręczną w tej samej lokalizacji co grupa zasobów.

    {
      "name": "[variables('cacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [ ],
      "tags": {
        "displayName": "cache"
      },
      "properties": {
        "sku": {
          "name": "[parameters('cacheSKUName')]",
          "family": "[parameters('cacheSKUFamily')]",
          "capacity": "[parameters('cacheSKUCapacity')]"
        }
      }
    }


### <a name="web-app"></a>Aplikacja internetowa
Tworzy aplikację sieci web o nazwie określonej w zmiennej **webSiteName.**

Należy zauważyć, że aplikacja sieci web jest skonfigurowana z właściwości ustawienia aplikacji, które umożliwiają jej pracę z pamięcią podręczną Azure dla redis. Te ustawienia aplikacji są tworzone dynamicznie na podstawie wartości podanych podczas wdrażania.

    {
      "apiVersion": "2015-08-01",
      "name": "[variables('webSiteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
        "displayName": "Website"
      },
      "properties": {
        "name": "[variables('webSiteName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "type": "config",
          "name": "appsettings",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
            "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
          ],
          "properties": {
            "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
