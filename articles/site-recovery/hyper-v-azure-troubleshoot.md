---
title: Rozwiązywanie problemów z odzyskiwaniem po awarii funkcji Hyper-V za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób rozwiązywania problemów z odzyskiwaniem po awarii za pomocą replikacji funkcji Hyper-V na platformie Azure przy użyciu usługi Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 0a3e5c922009353e4ba9ccab12cf70ea2b5992da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961482"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Rozwiązywanie problemów z replikacją i przełączaniem w tryb failover z funkcji Hyper-V do platformy Azure

W tym artykule opisano typowe problemy, które mogą wystąpić podczas replikowania lokalnych maszyn wirtualnych z programem Hyper V na platformę Azure przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)

## <a name="enable-protection-issues"></a>Włącz problemy z ochroną

Jeśli wystąpią problemy po włączeniu ochrony maszyn wirtualnych z programem Hyper-V, zapoznaj się z następującymi zaleceniami:

1. Sprawdź, czy hosty funkcji Hyper-V i maszyny wirtualne spełniają wszystkie [wymagania i wymagania wstępne.](hyper-v-azure-support-matrix.md)
2. Jeśli serwery funkcji Hyper-V znajdują się w chmurach Menedżera maszyn wirtualnych (System Center Virtual Machine Manager), sprawdź, czy [serwer programu VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional)został przygotowany.
3. Sprawdź, czy usługa zarządzania maszynami wirtualnymi funkcji Hyper-V jest uruchomiona na hostach funkcji Hyper-V.
4. Sprawdź, czy nie występują problemy, które pojawiają się w funkcji Hyper-V-VMMS\Admin logowania się do maszyny Wirtualnej. Ten dziennik znajduje się w **dzienniku** > aplikacji i usług**microsoft** > **windows**.
5. Na maszynie wirtualnej gościa sprawdź, czy w umi jest włączone i dostępne.
   - [Dowiedz się więcej o](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) podstawowych testach WMI.
   - [Rozwiązywanie problemów](https://aka.ms/WMiTshooting) Wmi.
   - [Rozwiązywanie](https://technet.microsoft.com/library/ff406382.aspx#H22) problemów ze skryptami i usługami usługi WMI.
6. Na maszynie wirtualnej gościa upewnij się, że jest uruchomiona najnowsza wersja integration services.
    - [Sprawdź,](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) czy masz najnowszą wersję.
    - [Zachowaj](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) usługi integracji na bieżąco.
    
## <a name="replication-issues"></a>Problemy dotyczące replikacji

Rozwiązywanie problemów ze wstępną i trwającą replikacją:

1. Upewnij się, że korzystasz z [najnowszej wersji](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) usług site recovery.
2. Sprawdź, czy replikacja jest wstrzymana:
   - Sprawdź stan kondycji maszyny Wirtualnej w konsoli Menedżera funkcji Hyper-V.
   - Jeśli jest krytyczna, kliknij prawym przyciskiem myszy kondycję**replikacji widoku replikacji**> **replikacji** > .
   - Jeśli replikacja jest wstrzymana, kliknij przycisk **Wznów replikację**.
3. Sprawdź, czy są uruchomione wymagane usługi. Jeśli tak nie jest, uruchom je ponownie.
    - Jeśli replikujesz funkcji Hyper-V bez programu VMM, sprawdź, czy te usługi są uruchomione na hoście funkcji Hyper-V:
        - Usługa zarządzania maszynami wirtualnymi
        - Usługa agenta usług Microsoft Azure Recovery Services
        - Usługa Microsoft Azure Site Recovery
        - Usługa hosta dostawcy WMI
    - Jeśli replikujesz program VMM w środowisku, sprawdź, czy te usługi są uruchomione:
        - Na hoście funkcji Hyper-V sprawdź, czy jest uruchomiona usługa zarządzania maszynami wirtualnymi, agent usług odzyskiwania platformy Microsoft Azure i usługa hosta dostawców WMI.
        - Na serwerze programu VMM upewnij się, że usługa Menedżera maszyn wirtualnych w centrum systemu jest uruchomiona.
4. Sprawdź łączność między serwerem funkcji Hyper-V a platformą Azure. Aby sprawdzić łączność, otwórz Menedżera zadań na hoście funkcji Hyper V. Na karcie **Wydajność** kliknij pozycję **Otwórz Monitor zasobów**. Na karcie **Sieć** > **proces z aktywnością sieciową**sprawdź, czy plik cbengine.exe aktywnie wysyła duże woluminy (Mbs) danych.
5. Sprawdź, czy hosty funkcji Hyper-V mogą łączyć się z adresem URL obiektu blob magazynu platformy Azure. Aby sprawdzić, czy hosty mogą się łączyć, wybierz i zaznacz **cbengine.exe**. Wyświetl **połączenia TCP,** aby zweryfikować łączność z hosta do obiektu blob magazynu platformy Azure.
6. Sprawdź problemy z wydajnością, jak opisano poniżej.
    
### <a name="performance-issues"></a>Problemy z wydajnością

Ograniczenia przepustowości sieci mogą mieć wpływ na replikację. Rozwiązywanie problemów w następujący sposób:

1. [Sprawdź,](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) czy w twoim środowisku występują ograniczenia dotyczące przepustowości lub ograniczania przepustowości.
2. Uruchom [profiler planowania wdrażania](hyper-v-deployment-planner-run.md).
3. Po uruchomieniu profilera postępuj zgodnie z zaleceniami dotyczącymi [przepustowości](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) i [magazynu.](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
4. Sprawdź [ograniczenia zmian danych](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Jeśli widzisz duże zmiany danych na maszynie wirtualnej, wykonaj następujące czynności:
   - Sprawdź, czy maszyna wirtualna jest oznaczona do ponownej synchronizyzacji.
   - Wykonaj [następujące kroki,](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) aby zbadać źródło zmian.
   - Zmiana może wystąpić, gdy pliki dziennika HRL przekraczają 50% dostępnego miejsca na dysku. W takim przypadku jest to problem, aprowizować więcej miejsca dla wszystkich maszyn wirtualnych, na których występuje problem.
   - Sprawdź, czy replikacja nie jest wstrzymana. Jeśli tak jest, kontynuuje zapisywanie zmian w pliku hrl, co może przyczynić się do jego zwiększonego rozmiaru.
 

## <a name="critical-replication-state-issues"></a>Problemy ze stanem replikacji krytycznej

1. Aby sprawdzić kondycję replikacji, połącz się z lokalną konsolą Menedżera funkcji Hyper-V, wybierz maszynę wirtualną i sprawdź kondycję.

    ![Kondycja replikacji](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Kliknij **pozycję Wyświetl kondycję replikacji,** aby wyświetlić szczegóły:

    - Jeśli replikacja jest wstrzymana, kliknij prawym przyciskiem myszy replikację wznawiania **replikacji replikacji replikacji replikacji replikacji > replikacji replikacji** > **replikacji replikacji replikacji replikacji replikacji replikacji .**
    - Jeśli maszyna wirtualna na hoście funkcji Hyper-V skonfigurowana w programie Site Recovery zostanie migrowana do innego hosta funkcji Hyper-V w tym samym klastrze lub na maszynę autonomiczną, nie ma to wpływu na replikację maszyny Wirtualnej. Wystarczy sprawdzić, czy nowy host funkcji Hyper-V spełnia wszystkie wymagania wstępne i jest skonfigurowany w programie Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problemy z migawkami spójnymi z aplikacjami

Migawka spójna z aplikacją jest migawką punktu w czasie danych aplikacji wewnątrz maszyny Wirtualnej. Usługa kopiowania woluminów w tle (VSS) zapewnia, że aplikacje na maszynie Wirtualnej są w spójnym stanie po wykonaniu migawki.  W tej sekcji szczegółowo opisano niektóre typowe problemy, które mogą wystąpić.

### <a name="vss-failing-inside-the-vm"></a>Usługa VSS nie działa wewnątrz maszyny Wirtualnej

1. Sprawdź, czy jest zainstalowana i uruchomiona najnowsza wersja usług integracji.  Sprawdź, czy aktualizacja jest dostępna, uruchamiając następujące polecenie z podwyższonego poziomu wiersza programu PowerShell na hoście funkcji **Hyper-V: get-vm | select Name, State, IntegrationServicesState**.
2. Sprawdź, czy usługi VSS są uruchomione i w dobrej kondycji:
   - Aby sprawdzić usługi, zaloguj się do maszyny Wirtualnej gościa. Następnie otwórz wiersz polecenia administratora i uruchom następujące polecenia, aby sprawdzić, czy wszystkie moduły zapisu usługi VSS są w dobrej kondycji.
       - **Vssadmin lista pisarzy**
       - **Cienie listy Vssadmin**
       - **Dostawcy listy Vssadmin**
   - Sprawdź dane wyjściowe. Jeśli moduły zapisu znajdują się w stanie awarii, wykonaj następujące czynności:
       - Sprawdź dziennik zdarzeń aplikacji na maszynie Wirtualnej dla błędów operacji vss.
   - Spróbuj ponownie uruchomić te usługi skojarzone z modułem zapisu, który nie powiódł się:
     - Kopiowanie woluminu w tle
       - Dostawca usług VSS usługi Azure Site Recovery
   - Po tym, aby odczekać kilka godzin, aby zobaczyć, czy migawki spójne z aplikacjami są generowane pomyślnie.
   - W ostateczności spróbuj ponownie uruchomić maszynę wirtualną. Może to rozwiązać usługi, które nie są w stanie braku odpowiedzi.
3. Sprawdź, czy nie masz dysków dynamicznych na maszynie wirtualnej. THis nie jest obsługiwany dla migawek spójne z aplikacjami. Można zaewidencjonować zarządzanie dyskami (diskmgmt.msc).

    ![Dysk dynamiczny](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Sprawdź, czy do maszyny wirtualnej nie jest podłączony dysk iSCSI. Ta funkcja nie jest obsługiwana.
5. Sprawdź, czy usługa Kopia zapasowa jest włączona. Sprawdź, czy jest włączona w >  **ustawieniach funkcji Hyper-V****Integration Services**.
6. Upewnij się, że nie ma żadnych konfliktów z aplikacjami robiącymi migawki usługi VSS. Jeśli wiele aplikacji próbuje zrobić migawki vss w tym samym czasie mogą wystąpić konflikty. Na przykład jeśli aplikacja kopia zapasowa jest wykonywanie migawek vss, gdy usługa Site Recovery jest zaplanowane przez zasady replikacji, aby zrobić migawkę.   
7. Sprawdź, czy maszyna wirtualna występuje wysoki współczynnik zmian:
    - Można zmierzyć dzienny współczynnik zmiany danych dla maszyn wirtualnych gościa, używając liczników wydajności na hoście funkcji Hyper-V. Aby zmierzyć szybkość zmiany danych, włącz następujący licznik. Agreguj próbkę tej wartości na dyskach maszyn wirtualnych przez 5-15 minut, aby uzyskać zmiany wartości maszyny Wirtualnej.
        - Kategoria: "Wirtualne urządzenie pamięci masowej Hyper-V"
        - Licznik: "Zapis bajtów / s"</br>
        - Ta szybkość zmiany danych wzrośnie lub pozostanie na wysokim poziomie, w zależności od tego, jak zajęty maszyny wirtualnej lub jej aplikacje są.
        - Średni współczynnik zmian danych dysku źródłowego wynosi 2 MB/s dla standardowego magazynu dla usługi Site Recovery. [Dowiedz się więcej](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Ponadto można [sprawdzić cele skalowalności pamięci masowej](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).
8. Uruchom [planista wdrożenia](hyper-v-deployment-planner-run.md).
9. Zapoznaj się z zaleceniami dotyczącymi [sieci](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) i [pamięci masowej](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>Niepowodzenie usługi VSS wewnątrz hosta funkcji Hyper-V

1. Sprawdź dzienniki zdarzeń pod kątem błędów i zaleceń usługi VSS:
    - Na serwerze hosta funkcji Hyper-V otwórz dziennik zdarzeń administratora funkcji Hyper-V w**dzienniku aplikacji i usług** >  **Podglądu** > zdarzeń w witrynie**Microsoft** > **Windows** > **Hyper-V** > **Admin**.
    - Sprawdź, czy są jakieś zdarzenia, które wskazują błędy migawki spójne z aplikacjami.
    - Typowy błąd jest: "Funkcja Hyper-V nie może wygenerować zestawu migawek vss dla maszyny wirtualnej 'XYZ': moduł zapisujący wystąpił błąd nieprzemijający. Ponowne uruchomienie usługi VSS może rozwiązać problemy, jeśli usługa nie odpowiada."

2. Aby wygenerować migawki usługi VSS dla maszyny Wirtualnej, sprawdź, czy usługi integracji funkcji Hyper-V są zainstalowane na maszynie Wirtualnej i czy usługa integracji kopii zapasowej (VSS) jest włączona.
    - Upewnij się, że usługa/demony usługi INTEGRATION Services VSS są uruchomione na gościu i są w stanie **OK.**
    - Można to sprawdzić z podwyższonego poziomu sesji programu PowerShell na hoście funkcji Hyper-V za pomocą polecenia **Get-VMIntegrationService -VMName\<VMName>-Name VSS** Można również uzyskać te informacje, logując się do maszyny wirtualnej gościa. [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Upewnij się, że usługi integracji kopii zapasowej/VSS na maszynie wirtualnej są uruchomione i w dobrej kondycji. Jeśli nie, uruchom ponownie te usługi i usługę żądania kopiowania woluminu hyper-V w tle na serwerze hosta funkcji Hyper-V.

### <a name="common-errors"></a>Typowe błędy

**Kod błędu** | **Komunikat** | **Szczegóły**
--- | --- | ---
**0x800700EA** | "Funkcja Hyper-V nie może wygenerować zestawu migawek vss dla maszyny wirtualnej: dostępnych jest więcej danych. (0x800700EA). Generowanie zestawu migawek usługi VSS może zakończyć się niepowodzeniem, jeśli trwa operacja tworzenia kopii zapasowej.<br/><br/> Operacja replikacji maszyny wirtualnej nie powiodła się: dostępnych jest więcej danych." | Sprawdź, czy maszyna wirtualna ma włączony dysk dynamiczny. Ta funkcja nie jest obsługiwana.
**0x80070032** | "Żądanie kopiowania woluminu funkcji Hyper-V nie może połączyć się z maszyną wirtualną <./VMname> ponieważ wersja nie jest zgodna z wersją oczekiwaną przez program Hyper-V | Sprawdź, czy są zainstalowane najnowsze aktualizacje systemu Windows.<br/><br/> [Uaktualnienie](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) do najnowszej wersji integration services.



## <a name="collect-replication-logs"></a>Zbieranie dzienników replikacji

Wszystkie zdarzenia replikacji funkcji Hyper-V są rejestrowane w dzienniku funkcji Hyper-V-VMMS\Admin, znajdującym się w **dziennikach** > aplikacji i usług**systemu Microsoft** > **Windows**. Ponadto można włączyć dziennik analityczny dla usługi zarządzania maszynami wirtualnymi funkcji Hyper-V w następujący sposób:

1. Upewnij się, że dzienniki analityczne i debugowania są widoczne w Podglądzie zdarzeń. Aby udostępnić dzienniki, w Podglądzie zdarzeń kliknij pozycję **Wyświetl** > **pokaż dzienniki analityczne i debugowania.**. Dziennik analityczny pojawia się w obszarze **Program Hyper-V-VMMS**.
2. W okienku **Akcje** kliknij pozycję **Włącz dziennik**. 

    ![Włącz dziennik](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Po włączeniu jest on wyświetlany w **Monitorze wydajności**jako **sesja śledzenia zdarzeń** w obszarze Zestawy **modułów zbierających dane**. 
4. Aby wyświetlić zebrane informacje, zatrzymaj sesję śledzenia, wyłączając dziennik. Następnie zapisz dziennik i otwórz go ponownie w Podglądzie zdarzeń lub użyj innych narzędzi, aby przekonwertować go zgodnie z wymaganiami.


### <a name="event-log-locations"></a>Lokalizacje dzienników zdarzeń

**Dziennik zdarzeń** | **Szczegóły** |
--- | ---
**Aplikacje i dzienniki usług/Microsoft/VirtualMachineManager/Serwer/Administrator** (serwer VMM) | Dzienniki do rozwiązywania problemów z programu VMM.
**Aplikacje i dzienniki usług/MicrosoftAzureRecoveryServices/Replication** (host funkcji Hyper-V) | Dzienniki do rozwiązywania problemów z usługami odzyskiwania platformy Microsoft Azure. 
**Aplikacje i dzienniki usług/Odzyskiwanie/dostawca/dostawca witryny platformy Microsoft/Azure/operacyjne** (host funkcji Hyper-V)| Dzienniki do rozwiązywania problemów z usługą odzyskiwania witryny platformy Microsoft Azure.
**Aplikacje i dzienniki usług/Microsoft/Windows/Hyper-V-VMMS/Admin** (host Funkcji Hyper-V) | Dzienniki do rozwiązywania problemów z zarządzaniem maszynami wirtualnymi funkcji Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Zbieranie dzienników w celu zaawansowanego rozwiązywania problemów

Te narzędzia mogą pomóc w zaawansowanym rozwiązywaniu problemów:

-   W przypadku programu VMM należy wykonać zbieranie dzienników odzyskiwania witryny za pomocą [narzędzia Support Diagnostics Platform (SDP).](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)
-   W przypadku funkcji Hyper-V bez programu VMM [pobierz to narzędzie](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)i uruchom je na hoście funkcji Hyper-V w celu zbierania dzienników.

