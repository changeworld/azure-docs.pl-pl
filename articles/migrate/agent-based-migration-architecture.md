---
title: Migracja oparta na agentach w migracji serwera migracji usługi Azure
description: Zawiera omówienie migracji maszyn wirtualnych VMware oparte na agentach w usłudze Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425865"
---
# <a name="agent-based-migration-architecture"></a>Architektura migracji z użyciem agentów

Ten artykuł zawiera omówienie architektury i procesów używanych do replikacji maszyn wirtualnych VMware na podstawie agenta za pomocą narzędzia [Azure Migrate: Server Migration.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

Korzystając z migracji platformy Azure: migracja serwera, można replikować maszyny wirtualne VMware za pomocą kilku opcji:

- Migrowanie maszyn wirtualnych przy użyciu replikacji opartej na agentach, zgodnie z opisem w tym artykule.
- Migrowanie maszyn wirtualnych VMware przy użyciu replikacji bez agenta. Spowoduje to migrację maszyn wirtualnych bez konieczności instalowania na nich niczego.

Dowiedz się więcej o [wybieraniu i porównywaniu](server-migrate-overview.md) metod migracji maszyn wirtualnych VMware. 


## <a name="agent-based-migration"></a>Migracja oparta na agentach

Migracja oparta na agentach jest używana do migracji lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Może również służyć do migracji innych zwirtualizowanych serwerów lokalnych, a także maszyn wirtualnych w chmurze prywatnej i publicznej, w tym wystąpień AWS i maszyn wirtualnych GCP. Migracja oparta na agentach w usłudze Azure Migrate korzysta z niektórych funkcji zaplecza z usługi [Azure Site Recovery.](../site-recovery/site-recovery-overview.md)


## <a name="architectural-components"></a>Składniki architektury

Diagram ilustruje składniki zaangażowane w migrację opartą na agentach.

![Architektura](./media/agent-based-replication-architecture/architecture.png)

W tabeli podsumowano składniki używane do migracji opartej na agentach.

**Składnik** | **Szczegóły** | **Instalacji**
--- | --- | ---
**Urządzenie replikacji** | Urządzenie replikacji (serwer konfiguracji/serwer przetwarzania) jest komputerem lokalnym, który działa jako most między środowiskiem lokalnym a migracją serwera. Urządzenie odnajduje lokalne spisy komputerów, dzięki czemu migracja serwera może aranżować replikację i migrację. Urządzenie posiada dwa elementy:<br/><br/> **Serwer konfiguracji**: Łączy się z migracją serwera i koordynuje replikację.<br/> **Serwer przetwarzania:** Obsługuje replikację danych. Serwer przetwarzania odbiera dane komputera, kompresuje i szyfruje je i wysyła na platformę Azure. Na platformie Azure migracja serwera zapisuje dane na dyskach zarządzanych. | Domyślnie serwer przetwarzania jest instalowany razem z serwerem konfiguracji urządzenia replikacji.
**Usługa mobilności** | Usługa mobilności jest agentem zainstalowanym na każdym komputerze, który ma być replikowany i migrowany. Wysyła dane replikacji z komputera do serwera przetwarzania. | Pliki instalacyjne dla różnych wersji usługi mobilności znajdują się na urządzeniu replikacji. Pobierasz i instalujesz potrzebny agent, zgodnie z systemem operacyjnym i wersją komputera, który chcesz replikować.

## <a name="mobility-service-installation"></a>Instalacja usługi mobilności

Usługę mobilności można wdrożyć przy użyciu następujących metod:

- **Instalacja**wypychająca: Usługa mobilności jest instalowana przez serwer przetwarzania po włączeniu ochrony komputera. 
- **Zainstaluj ręcznie:** Usługę mobilności można zainstalować ręcznie na każdym komputerze za pomocą interfejsu użytkownika lub wiersza polecenia.

Usługa mobilności komunikuje się z urządzeniem replikacji i maszynami replikowanymi. Jeśli na urządzeniu replikacji, serwerach przetwarzania lub maszynach replikowanych jest uruchomione oprogramowanie antywirusowe, skanowanie należy wykluczyć następujące foldery:


- C:\Pliki programów\Agent usług odzyskiwania platformy Microsoft Azure
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LoguploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Pliki programów (x86)\Odzyskiwanie witryny platformy Microsoft Azure
- C:\ProgramData\ASR\agent (na komputerach z systemem Windows z zainstalowaną usługą mobilności)

## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla komputera rozpoczyna się replikacja początkowa na platformie Azure.
2. Podczas replikacji początkowej usługa mobilności odczytuje dane z dysków komputera i wysyła je do serwera przetwarzania.
3. Te dane są używane do inicjatora kopię dysku w subskrypcji platformy Azure. 
4. Po zakończeniu replikacji początkowej rozpoczyna się replikacja zmian różnicowych na platformie Azure. Replikacja jest na poziomie bloku i prawie ciągła.
4. Usługa Mobilności przechwytuje zapisy w pamięci dysku, integrując się z podsystemem magazynu systemu operacyjnego. Ta metoda pozwala uniknąć operacji we/wy dysku na komputerze replikującym, dla replikacji przyrostowej. 
5. Śledzone zmiany dla komputera są wysyłane do serwera przetwarzania na porcie przychodzącym HTTPS 9443. Ten port można modyfikować. Serwer przetwarzania kompresuje i szyfruje go i wysyła do platformy Azure. 

## <a name="ports"></a>Porty

**Urządzenie** | **Połączenia**
--- | --- 
**Maszyny replikujące** | Usługa mobilności uruchomiona na maszynach wirtualnych komunikuje się z lokalnym urządzeniem replikacji na porcie HTTPS 443 przychodzącym w celu zarządzania replikacją.<br/><br/> Maszyny wysyłają dane replikacji do serwera przetwarzania na porcie HTTPS 9443 przychodzącym. Ten port można modyfikować.
**Urządzenie replikacji** | Urządzenie replikacji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 wychodzącego.
**Serwer przetwarzania** | Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do magazynu platformy Azure za pomocą portu 443 wychodzącego.


## <a name="performance-and-scaling"></a>Wydajność i skalowanie

Domyślnie można wdrożyć pojedyncze urządzenie replikacji, które uruchamia zarówno serwer konfiguracji, jak i serwer przetwarzania. Jeśli replikujesz tylko kilka maszyn, to wdrożenie jest wystarczające. Jednak jeśli replikujesz i migrujesz setki komputerów, pojedynczy serwer przetwarzania może nie być w stanie obsłużyć całego ruchu replikacji. W takim przypadku można wdrożyć dodatkowe serwery procesów skalowanych w poziomie.

### <a name="plan-vmware-deployment"></a>Planowanie wdrożenia VMware

W przypadku replikowania maszyn wirtualnych VMware można użyć [planisty wdrażania odzyskiwania witryny dla oprogramowania VMware,](../site-recovery/site-recovery-deployment-planner.md)aby określić wymagania dotyczące wydajności, w tym dzienną szybkość zmiany danych i potrzebne serwery przetwarzania.

### <a name="replication-appliance-capacity"></a>Pojemność urządzenia replikacji

Użyj wartości w tej tabeli, aby dowiedzieć się, czy potrzebujesz dodatkowego serwera przetwarzania w wdrożeniu.

- Jeśli dzienna szybkość zmiany (współczynnik zmian) wynosi ponad 2 TB, wdrożyć dodatkowy serwer procesów.
- Jeśli replikujesz więcej niż 200 maszyn, wdrożyć dodatkowe urządzenie replikacji.

**Procesora** | **Pamięci** | **Buforowanie wolnych danych o wolnych przestrzeniach** | **Współczynnik zmian** | **Limity replikacji**
--- | --- | --- | --- | ---
8 procesorów wirtualnych (2 gniazda \@ * 4 rdzenie 2,5 GHz) | 16 GB | 300 GB | 500 GB lub mniej | < 100 maszyn 
12 procesorów wirtualnych (2 gniazda \@ * 6 rdzeni 2,5 GHz) | 18 GB | 600 GB | 501 GB do 1 TB | 100-150 maszyn.
16 procesorów wirtualnych (2 gniazda \@ * 8 rdzeni 2,5 GHz) | 32 G1 |  1 TB | 1 TB do 2 TB | 151-200 maszyn.

### <a name="sizing-scale-out-process-servers"></a>Skalowanie w poziomie rozmiaru serwerów procesów

Jeśli musisz wdrożyć serwer procesów skalowanych w poziomie, użyj tej tabeli, aby dowiedzieć się, jak doszła do rozmiaru serwera.

**Serwer przetwarzania** | **Wolne miejsce na buforowanie danych** | **Współczynnik zmian** | **Limity replikacji**
--- | --- | --- | --- 
4 procesory wirtualne (2 gniazda \@ * 2 rdzenie 2,5 GHz), pamięć 8 GB | 300 GB | 250 GB lub mniej | Do 85 maszyn 
8 procesorów wirtualnych (2 gniazda \@ * 4 rdzenie 2,5 GHz), 12 GB pamięci | 600 GB | 251 GB do 1 TB    | 86-150 maszyn.
12 procesorów wirtualnych (2 gniazda \@ * 6 rdzeni 2,5 GHz), pamięć 24 GB | 1 TB | 1-2 TB | 151-225 maszyn.

## <a name="throttle-upload-bandwidth"></a>Przepustowość przesyłania przepustnicy.

Ruch VMware, który replikuje się na platformę Azure przechodzi przez serwer określonego procesu. Przepływność przekazywania można ograniczyć, ograniczając przepustowość na komputerach, które są uruchomione jako serwery przetwarzania. Za pomocą tego klucza rejestru można wpływać na przepustowość:

- Wartość rejestru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM określa liczbę wątków używanych do przesyłania danych (replikacja początkowa lub delta) dysku. Wyższa wartość zwiększa przepustowość sieci, która jest używana do replikacji. Wartość domyślna to cztery. Maksymalna wartość wynosi 32. Monitoruj ruch, aby zoptymalizować tę wartość.
- Ponadto przepustowość serwera przetwarzania można ograniczyć w następujący sposób:

    1. Na komputerze serwera przetwarzania otwórz przystawkę programu MMC kopii zapasowej platformy Azure. Istnieje skrót na pulpicie lub w folderze C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. W przystawce wybierz pozycję **Zmień właściwości**.
    3. W **obszarze Ograniczanie**wybierz pozycję **Włącz ograniczanie przepustowości internetu w przypadku operacji tworzenia kopii zapasowych**. Ustaw limity godzin pracy i niepracy. Prawidłowe zakresy to od 512 Kb/s do 1023 Mb/s.


## <a name="next-steps"></a>Następne kroki

Wypróbuj [migrację opartą na agentach](tutorial-migrate-vmware-agent.md) dla [serwerów VMware](tutorial-migrate-vmware-agent.md) lub [fizycznych](tutorial-migrate-physical-virtual-machines.md).
