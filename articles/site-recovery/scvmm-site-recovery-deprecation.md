---
title: Nadchodzące wycofanie firmy DR między lokacjami należącymi do klienta przy użyciu funkcji Hyper-V i między lokacjami zarządzanymi przez program SCVMM na platformie Azure | Microsoft Docs
description: Szczegółowe informacje o nadchodzącym zaniechaniu odzyskiwania po awarii między lokacjami należącymi do klienta przy użyciu funkcji Hyper-V i między lokacjami zarządzanymi przez program SCVMM a opcją alternatywną
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: 972223815810917684f35c4e99f04e1ab5b882c8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952123"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>Nadchodzące wycofanie firmy DR między lokacjami należącymi do klienta przy użyciu funkcji Hyper-V i między lokacjami zarządzanymi przez program SCVMM na platformie Azure

W tym artykule opisano nadchodzący plan wycofania, odpowiednie implikacje i alternatywne opcje dostępne dla klientów w następujących scenariuszach, które były obsługiwane przez Azure Site Recovery. 

- Program DR między maszynami wirtualnymi funkcji Hyper-V zarządzanymi przez program SCVMM między lokacjami należącymi do klienta 
- Odzyskiwanie maszyn wirtualnych funkcji Hyper-V zarządzanych przez program SCVMM do platformy Azure

> [!IMPORTANT]
> Te scenariusze są obecnie oznaczone jako przestarzałe, a klienci oczekują czynności zaradczych, aby uniknąć przerw w działaniu środowiska. 
 

## <a name="what-changes-should-you-expect"></a>Jakie zmiany należy oczekiwać?

- Od listopada 2019 do tych scenariuszy nie będą dozwolone żadne nowe użytkownika. **Istniejące replikacje i operacje zarządzania** , w tym przełączanie do trybu failover, Testowanie pracy w trybie failover, monitorowanie itp. **nie będzie miało wpływu**.

- Gdy scenariusze są przestarzałe, będą one miały następujące konsekwencje, chyba że klient postępuje zgodnie z zalecanymi krokami.

    - Program DR między maszynami wirtualnymi funkcji Hyper-V zarządzanymi przez program SCVMM między lokacjami należącymi do klienta: replikacja będzie nadal działać, ponieważ podstawowa możliwość funkcji Hyper-V Replica będzie nadal działać, ale klienci nie będą mogli wyświetlać ani wykonywać operacji związanych z odzyskiwaniem za pomocą narzędzia DR. za pośrednictwem środowiska odzyskiwania Azure Sire w Azure Portal. 
    - Odzyskiwanie maszyn wirtualnych funkcji Hyper-V zarządzanych przez program SCVMM do platformy Azure: istniejące replikacje zostaną zakłócone, a klienci nie będą mogli wyświetlać ani wykonywać żadnych operacji związanych z odzyskiwaniem za pośrednictwem programu przy użyciu Azure Site Recovery


## <a name="recommended-actions-to-be-taken"></a>Zalecane akcje do wykonania

Poniżej znajdują się alternatywne opcje, które klient musi zapewnić, że nie ma to wpływu na strategię odzyskiwania po wycofaniu scenariusza. 

- Wybierz, aby [rozpocząć korzystanie z platformy Azure jako elementu docelowego odzyskiwania maszyn wirtualnych na hostach funkcji Hyper-V](hyper-v-azure-tutorial.md).

> [!IMPORTANT]
> Należy pamiętać, że środowisko lokalne może nadal mieć SCVMMM, ale skonfigurujesz funkcję ASR z odwołaniami tylko do hostów funkcji Hyper-V.

- Wybierz, aby kontynuować replikację między lokacjami, ale używając bazowego [rozwiązania funkcji Hyper-V Replica](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), ale nie będzie można zarządzać konfiguracjami Dr przy użyciu Azure Site Recovery w Azure Portal. 


## <a name="next-steps"></a>Następne kroki
Zaplanuj wycofanie i wybierz opcję alternatywną, która jest najbardziej odpowiednia dla infrastruktury i firmy. Jeśli masz jakieś zapytania dotyczące tego, skontaktuj się z pomoc techniczna firmy Microsoft

