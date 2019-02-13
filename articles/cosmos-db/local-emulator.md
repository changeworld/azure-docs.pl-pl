---
title: Programowanie w środowisku lokalnym przy użyciu emulatora usługi Azure Cosmos DB
description: Za pomocą emulatora usługi Azure Cosmos DB możesz bezpłatnie opracowywać i testować swoją aplikację w środowisku lokalnym. Subskrypcja platformy Azure nie jest potrzebna.
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 04/20/2018
author: deborahc
ms.author: dech
ms.openlocfilehash: cbdc57489eb7ebd50e3ce7e2b4e0e4081aef8e27
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770388"
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Programowanie i testowanie w środowisku lokalnym przy użyciu emulatora usługi Azure Cosmos DB

|**Pliki binarne**|[Pobierz plik MSI](https://aka.ms/cosmosdb-emulator)| |**Docker**|[Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)| |**Źródło Docker** | [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker)|

Emulator usługi Azure Cosmos DB zapewnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów programistycznych. Za pomocą emulatora usługi Azure Cosmos DB możesz lokalnie opracowywać i testować swoją aplikację bez konieczności tworzenia subskrypcji platformy Azure i ponoszenia kosztów. Jeśli sposób działania aplikacji w emulatorze usługi Azure Cosmos DB jest zadowalający, możesz zacząć korzystać z konta usługi Azure Cosmos DB w chmurze.

W tej chwili Eksplorator danych w emulatorze obsługuje w pełni tylko klientów dla interfejsu API SQL i interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Klienci interfejsu API tabel, interfejsu API programu Graph i interfejsu API rozwiązania Cassandra nie są w pełni obsługiwani.

W tym artykule opisano następujące zadania:

> [!div class="checklist"]
> * Instalowanie emulatora
> * Uwierzytelnianie żądań
> * Używanie Eksploratora danych w emulatorze
> * Eksportowanie certyfikatów SSL
> * Wywoływanie emulatora z wiersza polecenia
> * Uruchamianie emulatora w aplikacji Docker for Windows
> * Zbieranie plików śledzenia
> * Rozwiązywanie problemów

## <a name="how-the-emulator-works"></a>Jak działa emulator

Emulator usługi Azure Cosmos DB umożliwia dokładną emulację działania usługi Azure Cosmos DB. Zapewnia on taką samą funkcjonalność co usługa Azure Cosmos DB, w tym obsługę tworzenia dokumentów JSON i wykonywania zapytań względem nich, aprowizowania i skalowania kolekcji oraz wykonywania procedur składowanych i wyzwalaczy. Przy użyciu emulatora usługi Azure Cosmos DB możesz tworzyć i testować aplikacje, które następnie możesz wdrażać na platformie Azure w skali globalnej, wprowadzając tylko jedną zmianę w konfiguracji punktu końcowego połączenia dla usługi Azure Cosmos DB.

Chociaż emulator dokładnie odzwierciedla działanie usługi Azure Cosmos DB, różni się od niej pod względem wdrożenia. Na przykład emulator używa standardowych składników systemu operacyjnego — między innymi lokalnego systemu plików jako magazynu trwałego i stosu protokołu HTTPS na potrzeby łączności. Niektóre funkcje zależne od infrastruktury platformy Azure, takie jak replikacja globalna, opóźnienie odczytu/zapisu na poziomie kilku milisekund czy możliwość dostosowania poziomów spójności, nie są dostępne.

## <a name="differences-between-the-emulator-and-the-service"></a>Różnice między emulatorem a usługą
Środowisko emulowane działa na lokalnej stacji roboczej developera, dlatego między emulatorem a kontem usługi Azure Cosmos DB w chmurze występują pewne różnice w zakresie funkcjonalności:

* Eksplorator danych w emulatorze obsługuje obecnie klientów dla interfejsu API SQL oraz interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Klienci interfejsu API tabel, interfejsu API programu Graph i interfejsu API rozwiązania Cassandra nie są jeszcze obsługiwani.
* Emulator usługi Azure Cosmos DB obsługuje tylko jedno stałe konto i dobrze znany klucz główny. Emulator usługi Azure Cosmos DB nie umożliwia ponownego generowania klucza.
* Emulator usługi Azure Cosmos DB nie jest skalowalny i nie obsługuje wielu kolekcji.
* Emulator usługi Azure Cosmos DB nie umożliwia symulacji różnych [poziomów spójności usługi Azure Cosmos DB](consistency-levels.md).
* Emulator usługi Azure Cosmos DB nie umożliwia symulacji [replikacji w wielu regionach](distribute-data-globally.md).
* Emulator usługi Azure Cosmos DB nie obsługuje zastępowania limitów przydziału usługi, dostępnego w usłudze Azure Cosmos DB (na przykład limitów rozmiaru dokumentów czy zwiększania magazynu partycjonowanej kolekcji).
* Twoja kopia emulatora usługi Azure Cosmos DB może nie odzwierciedlać najnowszych zmian w tej usłudze. Z tego względu zalecamy użycie [narzędzia do planowania wydajności usługi Azure Cosmos DB](https://www.documentdb.com/capacityplanner) w celu dokładnego oszacowania wymagań aplikacji w zakresie przepływności produkcyjnej (jednostek żądań).

## <a name="system-requirements"></a>Wymagania systemowe
Emulator usługi Azure Cosmos DB ma następujące wymagania w zakresie sprzętu i oprogramowania:

* Wymagania dotyczące oprogramowania
  * Windows Server 2012 R2, Windows Server 2016 lub Windows 10
*   Minimalne wymagania sprzętowe
  * 2 GB pamięci RAM
  * 10 GB dostępnego miejsca na dysku twardym

## <a name="installation"></a>Instalacja
Można zainstalować emulator usługi Azure Cosmos DB pobrany z [Centrum pobierania Microsoft](https://aka.ms/cosmosdb-emulator) lub uruchomić go w aplikacji Docker for Windows. Aby uzyskać instrukcje dotyczące korzystania z emulatora w aplikacji Docker for Windows, zobacz sekcję [Uruchamianie na platformie Docker](#running-on-docker).

> [!NOTE]
> Do zainstalowania, skonfigurowania i uruchomienia emulatora usługi Azure Cosmos DB potrzebne są uprawnienia administratora na komputerze.

## <a name="running-on-windows"></a>Uruchamianie w systemie Windows

Aby uruchomić emulator usługi Azure Cosmos DB, wybierz przycisk Start lub naciśnij klawisz systemu Windows. Zacznij pisać **Emulator usługi Azure Cosmos DB** i wybierz emulator z listy aplikacji.

![Wybierz przycisk Start lub naciśnij klawisz systemu Windows. Zacznij pisać **Emulator usługi Azure Cosmos DB** i wybierz emulator z listy aplikacji](./media/local-emulator/database-local-emulator-start.png)

Po uruchomieniu emulatora pojawi się ikona w obszarze powiadomień paska zadań systemu Windows. ![Powiadomienie o działaniu lokalnego emulatora usługi Azure Cosmos DB, wyświetlane na pasku zadań](./media/local-emulator/database-local-emulator-taskbar.png)

Emulator usługi Azure Cosmos DB domyślnie jest uruchamiany na komputerze lokalnym („localhost”) i nasłuchuje na porcie 8081.

Emulator usługi Azure Cosmos DB domyślnie jest instalowany w katalogu `C:\Program Files\Azure Cosmos DB Emulator`. Można również uruchomić i zatrzymać emulator z poziomu wiersza polecenia. Więcej informacji zawiera [dokumentacja narzędzia wiersza polecenia](#command-line).

## <a name="start-data-explorer"></a>Uruchamianie Eksploratora danych

Uruchomienie emulatora usługi Azure Cosmos DB powoduje automatyczne otwarcie Eksploratora danych usługi Azure Cosmos DB w przeglądarce. Adres jest wyświetlany jako [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Możesz zamknąć eksploratora. Jeśli zechcesz otworzyć go ponownie później, możesz otworzyć ten adres URL w przeglądarce lub uruchomić eksploratora za pomocą ikony emulatora usługi Azure Cosmos DB wyświetlanej w obszarze powiadomień systemu Windows.

![Program uruchamiający Eksploratora danych lokalnego emulatora usługi Azure Cosmos DB](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Sprawdzanie dostępności aktualizacji
Eksplorator danych informuje o możliwości pobrania nowych aktualizacji.

> [!NOTE]
> Nie ma gwarancji, że dane utworzone w jednej wersji emulatora usługi Azure Cosmos DB będą dostępne w innej wersji. Jeśli musisz przechowywać dane przez dłuższy czas, zaleca się ich zapisanie na koncie usługi Azure Cosmos DB, a nie w emulatorze usługi Azure Cosmos DB.

## <a name="authenticating-requests"></a>Uwierzytelnianie żądań
Podobnie jak w usłudze Azure Cosmos DB w chmurze, każde żądanie wykonywane względem emulatora usługi Azure Cosmos DB musi zostać uwierzytelnione. Emulator usługi Azure Cosmos DB obsługuje jedno stałe konto i dobrze znany klucz uwierzytelniania jako klucz główny. To konto i klucz są jedynymi poświadczeniami, których można używać z emulatorem usługi Azure Cosmos DB. Oto one:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Klucz główny obsługiwany przez emulatora usługi Azure Cosmos DB jest przeznaczony do użytku tylko w tym emulatorze. W emulatorze usługi Azure Cosmos DB nie można korzystać z klucza ani konta usługi Azure Cosmos DB używanych w środowisku produkcyjnym.

> [!NOTE]
> Jeśli emulator został uruchomiony z opcją /Key, użyj wygenerowanego klucza zamiast wartości „C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==”

Tak samo jak usługa Azure Cosmos DB emulator usługi Azure Cosmos DB obsługuje bezpieczną komunikację tylko za pośrednictwem protokołu SSL.

## <a name="running-on-a-local-network"></a>Uruchamianie w sieci lokalnej

Emulator możesz uruchomić w sieci lokalnej. Aby włączyć dostęp w sieci, podaj opcję /AllowNetworkAccess w [wierszu polecenia](#command-line-syntax), która dodatkowo wymaga określenia opcji /Key=ciąg_klucza lub /KeyFile=nazwa_pliku. Opcja /GenKeyFile=nazwa_pliku pozwala z wyprzedzeniem wygenerować plik z losowym kluczem. Można następnie użyć tego pliku w opcji /KeyFile=nazwa_pliku lub /Key=zawartość_pliku.

Aby po raz pierwszy włączyć dostęp w sieci, użytkownik powinien zamknąć emulator i usunąć jego katalog danych (C:\Users\nazwa_użytkownika\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Programowanie za pomocą emulatora
Po uruchomieniu emulatora usługi Azure Cosmos DB na komputerze możesz z niego korzystać przy użyciu dowolnego obsługiwanego [zestawu Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) lub [interfejsu API REST usługi Azure Cosmos DB](/rest/api/cosmos-db/). Emulator usługi Azure Cosmos DB ma również wbudowanego Eksploratora danych, który umożliwia tworzenie kolekcji dla interfejsu API SQL lub interfejsu API usługi Azure Cosmos DB dla bazy danych Mongo DB, a także wyświetlanie i edytowanie dokumentów bez pisania kodu.

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"),
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Jeśli korzystasz z [obsługi protokołów przewodowych usługi Azure Cosmos DB dla usługi MongoDB](mongodb-introduction.md), użyj następujących parametrów połączenia:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Aby nawiązać połączenie z emulatorem usługi Azure Cosmos DB, możesz użyć istniejących narzędzi, takich jak program [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio). Możesz również migrować dane między emulatorem usługi Azure Cosmos DB a usługą Azure Cosmos DB za pomocą [narzędzia do migracji danych usługi Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE]
> Jeśli emulator został uruchomiony z opcją /Key, użyj wygenerowanego klucza zamiast wartości „C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==”

Przy użyciu emulatora usługi Azure Cosmos DB możesz domyślnie utworzyć maksymalnie 25 kolekcji z jedną partycją lub jedną kolekcję partycjonowaną. Aby uzyskać więcej informacji na temat zmiany tej wartości, zobacz [Ustawianie wartości PartitionCount](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Eksportowanie certyfikatu SSL

Do nawiązywania bezpiecznego połączenia z lokalnym emulatorem usługi Azure Cosmos DB języki i środowisko uruchomieniowe .NET używają magazynu certyfikatów systemu Windows. Inne języki korzystają z własnych metod używania certyfikatów i zarządzania nimi. Język Java używa własnego [magazynu certyfikatów](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), a język Python — [otok gniazd](https://docs.python.org/2/library/ssl.html).

Aby uzyskać certyfikat do użycia z językami i środowiskami uruchomieniowymi, które nie zapewniają integracji z magazynem certyfikatów systemu Windows, trzeba wyeksportować ten certyfikat przy użyciu menedżera certyfikatów systemu Windows. Aby go otworzyć, możesz uruchomić plik certlm.msc lub wykonać instrukcje zawarte w temacie [Eksportowanie certyfikatów emulatora usługi Azure Cosmos DB](./local-emulator-export-ssl-certificates.md). Po uruchomieniu menedżera certyfikatów otwórz sekcję certyfikatów osobistych, jak pokazano poniżej, i wyeksportuj certyfikat o przyjaznej nazwie „DocumentDBEmulatorCertificate” jako plik Certyfikat X.509 szyfrowany algorytmem Base-64 (CER).

![Certyfikat SSL lokalnego emulatora usługi Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Aby zaimportować certyfikat X.509 do magazynu certyfikatów Java, postępuj zgodnie z instrukcjami zawartymi w artykule [Adding a Certificate to the Java CA Certificates Store (Dodawanie certyfikatu do magazynu certyfikatów Java CA)](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Po zaimportowaniu certyfikatu do magazynu certyfikatów klienci interfejsu API SQL i interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB będą mogli łączyć się z emulatorem usługi Azure Cosmos DB.

W przypadku łączenia się z emulatorem za pomocą zestawów SDK środowisk Python i Node.js weryfikacja SSL jest wyłączona.

## <a id="command-line"></a>Dokumentacja narzędzia wiersza polecenia
Wiersz polecenia umożliwia uruchamianie i zatrzymywanie emulatora, konfigurowanie opcji i wykonywanie innych operacji z poziomu lokalizacji instalacji.

### <a name="command-line-syntax"></a>Składnia wiersza polecenia

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Aby wyświetlić listę opcji, wpisz ciąg `CosmosDB.Emulator.exe /?` w wierszu polecenia.

|**Opcja** | **Opis** | **Polecenie**| **Argumenty**|
|---|---|---|---|
|[Bez argumentów] | Uruchamia emulator usługi Azure Cosmos DB z ustawieniami domyślnymi. |CosmosDB.Emulator.exe| |
|[Help] |Wyświetla listę obsługiwanych argumentów wiersza polecenia.|CosmosDB.Emulator.exe /? | |
| GetStatus |Pobiera stan emulatora usługi Azure Cosmos DB. Stan jest wskazywany przez kod zakończenia: 1 = uruchamiany, 2 = uruchomiony, 3 = zatrzymany. Kod zakończenia o wartości ujemnej informuje o wystąpieniu błędu. Inne dane wyjściowe nie są generowane. | CosmosDB.Emulator.exe /GetStatus| |
| Shutdown| Zamyka emulatora usługi Azure Cosmos DB.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Określa ścieżkę przechowywania plików danych. Ścieżka domyślna to %LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<ścieżka_do_danych\> | \<ścieżka_do_danych\>: dostępna ścieżka |
|Port | Określa numer portu używanego przez emulatora. Wartość domyślna to 8081. |CosmosDB.Emulator.exe /Port=\<port\> | \<port\>: numer pojedynczego portu |
| MongoPort | Określa numer portu używanego w celu zapewnienia zgodności z interfejsem API usługi MongoDB. Wartość domyślna to 10255. |CosmosDB.Emulator.exe /MongoPort= \<mongoport\>|\<port_mongo\>: numer pojedynczego portu|
| DirectPorts |Określa porty używane przez połączenia bezpośrednie. Wartość domyślna to 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<porty_bezpośrednie\> | \<porty_bezpośrednie\>: lista rozdzielana przecinkami zawierająca cztery porty |
| Klucz |Klucz autoryzacji dla emulatora. Klucz musi być 64-bajtowym wektorem szyfrowanym algorytmem Base-64. | CosmosDB.Emulator.exe /Key:\<klucz\> | \<klucz\>: klucz musi być 64-bajtowym wektorem szyfrowanym algorytmem Base-64|
| EnableRateLimiting | Wskazuje, że ograniczanie szybkości żądania jest włączone. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Wskazuje, że ograniczanie szybkości żądania jest wyłączone. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Nie wyświetla interfejsu użytkownika emulatora. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Nie wyświetla Eksploratora danych podczas uruchamiania. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | Określa maksymalną liczbę kolekcji partycjonowanych. Aby uzyskać więcej informacji, zobacz sekcję [Zmienianie liczby kolekcji](#set-partitioncount). | CosmosDB.Emulator.exe /PartitionCount=\<liczba_partycji\> | \<liczba_partycji\>: maksymalna liczba dozwolonych kolekcji z jedną partycją. Wartość domyślna to 25. Maksymalna dozwolona wartość to 250.|
| DefaultPartitionCount| Określa domyślną liczbę partycji w kolekcji partycjonowanej. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<domyślna_liczba_partycji\> | \<domyślna_liczba_partycji\>: wartość domyślna to 25.|
| AllowNetworkAccess | Włącza dostęp do emulatora za pośrednictwem sieci. Aby umożliwić dostęp w sieci, musisz także podać opcję /Key=\<ciąg_klucza\> lub /KeyFile=\<nazwa_pliku\>. | CosmosDB.Emulator.exe /AllowNetworkAccess /Key=\<ciąg_klucza\> lub CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=\<nazwa_pliku\>| |
| NoFirewall | Nie dostosowuje reguł zapory w przypadku użycia opcji /AllowNetworkAccess. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Generuje nowy klucz autoryzacji i zapisuje go w określonym pliku. Wygenerowanego klucza można używać z opcją /Key lub /KeyFile. | CosmosDB.Emulator.exe /GenKeyFile=\<ścieżka_do_pliku_klucza\> | |
| Spójność | Ustawia domyślny poziom spójności dla konta. | CosmosDB.Emulator.exe /Consistency=\<spójność\> | \<spójność\>: wartość musi być jednym z następujących [poziomów spójności](consistency-levels.md): Sesja, Silna, Ostateczna lub Powiązana nieaktualność. Wartość domyślna to Sesja. |
| ? | Wyświetla komunikat pomocy.| | |

## <a id="set-partitioncount"></a>Zmienianie liczby kolekcji

Przy użyciu emulatora usługi Azure Cosmos DB możesz domyślnie utworzyć maksymalnie 25 kolekcji z jedną partycją lub jedną kolekcję partycjonowaną. Zmieniając wartość **PartitionCount**, możesz utworzyć maksymalnie 250 kolekcji z jedną partycją lub 10 kolekcji partycjonowanych albo dowolną kombinację tych konfiguracji, która nie zawiera więcej niż 250 pojedynczych partycji (jedna kolekcja partycjonowana = 25 kolekcji z jedną partycją).

Jeśli spróbujesz utworzyć kolekcję po przekroczeniu bieżącej liczby partycji, emulator zgłosi wyjątek ServiceUnavailable z następującym komunikatem.

    Sorry, we are currently experiencing high demand in this region,
    and cannot fulfill your request at this time. We work continuously
    to bring more and more capacity online, and encourage you to try again.
    Please do not hesitate to email askcosmosdb@microsoft.com at any time or
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Aby zmienić liczbę kolekcji dostępnych dla emulatora usługi Azure Cosmos DB, wykonaj następujące czynności:

1. Usuń wszystkie lokalne dane emulatora usługi Azure Cosmos DB, klikając prawym przyciskiem myszy ikonę **emulatora usługi Azure Cosmos DB** w obszarze powiadomień, a następnie klikając polecenie **Reset Data…** (Resetuj dane).
2. Usuń wszystkie dane emulatora w folderze C:\Users\nazwa_użytkownika\AppData\Local\CosmosDBEmulator.
3. Zamknij wszystkie otwarte wystąpienia, klikając prawym przyciskiem myszy ikonę **emulatora usługi Azure Cosmos DB** w obszarze powiadomień, a następnie klikając polecenie **Exit** (Zakończ). Zamykanie wszystkich wystąpień może potrwać około minuty.
4. Zainstaluj najnowszą wersję [emulatora usługi Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).
5. Uruchom emulator z flagą PartitionCount, ustawiając wartość <= 250. Na przykład: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Sterowanie emulatorem

Emulator jest dostarczany z modułem programu PowerShell, który umożliwia uruchamianie, zatrzymywanie, odinstalowywanie i pobierania stanu usługi. Aby go użyć:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

lub umieść katalog `PSModules` na ścieżce `PSModulesPath` i zaimportuj go w następujący sposób:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Poniżej przedstawiono podsumowanie poleceń umożliwiających sterowanie emulatorem za pomocą programu PowerShell:

### `Get-CosmosDbEmulatorStatus`

#### <a name="syntax"></a>Składnia

`Get-CosmosDbEmulatorStatus`

#### <a name="remarks"></a>Uwagi

Zwraca jedną z następujących wartości ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running lub ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

#### <a name="syntax"></a>Składnia

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

#### <a name="remarks"></a>Uwagi

Uruchamia emulator. Domyślnie to polecenie czeka, aż emulator zgłosi gotowość do odbierania żądań. Jeśli chcesz, aby polecenie cmdlet zakończyło działanie od razu po uruchomieniu emulatora, użyj opcji -NoWait.

### `Stop-CosmosDbEmulator`

#### <a name="syntax"></a>Składnia

 `Stop-CosmosDbEmulator [-NoWait]`

#### <a name="remarks"></a>Uwagi

Zatrzymuje emulator. Domyślnie to polecenie czeka, aż emulator zostanie w pełni zamknięty. Jeśli chcesz, aby polecenie cmdlet zakończyło działanie od razu po rozpoczęciu zamykania emulatora, użyj opcji -NoWait.

### `Uninstall-CosmosDbEmulator`

#### <a name="syntax"></a>Składnia

`Uninstall-CosmosDbEmulator [-RemoveData]`

#### <a name="remarks"></a>Uwagi

Odinstalowuje emulator i opcjonalnie usuwa całą zawartość katalogu $env:LOCALAPPDATA\CosmosDbEmulator.
To polecenie cmdlet gwarantuje zatrzymanie emulatora przed jego odinstalowaniem.

## <a name="running-on-docker"></a>Uruchamianie na platformie Docker

Emulator usługi Azure Cosmos DB można uruchomić w aplikacji Docker for Windows. Emulator nie współdziała z aplikacją Docker for Oracle Linux.

Po zainstalowaniu aplikacji [Docker for Windows](https://www.docker.com/docker-windows) przełącz się na kontenery systemu Windows, klikając prawym przyciskiem myszy ikonę programu Docker na pasku narzędzi i wybierając polecenie **Switch to Windows containers** (Przełącz na kontenery systemu Windows).

Następnie ściągnij obraz emulatora z usługi Docker Hub, uruchamiając następujące polecenie w wybranej powłoce.

```
docker pull microsoft/azure-cosmosdb-emulator
```
Aby uruchomić obraz, uruchom następujące polecenia.

W wierszu polecenia:
```cmd
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:C:\CosmosDB.Emulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator
```

W programie PowerShell:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:C:\CosmosDB.Emulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator
```

Odpowiedź wygląda mniej więcej tak:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

W kliencie użyj punktu końcowego i klucza głównego z odpowiedzi i zaimportuj certyfikat SSL do hosta. Aby zaimportować certyfikat SSL, uruchom następujące polecenie w wierszu polecenia z uprawnieniami administratora:

W wierszu polecenia:
```cmd
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

W programie PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

Zamknięcie interakcyjnej powłoki po uruchomieniu emulatora spowoduje zamknięcie kontenera emulatora.

Aby otworzyć Eksploratora danych, otwórz następujący adres URL w przeglądarce. Punkt końcowy emulatora znajduje się w powyższym komunikacie odpowiedzi.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Następujące wskazówki mogą ułatwić rozwiązywanie problemów związanych z emulatorem usługi Azure Cosmos DB:

- Jeśli po zainstalowaniu nowej wersji emulatora występują błędy, upewnij się, że dane zostały zresetowane. Możesz to zrobić, klikając prawym przyciskiem myszy ikonę emulatora usługi Azure Cosmos DB w obszarze powiadomień, a następnie klikając polecenie Reset Data… (Resetuj dane). Jeśli to nie rozwiąże problemów, możesz odinstalować i ponownie zainstalować aplikację. Odpowiednie instrukcje znajdziesz w sekcji [Odinstalowywanie lokalnego emulatora](#uninstall).

- Jeśli emulator usługi Azure Cosmos DB ulegnie awarii, zbierz pliki zrzutu z folderu c:\Users\nazwa_użytkownika\AppData\Local\CrashDumps, skompresuj je i wyślij w wiadomości e-mail na adres [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Jeśli awarie występują po uruchomieniu pliku CosmosDB.StartupEntryPoint.exe, uruchom następujące polecenie w wierszu polecenia z uprawnieniami administratora: `lodctr /R`

- Jeśli wystąpi problem z łącznością, [zbierz pliki śledzenia](#trace-files), skompresuj je i wyślij w wiadomości e-mail na adres [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Jeśli zostanie wyświetlony komunikat **Service Unavailable** (Usługa niedostępna), może to wskazywać na błąd inicjacji stosu sieciowego. Sprawdź, czy masz zainstalowanego klienta Pulse Secure lub Juniper Networks — ich sterowniki filtrów sieci mogą powodować problemy. Odinstalowanie sterowników filtrów sieci innych firm zwykle pozwala rozwiązać problem.

- Kiedy emulator jest uruchomiony, jeśli komputer przejdzie w tryb uśpienia lub uruchomi dowolną aktualizację systemu operacyjnego, możesz zobaczyć komunikat **Service is currently unavailable** (Usługa jest obecnie niedostępna). Zresetuj emulator, klikając prawym przyciskiem myszy ikonę wyświetloną na pasku powiadomień systemu Windows i wybierając opcję **Reset Data** (Resetuj dane).

### <a id="trace-files"></a>Zbieranie plików śledzenia

Aby zebrać dane śledzenia debugowania, uruchom następujące polecenia w wierszu polecenia z uprawnieniami administratora:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. W obszarze powiadomień sprawdź, czy program został zamknięty — może to potrwać około minuty. Możesz także po prostu kliknąć polecenie **Exit** (Zakończ) w interfejsie użytkownika emulatora usługi Azure Cosmos DB.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Odtwórz problem. Jeśli Eksplorator danych nie działa, po prostu poczekaj na otwarcie przeglądarki, która po kilku sekundach przechwyci błąd.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Znajdź plik docdbemulator_000001.etl w folderze `%ProgramFiles%\Azure Cosmos DB Emulator`.
7. Wyślij plik etl razem z krokami umożliwiającymi odtworzenie problemu na adres [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) w celu debugowania.

### <a id="uninstall"></a>Odinstalowywanie lokalnego emulatora

1. Zamknij wszystkie otwarte wystąpienia lokalnego emulatora, klikając prawym przyciskiem myszy ikonę emulatora usługi Azure Cosmos DB w obszarze powiadomień, a następnie klikając polecenie Exit (Zakończ). Zamykanie wszystkich wystąpień może potrwać około minuty.
2. W polu wyszukiwania systemu Windows wpisz ciąg **Aplikacje i funkcje** i kliknij wynik **Aplikacje i funkcje (Ustawienia systemowe)**.
3. Przewiń listę aplikacji, wybierz pozycję **Emulator usługi Azure Cosmos DB**, kliknij przycisk **Odinstaluj**, potwierdź i ponownie kliknij przycisk **Odinstaluj**.
4. Gdy aplikacja zostanie odinstalowana, przejdź do katalogu `C:\Users\<user>\AppData\Local\CosmosDBEmulator` i usuń folder.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Instalowanie lokalnego emulatora
> * Uruchamianie emulatora w aplikacji Docker for Windows
> * Uwierzytelnianie żądań
> * Korzystanie z Eksploratora danych w emulatorze
> * Eksportowanie certyfikatów SSL
> * Wywoływanie emulatora z wiersza polecenia
> * Zbieranie plików śledzenia

W tym samouczku opisaliśmy sposób użycia lokalnego emulatora do bezpłatnego programowania w środowisku lokalnym. Teraz możesz przejść do następnego samouczka, aby dowiedzieć się, jak wyeksportować certyfikaty SSL emulatora.

> [!div class="nextstepaction"]
> [Eksportowanie certyfikatów emulatora usługi Azure Cosmos DB](local-emulator-export-ssl-certificates.md)
