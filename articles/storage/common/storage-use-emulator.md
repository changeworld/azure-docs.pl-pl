---
title: Użyć emulatora magazynu Azure do programowania i testowania | Dokumentacja firmy Microsoft
description: Emulatora magazynu Azure udostępnia wolnego lokalne Środowisko deweloperskie do tworzenia i testowania aplikacji usługi Azure Storage. Dowiedz się, jak żądania są autoryzowane, sposób nawiązywania połączenia z emulatora z aplikacji i jak używać narzędzia wiersza polecenia.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: tamram
ms.openlocfilehash: c6500cd1ddd31d789b8cd5d72d6e4614db3f88db
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295589"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Użyć emulatora magazynu Azure do programowania i testowania

Emulator magazynu Microsoft Azure zapewnia środowisko lokalne, które emuluje usługi obiektów Blob platformy Azure, kolejki i tabeli do celów programistycznych. Przy użyciu emulatora magazynu, można przetestować aplikację na usług magazynu lokalnie, bez tworzenia subskrypcji platformy Azure lub ponoszenia kosztów. Po zakończeniu jak aplikacja działa w emulatorze, możesz przełączyć się do konta magazynu Azure w chmurze.

## <a name="get-the-storage-emulator"></a>Pobierz emulator magazynu
Emulator magazynu jest dostępny jako część [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Można także zainstalować emulator magazynu przy użyciu [Autonomiczny Instalator rozszerzenia](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (bezpośrednie pobieranie). Aby zainstalować emulator magazynu, musi mieć uprawnienia administratora na tym komputerze.

Emulator magazynu jest obecnie działa tylko w systemie Windows. Dla osób, biorąc pod uwagę emulatora magazynu, dla systemu Linux, jedną z opcji jest społeczności utrzymywane emulatora magazynu typu open source [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Dane utworzone w jednej wersji emulatora magazynu nie jest gwarantowana była dostępna, gdy w innej wersji. Jeśli musisz utrwalić danych długoterminowym, zaleca się przechowywania tych danych na koncie magazynu platformy Azure, a nie w emulatorze magazynu.
> <p/>
> Emulator magazynu zależy od określonych wersji biblioteki OData. Zastępowanie bibliotek DLL OData używanego przez emulator magazynu z innymi wersjami nie jest obsługiwane i może spowodować nieoczekiwane zachowanie. Jednak dowolnej wersji OData obsługiwany przez usługę magazynu może służyć do wysyłania żądań do emulatora.
>

## <a name="how-the-storage-emulator-works"></a>Jak działa emulator magazynu
Emulator magazynu używa lokalnego wystąpienia programu Microsoft SQL Server i lokalnego systemu plików, co pozwoliłoby na emulowanie usług magazynu platformy Azure. Domyślnie emulator magazynu używa bazy danych w programie Microsoft SQL Server 2012 Express LocalDB. Można skonfigurować emulator magazynu, aby dostęp do lokalnego wystąpienia programu SQL Server zamiast wystąpieniem LocalDB. Aby uzyskać więcej informacji, zobacz [Start i zainicjować emulator magazynu](#start-and-initialize-the-storage-emulator) sekcji w dalszej części tego artykułu.

Emulator magazynu łączy do programu SQL Server lub LocalDB przy użyciu uwierzytelniania systemu Windows.

Istnieją pewne różnice w działaniu między emulatora magazynu i usług Azure storage. Aby uzyskać więcej informacji na temat tych różnic, zobacz [różnice między emulatora magazynu i usługi Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) sekcji w dalszej części tego artykułu.

## <a name="start-and-initialize-the-storage-emulator"></a>Uruchom i zainicjować emulator magazynu

Aby uruchomić emulatora magazynu Azure:
1. Wybierz **Start** przycisk lub naciśnij przycisk **Windows** klucza.
2. Rozpocznij wpisywanie `Azure Storage Emulator`.
3. Wybierz emulator z listy wyświetlanych aplikacji.

Po uruchomieniu emulatora magazynu, pojawi się okno wiersza polecenia. To okno konsoli służy do uruchamiania i zatrzymywania emulator magazynu, czyszczenia danych, Pobierz stan i zainicjować emulator. Aby uzyskać więcej informacji, zobacz [odwołanie do narzędzia wiersza polecenia emulatora magazynu](#storage-emulator-command-line-tool-reference) sekcji w dalszej części tego artykułu.

Po uruchomieniu emulatora będzie widoczna ikona w obszarze powiadomień paska zadań systemu Windows.

Po zamknięciu okna wiersza polecenia emulatora magazynu emulator magazynu będzie nadal działać. Aby ponownie wywołaj okno konsoli emulatora magazynu, postępuj zgodnie z poprzednim tak, jakby uruchamianie emulatora magazynu.

Przy pierwszym uruchomieniu emulatora magazynu zainicjowaniu środowiska lokalnego magazynu dla Ciebie. Proces inicjowania tworzy bazę danych w LocalDB i rezerwuje port HTTP dla każdej usługi Magazyn lokalny.

Emulator magazynu jest instalowana domyślnie `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Można użyć [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com) do pracy z zasobami emulatora magazynu lokalnego. Wyszukaj "(Programowanie)" w części "Konta magazynu" w drzewie Eksploratora usługi Storage zasobów po zainstalowaniu i uruchomiony emulator magazynu.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicjowanie emulator magazynu, aby użyć innej bazy danych SQL

Aby zainicjować emulatora magazynu, aby wskazywała wystąpienie bazy danych SQL innych niż domyślne wystąpienie bazy danych LocalDB, można użyć narzędzia wiersza polecenia emulatora magazynu:

1. Otwórz okno konsoli emulatora magazynu, zgodnie z opisem w [Start i zainicjować emulator magazynu](#start-and-initialize-the-storage-emulator) sekcji.
1. W oknie konsoli, wpisz następujące polecenie, gdzie `<SQLServerInstance>` to nazwa wystąpienia programu SQL Server. Aby użyć LocalDB, podaj `(localdb)\MSSQLLocalDb` jako wystąpienie programu SQL Server.

  `AzureStorageEmulator.exe init /server <SQLServerInstance>`

  Można także użyć następujące polecenie, które określa, że emulator, aby użyć domyślnego wystąpienia programu SQL Server:

  `AzureStorageEmulator.exe init /server .\\`

  Lub służy następujące polecenie, które ponownie inicjuje bazy danych na domyślne wystąpienie bazy danych LocalDB:

  `AzureStorageEmulator.exe init /forceCreate`

Aby uzyskać więcej informacji na temat tych poleceń, zobacz [odwołanie do narzędzia wiersza polecenia emulatora magazynu](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Można użyć [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS), aby zarządzać wystąpień programu SQL Server, w tym zainstalowanie LocalDB. W Menedżer SMSS **Połącz z serwerem** okna dialogowego, określ `(localdb)\MSSQLLocalDb` w **nazwa serwera:** pola, aby połączyć się z wystąpieniem LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Uwierzytelniania żądań dotyczących emulator magazynu
Po zainstalowaniu i uruchomiony emulator magazynu, można przetestować kod na nim. Podobnie jak w przypadku usługi Azure Storage w chmurze, każde żądanie, wprowadzone dla emulatora magazynu muszą być autoryzowane, chyba że jest żądania od użytkowników anonimowych. Można autoryzować żądania dla emulatora magazynu przy użyciu uwierzytelniania klucza współużytkowanego lub za pomocą sygnatury dostępu współdzielonego (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autoryzacji przy użyciu poświadczeń klucz wstępny
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Aby uzyskać więcej informacji na parametry połączenia, zobacz [parametry połączenia Konfigurowanie usługi Azure Storage](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autoryzacja sygnatury dostępu współdzielonego
Niektóre biblioteki klienta magazynu Azure, takiej jak biblioteka Xamarin obsługuje tylko uwierzytelnianie za pomocą tokenu sygnatury dostępu Współdzielonego dostępu współdzielonego. Możesz utworzyć token sygnatury dostępu Współdzielonego przy użyciu narzędzia, takiego jak [Eksploratora usługi Storage](http://storageexplorer.com/) lub innej aplikacji, która obsługuje uwierzytelnianie klucza wspólnego.

Można również generować tokenu sygnatury dostępu Współdzielonego przy użyciu programu Azure PowerShell. Poniższy przykład generuje token SAS z pełnymi uprawnieniami do kontenera obiektów blob:

1. Instalacja programu Azure PowerShell, jeśli nie jest jeszcze (przy użyciu najnowszej wersji programu Azure PowerShell polecenia cmdlet jest zalecane). Aby uzyskać instrukcje instalacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Otwórz program Azure PowerShell i uruchom następujące polecenia, zastępując `ACCOUNT_NAME` i `ACCOUNT_KEY==` przy użyciu własnych poświadczeń i `CONTAINER_NAME` o nazwie wybrane:

```powershell
$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Wynikowa sygnatury dostępu współdzielonego identyfikatora URI dla nowego kontenera powinna być podobna do:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Sygnatury dostępu współdzielonego utworzone za pomocą w tym przykładzie jest ważny przez jeden dzień. Podpis daje pełny dostęp (Odczyt, zapis, Usuń, listy) do obiektów blob w kontenerze.

Aby uzyskać więcej informacji dotyczących sygnatur dostępu współdzielonego, zobacz [używanie udostępnionych sygnatur dostępu (SAS) w usłudze Azure Storage](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Adresowania zasobów w emulatorze magazynu
Punkty końcowe usługi dla emulatora magazynu są inne niż konto usługi Azure storage. Różnica polega na tym, ponieważ komputer lokalny nie przeprowadza rozpoznawanie nazw domen, wymagających punkty końcowe emulatora magazynu być adresów lokalnych.

Podczas adresowania zasobów na koncie magazynu platformy Azure można używać następujących schematu. Nazwa konta jest częścią identyfikatora URI nazwy hosta i zasobów adresowane jest częścią ścieżka identyfikatora URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Na przykład następujący identyfikator URI jest prawidłowy adres obiektu blob na koncie magazynu Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Emulator magazynu, ponieważ jednak komputer lokalny nie przeprowadza rozpoznawanie nazwy domeny, nazwa konta jest częścią ścieżka identyfikatora URI zamiast nazwy hosta. Użyj następującego formatu identyfikatora URI zasobu w emulatorze magazynu:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Na przykład następujący adres może być używane do uzyskiwania dostępu do obiektu blob w emulatorze magazynu:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Punkty końcowe usługi dla emulatora magazynu są:

* Usługa blob: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Usługa kolejki: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Usługa tabel: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adresy pomocniczego z RA-GRS konta
Począwszy od wersji 3.1 emulator magazynu obsługuje dostęp do odczytu Replikacja geograficznie nadmiarowego (RA-GRS). Dla zasobów magazynowania zarówno w chmurze, jak i w lokalnym emulatorze, można uzyskać dostępu do lokalizacji dodatkowej dołączanie przez - dodatkowej do nazwy konta. Na przykład następujący adres może być używane do uzyskiwania dostępu do obiektu blob przy użyciu pomocniczego tylko do odczytu w emulatorze magazynu:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Dla programowy dostęp do pomocniczego przy użyciu emulatora magazynu należy używać biblioteki klienta usługi Storage dla platformy .NET w wersji 3.2 lub nowszej. Zobacz [Biblioteka klienta usługi Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) szczegółowe informacje.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Odwołanie do narzędzia wiersza polecenia emulatora magazynu
Począwszy od wersji 3.0 z okna konsoli jest wyświetlany po uruchomieniu emulatora magazynu. Użyć wiersza polecenia w oknie konsoli, aby uruchomić i zatrzymać emulatora, a także zapytanie o stan i wykonywanie innych operacji.

> [!NOTE]
> Jeśli masz Microsoft Azure obliczeniowe zainstalować emulator, po uruchomieniu emulatora magazynu pojawi się ikona na pasku zadań systemu. Kliknij prawym przyciskiem myszy ikonę, aby wyświetlić menu, które oferuje graficzny uruchamianie i zatrzymywanie emulatora magazynu.
>
>

### <a name="command-line-syntax"></a>Składnia wiersza polecenia
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opcje
Aby wyświetlić listę opcji, należy wpisać `/help` w wierszu polecenia.

| Opcja | Opis | Polecenie | Argumenty |
| --- | --- | --- | --- |
| **Rozpocznij** |Emulator magazynu jest uruchamiany. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: Uruchom emulator w bieżącym procesie zamiast tworzenia nowego procesu. |
| **Stop** |Zatrzymuje emulatora magazynu. |`AzureStorageEmulator.exe stop` | |
| **Stan** |Wyświetla stan emulatora magazynu. |`AzureStorageEmulator.exe status` | |
| **Wyczyść** |Usuwa dane w usługach wszystkie określone w wierszu polecenia. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*Obiekt blob*: Czyści obiektu blob danych. <br/>*kolejka*: usuwa dane kolejki. <br/>*Tabela*: usuwa dane w tabeli. <br/>*wszystkie*: usuwa wszystkie dane w wszystkich usług. |
| **Init** |Przeprowadza jednorazowe inicjowanie, aby skonfigurować emulator. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-Serwer serverName\instanceName*: Określa serwera obsługującego wystąpienie programu SQL. <br/>*-sqlinstance instanceName*: Określa nazwę wystąpienia serwera SQL do użycia w domyślnym wystąpieniu serwera. <br/>*-forcecreate*: wymusza utworzenie bazy danych SQL, nawet jeśli już istnieje. <br/>*-skipcreate*: pomija tworzenie bazy danych SQL. To ma pierwszeństwo przed - forcecreate.<br/>*-reserveports*: próbuje zarezerwować portów HTTP powiązanego ze wskazanymi usługami.<br/>*-unreserveports*: próby usunięcia zastrzeżenia dla portów HTTP powiązanego ze wskazanymi usługami. To ma pierwszeństwo przed - reserveports.<br/>*-inprocess*: wykonuje inicjowania w bieżącym procesie, a nie dochodzi do uruchamiania nowego procesu. Bieżący proces musi zostać uruchomiona z podwyższonym poziomem uprawnień w przypadku zmiany rezerwacji portu. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Różnice między emulatora magazynu i usługi Azure Storage
Ponieważ emulator magazynu jest emulowane środowisko uruchomiony w lokalnym wystąpieniu serwera SQL, istnieją różnice w działaniu między emulatora i konto magazynu platformy Azure w chmurze:

* Emulator magazynu obsługuje tylko jedno konto stałych i klucz uwierzytelniania dobrze znane.
* Emulator magazynu nie jest usługą skalowalności magazynu i nie obsługuje dużej liczby równoczesnych klientów.
* Zgodnie z opisem w [adresowania zasobów w emulatorze magazynu](#addressing-resources-in-the-storage-emulator), zasoby są opisane w różny sposób w emulatorze magazynu i konto magazynu platformy Azure. Ta różnica wynika rozpoznania nazwy domeny jest dostępny w chmurze, ale nie na komputerze lokalnym.
* Począwszy od wersji 3.1, konto emulatora magazynu obsługuje dostęp do odczytu Replikacja geograficznie nadmiarowego (RA-GRS). W emulatorze wszystkie konta mają włączoną RA-GRS, i nigdy nie jest wszelkie opóźnienia między replikami podstawowego i pomocniczego. Operacje uzyskać statystyki usługi obiektów Blob, Pobierz Statystyka usługi kolejki i uzyskać statystyki usługi tabeli są obsługiwane w dodatkowej konta i zawsze zwróci wartość `LastSyncTime` elementu odpowiedzi jako bieżącej godziny zgodnie z podstawowej bazy danych SQL.
* Usługi plików i punktów końcowych usługi protokołu SMB nie są obecnie obsługiwane w emulatorze magazynu.
* Jeśli używasz wersji usług magazynu, który nie jest jeszcze obsługiwany przez emulator emulator magazynu zwrócą błąd VersionNotSupportedByEmulator (kod stanu HTTP 400 - Niewłaściwe żądanie).

### <a name="differences-for-blob-storage"></a>Różnice dla magazynu obiektów Blob
Następujące różnice dotyczą magazynu obiektów Blob w emulatorze:

* Emulator tylko obsługuje obiektu blob magazynu rozmiar do 2 GB.
* Przyrostowa kopia umożliwia migawek z zastąpione obiekty BLOB do skopiowania, która zwraca błąd w usłudze.
* Diff zakresów stron Get nie działa między migawki skopiowane za pomocą przyrostowej kopii obiektu Blob.
* Operacja umieszczanie obiektu Blob może się powieść względem obiektu blob, który istnieje w emulatorze magazynu z aktywną dzierżawę, nawet wtedy, gdy w żądaniu nie określono Identyfikatora dzierżawy.
* Dołącz operacje nie są obsługiwane przez emulator obiektu Blob. Próba operacji na uzupełnialny obiekt blob zwrócą błąd FeatureNotSupportedByEmulator (kod stanu HTTP 400 - Niewłaściwe żądanie).

### <a name="differences-for-table-storage"></a>Różnice dla magazynu tabel
Następujące różnice dotyczą magazynu tabel w emulatorze:

* Właściwości daty w usłudze tabel w emulatorze magazynu obsługuje tylko zakresu obsługiwanego przez program SQL Server 2005 (są one wymagane przypadać później niż 1 stycznia 1753). Wszystkie daty przed 1 stycznia 1753 nie zostaną zmienione na tę wartość. Dokładność daty jest ograniczona do dokładność programu SQL Server 2005, co oznacza, że daty są dokładne 1/300th sekundy.
* Emulator magazynu obsługuje partycji klucza i wiersza wartości właściwości klucza mniej niż 512 bajtów każdego. Ponadto całkowity rozmiar nazwa konta oraz nazwę tabeli i nazw właściwości kluczy razem nie może przekraczać 900 bajtów.
* Całkowity rozmiar wiersza w tabeli w emulatorze magazynu jest ograniczona do mniej niż 1 MB.
* W emulatorze magazynu właściwości danych typu `Edm.Guid` lub `Edm.Binary` obsługuje tylko `Equal (eq)` i `NotEqual (ne)` operatory porównania w zapytaniu filtru ciągów.

### <a name="differences-for-queue-storage"></a>Różnice dla magazynu kolejek
Nie ma żadnych różnic specyficzne dla magazynu kolejek w emulatorze.

## <a name="storage-emulator-release-notes"></a>Informacje o wersji emulatora magazynu

### <a name="version-55"></a>W wersji 5.5
* Emulator magazynu obsługuje teraz 2017-11-09 wersji usług magazynu dla punktów końcowych usługi obiektów Blob, kolejki i tabeli.
* Dodano obsługę dla obiektu blob **utworzony** właściwość, która zwraca czas utworzenia obiektu blob.

### <a name="version-54"></a>W wersji 5.4
Aby poprawić stabilność instalacji, emulator próbuje już zarezerwować portów w czasie instalacji. W razie potrzeby są rezerwacji portu można używać *- reserveports* opcji **init** polecenie, aby je określić.

### <a name="version-53"></a>W wersji 5.3
Emulator magazynu obsługuje teraz 2017-07-29 wersji usług magazynu dla punktów końcowych usługi obiektów Blob, kolejki i tabeli.

### <a name="version-52"></a>W wersji 5.2
* Emulator magazynu obsługuje teraz 2017-04-17 wersji usług magazynu dla punktów końcowych usługi obiektów Blob, kolejki i tabeli.
* Stałe usterki, których wartości właściwości tabeli zostały nie jest poprawnie zaszyfrowana.

### <a name="version-51"></a>W wersji 5.1
Stałej usterki, gdzie został zwracanie emulator magazynu `DataServiceVersion` nagłówka w niektórych odpowiedzi, gdy usługa nie była.

### <a name="version-50"></a>W wersji 5.0
* Instalator emulatora magazynu nie sprawdza, czy istniejące MSSQL i instaluje .NET Framework.
* Instalator emulatora magazynu nie tworzy już bazy danych jako część instalacji. Nadal będzie można utworzyć bazy danych, w razie potrzeby jako część uruchomienia.
* Tworzenie bazy danych nie wymaga podniesienia uprawnień.
* Port rezerwacji nie są już potrzebne do uruchomienia.
* Dodaje następujące opcje, aby `init`: `-reserveports` (wymaga podniesienia uprawnień), `-unreserveports` (wymaga podniesienia uprawnień), `-skipcreate`.
* Teraz opcję interfejs użytkownika emulatora magazynu na ikony paska zadań uruchamia interfejsu wiersza polecenia. Stary graficznego interfejsu użytkownika nie jest już dostępny.
* Niektórych bibliotek DLL zostały usunięte lub zmieniono jego nazwę.

### <a name="version-46"></a>W wersji 4.6
* Emulator magazynu obsługuje teraz 2016-05-31 wersji usług magazynu dla punktów końcowych usługi obiektów Blob, kolejki i tabeli.

### <a name="version-45"></a>W wersji 4.5
* Stałe usterkę, która spowodowała inicjowania i instalacji emulator magazynu, aby zakończyć się niepowodzeniem podczas wykonywania kopii bazy danych została zmieniona.

### <a name="version-44"></a>Wersja 4.4
* Emulator magazynu obsługuje teraz wersji 2015-12-11 usług magazynu dla punktów końcowych usługi obiektów Blob, kolejki i tabeli.
* Emulator magazynu wyrzucanie elementów bezużytecznych danych obiektu blob jest teraz efektywniejsze, podczas pracy z dużą liczbą obiektów blob.
* Stałe usterkę, która spowodowała kontenera XML listy kontroli dostępu do sprawdzenia poprawności nieco inaczej, w jaki sposób Usługa magazynu jest.
* Stałe usterkę, która czasami spowodował max i min wartości daty/godziny należy podać w strefie czasowej niepoprawne.

### <a name="version-43"></a>Wersji 4.3
* Emulator magazynu obsługuje teraz wersji 2015-07-08 usług magazynu dla punktów końcowych usługi obiektów Blob, kolejki i tabeli.

### <a name="version-42"></a>W wersji 4.2
* Emulator magazynu obsługuje teraz wersji 2015-04-05 usług magazynu dla punktów końcowych usługi obiektów Blob, kolejki i tabeli.

### <a name="version-41"></a>Wersji 4.1
* Emulator magazynu obsługuje teraz wersji 2015-02-21 usług magazynu na obiektów Blob, kolejki i tabeli punktów końcowych usług, z wyjątkiem nowych funkcji Dołącz obiektu Blob.
* Jeśli używasz wersji usług magazynu, który nie jest jeszcze obsługiwany przez emulator emulator zwraca komunikat o błędzie łatwy do rozpoznania. Zalecamy używanie najnowszej wersji emulatora. Jeśli wystąpi błąd VersionNotSupportedByEmulator (kod stanu HTTP 400 - Niewłaściwe żądanie), Pobierz najnowszą wersję emulatora magazynu.
* Stałe usterki którym wyścigu spowodowaną tabeli jednostki jest niepoprawna podczas operacji scalania współbieżnych.

### <a name="version-40"></a>W wersji 4.0
* Emulator magazynu wykonywalnego została zmieniona na *AzureStorageEmulator.exe*.

### <a name="version-32"></a>W wersji 3.2
* Emulator magazynu obsługuje teraz wersji 2014-02-14 usług magazynu dla punktów końcowych usługi obiektów Blob, kolejki i tabeli. Punkty końcowe usługi plików nie są obecnie obsługiwane w emulatorze magazynu. Zobacz [przechowywanie wersji usług magazynu Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) szczegółowe informacje o wersji 2014-02-14.

### <a name="version-31"></a>W wersji 3.1
* Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) jest teraz obsługiwane w emulatorze magazynu. Uzyskać statystyki usługi obiektów Blob, Pobierz Statystyka usługi kolejki i pobrać tabeli Statystyka interfejsów API usługi Service są obsługiwane w przypadku konta dodatkowej i zawsze zwróci wartość elementu odpowiedzi LastSyncTime jako bieżącej godziny zgodnie z podstawowej bazy danych SQL. Dla programowy dostęp do pomocniczego przy użyciu emulatora magazynu należy używać biblioteki klienta usługi Storage dla platformy .NET w wersji 3.2 lub nowszej. Aby uzyskać więcej informacji, zobacz biblioteki klienta usługi Microsoft Azure Storage dla odwołania .NET.

### <a name="version-30"></a>W wersji 3.0
* Emulatora magazynu Azure nie jest dostarczany w pakiecie emulatora obliczeń.
* Skryptowy interfejs wiersza polecenia jest zastąpiona magazynu emulatora graficznego interfejsu użytkownika. Szczegółowe informacje dotyczące interfejsu wiersza polecenia Zobacz dotyczące narzędzia wiersza polecenia emulatora magazynu. Interfejs graficzny będzie znajdować się w wersji 3.0 lub nowszej, ale jego można uzyskać tylko po zainstalowaniu emulatora obliczeniowe przez kliknięcie prawym przyciskiem myszy na ikonie na pasku zadań systemu i wybierając polecenie Pokaż magazynu interfejs użytkownika emulatora.
* W wersji 2013-08-15 usług magazynu Azure teraz jest w pełni obsługiwany. (Wcześniej tej wersji jest obsługiwana tylko przez Emulator magazynu wersji 2.2.1 wersji zapoznawczej.)

## <a name="next-steps"></a>Kolejne kroki

* Ocena emulatora magazynu i platform, obsługiwane społeczności typu open source [Azurite](https://github.com/arafato/azurite). 
* [Przykładów dla magazynu platformy Azure przy użyciu platformy .NET](../storage-samples-dotnet.md) zawiera linki do kilka przykładów kodu, można użyć podczas tworzenia aplikacji.
* Można użyć [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com) do pracy z zasobami w chmurze konta magazynu, a w emulatorze magazynu.