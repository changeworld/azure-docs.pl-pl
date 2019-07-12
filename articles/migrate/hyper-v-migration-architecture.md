---
title: Jak działa migracji funkcji Hyper-V z migracją serwera migracji platformy Azure? | Microsoft Docs
description: Omówienie procesu migracji funkcji Hyper-V w procesie migracji serwera migracji platformy Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811715"
---
# <a name="how-does-hyper-v-replication-work"></a>Jak działa replikacja funkcji Hyper-V?

Ten artykuł zawiera omówienie architektury i procesem stosowanym podczas migracji maszyn wirtualnych funkcji Hyper-V za pomocą narzędzia migracji serwera migracji platformy Azure.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralną Centrum do śledzenia wykrywania, oceny i migracji aplikacji lokalnych i obciążeń i chmury prywatne/publiczne maszyn wirtualnych na platformie Azure. Centrum zapewnia usługa Azure Migrate narzędzia do oceny i migracji, a także oferty Niezależnym dostawcą oprogramowania niezależnie od innych firm.

## <a name="agentless-migration"></a>Migracja bez wykorzystania agentów

Narzędzie do migracji serwera migracji platformy Azure zapewnia bez wykorzystania agentów replikacji lokalnych maszyn wirtualnych funkcji Hyper-V, za pomocą przepływu pracy migracji, które jest zoptymalizowane pod kątem funkcji Hyper-V. Agent oprogramowania jest zainstalować tylko na hostach funkcji Hyper-V lub węzłów klastra. Nic nie musi być zainstalowany na maszynach wirtualnych funkcji Hyper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migracja serwera i usługi Azure Site Recovery

Migrowanie serwera migracji na platformę Azure to narzędzie służące do migrowania obciążeń lokalnych i opartych na chmurze maszynami wirtualnymi platformy Azure. Usługa Site Recovery to narzędzie do odzyskiwania po awarii. Narzędzia udostępnianie niektóre typowe składniki technologii, które są używane na potrzeby replikacji danych, ale także służyć do różnych celów. 


## <a name="architectural-components"></a>Składniki architektury

![Architektura](./media/hyper-v-replication-architecture/architecture.png)



**Składnik** | **Wdrożenie** | 
--- | --- 
**Dostawca replikacji** | Dostawcy usługi Microsoft Azure Site Recovery jest zainstalowany na hostach funkcji Hyper-V i zarejestrowane w usłudze migracji serwera migracji platformy Azure.<br/> Dostawca koordynuje replikację dla maszyn wirtualnych funkcji Hyper-V.
**Agent usługi Recovery Services** | Agent usługi Microsoft Azure Recovery obsługuje replikację danych. Współpracuje z dostawcą replikowania danych z maszyn wirtualnych funkcji Hyper-V do platformy Azure.<br/> Replikowane dane są przekazywane na konto magazynu w ramach subskrypcji platformy Azure. Migracja serwera narzędzia procesy replikowanych danych, a dotyczy dyskach repliki w ramach subskrypcji. Dyski repliki są używane do tworzenia maszyn wirtualnych platformy Azure, podczas migracji.

- Składniki są instalowane przez plik konfiguracji pojedynczego pobrany z usługi Azure Migration migracji serwera w portalu.
- Dostawcy i urządzenia używają HTTPS port 443 połączeń wychodzących, do komunikacji z migracji serwera migracji platformy Azure.
- Komunikacja od dostawcy i agenta jest bezpieczna i szyfrowana.


## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny Wirtualnej funkcji Hyper-V, rozpoczyna się Replikacja początkowa.
2. Wykonywana jest migawka maszyny Wirtualnej funkcji Hyper-V.
3. Wirtualne dyski twarde na maszynie Wirtualnej są replikowane-przez pojedynczo, dopóki nie zostaną wszystkie skopiowane na platformę Azure. Czas replikacji początkowej, zależy od rozmiaru maszyny Wirtualnej i przepustowości sieci.
4. Za pomocą funkcji Hyper-V Replica i przechowywane w plikach dziennika (pliki hrl), są śledzone zmiany dysku, występujące podczas replikacji początkowej.
    - Pliki dziennika znajdują się w tym samym folderze co dyski.
    - Każdy dysk ma plik hrl skojarzone, który jest wysyłany do magazynu pomocniczego.
    - Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny Wirtualnej zostanie usunięta, a następnie rozpoczyna się replikacja różnicowa.
5. Dysk przyrostowe zmiany są śledzone w plikach hrl. Dzienniki replikacji są okresowo przekazywane do konta usługi Azure storage przez agenta usługi Recovery Services.


## <a name="performance-and-scaling"></a>Wydajność i skalowanie

Wydajność replikacji funkcji Hyper-v ma wpływ czynników, które zawierają rozmiar maszyny Wirtualnej, współczynnik zmian danych (liczba zmian), maszyn wirtualnych, dostępne miejsce na hoście funkcji Hyper-V do przechowywania plików dziennika, przepustowość przekazywania replikacji danych i docelowego konta magazynu na platformie Azure.

- Jeśli replikujesz wielu maszyn w tym samym czasie, użyj [planista wdrażania usługi Azure Site Recovery](../site-recovery/hyper-v-deployment-planner-overview.md) funkcji Hyper-v, aby pomóc zoptymalizować replikacji.
- Plan usługi replikacji funkcji Hyper-V i dystrybucję replikacji za pośrednictwem konta usługi Azure storage, zgodnie z pojemności.

### <a name="control-upload-throughput"></a>Przepustowość przekazywania kontroli

Można ograniczyć ilość przepustowości używanej w celu przekazywania danych do platformy Azure na każdym hoście funkcji Hyper-V. Ostrożnie. Jeśli ustawisz zbyt niskich wartości negatywnie wpłynie na replikacji i migracja opóźnienia.


1. Zaloguj się do węzła hosta lub klastra funkcji Hyper-V.
2. Uruchom **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, aby otworzyć przystawkę MMC usługi Windows Azure Backup.
3. W przystawce, wybierz **Zmień właściwości**.
4. W **ograniczania**, wybierz opcję **włączyć internetowe ograniczanie użycia przepustowości dla operacji tworzenia kopii zapasowej**. Ustaw limity dla pracy i innych niż godziny pracy. Prawidłowe zakresy są od 512 KB/s do 1,023 MB/s.
I

### <a name="influence-upload-efficiency"></a>Wydajność przekazywania wpływ

Jeśli masz nieużywanej przepustowości dla replikacji i chcesz zwiększyć przekazywania, możesz zwiększyć liczbę wątków przydzielone zadanie przekazywania w następujący sposób:

1. Otwórz Regedit.
2. Przejdź do klucza HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Zwiększ wartość przez liczbę wątków używanych w celu przekazania danych dla każdej maszyny Wirtualnej z replikacji. Wartość domyślna to 4, a wartość maksymalna to 32. 




## <a name="next-steps"></a>Kolejne kroki

Wypróbuj [migracji funkcji Hyper-V](tutorial-migrate-hyper-v.md) przy użyciu usługi Azure Migration migracji serwera.
