---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60118774"
---
### <a name="cacheskuname"></a>cacheSKUName

Warstwa cenowa nowej usługi Azure Cache dla usługi Redis.

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

Szablon definiuje wartości, które są dozwolone dla tego parametru (podstawowa, standardowa lub Premium) i przypisuje wartość domyślną (Basic), jeśli nie określono wartości. Basic oferuje pojedynczego węzła z dostępnych się wiele rozmiarów do 53 GB. Standard oferuje dwóch węzłów podstawowy i węzeł repliki z dostępnych się wiele rozmiarów do 53 GB i na poziomie 99,9% umowy SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

Rodzina dla jednostki sku.

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

Rozmiar nowej usługi Azure Cache dla wystąpienia usługi Redis.

Dla rodziny podstawowa i standardowa:

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

Zdefiniowano pojemności pamięci podręcznej wartości Premium Uruchom takie same, z wyjątkiem dozwolonych wartości z zakresu od 1 do 5 zamiast z zakresu od 0 do 6.

Szablon definiuje wartości całkowitych, które są dozwolone dla tego parametru (od 0 do 6 dla rodziny podstawowa i standardowa, od 1 do 5 dla rodziny — wersja Premium). Jeśli wartość nie zostanie określona, szablon przypisuje wartości domyślnej 0 dla warstw podstawowa i standardowa, 1 — wersja Premium.

Wartości odpowiadają następujących rozmiarów pamięci podręcznej:

| Wartość | Podstawowa i standardowa<br>Rozmiar pamięci podręcznej | Premium<br>Rozmiar pamięci podręcznej |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (ustawienie domyślne)                 | Nie dotyczy                   |
| 1     | 1 GB                             | 6 GB (ustawienie domyślne)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | Nie dotyczy                   |
