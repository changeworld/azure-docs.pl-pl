---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 8b25d2395811a2197aff6d653c5038a4380021e9
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "68669778"
---
Do maszyny wirtualnej platformy Azure można dołączyć wiele dysków z danymi. Na podstawie celów skalowalności i wydajności dla dysków danych maszyny wirtualnej można określić liczbę i typ dysku, który trzeba spełnić wymagania dotyczące wydajności i pojemności.

> [!IMPORTANT]
> Aby zapewnić optymalną wydajność, należy ograniczyć liczbę dysków o wysokiej dostępności podłączonych do maszyny wirtualnej, aby uniknąć możliwego ograniczenia przepustowości. Jeśli wszystkie dołączone dyski nie są wysoce wykorzystywane w tym samym czasie, maszyna wirtualna może obsługiwać większą liczbę dysków.

**W przypadku usługi Azure Managed disks:**

W poniższej tabeli przedstawiono domyślne i maksymalne limity liczby zasobów na region na subskrypcję. Nie ma żadnego limitu liczby Managed Disks, migawek i obrazów na grupę zasobów.  

> | Resource | Limit domyślny  | Limit maksymalny |
> | --- | --- | --- |
> | Dyski zarządzane w warstwie Standardowa | 50,000 | 50,000 |
> | Zarządzane dyski SSD w warstwie Standardowa | 50,000 | 50,000 |
> | Dyski zarządzane w warstwie Premium | 50,000 | 50,000 |
> | Migawki Standard_LRS | 50,000 | 50,000 |
> | Migawki Standard_ZRS | 50,000 | 50,000 |
> | Obraz zarządzany | 50,000 | 50,000 |

* **W przypadku kont magazynu w warstwie Standardowa:** Maksymalna łączna liczba żądań w ramach konta magazynu w warstwie Standardowa wynosi 20 000 operacji we/wy na sekundę. Łączna liczba operacji we/wy na wszystkich dyskach maszyny wirtualnej na koncie magazynu w warstwie Standardowa nie powinna przekraczać tego limitu.
  
    Można w przybliżeniu obliczyć liczbę dysków o wysokiej dostępności obsługiwanych przez pojedyncze konto magazynu w warstwie Standardowa na podstawie limitu liczby żądań. Na przykład w przypadku maszyny wirtualnej warstwy Podstawowa Maksymalna liczba dysków o wysokiej dostępności wynosi około 66, czyli 20000/300 operacji we/wy na dysku. Maksymalna liczba dysków o wysokiej dostępności dla maszyny wirtualnej warstwy Standardowa to około 40, czyli 20000/500 operacji we/wy na dysku. 

* **Dla kont usługi Premium Storage:** Konto magazynu w warstwie Premium ma maksymalną łączną stawkę przepływności wynoszącą 50 GB/s. Całkowita przepływność na wszystkich dyskach maszyny wirtualnej nie powinna przekroczyć tego limitu.

