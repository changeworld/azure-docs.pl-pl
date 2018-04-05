---
title: Rozwiązywanie problemów z migracji Azure | Dokumentacja firmy Microsoft
description: Zawiera omówienie znanych problemów dotyczących usługi Azure migracji i rozwiązywania problemów wskazówki dotyczące typowych błędów.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: b2c89a980411cac02f46bc91d53620bc94fa845b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshoot-azure-migrate"></a>Rozwiązywanie problemów z usługą Azure Migrate

## <a name="troubleshoot-common-errors"></a>Rozwiązywanie typowych problemów

[Azure migracji](migrate-overview.md) ocenia obciążeń lokalnych do migracji do usługi Azure. W tym artykule umożliwiają rozwiązywanie problemów podczas wdrażania i przy użyciu migracji Azure.


**Moduł zbierający nie jest w stanie nawiązać połączenia z Internetem**

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
3. W Instalatorze na **powitalnej** kliknij przycisk **dalej**. Na **postanowień licencyjnych** kliknij przycisk **zgadzam się** do akceptowania licencji.
4. W **Folder docelowy**, Zachowaj lub zmienić domyślny folder instalacji > **dalej**.
5. W **opcje instalacji agenta**, wybierz pozycję **Analiza dzienników Azure (OMS)** > **dalej**.
6. Kliknij przycisk **Dodaj** Aby dodać nowy obszar roboczy analizy dzienników. Wklej identyfikator projektu i klucz skopiowane. Następnie kliknij przycisk **Next** (Dalej).
7. Sprawdź, czy agent może połączyć się z projektem. Jeśli nie, sprawdź ustawienia. Jeśli agent może połączyć, ale nie przez moduł zbierający, skontaktuj się z pomocą techniczną.


**Błąd 802: błąd datę i godzinę synchronizacji.**

Zegar serwera może być typu "out synchronizacji" przy użyciu bieżącego czasu, przez ponad pięć minut. Zmiana czasu zegara w module zbierającym maszyny Wirtualnej, aby dopasować bieżący czas, w następujący sposób:

1. Otwórz wiersz polecenia administratora na maszynie Wirtualnej.
2. Aby sprawdzić strefę czasową, uruchom w32tm /tz.
3. Aby zsynchronizować czas, należy uruchomić w32tm/resync.

**Mój klucz projektów ma "==" symbole w końcowej. Są one zakodowane, znaki alfanumeryczne przez moduł zbierający. Jest to oczekiwane?**

Tak, każdy klucz Projekt kończy się wyrazem "==". Moduł zbierający szyfruje klucz projektu przed jego przetworzeniem.

**Przedstawia dane wydajności dotyczące dysków i sieci karty jako zera**

Może to wystąpić, jeśli poziom ustawienie statystyk na serwer vCenter jest ustawiony na mniej niż trzech. Na poziomie 3 lub nowszym vCenter przechowuje historii wydajności maszyny Wirtualnej dla zasobów obliczeniowych, magazynu i sieci. Dla trzech poniżej poziomu vCenter nie przechowuje magazynu i sieci danych, ale tylko dane Procesora i pamięci. W tym scenariuszu wydajności pokazuje dane jako zero w migracji Azure i migracji Azure udostępnia zalecenia rozmiaru dysków i sieci na podstawie metadanych zebrane z komputerów lokalnych.

Aby włączyć zbieranie danych wydajności dysku i sieci, należy zmienić poziom ustawienia statystyki do trzech. Następnie należy poczekać co najmniej jeden dzień w celu odnajdywania środowisku i jego oceny.

**Agenci zostali zainstalowani i używane do tworzenia grup wizualizacji zależności. Teraz po pracy w trybie failover maszyny Pokaż akcji "Zainstaluj agentów" zamiast "Wyświetl zależności"**
* Post planowane lub nieplanowane przełączenie awaryjne, lokalne maszyny są wyłączone i maszyny równoważne są uruchomione na platformie Azure. Te maszyny uzyskać inny adres MAC. Mogą one uzyskać inny adres IP w oparciu Określa, czy użytkownik wybrał opcję zachowania lokalny adres IP, czy nie. Jeżeli różnią się adresy IP i MAC, Azure migracji nie wiąże lokalnymi maszynami z żadnych danych zależności mapy usług i pyta użytkownika, aby zainstalować agentów zamiast wyświetlanie zależności.
* Opublikuj testowania trybu failover maszyny lokalnej pozostają włączone zgodnie z oczekiwaniami. Odpowiednik maszyny przejścia na platformie Azure uzyskać inny adres MAC oraz może uzyskać inny adres IP. Chyba że użytkownik blokuje ruch wychodzący OMS z urządzeń, Azure migracji nie Skojarz lokalnymi maszynami z żadnych danych zależności mapy usług i pyta użytkownika, aby zainstalować agentów zamiast wyświetlanie zależności.


