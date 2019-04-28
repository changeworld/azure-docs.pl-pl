---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386339"
---
**Dyski Premium niezarządzanej maszyny wirtualnej: Limity dla konta**

| Resource | Limit domyślny |
| --- | --- |
| Całkowita pojemność dysku na konto |35 TB |
| Całkowita pojemność migawki na konto |10 TB |
| Maksymalna przepustowość na konto (ruch przychodzący i wychodzący)<sup>1</sup> |<= 50 Gb/s |

<sup>1</sup>*ruch przychodzący* odnosi się do wszystkich danych z żądań, które są wysyłane do konta magazynu. *Ruch wychodzący* odnosi się do wszystkich danych z odpowiedzi, które są odbierane z konta magazynu.

**Dyski Premium niezarządzanej maszyny wirtualnej: Limity dysku**

| Typ dysku magazynu Premium Storage | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Rozmiar dysku |128 GiB |512 GiB |1,024 GiB (1 TB) |2,048 GiB (2 TB)|4,095 GiB (4 TB)|
| Maksymalna liczba IOPS na dysk |500 |2300 |5000 |7500 |7500 |
| Maksymalna przepływność na dysk |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Maksymalna liczba dysków na konto magazynu |280 |70 |35 | 17 | 8 |

**Dyski Premium niezarządzanej maszyny wirtualnej: Limity poszczególnych maszyn wirtualnych**

| Resource | Limit domyślny |
| --- | --- |
| Maksymalna liczba IOPS na maszynę Wirtualną |80 000 operacji We/Wy z maszyn wirtualnych GS5 |
| Maksymalna przepływność na maszynę Wirtualną |2000 MB na sekundę przy użyciu maszyn wirtualnych GS5 |

