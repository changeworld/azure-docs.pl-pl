---
title: Rozwiązywanie problemów z replikacją na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dla typowych problemów z replikacją podczas odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/7/2019
ms.author: mayg
ms.openlocfilehash: 71c07d93d75ee372a50ec4ff5fc81e92926d329b
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964785"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Rozwiązywanie problemów z replikacją dla maszyn wirtualnych VMware i serwerów fizycznych

Za pomocą usługi Azure Site Recovery w przypadku ochrony sieci maszyn wirtualnych VMware lub serwerów fizycznych, może pojawić się komunikat błędu. W tym artykule opisano niektóre typowe problemy, które można napotkać podczas replikowania lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych na platformę Azure za pomocą [Site Recovery](site-recovery-overview.md).

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>Monitoruj kondycję serwera przetwarzania, aby uniknąć problemów z replikacją

Zalecane jest monitorowanie kondycji procesu serwera (PS) w portalu, aby upewnić się, że replikacja postępuje maszyn skojarzone źródło. W magazynie, przejdź do zarządzania > infrastruktura usługi Site Recovery > Serwery konfiguracji. W bloku serwera konfiguracji kliknij na serwerze przetwarzania, w obszarze skojarzonych serwerów. Proces serwera zostanie otwarty blok z jego statystyki kondycji. Możesz śledzić wykorzystanie procesora CPU, użycie pamięci stanu usług PS wymagane dla replikacji, Data wygaśnięcia certyfikatu i dostępne wolne miejsce. Stan wszystkich statystyk powinna być zielona. 

