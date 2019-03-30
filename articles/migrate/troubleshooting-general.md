---
title: Rozwiązywanie problemów z usługi Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera omówienie znanych problemów dotyczących usługi Azure Migrate, a wskazówki dotyczące typowych błędów rozwiązywania problemów.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: dff3c96cf3ac8eea7c1160ee1834cc70390c0333
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652641"
---
# <a name="troubleshoot-azure-migrate"></a>Rozwiązywanie problemów z usługą Azure Migrate

## <a name="troubleshoot-common-errors"></a>Rozwiązywanie typowych problemów

[Usługa Azure Migrate](migrate-overview.md) ocenia obciążenia lokalne pod kątem migracji na platformę Azure. W tym artykule umożliwiają rozwiązywanie problemów dotyczących wdrażania i korzystania z usługi Azure Migrate.

### <a name="i-am-using-the-ova-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Używam OVA, które stale odnajduje Moje w środowisku lokalnym, ale maszyny wirtualne, które są usuwane z moich w środowisku lokalnym, są nadal są wyświetlane w portalu.

Urządzenie ciągłe odnajdywania tylko zbiera dane dotyczące wydajności stale, nie wykrywa zmiany konfiguracji w środowisku lokalnym, (tj. Dodawanie maszyny Wirtualnej, usuwania, dodawania dysku itp.). W przypadku zmiany konfiguracji w środowisku lokalnym możesz wykonać następujące działania, aby odzwierciedlić zmiany w portalu:

- Dodanie elementów (maszyn wirtualnych, dysków, rdzeni itp.): aby uwzględnić te zmiany w witrynie Azure Portal, możesz zatrzymać odnajdywanie z urządzenia i następnie uruchomić je ponownie. Zapewni to, że zmiany zostaną zaktualizowane w projekcie usługi Azure Migrate.

   ![Zatrzymaj odnajdywanie](./media/troubleshooting-general/stop-discovery.png)

- Usunięcie maszyn wirtualnych: ze względu na konstrukcję urządzenia, usunięcie maszyny wirtualnej nie zostanie uwzględnione, nawet jeśli zatrzymasz odnajdywanie i uruchomisz je ponownie. Przyczyną jest to, że dane z kolejnych operacji odnajdywania są dołączane do starszych danych, a nie nadpisywane. W takim przypadku możesz po prostu zignorować maszynę wirtualną w portalu, usuwając ją z grupy i obliczając ponownie ocenę.

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Usuwanie usługi Azure Migrate projektów i skojarzone obszaru roboczego analizy dzienników

Po usunięciu projekt usługi Azure Migrate usuwa projekt migracji, wraz ze wszystkich grup i ocen. Jednak jeśli obszar roboczy usługi Log Analytics mają być dołączone do projektu, go nie powoduje automatycznego usunięcia obszaru roboczego usługi Log Analytics. Jest to spowodowane tym samym obszarze roboczym usługi Log Analytics mogą być używane dla wielu przypadków użycia. Jeśli chcesz usunąć obszar roboczy usługi Log Analytics, należy to zrobić ręcznie.

1. Przejdź do obszaru roboczego usługi Log Analytics, dołączonych do projektu.
   a. Jeśli projekt migracji nie zostały jeszcze usunięte, znajduje się łącze do obszaru roboczego ze strony Przegląd projektu w sekcji podstawowe elementy.

   ![LA Workspace](./media/troubleshooting-general/LA-workspace.png)

   b. Jeśli został już usunięty projektu migracji, kliknij przycisk **grup zasobów** w okienku po lewej stronie w witrynie Azure portal i przejdź do grupy zasobów, w którym obszar roboczy został utworzony, a następnie przejdź do jej.
2. Postępuj zgodnie z instrukcjami [w tym artykule](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) można usunąć obszaru roboczego.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Tworzenie projektu migracji nie powiodła się z powodu błędu *żądania muszą zawierać nagłówki tożsamości użytkownika*

