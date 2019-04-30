---
title: Rozwiązywanie problemów z replikacją na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dla typowych problemów z replikacją podczas odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 1aaf13f01c7e7197001f3099fabd4b8be8545f0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565060"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Rozwiązywanie problemów z replikacją dla maszyn wirtualnych VMware i serwerów fizycznych

Za pomocą usługi Azure Site Recovery w przypadku ochrony sieci maszyn wirtualnych VMware lub serwerów fizycznych, może pojawić się komunikat błędu. W tym artykule opisano niektóre typowe problemy, które można napotkać podczas replikowania lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych na platformę Azure za pomocą [Site Recovery](site-recovery-overview.md).

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>Monitoruj kondycję serwera przetwarzania, aby uniknąć problemów z replikacją

Zalecane jest monitorowanie kondycji procesu serwera (PS) w portalu, aby upewnić się, że replikacja postępuje maszyn skojarzone źródło. W magazynie, przejdź do zarządzania > infrastruktura usługi Site Recovery > Serwery konfiguracji. W bloku serwera konfiguracji kliknij na serwerze przetwarzania, w obszarze skojarzonych serwerów. Proces serwera zostanie otwarty blok z jego statystyki kondycji. Możesz śledzić wykorzystanie procesora CPU, użycie pamięci stanu usług PS wymagane dla replikacji, Data wygaśnięcia certyfikatu i dostępne wolne miejsce. Stan wszystkich statystyk powinna być zielona. 

