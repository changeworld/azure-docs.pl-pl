---
title: Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą narzędzia AzCopy na Windows | Dokumentacja firmy Microsoft
description: Narzędzia AzCopy można użyć na narzędzie Windows, do przeniesienia lub skopiowania danych do lub z obiektu blob, tabeli i zawartości pliku. Kopiowanie danych do usługi Azure Storage z lokalnych plików lub kopiowania danych w ramach lub między kontami magazynu. Łatwo Migruj dane do usługi Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 01/03/2019
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 6ae3ec566c05d2460747439d61c87c995a90b19c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58881736"
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Transferowanie danych za pomocą narzędzia AzCopy w Windows
AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych z systemu Microsoft Azure Blob, File i Table storage przy użyciu prostych poleceń zaprojektowane pod kątem optymalnej wydajności. Dane można kopiować między systemem plików i kontem magazynu lub między kontami magazynu.  

Istnieją dwie wersje narzędzia AzCopy, który można pobrać. Narzędzie AzCopy w Windows oferuje Windows style opcje wiersza polecenia. [Narzędzie AzCopy w systemie Linux](storage-use-azcopy-linux.md) jest przeznaczony dla platform Linux, oferuje opcje wiersza polecenia w stylu POSIX. W tym artykule opisano narzędzia AzCopy na Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Pobierz i zainstaluj narzędzie AzCopy na Windows

