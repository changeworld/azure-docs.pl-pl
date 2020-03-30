---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183039"
---
Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie Parametry, która zawiera wszystkie wartości parametrów.
Należy zdefiniować parametr dla tych wartości, które będą się różnić w zależności od projektu, który wdrażasz lub na podstawie środowiska, w które wdrażasz. Nie należy definiować parametrów dla wartości, które zawsze pozostaną takie same. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby. 

Podczas definiowania parametrów należy użyć pola **allowedValues,** aby określić, które wartości użytkownik może podać podczas wdrażania. Użyj **pola defaultValue,** aby przypisać wartość do parametru, jeśli podczas wdrażania nie podano żadnej wartości.

Opiszemy każdy parametr w szablonie.

### <a name="sitename"></a>Sitename
Nazwa aplikacji sieci web, którą chcesz utworzyć.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
Nazwa usługi App Service ma być używana do obsługi aplikacji sieci web.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
Warstwa cenowa dla planu hostingowego.

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

Szablon definiuje wartości, które są dozwolone dla tego parametru i przypisuje wartość domyślną (S1), jeśli nie określono żadnej wartości.

### <a name="workersize"></a>pracownikZmiar
Rozmiar wystąpienia planu hostingu (mały, średni lub duży).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Szablon definiuje wartości, które są dozwolone dla tego parametru (0, 1 lub 2) i przypisuje wartość domyślną (0), jeśli nie określono wartości. Wartości odpowiadają małym, średnim i dużym.

