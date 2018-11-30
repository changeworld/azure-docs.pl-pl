---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/21/2018
ms.author: wesmc
ms.openlocfilehash: 1ddb81de479317a098f9de8aa5756cbaae59cb72
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331316"
---
### <a name="cacheskuname"></a>cacheSKUName
Warstwa cenowa nowej usługi Azure Redis Cache.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

Szablon definiuje wartości, które są dozwolone dla tego parametru (podstawowa lub standardowa) i przypisuje wartość domyślną (Basic), jeśli nie określono wartości. Basic oferuje pojedynczego węzła z dostępnych się wiele rozmiarów do 53 GB.
Standard oferuje dwóch węzłów podstawowy i węzeł repliki z dostępnych się wiele rozmiarów do 53 GB i na poziomie 99,9% umowy SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily
Rodzina dla jednostki sku.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
Rozmiar nowego wystąpienia usługi Azure Redis Cache. 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


Szablon definiuje wartości, które są dozwolone dla tego parametru (0, 1, 2, 3, 4, 5 lub 6) i przypisuje wartość domyślną (0), jeśli nie określono wartości. Tych dwóch liczb odnoszą się do następujących rozmiarów pamięci podręcznej: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

