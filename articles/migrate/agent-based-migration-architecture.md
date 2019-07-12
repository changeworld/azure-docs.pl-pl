---
title: Architektury oparte na agentach migracji w procesie migracji serwera migracji platformy Azure
description: Omówienie migracji opartej na agenta maszyny Wirtualnej VMware, za pomocą usługi Azure Migration migracji serwera.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 21c779587842c976ba93d7fa592a91ee714bc55c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811156"
---
# <a name="agent-based-migration-architecture"></a>Architekturę migracji opartej o agentów

Ten artykuł zawiera omówienie architektury i procesów używanych w przypadku opartej o agentów replikacji za pomocą narzędzia migracji serwera migracji platformy Azure.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralną Centrum do śledzenia odnajdywania, oceny i migracji aplikacji lokalnych i obciążeń i wystąpień maszyn wirtualnych usług AWS/GCP, na platformie Azure. Centrum zapewnia usługa Azure Migrate narzędzia do oceny i migracji, a także oferty Niezależnym dostawcą oprogramowania niezależnie od innych firm.

## <a name="agent-based-replication"></a>Replikacja oparta na agenta

Oparte na agentach replikacji w usłudze Azure migracji serwera replikacji narzędzie służy do migrowania lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Może również służyć do migracji innych serwerów w środowisku lokalnym wirtualizacji, a także chmur prywatnych i publicznych maszyn wirtualnych, w tym wystąpień usługi AWS i GCP maszyn wirtualnych.

W przypadku migracji z programu VMware narzędzie do migracji serwera migracji platformy Azure oferuje kilka opcji:

- Migracja za pomocą replikacji opartej o agentów, zgodnie z opisem w tym artykule.
- Bez wykorzystania agentów replikacji do migrowania maszyn wirtualnych bez konieczności instalowania żadnych narzędzi na nich.

