---
title: Migracja oparta na agencie w migracji Azure Migrate serwera
description: Zawiera omówienie migracji maszyn wirtualnych VMware opartych na agentach z migracją na serwer Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: a8477b4c10ccbc76f36eed4d64ac12e8bb648a28
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186106"
---
# <a name="agent-based-migration-architecture"></a>Architektura migracji z użyciem agentów

Ten artykuł zawiera omówienie architektury i procesów używanych do replikacji opartej na agentach za pomocą narzędzia migracji Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz wystąpień maszyn wirtualnych AWS/GCP na platformę Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

## <a name="agent-based-replication"></a>Replikacja oparta na agentach

W celu migrowania lokalnych maszyn wirtualnych programu VMware i serwerów fizycznych do platformy Azure jest używana replikacja oparta na agencie w narzędziu replikacji Azure Migrate Server. Może również służyć do migrowania innych lokalnych serwerów zwirtualizowanych, a także maszyn wirtualnych chmur prywatnych i publicznych, w tym wystąpień AWS i maszyn wirtualnych GCP.

W przypadku migracji oprogramowania VMware narzędzie do migracji Azure Migrate Server oferuje kilka opcji:

- Migracja przy użyciu replikacji opartej na agentach, zgodnie z opisem w tym artykule.
- Replikacja bezagentowa do migracji maszyn wirtualnych bez konieczności instalacji jakichkolwiek elementów.

