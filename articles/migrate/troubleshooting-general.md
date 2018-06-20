---
title: Rozwiązywanie problemów z migracji Azure | Dokumentacja firmy Microsoft
description: Zawiera omówienie znanych problemów dotyczących usługi Azure migracji i rozwiązywania problemów wskazówki dotyczące typowych błędów.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 896e918f6031f3bc6b925a2ecdfa2a5c82f00e0b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228258"
---
# <a name="troubleshoot-azure-migrate"></a>Rozwiązywanie problemów z usługą Azure Migrate

## <a name="troubleshoot-common-errors"></a>Rozwiązywanie typowych problemów

[Azure migracji](migrate-overview.md) ocenia obciążeń lokalnych do migracji do usługi Azure. W tym artykule umożliwiają rozwiązywanie problemów podczas wdrażania i przy użyciu migracji Azure.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Tworzenie projektu migracji nie powiodła się z powodu błędu *żądania musi zawierać nagłówki tożsamości użytkownika*

Ten problem może się zdarzyć dla użytkowników, którzy nie mają dostęp do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji. Gdy użytkownik zostanie dodany do dzierżawy usługi Azure AD po raz pierwszy, on odbiera wiadomości e-mail zaproszenie do dołączenia do dzierżawy. Użytkownicy muszą przejść do wiadomości e-mail i zaakceptować zaproszenie, aby pobrać zostało pomyślnie dodane do dzierżawcy. Jeśli nie możesz wyświetlić wiadomości e-mail, dotrzeć do użytkownika, który już ma dostęp do dzierżawy i poproś o wysłać zaproszenie do użytkownika za pomocą kroków określony [tutaj](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Po otrzymaniu wiadomości e-mail z zaproszeniem należy otworzyć wiadomości e-mail i kliknij łącze w wiadomości e-mail o zaakceptowanie zaproszenia. Po zakończeniu musisz wylogować się z portalu Azure i zaloguj ponownie, odświeżanie w przeglądarce nie będzie działać. Następnie możesz spróbować utworzyć projekt migracji.

### <a name="performance-data-for-disks-and-networks-adapters-shows-as-zeros"></a>Przedstawia dane wydajności dotyczące dysków i sieci karty jako zera

Może to wystąpić, jeśli poziom ustawienie statystyk na serwer vCenter jest ustawiony na mniej niż trzech. Na poziomie 3 lub nowszym vCenter przechowuje historii wydajności maszyny Wirtualnej dla zasobów obliczeniowych, magazynu i sieci. Dla trzech poniżej poziomu vCenter nie przechowuje magazynu i sieci danych, ale tylko dane Procesora i pamięci. W tym scenariuszu wydajności pokazuje dane jako zero w migracji Azure i migracji Azure udostępnia zalecenia rozmiaru dysków i sieci na podstawie metadanych zebrane z komputerów lokalnych.

Aby włączyć zbieranie danych wydajności dysku i sieci, należy zmienić poziom ustawienia statystyki do trzech. Następnie należy poczekać co najmniej jeden dzień w celu odnajdywania środowisku i jego oceny.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Agenci zostali zainstalowani i używane do tworzenia grup wizualizacji zależności. Teraz po pracy w trybie failover maszyny Pokaż akcji "Zainstaluj agentów" zamiast "Wyświetl zależności"
* Post planowane lub nieplanowane przełączenie awaryjne, lokalne maszyny są wyłączone i maszyny równoważne są uruchomione na platformie Azure. Te maszyny uzyskać inny adres MAC. Mogą one uzyskać inny adres IP w oparciu Określa, czy użytkownik wybrał opcję zachowania lokalny adres IP, czy nie. Jeżeli różnią się adresy IP i MAC, Azure migracji nie wiąże lokalnymi maszynami z żadnych danych zależności mapy usług i pyta użytkownika, aby zainstalować agentów zamiast wyświetlanie zależności.
* Opublikuj testowania trybu failover maszyny lokalnej pozostają włączone zgodnie z oczekiwaniami. Odpowiednik maszyny przejścia na platformie Azure uzyskać inny adres MAC oraz może uzyskać inny adres IP. Chyba że użytkownik blokuje ruch wychodzący analizy dzienników z urządzeń, Azure migracji nie wiąże lokalnymi maszynami z żadnych danych zależności mapy usług i pyta użytkownika, aby zainstalować agentów zamiast wyświetlania zależności.

## <a name="collector-errors"></a>Błędy modułu zbierającego

### <a name="deployment-of-collector-ova-failed"></a>Wdrożenia modułu zbierającego komórek jajowych nie powiodło się

Może się to zdarzyć czy komórki jajowe częściowo jest pobierana z powodu przeglądarki korzystania z klienta sieci web vSphere komórki jajowe wdrażania. Upewnij się, że pobranie zostanie ukończone i spróbuj przeprowadzić wdrożenie komórek jajowych z innej przeglądarki.

### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Moduł zbierający nie jest w stanie nawiązać połączenia z Internetem

Może to nastąpić, gdy maszyny, którego używasz znajduje się za serwerem proxy. Upewnij się, że podajesz poświadczenia autoryzacji, jeśli serwer proxy wymaga jednego.
Jeśli używasz dowolnego zapora oparta na adres URL serwera proxy do sterowania łączność wychodząca, upewnij się listą dozwolonych adresów IP są wymagane adresów URL:

**Adres URL** | **Cel**  
--- | ---
*.portal.azure.com | Wymagane sprawdzenie połączenia z usługą Azure i sprawdzić poprawności synchronizacji czasu problemy.
*.oneget.org | Wymagany do pobrania programu powershell na podstawie modułu PowerCLI vCenter.

**Moduł zbierający nie może połączyć się projektu za pomocą Identyfikatora projektu i klucza I skopiowany z portalu.**

Upewnij się, zostały skopiowane i wklejone odpowiednie informacje. Aby rozwiązać problemy, zainstaluj program Microsoft Monitoring Agent (MMA) i sprawdź, czy MMA można połączyć się z projektu w następujący sposób:

1. W module zbierającym maszyny Wirtualnej, należy pobrać [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Aby rozpocząć instalację, kliknij dwukrotnie pobrany plik.
3. W Instalatorze na **powitalnej** kliknij przycisk **dalej**. Na stronie **Postanowienia licencyjne** kliknij przycisk **Zgadzam się**, aby zaakceptować warunki licencji.
4. W **Folder docelowy**, Zachowaj lub zmienić domyślny folder instalacji > **dalej**.
5. W **opcje instalacji agenta**, wybierz pozycję **Azure Log Analytics** > **dalej**.
6. Kliknij przycisk **Dodaj** Aby dodać nowy obszar roboczy analizy dzienników. Wklej identyfikator projektu i klucz skopiowane. Następnie kliknij przycisk **Next** (Dalej).
7. Sprawdź, czy agent może połączyć się z projektem. Jeśli nie, sprawdź ustawienia. Jeśli agent może połączyć, ale nie przez moduł zbierający, skontaktuj się z pomocą techniczną.


### <a name="error-802-date-and-time-synchronization-error"></a>Błąd 802: Daty i godziny błąd synchronizacji

Zegar serwera może być typu "out synchronizacji" przy użyciu bieżącego czasu, przez ponad pięć minut. Zmiana czasu zegara w module zbierającym maszyny Wirtualnej, aby dopasować bieżący czas, w następujący sposób:

1. Otwórz wiersz polecenia administratora na maszynie Wirtualnej.
2. Aby sprawdzić strefę czasową, uruchom w32tm /tz.
3. Aby zsynchronizować czas, należy uruchomić w32tm/resync.

### <a name="vmware-powercli-installation-failed"></a>VMware PowerCLI instalacja nie powiodła się

Azure migracji modułu zbierającego PowerCLI pobiera i instaluje je na urządzeniu. Niepowodzenie instalacji PowerCLI mogło być spowodowane nieosiągalny punktów końcowych dla repozytorium PowerCLI. Aby rozwiązać problemy, spróbuj ręcznego instalowania PowerCLI w module zbierającym maszyny Wirtualnej przy użyciu następujący krok:

1. Otwórz program Windows PowerShell w trybie administratora
2. Przejdź do katalogu C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. Uruchom skrypt InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Wystąpił błąd wewnętrzny UnhandledException: System.IO.FileNotFoundException

Jest to problem występujący w module zbierającym w wersjach starszych niż 1.0.9.5. Jeśli używasz modułu zbierającego w wersji 1.0.9.2 lub wersji wcześniejszych niż ogólnodostępne, takich jak 1.0.8.59, napotkasz ten problem. Użyj [tego linku, aby przejść do forów i uzyskać szczegółową odpowiedź](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate).

[Uaktualnij moduł zbierający, aby rozwiązać problem](https://aka.ms/migrate/col/checkforupdates).

### <a name="error-unabletoconnecttoserver"></a>Błąd UnableToConnectToServer

Nie można nawiązać połączenia z programem vCenter Server "Servername.com:9443" z powodu błędu: nie znaleziono żadnego punktu końcowego nasłuchiwania na https://Servername.com:9443/sdk który mógłby odebrać komunikat.

Sprawdź, czy możesz korzystają z najnowszej wersji urządzenia modułu zbierającego, jeśli nie, uaktualnić urządzenie [najnowszej wersji](https://docs.microsoft.com/azure/migrate/concepts-collector#how-to-upgrade-collector).

Jeśli problem nadal występuje w najnowszej wersji, być może maszyny modułu zbierającego nie może rozpoznać określonej nazwy serwera vCenter lub określony port jest nieprawidłowy. Domyślnie jeśli port nie jest określony, moduł zbierający będzie spróbują połączyć się z numerem portu 443.

1. Spróbuj wykonać polecenie ping Servername.com z komputera modułu zbierającego.
2. Jeśli nie możesz wykonać kroku 1, spróbuj połączyć się z programem vCenter Server za pośrednictwem adresu IP.
3. Podaj prawidłowy numer portu, aby nawiązać połączenie z programem vCenter.
4. Na koniec sprawdź, czy program vCenter Server działa.

## <a name="troubleshoot-readiness-issues"></a>Rozwiązywanie problemów gotowości

**Problem** | **Fix**
--- | ---
Nieobsługiwany typ rozruchu | Azure nie obsługuje maszyn wirtualnych z typem rozruchu interfejsu EFI. Zalecane jest można przekonwertować typu rozruchu systemu BIOS, przed rozpoczęciem migracji. <br/><br/>Można użyć [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) przeprowadzania migracji takich maszyn wirtualnych, jak konwersji typu rozruchowego maszyny wirtualnej do systemu BIOS podczas migracji.
Warunkowo obsługiwanych systemu operacyjnego Windows | System operacyjny osiągnęła koniec okresu pomocy technicznej i musi niestandardowe obsługuje umowy (CSA) dla [obsługuje na platformie Azure](https://aka.ms/WSosstatement), Rozważ uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformie Azure.
Nieobsługiwany system operacyjny Windows | Azure obsługuje tylko [wybranych wersji systemu operacyjnego Windows](https://aka.ms/WSosstatement), Rozważ uaktualnienie systemu operacyjnego maszyny przed migracją do systemu Azure.
Warunkowo wspierany system operacyjny Linux | Azure wspiera tylko działania [wybranych wersji systemu operacyjnego Linux](../virtual-machines/linux/endorsed-distros.md), Rozważ uaktualnienie systemu operacyjnego maszyny przed migracją do systemu Azure.
Niewspierany system operacyjny Linux | Komputer może rozruchu w systemie Azure, ale nie obsługuje systemu operacyjnego są dostarczane przez platformę Azure, Rozważ uaktualnienie systemu operacyjnego w celu [zatwierdzone wersji systemu Linux](../virtual-machines/linux/endorsed-distros.md) przed migracją do systemu Azure
Nieznany system operacyjny | System operacyjny maszyny wirtualnej została określona jako "Inne" w programie vCenter Server, z powodu którego Azure migracji nie można zidentyfikować platformy Azure gotowości maszyny wirtualnej. Upewnij się, że system operacyjny uruchomiony na maszynie jest [obsługiwane](https://aka.ms/azureoslist) przez platformę Azure, przed przeprowadzeniem migracji maszyny.
Nieobsługiwany typ systemu operacyjnego (liczba bitów) | Maszyny wirtualne z 32-bitowego systemu operacyjnego może rozruchu w systemie Azure, ale zaleca się uaktualnienie systemu operacyjnego maszyny wirtualnej z 32-bitowej do 64-bitowych przed migracją do systemu Azure.
Wymagana jest subskrypcja programu Visual Studio. | Maszyny została uruchomiona w nim co systemu operacyjnego klienta systemu Windows jest obsługiwana tylko w ramach subskrypcji programu Visual Studio.
Nie można odnaleźć wydajności wymagane magazynu maszyny Wirtualnej. | Wydajność magazynu (IOPS/przepływność) wymagane dla komputera przekracza obsługi maszyny Wirtualnej platformy Azure. Zmniejsz wymagania dotyczące magazynu dla maszyny przed migracją.
Nie można odnaleźć wydajności wymagana sieć maszyny Wirtualnej. | Wydajność sieci (We/Wy) wymagane dla komputera przekracza obsługi maszyny Wirtualnej platformy Azure. Zmniejsz wymagania sieciowe dla komputera.
Maszyna wirtualna nie można odnaleźć w określonej warstwie cenowej. | Jeśli warstwa cenowa jest ustawiony na standardowy, należy wziąć pod uwagę downsizing maszyny Wirtualnej przed migracją do systemu Azure. Jeśli zmiany rozmiaru warstwy Basic, należy rozważyć zmianę warstwy cenowej oceny Standard.
Maszyna wirtualna nie można odnaleźć w określonej lokalizacji. | Użyj inną lokalizację docelową przed migracją.
Co najmniej jeden dysk nieodpowiednie. | Co najmniej jeden dysk dołączony do maszyny Wirtualnej nie spełniają wymagań platformy Azure. Dla każdego dysku do maszyny Wirtualnej upewnij się, że rozmiar dysku jest < 4 TB, jeśli nie, Zmniejsz rozmiar dysku przed migracją do systemu Azure. Upewnij się, że wydajność (IOPS/przepływność) wymagane przez każdy dysk jest obsługiwany przez platformę Azure [zarządzane dysków maszyny wirtualnej](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).   
Jeden lub więcej kart sieciowych nie nadaje się. | Usuń nieużywane sieciowe z maszyny przed migracją.
Liczba dysków przekracza limit | Usuń nieużywane dyski na komputerze przed migracją.
Rozmiar dysku przekracza limit | Azure obsługuje dyski o rozmiarze nieprzekraczającym 4 TB. Zmniejszanie dysków do mniej niż 4 TB przed migracją.
Dysk niedostępny w określonej lokalizacji | Upewnij się, że dysk jest w lokalizacji docelowej, przed przeprowadzeniem migracji.
Brak dostępnych dysków dla określonej nadmiarowości | Dysk należy używać typu magazynu nadmiarowość zdefiniowanego w ustawieniach oceny (LRS domyślnie).
Nie można określić przydatności dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nowy oceny grupy.
Nie znaleziono maszyny wirtualnej o żądanej liczbie rdzeni i pamięci | Azure nie drobne odpowiedniego typu maszyny Wirtualnej. Ograniczenia pamięci i liczby rdzeni na lokalnej maszynie, przed przeprowadzeniem migracji.
Nie można określić przydatności maszyny Wirtualnej z powodu błędu wewnętrznego. | Spróbuj utworzyć nowy oceny grupy.
Nie można określić przydatności do co najmniej jeden dysk z powodu błędu wewnętrznego. | Spróbuj utworzyć nowy oceny grupy.
Nie można określić przydatności do co najmniej jednej karty sieciowej z powodu błędu wewnętrznego. | Spróbuj utworzyć nowy oceny grupy.


## <a name="collect-logs"></a>Zbieranie dzienników

**Jak zebrać dzienniki w module zbierającym maszyny Wirtualnej?**

Rejestrowanie jest domyślnie włączone. Dzienniki znajdują się w następujący sposób:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Aby zbierać zdarzenia śledzenia systemu Windows, wykonaj następujące czynności:

1. W module zbierającym maszyny Wirtualnej Otwórz okno poleceń programu PowerShell.
2. Uruchom **Get EventLog - Nazwa_dziennika aplikacji | export-csv eventlog.csv**.

**Jak zebrać dzienniki ruchu sieciowego portalu?**

1. Otwórz przeglądarkę i przejdź i zaloguj się za [do portalu](https://portal.azure.com).
2. Naciśnij klawisz F12, aby uruchomić narzędzia deweloperskie. Jeśli to konieczne, wyczyść ustawienie **Wyczyść wpisy w nawigacji**.
3. Kliknij przycisk **sieci** , a następnie uruchomić Przechwytywanie ruchu sieciowego:
 - W przeglądarce Chrome, wybierz **dziennika Preserve**. Rejestrowanie powinna być uruchamiana automatycznie. Czerwone koło wskazuje, że ruch jest przechwytywania. Jeśli nie zostanie wyświetlone, kliknij przycisk czarny okręgu do uruchomienia
 - W programie Microsoft Edge/IE rejestrowania powinna być uruchamiana automatycznie. W przeciwnym razie kliknij przycisk Odtwórz zielony.
4. Spróbuj odtwarzania błędu.
5. Po zostały napotkał błąd podczas rejestrowania, Zatrzymaj rejestrowanie i zapisać kopię zarejestrowanej czynności:
 - W przeglądarce Chrome, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **Zapisz jako HAR z zawartością**. To zips i eksportuje dzienniki w postaci pliku .har.
 - W programie Microsoft Edge/IE, kliknij przycisk **eksportu przechwycone ruchu** ikony. To zips i eksportuje dziennika.
6. Przejdź do **konsoli** kartę, aby sprawdzić wszelkie ostrzeżenia lub błędy. Aby zapisać dziennik konsoli:
 - W przeglądarce Chrome kliknij prawym przyciskiem myszy w dzienniku konsoli. Wybierz **Zapisz jako**, aby wyeksportować i zip dziennika.
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
| 754       | NoPerfDataAvaialable           | Dane wydajności są niedostępne.                                               | Sprawdź poziom statystyk w programie vCenter Server. Należy wybrać na 3, aby dane wydajności mają być dostępne. | Zmień poziom statystyk na poziom 3 (na 5 minut, 30 minut lub 2 godziny) i spróbuj po odczekaniu co najmniej jednego dnia.                   |
| 756       | NullInstanceUUID               | Napotkano maszynę z identyfikatorem InstanceUUID o wartości null                                  | Serwer vCenter może mieć nieodpowiedni obiekt.                                                      | Rozwiąż problem i spróbuj ponownie.                                                                                                           |
| 757       | VMNotFound                     | Nie odnaleziono maszyny wirtualnej                                                  | Maszyna wirtualna może zostać usunięta: %VMID;                                                                | Upewnij się, że maszyny wirtualne wybrane podczas określania zakresu magazynu programu vCenter istnieją podczas odnajdywania                                      |
| 758       | GetPerfDataTimeout             | Upłynął limit czasu żądania VCenter. Komunikat % Message;                                  | Poświadczenia serwera vCenter są nieprawidłowe                                                              | Sprawdź poświadczenia serwera vCenter i upewnij się, że ten serwer vCenter jest dostępny. Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocy technicznej. |
| 759       | VmwareDllNotFound              | Nie można odnaleźć biblioteki DLL VMWare.Vim.                                                     | Interfejs PowerCLI nie został prawidłowo zainstalowany.                                                                   | Sprawdź, czy PowerCLI jest poprawnie zainstalowany. Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocy technicznej.                               |
| 800       | ServiceError                   | Usługa Azure Migrate Collector nie jest uruchomiona.                               | Usługa Azure Migrate Collector nie jest uruchomiona.                                                       | Uruchom usługę za pomocą apletu services.msc i spróbuj ponownie wykonać operację.                                                                             |
| 801       | PowerCLIError                  | Instalowanie interfejsu PowerCLI programu VMware nie powiodło się.                                          | Instalowanie interfejsu PowerCLI programu VMware nie powiodło się.                                                                  | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, ręcznie zainstalować i spróbuj ponownie wykonać operację.                                                   |
| 802       | TimeSyncError                  | Czas nie jest zsynchronizowany z internetowym serwerem czasu.                            | Czas nie jest zsynchronizowany z internetowym serwerem czasu.                                                    | Upewnij się, że godzina na maszynie jest ustawiona zgodnie ze strefą czasową maszyny i ponów operację.                                 |
| 702       | OMSInvalidProjectKey           | Określono nieprawidłowy klucz projektu.                                                | Określono nieprawidłowy klucz projektu.                                                                        | Spróbuj ponownie wykonać operację, używając poprawnego klucza projektu.                                                                                              |
| 703       | OMSHttpRequestException        | Wystąpił błąd podczas wysyłania żądania. Komunikat % Message;                                | Sprawdź identyfikator i klucz projektu, aby upewnić się, że punkt końcowy jest osiągalny.                                       | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft.                                                                     |
| 704       | OMSHttpRequestTimeoutException | Upłynął limit czasu żądania HTTP. Komunikat % Message;                                     | Sprawdź identyfikator i klucz projektu, aby upewnić się, że punkt końcowy jest osiągalny.                                       | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft.                                                                     |
