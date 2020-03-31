---
title: Użyj emulatora magazynu platformy Azure do tworzenia i testowania | Dokumenty firmy Microsoft
description: Emulator magazynu platformy Azure zapewnia bezpłatne lokalne środowisko programistyczne do tworzenia i testowania aplikacji usługi Azure Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1f13efeb5c2ebcb8b23dea6f9ae997039972f089
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72428320"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Używanie emulatora magazynu platformy Azure do tworzenia i testowania

Emulator magazynu platformy Microsoft Azure to narzędzie, które emuluje usługi Azure Blob, Queue i Table do lokalnych celów programistów. Można przetestować aplikację na usługi magazynu lokalnie bez tworzenia subskrypcji platformy Azure lub ponoszenia żadnych kosztów. Jeśli jesteś zadowolony z tego, jak aplikacja działa w emulatorze, przełącz się na konto usługi Azure storage w chmurze.

## <a name="get-the-storage-emulator"></a>Pobierz emulator pamięci masowej

Emulator magazynu jest dostępny jako część microsoft [azure SDK](https://azure.microsoft.com/downloads/). Emulator pamięci masowej można również zainstalować za pomocą [autonomicznego instalatora](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (pobieranie bezpośrednie). Aby zainstalować emulator magazynu, musisz mieć uprawnienia administracyjne na komputerze.

Emulator magazynu działa obecnie tylko w systemie Windows. Jeśli potrzebujesz emulatora pamięci masowej dla Linuksa, jedną z opcji jest utrzymywany przez społeczność, emulator pamięci masowej typu open source [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Dane utworzone w jednej wersji emulatora magazynu nie jest gwarantowane być dostępne podczas korzystania z innej wersji. Jeśli musisz utrwalić dane na dłuższą metę, zaleca się przechowywanie tych danych na koncie magazynu platformy Azure, a nie w emulatorze magazynu.
> 
> Emulator magazynu zależy od określonych wersji bibliotek OData. Zastępowanie bibliotek DLL OData używanych przez emulator magazynu z innymi wersjami jest nieobsługiwał i może powodować nieoczekiwane zachowanie. Jednak każda wersja OData obsługiwane przez usługę magazynu może służyć do wysyłania żądań do emulatora.

## <a name="how-the-storage-emulator-works"></a>Jak działa emulator pamięci masowej

Emulator magazynu używa lokalnego wystąpienia usługi Microsoft SQL Server 2012 Express LocalDB do emulowania usług magazynu platformy Azure. Można skonfigurować emulator magazynu, aby uzyskać dostęp do lokalnego wystąpienia programu SQL Server zamiast wystąpienia LocalDB. Zobacz [Start i zainicjować emulator magazynu](#start-and-initialize-the-storage-emulator) sekcji w dalszej części tego artykułu, aby dowiedzieć się więcej.

Emulator magazynu łączy się z programem SQL Server lub LocalDB przy użyciu uwierzytelniania systemu Windows.

Istnieją pewne różnice w funkcjonalności między emulatorem magazynu i usługami magazynu platformy Azure. Aby uzyskać więcej informacji na temat tych różnic, zobacz [różnice między emulator magazynu i usługi Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) sekcji w dalszej części tego artykułu.

## <a name="start-and-initialize-the-storage-emulator"></a>Uruchamianie i inicjowanie emulatora magazynu

Aby uruchomić emulator magazynu platformy Azure:

1. Wybierz przycisk **Start** lub naciśnij klawisz **Windows.**
2. Rozpocznij `Azure Storage Emulator`wpisywanie .
3. Wybierz emulator z listy wyświetlanych aplikacji.

Po uruchomieniu emulatora magazynu pojawi się okno wiersza polecenia. Za pomocą tego okna konsoli można uruchomić i zatrzymać emulator magazynu. Można również wyczyścić dane, uzyskać stan i zainicjować emulator z wiersza polecenia. Aby uzyskać więcej informacji, zobacz sekcję [odwołania do wiersza polecenia emulatora magazynu](#storage-emulator-command-line-tool-reference) w dalszej części tego artykułu.

> [!NOTE]
> Emulator magazynu platformy Azure może nie uruchomić poprawnie, jeśli inny emulator magazynu, takich jak Azurite, jest uruchomiony w systemie.

Po uruchomieniu emulatora pojawi się ikona w obszarze powiadomień paska zadań systemu Windows.

Po zamknięciu okna wiersza polecenia emulatora magazynu emulator będzie nadal działać. Aby ponownie przywołać okno konsoli emulatora magazynu, wykonaj poprzednie kroki, tak jakby uruchamianie emulatora magazynu.

Przy pierwszym uruchomieniu emulatora magazynu, lokalne środowisko magazynu jest inicjowane dla Ciebie. Proces inicjowania tworzy bazę danych w LocalDB i rezerwuje porty HTTP dla każdej usługi magazynu lokalnego.

Emulator magazynu jest instalowany domyślnie na `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Za pomocą [Eksploratora magazynu platformy Microsoft Azure](https://storageexplorer.com) można pracować z zasobami emulatora magazynu lokalnego. Poszukaj "(Emulator - Porty domyślne) (Klucz)" w obszarze "Lokalne & dołączone" w drzewie zasobów Eksploratora magazynu po zainstalowaniu i uruchomieniu emulatora magazynu.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicjowanie emulatora magazynu w celu użycia innej bazy danych SQL

Za pomocą narzędzia wiersza polecenia emulatora magazynu można zainicjować emulator magazynu w celu wskazania wystąpienia bazy danych SQL innego niż domyślne wystąpienie localdb:

1. Otwórz okno konsoli emulatora magazynu, zgodnie z opisem w [sekcji Start i zainiólować emulator magazynu.](#start-and-initialize-the-storage-emulator)
1. W oknie konsoli wpisz następujące polecenie, gdzie `<SQLServerInstance>` jest nazwa wystąpienia programu SQL Server. Aby użyć localdb, należy określić `(localdb)\MSSQLLocalDb` jako wystąpienie programu SQL Server.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Można również użyć następującego polecenia, które kieruje emulator do używania domyślnego wystąpienia programu SQL Server:

   `AzureStorageEmulator.exe init /server .`

   Można też użyć następującego polecenia, które ponownie inicjuje bazę danych do domyślnego wystąpienia LocalDB:

   `AzureStorageEmulator.exe init /forceCreate`

Aby uzyskać więcej informacji na temat tych poleceń, zobacz [Odwołanie do wiersza polecenia emulatora magazynu](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Za pomocą [programu Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) można zarządzać wystąpieniami programu SQL Server, w tym instalacją LocalDB. W oknie dialogowym **Połącz z serwerem** programu SMSS określ `(localdb)\MSSQLLocalDb` w polu Nazwa **serwera:** aby połączyć się z wystąpieniem LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Uwierzytelnianie żądań względem emulatora magazynu

Po zainstalowaniu i uruchomieniu emulatora magazynu można przetestować kod przeciwko niemu. Każde żądanie, które zostanie złożony przeciwko emulatorowi magazynu, musi być autoryzowane, chyba że jest to żądanie anonimowe. Można autoryzować żądania względem emulatora magazynu przy użyciu uwierzytelniania klucza udostępnionego lub za pomocą sygnatury dostępu współdzielonego (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autoryzowanie przy użyciu poświadczeń klucza udostępnionego

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Aby uzyskać więcej informacji na temat ciągów połączeń, zobacz [Konfigurowanie ciągów połączeń usługi Azure Storage](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autoryzacja przy za pomocą udostępnionego podpisu dostępu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Niektóre biblioteki klienta magazynu platformy Azure, takie jak biblioteka platformy Xamarin, obsługują tylko uwierzytelnianie za pomocą tokenu sygnatury dostępu współdzielonego (SAS). Token sygnatury dostępu Współdzielonego można utworzyć przy użyciu [Eksploratora magazynu](https://storageexplorer.com/) lub innej aplikacji obsługującej uwierzytelnianie klucza udostępnionego.

Można również wygenerować token sygnatury dostępu Współdzielonego przy użyciu programu Azure PowerShell. Poniższy przykład generuje token sygnatury dostępu Współdzielonego z pełnymi uprawnieniami do kontenera obiektu blob:

1. Zainstaluj program Azure PowerShell, jeśli jeszcze tego nie zrobiłeś (zaleca się użycie najnowszej wersji poleceń cmdlet programu Azure PowerShell). Aby uzyskać instrukcje instalacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).
2. Otwórz program Azure PowerShell i uruchom `CONTAINER_NAME` następujące polecenia, zastępując wybraną nazwą:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Wynikowy identyfikator URI podpisu dostępu współdzielonego dla nowego kontenera powinien być podobny do:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Podpis dostępu współdzielonego utworzony w tym przykładzie jest prawidłowy przez jeden dzień. Podpis udziela pełnego dostępu (odczyt, zapis, usuń, lista) do obiektów blob w kontenerze.

Aby uzyskać więcej informacji na temat podpisów dostępu współdzielonego, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](storage-sas-overview.md)

## <a name="addressing-resources-in-the-storage-emulator"></a>Adresowanie zasobów w emulatorze magazynu

Punkty końcowe usługi dla emulatora magazynu różnią się od punktów końcowych dla konta magazynu platformy Azure. Komputer lokalny nie wykonuje rozpoznawania nazw domen, co wymaga, aby punkty końcowe emulatora magazynu były adresami lokalnymi.

Gdy adres zasobu na koncie magazynu platformy Azure, należy użyć następującego schematu. Nazwa konta jest częścią nazwy hosta identyfikatora URI, a zaadresowany zasób jest częścią ścieżki URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Na przykład następujący identyfikator URI jest prawidłowym adresem obiektu blob na koncie magazynu platformy Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Ponieważ komputer lokalny nie wykonuje rozpoznawania nazw domen, nazwa konta jest częścią ścieżki identyfikatora URI zamiast nazwy hosta. Użyj następującego formatu identyfikatora URI dla zasobu w emulatorze magazynu:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Na przykład następujący adres może służyć do uzyskiwania dostępu do obiektu blob w emulatorze magazynu:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Punkty końcowe usługi dla emulatora magazynu są:

* Usługa obiektów blob:`http://127.0.0.1:10000/<account-name>/<resource-path>`
* Usługa kolejki:`http://127.0.0.1:10001/<account-name>/<resource-path>`
* Obsługa tabel:`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adresowanie konta pomocniczego za pomocą RA-GRS

Począwszy od wersji 3.1, emulator magazynu obsługuje replikację geograficzną dostępu do odczytu (RA-GRS). Dostęp do lokalizacji dodatkowej można uzyskać, dołączając -secondary do nazwy konta. Na przykład następujący adres może służyć do uzyskiwania dostępu do obiektu blob przy użyciu pomocniczego tylko do odczytu w emulatorze magazynu:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Aby uzyskać programowy dostęp do pomocniczego za pomocą emulatora magazynu, użyj biblioteki klienta magazynu dla platformy .NET w wersji 3.2 lub nowszej. Szczegółowe informacje można znaleźć w [bibliotece klienta usługi Microsoft Azure Storage dla platformy .NET.](https://msdn.microsoft.com/library/azure/dn261237.aspx)
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Odwołanie do wiersza polecenia emulatora magazynu

Począwszy od wersji 3.0, okno konsoli jest wyświetlany po uruchomieniu emulatora magazynu. Użyj wiersza polecenia w oknie konsoli, aby uruchomić i zatrzymać emulator. Można również wykonywać kwerendy pod kątem stanu i wykonywać inne operacje z wiersza polecenia.

> [!NOTE]
> Jeśli masz zainstalowany emulator obliczeniowy platformy Microsoft Azure, ikona zasobnika systemowego pojawi się po uruchomieniu emulatora magazynu. Kliknij prawym przyciskiem myszy ikonę, aby wyświetlić menu, które zapewnia graficzny sposób uruchamiania i zatrzymywania emulatora magazynu.
>
>

### <a name="command-line-syntax"></a>Składnia wiersza polecenia

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opcje

Aby wyświetlić listę opcji, wpisz ciąg `/help` w wierszu polecenia.

| Opcja | Opis | Polecenie | Argumenty |
| --- | --- | --- | --- |
| **Początek** |Uruchamia emulator magazynu. |`AzureStorageEmulator.exe start [-inprocess]` |*-Reprocess:* Uruchom emulator w bieżącym procesie zamiast tworzenia nowego procesu. |
| **Zatrzymaj** |Zatrzymuje emulator magazynu. |`AzureStorageEmulator.exe stop` | |
| **Stan** |Drukuje stan emulatora magazynu. |`AzureStorageEmulator.exe status` | |
| **Clear** |Czyści dane we wszystkich usługach określonych w wierszu polecenia. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*obiekt blob*: Czyści dane obiektu blob. <br/>*kolejka:* Czyści dane kolejki. <br/>*tabela*: Czyści dane tabeli. <br/>*wszystkie*: Czyści wszystkie dane we wszystkich usługach. |
| **Init** |Wykonuje jednorazowe inicjowanie w celu skonfigurowania emulatora. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: Określa serwer obsługujący wystąpienie SQL. <br/>*-sqlinstance instanceName*: Określa nazwę wystąpienia SQL, które ma być używane w domyślnym wystąpieniu serwera. <br/>*-forcecreate*: Wymusza tworzenie bazy danych SQL, nawet jeśli już istnieje. <br/>*-skipcreate*: Pomija tworzenie bazy danych SQL. Ma to pierwszeństwo przed -forcecreate.<br/>*-reserveports*: Próbuje zarezerwować porty HTTP skojarzone z usługami.<br/>*-unreserveports*: Próbuje usunąć rezerwacje dla portów HTTP skojarzonych z usługami. Ma to pierwszeństwo przed portami -reserveports.<br/>*-inprocess*: Wykonuje inicjowanie w bieżącym procesie zamiast tarło nowego procesu. Bieżący proces musi zostać uruchomiony z podwyższonym poziomem uprawnień, jeśli zmiana rezerwacji portów. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Różnice między emulatorem magazynu a usługą Azure Storage

Ponieważ emulator magazynu jest lokalnym środowiskiem emulowanym, istnieją różnice między przy użyciu emulatora i konta magazynu platformy Azure w chmurze:

* Emulator magazynu obsługuje tylko jedno konto stałe i dobrze znany klucz uwierzytelniania.
* Emulator magazynu nie jest skalowalną usługą magazynu i nie obsługuje dużej liczby równoczesnych klientów.
* Zgodnie z opisem w [adresowanie zasobów w emulatorze magazynu](#addressing-resources-in-the-storage-emulator)zasoby są adresowane inaczej w emulatorze magazynu w porównaniu do konta magazynu platformy Azure. Różnica polega na tym, że rozpoznawanie nazw domen jest dostępne w chmurze, ale nie na komputerze lokalnym.
* Począwszy od wersji 3.1, konto emulatora magazynu obsługuje replikację geograficzną dostępu do odczytu (RA-GRS). W emulatorze wszystkie konta mają włączone RA-GRS i nigdy nie ma żadnych opóźnień między replikami podstawowymi i pomocniczymi. Get Blob Service Stats, Get Queue Service Stats i Get Table Service Stats operacje są obsługiwane `LastSyncTime` na koncie pomocniczym i zawsze zwróci wartość elementu odpowiedzi jako bieżący czas zgodnie z podstawowej bazy danych SQL.
* Punkty końcowe usługi file service i SMB protocol service nie są obecnie obsługiwane w emulatorze magazynu.
* Jeśli używasz wersji usług magazynu, która nie jest obsługiwana przez emulator, emulator zwraca błąd VersionNotSupportedByEmulator (kod stanu HTTP 400 - Złe żądanie).

### <a name="differences-for-blob-storage"></a>Różnice dla magazynu obiektów Blob

Następujące różnice dotyczą magazynu obiektów Blob w emulatorze:

* Emulator magazynu obsługuje tylko rozmiary obiektów blob do 2 GB.
* Maksymalna długość nazwy obiektu blob w emulatorze magazynu wynosi 256 znaków, podczas gdy maksymalna długość nazwy obiektu blob w usłudze Azure Storage to 1024 znaków.
* Kopia przyrostowa umożliwia kopiowanie migawek z nadpisanych obiektów blob, co zwraca błąd w usłudze.
* Get Page Ranges Diff nie działa między migawkami skopiowanymi przy użyciu przyrostowego obiektu blob kopiowania.
* Put Blob operacji może zakończyć się pomyślnie względem obiektu blob, który istnieje w emulatorze magazynu z aktywnej dzierżawy, nawet jeśli identyfikator dzierżawy nie został określony w żądaniu.
* Dołącz operacje obiektów blob nie są obsługiwane przez emulator. Próba operacji na obiekcie blob dołączania zwraca błąd FeatureNotSupportedByEmulator (kod stanu HTTP 400 - Złe żądanie).

### <a name="differences-for-table-storage"></a>Różnice w przechowywaniu tabel

Następujące różnice dotyczą magazynu tabel w emulatorze:

* Właściwości daty w usłudze tabel w emulatorze magazynu obsługują tylko zakres obsługiwany przez program SQL Server 2005 (muszą być późniejsze niż 1 stycznia 1753). Wszystkie daty przed 1 stycznia 1753 są zmieniane na tę wartość. Dokładność dat jest ograniczona do dokładności programu SQL Server 2005, co oznacza, że daty są precyzyjne do 1/300 sekundy.
* Emulator magazynu obsługuje wartości właściwości klucza partycji i wiersza mniejsze niż 512 bajtów. Całkowity rozmiar nazwy konta, nazwy tabeli i nazw właściwości kluczy razem nie może przekroczyć 900 bajtów.
* Całkowity rozmiar wiersza w tabeli w emulatorze magazynu jest ograniczony do mniej niż 1 MB.
* W emulatorze magazynu właściwości typu `Edm.Guid` `Edm.Binary` danych lub `Equal (eq)` `NotEqual (ne)` obsługi tylko operatorów i porównania w ciągach filtru kwerend.

### <a name="differences-for-queue-storage"></a>Różnice w magazynie kolejek

Nie ma żadnych różnic specyficznych dla magazynu kolejki w emulatorze.

## <a name="storage-emulator-release-notes"></a>Informacje o wydaniu emulatora magazynu

### <a name="version-510"></a>Wersja 5.10

* Emulator magazynu nie odrzuci wersji 2019-07-07 usług magazynu w punktach końcowych usługi blob, queue i table service.

### <a name="version-59"></a>Wersja 5.9

* Emulator magazynu nie odrzuci wersji 2019-02-02 usług magazynu w punktach końcowych usługi blob, queue i table service.

### <a name="version-58"></a>Wersja 5.8

* Emulator magazynu nie odrzuci wersji 2018-11-09 usług magazynu w punktach końcowych usługi blob, queue i table service.

### <a name="version-57"></a>Wersja 5.7

* Naprawiono błąd, który powodował awarię, jeśli rejestrowanie było włączone.

### <a name="version-56"></a>Wersja 5.6

* Emulator magazynu obsługuje teraz wersję 2018-03-28 usług magazynu w punktach końcowych usługi blob, queue i table service.

### <a name="version-55"></a>Wersja 5.5

* Emulator magazynu obsługuje teraz wersję 2017-11-09 usług magazynu w punktach końcowych usługi blob, queue i table service.
* Obsługa została dodana dla obiektu blob **Created** właściwość, która zwraca czas tworzenia obiektu blob.

### <a name="version-54"></a>Wersja 5.4

* Aby poprawić stabilność instalacji, emulator nie próbuje już rezerwować portów w czasie instalacji. Jeśli chcesz zarezerwować port, użyj opcji *-reserveports* polecenia **init,** aby je określić.

### <a name="version-53"></a>Wersja 5.3

* Emulator magazynu obsługuje teraz wersję 2017-07-29 usług magazynu w punktach końcowych usługi blob, queue i table service.

### <a name="version-52"></a>Wersja 5.2

* Emulator magazynu obsługuje teraz wersję 2017-04-17 usług magazynu w punktach końcowych usługi blob, queue i table service.
* Naprawiono błąd, który powodował, że wartości właściwości tabeli nie były poprawnie kodowane.

### <a name="version-51"></a>Wersja 5.1

* Naprawiono błąd, który powodował, `DataServiceVersion` że emulator magazynu zwracał nagłówek w niektórych odpowiedziach, w których usługa nie była.

### <a name="version-50"></a>Wersja 5.0

* Instalator emulatora magazynu nie sprawdza już istniejących instalacji MSSQL i .NET Framework.
* Instalator emulatora magazynu nie tworzy już bazy danych w ramach instalacji. Baza danych będzie nadal tworzony w razie potrzeby w ramach uruchamiania.
* Tworzenie bazy danych nie wymaga już podniesienia uprawnień.
* Rezerwacje portów nie są już potrzebne do uruchomienia.
* Dodaje następujące opcje `init` `-reserveports` do : (wymaga podniesienia), `-unreserveports` `-skipcreate`(wymaga podniesienia), .
* Opcja Interfejsu użytkownika emulatora pamięci masowej na ikonie zasobnika systemowego uruchamia teraz interfejs wiersza polecenia. Stary interfejs użytkownika nie jest już dostępny.
* Niektóre biblioteki DLL zostały usunięte lub zmieniono ich nazwę.

### <a name="version-46"></a>Wersja 4.6

* Emulator magazynu obsługuje teraz wersję 2016-05-31 usług magazynu w punktach końcowych usługi blob, queue i table service.

### <a name="version-45"></a>Wersja 4.5

* Naprawiono błąd, który powodował niepowodzenie instalacji i inicjowania po zmianie nazwy zapasowej bazy danych.

### <a name="version-44"></a>Wersja 4.4

* Emulator magazynu obsługuje teraz wersję 2015-12-11 usług magazynu w punktach końcowych usługi blob, queue i table service.
* Wyrzucanie elementów bezużytecznych danych obiektów blob w emulatorze magazynu jest teraz bardziej wydajne w przypadku dużej liczby obiektów blob.
* Naprawiono błąd, który powodował, że plik ACL XML kontenera był sprawdzany nieco inaczej niż usługa magazynu.
* Naprawiono błąd, który czasami powodował, że wartości max i min DateTime były zgłaszane w niewłaściwej strefie czasowej.

### <a name="version-43"></a>Wersja 4.3

* Emulator magazynu obsługuje teraz wersję 2015-07-08 usług magazynu w punktach końcowych usługi blob, queue i table service.

### <a name="version-42"></a>Wersja 4.2

* Emulator magazynu obsługuje teraz wersję 2015-04-05 usług magazynu w punktach końcowych usługi blob, queue i table service.

### <a name="version-41"></a>Wersja 4.1

* Emulator magazynu obsługuje teraz wersję 2015-02-21 usług magazynu w punktach końcowych usługi blob, queue i table service. Nie obsługuje nowych funkcji dołączania obiektów blob.
* Emulator zwraca teraz znaczący komunikat o błędzie dla nieobsługiwałych wersji usług magazynu. Zalecamy użycie najnowszej wersji emulatora. Jeśli wystąpi błąd VersionNotSupportedByEmulator (kod stanu HTTP 400 - Złe żądanie), pobierz najnowszą wersję emulatora.
* Naprawiono błąd, w którym stan wyścigu powodował, że dane jednostki tabeli były nieprawidłowe podczas równoczesnych operacji scalania.

### <a name="version-40"></a>Wersja 4.0

* Nazwa pliku wykonywalnego emulatora magazynu została zmieniona na *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Wersja 3.2

* Emulator magazynu obsługuje teraz wersję 2014-02-14 usług magazynu w punktach końcowych usługi blob, queue i table service. Punkty końcowe usługi plików nie są obecnie obsługiwane w emulatorze magazynu. Zobacz [przechowywanie wersji usług usługi Azure Storage, aby](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) uzyskać szczegółowe informacje na temat wersji 2014-02-14.

### <a name="version-31"></a>Wersja 3.1

* Magazyn geograficzny dostępu do odczytu (RA-GRS) jest teraz obsługiwany w emulatorze magazynu. , `Get Blob Service Stats` `Get Queue Service Stats`i `Get Table Service Stats` interfejsy API są obsługiwane dla konta pomocniczego i zawsze zwróci wartość LastSyncTime elementu odpowiedzi jako bieżący czas zgodnie z podstawowej bazy danych SQL. Aby uzyskać programowy dostęp do pomocniczego za pomocą emulatora magazynu, użyj biblioteki klienta magazynu dla platformy .NET w wersji 3.2 lub nowszej. Szczegółowe informacje można znaleźć w bibliotece klienta usługi Microsoft Azure Storage, aby uzyskać informacje o numerze .NET.

### <a name="version-30"></a>Wersja 3.0

* Emulator magazynu platformy Azure nie jest już dostarczany w tym samym pakiecie co emulator obliczeniowy.
* Graficzny interfejs użytkownika emulatora magazynu jest przestarzały. Został on zastąpiony przez skryptowy interfejs wiersza polecenia. Aby uzyskać szczegółowe informacje na temat interfejsu wiersza polecenia, zobacz Odwołanie do narzędzia wiersza polecenia emulatora magazynu. Interfejs graficzny będzie nadal obecny w wersji 3.0, ale jest dostępny tylko wtedy, gdy emulator obliczeniowy jest zainstalowany przez kliknięcie prawym przyciskiem myszy na ikonie zasobnika systemowego i wybranie opcji Pokaż interfejs emulatora magazynu.
* Wersja 2013-08-15 usług magazynu platformy Azure jest teraz w pełni obsługiwana. (Poprzednio ta wersja była obsługiwana tylko przez emulator pamięci masowego w wersji 2.2.1 Wersja zapoznawcza).

## <a name="next-steps"></a>Następne kroki

* Oceń wieloplatformowy, obsługiwany przez społeczność emulator pamięci masowej typu open source [Azurite](https://github.com/arafato/azurite). 
* [Przykłady usługi Azure Storage przy użyciu platformy .NET](../storage-samples-dotnet.md) zawierają łącza do kilku przykładów kodu, których można użyć podczas tworzenia aplikacji.
* Za pomocą [Eksploratora usługi Microsoft Azure Storage](https://storageexplorer.com) można pracować z zasobami na koncie magazynu w chmurze i w emulatorze magazynu.
