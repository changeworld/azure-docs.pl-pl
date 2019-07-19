---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: ef9c46c0b55f97b8931f16d751f1b1e6099e6c9d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68328629"
---
Do maszyny wirtualnej platformy Azure można dołączyć wiele dysków z danymi. Na podstawie celów skalowalności i wydajności dla dysków danych maszyny wirtualnej można określić liczbę i typ dysku, który trzeba spełnić wymagania dotyczące wydajności i pojemności.

> [!IMPORTANT]
> Aby zapewnić optymalną wydajność, należy ograniczyć liczbę dysków o wysokiej dostępności podłączonych do maszyny wirtualnej, aby uniknąć możliwego ograniczenia przepustowości. Jeśli wszystkie dołączone dyski nie są wysoce wykorzystywane w tym samym czasie, maszyna wirtualna może obsługiwać większą liczbę dysków.

**W przypadku usługi Azure Managed disks:**

W poniższej tabeli przedstawiono domyślne i maksymalne limity liczby zasobów na region na subskrypcję

> | Resource | Limit domyślny  | Limit maksymalny |
> | --- | --- | --- |
> | Dyski zarządzane w warstwie Standardowa | 50,000 | 50,000 |
> | SSD w warstwie Standardowa Managed disks | 50,000 | 50,000 |
> | Dyski zarządzane w warstwie Premium | 50,000 | 50,000 |
> | Migawki Standard_LRS | 50,000 | 50,000 |
> | Migawki Standard_ZRS | 50,000 | 50,000 |
> | Obraz zarządzany | 50,000 | 50,000 |

* **W przypadku kont magazynu w warstwie Standardowa:** Maksymalna łączna liczba żądań w ramach konta magazynu w warstwie Standardowa wynosi 20 000 operacji we/wy na sekundę. Łączna liczba operacji we/wy na wszystkich dyskach maszyny wirtualnej na koncie magazynu w warstwie Standardowa nie powinna przekraczać tego limitu.
  
    Można w przybliżeniu obliczyć liczbę dysków o wysokiej dostępności obsługiwanych przez pojedyncze konto magazynu w warstwie Standardowa na podstawie limitu liczby żądań. Na przykład w przypadku maszyny wirtualnej warstwy Podstawowa Maksymalna liczba dysków o wysokiej dostępności wynosi około 66, czyli 20000/300 operacji we/wy na dysku. Maksymalna liczba dysków o wysokiej dostępności dla maszyny wirtualnej warstwy Standardowa to około 40, czyli 20000/500 operacji we/wy na dysku. 

* **Dla kont usługi Premium Storage:** Konto magazynu w warstwie Premium ma maksymalną łączną stawkę przepływności wynoszącą 50 GB/s. Całkowita przepływność na wszystkich dyskach maszyny wirtualnej nie powinna przekroczyć tego limitu.

