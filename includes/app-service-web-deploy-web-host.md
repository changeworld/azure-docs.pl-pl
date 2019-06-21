---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 87fd6b626efb60c7fc7ec8896f2c3758ae5cc33c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183038"
---
### <a name="app-service-plan"></a>Plan usługi App Service
Tworzy plan usługi do hostowania aplikacji sieci web. Podaj nazwę planu za pośrednictwem **hostingPlanName** parametru. Lokalizacja planu jest tej samej lokalizacji, które są używane dla grupy zasobów. Cenową rozmiar warstwy i proces roboczy są określone w **jednostki sku** i **workerSize** parametrów

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

