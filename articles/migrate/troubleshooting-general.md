---
title: Rozwiązywanie problemów z usługi Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera omówienie znanych problemów dotyczących usługi Azure Migrate, a wskazówki dotyczące typowych błędów rozwiązywania problemów.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: raynew
ms.openlocfilehash: a41a27f2a87a67ea51bcbe110ac77f7908c44e7a
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945522"
---
# <a name="troubleshoot-azure-migrate"></a>Rozwiązywanie problemów z usługą Azure Migrate

## <a name="troubleshoot-common-errors"></a>Rozwiązywanie typowych problemów

[Usługa Azure Migrate](migrate-overview.md) ocenia obciążenia lokalne pod kątem migracji na platformę Azure. W tym artykule umożliwiają rozwiązywanie problemów dotyczących wdrażania i korzystania z usługi Azure Migrate.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Tworzenie projektu migracji nie powiodła się z powodu błędu *żądania muszą zawierać nagłówki tożsamości użytkownika*

Ten problem może się zdarzyć dla użytkowników, którzy nie mają dostępu do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji. Gdy użytkownik zostanie dodany do dzierżawy usługi Azure AD po raz pierwszy, ma uprawnienia otrzymuje wiadomość e-mail z zaproszeniem do dołączenia do dzierżawy. Użytkownicy muszą przejść do wiadomości e-mail i zaakceptować zaproszenie, aby pomyślnie poproś o dodanie do dzierżawy. Jeśli nie można wyświetlić adres e-mail, skontaktuj się użytkownika, który już ma dostęp do dzierżawy i poproś go o Wyślij ponownie zaproszenie do użytkownika za pomocą kroki określone [tutaj](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Po otrzymaniu wiadomości e-mail z zaproszeniem, musisz otworzyć wiadomości e-mail i kliknij łącze w wiadomości e-mail o zaakceptowanie zaproszenia. Po zakończeniu tej operacji należy się wylogować z witryny Azure portal, a następnie zaloguj się ponownie, odświeżeniu przeglądarki nie będą działać. Następnie możesz spróbować utworzyć projekt migracji.

### <a name="performance-data-for-disks-and-networks-adapters-shows-as-zeros"></a>Dane wydajności dla dysków i sieci kart sieciowych jest wyświetlany jako zera

Może to wystąpić, jeśli poziom ustawień statystyk na serwerze vCenter jest ustawiony do poniżej trzech. Na poziomie 3 lub nowszej vCenter przechowuje historię wydajności maszyn wirtualnych dla obliczeń, magazynu i sieci. Dla mniej niż poziom 3 vCenter nie przechowuje magazyn i dane sieci, ale tylko dane procesora CPU i pamięci. W tym scenariuszu wydajności pokazuje dane jako zero w usłudze Azure Migrate, a usługa Azure Migrate oferuje zalecenie dotyczące rozmiaru dysków i sieci na podstawie metadanych zebranych z maszyn lokalnych.

Aby włączyć zbieranie danych wydajności dysku i sieci, należy zmienić ustawienia poziomu statystyk do trzech. Następnie należy poczekać co najmniej dzień, aby odnaleźć środowiska i ocenić jej.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Czy mogę zainstalowanych agentów i umożliwiają tworzenie grup wizualizacji zależności. Teraz po przejściu w tryb failover maszyny Pokaż akcję "Zainstaluj agentów" zamiast "Widok zależności"
* Wpis planowanej lub nieplanowanej pracy awaryjnej, lokalnych maszynach są wyłączone i równoważne maszyn są uruchamiane na platformie Azure. Te maszyny uzyskać inny adres MAC. Mogą one uzyskać na inny adres IP, które są oparte na tego, czy użytkownik wybrał opcję zachowania dla lokalnego adresu IP lub nie. Jeśli są różne adresy IP i MAC, usługa Azure Migrate nie kojarzy maszyn lokalnych z wszelkimi danymi, zależności mapy usługi i prosi użytkownika o zainstalowanie agentów zamiast wyświetlanie zależności.
* Opublikuj testowania trybu failover maszyn lokalnych włączone zgodnie z oczekiwaniami. Równoważne maszyn przetworzyliśmy na platformie Azure uzyskać inny adres MAC i może uzyskać inny adres IP. Chyba że użytkownik blokuje ruch wychodzący usługi Log Analytics z tych maszyn, usługa Azure Migrate nie kojarzy maszyn lokalnych z wszelkimi danymi, zależności mapy usługi i prosi użytkownika o zainstalowanie agentów zamiast wyświetlanie zależności.

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>Lokalizacja geograficzna platformy Azure, czy określone podczas tworzenia projektu migracji, jak znaleźć się dokładnie region platformy Azure, czy przechowywania metadanych wykrytych?

Możesz przejść do **Essentials** sekcji **Przegląd** strony projektu, aby zidentyfikować dokładną lokalizację, w którym przechowywane są metadane. Lokalizacja jest wybranych losowo w lokalizacji geograficznej przez usługę Azure Migrate, i nie można go modyfikować. Jeśli chcesz utworzyć projekt w określonym regionie tylko, można użyć interfejsów API REST, aby utworzyć projekt migracji i przekazać odpowiedni region.

   ![Lokalizacja projektu](./media/troubleshooting-general/geography-location.png)

### <a name="i-am-using-the-continuous-discovery-ova-but-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Używam ciągłe odnajdywania, którą OVA, ale maszyny wirtualne, które są usuwane z mojego lokalnego środowiska nadal są wyświetlane w portalu.

Urządzenia dla urządzenia odnajdywania ciągłe tylko zbiera dane dotyczące wydajności stale, nie wykrywa zmiany konfiguracji w środowisku lokalnym, (tj. Dodawanie maszyny Wirtualnej, usuwania, dodawania dysku itp.). W przypadku zmiany konfiguracji w środowisku lokalnym, możesz wykonać następujące polecenie, aby odzwierciedlały zmiany w portalu:

1. Dodawanie elementów (maszyn wirtualnych, dysków, liczba rdzeni itp.): aby uwzględnić te zmiany w witrynie Azure portal, można zatrzymać odnajdywania przez urządzenie i uruchom go ponownie. Pozwoli to zagwarantować, że zmiany są uwzględniane w projekcie usługi Azure Migrate.

2. Usuwanie maszyn wirtualnych: ze względu na sposób zaprojektowano urządzenia, usunięcie maszyny wirtualne nie zostaną uwzględnione nawet, gdy zatrzymujesz i uruchamiasz odnajdywania. Jest to spowodowane dane z kolejne operacje odnajdywania są dołączane do odnajdywania starszych i nie zostanie zastąpiona. W takim przypadku możesz po prostu zignorować maszyny Wirtualnej w portalu, usuwając go z grupy i ponownego obliczania oceny.

## <a name="collector-errors"></a>Błędy modułu zbierającego dzienniki

### <a name="deployment-of-collector-ova-failed"></a>Wdrażanie modułu zbierającego OVA nie powiodło się

Może się to zdarzyć czy OVA częściowo zostanie pobrana z powodu przeglądarki korzystania z klienta internetowego vSphere do wdrożenia OVA. Upewnij się, że pobranie zostanie ukończone, a następnie spróbuj wdrożyć OVA przy użyciu innej przeglądarki.

### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Moduł zbierający nie jest w stanie połączyć się z Internetem

Może to nastąpić, gdy maszyny, którego używasz znajduje się za serwerem proxy. Upewnij się, że podajesz poświadczenia autoryzacji, jeśli serwer proxy wymaga jednego.
Jeśli używane są wszystkie opartego na adresach URL serwera proxy zapory do sterowania ruchem wychodzącym, upewnij się, do listy dozwolonych tych wymaganych adresów URL:

**Adres URL** | **Cel**  
--- | ---
*.portal.azure.com | Wymagany do sprawdzania łączności z usługą platformy Azure i weryfikowanie synchronizacji czasu problemy.
*.oneget.org | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter.

**Moduł zbierający nie może połączyć się z Internetem z powodu niepowodzenia weryfikacji certyfikatu**

Może to nastąpić, jeśli używasz przechwytujący serwer proxy do łączenia się z Internetem, a certyfikat serwera proxy na maszynie Wirtualnej modułu zbierającego nie została zaimportowana. Można zaimportować certyfikat serwera proxy wykonując kroki szczegółowe [tutaj](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity).

**Moduł zbierający nie można połączyć z projektu przy użyciu Identyfikatora projektu i kluczy I skopiowane z portalu.**

Upewnij się, że zostały skopiowane i wklejone odpowiednie informacje. Aby rozwiązać problemy, zainstaluj program Microsoft Monitoring Agent (MMA) i sprawdzić, jeśli programu MMA łączy do projektu w następujący sposób:

1. Na maszynie Wirtualnej modułu zbierającego, Pobierz [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Aby rozpocząć instalację, kliknij dwukrotnie pobrany plik.
3. W Instalatorze na **powitalnej** kliknij **dalej**. Na stronie **Postanowienia licencyjne** kliknij przycisk **Zgadzam się**, aby zaakceptować warunki licencji.
4. W **Folder docelowy**, zachować lub zmienić domyślny folder instalacji > **dalej**.
5. W **opcje instalacji agenta**, wybierz opcję **usługi Azure Log Analytics** > **dalej**.
6. Kliknij przycisk **Dodaj** Aby dodać nowy obszar roboczy usługi Log Analytics. Wklej identyfikator i klucz, który został skopiowany projektu. Następnie kliknij przycisk **Next** (Dalej).
7. Sprawdź, czy agent może połączyć się projekt. Jeśli nie jest, sprawdź ustawienia. Jeśli agent można połączyć z modułu zbierającego nie może jednak się z pomocą techniczną.


### <a name="error-802-date-and-time-synchronization-error"></a>Błąd 802: Data i godzina błąd synchronizacji

Zegar serwera musi być typu "out synchronizacji" z bieżącym czasem przez więcej niż pięć minut. Zmiana czasu zegara w module zbierającym maszyny Wirtualnej, aby dopasować bieżący czas, w następujący sposób:

1. Otwórz wiersz polecenia administratora na maszynie Wirtualnej.
2. Aby sprawdzić strefę czasową, uruchom w32tm /tz.
3. Aby zsynchronizować czas, należy uruchomić w32tm/resync.

### <a name="vmware-powercli-installation-failed"></a>Instalacja programu VMware PowerCLI nie powiodła się

Usługa Azure Migrate modułu zbierającego pobiera interfejs PowerCLI i instaluje je na urządzeniu. Wystąpił błąd podczas instalacji program PowerCLI może być spowodowany nieosiągalny punkty końcowe dla repozytorium interfejsu PowerCLI. Aby rozwiązać problemy, spróbuj wykonać ręczne instalowanie interfejsu PowerCLI w module zbierającym maszyny Wirtualnej, wykonując poniższe czynności:

1. Otwórz program Windows PowerShell w trybie administratora
2. Przejdź do katalogu C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. Uruchom skrypt InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Wystąpił błąd wewnętrzny UnhandledException: System.IO.FileNotFoundException

Ten problem może wystąpić z powodu problemu z instalacją programu VMware PowerCLI. Wykonaj poniższe kroki, aby rozwiązać ten problem:

1. Jeśli nie jesteś w najnowszej wersji urządzenia modułu zbierającego [Uaktualnij moduł zbierający do najnowszej wersji](https://aka.ms/migrate/col/checkforupdates) i sprawdź, czy problem został rozwiązany.
2. Jeśli masz już najnowszą wersję modułu zbierającego, ręcznie zainstaluj [program VMware PowerCLI 6.5.2](https://www.powershellgallery.com/packages/VMware.PowerCLI/6.5.2.6268016) i sprawdź, czy problem został rozwiązany.
3. Jeśli powyższe nie rozwiązać ten problem, przejdź do folderu C:\Program Files\ProfilerService i Usuń VMware.dll i pliki VimService65.dll istnieje w folderze i ponownie uruchom usługę Azure Migrate Collector w Windows usługi zarządzania (Open " Uruchom ' i typu "services.msc", aby otworzyć Menedżera usługi Windows).

### <a name="error-unabletoconnecttoserver"></a>Błąd UnableToConnectToServer

Nie można nawiązać połączenia z programem vCenter Server „Servername.com:9443” z powodu następującego błędu: brak punktów końcowych nasłuchujących w lokalizacji https://Servername.com:9443/sdk, które mogłyby zaakceptować komunikat.

Sprawdź, jeśli możesz korzystają z najnowszej wersji urządzenia modułu zbierającego, jeśli nie, uaktualnić urządzenie [najnowszej wersji](https://docs.microsoft.com/azure/migrate/concepts-collector#how-to-upgrade-collector).

Jeśli problem nadal występuje w najnowszej wersji, może to być, ponieważ maszyny modułu zbierającego nie może rozpoznać nazwy programu vCenter Server zostało określone lub określony port jest nieprawidłowy. Domyślnie jeśli nie określono portu, moduł zbierający spróbuje nawiązać połączenie z portem o numerze 443.

1. Spróbuj wysłać polecenie ping dla adresu Servername.com z maszyny modułu zbierającego.
2. Jeśli nie możesz wykonać kroku 1, spróbuj połączyć się z programem vCenter Server za pośrednictwem adresu IP.
3. Podaj prawidłowy numer portu, aby nawiązać połączenie z programem vCenter.
4. Na koniec sprawdź, czy program vCenter Server działa.

## <a name="troubleshoot-dependency-visualization-issues"></a>Rozwiązywanie problemów z zależnością wizualizacji

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
Usługa Azure Migrate umożliwia wizualizowanie zależności maksymalnie jedną godzinę czasu trwania. Mimo że usługa Azure Migrate umożliwia wróć do określonej daty w historii dla maksymalnie ostatni miesiąc, maksymalny czas trwania, dla którego można wizualizować zależności jest maksymalnie 1 godzinę. Na przykład można użyć funkcji okres czasu z mapy zależności, aby wyświetlić zależności dla wczoraj, ale tylko wtedy można wyświetlić okna jedną godzinę.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Nie można wizualizować zależności dla grup zawierających więcej niż 10 maszyn wirtualnych?
Możesz [wizualizacja zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) ma się do 10 maszyn wirtualnych, jeśli istnieje grupa z ponad 10 maszyn wirtualnych, zalecamy wizualizowanie zależności i podzielić grupy w mniejszym grupom.

## <a name="troubleshoot-readiness-issues"></a>Rozwiązywanie problemów z problemy z gotowością

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
Maszyna wirtualna nie można odnaleźć w określonej warstwie cenowej. | Jeśli warstwa cenowa jest ustawiana na standardowa, należy rozważyć zmniejszenie rozmiaru maszyny Wirtualnej przed migracją na platformę Azure. Jeśli warstwa ustalania rozmiaru jest podstawowy, należy wziąć pod uwagę zmianę warstwy cenowej dla oceny na standardowa.
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
 - W programie Microsoft Edge/IE rejestrowania powinna być uruchamiana automatycznie. W przeciwnym razie kliknij przycisk Odtwórz zielony.
4. Spróbuj odtworzyć błąd.
5. Po został napotkał błąd podczas rejestrowania, Zatrzymaj nagrywanie, a następnie zapisz kopię zarejestrowanych działań:
 - W przeglądarce Chrome, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **Zapisz jako plik HAR z zawartością**. Pakuje i eksportuje dzienniki w formacie .har.
 - W programie Microsoft Edge/IE, kliknij przycisk **eksportu przechwycone ruchu** ikony. Pakuje i eksportuje dziennika.
6. Przejdź do **konsoli** kartę pod kątem ostrzeżeń i błędów. Aby zapisać dziennik konsoli:
 - W przeglądarce Chrome kliknij prawym przyciskiem myszy w dowolnym miejscu w dzienniku konsoli. Wybierz **Zapisz jako**, aby wyeksportować i zip dziennika.
 - W programie Microsoft Edge/IE, kliknij prawym przyciskiem myszy na błędy i wybierz **skopiuj wszystkie**.
7. Zamknij narzędzia dla deweloperów.

## <a name="collector-error-codes-and-recommended-actions"></a>Kody błędów modułu zbierającego i zalecane akcje

|           |                                |                                                                               |                                                                                                       |                                                                                                                                            |
|-----------|--------------------------------|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Kod błędu | Nazwa błędu                      | Komunikat                                                                       | Możliwe przyczyny                                                                                        | Zalecana akcja                                                                                                                          |
| 601       | CollectorExpired               | Moduł zbierający wygasł.                                                        | Moduł zbierający wygasł.                                                                                    | Pobierz nową wersję modułu zbierającego i ponów próbę.                                                                                      |
| 751       | UnableToConnectToServer        | Nie można nawiązać połączenia z serwerem vCenter Server „%Name;” z powodu błędu: %ErrorMessage;     | Więcej informacji można znaleźć w komunikacie o błędzie.                                                             | Rozwiąż problem i spróbuj ponownie.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | Serwer „%Name;” nie jest serwerem vCenter Server.                                  | Podaj szczegóły serwera vCenter Server.                                                                       | Spróbuj ponownie wykonać operację, używając poprawnych informacji o serwerze vCenter Server.                                                                                   |
| 753       | InvalidLoginCredentials        | Nie można nawiązać połączenia z serwerem vCenter „%Name;” z powodu błędu: %ErrorMessage; | Połączenie z serwerem vCenter nie powiodło się z powodu nieprawidłowych poświadczeń logowania.                             | Upewnij się, że podane poświadczenia logowania są poprawne.                                                                                    |
| 754       | NoPerfDataAvaialable           | Dane wydajności są niedostępne.                                               | Sprawdź poziom statystyk w programie vCenter Server. Należy można ustawić na 3, aby dane wydajności mają być dostępne. | Zmień poziom statystyk na poziom 3 (na 5 minut, 30 minut lub 2 godziny) i spróbuj po odczekaniu co najmniej jednego dnia.                   |
| 756       | NullInstanceUUID               | Napotkano maszynę z identyfikatorem InstanceUUID o wartości null                                  | Serwer vCenter może mieć nieodpowiedni obiekt.                                                      | Rozwiąż problem i spróbuj ponownie.                                                                                                           |
| 757       | VMNotFound                     | Nie odnaleziono maszyny wirtualnej                                                  | Maszyna wirtualna może zostać usunięta: %VMID;                                                                | Upewnij się, że maszyny wirtualne wybrane podczas określania zakresu magazynu programu vCenter istnieją podczas odnajdywania                                      |
| 758       | GetPerfDataTimeout             | Upłynął limit czasu żądania programu VCenter. Komunikat % Message;                                  | Poświadczenia serwera vCenter są nieprawidłowe                                                              | Sprawdź poświadczenia serwera vCenter i upewnij się, że ten serwer vCenter jest dostępny. Spróbuj ponownie wykonać operację. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej. |
| 759       | VmwareDllNotFound              | Nie można odnaleźć biblioteki DLL VMWare.Vim.                                                     | Interfejs PowerCLI nie został prawidłowo zainstalowany.                                                                   | Sprawdź, czy prawidłowo zainstalowano PowerCLI. Spróbuj ponownie wykonać operację. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej.                               |
| 800       | ServiceError                   | Usługa Azure Migrate Collector nie jest uruchomiona.                               | Usługa Azure Migrate Collector nie jest uruchomiona.                                                       | Uruchom usługę za pomocą apletu services.msc i spróbuj ponownie wykonać operację.                                                                             |
| 801       | PowerCLIError                  | Instalowanie interfejsu PowerCLI programu VMware nie powiodło się.                                          | Instalowanie interfejsu PowerCLI programu VMware nie powiodło się.                                                                  | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, przeprowadzenie instalacji ręcznie i spróbuj ponownie wykonać operację.                                                   |
| 802       | TimeSyncError                  | Czas nie jest zsynchronizowany z internetowym serwerem czasu.                            | Czas nie jest zsynchronizowany z internetowym serwerem czasu.                                                    | Upewnij się, że godzina na maszynie jest ustawiona zgodnie ze strefą czasową maszyny i ponów operację.                                 |
| 702       | OMSInvalidProjectKey           | Określono nieprawidłowy klucz projektu.                                                | Określono nieprawidłowy klucz projektu.                                                                        | Spróbuj ponownie wykonać operację, używając poprawnego klucza projektu.                                                                                              |
| 703       | OMSHttpRequestException        | Wystąpił błąd podczas wysyłania żądania. Komunikat % Message;                                | Sprawdź identyfikator i klucz projektu, aby upewnić się, że punkt końcowy jest osiągalny.                                       | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft.                                                                     |
| 704       | OMSHttpRequestTimeoutException | Upłynął limit czasu żądania HTTP. Komunikat % Message;                                     | Sprawdź identyfikator i klucz projektu, aby upewnić się, że punkt końcowy jest osiągalny.                                       | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft.                                                                     |
