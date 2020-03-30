---
title: Jak działa migracja funkcji Hyper-V w programie Azure Migrate?
description: Dowiedz się więcej o migracji funkcji Hyper-V za pomocą usługi Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185880"
---
# <a name="how-does-hyper-v-replication-work"></a>Jak działa replikacja funkcji Hyper-V?

Ten artykuł zawiera omówienie architektury i procesów używanych podczas migracji maszyn wirtualnych funkcji Hyper-V za pomocą narzędzia migracji serwera usługi Azure Migrate Server.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdowania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmury prywatnej/publicznej na platformę Azure. Centrum udostępnia narzędzia migracji platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania innych firm (ISV).

## <a name="agentless-migration"></a>Migracja bez agenta

Narzędzie migracji serwera migracji usługi Azure udostępnia replikację bezagentową dla lokalnych maszyn wirtualnych z programem Hyper V przy użyciu przepływu pracy migracji zoptymalizowanego pod kątem funkcji Hyper-V. Agenta oprogramowania można zainstalować tylko na hostach funkcji Hyper-V lub węzłach klastra. Nic nie musi być zainstalowane na maszynach wirtualnych z programem Hyper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migracja serwera i odzyskiwanie witryny platformy Azure

Migracja serwera migracji z usługą Azure to narzędzie do migracji obciążeń lokalnych i maszyn wirtualnych opartych na chmurze na platformę Azure. Odzyskiwanie witryny jest narzędziem do odzyskiwania po awarii. Narzędzia współużytkują niektóre typowe składniki technologii używane do replikacji danych, ale służą różnym celom. 


## <a name="architectural-components"></a>Składniki architektury

![Architektura](./media/hyper-v-replication-architecture/architecture.png)



**Składnik** | **wdrażania** | 
--- | --- 
**Dostawca replikacji** | Dostawca odzyskiwania witryny platformy Microsoft Azure jest instalowany na hostach funkcji Hyper-V i zarejestrowany w usłudze Migracja serwera migracji platformy Azure.<br/> Dostawca organizuje replikację dla maszyn wirtualnych z programem Hyper-V.
**Agent usług odzyskiwania** | Agent usługi odzyskiwania platformy Microsoft Azure obsługuje replikację danych. Współpracuje z dostawcą do replikowania danych z maszyn wirtualnych z programem Hyper-V na platformie Azure.<br/> Zreplikowane dane są przekazywane do konta magazynu w ramach subskrypcji platformy Azure. Narzędzie Migracja serwera przetwarza replikowane dane i stosuje go do dysków replik w ramach subskrypcji. Dyski repliki są używane do tworzenia maszyn wirtualnych platformy Azure podczas migracji.

- Składniki są instalowane przez pojedynczy plik instalacyjny pobrany z migracji serwera migracji programu Azure w portalu.
- Dostawca i urządzenie używają wychodzących połączeń z portem HTTPS 443 do komunikowania się z migracją serwera migracji programu Azure Migrate Server.
- Komunikacja od dostawcy i agenta jest bezpieczna i szyfrowana.


## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny Wirtualnej funkcji Hyper-V rozpoczyna się replikacja początkowa.
2. Zostanie pobrana migawka maszyny wirtualnej funkcji Hyper-V.
3. VHD na maszynie Wirtualnej są replikowane jeden po drugim, dopóki nie są kopiowane na platformę Azure. Początkowy czas replikacji zależy od rozmiaru maszyny Wirtualnej i przepustowości sieci.
4. Zmiany dysku, które występują podczas replikacji początkowej są śledzone za pomocą repliki funkcji Hyper-V i przechowywane w plikach dziennika (plikach hrl).
    - Pliki dziennika znajdują się w tym samym folderze co dyski.
    - Każdy dysk ma skojarzony plik hrl, który jest wysyłany do magazynu pomocniczego.
    - Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny Wirtualnej jest usuwana i rozpoczyna się replikacja delta.
5. Przyrostowe zmiany dysku są śledzone w plikach hrl. Dzienniki replikacji są okresowo przekazywane do konta magazynu platformy Azure przez agenta usług odzyskiwania.


## <a name="performance-and-scaling"></a>Wydajność i skalowanie

Na wydajność replikacji funkcji Hyper-V mają wpływ czynniki, które obejmują rozmiar maszyny Wirtualnej, szybkość zmiany danych (zmiany) maszyn wirtualnych, dostępne miejsce na hoście funkcji Hyper-V dla magazynu plików dziennika, przepustowość przekazywania danych replikacji i magazyn docelowy na platformie Azure.

- Jeśli replikujesz wiele maszyn w tym samym czasie, użyj [programu Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) dla funkcji Hyper-V, aby zoptymalizować replikację.
- Planowanie replikacji funkcji Hyper-V i dystrybucja replikacji za pomocą kont magazynu platformy Azure zgodnie z pojemnością.

### <a name="control-upload-throughput"></a>Sterowanie przepływnością przekazywania

Można ograniczyć przepustowość używaną do przekazywania danych na platformę Azure na każdym hoście funkcji Hyper-V. Ostrożnie. Jeśli ustawisz zbyt niskie wartości, będzie to miało negatywny wpływ na replikację i opóźni migrację.


1. Zaloguj się do hosta funkcji Hyper-V lub węzła klastra.
2. Uruchom **program C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, aby otworzyć przystawkę programu MMC kopii zapasowej systemu Windows Azure.
3. W przystawce wybierz pozycję **Zmień właściwości**.
4. W **obszarze Ograniczanie**wybierz pozycję **Włącz ograniczanie przepustowości internetu w przypadku operacji tworzenia kopii zapasowych**. Ustaw limity godzin pracy i niepracy. Prawidłowe zakresy to od 512 Kb/s do 1023 Mb/s.
I

### <a name="influence-upload-efficiency"></a>Wpływ na wydajność przesyłania

Jeśli masz zapasową przepustowość dla replikacji i chcesz zwiększyć przekazywanie, możesz zwiększyć liczbę wątków przydzielonych do zadania przekazywania, w następujący sposób:

1. Otwórz rejestr za pomocą Regedit.
2. Przejdź do klawisza HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Zwiększ wartość liczby wątków używanych do przekazywania danych dla każdej replikującej maszyny Wirtualnej. Wartość domyślna to 4, a wartość maksymalna 32. 




## <a name="next-steps"></a>Następne kroki

Wypróbuj [migrację funkcji Hyper-V](tutorial-migrate-hyper-v.md) przy użyciu migracji serwera migracji usługi Azure.
