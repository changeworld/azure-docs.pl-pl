---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183231"
---
W poniższej tabeli wymieniono możliwe kombinacje przekazywania i przechwytywania obrazów systemu Windows uogólnionych (gen.) i wyspecjalizowanych (spec.) systemów operacyjnych. Kombinacje, które będą przetwarzać bez błędów są wskazywane przez Y, a te, które będą rzucać błędy są wskazywane przez N. Przyczyny i rozwiązania dla różnych błędów, które można napotkać są podane poniżej tabeli.

| System operacyjny | Specyfikacja przesyłania. | Wyślij gen. | Specyfikacja przechwytywania. | Przechwytywanie gen. |
| --- | --- | --- | --- | --- |
| Windows gen. |N<sup>1</sup> |Tak |N<sup>3</sup> |Tak |
| Specyfikacja systemu Windows. |Tak |N<sup>2</sup> |Tak |N<sup>4</sup> |