Ten problem może się zdarzyć dla użytkowników, którzy nie mają dostępu do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji. Gdy użytkownik zostanie dodany do dzierżawy usługi Azure AD po raz pierwszy, ma uprawnienia otrzymuje wiadomość e-mail z zaproszeniem do dołączenia do dzierżawy. Użytkownicy muszą przejść do wiadomości e-mail i zaakceptować zaproszenie, aby pomyślnie poproś o dodanie do dzierżawy. Jeśli nie można wyświetlić adres e-mail, skontaktuj się użytkownika, który już ma dostęp do dzierżawy i poproś go o Wyślij ponownie zaproszenie do użytkownika za pomocą kroki określone [tutaj](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Po otrzymaniu wiadomości e-mail z zaproszeniem, musisz otworzyć wiadomości e-mail i kliknij łącze w wiadomości e-mail o zaakceptowanie zaproszenia. Po zakończeniu tej operacji należy się wylogować z witryny Azure portal, a następnie zaloguj się ponownie, odświeżeniu przeglądarki nie będą działać. Następnie możesz spróbować utworzyć projekt migracji.

### <a name="i-am-unable-to-export-the-assessment-report"></a>Nie można wyeksportować raport z oceny

Jeśli nie można wyeksportować raport z oceny z portalu, spróbuj użyć poniżej interfejsu API REST, aby uzyskać adres URL pobierania raport z oceny.

1. Zainstaluj *armclient* na komputerze (Jeśli nie jest już zainstalowana):

   a. W oknie wiersza polecenia administratora uruchom następujące polecenie: ```@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"```

   b. W oknie administrator programu Windows PowerShell uruchom następujące polecenie: ```choco install armclient```

2. Pobierz adres URL pobierania dla raport z oceny za pomocą interfejsu API REST migracji platformy Azure

   a.    W oknie administrator programu Windows PowerShell uruchom następujące polecenie: ```armclient login```

        This opens the Azure login pop-up where you need to sign in to Azure.

   b.    W tym samym oknie programu PowerShell uruchom następujące polecenie, aby uzyskać adres URL pobierania raport z oceny (Zastąp parametry identyfikatora URI z odpowiednimi wartościami przykładowego interfejsu API żądanie poniżej)

       ```armclient POST https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Migrate/projects/{projectName}/groups/{groupName}/assessments/{assessmentName}/downloadUrl?api-version=2018-02-02```

      Przykładowe żądanie i dane wyjściowe:

      ```PS C:\WINDOWS\system32> armclient POST https://management.azure.com/subscriptions/8c3c936a-c09b-4de3-830b-3f5f244d72e9/r
   esourceGroups/ContosoDemo/providers/Microsoft.Migrate/projects/Demo/groups/contosopayroll/assessments/assessment_11_16_2
   018_12_16_21/downloadUrl?api-version=2018-02-02
   {
   "assessmentReportUrl": "https://migsvcstoragewcus.blob.core.windows.net/4f7dddac-f33b-4368-8e6a-45afcbd9d4df/contosopayrollassessment_11_16_2018_12_16_21?sv=2016-05-31&sr=b&sig=litQmHuwi88WV%2FR%2BDZX0%2BIttlmPMzfVMS7r7dULK7Oc%3D&st=2018-11-20T16%3A09%3A30Z&se=2018-11-20T16%3A19%3A30Z&sp=r",
   "expirationTime": "2018-11-20T22:09:30.5681954+05:30"```

3. Skopiuj adres URL z odpowiedzi i otwórz go w przeglądarce, aby pobrać raport z oceny.

4. Po pobraniu raportu, należy użyć programu Excel, przejdź do folderu pobrane i Otwórz plik w programie Excel, aby go wyświetlić.

### <a name="performance-data-for-cpu-memory-and-disks-is-showing-up-as-zeroes"></a>Dane wydajności dotyczące procesora CPU, pamięci i dysków są wyświetlane jako wartości zerowe

Usługa Azure Migrate profile ciągle środowisku lokalnym, aby zbierać dane dotyczące wydajności lokalnych maszyn wirtualnych. Odnajdywanie środowiska właśnie zostało uruchomione, musisz poczekać co najmniej jeden dzień do zbierania danych wydajności do wykonania. Jeśli ocena została utworzona bez oczekiwania na jeden dzień, metryki wydajności będzie wyświetlany jako zera. Po odczekaniu dnia, możesz utworzyć nowego rozwiązania do oceny lub zaktualizować oceny istniejących przy użyciu opcji "Oblicz ponownie" w raporcie oceny.

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>Lokalizacja geograficzna platformy Azure, czy określone podczas tworzenia projektu migracji, jak znaleźć się dokładnie region platformy Azure, czy przechowywania metadanych wykrytych?

Możesz przejść do **Essentials** sekcji **Przegląd** strony projektu, aby zidentyfikować dokładną lokalizację, w którym przechowywane są metadane. Lokalizacja jest wybranych losowo w lokalizacji geograficznej przez usługę Azure Migrate, i nie można go modyfikować. Jeśli chcesz utworzyć projekt w określonym regionie tylko, można użyć interfejsów API REST, aby utworzyć projekt migracji i przekazać odpowiedni region.

   ![Lokalizacja projektu](./media/troubleshooting-general/geography-location.png)

## <a name="collector-issues"></a>Problemy z modułu zbierającego

### <a name="deployment-of-azure-migrate-collector-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Wdrażanie usługi Azure Migrate Collector nie powiodło się z powodu błędu: Podany plik manifestu jest nieprawidłowy: Nieprawidłowy wpis manifestu pakietu OVF.

1. Upewnij się, jeśli plik OVA modułu zbierającego migracji platformy Azure jest ona pobierana poprawnie przez sprawdzenie wartości mieszania. Aby zweryfikować wartość skrótu, zapoznaj się z [artykułem](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance). Jeśli wartość skrótu nie jest zgodny, należy ponownie pobrać plik OVA i ponowieniem próby wdrożenia.
2. Jeśli problemy nadal występują, a plik OVF jest wdrażany przy użyciu klienta oprogramowania VMware vSphere, spróbuj wdrożyć go za pomocą internetowego klienta programu vSphere. Jeśli nadal nie, spróbuj użyć innej przeglądarki sieci web.
3. Jeśli są przy użyciu klienta sieci web vSphere, w trakcie wdrażania jej na vCenter Server 6.5 lub 6.7 próby wdrażanie OVA bezpośrednio na hoście ESXi, wykonując następujące czynności:
   - Połączenia z hostem ESXi bezpośrednio (zamiast serwera vCenter) za pomocą klienta usługi sieci web (https:// <*adres IP hosta*> /ui)
   - Przejdź do strony głównej > Spis
   - Kliknij pozycję Plik > szablon OVF wdrażanie > Przejdź do OVA i ukończyć wdrażanie
4. Jeśli wdrożenie nadal kończy się niepowodzeniem, należy się z pomocą techniczną usługi Azure Migrate.

### <a name="unable-to-select-the-azure-cloud-in-the-appliance-fails-with-error-azure-cloud-selection-failed"></a>Nie można wybrać Azure chmury do urządzenia, a kończy się niepowodzeniem z powodu błędu "Zaznaczenia nie powiodło się w chmurze platformy Azure"

Jest to znany problem i poprawka jest dostępna dla problemu. Pobierz [najnowsze uaktualnienie usługi bits](https://docs.microsoft.com/azure/migrate/concepts-collector-upgrade#continuous-discovery-upgrade-versions) urządzenia i aktualizacji urządzenia w celu zastosowania poprawki.

### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Moduł zbierający nie jest w stanie połączyć się z Internetem

Może to nastąpić, gdy maszyny, którego używasz znajduje się za serwerem proxy. Upewnij się, że podajesz poświadczenia autoryzacji, jeśli serwer proxy wymaga jednego.
Jeśli używane są wszystkie opartego na adresach URL serwera proxy zapory do sterowania ruchem wychodzącym, upewnij się, do listy dozwolonych tych wymaganych adresów URL:

**Adres URL** | **Cel**  
--- | ---
*.portal.azure.com | Wymagany do sprawdzania łączności z usługą platformy Azure i weryfikowanie synchronizacji czasu problemy.
*.oneget.org | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter.

**Moduł zbierający nie może połączyć się z Internetem z powodu niepowodzenia weryfikacji certyfikatu**

Może to nastąpić, jeśli używasz przechwytujący serwer proxy do łączenia się z Internetem, a certyfikat serwera proxy na maszynie Wirtualnej modułu zbierającego nie została zaimportowana. Można zaimportować certyfikat serwera proxy wykonując kroki szczegółowe [tutaj](https://docs.microsoft.com/azure/migrate/concepts-collector).

**Moduł zbierający nie można połączyć z projektu przy użyciu Identyfikatora projektu i kluczy I skopiowane z portalu.**

Upewnij się, że zostały skopiowane i wklejone odpowiednie informacje. Aby rozwiązać problemy, zainstaluj program Microsoft Monitoring Agent (MMA) i sprawdzić, jeśli programu MMA łączy do projektu w następujący sposób:

1. Na maszynie Wirtualnej modułu zbierającego, Pobierz [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Aby rozpocząć instalację, kliknij dwukrotnie pobrany plik.
3. W Instalatorze na **powitalnej** kliknij **dalej**. Na stronie **Postanowienia licencyjne** kliknij przycisk **Zgadzam się**, aby zaakceptować warunki licencji.
4. W **Folder docelowy**, zachować lub zmienić domyślny folder instalacji > **dalej**.
5. W **opcje instalacji agenta**, wybierz opcję **usługi Azure Log Analytics** > **dalej**.
6. Kliknij przycisk **Dodaj** Aby dodać nowy obszar roboczy usługi Log Analytics. Wklej identyfikator i klucz, który został skopiowany projektu. Następnie kliknij przycisk **Next** (Dalej).
7. Sprawdź, czy agent może połączyć się projekt. Jeśli nie jest, sprawdź ustawienia. Jeśli agent można połączyć z modułu zbierającego nie może jednak się z pomocą techniczną.


### <a name="error-802-date-and-time-synchronization-error"></a>Błąd 802: Błąd synchronizacji daty i godziny

Zegar serwera musi być typu "out synchronizacji" z bieżącym czasem przez więcej niż pięć minut. Zmiana czasu zegara w module zbierającym maszyny Wirtualnej, aby dopasować bieżący czas, w następujący sposób:

1. Otwórz wiersz polecenia administratora na maszynie Wirtualnej.
2. Aby sprawdzić strefę czasową, uruchom w32tm /tz.
3. Aby zsynchronizować czas, należy uruchomić w32tm/resync.

### <a name="vmware-powercli-installation-failed"></a>Instalacja programu VMware PowerCLI nie powiodła się

Usługa Azure Migrate modułu zbierającego pobiera interfejs PowerCLI i instaluje je na urządzeniu. Wystąpił błąd podczas instalacji program PowerCLI może być spowodowany nieosiągalny punkty końcowe dla repozytorium interfejsu PowerCLI. Aby rozwiązać problemy, spróbuj wykonać ręczne instalowanie interfejsu PowerCLI w module zbierającym maszyny Wirtualnej, wykonując poniższe czynności:

1. Otwórz program Windows PowerShell w trybie administratora
2. Przejdź do katalogu C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. Uruchom skrypt InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception"></a>Wystąpił błąd wewnętrzny UnhandledException: System.IO.FileNotFoundException

Ten problem może wystąpić z powodu problemu z instalacją programu VMware PowerCLI. Wykonaj poniższe kroki, aby rozwiązać ten problem:

1. Jeśli nie jesteś w najnowszej wersji urządzenia modułu zbierającego [Uaktualnij moduł zbierający do najnowszej wersji](https://aka.ms/migrate/col/checkforupdates) i sprawdź, czy problem został rozwiązany.
2. Jeśli masz już najnowszą wersję modułu zbierającego, wykonaj poniższe kroki, aby wykonać czystą instalację programu PowerCLI:

   a. Zamknij przeglądarkę sieci web w urządzeniu.

   b. Zatrzymaj usługę Azure Migrate Collector, przechodząc do Windows Service Manager (Otwórz "Uruchom" i typ services.msc otworzyć Windows Service Manager). Kliknij prawym przyciskiem myszy kliknij usługę modułu zbierającego migracji platformy Azure, a następnie kliknij przycisk Zatrzymaj.

   c. Usuń wszystkie foldery, rozpoczynając od "VMware, w następujących lokalizacjach: C:\Program Files\WindowsPowerShell\Modules  
        C:\Program Files (x86)\WindowsPowerShell\Modules

   d. Uruchom ponownie usługę Azure Migrate Collector w Windows Service Manager (Otwórz "Uruchom" i typ services.msc otworzyć Windows Service Manager). Kliknij prawym przyciskiem myszy kliknij usługę modułu zbierającego migracji platformy Azure, a następnie kliknij przycisk Uruchom.

   e. Kliknij dwukrotnie skrót "Uruchom moduł zbierający" Aby uruchomić aplikację modułu zbierającego. Aplikację modułu zbierającego automatycznie należy pobrać i zainstalować wymaganą wersję PowerCLI.

3. Jeśli powyższe nie rozwiąże problemu, wykonaj kroki do c powyżej, a następnie ręcznie zainstaluj interfejs PowerCLI w urządzeniu wykonując następujące czynności:

   a. Wyczyścić wszystkie niekompletne PowerCLI pliki instalacyjne, wykonując kroki #do #c w kroku 2 # powyżej.

   b. Przejdź do menu Start > Uruchom > Otwórz PowerShell(x86) Windows w trybie administratora

   c. Uruchom polecenie:  Install-Module "VMWare.VimAutomation.Core" - RequiredVersion "6.5.2.6234650" (typ 'A' prosi o potwierdzenie)

   d. Uruchom ponownie usługę Azure Migrate Collector w Windows Service Manager (Otwórz "Uruchom" i typ services.msc otworzyć Windows Service Manager). Kliknij prawym przyciskiem myszy kliknij usługę modułu zbierającego migracji platformy Azure, a następnie kliknij przycisk Uruchom.

   e. Kliknij dwukrotnie skrót "Uruchom moduł zbierający" Aby uruchomić aplikację modułu zbierającego. Aplikację modułu zbierającego automatycznie należy pobrać i zainstalować wymaganą wersję PowerCLI.

4. Jeśli nie można załadować modułu w urządzeniu ze względu na problemy z zaporą, należy pobrać i zainstalować moduł w komputerze, który ma dostęp do Internetu wykonując następujące czynności:

    a. Wyczyścić wszystkie niekompletne PowerCLI pliki instalacyjne, wykonując kroki #do #c w kroku 2 # powyżej.

    b. Przejdź do menu Start > Uruchom > Otwórz PowerShell(x86) Windows w trybie administratora

    c. Uruchom polecenie:  Install-Module "VMWare.VimAutomation.Core" - RequiredVersion "6.5.2.6234650" (typ 'A' prosi o potwierdzenie)

    d. Skopiuj wszystkie moduły, rozpoczynając od "VMware" z "C:\Program Files (x86) \WindowsPowerShell\Modules" w tej samej lokalizacji na maszynie Wirtualnej modułu zbierającego.

    e. Uruchom ponownie usługę Azure Migrate Collector w Windows Service Manager (Otwórz "Uruchom" i typ services.msc otworzyć Windows Service Manager). Kliknij prawym przyciskiem myszy kliknij usługę modułu zbierającego migracji platformy Azure, a następnie kliknij przycisk Uruchom.

    f. Kliknij dwukrotnie skrót "Uruchom moduł zbierający" Aby uruchomić aplikację modułu zbierającego. Aplikację modułu zbierającego automatycznie należy pobrać i zainstalować wymaganą wersję PowerCLI.

### <a name="error-unabletoconnecttoserver"></a>Error UnableToConnectToServer

Nie można nawiązać połączenia z programem vCenter Server „Servername.com:9443” z powodu następującego błędu: Brak punktów końcowych nasłuchujących w lokalizacji https://Servername.com:9443/sdk, które mogłyby zaakceptować komunikat.

Sprawdź, jeśli możesz korzystają z najnowszej wersji urządzenia modułu zbierającego, jeśli nie, uaktualnić urządzenie [najnowszej wersji](https://docs.microsoft.com/azure/migrate/concepts-collector).

Jeśli problem nadal występuje w najnowszej wersji, może to być, ponieważ maszyny modułu zbierającego nie może rozpoznać nazwy programu vCenter Server zostało określone lub określony port jest nieprawidłowy. Domyślnie jeśli nie określono portu, moduł zbierający spróbuje nawiązać połączenie z portem o numerze 443.

1. Spróbuj wysłać polecenie ping dla adresu Servername.com z maszyny modułu zbierającego.
2. Jeśli nie możesz wykonać kroku 1, spróbuj połączyć się z programem vCenter Server za pośrednictwem adresu IP.
3. Podaj prawidłowy numer portu, aby nawiązać połączenie z programem vCenter.
4. Na koniec sprawdź, czy program vCenter Server działa.

### <a name="antivirus-exclusions"></a>Wykluczenia programu antywirusowego

Zabezpieczyć urządzenie Azure Migrate, musisz wykluczyć następujące foldery, w urządzeniu ze skanowania antywirusowego:

- Folder, który zawiera pliki binarne usługi migracji platformy Azure. Wyklucz wszystkie podfoldery.
  %ProgramFiles%\ProfilerService  
- Usługa Azure Migrate aplikacji sieci Web. Wyklucz wszystkie podfoldery.
  %SystemDrive%\inetpub\wwwroot
- Lokalnej pamięci podręcznej dla plików dziennika i bazy danych. Usługa Azure migrate usługa wymaga RW dostęp do tego folderu.
  %SystemDrive%\Profiler

## <a name="dependency-visualization-issues"></a>Problemy z wizualizacji zależności

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Nie można odnaleźć funkcji wizualizacji zależności dla projektów platformy Azure Government.

Usługa Azure Migrate zależy od rozwiązania Service Map dla funkcji wizualizacji zależności, a ponieważ mapa usługi jest obecnie niedostępna na platformie Azure Government, ta funkcja nie jest dostępne na platformie Azure Government.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Po zainstalowaniu programu Microsoft Monitoring Agent (MMA) i agenta zależności maszyn wirtualnych w środowisku lokalnym, ale zależności są teraz wyświetlane w portalu usługi Azure Migrate.

Po zainstalowaniu agentów usługi Azure Migrate trwa zwykle 15 – 30 minut, aby wyświetlić zależności w portalu. Jeśli upłynął czas potrzebny na ponad 30 minut, upewnij się, że MMA agent mogła komunikować się obszar roboczy pakietu OMS, wykonując następujące czynności:

Dla maszyn wirtualnych Windows:
1. Przejdź do **Panelu sterowania** i uruchom **Microsoft Monitoring Agent**
2. Przejdź do **Azure Log Analytics (OMS)** karty we właściwościach programu MMA wyskakujących
3. Upewnij się, że **stan** dla obszaru roboczego jest zielony.
4. Jeśli nie jest w stanie zielony, spróbuj usunąć obszar roboczy i ponownie dodać do programu MMA.
        ![Stan programu MMA](./media/troubleshooting-general/mma-status.png)

Dla maszyny Wirtualnej systemu Linux upewnij się, że zakończyło się polecenia instalacji agenta MMA i zależności.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Co to są systemów operacyjnych obsługiwanych przez MMA?

Lista systemów operacyjnych Windows obsługiwanych przez MMA jest [tutaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Lista systemów operacyjnych Linux obsługiwane przez MMA jest [tutaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Co to są systemy operacyjne obsługiwane przez agenta zależności?

Lista systemów operacyjnych Windows obsługiwanych przez agenta zależności jest [tutaj](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
Lista systemów operacyjnych Linux obsługiwane przez agenta zależności jest [tutaj](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Nie można wizualizować zależności w usłudze Azure Migrate dla więcej niż jedna godzina, czas trwania?
Usługa Azure Migrate umożliwia wizualizowanie zależności maksymalnie jedną godzinę czasu trwania. Mimo że usługa Azure Migrate umożliwia wróć do określonej daty w historii dla maksymalnie ostatni miesiąc, maksymalny czas trwania, dla którego można wizualizować zależności jest maksymalnie 1 godzinę. Na przykład można użyć funkcji okres czasu z mapy zależności, aby wyświetlić zależności dla wczoraj, ale tylko wtedy można wyświetlić okna jedną godzinę. Jednak można użyć usługi Azure Monitor dzienniki, aby [wykonywanie zapytań o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Nie można wizualizować zależności dla grup zawierających więcej niż 10 maszyn wirtualnych?
Możesz [wizualizacja zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) ma się do 10 maszyn wirtualnych, jeśli istnieje grupa z ponad 10 maszyn wirtualnych, zalecamy wizualizowanie zależności i podzielić grupy w mniejszym grupom.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Czy mogę zainstalowanych agentów i umożliwiają tworzenie grup wizualizacji zależności. Teraz po przejściu w tryb failover maszyny Pokaż akcję "Zainstaluj agentów" zamiast "Widok zależności"
* Wpis planowanej lub nieplanowanej pracy awaryjnej, lokalnych maszynach są wyłączone i równoważne maszyn są uruchamiane na platformie Azure. Te maszyny uzyskać inny adres MAC. Mogą one uzyskać na inny adres IP, które są oparte na tego, czy użytkownik wybrał opcję zachowania dla lokalnego adresu IP lub nie. Jeśli są różne adresy IP i MAC, usługa Azure Migrate nie kojarzy maszyn lokalnych z wszelkimi danymi, zależności mapy usługi i prosi użytkownika o zainstalowanie agentów zamiast wyświetlanie zależności.
* Opublikuj testowania trybu failover maszyn lokalnych włączone zgodnie z oczekiwaniami. Równoważne maszyn przetworzyliśmy na platformie Azure uzyskać inny adres MAC i może uzyskać inny adres IP. O ile nie blokuje użytkownika wychodzących usługi Azure Monitor dzienniki ruchu z tych maszyn, usługa Azure Migrate nie maszyn lokalnych z wszelkimi danymi, zależności mapy usługi i skojarzyć prosi użytkownika o zainstalowanie agentów zamiast wyświetlanie zależności.

## <a name="troubleshoot-azure-readiness-issues"></a>Rozwiązywanie problemów gotowości na platformę Azure

**Problem** | **Fix**
--- | ---
Nieobsługiwany typ rozruchu | Azure nie obsługuje maszyny wirtualne o typie rozruchu EFI. Zalecane jest, aby przekonwertować typ rozruchu systemu BIOS, przed rozpoczęciem migracji. <br/><br/>Możesz użyć [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) przeprowadzania migracji takich maszyn wirtualnych, zgodnie z jego przekonwertuje typ rozruchu maszyny wirtualnej systemu BIOS podczas migracji.
Warunkowo obsługiwany system operacyjny Windows | System operacyjny został przekazany na koniec okresu pomocy technicznej i musi niestandardowe obsługują umowy (CSA) dla [obsługi na platformie Azure](https://aka.ms/WSosstatement), Rozważ uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Nieobsługiwany system operacyjny Windows | Platforma Azure obsługuje tylko [wybranych wersji systemu operacyjnego Windows](https://aka.ms/WSosstatement), Rozważ uaktualnienie systemu operacyjnego maszyny przed migracją na platformę Azure.
Warunkowo wspierany system operacyjny Linux | Tylko platforma Azure wspiera [wybranych wersji systemu operacyjnego Linux](../virtual-machines/linux/endorsed-distros.md), Rozważ uaktualnienie systemu operacyjnego maszyny przed migracją na platformę Azure.
Niewspierany system operacyjny Linux | Maszynę można uruchomić na platformie Azure, ale nie zapewnia obsługi systemu operacyjnego znajduje się na platformie Azure, Rozważ uaktualnienie systemu operacyjnego w celu [zatwierdzonego dla wersji systemu Linux](../virtual-machines/linux/endorsed-distros.md) przed migracją na platformę Azure
Nieznany system operacyjny | System operacyjny maszyny wirtualnej została określona jako "Inne" w programie vCenter Server, z powodu której usługa Azure Migrate nie może ustalić gotowości na platformę Azure maszyny Wirtualnej. Upewnij się, że system operacyjny działający na maszynie [obsługiwane](https://aka.ms/azureoslist) przez platformę Azure, przed przeprowadzeniem migracji maszyny.
Nieobsługiwany typ systemu operacyjnego (liczba bitów) | Maszyny wirtualne z 32-bitowych systemach operacyjnych można uruchomić na platformie Azure, ale zaleca się uaktualnienie systemu operacyjnego maszyny wirtualnej z 32-bitowy na 64-bitowych przed migracją na platformę Azure.
Wymaga subskrypcji programu Visual Studio. | Na maszynach był uruchomioną wewnątrz której w systemie operacyjnym klienta Windows jest obsługiwana tylko w subskrypcji programu Visual Studio.
Nie znaleziono dla żądanej wartości wydajności magazynu maszyny Wirtualnej. | Wydajność magazynu (operacje We/Wy/przepływność) wymagany dla komputera przekracza obsługi maszyny Wirtualnej platformy Azure. Zmniejsz wymagania dotyczące magazynu dla maszyny przed migracją.
Nie znaleziono dla żądanej wartości wydajności sieci maszyny Wirtualnej. | Wydajność sieci (We/Wy) wymagany dla komputera przekracza obsługi maszyny Wirtualnej platformy Azure. Zmniejsz wymagania sieciowe dla maszyny.
Nie znaleziono w określonej lokalizacji maszyny Wirtualnej. | Użyj inną lokalizację docelową przed migracją.
Co najmniej jeden nieodpowiedni dysk. | Co najmniej jeden dysk dołączony do maszyny Wirtualnej nie spełnia wymagania platformy Azure. Dla każdego dysku, podłączonego do maszyny Wirtualnej upewnij się, że rozmiar dysku < 4 TB, jeśli nie, Zmniejsz rozmiar dysku przed migracją na platformę Azure. Upewnij się, że wydajność (operacje We/Wy/przepływność) wymagane przez każdy dysk jest obsługiwany przez platformę Azure [zarządzane dyski maszyny wirtualnej](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).   
Co najmniej jednej karty nieodpowiednią kartę sieciową. | Usunięcie nieużywanych kart sieciowych na komputerze przed migracją.
Liczba dysków przekracza limit | Usunięcie nieużywanych dysków z maszyny przed migracją.
Rozmiar dysku przekracza limit | Platforma Azure obsługuje dysków o rozmiarze 4 TB. Zmniejszanie dysków do mniej niż 4 TB przed migracją.
Dysk niedostępny w określonej lokalizacji | Upewnij się, że dysk jest w lokalizacji docelowej przed rozpoczęciem migracji.
Brak dostępnych dysków dla określonej nadmiarowości | Dysk należy używać typu magazynu nadmiarowości, które są zdefiniowane w ustawieniach oceny (LRS, domyślnie).
Nie można określić przydatności dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie znaleziono maszyny wirtualnej o żądanej liczbie rdzeni i pamięci | Azure nie można poprawnie odpowiedniego typu maszyny Wirtualnej. Przed przeprowadzeniem migracji, należy zmniejszyć pamięci i liczbę rdzeni na maszynie lokalnej.
Nie można określić gotowości maszyny Wirtualnej z powodu błędu wewnętrznego. | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić gotowości co najmniej jeden dysk z powodu błędu wewnętrznego. | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić gotowości co najmniej jednej karty sieciowej z powodu błędu wewnętrznego. | Spróbuj utworzyć nową ocenę dla grupy.


## <a name="collect-logs"></a>Zbieranie dzienników

**Jak zebrać dzienniki na maszynie Wirtualnej modułu zbierającego?**

Rejestrowanie jest domyślnie włączone. Dzienniki znajdują się w następujący sposób:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Aby zbierać zdarzenia śledzenia dla Windows, wykonaj następujące czynności:

1. Na maszynie Wirtualnej modułu zbierającego Otwórz okno poleceń programu PowerShell.
2. Uruchom **Get EventLog - Nazwa_dziennika aplikacji | export-csv eventlog.csv**.

**Jak zebrać dzienniki ruchu sieciowego portalu?**

1. Otwórz przeglądarkę i przejdź i zaloguj się [do portalu](https://portal.azure.com).
2. Naciśnij klawisz F12, aby uruchomić narzędzia dla deweloperów. Jeśli to konieczne, wyczyść ustawienie **Wyczyść wpisy w okienku nawigacji**.
3. Kliknij przycisk **sieci** , a następnie Rozpocznij przechwytywanie ruchu sieciowego:
   - W przeglądarce Chrome, zaznacz **dziennika Zachowaj**. Nagrywanie powinna być uruchamiana automatycznie. Czerwone kółko oznacza, że ruch jest przechwytywania. Jeśli nie pojawia się, kliknij przycisk koła czarny, uruchom
   - W programie Microsoft Edge, IE, rejestrowanie, powinna być uruchamiana automatycznie. W przeciwnym razie kliknij przycisk Odtwórz zielony.
4. Spróbuj odtworzyć błąd.
5. Po został napotkał błąd podczas rejestrowania, Zatrzymaj nagrywanie, a następnie zapisz kopię zarejestrowanych działań:
   - W przeglądarce Chrome, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **Zapisz jako plik HAR z zawartością**. Pakuje i eksportuje dzienniki w formacie .har.
   - W programie Microsoft Edge, IE, kliknij przycisk **eksportu przechwycić ruch** ikony. Pakuje i eksportuje dziennika.
6. Przejdź do **konsoli** kartę pod kątem ostrzeżeń i błędów. Aby zapisać dziennik konsoli:
   - W przeglądarce Chrome kliknij prawym przyciskiem myszy w dowolnym miejscu w dzienniku konsoli. Wybierz **Zapisz jako**, aby wyeksportować i zip dziennika.
   - W programie Microsoft Edge, IE, kliknij prawym przyciskiem myszy na błędy i wybierz **Kopiuj wszystko**.
7. Zamknij narzędzia dla deweloperów.

## <a name="collector-error-codes-and-recommended-actions"></a>Kody błędów modułu zbierającego i zalecane akcje

| Kod błędu | Nazwa błędu   | Komunikat   | Możliwe przyczyny | Zalecana akcja  |
| --- | --- | --- | --- | --- |
| 601       | CollectorExpired               | Moduł zbierający wygasł.                                                        | Moduł zbierający wygasł.                                                                                    | Pobierz nową wersję modułu zbierającego i ponów próbę.                                                                                      |
| 751       | UnableToConnectToServer        | Nie można nawiązać połączenia z serwerem vCenter Server „%Name;” z powodu błędu: %ErrorMessage;     | Więcej informacji można znaleźć w komunikacie o błędzie.                                                             | Rozwiąż problem i spróbuj ponownie.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | Serwer „%Name;” nie jest serwerem vCenter Server.                                  | Podaj szczegóły serwera vCenter Server.                                                                       | Spróbuj ponownie wykonać operację, używając poprawnych informacji o serwerze vCenter Server.                                                                                   |
| 753       | InvalidLoginCredentials        | Nie można nawiązać połączenia z serwerem vCenter „%Name;” z powodu błędu: %ErrorMessage; | Połączenie z serwerem vCenter nie powiodło się z powodu nieprawidłowych poświadczeń logowania.                             | Upewnij się, że podane poświadczenia logowania są poprawne.                                                                                    |
| 754       | NoPerfDataAvailable           | Dane wydajności są niedostępne.                                               | Sprawdź poziom statystyk w programie vCenter Server. Należy można ustawić na 3, aby dane wydajności mają być dostępne. | Zmień poziom statystyk na poziom 3 (na 5 minut, 30 minut lub 2 godziny) i spróbuj po odczekaniu co najmniej jednego dnia.                   |
| 756       | NullInstanceUUID               | Napotkano maszynę z identyfikatorem InstanceUUID o wartości null                                  | Serwer vCenter może mieć nieodpowiedni obiekt.                                                      | Rozwiąż problem i spróbuj ponownie.                                                                                                           |
| 757       | VMNotFound                     | Nie odnaleziono maszyny wirtualnej                                                  | Maszyna wirtualna może zostać usunięta: %VMID;                                                                | Upewnij się, że maszyny wirtualne wybrane podczas określania zakresu magazynu programu vCenter istnieją podczas odnajdywania                                      |
| 758       | GetPerfDataTimeout             | Upłynął limit czasu żądania programu VCenter. Komunikat % Message;                                  | Poświadczenia serwera vCenter są nieprawidłowe                                                              | Sprawdź poświadczenia serwera vCenter i upewnij się, że ten serwer vCenter jest dostępny. Ponów operację. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej. |
| 759       | VmwareDllNotFound              | Nie można odnaleźć biblioteki DLL VMWare.Vim.                                                     | Interfejs PowerCLI nie został prawidłowo zainstalowany.                                                                   | Sprawdź, czy prawidłowo zainstalowano PowerCLI. Ponów operację. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej.                               |
| 800       | ServiceError                   | Usługa Azure Migrate Collector nie jest uruchomiona.                               | Usługa Azure Migrate Collector nie jest uruchomiona.                                                       | Uruchom usługę za pomocą apletu services.msc i spróbuj ponownie wykonać operację.                                                                             |
| 801       | PowerCLIError                  | Instalowanie interfejsu PowerCLI programu VMware nie powiodło się.                                          | Instalowanie interfejsu PowerCLI programu VMware nie powiodło się.                                                                  | Ponów operację. Jeśli problem będzie się powtarzać, przeprowadzenie instalacji ręcznie i spróbuj ponownie wykonać operację.                                                   |
| 802       | TimeSyncError                  | Czas nie jest zsynchronizowany z internetowym serwerem czasu.                            | Czas nie jest zsynchronizowany z internetowym serwerem czasu.                                                    | Upewnij się, że godzina na maszynie jest ustawiona zgodnie ze strefą czasową maszyny i ponów operację.                                 |
| 702       | OMSInvalidProjectKey           | Określono nieprawidłowy klucz projektu.                                                | Określono nieprawidłowy klucz projektu.                                                                        | Spróbuj ponownie wykonać operację, używając poprawnego klucza projektu.                                                                                              |
| 703       | OMSHttpRequestException        | Wystąpił błąd podczas wysyłania żądania. Komunikat % Message;                                | Sprawdź identyfikator i klucz projektu, aby upewnić się, że punkt końcowy jest osiągalny.                                       | Ponów operację. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft.                                                                     |
| 704       | OMSHttpRequestTimeoutException | Upłynął limit czasu żądania HTTP. Komunikat % Message;                                     | Sprawdź identyfikator i klucz projektu, aby upewnić się, że punkt końcowy jest osiągalny.                                       | Ponów operację. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft.                                                                     |