**Zaleca się posiadanie pamięci i Procesora CPU w obszarze 70% i wolne miejsce powyżej 25%**. Wolne miejsce odnosi się do tego miejsca pamięci podręcznej serwera przetwarzania, który jest używany do przechowywania danych replikacji z maszyn źródłowych przed przekazaniem do usługi Azure. W przypadku zmniejsza się mniej niż 20%, Replikacja zostanie ograniczona dla wszystkich maszyn skojarzone źródło. Postępuj zgodnie z [pojemności wskazówki](./site-recovery-plan-capacity-vmware.md#capacity-considerations) zrozumienie wymaganej konfiguracji, aby replikować maszyny źródłowej.

Upewnij się, że następujące usługi są uruchomione na maszynie PS. Uruchom lub uruchom ponownie wszystkie usługi, która nie jest uruchomiona.

**Serwer przetwarzania wbudowane**

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

* cxprocessserver
* InMage PushInstall
* Usługa przekazywania dziennika (LogUpload)
* Usługa aplikacji InMage Scout
* Agent usług odzyskiwania Microsoft Azure (obengine)
* Agent InMage Scout VX Agent — Sentinel/Outpost (svagents)
* tmansvc

**Serwer przetwarzania na platformie Azure na potrzeby powrotu po awarii**

* cxprocessserver
* InMage PushInstall
* Usługa przekazywania dziennika (LogUpload)

Upewnij się, że wartości StartType wszystkich usług jest równa **automatyczne lub automatycznie (opóźnione uruchomienie)**. Usługa agenta usług odzyskiwania Microsoft Azure (obengine) nie wymagają jego wartości StartType Ustaw opisanych powyżej.

## <a name="initial-replication-issues"></a>Problemy dotyczące replikacji początkowej

Błędy replikacji początkowej jest często spowodowane przez problemy z łącznością między serwerem źródłowym i serwerem przetwarzania lub między serwerem przetwarzania a platformą Azure. W większości przypadków te problemy można rozwiązać, wykonując czynności opisane w poniższych sekcjach.

### <a name="check-the-source-machine"></a>Sprawdź maszyna źródłowa

Poniższej liście przedstawiono sposoby można sprawdzić na maszynie źródłowej:

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

### <a name="check-the-process-server"></a>Sprawdź serwer przetwarzania

Poniższej liście przedstawiono sposoby można sprawdzić na serwerze przetwarzania:

> [!NOTE]
> Serwer przetwarzania musi mieć statyczny adres IPv4 i nie powinno mieć IP translatora adresów Sieciowych skonfigurowane na nim.

* **Sprawdź łączność między maszyn źródłowych i serwera przetwarzania**
1. W przypadku, gdy jesteś w stanie się telnet z komputera źródłowego i jeszcze środowiska PS nie jest dostępny ze źródła, sprawdź połączenie end-to-end z cxprocessserver ze źródłowej maszyny Wirtualnej, uruchamiając narzędzie cxpsclient na źródłowej maszynie Wirtualnej:

       <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

    Sprawdź dzienniki wygenerowane w PS w następujących katalogach, aby uzyskać szczegółowe informacje na temat błędów odpowiedniego:

       C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
       and
       C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
2. Sprawdź następujące dzienniki na serwer przetwarzania, w przypadku, gdy nie było pulsu z PS:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

*  **Sprawdź, czy serwer przetwarzania jest aktywnie wypychanie danych do platformy Azure**.

   1. Na serwerze przetwarzania Otwórz Menedżera zadań (naciśnij klawisze Ctrl + Shift + Esc).
   2. Wybierz **wydajności** , a następnie wybierz pozycję **otwórz Monitor zasobów** łącza. 
   3. Na **Monitor zasobów** wybierz opcję **sieci** kartę. W obszarze **procesów przy użyciu działań sieciowych**, sprawdź, czy **cbengine.exe** aktywnie wysyła duże ilości danych.

        ![Zrzut ekranu pokazujący woluminy w ramach procesów przy użyciu działań sieciowych](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   Jeśli cbengine.exe nie jest wysyłany dużej ilości danych, wykonaj kroki opisane w poniższych sekcjach.

*  **Sprawdź, czy serwer procesu można połączyć z usługą Azure Blob storage**.

   Wybierz **cbengine.exe**. W obszarze **połączeń TCP**, sprawdź, czy jest łączność z serwerem przetwarzania na adres URL magazynu Blog dotyczący platformy Azure.

   ![Zrzut ekranu pokazujący łączność między cbengine.exe i adres URL magazynu obiektów Blob platformy Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   Jeśli nie ma łączności z serwerem przetwarzania na blogu dotyczącym platformy Azure storage, adres URL w Panelu sterowania wybierz **usług**. Sprawdź, czy są uruchomione następujące usługi:

   *  cxprocessserver
   *  Agent InMage Scout VX Agent — Sentinel/Outpost
   *  Agent usług Microsoft Azure Recovery Services
   *  Usługa Microsoft Azure Site Recovery
   *  tmansvc

   Uruchom lub uruchom ponownie wszystkie usługi, która nie jest uruchomiona. Sprawdź, czy problem nadal występuje.

*  **Sprawdź, czy serwer przetwarzania może łączyć się z platformy Azure, publiczny adres IP przy użyciu portu 443**.

   W folderze %programfiles%\Microsoft Agent\Temp usług odzyskiwania Azure Otwórz najnowszy plik CBEngineCurr.errlog. W pliku, wyszukiwanie **443** lub ciągu **próba połączenia nie powiodło się**.

   ![Zrzut ekranu pokazujący błąd rejestruje się w folderze Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   Jeśli problemy zostaną wyświetlone w wierszu polecenia na serwerze przetwarzania, użyj Telnet, aby wykonać polecenie ping Azure publicznego adresu IP (adres IP jest zamaskowana w powyższej ilustracji). Usługi platformy Azure, publiczny adres IP można znaleźć w pliku CBEngineCurr.currLog przy użyciu portu 443:

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   Jeśli nie możesz się połączyć, sprawdź, czy jest problem z dostępem do z powodu ustawień zapory lub serwera proxy, zgodnie z opisem w następnym kroku.

*  **Sprawdź, czy zapora oparta na adres IP na serwerze przetwarzania blokuje dostęp**.

   Jeśli używasz reguły zapory oparte na adresie IP na serwerze, Pobierz pełną listę [zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Dodaj zakresy adresów IP na konfigurację zapory, aby upewnić się, że zapory umożliwia komunikację na platformie Azure (i domyślnym portem HTTPS 443). Zezwalaj na zakresy adresów IP dla regionu platformy Azure Twojej subskrypcji i regionu platformy Azure zachodnie stany USA (używane do kontrolowania dostępu i tożsamości zarządzania).

*  **Sprawdź, czy Zapora opartego na adresach URL na serwerze przetwarzania blokuje dostęp**.

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

## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz więcej pomocy, opublikuj swoje pytanie w [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Mamy aktywnej społeczności użytkowników, a jeden z naszych inżynierów może być pomocny.
