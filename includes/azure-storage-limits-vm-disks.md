---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 2936fd318f08c74675f7e8b382c861f4a28319fc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183221"
---
Liczba dysków z danymi można dołączyć do maszyny wirtualnej platformy Azure. Oparte na cele skalowalności i wydajności dla dysków z danymi maszyny Wirtualnej, można określić liczbę i typ dysku, który trzeba spełniaj swoje wymagania pojemności i wydajności.

> [!IMPORTANT]
> Aby uzyskać optymalną wydajność należy ograniczyć liczbę intensywnie używanych dysków dołączonych do maszyny wirtualnej w celu uniknięcia potencjalnego ograniczania. Jeśli wszystkich dołączonych dysków nie są intensywnie używane w tym samym czasie, maszyna wirtualna może obsługiwać większą liczbę dysków.

**Dyski zarządzane Azure:**

W poniższej tabeli przedstawiono domyślne i maksymalnych limitów wybranej liczby zasobów, na region na subskrypcję

> | Resource | Limit domyślny  | Limit maksymalny |
> | --- | --- | --- |
> | Dyski zarządzane w warstwie standardowa | 25,000 | 50,000 |
> | SSD w warstwie standardowa usługi managed disks | 25,000 | 50,000 |
> | Managed disks w warstwie Premium | 25,000 | 50,000 |
> | Standard_LRS migawek | 25,000 | 50,000 |
> | Standard_ZRS migawek | 25,000 | 50,000 |
> | Zarządzany obraz | 25,000 | 50,000 |

* **W przypadku kont magazynu w warstwie standardowa:** Standardowe konto magazynu ma maksymalna całkowita liczba żądań 20 000 operacji We/Wy. Łączna liczba operacji We/Wy dla wszystkich dysków maszyny wirtualnej na koncie magazynu w warstwie standardowa nie powinna przekroczyć tego limitu.
  
    Możesz orientacyjnie obliczyć liczbę intensywnie używanych dysków obsługiwanych przez jednego standardowego konta magazynu, oparte na limitu liczby żądań. Na przykład dla warstwy podstawowa maszyna wirtualna maksymalną liczbę intensywnie używanych dysków jest o 66, czyli 20 000/300 IOPS na dysk. Maksymalna liczba intensywnie używanych dysków dla maszyny Wirtualnej w warstwie standardowa jest około 40, który jest 20 000/500 IOPS na dysk. 

* **Dla kont usługi Premium storage:** Konto magazynu Premium storage ma stawkę maksymalna całkowita przepływność 50 GB/s. Całkowita przepływność na wszystkich dyskach maszyny wirtualnej nie powinna przekroczyć tego limitu.

