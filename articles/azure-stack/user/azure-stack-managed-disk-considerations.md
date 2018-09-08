---
title: Różnice i zagadnienia dotyczące dysków Managed Disks w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Poznaj różnice i zagadnienia podczas pracy z usługą Managed Disks w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: a2ad07809963560b1225ff07b095509c93618996
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160425"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Usługi Azure Managed Disks stosu: Różnice i zagadnienia dotyczące
Ten artykuł zawiera podsumowanie znane różnice między stosu usługi Azure Managed Disks i dysków Managed Disks na platformie Azure. Aby dowiedzieć się więcej o różnicach wysokiego poziomu usługi Azure Stack i platformą Azure, zobacz [kluczowe zagadnienia](azure-stack-considerations.md) artykułu.

Usługa Managed Disks upraszcza zarządzanie dyskami dla maszyn wirtualnych IaaS dzięki zarządzaniu [kont magazynu](/azure/azure-stack/azure-stack-manage-storage-accounts) skojarzone z dyskami maszyn wirtualnych.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Ściągawka: różnice dysku zarządzanego

| Cecha | Azure (globalna) | Azure Stack |
| --- | --- | --- |
|Szyfrowanie danych magazynowanych |Szyfrowanie usługi Azure Storage (SSE), usługa Azure Disk Encryption (ADE)     |Funkcja BitLocker 128-bitowego szyfrowania AES      |
|Image (Obraz)          | Obsługa zarządzany obraz niestandardowy |Nie jest jeszcze obsługiwany|
|Opcje kopii zapasowych |Obsługa usługi Azure Backup |Nie jest jeszcze obsługiwany |
|Opcje odzyskiwania po awarii |Obsługa usługi Azure Site Recovery |Nie jest jeszcze obsługiwany|
|Typy dysków     |Dysk SSD w warstwie Premium SSD w warstwie standardowa (wersja zapoznawcza) i standardowych dysków Twardych |Premium SSD, HDD standardowe |
|Dyski w warstwie Premium  |W pełni obsługiwane |Mogą być udostępniane, ale bez ograniczeń wydajności lub gwarancji  |
|Dyski w warstwie Premium  |Operacje We/Wy  |Zależy od rozmiaru 2300 operacje We/Wy na dysk na dysku |
|Przepływność dysków Premium |Zależy od rozmiaru dysku |145 MB na sekundę na dysk |
|Maksymalny rozmiar dysku  |4 TB       |1 TB       |
|Wydajność dysków analitycznych |Agregują metryki i metryki na dysk obsługiwane |Nie jest jeszcze obsługiwany |
|Migracja      |Zapewnia narzędzia do migracji z istniejącego niezarządzanych Menedżera zasobów maszyn wirtualnych platformy Azure bez konieczności ponowne utworzenie maszyny Wirtualnej  |Nie jest jeszcze obsługiwany |


## <a name="metrics"></a>Metryki
Istnieją różnice za pomocą metryk usługi storage:
- Dzięki usłudze Azure Stack dane transakcji w metryk magazynowania nie zróżnicować przepustowość sieci wewnętrznej lub zewnętrznej.
- Usługa Azure danych transakcji stosu w metryk magazynowania nie zawiera maszynie wirtualnej dostęp do dysków zainstalowanych.


## <a name="api-versions"></a>Wersje interfejsu API
Usługa Azure Stack Managed Disks obsługuje następujące wersje interfejsu API:
- 2017-03-30


## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o maszynach wirtualnych usługi Azure Stack](azure-stack-compute-overview.md)
