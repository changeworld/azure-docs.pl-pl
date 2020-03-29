---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183040"
---
### <a name="cacheskuname"></a>nazwa pamięci podręcznejNA

Warstwa cenowa nowej pamięci podręcznej Azure dla redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

Szablon definiuje wartości, które są dozwolone dla tego parametru (Basic, Standard lub Premium) i przypisuje wartość domyślną (Podstawowa), jeśli nie określono żadnej wartości. Basic zapewnia jeden węzeł o wielu rozmiarach dostępnych do 53 GB. Standard zapewnia dwuwęzłową podstawową/replikę o wielu rozmiarach dostępnych do 53 GB i 99,9% umowy SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

Rodzina dla sku.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

Rozmiar nowej pamięci podręcznej azure dla wystąpienia Redis.

Dla rodzin podstawowych i standardowych:

```json
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
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

Pojemność pamięci podręcznej wartości premium jest zdefiniowana tak samo, z wyjątkiem dozwolonych wartości uruchamianych od 1 do 5 zamiast od 0 do 6.

Szablon definiuje wartości całkowite, które są dozwolone dla tego parametru (od 0 do 6 dla rodzin podstawowych i standardowych; od 1 do 5 dla rodziny Premium). Jeśli nie określono żadnej wartości, szablon przypisuje domyślną wartość 0 dla basic i standard, 1 dla premium.

Wartości odpowiadają następującym rozmiarom pamięci podręcznej:

| Wartość | Podstawowe i standardowe<br>rozmiar pamięci podręcznej | Premium<br>rozmiar pamięci podręcznej |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (domyślnie)                 | Nie dotyczy                   |
| 1     | 1 GB                             | 6 GB (domyślnie)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | Nie dotyczy                   |