Dowiedz się więcej o [wybierania metody migracji dla programu VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Migracja serwera i usługi Azure Site Recovery

Migrowanie serwera migracji na platformę Azure to narzędzie służące do migrowania lokalnych i obciążeń w chmurze publicznej Azure. Jest on zoptymalizowany pod kątem migracji. Usługa Site Recovery to narzędzie do odzyskiwania po awarii. Usługa Azure migracji serwera i Site Recovery mają niektóre typowe składniki technologii, które są używane na potrzeby replikacji danych, ale służyć do różnych celów.

## <a name="architectural-components"></a>Składniki architektury

![Architektura](./media/agent-based-replication-architecture/architecture.png)

W tabeli przedstawiono składniki używane do migracji opartej o agentów.

**Składnik** | **Szczegóły** | **Instalacja**
--- | --- | ---
**Urządzenie replikacji** | Urządzenie replikacji (serwer konfiguracji) jest maszynie lokalnej, która działa jako Most między środowiskiem lokalnym i narzędzia do migracji serwera migracji platformy Azure. Urządzenie umożliwia odnalezienie magazynu maszyny Wirtualnej w środowisku lokalnym, więc, że migracja serwera usługi Azure można organizować replikację i migracji. Urządzenie ma dwa składniki:<br/><br/> **Serwer konfiguracji**: Nawiązanie połączenia z usługi Azure Migration migracji serwera i koordynuje replikację.<br/> **Serwer przetwarzania**: Obsługuje replikację danych. Go odbiera dane maszyny Wirtualnej, kompresuje szyfruje je i wysyła do subskrypcji platformy Azure. Migracja serwera zapisuje dane do usługi managed disks. | Domyślnie serwer przetwarzania jest instalowany razem z serwerem konfiguracji na urządzenie replikacji.
**Usługa mobilności** | Usługa mobilności jest zainstalowana na każdej maszynie, którą chcesz replikować, a następnie przeprowadzić migrację agenta. Wysyła dane replikacji z komputera do serwera przetwarzania. Istnieje szereg różnych agenci usługi mobilności są dostępne. | Pliki instalacji usługi mobilności znajdują się na urządzeniu replikacji. Pobierz i zainstaluj agenta, których potrzebujesz, zgodnie z systemu operacyjnego i wersji maszyny, którą chcesz replikować.

### <a name="mobility-service-installation"></a>Instalacja usługi mobilności

Można wdrożyć usługę mobilności, za pomocą następujących metod:

- **Instalacja wypychana**: Usługa mobilności jest zainstalowana za pomocą serwera przetwarzania po włączeniu ochrony dla maszyny. 
- **Zainstaluj ręcznie**: Usługa mobilności można zainstalować ręcznie na każdym komputerze za pośrednictwem interfejsu użytkownika lub wiersza polecenia.

Usługa mobilności komunikuje się z urządzeniem replikacji i replikowane maszyny. Jeśli na urządzeniu replikacji, serwerów przetwarzania lub replikowane maszyny jest uruchomione oprogramowanie antywirusowe, następujące foldery powinny wykluczone ze skanowania:


- Agent usług C:\Program Files\Microsoft Azure Recovery Services
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (na maszynach Windows z zainstalowaną usługę mobilności)

## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny Wirtualnej, rozpoczyna się Replikacja początkowa na platformie Azure.
2. Podczas replikacji początkowej usługi mobilności odczytuje dane z dysków maszyny i wysyła je do serwera przetwarzania.
3. Te dane są używane w celu umieszczenia kopii dysku w ramach subskrypcji platformy Azure. 
4. Po zakończeniu replikacji początkowej rozpoczyna się replikacja zmian różnicowych do platformy Azure. Replikacja jest na poziomie bloku i w ciągły.
4. Przechwytuje usługi mobilności zapisuje w pamięci dysku maszyny Wirtualnej, dzięki integracji z usługą podsystemu magazynowania, systemu operacyjnego. Ta metoda umożliwia uniknięcie operacji We/Wy dysku na maszynie replikującej replikacji przyrostowej. 
5. Śledzone zmiany dla maszyny wysyłanych do serwera przetwarzania na porcie HTTPS 9443 dla ruchu przychodzącego. Ten port może być modyfikowany. Serwer przetwarzania kompresuje szyfruje je i wysyła je do platformy Azure. 

## <a name="ports"></a>Porty

**urządzenia** | **połączenia**
--- | --- 
Maszyny wirtualne | Usługi mobilności uruchomioną na maszynach wirtualnych komunikuje się z urządzeniem replikacji lokalnej na porcie HTTPS 443 dla ruchu przychodzącego na potrzeby zarządzania replikacją.<br/><br/> Maszyny wirtualne wysyłają dane replikacji do serwera procesu (działającej na urządzeniu replikacji domyślnie) na porcie HTTPS 9443 dla ruchu przychodzącego. Ten port może być modyfikowany.
Urządzenie replikacji | Urządzenie replikacji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego.
Serwer przetwarzania | Serwer przetwarzania odbiera dane replikacji, optymalizuje je szyfruje i wysyła je do usługi Azure storage za pośrednictwem portu 443 wychodzących.


## <a name="performance-and-scaling"></a>Wydajność i skalowanie

Domyślnie możesz wdrożyć urządzenie pojedynczej replikacji, które działa zarówno na serwerze konfiguracji, jak i serwera przetwarzania. Jeśli replikujesz tylko kilka maszyny, to wdrożenie jest wystarczająca. Jednak jeśli replikacji i migracja setki maszyn, serwer pojedynczego procesu nie można obsłużyć całego ruchu replikacji. W takim przypadku można wdrożyć serwery dodatkowych, skalowalnego w poziomie procesu.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Planista wdrażania usługi Site Recovery dla oprogramowania VMware

Jeśli replikujesz maszyny wirtualne VMware, możesz użyć [planista wdrażania usługi Site Recovery](../site-recovery/site-recovery-deployment-planner.md) dla replikacji VMware w celu określenia wymagań dotyczących wydajności, w tym dane dotyczące dziennego zmienić szybkość i potrzebnych serwerów procesu.

### <a name="replication-appliance-capacity"></a>Pojemność urządzenia replikacji

Wartości w tej tabeli można ustalić, czy potrzebujesz dodatkowym serwerze przetwarzania w danym wdrożeniu.

- Jeśli dziennych zmian (współczynnik zmian danych) jest ponad 2 TB, należy wdrożyć dodatkowym serwerze przetwarzania.
- Jeśli replikujesz ponad 200 maszyn, należy wdrożyć urządzenie dodatkowa replikacja.

**CPU** | **Pamięć** | **Ilość wolnego miejsca dla pamięci podręcznej danych** | **Współczynnik zmian danych** | **Ograniczenia replikacji**
--- | --- | --- | --- | ---
8 wirtualnych procesorów CPU (2 sockets * 4 rdzenie \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB lub mniej | < 100 maszyn 
12 procesorów wirtualnych Vcpu (2 sockets * 6 rdzeni \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB do 1 TB | 100-150 maszyny.
16 procesorów wirtualnych Vcpu (2 sockets * 8 rdzeni \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB do 2 TB | 151 200 maszyny.

### <a name="scale-out-process-server-sizing"></a>Określania rozmiaru serwera przetwarzania skalowalnego w poziomie

Jeśli zajdzie potrzeba wdrożenia serwera przetwarzania skalowalnego w poziomie, ta tabela może pomóc ustalić określania rozmiaru serwera.

**Serwer przetwarzania** | **Ilość wolnego miejsca dla pamięci podręcznej danych** | **Współczynnik zmian danych** | **Ograniczenia replikacji**
--- | --- | --- | --- 
4 Vcpu (2 sockets * 2 rdzenie \@ 2,5 GHz), 8 GB pamięci RAM | 300 GB | 250 GB lub mniej | Maksymalnie 85 maszyn 
8 wirtualnych procesorów CPU (2 sockets * 4 rdzenie \@ 2,5 GHz), 12 GB pamięci RAM | 600 GB | 251 GB do 1 TB    | 86 150 maszyny.
12 procesorów wirtualnych Vcpu (2 sockets * 6 rdzeni \@ 2,5 GHz), 24 GB pamięci | 1 TB | 1-2 TB | 151 225 maszyny.

## <a name="control-upload-throughput"></a>Przepustowość przekazywania kontroli

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

Wypróbuj oparte na agentach [migracji maszyny Wirtualnej VMware](tutorial-migrate-vmware-agent.md) przy użyciu usługi Azure Migration migracji serwera.
