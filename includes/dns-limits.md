---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334670"
---
**Publiczne strefy DNS**

| Zasób | Limit |
| --- | --- |
| Publiczne strefy DNS na subskrypcję |250 <sup>1</sup> |
| Zestawy rekordów dla publicznej strefy DNS |10 000 <sup>1</sup> |
| Rekordy na rekord ustawiony w publicznej strefie DNS |20 |
| Liczba rekordów aliasu dla pojedynczego zasobu platformy Azure |20|
| Prywatne strefy DNS na subskrypcję |1000|
| Zestawy rekordów na prywatną strefę DNS |25 000|
| Rekordy dla prywatnych stref DNS dla rekordów |20|
| Łącza sieci wirtualnej na prywatną strefę DNS |1000|
| Łącza do sieci wirtualnych na prywatne strefy DNS z włączoną automatyczną rejestracją |100|
| Liczba prywatnych stref DNS, z które sieć wirtualna może połączyć się z włączoną automatyczną rejestracją |1|
| Liczba prywatnych stref DNS, z które sieć wirtualna może zostać połączona |1000|
| Liczba zapytań DNS, które maszyna wirtualna może wysyłać do programu rozpoznawania nazw DNS platformy Azure na sekundę |500 <sup>2</sup> |
| Maksymalna liczba kwerend DNS w kolejce (oczekująca odpowiedź) na maszynę wirtualną |200 <sup>2</sup> |

<sup>1.</sup> Jeśli chcesz zwiększyć te limity, skontaktuj się z pomocą techniczną platformy Azure.

<sup>2.</sup> Limity te są stosowane do każdej maszyny wirtualnej, a nie na poziomie sieci wirtualnej. Kwerendy DNS przekraczające te limity są odrzucane.