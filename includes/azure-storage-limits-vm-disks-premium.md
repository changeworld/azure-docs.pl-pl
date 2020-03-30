---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334756"
---
**Dyski maszyn wirtualnych niezarządzanych w uliczce: limity na konto**

| Zasób | Limit |
| --- | --- |
| Całkowita pojemność dysku na konto |35 TB |
| Całkowita pojemność migawki na konto |10 TB |
| Maksymalna przepustowość na konto (ruch przychodzący + wyjście)<sup>1</sup> |<= 50 Gb/s |

<sup>1</sup>*Ruch przychodzący* odnosi się do wszystkich danych z żądań, które są wysyłane do konta magazynu. *Ruch wychodzący* odnosi się do wszystkich danych z odpowiedzi, które są odbierane z konta magazynu.

**Dyski maszyn wirtualnych niezarządzanych w wersji premium: limity na dysk**

| Typ dysku magazynu Premium Storage | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Rozmiar dysku |128 GiB |512 GiB |1024 GiB (1 TB) |2,048 GiB (2 TB)|4,095 GiB (4 TB)|
| Maksymalna liczba we/wy na dysk |500 |2300 |5000 |7500 |7500 |
| Maksymalna przepustowość na dysk |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Maksymalna liczba dysków na konto magazynu |280 |70 |35 | 17 | 8 |

**Dyski maszyn wirtualnych niezarządzanych w u. w wersji premium: limity na maszynę wirtualną**

| Zasób | Limit |
| --- | --- |
| Maksymalna liczba we/wy na maszynę wirtualną |80 000 IOPS z maszyną wirtualną GS5 |
| Maksymalna przepustowość na maszynę wirtualną |2000 MB/s z maszyną wirtualną GS5 |

