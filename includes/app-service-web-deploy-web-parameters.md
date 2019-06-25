---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183039"
---
Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie parametrów, który zawiera wszystkie wartości parametrów.
Należy zdefiniować parametr dla tych wartości, które będą się różnić, na podstawie projektu, który jest wdrażany lub opartych na środowisku, w których jest wdrażany z. Nie należy definiować parametrów dla wartości, które zawsze pozostają takie same. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby. 

Podczas definiowania parametrów należy użyć **allowedValues** pola, aby określić, jakie wartości przez użytkownika można podać podczas wdrażania. Użyj **defaultValue** pola do przypisania wartości do parametru, jeśli wartość nie zostanie podana podczas wdrażania.

Opiszemy każdego parametru w szablonie.

### <a name="sitename"></a>siteName
Nazwa aplikacji sieci web, który chcesz utworzyć.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
Nazwa planu usługi App Service na potrzeby hostowania aplikacji sieci web.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
Warstwę cenową dla planu hostingu.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Szablon definiuje wartości, które są dozwolone dla tego parametru i przypisuje wartość domyślną (S1), jeśli nie określono wartości.

### <a name="workersize"></a>workerSize
Rozmiar wystąpienia planu hostingu (małych, średnich i dużych).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Szablon definiuje wartości, które są dozwolone dla tego parametru (0, 1 lub 2) i przypisuje wartość domyślną (0), jeśli nie określono wartości. Wartości odpowiadają małych, średnich i dużych.

