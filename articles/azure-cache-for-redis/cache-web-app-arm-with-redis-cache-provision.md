---
title: Udostępnianie aplikacji sieci Web za pomocą usługi Azure cache for Redis
description: Użyj szablonu Azure Resource Manager, aby wdrożyć aplikację internetową za pomocą usługi Azure cache for Redis.
services: app-service
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 6e99c71f-ef8e-4570-a307-e4c059e60c35
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: b28ed58159545bca10ec89375b82b9c97ae38630
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098239"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Tworzenie aplikacji sieci Web i usługi Azure cache for Redis przy użyciu szablonu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie dowiesz się, jak utworzyć szablon Azure Resource Manager, który wdraża aplikację internetową platformy Azure w usłudze Azure cache for Redis. Dowiesz się, jak definiować wdrożone zasoby oraz jak definiować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Aby dowiedzieć się więcej na temat składni i właściwości JSON dla typów zasobów pamięci podręcznej, zobacz [Microsoft. cache — typy zasobów](/azure/templates/microsoft.cache/allversions).

Aby zapoznać się z pełnym szablonem, zobacz [Web App with Azure cache for Redis Template](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Co zostanie wdrożone
W tym szablonie zostaną wdrożone następujące narzędzia:

* Aplikacja sieci Web platformy Azure
* Azure Cache for Redis

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Parametry do określenia
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Zmienne nazw
Ten szablon używa zmiennych do konstruowania nazw zasobów. Używa funkcji [uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) do konstruowania wartości na podstawie identyfikatora grupy zasobów.

    "variables": {
      "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
      "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
      "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
    },


## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Tworzy pamięć podręczną platformy Azure dla usługi Redis, która jest używana z aplikacją sieci Web. Nazwa pamięci podręcznej jest określona w zmiennej **cacheName** .

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
Tworzy aplikację sieci Web o nazwie określonej w zmiennej WebsiteName.

Zwróć uwagę, że aplikacja sieci Web jest skonfigurowana przy użyciu właściwości ustawienia aplikacji, które umożliwiają współdziałanie z pamięcią podręczną platformy Azure dla Redis. Te ustawienia aplikacji są dynamicznie tworzone na podstawie wartości podanych podczas wdrażania.

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
