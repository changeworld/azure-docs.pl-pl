---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: aa701ada917811382351fee9469a5cfa9a7599b8
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279827"
---
Maszyna wirtualna platformy Azure obsługuje dołączanie wielu dysków danych. W tym artykule opisano cele dotyczące skalowalności i wydajności dla dysków z danymi maszyny Wirtualnej. Aby zdecydować, liczba i typ dysku, który konieczną do spełnienia wymagań dotyczących wydajności i pojemności, należy użyć następujących elementów docelowych. 

> [!IMPORTANT]
> Aby uzyskać optymalną wydajność należy ograniczyć liczbę intensywnie używanych dysków dołączonych do maszyny wirtualnej w celu uniknięcia potencjalnego ograniczania. Jeśli wysoce wszystkich dołączonych dysków nie są używane w tym samym czasie, maszyna wirtualna może obsługiwać większą liczbę dysków.

* **Dyski zarządzane Azure:** 

> | Zasób | Limit domyślny | Limit maksymalny |
> | --- | --- | --- |
> | Dyski Managed Disks w warstwie Standardowa | 10 000 | 50,000 |
> | Usługa Managed Disks z dyskami SSD w warstwie Standardowa | 10 000 | 50,000 |
> | Dyski Managed Disks w warstwie Premium | 10 000 | 50,000 |
> | Standard_LRS migawek | 10 000 | 50,000 |
> | Standard_ZRS migawek | 10 000 | 50,000 |
> | Zarządzany obraz | 10 000 | 50,000 |

* **Konto magazynu w warstwie Standardowa:** maksymalna całkowita liczba żądań dla konta magazynu w warstwie Standardowa to 20 000 operacji wejścia/wyjścia na sekundę (IOPS). Łączna liczba IOPS na wszystkich dyskach maszyny wirtualnej w koncie magazynu w warstwie Standardowa nie powinna przekroczyć tego limitu.
  
    Możesz orientacyjnie obliczyć liczbę intensywnie używanych dysków obsługiwanych przez jedno konto magazynu w warstwie Standardowa na podstawie limitu liczby żądań. Na przykład dla podstawowej warstwy maszyny Wirtualnej, maksymalna liczba intensywnie używanych dysków to 66 (20 000/300 IOPS na dysk), a dla maszyny Wirtualnej warstwy standardowa, to około 40 (20 000/500 IOPS na dysk). 

* **Konta magazynu w warstwie Premium:** maksymalna całkowita przepływność konta magazynu w warstwie Premium to 50 Gb/s. Całkowita przepływność na wszystkich dyskach maszyny wirtualnej nie powinna przekroczyć tego limitu.

