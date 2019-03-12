---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: 09b4f94db3464943a8367bfb3ca89f9a88446193
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554177"
---
Liczba dysków z danymi można dołączyć do maszyny wirtualnej platformy Azure. Oparte na cele skalowalności i wydajności dla dysków z danymi maszyny Wirtualnej, można określić liczbę i typ dysku, który trzeba spełniaj swoje wymagania pojemności i wydajności.

> [!IMPORTANT]
> Aby uzyskać optymalną wydajność należy ograniczyć liczbę intensywnie używanych dysków dołączonych do maszyny wirtualnej w celu uniknięcia potencjalnego ograniczania. Jeśli wszystkich dołączonych dysków nie są intensywnie używane w tym samym czasie, maszyna wirtualna może obsługiwać większą liczbę dysków.

* **Dyski zarządzane Azure:** 

> | Zasób | Limit domyślny | Limit maksymalny |
> | --- | --- | --- |
> | Dyski zarządzane w warstwie standardowa | 10 000 | 50,000 |
> | Zarządzane dyski SSD w warstwie Standardowa | 10 000 | 50,000 |
> | Dyski zarządzane w warstwie Premium | 10 000 | 50,000 |
> | Standard_LRS migawek | 10 000 | 50,000 |
> | Standard_ZRS migawek | 10 000 | 50,000 |
> | Zarządzany obraz | 10 000 | 50,000 |

* **W przypadku kont magazynu w warstwie standardowa:** Standardowe konto magazynu ma maksymalna całkowita liczba żądań 20 000 operacji We/Wy. Łączna liczba operacji We/Wy dla wszystkich dysków maszyny wirtualnej na koncie magazynu w warstwie standardowa nie powinna przekroczyć tego limitu.
  
    Możesz orientacyjnie obliczyć liczbę intensywnie używanych dysków obsługiwanych przez jednego standardowego konta magazynu, oparte na limitu liczby żądań. Na przykład dla warstwy podstawowa maszyna wirtualna maksymalną liczbę intensywnie używanych dysków jest o 66, czyli 20 000/300 IOPS na dysk. Maksymalna liczba intensywnie używanych dysków dla maszyny Wirtualnej w warstwie standardowa jest około 40, który jest 20 000/500 IOPS na dysk. 

* **Dla kont usługi Premium storage:** Konto magazynu Premium storage ma stawkę maksymalna całkowita przepływność 50 GB/s. Całkowita przepływność na wszystkich dyskach maszyny wirtualnej nie powinna przekroczyć tego limitu.

