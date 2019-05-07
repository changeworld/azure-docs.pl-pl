---
title: Korzystanie z emulatora usługi Azure storage do programowania i testowania | Dokumentacja firmy Microsoft
description: Emulator usługi Azure storage zapewnia bezpłatne lokalne Środowisko deweloperskie do tworzenia i testowania aplikacji usługi Azure Storage. Dowiedz się, jak żądania są autoryzowane, jak nawiązać połączenie w emulatorze z aplikacji i jak używać narzędzia wiersza polecenia.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: article
ms.date: 08/10/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 5f55228c80142b2a21af585cb04d16f148460af0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149098"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Korzystanie z emulatora usługi Azure storage do programowania i testowania

Emulator usługi Microsoft Azure storage zapewnia środowisko lokalne, które emuluje obiekty usługi Azure Blob, kolejki i tabeli do celów programistycznych. Za pomocą emulatora magazynu, można przetestować aplikację lokalnie, za pomocą usług magazynu bez tworzenia subskrypcji platformy Azure ani generowania kosztów. Po zakończeniu pracy aplikacji w emulatorze, możesz przełączyć się do korzystania z konta usługi Azure storage w chmurze.

## <a name="get-the-storage-emulator"></a>Pobierz emulator magazynu
Emulator magazynu jest dostępny jako część [zestawu SDK usługi Microsoft Azure](https://azure.microsoft.com/downloads/). Można także zainstalować za pomocą emulatora magazynu [Autonomiczny Instalator rozszerzenia](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (pobieranie bezpośrednie). Aby zainstalować emulator magazynu, musi mieć uprawnienia administratora na komputerze.

Emulator magazynu jest obecnie działa tylko na Windows. Dla osób, biorąc pod uwagę emulatora magazynu, dla systemu Linux, jedną z opcji jest społeczności utrzymywane emulatora magazynu typu open source [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Dane utworzone w jednej wersji emulatora magazynu nie musi być dostępny w przypadku korzystania z innej wersji. Jeśli potrzebujesz w celu utrwalenia Twoich danych w perspektywie długoterminowej, zalecane jest przechowywanie danych na koncie usługi Azure storage, a nie w emulatorze magazynu.
> 
> Emulator magazynu zależy od określonej wersji bibliotek OData. Zastępowanie biblioteki DLL OData, używane przez emulator magazynu z innymi wersjami nie jest obsługiwane i może spowodować nieoczekiwane zachowanie. Jednak dowolnej wersji OData obsługiwane przez usługę storage służy do wysyłania żądań do emulatora.

## <a name="how-the-storage-emulator-works"></a>Jak działa z emulatora magazynu
Emulator magazynu używa lokalnego wystąpienia programu Microsoft SQL Server i lokalnego systemu plików do emulowania usług Azure storage. Domyślnie emulator magazynu używa bazy danych w programie Microsoft SQL Server 2012 Express LocalDB. Można wybrać skonfigurować emulator magazynu, aby dostęp do lokalnego wystąpienia programu SQL Server zamiast wystąpienia LocalDB. Aby uzyskać więcej informacji, zobacz [rozpoczęcia i zainicjować emulatora magazynu](#start-and-initialize-the-storage-emulator) sekcję w dalszej części tego artykułu.

Emulator magazynu łączy się z programu SQL Server lub LocalDB przy użyciu uwierzytelniania Windows.

Istnieją pewne różnice w funkcjonalności między emulatora magazynu i usług Azure storage. Aby uzyskać więcej informacji dotyczących tych różnic, zobacz [różnice między emulatora magazynu i usługi Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) sekcję w dalszej części tego artykułu.

## <a name="start-and-initialize-the-storage-emulator"></a>Uruchom i zainicjować emulatora magazynu

Można uruchomić emulatora usługi Azure storage:
1. Wybierz **Start** przycisk lub naciśnij klawisz **Windows** klucza.
2. Rozpocznij wpisywanie ciągu `Azure Storage Emulator`.
3. Wybierz emulator z listy wyświetlanych aplikacji.

Po uruchomieniu emulatora magazynu, pojawi się okno wiersza polecenia. Aby uruchomić i zatrzymać emulatora magazynu, czyszczenie danych, Pobierz stan i zainicjować emulatora, można użyć tego okna konsoli. Aby uzyskać więcej informacji, zobacz [dokumentacja narzędzia wiersza polecenia usługi Storage emulator](#storage-emulator-command-line-tool-reference) sekcję w dalszej części tego artykułu.

Po uruchomieniu emulatora pojawi się ikona w obszarze powiadomień paska zadań systemu Windows.

Po zamknięciu okna wiersza polecenia emulatora magazynu z emulatora magazynu będzie kontynuował pracę. Aby ponownie wyświetlić okno konsoli emulatora magazynu, postępuj zgodnie z poprzednich kroków, tak, jakby uruchamianie emulatora magazynu.

Podczas pierwszego uruchomienia z emulatora magazynu środowiska lokalnego magazynu jest inicjowany za Ciebie. Proces inicjowania tworzy bazę danych LocalDB i rezerwuje port HTTP dla każdej usługi magazynu lokalnego.

Emulator magazynu jest instalowany domyślnie na `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Możesz użyć [Microsoft Azure Storage Explorer](https://storageexplorer.com) do pracy z zasobami emulatora magazynu lokalnego. Wyszukaj "(projektowanie)" w obszarze "Konta magazynu" w drzewie Eksploratora usługi Storage zasobów po został zainstalowany i uruchomiony emulator magazynu.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicjowanie emulator magazynu, aby użyć innej bazy danych SQL

Można zainicjować z emulatora magazynu, aby wskazywała wystąpienie bazy danych SQL innych niż domyślne wystąpienie LocalDB, można użyć narzędzia wiersza polecenia emulatora magazynu:

1. Otwórz okno konsoli emulatora magazynu, zgodnie z opisem w [rozpoczęcia i zainicjować emulatora magazynu](#start-and-initialize-the-storage-emulator) sekcji.
1. W oknie konsoli, wpisz następujące polecenie, gdzie `<SQLServerInstance>` to nazwa wystąpienia programu SQL Server. Aby użyć programu LocalDB, należy określić `(localdb)\MSSQLLocalDb` jako wystąpienie programu SQL Server.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Umożliwia także następujące polecenie, które określa, że emulator, aby użyć domyślnego wystąpienia programu SQL Server:

   `AzureStorageEmulator.exe init /server .`

   Lub służy następujące polecenie, które ponownie inicjuje bazę danych do domyślnego wystąpienia LocalDB:

   `AzureStorageEmulator.exe init /forceCreate`

Aby uzyskać więcej informacji na temat tych poleceń, zobacz [dokumentacja narzędzia wiersza polecenia usługi Storage emulator](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Możesz użyć [programu Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) do zarządzania wystąpień programu SQL Server, w tym zainstalowanie LocalDB. W Menedżer SMSS **Połącz z serwerem** okno dialogowe, określ `(localdb)\MSSQLLocalDb` w **nazwa serwera:** pola, aby połączyć się z wystąpieniem LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Uwierzytelniania żądań dotyczących emulatora magazynu
Po zainstalowaniu i pracę z emulatora magazynu, możesz przetestować kod przed nim. Podobnie jak w przypadku usługi Azure Storage w chmurze, każde żądanie, wprowadzone przed emulatora magazynu muszą być autoryzowane, chyba że jest to żądania od użytkowników anonimowych. Możesz autoryzować żądania względem emulatora magazynu przy użyciu uwierzytelniania klucza współużytkowanego lub przy użyciu sygnatury dostępu współdzielonego (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autoryzuj przy użyciu poświadczeń klucza wspólnego
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Aby uzyskać więcej informacji na temat parametrów połączenia, zobacz [Konfigurowanie usługi Azure Storage, parametry połączenia](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autoryzuj przy użyciu sygnatury dostępu współdzielonego

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Niektóre biblioteki klienta usługi Azure storage, takich jak biblioteka Xamarin obsługują tylko uwierzytelnianie przy użyciu tokenu (SAS) sygnatury dostępu współdzielonego. Możesz utworzyć token sygnatury dostępu Współdzielonego, za pomocą narzędzia, takiego jak [Eksploratora usługi Storage](https://storageexplorer.com/) lub innej aplikacji, która obsługuje uwierzytelnianie klucza wspólnego.

Można również wygenerować token sygnatury dostępu Współdzielonego, za pomocą programu Azure PowerShell. Poniższy przykład generuje token SAS z pełnymi uprawnieniami do kontenera obiektów blob:

1. Instalacja programu Azure PowerShell, jeśli nie jest jeszcze (przy użyciu najnowszej wersji programu Azure PowerShell, poleceń cmdlet zaleca się). Aby uzyskać instrukcje dotyczące instalacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).
2. Otwórz program Azure PowerShell i uruchom następujące polecenia, zastępując `CONTAINER_NAME` o nazwie wybrane:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Wynikowy sygnatury dostępu współdzielonego identyfikator URI dla nowego kontenera powinny wyglądać podobnie do:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Sygnatura dostępu współdzielonego utworzonych za pomocą w tym przykładzie jest ważny przez jeden dzień. Podpis daje pełny dostęp (odczytu, zapisu, usuwanie, lista), do obiektów blob w kontenerze.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [Using shared sygnatur dostępu (SAS) w usłudze Azure Storage](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Adresowania zasobów w emulatorze magazynu
Punkty końcowe usługi z emulatora magazynu są inne niż konto usługi Azure storage. Różnica polega na ponieważ komputer lokalny nie wykonuje rozpoznawanie nazw domen, wymagających punktów końcowych emulatora magazynu jako adresów lokalnych.

Podczas adresowania zasobów na koncie usługi Azure storage można używać następujących schematu. Nazwa konta jest częścią nazwy hosta identyfikatora URI i zasobów skierowane jest częścią ścieżki identyfikatora URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Na przykład następujący identyfikator URI jest prawidłowym adresem obiektu blob na koncie usługi Azure storage:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Jednak w emulatorze magazynu, ponieważ komputer lokalny nie wykonuje rozpoznawanie nazw domen, nazwa konta jest częścią ścieżka identyfikatora URI, zamiast nazwy hosta. Użyj następującego formatu identyfikatora URI zasobu w emulatorze magazynu:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Na przykład następujący adres, mogą być używane do uzyskiwania dostępu do obiektu blob w emulatorze magazynu:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Punkty końcowe usługi z emulatora magazynu są:

* Usługa blob: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Usługa kolejki: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Usługi TABLE service: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adresowanie dodatkowej za pomocą RA-GRS konta
Począwszy od wersji 3.1 emulatora magazynu obsługuje dostęp do odczytu replikacji magazynu geograficznie nadmiarowego (RA-GRS). Dla zasobów magazynu w chmurze i w lokalnym emulatorze mogą uzyskać dostęp do lokalizacji dodatkowej dołączanie przez - pomocniczego do nazwy konta. Na przykład następujący adres, mogą być używane do uzyskiwania dostępu do obiektu blob przy użyciu pomocniczego tylko do odczytu w emulatorze magazynu:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Dostęp programowy do regionu pomocniczego, za pomocą emulatora magazynu użyj biblioteki klienta usługi Storage dla platformy .NET w wersji 3.2 lub nowszej. Zobacz [biblioteki klienta usługi Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) Aby uzyskać szczegółowe informacje.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Odwołanie do narzędzia wiersza polecenia emulatora magazynu
Począwszy od wersji 3.0 lub nowszej, okno konsoli jest wyświetlany po uruchomieniu emulatora magazynu. Użyj wiersza polecenia w oknie konsoli, aby uruchomić i zatrzymać emulator, a także zapytanie dotyczące stanu i wykonywać inne operacje.

> [!NOTE]
> Jeśli masz program Microsoft Azure compute, zainstalować emulator, ikoną w zasobniku systemowym pojawia się podczas uruchamiania emulatora magazynu. Kliknij prawym przyciskiem myszy ikonę Aby wyświetlić menu, który oferuje graficzny do uruchamiania i zatrzymywania emulatora magazynu.
>
>

### <a name="command-line-syntax"></a>Składnia wiersza polecenia
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opcje
Aby wyświetlić listę opcji, wpisz ciąg `/help` w wierszu polecenia.

| Opcja | Opis | Polecenie | Argumenty |
| --- | --- | --- | --- |
| **Rozpocznij** |Uruchamiania emulatora magazynu. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: Uruchom emulator w bieżącym procesie zamiast tworzenia nowego procesu. |
| **Stop** |Zatrzymuje emulatora magazynu. |`AzureStorageEmulator.exe stop` | |
| **Stan** |Wyświetla stan emulatora magazynu. |`AzureStorageEmulator.exe status` | |
| **Usuń zaznaczenie** |Czyści dane w usługach wszystkie określone w wierszu polecenia. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*Obiekt blob*: Czyści dane z obiektu blob. <br/>*kolejka*: Czyści dane w kolejce. <br/>*Tabela*: Czyści tabeli danych. <br/>*Wszystkie*: Czyści wszystkie dane w przypadku wszystkich usług. |
| **Init** |Przeprowadza jednorazowe inicjowanie, aby skonfigurować emulator. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-Serwer serverName\instanceName*: Określa serwera hostującego wystąpienie programu SQL. <br/>*instanceName — sqlinstance*: Określa nazwę wystąpienia serwera SQL, który ma być używany w domyślnym wystąpieniu serwera. <br/>*-forcecreate*: Wymusza utworzenie bazy danych SQL, nawet jeśli już istnieje. <br/>*-skipcreate*: Pomija tworzenie bazy danych SQL. To ma pierwszeństwo przed - forcecreate.<br/>*-reserveports*: Próby zarezerwowania portach HTTP skojarzone z usługami.<br/>*-unreserveports*: Podejmie próbę usunięcia rezerwacji dla portów HTTP skojarzone z usługami. To ma pierwszeństwo przed - reserveports.<br/>*-inprocess*: Wykonuje inicjowania w bieżącym procesie zamiast duplikowania nowego procesu. Bieżący proces musi zostać uruchomiona z podwyższonym poziomem uprawnień, jeśli zmiana portu rezerwacji. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Różnice między emulatora magazynu i usługi Azure Storage
Ponieważ emulator magazynu jest emulowane środowiska uruchomiona w lokalnym wystąpieniu programu SQL, istnieją różnice w funkcjonalności między emulatora i konto magazynu platformy Azure w chmurze:

* Emulator magazynu obsługuje tylko jedno konto stały i klucza uwierzytelniania dobrze znane.
* Emulator magazynu nie jest usługą skalowalny magazyn i nie obsługuje dużą liczbę klientów jednocześnie.
* Zgodnie z opisem w [adresowania zasobów w emulatorze magazynu](#addressing-resources-in-the-storage-emulator), zasoby zostały rozwiązane w różny sposób w emulatorze magazynu, a konto usługi Azure storage. Różnica ta ma ponieważ rozpoznawanie nazw domen jest dostępna w chmurze, ale nie na komputerze lokalnym.
* Począwszy od wersji 3.1, w ramach emulatora magazynu obsługuje dostęp do odczytu replikacji magazynu geograficznie nadmiarowego (RA-GRS). W emulatorze wszystkie konta mają włączoną RA-GRS i nigdy nie jest dowolnym opóźnienie między replikę podstawową i pomocniczą. Operacje uzyskać statystyki usługi obiektów Blob, Uzyskaj Queue Stats usługi i Uzyskaj Table Stats usługi są obsługiwane w pomocniczych konta i zawsze zwróci wartość `LastSyncTime` element odpowiedzi jako bieżącej godziny zgodnie z podstawowej bazy danych SQL.
* Usługi plików i punkty końcowe usługi protokołu SMB nie są obecnie obsługiwane w emulatorze magazynu.
* Jeśli używasz wersji usługi magazynu, który nie jest jeszcze obsługiwany przez emulator, emulatora magazynu zwraca błąd VersionNotSupportedByEmulator (kod stanu HTTP 400 — Nieprawidłowe żądanie).

### <a name="differences-for-blob-storage"></a>Różnice związane z magazynu obiektów Blob
Następujące różnice dotyczą magazynu obiektów Blob w emulatorze:

* Emulator tylko obsługuje obiektu blob magazynu o rozmiarach do 2 GB.
* Maksymalna długość nazwy obiektu blob w emulatorze magazynu to 256 znaków, a maksymalna długość nazwy obiektu blob w usłudze Azure Storage to 1024 znaki.
* Przyrostowa kopia umożliwia migawki z zastąpione obiektów blob do skopiowania, która zwraca błąd w usłudze.
* Pobieranie zakresów stron różnicowego nie działa między migawkami skopiowana za pomocą przyrostowej kopii obiektu Blob.
* Operacji Put obiektu Blob może się powieść względem obiektu blob, który znajduje się w emulatorze magazynu z aktywną dzierżawę, nawet jeśli w żądaniu nie określono Identyfikatora dzierżawy.
* Dołącz obiekt Blob operacje nie są obsługiwane przez emulator. Próba operacji na uzupełnialny obiekt blob zwrócą błąd FeatureNotSupportedByEmulator (kod stanu HTTP 400 — Nieprawidłowe żądanie).

### <a name="differences-for-table-storage"></a>Różnice związane z usługi Table storage
Następujące różnice dotyczą Table storage w emulatorze:

* Właściwości daty w usłudze tabeli w emulatorze magazynu obsługuje tylko zakres obsługiwanych przez program SQL Server 2005 (są zobowiązani do przypadać później niż 1 stycznia 1753). Wszystkie daty przed 1 stycznia 1753 nie zostaną zmienione na tę wartość. Dokładność daty jest ograniczona do dokładności programu SQL Server 2005, co oznacza, że daty dokładne 1/300th części sekundy.
* Emulator magazynu obsługuje wartości partycji klucz i wiersz właściwości klucza mniej niż 512 bajtów każdego. Ponadto całkowity rozmiar nazwy konta, nazwę tabeli i nazwy właściwości klucza razem nie może przekraczać 900 bajtów.
* Całkowity rozmiar wiersza w tabeli w emulatorze magazynu jest ograniczona do mniej niż 1 MB.
* W emulatorze magazynu, wpisz właściwości danych `Edm.Guid` lub `Edm.Binary` obsługują tylko `Equal (eq)` i `NotEqual (ne)` operatorów porównania w zapytaniu filtru ciągów.

### <a name="differences-for-queue-storage"></a>Różnice związane z usługi Queue storage
Nie ma żadnych różnic specyficzne dla usługi Queue storage w emulatorze.

## <a name="storage-emulator-release-notes"></a>Informacje o wersji emulatora magazynu

### <a name="version-57"></a>W wersji 5.7
Naprawiono usterkę, która mogłoby spowodować awarię, jeśli włączono rejestrowanie.

### <a name="version-56"></a>Wersja 5.6
* Emulator magazynu obsługuje teraz wersji 2018-03-28 usług magazynu w punktach końcowych usługi obiektów Blob, kolejek i tabel.

### <a name="version-55"></a>W wersji 5.5
* Emulator magazynu obsługuje teraz usług magazynu w wersji 2017-11-09 punkty końcowe usługi obiektów Blob, kolejek i tabel.
* Dodano obsługę dla obiektu blob **utworzono** właściwość, która zwraca czas utworzenia obiektu blob.

### <a name="version-54"></a>W wersji 5.4
Aby poprawić stabilność instalacji, emulator nie jest już próbuje zarezerwować portów w czasie instalacji. Razie rezerwacje portu użyj *- reserveports* opcji **init** polecenie, aby je określić.

### <a name="version-53"></a>W wersji 5.3
Emulator magazynu obsługuje teraz usług magazynu w wersji 2017-07-29 punkty końcowe usługi obiektów Blob, kolejek i tabel.

### <a name="version-52"></a>W wersji 5.2
* Emulator magazynu obsługuje teraz usług magazynu w wersji 2017-04-17 w punktach końcowych usługi obiektów Blob, kolejek i tabel.
* Usunięto usterkę, w którym wartości właściwości tabeli zostały nie jest poprawnie zaszyfrowana.

### <a name="version-51"></a>W wersji 5.1
Usunięto usterkę, w której został zwracanie emulatora magazynu `DataServiceVersion` nagłówka w niektórych odpowiedziach, gdzie usługa nie jest.

### <a name="version-50"></a>W wersji 5.0
* Instalator emulatora magazynu już nie sprawdza, czy istniejący MSSQL i instaluje .NET Framework.
* Instalator emulatora magazynu już nie tworzy bazę danych jako część instalacji. Nadal będzie można utworzyć bazy danych, w razie potrzeby uruchamiania w ramach.
* Tworzenie bazy danych nie jest już wymaga podniesienia uprawnień.
* Port rezerwacji nie są już potrzebne do uruchomienia.
* Dodaje następujące opcje `init`: `-reserveports` (wymaga podniesienia), `-unreserveports` (wymaga podniesienia), `-skipcreate`.
* Opcja interfejs użytkownika emulatora magazynu na ikony paska zadań teraz uruchamia interfejsu wiersza polecenia. Stary graficznego interfejsu użytkownika nie jest już dostępna.
* Niektóre biblioteki DLL zostały usunięte lub zmieniono jego nazwę.

### <a name="version-46"></a>W wersji 4.6
* Emulator magazynu obsługuje teraz wersja 2016-05-31 usług magazynu w punktach końcowych usługi obiektów Blob, kolejek i tabel.

### <a name="version-45"></a>W wersji 4.5
* Usunięto usterkę powodującą, inicjowanie i instalacji emulator magazynu, aby zakończyć się niepowodzeniem podczas wykonywania kopii bazy danych została zmieniona.

### <a name="version-44"></a>Wersja 4.4
* Emulator magazynu obsługuje teraz usług magazynu w wersji 2015-12-11 na punkty końcowe usługi obiektów Blob, kolejek i tabel.
* Emulator magazynu wyrzucania elementów bezużytecznych danych obiektów blob jest teraz bardziej wydajne, podczas pracy z dużą liczbą obiektów blob.
* Usunięto usterkę powodującą kontenera XML listy ACL na sprawdzenie poprawności nieco inaczej od jak Usługa magazynu robi.
* Usunięto usterkę powodującą czasami maksymalne i minimalne wartości typu DateTime do zgłaszania w strefie czasowej niepoprawne.

### <a name="version-43"></a>W wersji 4.3
* Emulator magazynu obsługuje teraz usług magazynu w wersji 2015-07-08 punkty końcowe usługi obiektów Blob, kolejek i tabel.

### <a name="version-42"></a>Wersja 4.2
* Emulator magazynu obsługuje teraz usług magazynu w wersji 2015-04-05 punkty końcowe usługi obiektów Blob, kolejek i tabel.

### <a name="version-41"></a>W wersji 4.1
* Emulator magazynu obsługuje teraz w wersji 2015-02-21 usług magazynu obiektów Blob, kolejek i tabel usługi punkty końcowe, z wyjątkiem nowe funkcje Uzupełnialnych obiektów Blob.
* Jeśli używasz wersji usługi magazynu, który nie jest jeszcze obsługiwany przez emulator, emulator zwraca komunikat zrozumiałego błędu. Zalecamy używanie najnowszej wersji emulatora. Jeśli wystąpi błąd VersionNotSupportedByEmulator (kod stanu HTTP 400 — Nieprawidłowe żądanie), Pobierz najnowszą wersję z emulatora magazynu.
* Usunięto usterkę którym dane jednostki wyścigu spowodowaną tabeli jako niepoprawna podczas operacji scalania współbieżnych.

### <a name="version-40"></a>W wersji 4.0
* Nazwa pliku wykonywalnego emulatora magazynu, które została zmieniona na *AzureStorageEmulator.exe*.

### <a name="version-32"></a>W wersji 3.2
* Emulator magazynu obsługuje teraz usług magazynu w wersji 2014-02-14 punkty końcowe usługi obiektów Blob, kolejek i tabel. Punkty końcowe usługi plików nie są obecnie obsługiwane w emulatorze magazynu. Zobacz [przechowywania wersji dla usług Azure Storage](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) szczegółowe informacje na temat wersji 2014-02-14.

### <a name="version-31"></a>Wersja 3.1
* Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) jest teraz obsługiwany w emulatorze magazynu. Pobieranie statystyki usługi obiektów Blob, Uzyskaj Queue Stats usługi i uzyskać tabelę statystyki interfejsów API usługi są obsługiwane w przypadku pomocniczego konta i zawsze zwraca wartość elementu LastSyncTime odpowiedzi jako bieżącej godziny zgodnie z podstawowej bazy danych SQL. Dostęp programowy do regionu pomocniczego, za pomocą emulatora magazynu użyj biblioteki klienta usługi Storage dla platformy .NET w wersji 3.2 lub nowszej. Informacji można znaleźć biblioteki klienta usługi Microsoft Azure Storage dla platformy .NET odwołania.

### <a name="version-30"></a>W wersji 3.0
* Emulator usługi Azure storage nie jest już dostarczany w pakiecie emulatora obliczeń.
* Magazyn emulatora graficznego interfejsu użytkownika jest przestarzała na rzecz za pomocą skryptów interfejsu wiersza polecenia. Aby uzyskać szczegółowe informacje dotyczące interfejsu wiersza polecenia Zobacz dokumentacja narzędzia wiersza polecenia usługi Storage Emulator. Interfejs graficzny będą w dalszym ciągu być obecne w wersji 3.0 lub nowszej, ale może zostać oceniony jedynie, po zainstalowaniu emulatora obliczeń przez kliknięcie prawym przyciskiem myszy na ikonie na pasku zadań systemu i wybierając polecenie Pokaż magazynowania interfejs użytkownika emulatora.
* Wersji 2013-08-15 usług Azure storage jest teraz w pełni obsługiwane. (Wcześniej ta wersja została obsługiwane tylko za pomocą emulatora magazynu wersję 2.2.1 (wersja zapoznawcza).)

## <a name="next-steps"></a>Kolejne kroki

* Oceń emulatora magazynu dla wielu platform, obsługiwane przez społeczności "open source" [Azurite](https://github.com/arafato/azurite). 
* [Przykłady usługi Azure Storage przy użyciu platformy .NET](../storage-samples-dotnet.md) zawiera linki do kilka przykładów kodu, można użyć podczas tworzenia aplikacji.
* Możesz użyć [Microsoft Azure Storage Explorer](https://storageexplorer.com) do pracy z zasobami w chmurze konta magazynu i w emulatorze magazynu.
