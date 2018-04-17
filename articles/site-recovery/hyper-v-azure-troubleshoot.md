---
title: Rozwiązywanie problemów z funkcją Hyper-V do platformy Azure replikacji za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób rozwiązywania problemów z funkcją Hyper-V do platformy Azure replikacji przy użyciu usługi Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 04/09/2018
ms.author: rayne
ms.openlocfilehash: 95a33c80b1aeef7fbf8bea0ab760bbd66babdac8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Rozwiązywanie problemów z funkcją Hyper-V do platformy Azure replikacji i trybu failover

W tym artykule opisano typowe problemy, które mogą wystąpić podczas replikowania lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure, przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Włącz problemy z ochroną

Jeśli wystąpią problemy, po włączeniu ochrony dla maszyn wirtualnych funkcji Hyper-V, sprawdź następujące informacje:

1. Sprawdź, czy hosty funkcji Hyper-V, a maszyny wirtualne zgodne ze wszystkimi [wymagania i wymagania wstępne](hyper-v-azure-support-matrix.md).
2. Jeśli serwery funkcji Hyper-V znajdują się w chmurach programu System Center Virtual Machine Manager (VMM), sprawdź, czy zostały przygotowane [serwera VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Sprawdź, czy usługa zarządzania maszynami wirtualnymi funkcji Hyper-V jest uruchomiona na hostach funkcji Hyper-V.
4. Sprawdź, czy problemy, które pojawiają się w dzienniku funkcji Hyper-V-VMMS\Admin na maszynie Wirtualnej. Ten dziennik znajduje się w **Dzienniki aplikacji i usług** > **Microsoft** > **Windows**.
5. Na maszynie Wirtualnej gościa Sprawdź, czy usługa WMI jest włączona i jest dostępny.
  - [Dowiedz się więcej o](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) podstawowe testy WMI.
  - [Rozwiązywanie problemów z](https://aka.ms/WMiTshooting) WMI.
  - [Rozwiązywanie problemów z ](https://technet.microsoft.com/library/ff406382.aspx#H22) problemów z usługami i skrypty WMI.
5. Na maszynie Wirtualnej gościa upewnij się, że działa najnowszą wersję usług integracji.
    - [Sprawdź](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) czy masz najnowszą wersję.
    - [Zachowaj](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) usługi integracji są aktualne.
    
## <a name="replication-issues"></a>Problemy dotyczące replikacji

Rozwiązywanie problemów z replikacją wstępnych i bieżących w następujący sposób:

1. Upewnij się, że używasz [najnowszej wersji](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) usługi Site Recovery.
2. Sprawdź, czy replikacja została wstrzymana:
  - Sprawdź stan kondycji maszyny Wirtualnej w konsoli Menedżera funkcji Hyper-V.
  - Bardzo ważne jest, kliknij prawym przyciskiem myszy maszyny Wirtualnej > **replikacji** > **Wyświetl kondycję replikacji**.
  - Jeśli replikacja jest wstrzymana, kliknij przycisk **Wznów replikację**.
3. Sprawdź, czy wymagane usługi są uruchomione. Jeśli nie są, uruchomić je ponownie.
    - Jeśli przeprowadzasz replikację funkcji Hyper-V bez programu VMM, sprawdź, czy te usługi są uruchomione na hoście funkcji Hyper-V:
        - Usługa zarządzania maszynami wirtualnymi
        - Usługa agenta usług odzyskiwania Microsoft Azure
        - Usługa Microsoft Azure Site Recovery
        - Usługa hosta dostawcy WMI
    - Jeśli przeprowadzasz replikację z programem VMM w środowisku, należy sprawdzić, czy są uruchomione następujące usługi:
        - Na hoście funkcji Hyper-V Sprawdź, czy usługa zarządzania maszynami wirtualnymi, agenta usług odzyskiwania Microsoft Azure i usługi Host dostawcy WMI są uruchomione.
        - Na serwerze programu VMM upewnij się, że działa usługa System Center Virtual Machine Manager.
4. Sprawdź łączność między serwerem funkcji Hyper-V i platformy Azure. Aby to zrobić, otwórz Menedżera zadań na hoście funkcji Hyper-V. Na **wydajności** , kliknij pozycję **otwórz Monitor zasobów**. Na **sieci** kartę > **Processess z działaniem sieci**, sprawdź, czy cbengine.exe jest aktywnie wysłanie dużych woluminów (MB) danych.
5. Sprawdź, czy hosty funkcji Hyper-V może nawiązać połączenie adresu URL obiektu blob magazynu Azure. Aby to zrobić, wybierz i sprawdź **cbengine.exe**. Widok **połączeń TCP** do weryfikowania łączności z hosta do obiektu blob magazynu Azure.
6. Sprawdź problemy z wydajnością, zgodnie z poniższym opisem.
    
### <a name="performance-issues"></a>problemy z wydajnością

Ograniczenia przepustowości sieci może wpływać na replikacji. Rozwiązywanie problemów w następujący sposób:

1. [Sprawdź](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) przypadku przepustowości lub ograniczanie ograniczenia w danym środowisku.
2. Uruchom [profilera wdrożenia Planistę](hyper-v-deployment-planner-run.md).
3. Po uruchomieniu profilera, wykonaj [przepustowości](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) i [magazynu](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) zalecenia.
4. Sprawdź [danych churn — ograniczenia](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Jeśli widzisz wysokiej danych churn — na maszynie Wirtualnej, wykonaj następujące czynności:
  - Sprawdź, czy maszyna wirtualna jest oznaczona do ponownej synchronizacji.
  - Postępuj zgodnie z [te kroki](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) do sprawdzania, czy źródło danych churn.
  - Przenoszenie może wystąpić, gdy plik dziennika HRL przekracza 50% wolnego miejsca. Jeśli jest to problem, udostępnić więcej miejsca w magazynie dla wszystkich maszyn wirtualnych, na których występuje problem.
  - Sprawdź, czy replikacja nie jest wstrzymana. Jeśli tak jest, nadal zapisywania zmian w pliku hrl, co może przyczynić się do zwiększenia rozmiaru.
 

## <a name="critical-replication-state-issues"></a>Problemy dotyczące stanu replikacji krytycznej

1. Aby sprawdzić kondycję replikacji, nawiązania połączenia w lokalnej konsoli Menedżera funkcji Hyper-V, wybierz maszynę Wirtualną i Sprawdź kondycję.

    ![Kondycję replikacji](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Kliknij przycisk **Wyświetl kondycję replikacji** Aby wyświetlić szczegóły:

    - Jeśli replikacja jest wstrzymana, kliknij prawym przyciskiem myszy maszyny Wirtualnej > **replikacji** > **Wznów replikację**.
    - Jeśli maszyna wirtualna na hoście funkcji Hyper-V skonfigurowane w usłudze Site Recovery migruje do innego hosta funkcji Hyper-V w tym samym klastrze lub do autonomicznej maszyny, nie jest wpływ na replikację dla maszyny Wirtualnej. Tylko Sprawdź, czy nowy host funkcji Hyper-V, spełnia wszystkie wymagania wstępne i jest skonfigurowany w usłudze Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problemy z migawki dotyczącej spójności aplikacji

Migawki dotyczącej spójności aplikacji jest w momencie migawki danych aplikacji wewnątrz maszyny Wirtualnej. Usługa kopiowania woluminów w tle (VSS) zapewnia, że aplikacje na maszynie Wirtualnej są w spójnym stanie podczas wykonywania migawki.  Ta sekcja zawiera szczegóły dotyczące niektórych typowych problemów, które mogą wystąpić.

### <a name="vss-failing-inside-the-vm"></a>Niepowodzenie w ramach maszyny Wirtualnej usługi VSS

1. Sprawdź, czy najnowszej wersji usług Integration Services jest zainstalowana i uruchomiona.  Sprawdź, czy jest dostępna aktualizacja, uruchamiając następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień programu PowerShell na hoście funkcji Hyper-V: **get-vm | wybierz nazwę, stan, IntegrationServicesState**.
2. Sprawdź, czy usługi VSS są uruchomione i dobrej kondycji:
    - Aby to zrobić, zaloguj się na maszynie Wirtualnej gościa. Następnie otwórz wiersz polecenia administratora, a następnie uruchom następujące polecenia, aby sprawdzić, czy wszystkie składniki zapisywania usługi VSS są w dobrej kondycji.
        - **Vssadmin listy autorów**
        - **Vssadmin shadows listy**
        - **Vssadmin listy dostawców**
    - Sprawdź dane wyjściowe. Składniki zapisywania są w stanie niepowodzenia, należy wykonać następujące czynności:
        - Sprawdź dziennik zdarzeń aplikacji na Maszynie wirtualnej błędy operację usługi VSS.
    - Ponowne uruchomienie tych usług, skojarzone ze składnikiem zapisywania nie powiodło się:
        - Kopiowanie woluminów w tle
         - Dostawca usługi Azure Site Recovery usługi VSS
    - Po wykonaniu tej czynności Poczekaj kilka godzin, jeśli migawki spójne z aplikacji są generowane pomyślnie.
    - W ostateczności spróbuj wykonać ponowny rozruch maszyny Wirtualnej. Problem może rozwiązać usług, które są w stanie odpowiadać.
3. Sprawdź, czy w Maszynie wirtualnej nie ma dysków dynamicznych. To nie jest obsługiwana dla migawki spójne z aplikacjami. Można sprawdzić w konsoli Zarządzanie dyskami (diskmgmt.msc).

    ![Dysk dynamiczny](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Upewnij się, że nie ma dysku iSCSI dołączona do maszyny Wirtualnej. Ta funkcja nie jest obsługiwana.
5. Sprawdź, czy włączono usługi tworzenia kopii zapasowej. Sprawdź, to w **ustawienia funkcji Hyper-V** > **usług integracji**.
6. Upewnij się, że nie ma żadnych konfliktów z aplikacjami wykonywania migawki VSS. Może wystąpić, jeśli wiele aplikacji próbuje wykonać migawki VSS na tej samej konflikty czasu. Na przykład jeśli aplikacja kopii zapasowej trwa migawki VSS, podczas planowania usługi Site Recovery przez zasady replikacji do tworzenia migawki.   
7. Sprawdź, czy maszyna wirtualna występuje wiele zmian:
    - Dla maszyn wirtualnych gościa, na hoście funkcji Hyper-V za pomocą liczników wydajności można zmierzyć codzienne częstotliwości zmian danych. Aby to zrobić, należy włączyć następujące licznika. Aggregrate próbkę tę wartość na dyskach maszyny Wirtualnej przez 5 – 15 minut, aby uzyskać przenoszenie maszyny Wirtualnej.
        - Kategoria: "funkcji Hyper-V wirtualne urządzenie magazynujące"
        - Licznik: "zapisu bajtów / s"</br>
        - Zwiększa lub pozostają na wysokim poziomie, w zależności od obciążenia maszyny Wirtualnej lub jej aplikacje tej częstotliwości zmian danych.
        - Przenoszenie danych dysku źródłowego średni to 2 MB/s dla magazynu w warstwie standardowa dla usługi Site Recovery. [Dowiedz się więcej](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Ponadto można [Sprawdź wartości docelowe skalowalności magazynu](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets.md#scalability-targets-for-a-storage-account).
8. Uruchom [wdrożenia Planistę](hyper-v-deployment-planner-run.md).
9. Przejrzyj zalecenia dotyczące [sieci](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) i [magazynu](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>Usługa VSS awarii w ramach hosta funkcji Hyper-V

1. Sprawdź dzienniki zdarzeń błędów usługi VSS i zalecenia:
    - Na serwerze hosta funkcji Hyper-V, Otwórz dziennik zdarzeń administratora funkcji Hyper-V w **Podgląd zdarzeń** > **Dzienniki aplikacji i usług** > **Microsoft**  >  **Windows** > **funkcji Hyper-V** > **Admin**.
    - Sprawdź, czy są wszystkie zdarzenia, które wskazują błędy migawki dotyczącej spójności aplikacji.
    - Typowy błąd to: "funkcji Hyper-V nie można wygenerować zestaw migawki VSS dla maszyny wirtualnej"XYZ": moduł zapisujący napotkał błąd nieprzejściowego. Ponowne uruchamianie usługi VSS mogą rozwiązać problemy, jeśli usługa nie odpowiada."

2. Aby wygenerować migawki VSS dla maszyny Wirtualnej, sprawdź, czy są zainstalowane usługi integracji funkcji Hyper-V na maszynie Wirtualnej i czy jest włączona usługa integracji kopii zapasowych w tle (VSS).
    - Sprawdź, czy usługa integracji usługi VSS/demonów uruchomionych na maszynie gościa i znajdują się w **OK** stanu.
    - Można to sprawdzić z sesji programu PowerShell z podwyższonym poziomem uprawnień na hoście funkcji Hyper-V za pomocą polecenia **et-VMIntegrationService - VMName<VMName>— nazwa VSS** te informacje można także uzyskać, logując się w maszynie Wirtualnej gościa. [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Upewnij się, że kopii zapasowej/usługi VSS usług integracji na maszynie Wirtualnej są uruchomione i w dobrej kondycji. Jeśli nie, należy ponownie uruchomić te usługi i i usługę kopiowania woluminów w tle funkcji Hyper-V obiektu żądającego na serwerze hosta funkcji Hyper-V.

### <a name="common-errors"></a>Typowe błędy

**Kod błędu:** | **Komunikat** | **Szczegóły**
--- | --- | ---
**0x800700EA** | "Nie można wygenerować zestaw migawki VSS dla maszyny wirtualnej funkcji Hyper-V: dostępnych jest więcej danych. (0x800700EA). Ustaw generacji migawki VSS może zakończyć się niepowodzeniem, jeśli operacja tworzenia kopii zapasowej jest w toku.<br/><br/> Operacja replikacji dla maszyny wirtualnej nie powiodła się: dostępnych jest więcej danych. " | Sprawdź, czy maszyna wirtualna ma włączone dysku dynamicznego. Ta funkcja nie jest obsługiwana.
**0x80070032** | "Żądającego kopii tle woluminu funkcji Hyper-V nie można nawiązać połączenia z maszyną wirtualną <. / VMname > ponieważ wersja jest niezgodna z wersją oczekiwany przez funkcję Hyper-V | Sprawdź, czy są zainstalowane najnowsze aktualizacje systemu Windows.<br/><br/> [Uaktualnij](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services.md#keep-integration-services-up-to-date) do najnowszej wersji usług Integration Services.



## <a name="collect-replication-logs"></a>Replikacja zbieranie dzienników

Wszystkie zdarzenia replikacji funkcji Hyper-V są rejestrowane w dzienniku funkcji Hyper-V-VMMS\Admin znajduje się w **Dzienniki aplikacji i usług** > **Microsoft** > **systemuWindows**. Ponadto można włączyć dziennika analityczne dla Usługa zarządzania maszynami wirtualnymi funkcji Hyper-V, w następujący sposób:

1. Dzienniki analityczne i debugowania należy możliwych do wyświetlenia w Podglądzie zdarzeń. Aby to zrobić, w Podglądzie zdarzeń, kliknij przycisk **widoku** > **dzienniki Pokaż analityczne i debugowania.**. Analityczne dziennik jest wyświetlany w obszarze **funkcji Hyper-V-VMMS**.
2. W **akcje** okienku, kliknij przycisk **Włącz dziennik**. 

    ![Włącz dziennik](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Po włączeniu, zostanie wyświetlony w **monitora wydajności**, jako **sesji śledzenia zdarzeń** w obszarze **zestawy modułów zbierających dane**. 
4. Aby wyświetlić informacje zbierane, Zatrzymaj sesję śledzenia, wyłączając w dzienniku. Następnie Zapisz dziennik i otwórz go ponownie w Podglądzie zdarzeń lub użyć innych narzędzi, aby przekonwertować go zgodnie z potrzebami.


### <a name="event-log-locations"></a>Lokalizacje dziennika zdarzeń

**Dziennik zdarzeń** | **Szczegóły** |
--- | ---
**Aplikacje i usługi Dzienniki/Microsoft/VirtualMachineManager/Server/Admin** (serwer programu VMM) | Rejestruje rozwiązywać problemy z programu VMM.
**Aplikacje i usługi Dzienniki/MicrosoftAzureRecoveryServices/replikacji** (host funkcji Hyper-V) | Rejestruje rozwiązywać problemy z agenta usług odzyskiwania Microsoft Azure. 
**Aplikacje i usługi Microsoft/dzienniki/Azure lokacji odzyskiwania/dostawcy/Operational** (host funkcji Hyper-V)| Rejestruje rozwiązywać problemy z usługą Microsoft Azure Site Recovery.
**Aplikacje i usługi Dzienniki/Microsoft/Windows/Hyper-V-VMMS/Admin** (host funkcji Hyper-V) | Rejestruje Rozwiązywanie problemów z zarządzaniem maszyny Wirtualnej funkcji Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Zbieranie danych dziennika dla zaawansowanego rozwiązywania problemów

Te narzędzia mogą ułatwić z zaawansowanego rozwiązywania problemów:

-   Dla programu VMM, należy wykonać przy użyciu kolekcji dziennika usługi Site Recovery [narzędzie pomocy technicznej platformy diagnostyki (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   Dla funkcji Hyper-V bez programu VMM [pobrać to narzędzie](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), i uruchom je na hoście funkcji Hyper-V do zbierania dzienników.

