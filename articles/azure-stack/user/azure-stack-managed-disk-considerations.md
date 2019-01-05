---
title: Różnice i zagadnienia dotyczące dysków Managed Disks w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Poznaj różnice i zagadnienia podczas pracy z usługą Managed Disks w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: e86e1d862644aa143046045e74ec994d42a5598e
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050170"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Usługi Azure Managed Disks stosu: różnice i zagadnienia dotyczące

Ten artykuł zawiera podsumowanie znane różnice między [stosu usługi Azure Managed Disks](azure-stack-manage-vm-disks.md) i [Managed Disks na platformie Azure](../../virtual-machines/windows/managed-disks-overview.md). Aby dowiedzieć się więcej o różnicach wysokiego poziomu usługi Azure Stack i platformą Azure, zobacz [kluczowe zagadnienia](azure-stack-considerations.md) artykułu.

Usługa Managed Disks upraszcza zarządzanie dyskami dla maszyn wirtualnych IaaS dzięki zarządzaniu [kont magazynu](../azure-stack-manage-storage-accounts.md) skojarzone z dyskami maszyn wirtualnych.

> [!Note]  
> Dyski zarządzane w usłudze Azure Stack jest dostępna w wersji 1808.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Ściągawka: Różnice dysku zarządzanego

| Cecha | Azure (globalna) | Azure Stack |
| --- | --- | --- |
|Szyfrowanie danych magazynowanych |Szyfrowanie usługi Azure Storage (SSE), usługa Azure Disk Encryption (ADE)     |Funkcja BitLocker 128-bitowego szyfrowania AES      |
|Image (Obraz)          | Obsługa zarządzany obraz niestandardowy |Nie jest jeszcze obsługiwany|
|Opcje kopii zapasowych |Obsługa usługi Azure Backup |Nie jest jeszcze obsługiwany |
|Opcje odzyskiwania po awarii |Obsługa usługi Azure Site Recovery |Nie jest jeszcze obsługiwany|
|Typy dysków     |Dysk SSD w warstwie Premium SSD w warstwie standardowa (wersja zapoznawcza) i standardowych dysków Twardych |Premium SSD, HDD standardowe |
|Dyski w warstwie Premium  |W pełni obsługiwane |Mogą być udostępniane, ale bez ograniczeń wydajności lub gwarancji  |
|Dyski Premium operacje We/Wy  |Zależy od rozmiaru dysku  |2300 operacje We/Wy na dysk |
|Przepływność dysków Premium |Zależy od rozmiaru dysku |145 MB na sekundę na dysk |
|Rozmiar dysku  |Dysk Premium platformy Azure: P4 (32 GiB) do P80 (32 TiB)<br>Dysk SSD w warstwie standardowa platformy Azure: E10 (128 GiB) do E80 (32 TiB)<br>Dysk Azure standardowych dysków Twardych: S4 (32 GiB) do S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>— M30: 1024 giB |
|Kopiowanie migawki dysków|Tworzenie migawki Azure dołączone do uruchomionej maszyny Wirtualnej obsługiwane dyski zarządzane|Nie jest jeszcze obsługiwany |
|Wydajność dysków analitycznych |Agregują metryki i metryki na dysk obsługiwane |Nie jest jeszcze obsługiwany |
|Migracja      |Zapewnia narzędzia do migracji z istniejącego niezarządzanych Menedżera zasobów maszyn wirtualnych platformy Azure bez konieczności ponowne utworzenie maszyny Wirtualnej  |Nie jest jeszcze obsługiwany |

> [!NOTE]  
> Zarządzane dyski operacje We/Wy i Przepływność w usłudze Azure Stack jest liczbą limit zamiast elastycznie numer, który może wpływ na sprzęt i obciążeń działających w usłudze Azure Stack.

## <a name="metrics"></a>Metryki

Istnieją różnice za pomocą metryk usługi storage:

- Dzięki usłudze Azure Stack dane transakcji w metryk magazynowania nie odróżniają przepustowość sieci wewnętrznych lub zewnętrznych.
- Transakcji danych platformy Azure Stack w metryk usługi storage nie ma na maszynie wirtualnej dostęp do dysków zainstalowanych.

## <a name="api-versions"></a>Wersje interfejsu API

Usługa Azure Stack Managed Disks obsługuje następujące wersje interfejsu API:

- 2017-03-30

## <a name="known-issues"></a>Znane problemy

Po zastosowaniu [aktualizacji 1811](../azure-stack-update-1811.md), można napotkać następujące problemy podczas wdrażania maszyn wirtualnych z usługą Managed Disks:

- Jeśli subskrypcja została utworzona przed aktualizacją 1808, wdrażanie maszyny Wirtualnej z usługą Managed Disks może zakończyć się niepowodzeniem z komunikatem o błąd wewnętrzny. Aby naprawić błąd, wykonaj następujące kroki dla każdej subskrypcji:
   1. W portalu dzierżawcy, przejdź do **subskrypcje** i Znajdź subskrypcji. Kliknij przycisk **dostawców zasobów**, następnie kliknij przycisk **Microsoft.Compute**, a następnie kliknij przycisk **ponownie zarejestrować**.
   2. W ramach tej samej subskrypcji, przejdź do **kontrola dostępu (IAM)** i upewnij się, że **usługi Azure Stack — dysk zarządzany** znajduje się na liście.
- Po skonfigurowaniu środowiska z wieloma dzierżawami wdrażania maszyn wirtualnych w ramach subskrypcji, skojarzony z katalogiem gościa może zakończyć się komunikat o błędzie wewnętrznym. Aby naprawić błąd, wykonaj następujące kroki w [w tym artykule](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) Aby zmienić konfigurację wszystkich katalogów gościa.


## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o maszynach wirtualnych usługi Azure Stack](azure-stack-compute-overview.md)