### <a name="latest-version-v81"></a>Najnowsza wersja (w wersji 8.1)
Pobierz [najnowszą wersję programu AzCopy na Windows](https://aka.ms/downloadazcopy).

#### <a name="azcopy-on-windows-81-release-notes"></a>Narzędzie AzCopy w wersji Windows 8.1
- Usługa TABLE service nie jest już obsługiwana w najnowszej wersji. Jeśli używasz funkcji eksportowania tabeli, pobrać wersję 7.3 narzędzia AzCopy.
- Utworzonych za pomocą platformy .NET Core 2.1 i wszystkie zależności platformy .NET Core teraz są spakowane w instalacji.
- Dodano obsługę uwierzytelniania OAuth. Użyj ```azcopy login``` zalogowanie się za pomocą usługi Azure Active Directory.

### <a name="azcopy-with-table-support-v73"></a>Narzędzia Azcopy z obsługą tabeli (v7.3)
Pobierz [7.3 narzędzia AzCopy z obsługą tabeli](https://aka.ms/downloadazcopynet).

### <a name="post-installation-step"></a>Krok po instalacji

Po zainstalowaniu narzędzia AzCopy na za pomocą Instalatora Windows, Otwórz okno polecenia i przejdź do katalogu instalacyjnego programu AzCopy na komputerze — gdzie `AzCopy.exe` wykonywalny znajduje się. Jeśli to konieczne, można dodać lokalizacji instalacji narzędzia AzCopy do ścieżki systemowej. Domyślnie narzędzie AzCopy jest zainstalowane na `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` lub `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Pisanie pierwszego polecenia narzędzia AzCopy

Podstawowa składnia polecenia narzędzia AzCopy jest:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

W poniższych przykładach pokazano różne scenariusze dotyczące kopiowania danych do i z plików, tabel i obiektów blob systemu Azure firmy Microsoft. Zapoznaj się [parametrów narzędzia AzCopy](#azcopy-parameters) sekcji, aby uzyskać szczegółowy opis parametrów użytych w każdym przykładzie.

## <a name="download-blobs-from-blob-storage"></a>Pobierz obiekty BLOB z magazynu obiektów Blob

Spójrzmy na kilka sposobów, aby pobierać obiekty BLOB przy użyciu narzędzia AzCopy.

### <a name="download-a-single-blob"></a>Pobieranie pojedynczego obiektu blob

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Należy pamiętać, że jeśli folder `C:\myfolder` nie istnieje, narzędzie AzCopy utworzy go i pobierania `abc.txt` do nowego folderu.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Pobierz pojedynczy obiekt blob z regionu pomocniczego

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Należy pamiętać, że użytkownik musi mieć dostęp do odczytu magazynu geograficznie nadmiarowego włączone, aby uzyskać dostęp w regionie pomocniczym.

### <a name="download-all-blobs-in-a-container"></a>Pobierz wszystkie obiekty BLOB w kontenerze

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Przyjęto założenie, że następujących obiektów blob znajdują się w określonym kontenerze:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Po zakończeniu operacji pobierania katalogu `C:\myfolder` zawiera następujące pliki:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Jeśli nie określisz opcji `/S`, nie obiekty BLOB są pobierane.

### <a name="download-blobs-with-a-specific-prefix"></a>Pobieranie obiektów BLOB od określonego prefiksu

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Załóżmy, że następujących obiektów blob znajdują się w określonym kontenerze. Wszystkie obiekty BLOB rozpoczynający się od prefiksu `a` są pobierane:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Po zakończeniu operacji pobierania folderu `C:\myfolder` zawiera następujące pliki:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Prefiks mają zastosowanie do katalogu wirtualnego, który stanowi pierwszą część nazwy obiektu blob. W powyższym przykładzie katalog wirtualny nie pasuje określonego prefiksu, więc nie jest pobierana. Ponadto jeśli opcja `/S` nie zostanie określony, narzędzie AzCopy nie pobiera wszystkie obiekty BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Ustaw czas ostatniej modyfikacji wyeksportowany plik, aby była taka sama jak obiekty BLOB źródła

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Można również wykluczyć obiekty BLOB z operacji pobierania, w oparciu o czas ostatniej modyfikacji. Na przykład, jeśli chcesz wykluczyć obiekty BLOB, w których ostatni czas modyfikacji jest tą samą lub nowszą niż plik docelowy, Dodaj `/XN` opcji:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Jeśli chcesz wykluczyć obiekty BLOB, w których ostatni czas modyfikacji, to dodanie tej samej lub starszy niż plik docelowy `/XO` opcji:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Przekazywanie obiektów blob do magazynu obiektów Blob

Spójrzmy na kilka sposobów przekazywania obiektów blob za pomocą narzędzia AzCopy.

### <a name="upload-a-single-blob"></a>Przekaż jeden obiekt blob

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Jeśli określony kontener docelowy nie istnieje, narzędzie AzCopy utworzy go i przekaże do niego plik.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Przekaż jeden obiekt blob do katalogu wirtualnego

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Jeśli określony katalog wirtualny nie istnieje, narzędzie AzCopy przekazuje plik do uwzględnienia katalogu wirtualnego w jego nazwę (*np.*, `vd/abc.txt` w powyższym przykładzie).

### <a name="upload-all-blobs-in-a-folder"></a>Przekazać wszystkie obiekty BLOB w folderze

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Określenie opcji `/S` przesyła zawartość określonego katalogu do rekursywnie magazynu obiektów Blob, co oznacza, że wszystkie podfoldery i pliki również zostaną przekazane. Na przykład załóżmy następujące pliki znajdują się w folderze `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Po wykonaniu operacji przekazywania kontener zawiera następujące pliki:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Jeśli nie określisz opcji `/S`, narzędzie AzCopy nie załaduje cyklicznie. Po wykonaniu operacji przekazywania kontener zawiera następujące pliki:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Przekazywanie obiektów blob pasujące do określonego wzorca

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Załóżmy następujące pliki znajdują się w folderze `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Po wykonaniu operacji przekazywania kontener zawiera następujące pliki:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Jeśli nie określisz opcji `/S`, narzędzie AzCopy przekazuje tylko obiekty BLOB, które nie znajdują się w katalogu wirtualnego:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Określ typ zawartości MIME docelowego obiektu blob

Domyślnie narzędzie AzCopy ustawia typ zawartości do docelowego obiektu blob `application/octet-stream`. Począwszy od wersji 3.1.0 można jawnie określić typ zawartości za pomocą opcji `/SetContentType:[content-type]`. Ta składnia ustawia typ zawartości dla wszystkich obiektów blob w operacji przekazywania.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Jeśli określisz `/SetContentType` bez wartości, narzędzia AzCopy ustawia każdy obiekt blob lub typ zawartości pliku, zgodnie z jego rozszerzenie pliku.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Kopiowanie obiektów blob w magazynie obiektów Blob

Spójrzmy na kilka sposobów, aby kopiować obiekty BLOB z jednej lokalizacji do innej przy użyciu narzędzia AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Skopiuj jeden obiekt blob z jednego kontenera do drugiej w ramach tego samego konta magazynu 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Podczas kopiowania obiektu blob na koncie magazynu [kopiowania po stronie serwera](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Skopiuj jeden obiekt blob z jednego konta magazynu do innego

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Podczas kopiowania obiektu blob na kontach magazynu [kopiowania po stronie serwera](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Skopiuj jeden obiekt blob z regionu pomocniczego do regionu podstawowego

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Należy pamiętać, że użytkownik musi mieć dostęp do odczytu magazynu geograficznie nadmiarowego włączony dostęp do magazynu pomocniczego.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Skopiuj jeden obiekt blob i jego migawek z jednego konta magazynu do innego

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Po zakończeniu operacji kopiowania kontener docelowy zawiera obiekt blob i jego migawek. Przy założeniu, że obiekt blob w powyższym przykładzie ma dwa migawki, kontener zawiera następujących obiektów blob i migawki:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Skopiuj wszystkie obiekty BLOB w kontenerze do innego konta magazynu 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Określenie opcji /S służy do przekazywania zawartości rekursywnie określonego kontenera. Zobacz [przekazać wszystkie obiekty BLOB w folderze](#upload-all-blobs-in-a-folder) uzyskać więcej informacji i obejrzeć przykład.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Synchronicznie kopiować obiekty BLOB z jednego konta magazynu do innego

Narzędzie AzCopy domyślnie kopiuje dane między dwoma punktami końcowymi magazynu asynchronicznie. W związku z tym operacja kopiowania działa w tle za pomocą dyspozycyjność nieużywanej przepustowości, która została objęta umową SLA pod kątem jak szybko obiekt blob jest kopiowana i narzędzia AzCopy okresowo sprawdza stan kopiowania do momentu Kopiowanie ukończone lub nie powiodło się.

`/SyncCopy` Opcja gwarantuje, że operacji kopiowania pobiera szybkość spójne. Narzędzie AzCopy wykonuje synchroniczne kopiowania pobieranie obiektów blob, aby skopiować pochodzących z określonego źródła do pamięci lokalnej, a następnie przekazaniu ich do docelowego magazynu obiektów Blob.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` może generować koszty dodatkowe ruchu wychodzącego w porównaniu z asynchronicznego kopiowania, zalecanym podejściem jest użycie tej opcji w maszynie Wirtualnej platformy Azure, który znajduje się w tym samym regionie, co źródłowego konta magazynu w celu uniknięcia koszty ruchu wychodzącego.

## <a name="download-files-from-file-storage"></a>Pobierz pliki z usługi File storage

Spójrzmy na kilka sposobów pobierania plików przy użyciu narzędzia AzCopy.

### <a name="download-a-single-file"></a>Pobierz jeden plik

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Jeśli określone źródło udziału plików platformy Azure, wówczas należy albo określić dokładnej nazwy pliku, (*np.* `abc.txt`) do pobrania jednego pliku lub wybierz opcję `/S` można pobrać wszystkich plików w lokalizacji udziału. Podjęto próbę Określ wzorzec pliku i opcji `/S` razem będzie skutkowało błędem.

### <a name="download-all-files-in-a-directory"></a>Pobierz wszystkie pliki w katalogu

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Należy zauważyć, że puste foldery nie zostaną pobrane.

## <a name="upload-files-to-an-azure-file-share"></a>Przekazywanie plików do udziału plików platformy Azure

Spójrzmy na kilka sposobów na przekazywanie plików za pomocą narzędzia AzCopy.

### <a name="upload-a-single-file"></a>Przekazać jeden plik

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Przekazywanie wszystkich plików w folderze

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Należy zauważyć, że puste foldery nie są przekazywane.

### <a name="upload-files-matching-a-specific-pattern"></a>Przekaż pliki pasujące do określonego wzorca

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Skopiuj pliki w usłudze File storage

Spójrzmy na kilka sposobów, aby skopiować pliki w udziale plików platformy Azure przy użyciu narzędzia AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Kopiowanie z jednego udziału plików do innego

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Podczas kopiowania plików między udziałami plików [kopiowania po stronie serwera](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Kopiowanie z udziału plików platformy Azure do magazynu obiektów Blob

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Podczas kopiowania pliku z udziału plików do obiektów blob, [kopiowania po stronie serwera](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Kopiowanie obiektu blob z magazynu obiektów Blob do udziału plików platformy Azure

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Podczas kopiowania pliku z obiektu blob do udziału plików [kopiowania po stronie serwera](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="synchronously-copy-files"></a>Synchronicznie kopiowanie plików

Można określić `/SyncCopy` opcji, aby skopiować dane z usługi File Storage do usługi File Storage z usługi File Storage do usługi Blob Storage i z usługi Blob Storage do przechowywania plików synchronicznie, narzędzie AzCopy to realizowane przez pobranie danych źródłowych do pamięci lokalnej i przekaż go ponownie do miejsce docelowe. Wyjście standardowe są koszty.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Podczas kopiowania z usługi File storage do magazynu obiektów Blob, domyślnym typem obiektów blob jest blokowy obiekt blob; Użytkownik może określić opcję `/BlobType:page` zmienić docelowy typ obiektu blob.

Należy pamiętać, że `/SyncCopy` może wygenerować dodatkowy ruch wychodzący koszty w porównaniu do asynchronicznego kopiowania. Zalecanym podejściem jest użycie tej opcji w maszynie Wirtualnej platformy Azure, który znajduje się w tym samym regionie, co źródłowego konta magazynu w celu uniknięcia koszty ruchu wychodzącego.

## <a name="export-data-from-table-storage"></a>Eksportowanie danych z usługi Table storage

Spójrzmy na eksportowanie danych z usługi Azure Table storage przy użyciu narzędzia AzCopy.

### <a name="export-a-table"></a>Eksportowanie tabeli

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

Narzędzie AzCopy zapisuje plik manifestu do folderu określonego docelowego. Plik manifestu jest używany w procesie importu do lokalizowania plików niezbędnych danych i wykonywania sprawdzania poprawności danych. Domyślnie plik manifestu używa następującej konwencji nazewnictwa:

    <account name>_<table name>_<timestamp>.manifest

Użytkownik może również określić opcję `/Manifest:<manifest file name>` można ustawić nazwy pliku manifestu.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Podziel eksportu z usługi Table storage na wiele plików

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

Korzysta z narzędzia AzCopy *indeksu woluminu* w nazwach plików danych podziału w celu rozróżnienia wielu plików. Indeks woluminu, który składa się z dwóch części *indeks zakres kluczy partycji* i *podziału plik indeksu*. Zarówno indeksów zaczynają się od zera.

Indeks zakres kluczy partycji wynosi 0, jeśli użytkownik nie określono opcji `/PKRS`.

Na przykład załóżmy, że narzędzie AzCopy generuje dwa pliki danych po użytkownik Określa opcję `/SplitSize`. Wynikowy nazwy pliku danych może być:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Należy zauważyć, że minimalna możliwa wartość dla opcji `/SplitSize` wynosi 32 MB. Jeśli określone miejsce docelowe jest magazynu obiektów Blob, narzędzia AzCopy dzieli pliku danych, po jego rozmiary osiągnie limit rozmiaru obiektów blob (200GB), niezależnie od tego, czy opcja `/SplitSize` został określony przez użytkownika.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Eksportowanie tabeli z formatem pliku danych JSON lub CSV

Domyślnie narzędzie AzCopy eksportuje tabel do plików danych JSON. Można określić opcję `/PayloadFormat:JSON|CSV` Eksportowanie tabel jako JSON lub CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

Podczas określania ładunku w formacie CSV, narzędzia AzCopy również wygenerowanie pliku schematu z rozszerzeniem pliku `.schema.csv` dla każdego pliku danych.

### <a name="export-table-entities-concurrently"></a>Eksportowanie tabeli jednocześnie jednostek

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

Narzędzie AzCopy uruchamia jednoczesnych operacji eksportowania jednostki po użytkownik Określa opcję `/PKRS`. Każda operacja Eksportuje jeden zakres kluczy partycji.

Należy pamiętać, że liczby operacji jednoczesnych również jest kontrolowane przez opcję `/NC`. Narzędzie AzCopy używa liczba rdzeni procesorów jako wartość domyślną `/NC` podczas kopiowania jednostki z tabeli, nawet wtedy, gdy `/NC` nie został określony. Kiedy użytkownik Określa opcję `/PKRS`, narzędzia AzCopy korzysta z mniejszą z dwóch wartości — zakresów kluczy partycji i niejawnie lub jawnie określony współbieżnych operacji — do określenia liczby jednoczesnych operacji, aby rozpocząć. Aby uzyskać więcej informacji, wpisz `AzCopy /?:NC` w wierszu polecenia.

### <a name="export-a-table-to-blob-storage"></a>Eksportowanie tabeli do magazynu obiektów Blob

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

Narzędzie AzCopy generuje plik danych JSON do kontenera obiektów blob z następującą konwencją nazewnictwa:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Wygenerowany plik JSON w danych następuje format ładunku minimalne metadane. Szczegółowe informacje na temat tego formatu ładunek, [Format ładunku dla operacji usługi tabeli](https://msdn.microsoft.com/library/azure/dn535600.aspx).

Należy pamiętać, że podczas eksportowania tabele, obiekty BLOB, narzędzia AzCopy pliki do pobrania jednostki z tabeli do lokalnych danych tymczasowych plików, a następnie przekazuje te jednostki do obiektu blob. Te pliki tymczasowe dane są umieszczane w folder plików dziennika przy użyciu domyślnej ścieżki "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", można określić opcję/Z: [dziennika pliku folder] do dziennika zmian lokalizacja folderu plików i dlatego Zmień lokalizację plików danych tymczasowych. Dane tymczasowe, których rozmiar plików, decydują rozmiar jednostek tabeli i rozmiar, określony /SplitSize opcji, mimo, że plik danych tymczasowych na dysku lokalnym jest usuwany natychmiast po został przekazany do obiektu blob, upewnij się, że masz wystarczająco lokalne Miejsce na dysku do przechowywania plików danych tymczasowych, zanim zostaną one usunięte.

## <a name="import-data-into-table-storage"></a>Importowanie danych do usługi Table storage

Spójrzmy na importowanie danych do usługi Azure Table storage przy użyciu narzędzia AzCopy.

### <a name="import-a-table"></a>Importuj tabelę

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

Opcja `/EntityOperation` wskazuje sposób wstawiania jednostki do tabeli. Możliwe wartości:

* `InsertOrSkip`: Pomija istniejącej jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.
* `InsertOrMerge`: Scala istniejącej jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.
* `InsertOrReplace`: Zastępuje istniejące jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.

Zauważ, że nie można określić opcji `/PKRS` w scenariuszu importu. W odróżnieniu od scenariusza eksportu, w którym należy określić opcję `/PKRS` można uruchomić operacji jednoczesnych, narzędzia AzCopy jest uruchamiany jednoczesnych operacji domyślnie podczas importowania tabeli. Domyślna liczba jednoczesnych operacji pracy jest równa liczbie procesorów; jednak można określić różne liczby równoczesnych z opcją `/NC`. Aby uzyskać więcej informacji, wpisz `AzCopy /?:NC` w wierszu polecenia.

Należy pamiętać, że narzędzie AzCopy obsługuje tylko importowania dla formatu JSON, a nie CSV. Narzędzie AzCopy nie obsługuje importu tabeli z utworzonych przez użytkownika do JSON i pliki manifestu. Oba te pliki muszą pochodzić z eksportową tabeli narzędzia AzCopy. Aby uniknąć błędów, nie zmodyfikuj wyeksportowanego za pomocą pliku JSON lub plik manifestu.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Zaimportować jednostki do tabeli z magazynu obiektów Blob

Przyjęto założenie, że kontener obiektów Blob zawiera następujące elementy: Plik w formacie JSON reprezentująca tabeli platformy Azure i jej towarzyszącego pliku manifestu.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Można uruchomić następujące polecenie, aby zaimportować jednostki do tabeli za pomocą pliku manifestu w tym kontenerze obiektów blob:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Inne funkcje programu AzCopy

Spójrzmy na niektóre funkcje narzędzia AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Kopiuj tylko te dane, które nie istnieje w miejscu docelowym

`/XO` i `/XN` parametry umożliwiają Wyklucz zasoby źródłowe starszego lub nowszego kopiowaniu, odpowiednio. Jeśli chcesz skopiować zasoby źródłowe, które nie istnieją w miejscu docelowym, można określić obu parametrów w poleceniu narzędzia AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Należy pamiętać, że to nie jest obsługiwany podczas źródłowym lub docelowym jest tabelą.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Użyj pliku odpowiedzi, aby określić parametry wiersza polecenia

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

Parametry wiersza polecenia narzędzia AzCopy można uwzględnić w pliku odpowiedzi. Narzędzie AzCopy przetwarza parametry w pliku tak, jakby były one określone w wierszu polecenia wykonywania bezpośrednie podstawianie przy użyciu zawartości pliku.

Przyjęto założenie, plik odpowiedzi, o nazwie `copyoperation.txt`, który zawiera następujące wiersze. Każdy parametr narzędzia AzCopy można określić w jednym wierszu

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

lub w postaci odrębnych, wiersze:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

Narzędzie AzCopy nie działa, jeśli parametr można podzielić na dwa wiersze, jak pokazano poniżej, aby uzyskać `/sourcekey` parametru:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Użyj wielu plików odpowiedzi, aby określić parametry wiersza polecenia

Przyjęto założenie, plik odpowiedzi, o nazwie `source.txt` kontenera źródłowego, który określa:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

I plik odpowiedzi, o nazwie `dest.txt` określający folder docelowy w systemie plików:

    /Dest:C:\myfolder

I plik odpowiedzi, o nazwie `options.txt` , który określa opcje dla narzędzia AzCopy:

    /S /Y

Aby wywołać narzędzia AzCopy z tymi plikami odpowiedzi, które znajdują się w katalogu `C:\responsefiles`, użyj tego polecenia:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

Narzędzie AzCopy przetwarza tego polecenia, tak jak w przypadku dołączenia wszystkich poszczególnych parametrów w wierszu polecenia:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Określ sygnatury dostępu współdzielonego (SAS)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

Można również określić sygnatury dostępu Współdzielonego dla kontenera identyfikatora URI:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Folder plików dziennika

Każdorazowo, gdy wydać polecenie do narzędzia AzCopy, sprawdza czy istnieje plik dziennika w folderze domyślnym, lub czy istnieje w folderze, który określono za pomocą tej opcji. Jeśli plik dziennika nie istnieje w dowolnym miejscu, narzędzia AzCopy traktuje operacji jako nowe i generuje nowy plik dziennika.

Jeśli plik dziennika istnieje, narzędzie AzCopy sprawdza, czy wiersza polecenia, które należy wprowadzić pasuje do wiersza polecenia w pliku dziennika. Jeśli dwa wiersze poleceń są zgodne, narzędzia AzCopy Wznawia Nieukończona operacja. Jeśli nie są zgodne, monit albo zastąpienia pliku dziennika, można uruchomić nowej operacji lub Anuluj bieżącą operację.

Jeśli chcesz użyć domyślnej lokalizacji pliku dziennika:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Jeżeli pominięto opcję `/Z`, lub wybierz opcję `/Z` bez ścieżki folderu, jak pokazano powyżej, narzędzia AzCopy tworzy plik dziennika w lokalizacji domyślnej, która jest `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Jeśli plik dziennika już istnieje, narzędzia AzCopy wznawia działanie na podstawie pliku dziennika.

Jeśli chcesz określić niestandardową lokalizację pliku dziennika:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Ten przykład tworzy plik dziennika, jeśli jeszcze nie istnieje. Jeśli istnieje, narzędzie AzCopy wznawia działanie na podstawie pliku dziennika.

Jeśli chcesz wznowić działanie narzędzia AzCopy:

```azcopy
AzCopy /Z:C:\journalfolder\
```

W tym przykładzie wznawia ostatniej operacji, która nie może ukończyć.

### <a name="generate-a-log-file"></a>Generowanie pliku dziennika

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

W przypadku określenia opcji `/V` bez podawania ścieżki pliku do pełnego dziennika, narzędzie AzCopy utworzy plik dziennika w lokalizacji domyślnej, która jest `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

W przeciwnym razie można utworzyć pliku dziennika w lokalizacji niestandardowej:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Należy pamiętać, że w przypadku określenia ścieżki względnej, zgodnie z opcją `/V`, takich jak `/V:test/azcopy1.log`, a następnie pełny dziennik jest tworzony w bieżącym katalogu roboczym w podfolderze o nazwie `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Określ liczbę jednoczesnych operacji, aby rozpocząć

Opcja `/NC` określa liczby operacji jednoczesnych kopii. Domyślnie narzędzie AzCopy uruchamia niektórych liczby operacji jednoczesnych, aby zwiększyć przepływność transferu danych. Dla operacji tabeli liczby operacji jednoczesnych jest równa liczbie procesorów, które masz. Operacje obiektów Blob i plików, liczby operacji jednoczesnych jest równa 8 razy liczba procesorów, których masz. Jeśli używasz narzędzia AzCopy za pośrednictwem sieci o niskiej przepustowości, można określić mniejszą liczbę dla /NC uniknąć błąd spowodowany konkurencji zasobów.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Uruchamiać narzędzia AzCopy dla emulatora usługi Azure Storage

Możesz uruchomić narzędzie AzCopy względem [emulatora usługi Azure Storage](storage-use-emulator.md) dla obiektów blob:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Można również uruchomić dla tabel:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

### <a name="automatically-determine-content-type-of-a-blob"></a>Automatycznie Określ typ zawartości obiektu Blob

Narzędzie AzCopy Określa typ zawartości obiektu blob, w oparciu o plik w formacie JSON, który przechowuje, typ zawartości do mapowanie rozszerzenia pliku. Ten plik JSON o nazwie AzCopyConfig.json i znajduje się w katalogu narzędzia AzCopy. Jeśli typ pliku, który nie jest na liście mapowania można dołączyć do pliku JSON:

```
{
  "MIMETypeMapping": {
    ".myext": "text/mycustomtype",
    .
    .
  }
}
```     

## <a name="azcopy-parameters"></a>Parametry narzędzia AzCopy

Poniżej opisano parametry dla narzędzia AzCopy. Można także wpisać jedną z następujących poleceń z poziomu wiersza polecenia, aby uzyskać pomoc przy użyciu narzędzia AzCopy:

* Aby uzyskać szczegółową pomoc wiersza polecenia narzędzia AzCopy: `AzCopy /?`
* Aby uzyskać szczegółową pomoc dotyczącą żadnych parametrów narzędzia AzCopy: `AzCopy /?:SourceKey`
* Aby uzyskać przykłady wiersza polecenia: `AzCopy /?:Sample`

### <a name="sourcesource"></a>/Source:"source"

Określa dane źródłowe do skopiowania. Źródło może być katalogu w systemie plików, kontener obiektów blob, katalog wirtualny obiektów blob, udział pliku magazynu zostanie, katalog pliku magazynu lub tabelę platformy Azure.

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="destdestination"></a>/Dest:"destination"

Określa miejsce docelowe, aby skopiować. Miejsce docelowe może być katalogu w systemie plików, kontener obiektów blob, katalog wirtualny obiektów blob, udział pliku magazynu, katalog pliku magazynu lub tabelę platformy Azure.

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="patternfile-pattern"></a>/ Wzorzec: "wzorzec pliku"

Określa wzorzec pliku, która wskazuje, które pliki do skopiowania. Zachowanie parametr /Pattern zależy od lokalizacji źródła danych, a obecność opcja Tryb cykliczne. Tryb cyklicznego jest określony za pomocą opcji/s. — opcja

Jeśli określona źródłowa jest katalogiem w systemie plików, a następnie obowiązują standardowe symbole wieloznaczne i podać wzorzec pliku jest dopasowywana do plików w katalogu. Jeśli opcja określeniu, następnie AzCopy pasuje również określony wzorzec względem wszystkich plików w wszystkie podfoldery znajdujące się poniżej katalogu.

Jeśli określona źródłowa jest kontener obiektów blob lub katalogu wirtualnego, symbole wieloznaczne nie są stosowane. Jeśli opcja /S jest określona, narzędzie AzCopy następnie interpretuje wzorca określonego pliku jako prefiks obiektu blob. Jeśli opcja /S nie zostanie określony, narzędzie AzCopy następnie pasuje wzorzec pliku względem nazwy dokładnie obiektów blob.

Jeśli określona źródłowa jest udział plików platformy Azure, a następnie możesz określić dokładną nazwę pliku, (np. abc.txt) do skopiowania pojedynczy plik, lub wybierz opcję /S do skopiowania wszystkich plików w lokalizacji udziału. Podjęto określić plik wzorzec i opcja /S razem wyników zarówno w błąd.

Narzędzie AzCopy używa uwzględniana wielkość liter dopasowanie / Source jest kontener obiektów blob lub katalog wirtualny obiektów blob, gdy używa dopasowywania bez uwzględniania wielkości liter we wszystkich innych przypadkach.

Domyślny wzorzec pliku używany, gdy nie określono żadnych wzorzec pliku to *.* dla lokalizacji systemu plików lub pustego prefiksu dla lokalizacji usługi Azure Storage. Określanie wielu wzorców pliku nie jest obsługiwane.

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="destkeystorage-key"></a>/DestKey:"storage-key"

Określa klucz konta magazynu dla zasobu docelowego.

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="destsassas-token"></a>/DestSAS:"sas-token"

Określa sygnatury dostępu współdzielonego (SAS) uprawnienia do odczytu i zapisu dla miejsca docelowego (jeśli dotyczy). Sygnatury dostępu Współdzielonego przy użyciu podwójnych cudzysłowów, należy ująć, jak może zawiera znaki specjalne wiersza polecenia.

Jeśli zasób docelowy jest kontener obiektów blob, udziału plików lub tabeli, można określić tę opcję, następuje token sygnatury dostępu Współdzielonego lub sygnatury dostępu Współdzielonego można określić jako element docelowy kontener obiektów blob, udziału plików lub tabeli identyfikatora URI, bez tej opcji.

Jeśli źródłowy i docelowy są oba obiekty BLOB, docelowego obiektu blob musi znajdować się w obrębie tego samego konta magazynu jako źródłowy obiekt blob.

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="sourcekeystorage-key"></a>/SourceKey:"storage-key"

Określa klucz konta magazynu dla zasobu źródła.

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="sourcesassas-token"></a>/SourceSAS:"sas-token"

Określa sygnaturę dostępu współdzielonego z uprawnieniami do odczytu i listy dla źródła, (jeśli dotyczy). Sygnatury dostępu Współdzielonego przy użyciu podwójnych cudzysłowów, należy ująć, jak może zawiera znaki specjalne wiersza polecenia.

Jeśli zasób źródła jest kontener obiektów blob, a podano klucz ani sygnatury dostępu Współdzielonego, za pośrednictwem dostępu anonimowego jest odczytu kontenera obiektów blob.

Jeśli źródło jest udział plików lub tabeli, należy podać klucz lub sygnatury dostępu Współdzielonego.

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="s"></a>/S

Określa tryb cyklicznego dla operacji kopiowania. W trybie cykliczne narzędzia AzCopy kopiuje wszystkie obiekty BLOB i plików pasujących do wzorca określonego pliku, włączając te w podfolderach.

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="blobtypeblock--page--append"></a>/BlobType:"block" | "page" | "append"

Określa, czy docelowy obiekt blob jest blokowy obiekt blob, stronicowych obiektów blob lub uzupełnialnego obiektu blob. Ta opcja ma zastosowanie tylko wtedy, gdy podczas przekazywania obiektu blob. W przeciwnym razie zostanie wygenerowany błąd. Jeśli obiektem docelowym jest obiekt blob, a ta opcja nie jest określona, domyślnie narzędzie AzCopy utworzy blokowych obiektów blob.

**Ma zastosowanie do:** Obiekty blob

### <a name="checkmd5"></a>/CheckMD5

Oblicza Skrót MD5 dla pobranych danych i weryfikuje, czy skrót MD5 jest przechowywana w obiekcie blob lub właściwość Content-MD5 pliku odpowiada skrót obliczony. Jeśli wartości nie są zgodne, narzędzia AzCopy zakończy się niepowodzeniem pobrać dane. Wyboru MD5 jest domyślnie wyłączona, dlatego należy określić tę opcję, aby sprawdzić MD5 podczas pobierania danych.

Pamiętaj, że usługi Azure Storage nie gwarantuje, że Skrót MD5 przechowywane dla obiektów blob lub pliku są aktualne. Odpowiada klienta można zaktualizować Skrót MD5, zawsze wtedy, gdy obiekt blob lub plik jest modyfikowany. W przypadku obrazów dysków (dysków zarządzanych lub niezarządzanych) maszyn wirtualnych platformy Azure nie są uaktualniane wartość MD5, jak zmiana zawartości dysku, dlatego /CheckMD5 zgłosi błąd podczas pobierania obrazów dysku.

Narzędzie AzCopy v8 zawsze ustawia właściwość Content-MD5 dla pliku lub obiektów blob platformy Azure po przekazaniu go do usługi.  

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="snapshot"></a>/Snapshot

Wskazuje, czy należy przesłać migawki. Ta opcja jest prawidłowy tylko w przypadku, gdy źródłem jest obiekt blob.

Migawki przeniesionych obiektów blob zostały zmienione w następującym formacie: .extension blob-name (migawka time)

Domyślnie migawek nie są kopiowane.

**Ma zastosowanie do:** Obiekty blob

### <a name="vverbose-log-file"></a>/ V: [plik pełnego dziennika]

Komunikaty o stanie pełne dane wyjściowe do pliku dziennika.

Domyślnie plik dziennika pełne nosi nazwę AzCopyVerbose.log w `%LocalAppData%\Microsoft\Azure\AzCopy`. Jeśli określisz istniejącej lokalizacji plików dla tej opcji, pełny dziennik jest dołączany do tego pliku.  

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="zjournal-file-folder"></a>/Z:[journal-file-folder]

Określa folder plików dziennika, w przypadku wznawiania operację.

Narzędzie AzCopy zawsze obsługuje wznawiania, jeśli operacja została przerwana.

Jeśli ta opcja nie jest określona lub jest określona bez ścieżki folderu, narzędzie AzCopy utworzy plik dziennika w lokalizacji domyślnej, która jest % LocalAppData%\Microsoft\Azure\AzCopy.

Każdorazowo, gdy wydać polecenie do narzędzia AzCopy, sprawdza czy istnieje plik dziennika w folderze domyślnym, lub czy istnieje w folderze, który określono za pomocą tej opcji. Jeśli plik dziennika nie istnieje w dowolnym miejscu, narzędzia AzCopy traktuje operacji jako nowe i generuje nowy plik dziennika.

Jeśli plik dziennika istnieje, narzędzie AzCopy sprawdza, czy wiersza polecenia, które należy wprowadzić pasuje do wiersza polecenia w pliku dziennika. Jeśli dwa wiersze poleceń są zgodne, narzędzia AzCopy Wznawia Nieukończona operacja. Jeśli nie są zgodne, monit albo zastąpienia pliku dziennika, można uruchomić nowej operacji lub Anuluj bieżącą operację.

Plik dziennika jest usuwany po pomyślnym zakończeniu operacji.

Należy pamiętać, że wznawia działanie z pliku dziennika utworzonego przez poprzednią wersję programu AzCopy nie jest obsługiwany.

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="parameter-file"></a>/@:"parameter-file"

Określa plik, który zawiera parametry. Narzędzie AzCopy przetwarza parametry w pliku tak, jakby były one określone w wierszu polecenia.

W pliku odpowiedzi można określić wiele parametrów w pojedynczym wierszu lub określ każdego parametru w osobnym wierszu. Należy pamiętać, że poszczególnych parametrów nie może obejmować wiele wierszy.

Pliki odpowiedzi może zawierać wiersze komentarzy, które zaczynają się od symbolu #.

Można określić wiele plików odpowiedzi. Jednak należy pamiętać, że narzędzie AzCopy nie obsługuje zagnieżdżone pliki odpowiedzi.

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="y"></a>/Y

Pomija wszystkie monity o potwierdzenie narzędzia AzCopy. Ta opcja umożliwia również korzystanie z tokenów sygnatur dostępu Współdzielonego tylko do zapisu dla scenariuszy przekazywania danych, gdy nie określono /XO i /XN.

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="l"></a>/L

Określa operację listy. Brak danych jest kopiowany.

Narzędzia AzCopy interpretuje korzystanie z tej opcji jako symulacji w zakresie uruchamiania wiersza polecenia, bez stosowania tej opcji/l i liczby, ile obiekty są kopiowane, można określić opcję /V w tym samym czasie, aby sprawdzić, które obiekty są kopiowane w pełny dziennik.

Lokalizacja źródła danych i występowania trybu opcja /S i plik wzorzec opcją cykliczną /Pattern zależy również zachowanie tej opcji.

Narzędzie AzCopy wymaga listy i uprawnienia do odczytu tej lokalizacji źródłowej, przy użyciu tej opcji.

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="mt"></a>/MT

Ustawia czas ostatniej modyfikacji pobrany plik być taki sam jak źródłowy obiekt blob lub pliku.

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="xn"></a>/XN

Wyklucza nowsze zasobów źródła. Zasób nie jest kopiowany, jeśli godzina ostatniej modyfikacji źródła jest taka sama lub nowsza niż docelowy.

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="xo"></a>/XO
Wyklucza starszych zasobów źródła. Zasób nie jest kopiowany, jeśli godzina ostatniej modyfikacji źródła jest taka sama lub starsze niż docelowy.

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="a"></a>/A

Służy do przekazywania tylko pliki, które mają ustawiony atrybut archiwum.

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="iarashcnetoi"></a>/IA:[RASHCNETOI]

Służy do przekazywania tylko pliki, które zawierają dowolne spośród zestaw określonych atrybutów.

Dostępne atrybuty obejmują:

* R = plików tylko do odczytu
* = Pliki gotowe do archiwizacji
* S pliki systemowe =
* H = ukryte pliki
* C = pliki skompresowane
* N = normalne pliki
* E = zaszyfrowane pliki
* T = pliki tymczasowe
* O = pliki trybu Offline
* Czy mogę = Non indeksowane pliki

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]

Nie obejmuje pliki, które zawierają dowolne spośród zestaw określonych atrybutów.

Dostępne atrybuty obejmują:

* R = plików tylko do odczytu
* = Pliki gotowe do archiwizacji
* S pliki systemowe =
* H = ukryte pliki
* C = pliki skompresowane
* N = normalne pliki
* E = zaszyfrowane pliki
* T = pliki tymczasowe
* O = pliki trybu Offline
* Czy mogę = Non indeksowane pliki

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="delimiterdelimiter"></a>/Delimiter:"delimiter"

Określa znak ogranicznika używany do ograniczania katalogów wirtualnych w nazwie obiektu blob.

Domyślnie używa narzędzia AzCopy / jako znak ogranicznika. Narzędzie AzCopy obsługuje jednak przy użyciu dowolnego znaku wspólnych (takich jak @, #, lub %) jako ogranicznika. Jeśli potrzebujesz uwzględnić jedną z następujących znaków specjalnych w wierszu polecenia, należy ująć nazwę pliku znakami podwójnego cudzysłowu.

Ta opcja dotyczy tylko do pobierania obiektów blob.

**Ma zastosowanie do:** Obiekty blob

### <a name="ncnumber-of-concurrent-operations"></a>/ NC: "numer z współbieżnych — operacje"

Określa liczbę jednoczesnych operacji.

Narzędzie AzCopy domyślnie uruchamia niektórych liczby operacji jednoczesnych, aby zwiększyć przepływność transferu danych. Pamiętaj, że dużej liczby równoczesnych operacji w środowisku o niskiej przepustowości mogą przeciąży połączenia sieciowego zapobiec operacje pełni ukończenie. Ograniczenie przepustowości jednoczesnych operacji w oparciu o rzeczywiste dostępnej przepustowości sieci.

Górny limit współbieżnych operacji to 512.

**Ma zastosowanie do:** Obiekty BLOB, pliki, tabele

### <a name="sourcetypeblob--table"></a>/SourceType:"Blob" | "Table"

Określa, że `source` zasób jest dostępny w środowisku programistycznym lokalne uruchamianie w emulatorze magazynu obiektów blob.

**Ma zastosowanie do:** Obiekty BLOB, tabel

### <a name="desttypeblob--table"></a>/DestType:"Blob" | "Table"

Określa, że `destination` zasób jest dostępny w środowisku programistycznym lokalne uruchamianie w emulatorze magazynu obiektów blob.

**Ma zastosowanie do:** Obiekty BLOB, tabel

### <a name="pkrskey1key2key3"></a>/PKRS:"key1#key2#key3#..."

Dzieli zakres kluczy partycji, aby włączyć eksportowanie danych tabeli w sposób równoległy, co zwiększa szybkość operacji eksportowania.

Jeśli ta opcja nie jest określona, następnie AzCopy używa jednego wątku do wyeksportowania jednostki z tabeli. Na przykład, jeśli użytkownik określi /PKRS: "aa #bb", a następnie AzCopy uruchamia trzy operacji jednoczesnych.

Każda operacja eksportuje jednego z trzech zakresów kluczy partycji, jak pokazano poniżej:

  [klucza partycji pierwszy, aa)

  [aa, bb)

  [bb klucza partycji ostatni]

**Ma zastosowanie do:** Tabele

### <a name="splitsizefile-size"></a>/SplitSize:"file-size"

Określa wyeksportowany plik podziału rozmiar w Megabajtach, minimalnym dozwolona wartość to 32.

Jeśli ta opcja nie jest określona, narzędzie AzCopy eksportuje dane tabeli do pojedynczego pliku.

Jeśli dane w tabeli są eksportowane do obiektu blob, a rozmiar wyeksportowany plik osiągnie limit 200 GB rozmiar obiektu blob, następnie AzCopy dzieli wyeksportowany plik, nawet jeśli ta opcja nie jest określona.

**Ma zastosowanie do:** Tabele

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Określa zachowanie importowanie danych tabeli.

* InsertOrSkip - pomija istniejącej jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.
* InsertOrMerge - scala istniejącej jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.
* InsertOrReplace — zastępuje istniejącej jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.

**Ma zastosowanie do:** Tabele

### <a name="manifestmanifest-file"></a>/Manifest:"manifest-file"

Określa plik manifestu dla tabeli, eksportowanie i importowanie operacji.

Ta opcja jest opcjonalny podczas operacji eksportowania, narzędzia AzCopy generuje plik manifestu o nazwie wstępnie zdefiniowanych, jeśli ta opcja nie jest określona.

Ta opcja jest wymagana podczas operacji importowania do lokalizowania plików danych.

**Ma zastosowanie do:** Tabele

### <a name="synccopy"></a>/SyncCopy

Wskazuje, czy synchronicznie kopiować obiekty BLOB i plików między dwoma punktami końcowymi usługi Azure Storage.

Narzędzie AzCopy domyślnie używa kopii asynchronicznych po stronie serwera. Określ tę opcję, aby wykonać synchroniczne kopia, która pobiera obiekty BLOB i plików do pamięci lokalnej, a następnie przekazuje je do usługi Azure Storage.

Można użyć tej opcji, podczas kopiowania plików w ramach magazynu obiektów Blob, w ramach usługi File storage oraz z usługi Blob storage do przechowywania plików lub na odwrót.

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="setcontenttypecontent-type"></a>/SetContentType:"content-type"

Określa typ zawartości MIME dla obiektów blob w docelowym lub plików.

Domyślnie narzędzie AzCopy ustawia typ zawartości do obiektu blob lub pliku application/octet-stream. Można ustawić typu zawartości dla wszystkich obiektów blob lub pliki, jawnie określając wartość dla tej opcji.

Wybranie tej opcji bez wartości AzCopy ustawia każdy obiekt blob lub typ zawartości pliku, zgodnie z jego rozszerzenie pliku.

**Ma zastosowanie do:** Obiekty BLOB, pliki

### <a name="payloadformatjson--csv"></a>/PayloadFormat:"JSON" | "CSV"

Określa format pliku wyeksportowanych danych tabeli.

Jeśli ta opcja nie jest określona, narzędzie AzCopy domyślnie eksportuje plik danych tabeli w formacie JSON.

**Ma zastosowanie do:** Tabele

## <a name="known-issues-and-best-practices"></a>Znane problemy i najlepsze rozwiązania

Spójrzmy na kilka znanych problemów i najlepsze rozwiązania.

### <a name="limit-concurrent-writes-while-copying-data"></a>Limit współbieżne operacje podczas kopiowania danych

Podczas kopiowania obiektów blob lub plików za pomocą narzędzia AzCopy, należy pamiętać o tym, że inna aplikacja może być modyfikowania danych podczas kopiowania go. Jeśli to możliwe upewnij się, że dane, które są kopiowane nie jest modyfikowana podczas operacji kopiowania. Na przykład podczas kopiowania wirtualnego dysku twardego skojarzonego z maszyną wirtualną platformy Azure, upewnij się, że żadne inne aplikacje są obecnie zapisywania wirtualnego dysku twardego. Dobrym sposobem na to jest dzierżawa zasobu, który ma być skopiowany. Alternatywnie można najpierw utworzyć migawki wirtualnego dysku twardego, a następnie skopiuj migawki.

Jeśli nie uniemożliwiają innych aplikacji zapisywanie do obiektów blob lub pliki podczas są kopiowane, następnie należy pamiętać o tym, czy do czasu zakończenia zadania, skopiowanych zasobów może nie masz już pełną parzystości z zasobami źródła.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Włącz Algorytmy MD5 zgodne ze standardem FIPS dla narzędzia AzCopy po użytkownik "Użyj zgodnych algorytmów FIPS dla celów szyfrowania, mieszania i podpisywania."

Narzędzie AzCopy domyślnie używa implementacji .NET MD5 do obliczania Skrót MD5, podczas kopiowania obiektów, ale istnieją pewne wymagania dotyczące zabezpieczeń potrzebne narzędzia AzCopy, aby włączyć ustawienie MD5 zgodne ze standardem FIPS.

Można utworzyć pliku app.config `AzCopy.exe.config` z właściwością `AzureStorageUseV1MD5` i umieść je aside z AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Dla właściwości "AzureStorageUseV1MD5":

* Wartość true — wartość domyślna to narzędzie AzCopy używa implementacji .NET MD5.
* FALSE — narzędzie AzCopy używa algorytmu MD5 zgodne ze standardem FIPS.

Zgodne ze standardem FIPS algorytmy są wyłączone domyślnie w systemie Windows. Możesz zmienić to ustawienie zasad na tym komputerze. W oknie uruchamiania (Windows + R) wpisz secpol.msc, aby otworzyć **zasady zabezpieczeń lokalnych** okna. W **ustawienia zabezpieczeń** okna, przejdź do **ustawienia zabezpieczeń** > **zasady lokalne** > **opcje zabezpieczeń**. Znajdź **Kryptografia systemu: Użyj zgodnych algorytmów FIPS dla celów szyfrowania, mieszania i podpisywania** zasad. Kliknij dwukrotnie zasady Aby wyświetlić wartość wyświetlana w **ustawienie zabezpieczeń** kolumny.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure Storage i narzędzia AzCopy, zapoznaj się z następującymi zasobami:

### <a name="azure-storage-documentation"></a>Dokumentacja usługi Azure Storage:
* [Wprowadzenie do usługi Azure Storage](../storage-introduction.md)
* [Jak używać magazynu obiektów Blob w języku .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Jak używać magazynu plików w języku .NET](../storage-dotnet-how-to-use-files.md)
* [Jak używać magazynu tabel w języku .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Sposoby tworzenia, zarządzania i usuwania konta magazynu](../storage-create-storage-account.md)
* [Transferowanie danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Wpisy blogu magazynu platformy Azure:
* [Wprowadzenie do usługi Azure Storage Data przenoszenia Library w wersji zapoznawczej](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Wprowadzenie do kopii synchroniczne i dostosowanego typu zawartości](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Ogłoszenie ogólnego dostępności 3.0 narzędzie AzCopy oraz wersję narzędzia AzCopy 4.0, tabela i plik pomocy technicznej w wersji zapoznawczej](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Zoptymalizowane pod kątem scenariuszy kopiowania na dużą skalę](https://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Obsługa magazynu geograficznie nadmiarowego do odczytu](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transferowanie danych za pomocą trybie ponownego uruchamiania i tokenu sygnatury dostępu Współdzielonego](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Przy użyciu obiektu Blob kopiowania między konta](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Trwa przekazywanie/pobieranie plików obiektów blob platformy Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