## <a name="troubleshoot-readiness-issues"></a>Rozwiązywanie problemów gotowości

**Problem** | **Fix**
--- | ---
Nieobsługiwany typ rozruchu | Azure nie obsługuje maszyn wirtualnych z typem rozruchu interfejsu EFI. Zalecane jest można przekonwertować typu rozruchu systemu BIOS, przed rozpoczęciem migracji. <br/><br/>Można użyć [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) przeprowadzania migracji takich maszyn wirtualnych, jak konwersji typu rozruchowego maszyny wirtualnej do systemu BIOS podczas migracji.
Warunkowo obsługiwanych systemu operacyjnego Windows | System operacyjny osiągnęła koniec okresu pomocy technicznej i musi niestandardowe obsługuje umowy (CSA) dla [obsługuje na platformie Azure](https://aka.ms/WSosstatement), Rozważ uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformie Azure.
Nieobsługiwany system Windows. | Azure obsługuje tylko [wybranych wersji systemu operacyjnego Windows](https://aka.ms/WSosstatement), Rozważ uaktualnienie systemu operacyjnego maszyny przed migracją do systemu Azure.
Warunkowo zatwierdzone systemu operacyjnego Linux | Azure wspiera tylko działania [wybranych wersji systemu operacyjnego Linux](../virtual-machines/linux/endorsed-distros.md), Rozważ uaktualnienie systemu operacyjnego maszyny przed migracją do systemu Azure.
Unendorsed system operacyjny Linux | Komputer może rozruchu w systemie Azure, ale nie obsługuje systemu operacyjnego są dostarczane przez platformę Azure, Rozważ uaktualnienie systemu operacyjnego w celu [zatwierdzone wersji systemu Linux](../virtual-machines/linux/endorsed-distros.md) przed migracją do systemu Azure
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
 - W programie Edge/IE rejestrowania powinna być uruchamiana automatycznie. W przeciwnym razie kliknij przycisk Odtwórz zielony.
4. Spróbuj odtwarzania błędu.
5. Po zostały napotkał błąd podczas rejestrowania, Zatrzymaj rejestrowanie i zapisać kopię zarejestrowanej czynności:
 - W przeglądarce Chrome, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **Zapisz jako HAR z zawartością**. To zips i eksportuje dzienniki w postaci pliku .har.
 - W programie Edge/IE, kliknij przycisk **eksportu przechwycone ruchu** ikony. To zips i eksportuje dziennika.
6. Przejdź do **konsoli** kartę, aby sprawdzić wszelkie ostrzeżenia lub błędy. Aby zapisać dziennik konsoli:
 - W przeglądarce Chrome kliknij prawym przyciskiem myszy w dzienniku konsoli. Wybierz **Zapisz jako**, aby wyeksportować i zip dziennika.
 - W programie Edge/IE, kliknij prawym przyciskiem myszy na błędy i wybierz **skopiuj wszystkie**.
7. Zamknij narzędzia dla deweloperów.


## <a name="vcenter-errors"></a>błędy vCenter

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Wystąpił błąd wewnętrzny UnhandledException: System.IO.FileNotFoundException

Jest to problem występujący w module zbierającym w wersjach starszych niż 1.0.9.5. Jeśli używasz modułu zbierającego w wersji 1.0.9.2 lub wersji wcześniejszych niż ogólnodostępne, takich jak 1.0.8.59, napotkasz ten problem. Użyj [tego linku, aby przejść do forów i uzyskać szczegółową odpowiedź](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate).

[Uaktualnij moduł zbierający, aby rozwiązać problem](https://aka.ms/migrate/col/checkforupdates).

### <a name="error-unabletoconnecttoserver"></a>Błąd UnableToConnectToServer

Nie można nawiązać połączenia z programem vCenter Server "Servername.com:9443" z powodu błędu: nie znaleziono żadnego punktu końcowego nasłuchiwania na https://Servername.com:9443/sdk który mógłby odebrać komunikat.

Dzieje się tak, gdy maszyna modułu zbierającego nie może rozpoznać podanej nazwy programu vCenter Server lub gdy wybrano nieprawidłowy port. Jeśli port nie zostanie określony, moduł zbierający domyślnie podejmie próbę połączenia z portem o numerze 443.

1. Spróbuj wykonać polecenie ping dla adresu Servername.com z maszyny modułu zbierającego.
2. Jeśli nie możesz wykonać kroku 1, spróbuj połączyć się z programem vCenter Server za pośrednictwem adresu IP.
3. Podaj prawidłowy numer portu, aby nawiązać połączenie z programem vCenter.
4. Na koniec sprawdź, czy program vCenter Server działa.

## <a name="collector-error-codes-and-recommended-actions"></a>Kody błędów modułu zbierającego i zalecane akcje

|           |                                |                                                                               |                                                                                                       |                                                                                                                                            | 
|-----------|--------------------------------|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------| 
| Kod błędu | Nazwa błędu                      | Komunikat                                                                       | Możliwe przyczyny                                                                                        | Zalecana akcja                                                                                                                          | 
| 601       | CollectorExpired               | Moduł zbierający wygasł.                                                        | Moduł zbierający wygasł.                                                                                    | Pobierz nową wersję modułu zbierającego i ponów próbę.                                                                                      | 
| 751       | UnableToConnectToServer        | Nie można nawiązać połączenia z serwerem vCenter Server „%Name;” z powodu błędu: %ErrorMessage;     | Więcej informacji można znaleźć w komunikacie o błędzie.                                                             | Rozwiąż problem i spróbuj ponownie.                                                                                                           | 
| 752       | InvalidvCenterEndpoint         | Serwer „%Name;” nie jest serwerem vCenter Server.                                  | Podaj szczegóły serwera vCenter.                                                                       | Spróbuj ponownie wykonać operację, używając poprawnych informacji o serwerze vCenter Server.                                                                                   | 
| 753       | InvalidLoginCredentials        | Nie można nawiązać połączenia z programem vCenter Server "% Name;" z powodu błędu: % ErrorMessage; | Połączenie z serwerem vCenter nie powiodło się z powodu nieprawidłowych poświadczeń logowania.                             | Upewnij się, że podane poświadczenia logowania są poprawne.                                                                                    | 
| 754       | NoPerfDataAvaialable           | Dane wydajności są niedostępne.                                               | Sprawdź poziom statystyk w programie vCenter Server. Należy wybrać na 3, aby dane wydajności mają być dostępne. | Zmień poziom statystyk na 3 (do 5 minut, 30 minut, a czas trwania 2 godziny), a następnie spróbuj po odczekaniu co najmniej jeden dzień.                   | 
| 756       | NullInstanceUUID               | Napotkano maszynę o wartości null InstanceUUID                                  | Serwer vCenter może być nieodpowiednie obiektu.                                                      | Rozwiąż problem i spróbuj ponownie.                                                                                                           | 
| 757       | VMNotFound                     | Nie znaleziono maszyny wirtualnej                                                  | Maszyny wirtualne mogą zostać usunięte: % VMID;                                                                | Upewnij się, że podczas określania zakresu spisu vCenter istnieje podczas odnajdywania zaznaczone maszyny wirtualne                                      | 
| 758       | GetPerfDataTimeout             | Upłynął limit czasu żądania VCenter. Komunikat % Message;                                  | poświadczenia serwera vCenter są nieprawidłowe                                                              | Sprawdź poświadczenia serwera vCenter i upewnij się, że ten serwer vCenter jest dostępny. Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocy technicznej. | 
| 759       | VmwareDllNotFound              | Nie można odnaleźć biblioteki DLL VMWare.Vim.                                                     | PowerCLI nie został poprawnie zainstalowany.                                                                   | Sprawdź, czy PowerCLI jest poprawnie zainstalowany. Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocy technicznej.                               | 
| 800       | ServiceError                   | Usługa Azure Migrate Collector nie jest uruchomiona.                               | Usługa Azure Migrate Collector nie jest uruchomiona.                                                       | Użyj services.msc, aby uruchomić tę usługę i spróbuj ponownie wykonać operację.                                                                             | 
| 801       | PowerCLIError                  | VMware PowerCLI instalacja nie powiodła się.                                          | VMware PowerCLI instalacja nie powiodła się.                                                                  | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, ręcznie zainstalować i spróbuj ponownie wykonać operację.                                                   | 
| 802       | TimeSyncError                  | Czas nie jest zsynchronizowany z internetowym serwerem czasu.                            | Czas nie jest zsynchronizowany z internetowym serwerem czasu.                                                    | Upewnij się, że czas na komputerze dokładnie jest ustawiona na strefę czasową na komputerze i spróbuj ponownie wykonać operację.                                 | 
| 702       | OMSInvalidProjectKey           | Określono nieprawidłowy klucz projektu.                                                | Określono nieprawidłowy klucz projektu.                                                                        | Spróbuj ponownie wykonać operację, używając poprawnego klucza projektu.                                                                                              | 
| 703       | OMSHttpRequestException        | Wystąpił błąd podczas wysyłania żądania. Komunikat % Message;                                | Sprawdź identyfikator projektu i klucz i upewnij się, że ten punkt końcowy jest dostępny.                                       | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft.                                                                     | 
| 704       | OMSHttpRequestTimeoutException | Upłynął limit czasu żądania HTTP. Komunikat % Message;                                     | Sprawdź identyfikator i klucz projektu, aby upewnić się, że punkt końcowy jest osiągalny.                                       | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft.                                                                     | 
