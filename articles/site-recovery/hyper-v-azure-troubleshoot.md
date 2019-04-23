---
title: Rozwiązywanie problemów z funkcją Hyper-V do odzyskiwania po awarii platformy Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Opisuje, jak rozwiązywać problemy z odzyskiwaniem po awarii z funkcji Hyper-V do platformy Azure replikacji przy użyciu usługi Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 8bb790571e1499bd45fb8bee27f4f1896046cbc2
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149094"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Rozwiązywanie problemów z funkcją Hyper-V do platformy Azure replikacji i trybu failover

W tym artykule opisano typowe problemy, które mogą pochodzić między podczas replikowania lokalnych maszyn wirtualnych funkcji Hyper-V, na platformie Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Włącz problemy związane z ochroną

Jeśli występują problemy podczas włączania ochrony dla maszyn wirtualnych funkcji Hyper-V, należy sprawdzić następujące zalecenia:

1. Sprawdź, czy maszyny wirtualne i hosty funkcji Hyper-V spełnisz [wymogi i wymagania wstępne](hyper-v-azure-support-matrix.md).
2. Jeśli serwery funkcji Hyper-V znajdują się w chmurach programu System Center Virtual Machine Manager (VMM), sprawdź, czy przygotowanej [serwer VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Sprawdź, czy usługa zarządzania maszynami wirtualnymi funkcji Hyper-V jest uruchomiony na hostach funkcji Hyper-V.
4. Sprawdź, czy problemy, które pojawiają się w funkcji Hyper-V-VMMS\Admin logowanie do maszyny Wirtualnej. Ten dziennik znajduje się w **Dzienniki aplikacji i usług** > **Microsoft** > **Windows**.
5. Na maszynie Wirtualnej gościa Sprawdź, czy usługa WMI jest włączony i jest dostępny.
   - [Dowiedz się więcej o](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) podstawowe testy usługi WMI.
   - [Rozwiązywanie problemów z](https://aka.ms/WMiTshooting) WMI.
   - [Rozwiązywanie problemów z](https://technet.microsoft.com/library/ff406382.aspx#H22) problemów za pomocą skryptów usługi WMI i usługi.
6. Na maszynie Wirtualnej gościa upewnij się, że działa najnowszą wersję usług integracji.
    - [Sprawdź](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) czy masz najnowszą wersję.
    - [Zachowaj](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) usługi integracji są aktualne.
    
## <a name="replication-issues"></a>Problemy z replikacją

Rozwiązywanie problemów z replikacją wstępnych i bieżących w następujący sposób:

1. Upewnij się, że używasz [najnowszej wersji](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) usługi Site Recovery.
2. Sprawdź, czy replikacja została wstrzymana:
   - Sprawdź stan kondycji maszyny Wirtualnej w konsoli Menedżera funkcji Hyper-V.
   - Ważne jest, kliknij prawym przyciskiem myszy maszynę Wirtualną > **replikacji** > **Wyświetl kondycję replikacji**.
   - Jeżeli replikacja została wstrzymana, kliknij przycisk **Wznów replikację**.
3. Sprawdź, czy wymagane usługi są uruchomione. Jeśli nie, uruchom je ponownie.
    - Jeśli przeprowadzasz replikację funkcji Hyper-V bez programu VMM, upewnij się, że te usługi są uruchomione na hoście funkcji Hyper-V:
        - Usługa zarządzania maszynami wirtualnymi
        - Usługa agenta usług odzyskiwania Microsoft Azure
        - Usługa Microsoft Azure Site Recovery
        - Usługa hosta dostawcy WMI
    - Jeśli przeprowadzasz replikację za pomocą programu VMM w środowisku, należy sprawdzić, czy są uruchomione następujące usługi:
        - Na hoście funkcji Hyper-V Sprawdź, czy uruchomiona usługa zarządzania maszynami wirtualnymi, Agent usługi Microsoft Azure Recovery Services i usługi Host dostawcy WMI.
        - Na serwerze programu VMM upewnij się, że działa usługa System Center Virtual Machine Manager.
4. Sprawdź łączność między serwerem funkcji Hyper-V i platformą Azure. Aby sprawdzić łączność, otwórz Menedżera zadań na hoście funkcji Hyper-V. Na **wydajności** kliknij pozycję **otwórz Monitor zasobów**. Na **sieci** kartę > **przetwarzanie za pomocą działań sieciowych**, sprawdź, czy cbengine.exe aktywnie wysyła dużych woluminów (MB) danych.
5. Sprawdź, jeśli hosty funkcji Hyper-V można nawiązać adresu URL obiektu blob magazynu platformy Azure. Aby Sprawdź, czy hosty można połączyć, wybierz i sprawdź **cbengine.exe**. Widok **połączeń TCP** do weryfikowania łączności z hosta do obiektu blob magazynu Azure.
6. Problemy z wydajnością, należy sprawdzić, zgodnie z poniższym opisem.
    
### <a name="performance-issues"></a>Problemy z wydajnością

Ograniczenia przepustowości sieci, mogą mieć wpływ na replikację. Rozwiązywanie problemów w następujący sposób:

1. [Sprawdź](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) przypadku przepustowości lub ograniczanie ograniczenia w danym środowisku.
2. Uruchom [planista wdrażania usługi program profilujący](hyper-v-deployment-planner-run.md).
3. Po uruchomieniu profilera, postępuj zgodnie z [przepustowości](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) i [magazynu](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) zalecenia.
4. Sprawdź [ograniczenia współczynnika zmian danych](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Jeśli widzisz wysokim współczynniku zmian na maszynie Wirtualnej danych, wykonaj następujące czynności:
   - Sprawdź, jeśli Twoja maszyna wirtualna jest oznaczana do ponownej synchronizacji.
   - Postępuj zgodnie z [te kroki](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) do badania źródła danych churn.
   - Postęp dokonany w może wystąpić, gdy pliki dziennika HRL przekracza 50% wolnego miejsca. Jeśli jest to problem, należy udostępnić więcej miejsca w magazynie dla wszystkich maszyn wirtualnych, na których występuje problem.
   - Sprawdź, czy replikacja nie jest wstrzymane. Jeśli tak jest, kontynuuje zapisywanie zmian w pliku hrl, który może przyczynić się do zwiększenia rozmiaru.
 

## <a name="critical-replication-state-issues"></a>Problemy dotyczące stanu replikacji krytycznej

1. Aby sprawdzić kondycję replikacji, nawiązać połączenie z konsoli Menedżera funkcji Hyper-V w środowisku lokalnym, wybierz maszynę Wirtualną i sprawdzać kondycję.

    ![Kondycja replikacji](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Kliknij przycisk **Wyświetl kondycję replikacji** Aby wyświetlić szczegóły:

    - Replikacja została wstrzymana, kliknij prawym przyciskiem myszy maszynę Wirtualną > **replikacji** > **Wznów replikację**.
    - Jeśli Maszynę wirtualną na hoście funkcji Hyper-V skonfigurowane w usłudze Site Recovery jest migrowana do innego hosta funkcji Hyper-V, w tym samym klastrze lub do autonomicznej maszyny, nie jest to negatywny wpływ na replikację maszyny Wirtualnej. Po prostu Sprawdź, czy nowy host funkcji Hyper-V, spełnia wszystkie wymagania wstępne i jest skonfigurowany w usłudze Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problemy z migawkami spójny na poziomie aplikacji

Migawka spójności aplikacji jest w momencie migawkę danych aplikacji wewnątrz maszyny Wirtualnej. Usługa kopiowania woluminów w tle (VSS) zapewnia, że aplikacje, na maszynie Wirtualnej są w spójnym stanie, gdy wykonywana jest migawka.  W tej sekcji przedstawiono niektóre typowe problemy, które mogą wystąpić.

### <a name="vss-failing-inside-the-vm"></a>Usługa VSS kończy się niepowodzeniem w ramach maszyny Wirtualnej

1. Sprawdź, czy najnowszej wersji usług Integration services jest zainstalowana i uruchomiona.  Sprawdź, czy aktualizacja jest dostępna, uruchamiając następujące polecenie w wierszu programu PowerShell z podwyższonym poziomem uprawnień na hoście funkcji Hyper-V: **get-vm | wybierz nazwę, stan, IntegrationServicesState**.
2. Sprawdź, czy usługi VSS są uruchomione i dobrej kondycji:
   - Aby sprawdzić, usługi, zaloguj się do maszyny Wirtualnej gościa. Następnie otwórz wiersz polecenia administratora i uruchom następujące polecenia, aby sprawdzić, czy wszystkie składniki zapisywania usługi VSS są w dobrej kondycji.
       - **Vssadmin list writers**
       - **Vssadmin list shadows**
       - **Vssadmin lista dostawców**
   - Sprawdź dane wyjściowe. Jeśli moduły zapisujące są w stanie niepowodzenia, wykonaj następujące czynności:
       - Sprawdź dziennik zdarzeń aplikacji na maszynie Wirtualnej, błędy operację usługi VSS.
   - Ponowne uruchomienie tych usług, skojarzone ze składnikiem zapisywania nie powiodło się:
     - Kopiowanie woluminów w tle
       - Dostawcy usługi VSS programu usługi Azure Site Recovery
   - Po wykonaniu tej czynności, poczekaj kilka godzin, jeśli migawki spójne z aplikacji są generowane pomyślnie.
   - W ostateczności spróbuj wykonać ponowny rozruch maszyny Wirtualnej. To może rozwiązać usług, które są w stanie odpowiadać.
3. Sprawdź, czy nie ma dynamicznych dysków na maszynie wirtualnej. To nie jest obsługiwane dla migawek spójności aplikacji. Można sprawdzić w przystawce Zarządzanie dyskami (diskmgmt.msc).

    ![Dysk dynamiczny](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Upewnij się, że nie ma dysku iSCSI dołączona do maszyny Wirtualnej. Ta funkcja nie jest obsługiwana.
5. Sprawdź, że usługa Kopia zapasowa jest włączona. Sprawdź, czy jest włączone w **ustawienia funkcji Hyper-V** > **usług Integration Services**.
6. Upewnij się, że nie ma żadnych konfliktów z aplikacjami, wykonywanie migawki VSS. Może wystąpić, jeśli wiele aplikacji próbuje wykonać migawki VSS w tej samej konflikty czasu. Na przykład, jeśli aplikacja kopii zapasowej trwa migawki VSS, gdy usługa Site Recovery jest zaplanowane przez zasady replikacji, aby zrobić migawkę.   
7. Sprawdź, jeśli maszyna wirtualna występuje wysokie tempo:
    - W przypadku maszyn wirtualnych gościa, przy użyciu liczników wydajności na hoście funkcji Hyper-V można zmierzyć dziennych zmian danych. Aby zmierzyć współczynnik zmian danych, należy włączyć następujący licznik. Agregować próbkę tę wartość na dyskach maszyn wirtualnych na 5 – 15 minut, można pobrać postęp dokonany w maszynie Wirtualnej.
        - Kategoria: "Funkcji hyper-V wirtualne urządzenie magazynujące"
        - Licznik: "Zapisane bajty / s"</br>
        - Współczynnik zmian danych w tym danych zwiększa lub pozostać na wysokim poziomie, w zależności od ich obciążenia maszyny Wirtualnej lub jej aplikacji.
        - Średni źródłowy współczynnik zmian danych dysku to 2 MB/s dla magazynu w warstwie standardowa dla usługi Site Recovery. [Dowiedz się więcej](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Ponadto możesz [Sprawdź cele usługi storage dotyczące skalowalności](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).
8. Uruchom [planista wdrażania](hyper-v-deployment-planner-run.md).
9. Przejrzyj zalecenia dotyczące [sieci](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) i [magazynu](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>Usługa VSS kończy się niepowodzeniem w ramach hosta funkcji Hyper-V

1. Sprawdź dzienniki zdarzeń błędów usługi VSS i zalecenia:
    - Na serwerze hosta funkcji Hyper-V, Otwórz dziennik zdarzeń administratora funkcji Hyper-V w **Podgląd zdarzeń** > **Dzienniki aplikacji i usług** > **Microsoft**  >  **Windows** > **funkcji Hyper-V** > **administratora**.
    - Sprawdź, czy są wszystkie zdarzenia, które wskazują błędy migawki spójności aplikacji.
    - Typowy błąd to: "Funkcji Hyper-V nie można wygenerować zestaw migawkę usługi VSS dla maszyny wirtualnej"XYZ": Moduł zapisujący napotkał błąd nieprzejściowych. Ponowne uruchamianie usługi VSS może rozwiązać problemy, jeśli usługa nie odpowiada."

2. Aby wygenerować migawki VSS na maszynie Wirtualnej, sprawdź, czy są zainstalowane usługi integracji funkcji Hyper-V, na maszynie Wirtualnej i czy jest włączona usługa integracji kopii zapasowych w tle (VSS).
    - Sprawdź, czy integracji usługi VSS usług/demonów uruchomionych na gościa i znajdują się w **OK** stanu.
    - Można to sprawdzić z sesji programu PowerShell z podwyższonym poziomem uprawnień na hoście funkcji Hyper-V za pomocą polecenia **et-VMIntegrationService - VMName<VMName>— VSS nazwa** można także uzyskać te informacje, logując się do maszyny Wirtualnej gościa. [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Upewnij się, że usługi integracji kopii zapasowej/VSS na maszynie Wirtualnej jest uruchomiona i w dobrej kondycji. W przeciwnym razie ponownie uruchom te usługi i Usługa obiektu żądającego kopiowania woluminów w tle funkcji Hyper-V na serwerze hosta funkcji Hyper-V.

### <a name="common-errors"></a>Typowe błędy

**Kod błędu:** | **Komunikat** | **Szczegóły**
--- | --- | ---
**0x800700EA** | "Funkcji Hyper-V nie można wygenerować zestaw migawkę usługi VSS dla maszyny wirtualnej: Dostępnych jest więcej danych. (0x800700EA). Generowanie Ustaw migawki VSS może zakończyć się niepowodzeniem, jeśli operacja tworzenia kopii zapasowej jest w toku.<br/><br/> Operacja replikacji dla maszyny wirtualnej nie powiodła się: Dostępnych jest więcej danych." | Sprawdź, czy maszyna wirtualna ma dysk dynamiczny włączone. Ta funkcja nie jest obsługiwana.
**0x80070032** | "Żądającego kopii w tle woluminu funkcji Hyper-V nie można nawiązać połączenia z maszyną wirtualną <. / VMname > ponieważ wersja jest niezgodna wersja oczekiwana przez funkcję Hyper-V | Sprawdź, jeśli są zainstalowane najnowsze aktualizacje Windows.<br/><br/> [Uaktualnij](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) do najnowszej wersji usług Integration Services.



## <a name="collect-replication-logs"></a>Replikacja zbieranie dzienników

Wszystkie zdarzenia replikacji funkcji Hyper-V są rejestrowane w dzienniku funkcji Hyper-V-VMMS\Admin znajduje się w **Dzienniki aplikacji i usług** > **Microsoft** > **Windows**. Ponadto można włączyć dziennik analityczny dla Usługa zarządzania maszynami wirtualnymi funkcji Hyper-V, w następujący sposób:

1. Należy dzienniki analityczne i debugowania można wyświetlić w Podglądzie zdarzeń. Aby udostępnić dzienniki, w Podglądzie zdarzeń, kliknij przycisk **widoku** > **Pokaż analityczne i debugowania dzienniki.**. Dziennik analityczny, który pojawia się w obszarze **funkcji Hyper-V-VMMS**.
2. W **akcje** okienku kliknij **Włącz dziennik**. 

    ![Włącz dziennik](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Po włączeniu, pojawia się w **monitora wydajności**, jako **sesji śledzenia zdarzeń** w obszarze **zestawy modułów zbierających dane**. 
4. Aby wyświetlić informacje zbierane, Zatrzymaj sesję śledzenia, wyłączając w dzienniku. Następnie Zapisz dziennik i otwórz go ponownie w Podglądzie zdarzeń lub użyć innych narzędzi, aby przekonwertować go zgodnie z potrzebami.


### <a name="event-log-locations"></a>Lokalizacje dziennika zdarzeń

**Dziennik zdarzeń** | **Szczegóły** |
--- | ---
**Aplikacje i usługi Dzienniki/Microsoft/VirtualMachineManager/serwera/Admin** (serwer programu VMM) | Dzienniki do rozwiązywania problemów programu VMM.
**Aplikacje i usługi Dzienniki/MicrosoftAzureRecoveryServices/replikacji** (host funkcji Hyper-V) | Dzienniki, aby rozwiązać problemy agenta usług odzyskiwania Microsoft Azure. 
**Aplikacje i usługi Dzienniki/Microsoft/Azure Site Recovery/dostawcy/Operational** (host funkcji Hyper-V)| Dzienniki do rozwiązywania problemów z usługą Microsoft Azure Site Recovery.
**Aplikacje i usługi Dzienniki/Microsoft/Windows/Hyper-V-VMMS/Admin** (host funkcji Hyper-V) | Dzienniki rozwiązywania problemów z zarządzaniem maszyn wirtualnych funkcji Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Zbieranie dzienników do zaawansowanego rozwiązywania problemów

Te narzędzia mogą pomóc przy użyciu zaawansowanego rozwiązywania problemów:

-   Dla programu VMM, należy wykonać, Site Recovery dziennika kolekcję przy użyciu [narzędzie pomocy technicznej platformy diagnostyki (SDP)](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   Dla funkcji Hyper-V bez programu VMM [pobrać to narzędzie](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), i uruchom go na hoście funkcji Hyper-V, aby zebrać dzienniki.

