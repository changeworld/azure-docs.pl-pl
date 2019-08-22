---
title: Korzystanie z emulatora usługi Azure Storage do programowania i testowania | Microsoft Docs
description: Emulator usługi Azure Storage udostępnia bezpłatne lokalne środowisko programistyczne na potrzeby tworzenia i testowania aplikacji usługi Azure Storage. Dowiedz się, jak są autoryzowane żądania, jak nawiązać połączenie z emulatorem z aplikacji oraz jak używać narzędzia wiersza polecenia.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/10/2018
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: ee8dd9da186013dc168e20e6b8b842cca85de172
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877623"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Korzystanie z emulatora usługi Azure Storage na potrzeby tworzenia i testowania

Emulator magazynu Microsoft Azure udostępnia środowisko lokalne, które emuluje usługi obiektów blob, kolejek i tabel platformy Azure do celów deweloperskich. Za pomocą emulatora magazynu można testować swoją aplikację lokalnie, bez tworzenia subskrypcji platformy Azure ani ponoszenia kosztów. Gdy aplikacja działa w emulatorze, możesz przełączyć się do korzystania z konta usługi Azure Storage w chmurze.

## <a name="get-the-storage-emulator"></a>Pobierz emulator magazynu

Emulator magazynu jest dostępny w ramach [zestawu SDK Microsoft Azure](https://azure.microsoft.com/downloads/). Emulator magazynu można również zainstalować przy użyciu [autonomicznego Instalatora](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (Pobieranie bezpośrednie). Aby zainstalować emulator magazynu, musisz mieć uprawnienia administracyjne na komputerze.

Emulator magazynu jest obecnie uruchamiany tylko w systemie Windows. Dla tych rozważających emulator magazynu dla systemu Linux jedną z opcji jest obsługiwana przez społeczność, czyli emulator magazynu [azurite](https://github.com/azure/azurite)typu open source.

> [!NOTE]
> W przypadku korzystania z innej wersji nie ma gwarancji, że dane utworzone w jednej wersji emulatora magazynu są dostępne. Jeśli potrzebujesz utrzymywać dane przez długi czas, zalecamy przechowywanie tych danych na koncie usługi Azure Storage, a nie w emulatorze magazynu.
> 
> Emulator magazynu zależy od określonych wersji bibliotek OData. Zastępowanie bibliotek DLL OData używanych przez emulator magazynu z innymi wersjami nie jest obsługiwane i może spowodować nieoczekiwane zachowanie. Jednak w celu wysyłania żądań do emulatora może być używana dowolna wersja protokołu OData obsługiwana przez usługę magazynu.

## <a name="how-the-storage-emulator-works"></a>Jak działa emulator magazynu

Emulator magazynu używa lokalnego wystąpienia Microsoft SQL Server i lokalnego systemu plików do emulowania usług Azure Storage. Domyślnie emulator magazynu używa bazy danych w Microsoft SQL Server 2012 Express LocalDB. Można skonfigurować emulator magazynu, aby mógł uzyskać dostęp do lokalnego wystąpienia SQL Server, a nie wystąpienia LocalDB. Aby uzyskać więcej informacji, zobacz sekcję [Uruchamianie i Inicjowanie emulatora magazynu](#start-and-initialize-the-storage-emulator) w dalszej części tego artykułu.

Emulator magazynu łączy się z SQL Server lub LocalDB przy użyciu uwierzytelniania systemu Windows.

Istnieją pewne różnice w działaniu między emulatorem magazynu a usługami Azure Storage. Więcej informacji na temat tych różnic znajduje się w sekcji [różnice między emulatorem magazynu i usługą Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) w dalszej części tego artykułu.

## <a name="start-and-initialize-the-storage-emulator"></a>Uruchamianie i Inicjowanie emulatora magazynu

Aby uruchomić emulator usługi Azure Storage:

1. Wybierz przycisk **Start** lub naciśnij klawisz **systemu Windows** .
2. Zacznij pisać `Azure Storage Emulator`.
3. Wybierz emulator z listy wyświetlanych aplikacji.

Po uruchomieniu emulatora magazynu zostanie wyświetlone okno wiersza polecenia. Za pomocą tego okna konsoli można uruchomić i zatrzymać emulator magazynu, wyczyścić dane, pobrać stan i zainicjować emulator. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [narzędzia wiersza polecenia emulatora magazynu](#storage-emulator-command-line-tool-reference) w dalszej części tego artykułu.

Po uruchomieniu emulatora pojawi się ikona w obszarze powiadomień paska zadań systemu Windows.

Po zamknięciu okna wiersza polecenia emulatora magazynu będzie nadal można uruchamiać emulator magazynu. Aby ponownie wyświetlić okno konsoli emulatora magazynu, wykonaj powyższe kroki, tak jak w przypadku uruchamiania emulatora magazynu.

Przy pierwszym uruchomieniu emulatora magazynu jest inicjowane lokalne środowisko magazynu. Proces inicjalizacji tworzy bazę danych w LocalDB i rezerwuje porty HTTP dla każdej lokalnej usługi magazynu.

Emulator magazynu jest instalowany domyślnie w `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`programie.

> [!TIP]
> [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com) można użyć do pracy z zasobami emulatora magazynu lokalnego. Poszukaj pozycji "(Programowanie)" w obszarze "konta magazynu" w drzewie zasobów Eksplorator usługi Storage po zainstalowaniu i uruchomieniu emulatora magazynu.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicjowanie emulatora magazynu w celu korzystania z innej bazy danych SQL

Za pomocą narzędzia wiersza polecenia emulatora magazynu można zainicjować emulator magazynu, aby wskazywał wystąpienie bazy danych SQL inne niż domyślne wystąpienie LocalDB:

1. Otwórz okno konsoli emulatora magazynu, zgodnie z opisem w sekcji [Uruchamianie i Inicjowanie emulatora magazynu](#start-and-initialize-the-storage-emulator) .
1. W oknie konsoli wpisz następujące polecenie, gdzie `<SQLServerInstance>` jest nazwą wystąpienia SQL Server. Aby użyć LocalDB, określ `(localdb)\MSSQLLocalDb` jako wystąpienie SQL Server.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Można również użyć następującego polecenia, które kieruje emulator do użycia domyślnego wystąpienia SQL Server:

   `AzureStorageEmulator.exe init /server .`

   Można też użyć poniższego polecenia, które ponownie inicjuje bazę danych do domyślnego wystąpienia LocalDB:

   `AzureStorageEmulator.exe init /forceCreate`

Aby uzyskać więcej informacji na temat tych poleceń, zobacz temat [Narzędzie wiersza polecenia emulatora magazynu](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Możesz użyć [Management Studio Microsoft SQL Server](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS), aby zarządzać wystąpieniami SQL Server, w tym instalacją LocalDB. W oknie dialogowym smss **łączenia z serwerem** Podaj `(localdb)\MSSQLLocalDb` wartość w polu **Nazwa serwera:** , aby połączyć się z wystąpieniem LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Uwierzytelnianie żądań względem emulatora magazynu

Po zainstalowaniu i uruchomieniu emulatora magazynu można przetestować swój kod. Podobnie jak w przypadku usługi Azure Storage w chmurze, każde żądanie wprowadzane względem emulatora magazynu musi być autoryzowane, chyba że jest to żądanie anonimowe. Żądania można autoryzować do emulatora magazynu przy użyciu uwierzytelniania klucza współużytkowanego lub z sygnaturą dostępu współdzielonego (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autoryzuj przy użyciu poświadczeń klucza współużytkowanego

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Konfigurowanie parametrów połączenia usługi Azure Storage](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autoryzuj przy użyciu sygnatury dostępu współdzielonego

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Niektóre biblioteki klienta usługi Azure Storage, takie jak biblioteka Xamarin, obsługują tylko uwierzytelnianie z tokenem sygnatury dostępu współdzielonego (SAS). Token SYGNATURy dostępu współdzielonego można utworzyć za pomocą narzędzia takiego jak [Eksplorator usługi Storage](https://storageexplorer.com/) lub innej aplikacji obsługującej uwierzytelnianie klucza współużytkowanego.

Token sygnatury dostępu współdzielonego można również wygenerować przy użyciu Azure PowerShell. Poniższy przykład generuje token SAS z pełnymi uprawnieniami do kontenera obiektów blob:

1. Zainstaluj Azure PowerShell, jeśli nie zostało to jeszcze zrobione (zaleca się użycie najnowszej wersji Azure PowerShell poleceń cmdlet). Instrukcje instalacji znajdują się w temacie [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps).
2. Otwórz Azure PowerShell i uruchom następujące polecenia, zastępując `CONTAINER_NAME` je wybraną nazwą:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Otrzymany identyfikator URI sygnatury dostępu współdzielonego dla nowego kontenera powinien wyglądać podobnie do:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Sygnatura dostępu współdzielonego utworzona przy użyciu tego przykładu jest ważna przez jeden dzień. Podpis przyznaje pełen dostęp (odczyt, zapis, usuwanie, lista) do obiektów BLOB w kontenerze.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Adresowanie zasobów w emulatorze magazynu

Punkty końcowe usługi dla emulatora magazynu są inne niż te w ramach konta usługi Azure Storage. Różnica polega na tym, że komputer lokalny nie wykonuje rozpoznawania nazw domen, co wymaga, aby punkty końcowe emulatora magazynu były adresami lokalnymi.

Gdy adresowanie zasobu na koncie usługi Azure Storage jest możliwe, należy użyć poniższego schematu. Nazwa konta jest częścią nazwy hosta identyfikatora URI, a zasób jest częścią ścieżki URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Na przykład następujący identyfikator URI jest prawidłowym adresem dla obiektu BLOB na koncie usługi Azure Storage:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Jednak emulator magazynu, ponieważ komputer lokalny nie wykonuje rozpoznawania nazw domen, nazwa konta jest częścią ścieżki URI zamiast nazwy hosta. Użyj następującego formatu identyfikatora URI dla zasobu w emulatorze magazynu:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Na przykład następujący adres może służyć do uzyskiwania dostępu do obiektu BLOB w emulatorze magazynu:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Punkty końcowe usługi dla emulatora magazynu są następujące:

* Blob service:`http://127.0.0.1:10000/<account-name>/<resource-path>`
* Usługa kolejki:`http://127.0.0.1:10001/<account-name>/<resource-path>`
* Table service:`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adresowanie pomocniczej konta za pomocą RA-GRS

Począwszy od wersji 3,1 emulator magazynu obsługuje replikację Geograficznie nadmiarowy do odczytu (RA-GRS). W przypadku zasobów magazynu zarówno w chmurze, jak i w emulatorze lokalnym, można uzyskać dostęp do lokalizacji dodatkowej przez dołączenie-pomocnicze do nazwy konta. Na przykład następujący adres może służyć do uzyskiwania dostępu do obiektu BLOB przy użyciu pomocniczego elementu tylko do odczytu w emulatorze magazynu:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Aby uzyskać programistyczny dostęp do pomocniczego elementu z emulatorem magazynu, użyj biblioteki klienta usługi Storage dla programu .NET w wersji 3,2 lub nowszej. Aby uzyskać szczegółowe informacje, zobacz [Biblioteka klienta Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) .
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Dokumentacja narzędzia wiersza polecenia emulatora magazynu

Począwszy od wersji 3,0, okno konsoli jest wyświetlane po uruchomieniu emulatora magazynu. Użyj wiersza polecenia w oknie konsoli, aby uruchomić i zatrzymać emulator, a także zbadać stan i wykonać inne operacje.

> [!NOTE]
> Jeśli zainstalowano emulator obliczeń Microsoft Azure, podczas uruchamiania emulatora magazynu zostanie wyświetlona ikona zasobnika systemowego. Kliknij prawym przyciskiem myszy ikonę, aby wyświetlić menu, które zapewnia graficzny sposób uruchamiania i zatrzymywania emulatora magazynu.
>
>

### <a name="command-line-syntax"></a>Składnia wiersza polecenia

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opcje

Aby wyświetlić listę opcji, wpisz ciąg `/help` w wierszu polecenia.

| Opcja | Opis | Polecenie | Argumenty |
| --- | --- | --- | --- |
| **Start** |Uruchamia emulator magazynu. |`AzureStorageEmulator.exe start [-inprocess]` |*-unprocess*: Uruchom emulator w bieżącym procesie zamiast tworzenia nowego procesu. |
| **Stop** |Powoduje zatrzymanie emulatora magazynu. |`AzureStorageEmulator.exe stop` | |
| **Stan** |Drukuje stan emulatora magazynu. |`AzureStorageEmulator.exe status` | |
| **Wyczyść** |Czyści dane we wszystkich usługach określonych w wierszu polecenia. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*obiekt BLOB*: Czyści dane obiektów BLOB. <br/>*Kolejka*: Czyści dane kolejki. <br/>*tabela*: Czyści dane tabeli. <br/>*wszystkie*: Czyści wszystkie dane we wszystkich usługach. |
| **Init** |Wykonuje jednorazowe inicjowanie w celu skonfigurowania emulatora. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-serwer serverName\instanceName*: Określa serwer hostujący wystąpienie bazy danych SQL. <br/>*-SQLInstance InstanceName*: Określa nazwę wystąpienia SQL, które ma być używane w domyślnym wystąpieniu serwera. <br/>*-forceCreate*: Wymusza utworzenie bazy danych SQL, nawet jeśli już istnieje. <br/>*-skipcreate*: Pomija Tworzenie bazy danych SQL. Ma to pierwszeństwo przed forceCreate.<br/>*-reserveports*: Podejmuje próbę zarezerwowania portów HTTP skojarzonych z usługami.<br/>*-unreserveports*: Próbuje usunąć rezerwacje dla portów HTTP skojarzonych z usługami. Ma to pierwszeństwo przed reserveports.<br/>*-unprocess*: Wykonuje inicjalizację w bieżącym procesie zamiast duplikowania nowego procesu. W przypadku zmiany rezerwacji portów bieżący proces musi zostać uruchomiony z podwyższonym poziomem uprawnień. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Różnice między emulatorem magazynu a usługą Azure Storage

Ponieważ emulator magazynu jest emulowane środowisko działające w lokalnym wystąpieniu programu SQL Server, istnieją różnice w działaniu między emulatorem a kontem usługi Azure Storage w chmurze:

* Emulator magazynu obsługuje tylko pojedyncze stałe konto i dobrze znane klucze uwierzytelniania.
* Emulator magazynu nie jest skalowalną usługą magazynu i nie obsługuje dużej liczby równoczesnych klientów.
* Zgodnie z opisem w temacie Addressing Resources [w emulatorze magazynu](#addressing-resources-in-the-storage-emulator)zasoby są rozwiązywane inaczej w emulatorze magazynu, a konto usługi Azure Storage. Różnica polega na tym, że rozpoznawanie nazw domen jest dostępne w chmurze, ale nie na komputerze lokalnym.
* Począwszy od wersji 3,1, konto emulatora magazynu obsługuje replikację Geograficznie nadmiarowy do odczytu (RA-GRS). W emulatorze wszystkie konta mają włączoną funkcję RA-GRS i nigdy nie ma zwłoki między repliką podstawową i pomocniczą. Statystyki usługi Get BLOB Service, get Queue Service i Get Table Service są obsługiwane na serwerze pomocniczym i zawsze zwracają wartość `LastSyncTime` elementu Response jako bieżący czas zależny od podstawowej bazy danych SQL.
* Punkty końcowe usługi plików i protokołu SMB nie są obecnie obsługiwane w emulatorze magazynu.
* Jeśli używasz wersji usług magazynu, która nie jest jeszcze obsługiwana przez emulator, Emulator magazynu zwróci błąd VersionNotSupportedByEmulator (kod stanu HTTP 400-złe żądanie).

### <a name="differences-for-blob-storage"></a>Różnice dotyczące usługi BLOB Storage

Poniższe różnice dotyczą magazynu obiektów BLOB w emulatorze:

* Emulator magazynu obsługuje tylko rozmiary obiektów BLOB do 2 GB.
* Maksymalna długość nazwy obiektu BLOB w emulatorze magazynu to 256 znaków, podczas gdy maksymalna długość nazwy obiektu BLOB w usłudze Azure Storage to 1024 znaków.
* Kopiowanie przyrostowe umożliwia skopiowanie migawek z nadpisanych obiektów blob, które zwracają błąd w usłudze.
* Pobieranie zakresów stron różnica nie działa między migawkami skopiowanymi przy użyciu obiektu BLOB kopiowania przyrostowego.
* Operacja Put obiektu BLOB może powieść się z obiektem BLOB, który istnieje w emulatorze magazynu z aktywną dzierżawą, nawet jeśli nie został określony identyfikator dzierżawy w żądaniu.
* Operacje dołączania obiektów BLOB nie są obsługiwane przez emulator. Próba wykonania operacji na dołączeniu obiektu BLOB zwraca błąd FeatureNotSupportedByEmulator (kod stanu HTTP 400-złe żądanie).

### <a name="differences-for-table-storage"></a>Różnice dotyczące usługi Table Storage

Poniższe różnice dotyczą magazynu tabel w emulatorze:

* Właściwości Date w Table service w emulatorze magazynu obsługują tylko zakres obsługiwany przez SQL Server 2005 (muszą one być późniejsze od 1 stycznia 1753). Wszystkie daty przed 1 stycznia 1753 są zmieniane na tę wartość. Precyzja dat jest ograniczona do dokładności SQL Server 2005, co oznacza, że daty są precyzyjne do 1/300th sekundy.
* Emulator magazynu obsługuje wartości właściwości klucza partycji i klucza wiersza mniejsze niż 512 bajtów. Ponadto łączny rozmiar nazwy konta, nazwy tabeli i nazwy właściwości klucza nie może przekroczyć 900 bajtów.
* Łączny rozmiar wiersza w tabeli w emulatorze magazynu jest ograniczony do mniej niż 1 MB.
* W `Edm.Guid` emulatorze magazynu właściwości typu danych lub `Edm.Binary` obsługują tylko `Equal (eq)` `NotEqual (ne)` operatory porównania w ciągach filtru zapytań.

### <a name="differences-for-queue-storage"></a>Różnice dotyczące usługi queue storage

W emulatorze nie ma żadnych różnic związanych z magazynem kolejek.

## <a name="storage-emulator-release-notes"></a>Informacje o wersji emulatora magazynu

### <a name="version-510"></a>Wersja 5,10

* Emulator magazynu nie odrzuci wersji 2019-07-07 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.

### <a name="version-59"></a>Wersja 5,9

* Emulator magazynu nie odrzuci wersji 2019-02-02 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.

### <a name="version-58"></a>Wersja 5,8

* Emulator magazynu nie odrzuci wersji 2018-11-09 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.

### <a name="version-57"></a>Wersja 5,7

* Naprawiono usterkę powodującą awarię, jeśli rejestrowanie zostało włączone.

### <a name="version-56"></a>Wersja 5,6

* Emulator magazynu obsługuje teraz wersję 2018-03-28 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.

### <a name="version-55"></a>Wersja 5,5

* Emulator magazynu obsługuje teraz wersję 2017-11-09 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.
* Dodano obsługę dla właściwości obiektu BLOB **Created** , która zwraca czas utworzenia obiektu BLOB.

### <a name="version-54"></a>Wersja 5,4

* Aby poprawić stabilność instalacji, Emulator nie próbuje zarezerwować portów w czasie instalacji. Jeśli żądane są rezerwacje portów, użyj opcji *-reserveports* polecenia **init** , aby je określić.

### <a name="version-53"></a>Wersja 5,3

* Emulator magazynu obsługuje teraz wersję 2017-07-29 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.

### <a name="version-52"></a>Wersja 5,2

* Emulator magazynu obsługuje teraz wersję 2017-04-17 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.
* Naprawiono usterkę polegającą na tym, że wartości właściwości tabeli nie zostały poprawnie zakodowane.

### <a name="version-51"></a>Wersja 5,1

* Rozwiązano problem polegający na tym, że emulator `DataServiceVersion` magazynu zwrócił nagłówek w niektórych odpowiedziach, gdzie usługa nie została.

### <a name="version-50"></a>Wersja 5,0

* Instalator emulatora magazynu nie sprawdza już istniejących instalacji programu MSSQL i .NET Framework.
* Instalator emulatora magazynu nie tworzy już bazy danych w ramach instalacji. Baza danych będzie w razie konieczności tworzona w ramach uruchomienia.
* Tworzenie bazy danych nie wymaga już podniesienia uprawnień.
* Rezerwacje portów nie są już potrzebne do uruchomienia.
* Dodaje następujące opcje `init`do: `-reserveports` (wymaga podniesienia uprawnień) `-unreserveports` , (wymaga podniesienia `-skipcreate`uprawnień),.
* Opcja interfejsu użytkownika emulatora magazynu w ikonie zasobnika systemu teraz uruchamia interfejs wiersza polecenia. Stary interfejs GUI nie jest już dostępny.
* Niektóre biblioteki DLL zostały usunięte lub zmieniono ich nazwy.

### <a name="version-46"></a>Wersja 4,6

* Emulator magazynu obsługuje teraz wersję 2016-05-31 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.

### <a name="version-45"></a>Wersja 4,5

* Usunięto usterkę, która spowodowała zainicjowanie i zainstalowanie emulatora magazynu, aby zakończyć się niepowodzeniem w przypadku zmiany nazwy bazy danych.

### <a name="version-44"></a>Wersja 4,4

* Emulator magazynu obsługuje teraz wersję 2015-12-11 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.
* Odzyskiwanie pamięci przez emulator magazynu danych obiektów BLOB jest teraz wydajniejsze w przypadku dużej liczby obiektów BLOB.
* Usunięto usterkę, która spowodowała sprawdzenie poprawności kodu XML listy ACL kontenerów nieco inaczej niż w przypadku usługi Storage.
* Rozwiązano usterkę, która czasami powodowała raportowanie wartości maks. Max i min DateTime w niepoprawnej strefie czasowej.

### <a name="version-43"></a>Wersja 4,3

* Emulator magazynu obsługuje teraz wersję 2015-07-08 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.

### <a name="version-42"></a>Wersja 4,2

* Emulator magazynu obsługuje teraz wersję 2015-04-05 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service.

### <a name="version-41"></a>Wersja 4,1

* Emulator magazynu obsługuje teraz wersję 2015-02-21 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service, z wyjątkiem nowych funkcji dołączania obiektów BLOB.
* Jeśli używasz wersji usług magazynu, która nie jest jeszcze obsługiwana przez emulator, Emulator zwróci zrozumiały komunikat o błędzie. Zalecamy użycie najnowszej wersji emulatora. Jeśli wystąpi błąd VersionNotSupportedByEmulator (kod stanu HTTP 400-złe żądanie), Pobierz najnowszą wersję emulatora magazynu.
* Naprawiono usterkę polegającą na tym, że stan wyścigu spowodował nieprawidłowe dane jednostki tabeli podczas współbieżnych operacji scalania.

### <a name="version-40"></a>Wersja 4,0

* Nazwa pliku wykonywalnego emulatora magazynu została zmieniona na *AzureStorageEmulator. exe*.

### <a name="version-32"></a>Wersja 3,2

* Emulator magazynu obsługuje teraz wersję 2014-02-14 usług magazynu dla punktów końcowych obiektów blob, kolejek i Table service. Punkty końcowe usługi plików nie są obecnie obsługiwane w emulatorze magazynu. Aby uzyskać szczegółowe informacje na temat wersji 2014-02-14 [, zobacz Przechowywanie wersji usług Azure Storage](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) .

### <a name="version-31"></a>Wersja 3,1

* Magazyn Geograficznie nadmiarowy dostępny do odczytu (RA-GRS) jest teraz obsługiwany w emulatorze magazynu. Statystyki usługi Get BLOB Service, get Queue Service i Get Table Service API są obsługiwane dla pomocniczego konta i zawsze zwracają wartość elementu odpowiedzi LastSyncTime jako bieżący czas zależny od podstawowej bazy danych SQL. Aby uzyskać programistyczny dostęp do pomocniczego elementu z emulatorem magazynu, użyj biblioteki klienta usługi Storage dla programu .NET w wersji 3,2 lub nowszej. Szczegółowe informacje znajdują się w temacie Microsoft Azure Storage Client Library for .NET Reference.

### <a name="version-30"></a>Wersja 3,0

* Emulator usługi Azure Storage nie jest już dostarczany w tym samym pakiecie co emulator obliczeniowy.
* Graficzny interfejs użytkownika emulatora magazynu jest przestarzały na rzecz interfejsu wiersza polecenia z obsługą skryptu. Aby uzyskać szczegółowe informacje na temat interfejsu wiersza polecenia, zobacz temat narzędzie wiersza polecenia emulatora magazynu. Interfejs graficzny będzie nadal występować w wersji 3,0, ale można uzyskać do niego dostęp tylko wtedy, gdy emulator obliczeń zostanie zainstalowany, klikając prawym przyciskiem myszy ikonę zasobnika systemowego i wybierając pozycję Pokaż interfejs użytkownika emulatora magazynu.
* Wersja 2013-08-15 usług Azure Storage jest teraz w pełni obsługiwana. (Wcześniej ta wersja była obsługiwana tylko przez emulator magazynu w wersji wersja zapoznawcza).

## <a name="next-steps"></a>Następne kroki

* Oceń Międzyplatformowe, obsługiwane przez społeczność [azurite](https://github.com/arafato/azurite)emulator magazynu Open Source. 
* [Przykłady usługi Azure Storage korzystające z platformy .NET](../storage-samples-dotnet.md) zawierają linki do kilku przykładów kodu, których można użyć podczas tworzenia aplikacji.
* [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com) można użyć do pracy z zasobami na koncie magazynu w chmurze i w emulatorze magazynu.