Dowiedz się więcej na temat [wybierania metody migracji dla programu VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Migracja serwera i Azure Site Recovery

Migracja serwera Azure Migrate to narzędzie służące do migrowania obciążeń lokalnych i publicznych w chmurze na platformę Azure. Jest zoptymalizowany pod kątem migracji. Site Recovery to narzędzie odzyskiwania po awarii. Migracja serwera platformy Azure i Site Recovery udostępniają niektóre typowe składniki technologii używane do replikacji danych, ale służą do różnych celów.

## <a name="architectural-components"></a>Składniki architektury

![Architektura](./media/agent-based-replication-architecture/architecture.png)

W tabeli zestawiono składniki używane do migracji opartej na agentach.

**Składnik** | **Szczegóły** | **Instalacja**
--- | --- | ---
**Urządzenie replikacji** | Urządzenie replikacji (serwer konfiguracji) jest maszyną lokalną, która działa jako Most między środowiskiem lokalnym i narzędziem migracji Azure Migrate Server. Urządzenie odnajduje spis lokalnych maszyn wirtualnych, dzięki czemu Migracja serwera Azure może organizować replikację i migrację. Urządzenie ma dwa składniki:<br/><br/> **Serwer konfiguracji**: nawiązuje połączenie z migracją serwera Azure Migrate i koordynuje replikację.<br/> **Serwer przetwarzania**: obsługuje replikację danych. Odbiera dane dotyczące maszyn wirtualnych, kompresuje je i szyfruje oraz wysyła do subskrypcji platformy Azure. W tym przypadku Migracja serwera zapisuje dane na dyskach zarządzanych. | Domyślnie serwer przetwarzania jest instalowany razem z serwerem konfiguracji na urządzeniu replikacji.
**Usługa mobilności** | Usługa mobilności jest agentem zainstalowanym na każdej maszynie, która ma być replikowana i migrowana. Wysyła dane replikacji z komputera do serwera przetwarzania. Dostępnych jest wiele różnych agentów usługi mobilności. | Pliki instalacyjne usługi mobilności znajdują się na urządzeniu replikacji. Należy pobrać i zainstalować agenta, którego potrzebujesz, zgodnie z systemem operacyjnym i wersją maszyny, która ma być replikowana.

### <a name="mobility-service-installation"></a>Instalacja usługi mobilności

Usługę mobilności można wdrożyć przy użyciu następujących metod:

- **Instalacja wypychana**: usługa mobilności jest instalowana przez serwer przetwarzania po włączeniu ochrony dla komputera. 
- **Zainstaluj ręcznie**: usługę mobilności można zainstalować ręcznie na każdym komputerze za pomocą interfejsu użytkownika lub wiersza polecenia.

Usługa mobilności komunikuje się z urządzeniem replikacji i replikowanymi maszynami. W przypadku oprogramowania antywirusowego uruchomionego na urządzeniu replikacji, serwerów przetwarzania lub replikowanych maszyn należy wykluczyć następujące foldery z skanowania:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (na maszynach z systemem Windows z zainstalowaną usługą mobilności)

## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny wirtualnej rozpocznie się replikacja początkowa na platformę Azure.
2. Podczas replikacji początkowej usługa mobilności odczytuje dane z dysków komputera i wysyła je do serwera przetwarzania.
3. Te dane są używane do wypełniania kopii dysku w ramach subskrypcji platformy Azure. 
4. Po zakończeniu replikacji początkowej zostanie rozpoczęta replikacja zmian różnicowych na platformie Azure. Replikacja jest na poziomie bloku i blisko ciągła.
4. Usługa mobilności przechwytuje operacje zapisu do pamięci dysku maszyny wirtualnej przez integrację z podsystemem magazynowania systemu operacyjnego. Ta metoda pozwala uniknąć operacji we/wy dysku na maszynie replikacji przyrostowej. 
5. Śledzone zmiany dla maszyny są wysyłane do serwera przetwarzania na porcie HTTPS 9443. Ten port może być modyfikowany. Serwer przetwarzania kompresuje i szyfruje go i wysyła do platformy Azure. 

## <a name="ports"></a>Porty

**urządzenia** | **połączenia**
--- | --- 
Maszyny wirtualne | Usługa mobilności działająca na maszynach wirtualnych komunikuje się z lokalnym urządzeniem do replikacji na porcie HTTPS 443 ruchu przychodzącego na potrzeby zarządzania replikacją.<br/><br/> Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (domyślnie uruchomione na urządzeniu replikacji) na porcie HTTPS 9443 w ruchu przychodzącym. Ten port może być modyfikowany.
Urządzenie replikacji | Urządzenie replikacji organizuje replikację za pomocą platformy Azure przez port HTTPS 443.
Serwer przetwarzania | Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage przez port 443 wychodzące.


## <a name="performance-and-scaling"></a>Wydajność i skalowanie

Domyślnie wdrażane jest pojedyncze urządzenie do replikacji, na którym działa serwer konfiguracji i serwer przetwarzania. W przypadku replikowania tylko kilku maszyn to wdrożenie jest wystarczające. Jednak w przypadku replikowania i migrowania setek maszyn jeden serwer przetwarzania może nie być w stanie obsłużyć całego ruchu związanego z replikacją. W takim przypadku można wdrożyć dodatkowe serwery przetwarzania skalowalnego w poziomie.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Site Recovery Planista wdrażania dla oprogramowania VMware

Jeśli replikujesz maszyny wirtualne VMware, możesz użyć [Planista wdrażania Site Recovery](../site-recovery/site-recovery-deployment-planner.md) dla programu VMware, aby pomóc określić wymagania dotyczące wydajności, w tym dzienny współczynnik zmian danych i serwery przetwarzania, których potrzebujesz.

### <a name="replication-appliance-capacity"></a>Pojemność urządzenia replikacji

Wartości w tej tabeli mogą służyć do ustalenia, czy potrzebujesz dodatkowego serwera przetwarzania w danym wdrożeniu.

- Jeśli dzienny współczynnik zmian (tempo zmiany) przekracza 2 TB, wdróż dodatkowy serwer przetwarzania.
- W przypadku replikowania więcej niż 200 maszyn należy wdrożyć dodatkowe urządzenie do replikacji.

**CPU** | **Pamięć** | **Wolne miejsce dla buforowania danych** | **Współczynnik zmian** | **Limity replikacji**
--- | --- | --- | --- | ---
8 procesorów wirtualnych vCPU (2 gniazda * 4 rdzenie \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB lub mniej | Maszyny < 100 
12 procesorów wirtualnych vCPU (2 gniazda * 6 rdzeni \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB do 1 TB | 100-150 maszyn.
16 procesorów wirtualnych vCPU (2 gniazda * 8 rdzeni \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB do 2 TB | 151-200 maszyn.

### <a name="scale-out-process-server-sizing"></a>Ustalanie rozmiaru serwera przetwarzania skalowalnego w poziomie

Jeśli zachodzi potrzeba wdrożenia serwera przetwarzania skalowalnego w poziomie, ta tabela pomoże Ci ustalić rozmiar serwera.

**Serwer przetwarzania** | **Wolne miejsce dla buforowania danych** | **Współczynnik zmian** | **Limity replikacji**
--- | --- | --- | --- 
4 procesorów wirtualnych vCPU (2 gniazda * 2 rdzenie \@ 2,5 GHz), 8 GB pamięci | 300 GB | 250 GB lub mniej | Do 85 maszyn 
8 procesorów wirtualnych vCPU (2 gniazda * 4 rdzenie \@ 2,5 GHz), 12 GB pamięci | 600 GB | 251 GB do 1 TB    | 86-150 maszyn.
12 procesorów wirtualnych vCPU (2 gniazda * 6 rdzeni \@ 2,5 GHz), 24 GB pamięci | 1 TB | 1-2 TB | 151-225 maszyn.

## <a name="control-upload-throughput"></a>Przepływność przekazywania kontroli


 Ruch VMware replikowany do platformy Azure odbywa się za pomocą określonego serwera przetwarzania. Przepływność przekazywania można ograniczyć, ograniczając przepustowość na komputerach, na których działają jako serwery przetwarzania. Korzystając z tego klucza rejestru, można mieć wpływ na przepustowość:

- Wartość rejestru HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM określa liczbę wątków używanych na potrzeby transferu danych (replikacja początkowa lub różnicowa) dysku. Wyższa wartość zwiększa przepustowość sieci używaną podczas replikacji. Wartość domyślna to 4. Wartość maksymalna to 32. Monitoruj ruch, aby zoptymalizować tę wartość.
- Ponadto można ograniczyć przepustowość na komputerze serwera przetwarzania w następujący sposób:

    1. Na komputerze serwera przetwarzania Otwórz przystawkę MMC Azure Backup. Istnieje skrót na pulpicie lub w folderze C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. W przystawce wybierz pozycję **Zmień właściwości**.
    3. W obszarze **ograniczenie przepustowości**wybierz opcję **Włącz ograniczenie przepustowości Internetu dla operacji tworzenia kopii zapasowej**. Ustaw limity dla godzin pracy i czasu wolnego. Prawidłowe zakresy są z zakresu od 512 KB/s do 1 023 MB/s.


## <a name="next-steps"></a>Kolejne kroki

Wypróbuj [migrację maszyny wirtualnej VMware](tutorial-migrate-vmware-agent.md) opartą na agencie przy użyciu migracji serwera Azure Migrate.
