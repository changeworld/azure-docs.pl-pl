---
title: Aprowizowanie aplikacji sieci Web i pamięć podręczna systemu Azure dla usługi Redis
description: Szablon usługi Azure Resource Manager umożliwia wdrażanie aplikacji internetowej za pomocą usługi Azure Cache dla usługi Redis.
services: app-service
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 6e99c71f-ef8e-4570-a307-e4c059e60c35
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: 23b8e4e7e88f5b993f9b0f9981bbae6b884e2818
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65911311"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Tworzenie aplikacji internetowej oraz pamięć podręczna systemu Azure dla pamięci podręcznej Redis przy użyciu szablonu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie dowiesz się, jak utworzyć szablon usługi Azure Resource Manager, która wdraża aplikację internetową platformy Azure z pamięcią podręczną Azure dla usługi Redis. Dowiesz się, jak zdefiniować zasoby, które są wdrażane i jak zdefiniować parametry, które są określone, gdy wdrożenie jest wykonywane. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Resource Manager platformy Azure](../azure-resource-manager/resource-group-authoring-templates.md). Aby dowiedzieć się więcej na temat składni JSON i właściwości dla typów zasobów pamięci podręcznej, zobacz [typów zasobów Microsoft.Cache](/azure/templates/microsoft.cache/allversions).

Aby uzyskać kompletny szablon, zobacz [aplikacji internetowej z pamięcią podręczną Azure Redis szablonu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Zostanie wdrożona
W tym szablonie zostanie wdrożona:

* Aplikacja sieci Web platformy Azure
* Azure Cache for Redis

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>W celu określenia
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Zmienne dla nazwy
Ten szablon używa zmiennych do konstruowania nazwy zasobów. Używa ona [uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) funkcję, aby skonstruować wartości oparte na identyfikator grupy zasobów.

    "variables": {
      "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
      "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
      "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
    },


## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Tworzy pamięć podręczna Azure redis cache, który jest używany z aplikacji sieci web. Nazwa pamięci podręcznej została określona w **cacheName** zmiennej.

Ten szablon tworzy pamięć podręczną w tej samej lokalizacji co grupa zasobów.

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
Służy do tworzenia aplikacji sieci web przy użyciu nazwy określonej w **podaną nazwą** zmiennej.

Zwróć uwagę, aplikacji sieci web skonfigurowano właściwości ustawienia aplikacji, które umożliwiają go do pracy z pamięcią podręczną Azure dla usługi Redis. Te ustawienia są dynamicznie utworzona aplikacja na podstawie wartości podanych podczas wdrażania.

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
