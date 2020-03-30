---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334962"
---
Do maszyny wirtualnej platformy Azure można dołączyć wiele dysków danych. Na podstawie celów skalowalności i wydajności dysków danych maszyny Wirtualnej można określić liczbę i typ dysku, który jest potrzebny do spełnienia wymagań dotyczących wydajności i pojemności.

> [!IMPORTANT]
> Aby uzyskać optymalną wydajność, należy ograniczyć liczbę wysoce wykorzystywanych dysków dołączonych do maszyny wirtualnej, aby uniknąć możliwego ograniczania przepustowości. Jeśli wszystkie podłączone dyski nie są wysoko wykorzystywane w tym samym czasie, maszyna wirtualna może obsługiwać większą liczbę dysków.

**W przypadku dysków zarządzanych platformy Azure:**

W poniższej tabeli przedstawiono domyślne i maksymalne limity liczby zasobów na region na subskrypcję. Nie ma limitu liczby dysków zarządzanych, migawek i obrazów na grupę zasobów.  

> | Zasób | Limit |
> | --- | --- |
> | Standardowe dyski zarządzane | 50 000 |
> | Standardowe dyski zarządzane SSD | 50 000 |
> | Dyski zarządzane w wersji Premium | 50 000 |
> | Standard_LRS migawki | 50 000 |
> | Migawki Standard_ZRS | 50 000 |
> | Obraz zarządzany | 50 000 |

* **W przypadku standardowych kont magazynu:** Standardowe konto magazynu ma maksymalną łączną szybkość żądania 20 000 we/wy. Całkowita liczba we/wy we wszystkich dyskach maszyny wirtualnej na koncie magazynu standardowego nie powinna przekraczać tego limitu.
  
    Można z grubsza obliczyć liczbę wysoce wykorzystywanych dysków obsługiwanych przez jedno standardowe konto magazynu na podstawie limitu szybkości żądania. Na przykład dla maszyny Wirtualnej warstwy Podstawowej maksymalna liczba wysoce wykorzystywanych dysków wynosi około 66, czyli 20 000/300 operacji we/wy na dysk. Maksymalna liczba wysoce wykorzystywanych dysków dla maszyny wirtualnej warstwy standardowej wynosi około 40, czyli 20 000/500 we/wy na dysk. 

* **W przypadku kont magazynu w wersji Premium:** Konto magazynu w wersji Premium ma maksymalną całkowitą przepustowość 50 Gb/s. Całkowita przepływność na wszystkich dyskach maszyny wirtualnej nie powinna przekroczyć tego limitu.