**Zaleca się posiadanie pamięci i Procesora CPU w obszarze 70% i wolne miejsce powyżej 25%**. Wolne miejsce odnosi się do tego miejsca pamięci podręcznej serwera przetwarzania, który jest używany do przechowywania danych replikacji z maszyn źródłowych przed przekazaniem do usługi Azure. W przypadku zmniejsza się mniej niż 20%, Replikacja zostanie ograniczona dla wszystkich maszyn skojarzone źródło. Postępuj zgodnie z [pojemności wskazówki](./site-recovery-plan-capacity-vmware.md#capacity-considerations) zrozumienie wymaganej konfiguracji, aby replikować maszyny źródłowej.

Upewnij się, że następujące usługi są uruchomione na maszynie PS. Uruchom lub uruchom ponownie wszystkie usługi, która nie jest uruchomiona.

**Serwer przetwarzania wbudowane**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Usługa przekazywania dziennika (LogUpload)
* Usługa aplikacji InMage Scout
* Agent usług odzyskiwania Microsoft Azure (obengine)
* Agent InMage Scout VX Agent — Sentinel/Outpost (svagents)
* tmansvc
* World Wide Web Publishing (W3SVC) usługi
* MySQL
* Usługa Microsoft Azure Site Recovery (dra)

**Serwera przetwarzania skalowalnego w poziomie**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Usługa przekazywania dziennika (LogUpload)
* Usługa aplikacji InMage Scout
* Agent usług odzyskiwania Microsoft Azure (obengine)
* Agent InMage Scout VX Agent — Sentinel/Outpost (svagents)
* tmansvc

**Serwer przetwarzania na platformie Azure na potrzeby powrotu po awarii**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Usługa przekazywania dziennika (LogUpload)

Upewnij się, że wartości StartType wszystkich usług jest równa **automatyczne lub automatycznie (opóźnione uruchomienie)**. Usługa agenta usług odzyskiwania Microsoft Azure (obengine) nie wymagają jego wartości StartType Ustaw opisanych powyżej.

## <a name="replication-issues"></a>Problemy z replikacją

Niepowodzenia replikacji początkowej i bieżących często są spowodowane przez problemy z łącznością między serwerem źródłowym i serwerem przetwarzania lub między serwerem przetwarzania a platformą Azure. W większości przypadków te problemy można rozwiązać, wykonując czynności opisane w poniższych sekcjach.

>[!Note]
>Upewnij się, że:
>1. System Data Godzina dla chronionego elementu jest zsynchronizowany.
>2. Oprogramowanie antywirusowe nie blokuje usługi Azure Site Recovery. Dowiedz się, [więcej](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) na wykluczenia folderów wymagane dla usługi Azure Site Recovery.

### <a name="check-the-source-machine-for-connectivity-issues"></a>Sprawdź maszyna źródłowa występują problemy dotyczące połączenia

Poniższej liście przedstawiono sposoby można sprawdzić na maszynie źródłowej.

*  W wierszu polecenia na serwerze źródłowym użyj Telnet, aby wykonać polecenie ping serwera przetwarzania za pośrednictwem portu HTTPS, uruchamiając następujące polecenie. HTTPS Port 9443 jest domyślnie serwer przetwarzania używany do wysyłania i odbierania ruchu związanego z replikacją. Można zmodyfikować tego portu, w chwili rejestracji. Poniższe polecenie sprawdza, czy problemy z połączeniem sieciowym i problemy z tego bloku port zapory.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Użyj Telnet, aby przetestować łączność. Nie używaj `ping`. Jeśli Telnet nie jest zainstalowany, wykonaj czynności opisane w [zainstalować klienta usługi Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   W przypadku mógł pomyślnie nawiązać połączenie z portem PS telnet pusty ekran będzie widoczna.

   Jeśli nie można nawiązać połączenia serwera przetwarzania, zezwala na przychodzący port 9443 na serwerze przetwarzania. Na przykład może być konieczne zezwolić na przychodzący port 9443 na serwerze przetwarzania, jeśli sieć ma sieć obwodową lub podsiecią ekranowaną. Następnie sprawdź, czy problem nadal występuje.

*  Jeszcze na maszynie źródłowej raportami, że serwer przetwarzania nie jest osiągalna i telnet zakończy się pomyślnie, otwórz przeglądarkę sieci web na maszynie źródłowej i sprawdź, czy https://<PS_IP>:<PS_Data_Port>/ adres jest dostępny.

    Błąd certyfikatu HTTPS jest oczekiwany w osiągnięciu tego adresu. Ignorowanie błędu certyfikatu i kontynuowanie powinien znajdą się w 400 — Nieprawidłowe żądanie, co oznacza, że serwer nie może obsługiwać żądania w przeglądarce i czy standardowego połączenia HTTPS do serwera działa poprawnie, a dobra.

    Jeśli nie powiedzie się, szczegółowe informacje na temat komunikat o błędzie w przeglądarce przedstawiono wskazówki. Na przykład jeśli uwierzytelnianie serwera proxy jest nieprawidłowa, serwer proxy zwraca 407 — serwer Proxy uwierzytelniania wymagane wraz z wymaganych działań w komunikacie o błędzie. 

*  Sprawdź, czy błędy przekazywania następujące dzienniki na źródłowej maszynie Wirtualnej błędów związanych z siecią:

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server-for-connectivity-issues"></a>Sprawdź serwer przetwarzania, występują problemy dotyczące połączenia

Poniższej liście przedstawiono sposoby można sprawdzić na serwerze przetwarzania:

> [!NOTE]
> Serwer przetwarzania musi mieć statyczny adres IPv4 i nie powinno mieć IP translatora adresów Sieciowych skonfigurowane na nim.

* **Sprawdź łączność między maszyn źródłowych i serwera przetwarzania**
* W przypadku, gdy jesteś w stanie się telnet z komputera źródłowego i jeszcze środowiska PS nie jest dostępny ze źródła, sprawdź połączenie end-to-end z cxprocessserver ze źródłowej maszyny Wirtualnej, uruchamiając narzędzie cxpsclient na źródłowej maszynie Wirtualnej:

      <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

   Sprawdź dzienniki wygenerowane w PS w następujących katalogach, aby uzyskać szczegółowe informacje na temat błędów odpowiedniego:

      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
      and
      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
* Sprawdź następujące dzienniki na serwer przetwarzania, w przypadku, gdy nie było pulsu z PS. To jest identyfikowane za pomocą **kod błędu: 806** w portalu.

      C:\ProgramData\ASR\home\svsystems\eventmanager*.log
      and
      C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

* **Sprawdź, czy serwer przetwarzania jest aktywnie wypychanie danych do platformy Azure**.

  1. Na serwerze przetwarzania Otwórz Menedżera zadań (naciśnij klawisze Ctrl + Shift + Esc).
  2. Wybierz **wydajności** , a następnie wybierz pozycję **otwórz Monitor zasobów** łącza. 
  3. Na **Monitor zasobów** wybierz opcję **sieci** kartę. W obszarze **procesów przy użyciu działań sieciowych**, sprawdź, czy **cbengine.exe** aktywnie wysyła duże ilości danych.

       ![Zrzut ekranu pokazujący woluminy w ramach procesów przy użyciu działań sieciowych](./media/vmware-azure-troubleshoot-replication/cbengine.png)

  Jeśli cbengine.exe nie jest wysyłany dużej ilości danych, wykonaj kroki opisane w poniższych sekcjach.

* **Sprawdź, czy serwer procesu można połączyć z usługą Azure Blob storage**.

  Wybierz **cbengine.exe**. W obszarze **połączeń TCP**, sprawdź, czy jest łączność z serwerem przetwarzania na adres URL magazynu Blog dotyczący platformy Azure.

  ![Zrzut ekranu pokazujący łączność między cbengine.exe i adres URL magazynu obiektów Blob platformy Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

  Jeśli nie ma łączności z serwerem przetwarzania na blogu dotyczącym platformy Azure storage, adres URL w Panelu sterowania wybierz **usług**. Sprawdź, czy są uruchomione następujące usługi:

  *  cxprocessserver
  *  Agent InMage Scout VX Agent — Sentinel/Outpost
  *  Agent usług Microsoft Azure Recovery Services
  *  Usługa Microsoft Azure Site Recovery
  *  tmansvc

  Uruchom lub uruchom ponownie wszystkie usługi, która nie jest uruchomiona. Sprawdź, czy problem nadal występuje.

* **Sprawdź, czy serwer przetwarzania może łączyć się z platformy Azure, publiczny adres IP przy użyciu portu 443**.

  W folderze %programfiles%\Microsoft Agent\Temp usług odzyskiwania Azure Otwórz najnowszy plik CBEngineCurr.errlog. W pliku, wyszukiwanie **443** lub ciągu **próba połączenia nie powiodło się**.

  ![Zrzut ekranu pokazujący błąd rejestruje się w folderze Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

  Jeśli problemy zostaną wyświetlone w wierszu polecenia na serwerze przetwarzania, użyj Telnet, aby wykonać polecenie ping Azure publicznego adresu IP (adres IP jest zamaskowana w powyższej ilustracji). Usługi platformy Azure, publiczny adres IP można znaleźć w pliku CBEngineCurr.currLog przy użyciu portu 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

  Jeśli nie możesz się połączyć, sprawdź, czy jest problem z dostępem do z powodu ustawień zapory lub serwera proxy, zgodnie z opisem w następnym kroku.

* **Sprawdź, czy zapora oparta na adres IP na serwerze przetwarzania blokuje dostęp**.

  Jeśli używasz reguły zapory oparte na adresie IP na serwerze, Pobierz pełną listę [zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Dodaj zakresy adresów IP na konfigurację zapory, aby upewnić się, że zapory umożliwia komunikację na platformie Azure (i domyślnym portem HTTPS 443). Zezwalaj na zakresy adresów IP dla regionu platformy Azure Twojej subskrypcji i regionu platformy Azure zachodnie stany USA (używane do kontrolowania dostępu i tożsamości zarządzania).

* **Sprawdź, czy Zapora opartego na adresach URL na serwerze przetwarzania blokuje dostęp**.

  Jeśli używasz regułę zapory na podstawie adresu URL na serwerze, należy dodać adresów URL wymienionych w poniższej tabeli, aby konfiguracja zapory:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Sprawdź, czy ustawienia serwera proxy na serwerze przetwarzania blokowana**.

   Jeśli używasz serwera proxy, upewnij się, że nazwa serwera proxy jest rozpoznany przez serwer DNS. Aby sprawdzić wartości, podane podczas konfigurowania serwera konfiguracji, przejdź do klucza rejestru **Recovery\ProxySettings witryny HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure**.

   Następnie upewnij się, że te same ustawienia są używane przez agenta usługi Azure Site Recovery do przesyłania danych: 
      
   1. Wyszukaj **kopia zapasowa Microsoft Azure**. 
   2. Otwórz **kopia zapasowa Microsoft Azure**, a następnie wybierz pozycję **akcji** > **Zmień właściwości**. 
   3. Na **konfigurację serwera Proxy** kartę, powinien zostać wyświetlony adres serwera proxy. Adres serwera proxy powinien być taki sam jak adres serwera proxy, który jest wyświetlany w ustawieniach rejestru. W przeciwnym razie zmień go na ten sam adres.

*  **Sprawdź, czy ograniczania przepustowości jest ograniczona na serwerze przetwarzania**.

   Zwiększyć przepustowość, a następnie sprawdź, czy problem nadal występuje.


## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>Maszyna źródłowa nie ma na liście w witrynie Azure portal

Podczas próby wybierz maszyny źródłowej, aby włączyć replikację przy użyciu usługi Site Recovery maszyny może nie być dostępne dla jednego z następujących powodów:

* **Dwie maszyny wirtualne z tym samym wystąpieniu UUID**: Jeśli dwie maszyny wirtualne w ramach programu vCenter to samo wystąpienie identyfikatora UUID, pierwszej maszyny wirtualnej, wykrytych przez serwer konfiguracji jest wyświetlany w witrynie Azure portal. Aby rozwiązać ten problem, upewnij się, że nie dwie maszyny wirtualne mają tego samego wystąpienia identyfikatora UUID. Ten scenariusz jest typowy widoczne w wystąpieniach, gdzie staje się aktywny kopii zapasowej maszyny Wirtualnej, a jest zalogowany do naszych danych odnajdywania. Zapoznaj się [usługi Azure Site Recovery VMware na platformę Azure: Jak wyczyścić zduplikowane lub nieaktualne wpisy](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) do rozwiązania.
* **VCenter niepoprawne poświadczenia użytkownika**: Nie zapomnij dodać poświadczenia poprawne vCenter, po skonfigurowaniu serwera konfiguracji za pomocą szablonu pakietu OVF lub ujednoliconej konfiguracji. Aby zweryfikować poświadczenia, które dodałeś podczas instalacji, zobacz [zmodyfikowania poświadczeń do automatycznego odnajdowania](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **niewystarczające uprawnienia vCenter**: Jeśli uprawnienia podany dostępu vCenter do nie ma wymaganych uprawnień, może wystąpić błąd, aby odnaleźć maszyny wirtualne. Upewnij się, że uprawnienia opisane w [przygotowywanie konta do automatycznego odnajdowania](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) są dodawane do konta użytkownika vCenter.
* **Serwery zarządzania w usłudze Azure Site Recovery**: Jeśli maszyna wirtualna jest używany jako serwer zarządzania w co najmniej jeden z następujących ról — serwer przetwarzania /scale-out serwera konfiguracji / głównym serwerze docelowym, nie będzie mógł wybrać maszynę wirtualną z portalu. Nie można zreplikować serwery zarządzania.
* **Już chronione/przełączone w tryb failover za pomocą usługi Azure Site Recovery**: Jeśli maszyna wirtualna jest już chronione lub przełączone w tryb failover przez usługi Site Recovery, maszyna wirtualna nie jest dostępny dla wybranych do ochrony w portalu. Upewnij się, czy maszyny wirtualnej, którego szukasz w portalu nie jest już chroniony przez żadnego innego użytkownika lub innej subskrypcji.
* **vCenter niepołączony**: Sprawdź, czy vCenter jest w stanie połączonym. Aby sprawdzić, przejdź do magazynu usługi Recovery Services > infrastruktura usługi Site Recovery > Serwery konfiguracji > kliknij na serwerze konfiguracji odpowiednich > zostanie otwarty blok z prawej strony ze szczegółami skojarzonych serwerów. Sprawdź, czy vCenter jest połączony. Jeśli komputer znajduje się w stanie "Niepodłączone", rozwiąż problem i następnie [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server) w portalu. Po tym maszyna wirtualna będzie wyświetlane w portalu.
* **Wyłączone ESXi**: Jeśli host ESXi, pod którą znajduje się maszyna wirtualna znajduje się w stanie, wyłączenia następnie maszyna wirtualna nie będą wyświetlane lub nie będzie możliwe w witrynie Azure portal. Zasilania na hoście ESXi [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server) w portalu. Po tym maszyna wirtualna będzie wyświetlane w portalu.
* **Oczekujące na ponowny rozruch**: W przypadku oczekuje na ponowny rozruch na maszynie wirtualnej, następnie nie będzie możliwe wybranie maszyny w witrynie Azure portal. Upewnij się, że ukończenie działania oczekiwanie na ponowny rozruch [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Po tym maszyna wirtualna będzie wyświetlane w portalu.
* **Nie można odnaleźć adresu IP**: Jeśli maszyna wirtualna nie ma prawidłowego adresu IP skojarzone z nim, następnie nie będzie możliwe wybranie maszyny w witrynie Azure portal. Upewnij się, aby przypisać prawidłowego adresu IP do maszyny wirtualnej [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Po tym maszyna wirtualna będzie wyświetlane w portalu.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Chronione maszyny wirtualne są wyszarzone na zewnątrz w portalu

Maszyny wirtualne, które są replikowane w ramach odzyskiwania lokacji nie są dostępne w witrynie Azure portal, jeśli istnieją zduplikowane wpisy w systemie. Aby dowiedzieć się, jak usunąć przestarzałych wpisów i rozwiązać problem, zapoznaj się [usługi Azure Site Recovery VMware na platformę Azure: Jak wyczyścić zduplikowane lub nieaktualne wpisy](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-recommended-steps-for-resolution"></a>Typowe błędy i zalecane kroki do rozpoznania

### <a name="initial-replication-issues-error-78169"></a>Replikacja początkowa problemów [błąd 78169]

W powyższym zapewnienie, że istnieją nie łączności, przepustowości lub czasu synchronizacji problemy związane z, upewnij się, że:

- Oprogramowanie antywirusowe nie blokuje usługi Azure Site Recovery. Dowiedz się, [więcej](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) na wykluczenia folderów wymagane dla usługi Azure Site Recovery.

### <a name="application-consistency-recovery-point-missing-error-78144"></a>Punktu odzyskiwania spójności aplikacji brakuje [błąd 78144]

 Dzieje się tak z powodu problemów z kopiowania woluminów w tle Service (VSS). Aby rozwiązać: 
 
- Sprawdź, czy zainstalowana wersja agenta usługi Azure Site Recovery jest co najmniej 9.22.2. 
- Sprawdź, czy dostawca usługi VSS jest zainstalowany jako usługa na platformie usług Windows, a także sprawdzić MMC usługi składnika, aby sprawdzić, czy dostawca VSS usługi Azure Site Recovery znajduje się.
- Jeśli nie zainstalowano dostawcy usługi VSS, zapoznaj się [niepowodzenia instalacji w artykule dotyczącym rozwiązywania problemów](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Wyłączenie usługi VSS
    - Sprawdź, czy typ uruchamiania usługi dostawcy usługi VSS jest równa **automatyczne**.
    - Uruchom ponownie następujące usługi:
        - Usługa VSS
        - Dostawcy usługi VSS programu usługi Azure Site Recovery
        - Usługę VDS

### <a name="high-churn-on-source-machine-error-78188"></a>Dużą liczbą zmian na maszynie źródłowej [błąd 78188]

Możliwe przyczyny:
- Współczynnik zmian danych (zapisane bajty/s) na liście dysków maszyny wirtualnej wynosi więcej niż [usługi Azure Site Recovery obsługiwane limity](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) dla typu konta replikacji magazynu docelowego.
- Współczynnik zmian danych jest nagłym skokiem z powodu której dużej ilości danych jest w stanie oczekiwania do przekazania.

Aby rozwiązać ten problem:
- Upewnij się, że docelowy typ konta magazynu (standardowa / Premium) zostanie zainicjowana dla każdego wymagania szybkość zmian w źródle.
- W przypadku tymczasowego obserwowanych zmian, poczekaj kilka godzin w celu przekazania danych oczekujące, zapoznać się z nimi, a także tworzenie punktów odzyskiwania.
- Jeśli problem pozostaje nierozwiązany, użyj usługi ASR [planista wdrażania](site-recovery-deployment-planner.md#overview) ułatwiające zaplanowanie replikacji.

### <a name="no-heartbeat-from-source-machine-error-78174"></a>Brak pulsu z maszyny źródłowej [błąd 78174]

Dzieje się tak, gdy agent usługi Azure Site Recovery Mobility na maszynie źródłowej nie komunikuje się za pomocą serwera konfiguracji (CS).

Aby rozwiązać ten problem, należy użyć do weryfikowania łączności sieciowej ze źródłowej maszyny Wirtualnej serwera konfiguracji następujące czynności:

1. Sprawdź, czy na maszynie źródłowej jest uruchomiony.
2. Zaloguj się na maszynie źródłowej przy użyciu konta z uprawnieniami administratora.
3. Sprawdź, czy następujące usługi są uruchomione i ponownego uruchamiania usług, gdy:
   - Svagents (Agent InMage Scout VX Agent)
   - Usługa aplikacji InMage Scout
4. Na maszynie źródłowej Sprawdź dzienniki w lokalizacji, aby uzyskać szczegóły błędu:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="no-heartbeat-from-process-server-error-806"></a>Brak pulsu z serwera przetwarzania [błąd 806]
W przypadku, gdy jest brak pulsu z serwera przetwarzania (PS), sprawdź, czy:
1. PS Maszyna wirtualna jest uruchomiona
2. Sprawdź następujące dzienniki na PS, aby uzyskać szczegóły błędu:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="no-heartbeat-from-master-target-error-78022"></a>Brak pulsu z głównego serwera docelowego [błąd 78022]

Dzieje się tak, gdy agent usługi Azure Site Recovery Mobility na główny element docelowy nie komunikuje się z serwerem konfiguracji.

Aby rozwiązać ten problem, należy użyć następujące kroki, aby sprawdzić stan usługi:

1. Sprawdź, czy wzorzec docelowej maszyny Wirtualnej jest uruchomiony.
2. Zaloguj się do wzorca docelowej maszyny Wirtualnej przy użyciu konta z uprawnieniami administratora.
    - Sprawdź, czy usługa svagents jest uruchomiona. Jeśli jest uruchomiona, uruchom ponownie usługę
    - Sprawdź dzienniki w lokalizacji, aby uzyskać szczegóły błędu:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-is-not-reachable-from-the-source-machine-error-78186"></a>Serwer przetwarzania nie jest dostępny z maszyny źródłowej [błąd 78186]

Ten błąd w wyniku awarii i aplikacji, spójne punktów nie są generowane, jeśli nie jest skierowany. Aby rozwiązać ten problem, wykonaj poniższe Rozwiązywanie problemów z łącza:
1. Upewnij się, że [są uruchomione usługi PS](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Sprawdź problemy z łącznością maszyny źródłowej](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Sprawdź problemy z łącznością serwera przetwarzania](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) i postępuj zgodnie ze wskazówkami dla:
    - Sprawdzanie łączności ze źródłem
    - Problemy związane z zapory i serwera proxy

### <a name="data-upload-blocked-from-source-machine-to-process-server-error-78028"></a>Przekazywanie danych zablokowane z komputera źródłowego na serwer przetwarzania [błąd 78028]

Ten błąd w wyniku awarii i aplikacji, spójne punktów nie są generowane, jeśli nie jest skierowany. Aby rozwiązać ten problem, wykonaj poniższe Rozwiązywanie problemów z łącza:

1. Upewnij się, że [są uruchomione usługi PS](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Sprawdź problemy z łącznością maszyny źródłowej](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Sprawdź problemy z łącznością serwera przetwarzania](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) i postępuj zgodnie ze wskazówkami dla:
    - Sprawdzanie łączności ze źródłem
    - Problemy związane z zapory i serwera proxy

## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz więcej pomocy, opublikuj swoje pytanie w [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Mamy aktywnej społeczności użytkowników, a jeden z naszych inżynierów może być pomocny.
