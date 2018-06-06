---
title: Kopiowanie lub przenoszenie danych do magazynu Azure z narzędzia AzCopy w systemie Windows | Dokumentacja firmy Microsoft
description: W systemie AzCopy to narzędzie systemu Windows przenosić i kopiować dane do lub z obiektu blob, tabel i zawartości pliku. Kopiowanie danych do magazynu Azure z lokalnych plików lub kopiowania danych w ramach urządzeń magazynujących lub między kontami magazynu. Łatwo przeprowadzić migrację danych do usługi Azure Storage.
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: seguler
ms.openlocfilehash: 430979cf197138a9e239eba74e50e9f97d96cbf6
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757608"
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Transfer danych za pomocą narzędzia AzCopy w systemie Windows
Narzędzie AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych z magazynu obiektów Blob Microsoft Azure, plików i tabeli, przy użyciu prostego polecenia przeznaczone do uzyskania optymalnej wydajności. Dane można kopiować między systemem plików i kontem magazynu lub między kontami magazynu.  

Istnieją dwie wersje programu AzCopy, który można pobrać. Narzędzie AzCopy w systemie Windows oferuje styl Windows opcje wiersza polecenia. [Narzędzie AzCopy w systemie Linux](storage-use-azcopy-linux.md) jest przeznaczony dla platformy Linux oferty styl POSIX opcje wiersza polecenia. W tym artykule omówiono AzCopy w systemie Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Pobierz i zainstaluj narzędzie AzCopy w systemie Windows

### <a name="latest-preview-version-v800"></a>Najnowszej wersji wstępnej (v8.0.0)
Pobierz [najnowszej wersji wstępnej programu AzCopy w systemie Windows](http://aka.ms/downloadazcopypr). Ta wersja zapoznawcza zapewnia znaczną poprawę wydajności i pakietów platformy .NET Core w instalacji.

#### <a name="azcopy-on-windows-80-preview-release-notes"></a>Narzędzie AzCopy na informacje o wersji systemu Windows 8.0 podglądu
- Usługa tabel nie jest już obsługiwana w najnowszej wersji. Jeśli używasz funkcji tabeli eksportu, Pobierz stabilną wersję.
- Utworzone przy użyciu zestawu .NET Core 2.1 i wszystkie zależności .NET Core są teraz opakowane w instalacji.
- Znaczący wzrost wydajności zarówno przekazywanie i pobieranie scenariuszy

### <a name="latest-stable-version-v710"></a>Najnowsza stabilna wersja (v7.1.0)
Pobierz [najnowszą wersję narzędzia AzCopy w systemie Windows](http://aka.ms/downloadazcopy).

### <a name="post-installation-step"></a>Krok po instalacji

Po zainstalowaniu narzędzia AzCopy w systemie Windows za pomocą Instalatora, Otwórz okno polecenia i przejdź do katalogu instalacyjnego programu AzCopy na komputerze — gdy `AzCopy.exe` wykonywalny znajduje się. W razie potrzeby można dodać do ścieżki systemowej lokalizacja instalacji narzędzia AzCopy. Domyślnie program AzCopy jest instalowana na `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` lub `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Pisanie pierwszego polecenia AzCopy

Podstawowa składnia polecenia AzCopy jest:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

W poniższych przykładach pokazano różnych scenariuszy kopiowania danych do i z programu Microsoft Azure blob, tabel i plików. Zapoznaj się [parametrów narzędzia AzCopy](#azcopy-parameters) sekcji, aby uzyskać szczegółowy opis parametry używane w każdej próbce.

## <a name="download-blobs-from-blob-storage"></a>Pobierać obiekty BLOB z magazynu obiektów Blob

Oto kilka sposobów, aby pobierać obiekty BLOB przy użyciu narzędzia AzCopy.

### <a name="download-a-single-blob"></a>Pobieranie pojedynczego obiektu blob

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Należy pamiętać, że jeśli folder `C:\myfolder` nie istnieje, tworzy AzCopy i pobierania `abc.txt ` do nowego folderu.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Pobieranie pojedynczego obiektu blob z regionu pomocniczego

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Należy pamiętać, że użytkownik musi mieć dostęp do odczytu magazynu geograficznie nadmiarowego umożliwia dostęp do regionu pomocniczego.

### <a name="download-all-blobs-in-a-container"></a>Pobierz wszystkie obiekty BLOB w kontenerze

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Przyjęto założenie, że następujące obiekty BLOB znajdują się w określonym kontenerze:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Po wykonaniu operacji pobierania katalogu `C:\myfolder` zawiera następujące pliki:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Jeśli nie zostanie określona opcja `/S`, są pobierane nie obiekty BLOB.

### <a name="download-blobs-with-a-specific-prefix"></a>Pobieranie obiektów blob z określonego prefiksu

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Załóżmy, że następujące obiekty BLOB znajdują się w określonym kontenerze. Wszystkie obiekty BLOB rozpoczynający się od prefiksu `a` są pobierane:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Po wykonaniu operacji pobierania folderu `C:\myfolder` zawiera następujące pliki:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Prefiks ma zastosowanie do katalogu wirtualnego, który stanowi pierwsza część nazwy obiektu blob. W przykładzie przedstawionym powyżej katalogu wirtualnego nie pasuje określony prefiks, więc nie zostanie pobrana. Ponadto jeśli opcja `/S` nie zostanie określony, narzędzie AzCopy nie pobiera wszystkie obiekty BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Ustawianie czasu ostatniej modyfikacji plików wyeksportowane taka sama jak obiekty BLOB źródła

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Można również wykluczyć obiekty BLOB z operacji pobierania na podstawie ich czasu ostatniej modyfikacji. Na przykład, jeśli chcesz wykluczyć obiekty BLOB, którego ostatniej modyfikacji jest taką samą lub nowszą niż plik docelowy Dodaj `/XN` opcji:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Jeśli chcesz wykluczyć obiekty BLOB, którego ostatniej modyfikacji jest tym samym lub starsze niż plik docelowy Dodaj `/XO` opcji:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Przekaż obiekty BLOB do magazynu obiektów Blob

Oto kilka sposobów, aby przekazać obiektów blob przy użyciu narzędzia AzCopy.

### <a name="upload-a-single-blob"></a>Przekaż pojedynczego obiektu blob

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Jeśli określony kontener docelowy nie istnieje, narzędzie AzCopy utworzy go i przekaże do niego plik.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Przekaż pojedynczego obiektu blob do katalogu wirtualnego

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Jeśli określony katalog wirtualny nie istnieje, narzędzie AzCopy przekazywania pliku, aby uwzględnić w nazwie katalogu wirtualnego (*np.*, `vd/abc.txt` w powyższym przykładzie).

### <a name="upload-all-blobs-in-a-folder"></a>Przekaż wszystkie obiekty BLOB w folderze

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Określenie opcji `/S` przekazuje zawartość określonego katalogu rekursywnie magazynu obiektów Blob, co oznacza, jak również przekazać wszystkie podfoldery i pliki. Na przykład załóżmy następujące pliki znajdują się w folderze `C:\myfolder`:

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

Jeśli nie zostanie określona opcja `/S`, AzCopy nie przekazuje rekursywnie. Po wykonaniu operacji przekazywania kontener zawiera następujące pliki:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Przekaż obiekty BLOB, pasujące z określonym wzorcem

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

Jeśli nie zostanie określona opcja `/S`, AzCopy przekazuje tylko obiekty BLOB, które nie znajdują się w katalogu wirtualnym:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Określ typ zawartości MIME docelowego obiektu blob

Domyślnie narzędzie AzCopy ustawia typ zawartości do docelowego obiektu blob `application/octet-stream`. Począwszy od wersji 3.1.0, można jawnie określić typ zawartości przy użyciu opcji `/SetContentType:[content-type]`. Ta składnia ustawia typ zawartości dla wszystkich obiektów blob w operacji przekazywania.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Jeśli określisz `/SetContentType` bez wartości, AzCopy ustawia każdy obiekt blob lub typ zawartości pliku, zgodnie z jego rozszerzenie pliku.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Kopiowanie obiektów blob w magazynie obiektów Blob

Oto kilka sposobów, aby skopiować obiekty BLOB z jednej lokalizacji do innej przy użyciu narzędzia AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Skopiuj pojedynczego obiektu blob z jednego kontenera do drugiej w ramach tego samego konta magazynu 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Podczas kopiowania obiektu blob w ramach konta magazynu, [po stronie serwera kopii](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Kopiowanie pojedynczego obiektu blob z jednego konta magazynu do innego

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Podczas kopiowania obiektu blob na kontach magazynu, [po stronie serwera kopii](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Skopiuj pojedynczego obiektu blob z regionu pomocniczego do regionu podstawowego

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Należy pamiętać, że użytkownik musi mieć dostęp do odczytu magazynu geograficznie nadmiarowego umożliwia dostęp do magazynu pomocniczego.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Kopiowanie pojedynczego obiektu blob i jego migawki z jedno konto magazynu do innego

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Po wykonaniu operacji kopiowania kontenera docelowego obejmuje obiektu blob i jego migawek. Zakładając, że obiekt blob w powyższym przykładzie ma dwa migawki, kontener zawiera następujące obiektów blob i migawki:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Skopiuj wszystkie obiekty BLOB w kontenerze na inne konto magazynu 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Określenie opcji/s przekazywać zawartość rekursywnie określonego kontenera. Zobacz [Przekaż wszystkie obiekty BLOB w folderze](#upload-all-blobs-in-a-folder) więcej informacji oraz przykład.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Synchronicznie kopiować z jednego konta magazynu obiektów blob na inny

Narzędzie AzCopy domyślnie kopiuje dane między dwoma punktami końcowymi magazynu asynchronicznie. W związku z tym operacji kopiowania działa w tle przy użyciu pojemności nieużywanej przepustowości ma umowy dotyczącej poziomu usług pod względem jak szybko obiektu blob jest kopiowany i AzCopy okresowo sprawdza stan kopiowania do momentu Kopiowanie zakończone lub nie powiodło się.

`/SyncCopy` Opcja zapewnia, że operacji kopiowania pobiera spójne szybkości. Narzędzie AzCopy wykonuje synchroniczne kopiowania pobieranie obiektów blob można skopiować z określonego źródła do pamięci lokalnej, a następnie przekazywanie ich do miejsce docelowe magazynu obiektów Blob.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` może generować dodatkowe wyjście koszt w porównaniu do kopiowania asynchroniczne, zalecanym rozwiązaniem jest użycie tej opcji w maszynie Wirtualnej platformy Azure, który znajduje się w tym samym regionie co konta magazynu źródłowego, aby uniknąć kosztów wyjście.

## <a name="download-files-from-file-storage"></a>Pobierz pliki z pliku magazynu

Oto kilka sposobów pobieranie plików przy użyciu narzędzia AzCopy.

### <a name="download-a-single-file"></a>Pobierz pojedynczy plik

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Jeśli określone źródło jest udział plików na platformę Azure, a następnie podaj nazwę pliku dokładną (*np.* `abc.txt`) do pobrania pojedynczy plik, lub określ opcję `/S` Aby pobrać wszystkie pliki w udziale rekursywnie. Podjęto próbę Określ wzorzec pliku i opcji `/S` razem powoduje błąd.

### <a name="download-all-files-in-a-directory"></a>Pobierz wszystkie pliki w katalogu

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Należy pamiętać, że puste foldery nie zostaną pobrane.

## <a name="upload-files-to-an-azure-file-share"></a>Przekazywanie plików do udziału plików na platformę Azure

Oto kilka sposobów, aby przekazać pliki przy użyciu narzędzia AzCopy.

### <a name="upload-a-single-file"></a>Przekaż pojedynczego pliku

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Przekaż wszystkie pliki w folderze

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Należy pamiętać, że puste foldery nie zostały przekazane.

### <a name="upload-files-matching-a-specific-pattern"></a>Przekazywanie plików zgodnych z określonym wzorcem

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Skopiuj pliki w usłudze magazyn plików

Oto kilka sposobów, aby skopiować pliki w udziale plików na platformę Azure przy użyciu narzędzia AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Kopiowanie z jednym udziale plików do innego

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Podczas kopiowania plików między udziałami plików [po stronie serwera kopii](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Kopiowanie z udziału plików na platformę Azure do magazynu obiektów Blob

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Podczas kopiowania pliku z udziału plików do obiektów blob, [po stronie serwera kopii](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Kopiowanie obiektu blob z magazynu obiektów Blob do udziału plików na platformę Azure

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Podczas kopiowania pliku z obiektu blob do udziału plików, [po stronie serwera kopii](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="synchronously-copy-files"></a>Synchronicznie kopiować pliki

Można określić `/SyncCopy` opcję, aby skopiować dane z pliku magazynu do przechowywania plików, z pliku magazynu do magazynu obiektów Blob i z magazynu obiektów Blob do przechowywania plików synchronicznie, AzCopy przez pobieranie źródła danych do pamięci lokalnej i ponownie przekazać go do lokalizacji docelowej. Stosuje standardowe wyjście kosztów.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Podczas kopiowania z pliku magazynu do magazynu obiektów Blob, domyślnym typem obiektu blob jest blokowych obiektów blob; Użytkownik może określić opcję `/BlobType:page` zmienić typ docelowego obiektu blob.

Należy pamiętać, że `/SyncCopy` może generować koszty dodatkowe wyjście w porównaniu do kopiowania asynchronicznego. Zalecanym podejściem jest użycie tej opcji w maszynie Wirtualnej platformy Azure, który znajduje się w tym samym regionie co konta magazynu źródłowego, aby uniknąć kosztów wyjście.

## <a name="export-data-from-table-storage"></a>Eksportowanie danych z magazynu tabel

Spójrzmy na eksportowanie danych z magazynem tabel Azure przy użyciu narzędzia AzCopy.

### <a name="export-a-table"></a>Eksportowanie tabeli

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

Narzędzie AzCopy zapisuje plik manifestu do folderu określonej lokalizacji docelowej. Plik manifestu jest używany w procesie importu do lokalizowania plików niezbędnych danych i sprawdzania poprawności danych. Domyślnie plik manifestu używa następującej konwencji nazewnictwa:

    <account name>_<table name>_<timestamp>.manifest

Użytkownik może również określić opcję `/Manifest:<manifest file name>` można ustawić nazwy pliku manifestu.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Podziel eksportu z magazynu tabel na wiele plików

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

Korzysta z narzędzia AzCopy *indeksu woluminu* w nazwach plików danych podziału celu rozróżnienia wielu plików. Indeks woluminu składa się z dwóch części *indeksu zakresem kluczy partycji* i *podziału pliku indeksu*. Zarówno indeksy są liczony od zera.

Indeks zakresem kluczy partycji jest 0, jeśli użytkownik nie określono opcji `/PKRS`.

Na przykład, załóżmy, że AzCopy generuje dwa pliki danych po użytkownika określa opcję `/SplitSize`. Wynikowa nazwy pliku danych może być:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Należy pamiętać, że minimalna możliwych wartości dla opcji `/SplitSize` wynosi 32 MB. Jeśli określone miejsce docelowe magazynu obiektów Blob, AzCopy dzieli pliku danych, po jego rozmiary przez nią miejsce osiągnie limit rozmiaru obiektów blob (200GB), niezależnie od tego, czy opcja `/SplitSize` został określony przez użytkownika.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Eksportowanie tabeli na format pliku danych JSON lub CSV

Domyślnie narzędzie AzCopy eksportuje tabel do plików danych JSON. Można określić opcję `/PayloadFormat:JSON|CSV` Eksportowanie tabel jako JSON lub CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

Podczas określania format ładunku CSV, AzCopy także generuje plik schematu z rozszerzeniem pliku `.schema.csv` dla każdego pliku danych.

### <a name="export-table-entities-concurrently"></a>Eksportowanie tabeli jednocześnie jednostek

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

Narzędzie AzCopy uruchamia jednoczesnych operacji eksportowania jednostek, gdy użytkownik określi opcji `/PKRS`. Każda operacja eksportuje jednym zakresem kluczy partycji.

Należy pamiętać, że liczba jednoczesnych operacji również jest kontrolowane przez opcję `/NC`. AzCopy używa liczba rdzeni procesorów jako wartość domyślną `/NC` podczas kopiowania jednostek tabeli, nawet jeśli `/NC` nie została określona. Gdy użytkownik określi opcji `/PKRS`, AzCopy używa mniejszej z dwóch wartości — zakresami kluczy partycji i jawnie lub niejawnie określono jednoczesnych operacji — do określić liczbę jednoczesnych operacji można uruchomić. Aby uzyskać więcej informacji, wpisz `AzCopy /?:NC` w wierszu polecenia.

### <a name="export-a-table-to-blob-storage"></a>Eksportowanie tabeli do magazynu obiektów Blob

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

Narzędzie AzCopy generuje plik danych JSON do kontenera obiektów blob z następującą konwencją:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Wygenerowany plik danych JSON następuje format ładunku dla minimalne metadane. Aby uzyskać więcej informacji na ten format ładunku, zobacz [Format ładunku dla operacji usługi tabeli](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Należy pamiętać, że podczas eksportowania tabel do obiektów blob, AzCopy jednostek tabeli do lokalnego tymczasowe pliki danych i następnie pobiera tych jednostek do obiektu blob. Te dane tymczasowe pliki są umieszczane w folder plików dziennika z domyślnej ścieżki "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", można określić opcję/Z: [arkusza pliku folder] do dziennika zmian lokalizacja folderu pliku i w związku z tym zmienić lokalizację plików danych tymczasowych. Dane tymczasowe, które rozmiaru plików zadecyduje o rozmiar jednostek tabeli i rozmiar określony /SplitSize opcji, chociaż plik danych tymczasowych w dysk lokalny jest usuwany natychmiast po przekazaniu obiektu blob, upewnij się, że masz wystarczająco lokalnego Miejsce na dysku do przechowywania plików danych tymczasowych, zanim zostaną one usunięte.

## <a name="import-data-into-table-storage"></a>Importowanie danych do magazynu tabel

Spójrzmy na importowanie danych do magazynu tabel Azure przy użyciu narzędzia AzCopy.

### <a name="import-a-table"></a>Importowanie tabeli

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

Opcja `/EntityOperation` wskazuje sposób wstawiania jednostek do tabeli. Możliwe wartości:

* `InsertOrSkip`: Pomija istniejącej jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.
* `InsertOrMerge`: Scala istniejącej jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.
* `InsertOrReplace`: Zastępuje istniejące jednostki i wstawia nową jednostkę, jeśli nie istnieje w tabeli.

Zauważ, że nie można określić opcji `/PKRS` w scenariuszu importu. W odróżnieniu od scenariusza eksportu, w którym należy określić opcję `/PKRS` zacząć jednoczesnych operacji AzCopy jest uruchamiany jednoczesnych operacji domyślnie podczas importowania tabeli. Domyślna liczba jednoczesnych operacji uruchomiona jest równa liczbie procesorów; jednak można określić różne liczby równoczesnych z opcją `/NC`. Aby uzyskać więcej informacji, wpisz `AzCopy /?:NC` w wierszu polecenia.

Należy pamiętać, że narzędzie AzCopy obsługuje wyłącznie importowanie do formatu JSON, CSV nie. AzCopy nie obsługuje importowania tabeli z JSON utworzonych przez użytkownika i pliki manifestu. Oba te pliki muszą pochodzić z narzędzia AzCopy tabeli eksportu. Aby uniknąć błędów, nie zmodyfikuj wyeksportowanego JSON lub pliku manifestu.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Importowanie jednostek do tabeli z magazynu obiektów Blob

Założono kontenera obiektów Blob zawiera następujące: plik A JSON reprezentujący tabel Azure i jego towarzyszący plik manifestu.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Można uruchomić następujące polecenie, aby zaimportować jednostki do tabeli za pomocą pliku manifestu w danym kontenerze obiektów blob:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Inne funkcje AzCopy

Spójrzmy na innych funkcji AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Tylko skopiować dane, które nie istnieje w docelowym

`/XO` i `/XN` parametry umożliwiają Wyklucz zasoby starszej lub nowszej źródła zapobiega kopiowaniu odpowiednio. Jeśli chcesz skopiować źródła zasobów, które nie istnieje w docelowym, można określić oba parametry polecenia AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Należy pamiętać, że to nie jest obsługiwana podczas źródłowe lub docelowe jest tabelą.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Określ parametry wiersza polecenia przy użyciu pliku odpowiedzi

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

Parametry wiersza polecenia AzCopy można umieścić w pliku odpowiedzi. Narzędzie AzCopy przetwarza parametrów w pliku tak, jakby były one określone w wierszu polecenia wykonywanie bezpośrednich podstawienia z zawartością pliku.

Załóżmy, plik odpowiedzi o nazwie `copyoperation.txt`, który zawiera następujące wiersze. Każdy parametr narzędzia AzCopy można określić w jednym wierszu

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

lub na oddzielnych wierszach:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

Narzędzie AzCopy wynik negatywny, jeśli parametr można podzielić na dwa wiersze, jak pokazano poniżej, aby uzyskać `/sourcekey` parametru:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Użyj wielu plików odpowiedzi, aby określić parametry wiersza polecenia

Załóżmy, plik odpowiedzi o nazwie `source.txt` , który określa kontener źródła:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

I plik odpowiedzi o nazwie `dest.txt` określający folderu docelowego w systemie plików:

    /Dest:C:\myfolder

I plik odpowiedzi o nazwie `options.txt` , który określa opcje narzędzia AzCopy:

    /S /Y

Aby wywołać narzędzia AzCopy z tych plików odpowiedzi, które znajdują się w katalogu `C:\responsefiles`, użyj tego polecenia:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

Narzędzie AzCopy przetwarza to polecenie tak samo, jak gdyby uwzględnione wszystkie poszczególne parametry w wierszu polecenia:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Określ sygnatury dostępu współdzielonego (SAS)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

Można również określić sygnatury dostępu Współdzielonego w kontenerze identyfikatora URI:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Folder plików dziennika

Zawsze, gdy wydać polecenie do narzędzia AzCopy, sprawdza czy istnieje w pliku dziennika w domyślnym folderze, lub czy istnieje w folderze określonej za pomocą tej opcji. Jeśli plik dziennika nie istnieje w każdym miejscu, AzCopy traktuje jako nowe działanie i generuje nowy plik dziennika.

Jeśli plik dziennika istnieje, narzędzie AzCopy sprawdza, czy wiersz polecenia, który możesz wpisać zgodny wiersz polecenia w pliku dziennika. Jeśli dwa wiersze poleceń są zgodne, AzCopy wznawia działanie niekompletne. Jeśli nie są zgodne, zostanie wyświetlony monit albo zastąpić plik dziennika, można uruchomić nowej operacji lub Anuluj bieżącą operację.

Jeśli chcesz użyć domyślnej lokalizacji pliku dziennika:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Jeśli pominięto opcję `/Z`, lub określ opcję `/Z` bez ścieżkę do folderu, jak pokazano powyżej, AzCopy tworzy plik dziennika w lokalizacji domyślnej, która jest `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Jeśli istnieje już plik dziennika, AzCopy wznawia działanie na podstawie pliku dziennika.

Jeśli chcesz określić niestandardową lokalizację pliku dziennika:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

W tym przykładzie powoduje utworzenie pliku dziennika, jeśli jeszcze nie istnieje. Jeśli istnieje, narzędzie AzCopy wznawia działanie na podstawie pliku dziennika.

Jeśli chcesz wznowić działanie narzędzia AzCopy:

```azcopy
AzCopy /Z:C:\journalfolder\
```

W tym przykładzie wznawia Ostatnia operacja mogła zakończyć się niepowodzeniem, aby zakończyć.

### <a name="generate-a-log-file"></a>Wygenerowanie pliku dziennika

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Jeśli określono opcję `/V` bez podawania ścieżkę pliku do pełnego dziennika, następnie AzCopy tworzy plik dziennika w lokalizacji domyślnej, która jest `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

W przeciwnym razie należy utworzyć plik dziennika w lokalizacji niestandardowej:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Należy pamiętać, że jeśli Określ ścieżkę względną po opcji `/V`, takich jak `/V:test/azcopy1.log`, a następnie pełnego dziennika jest tworzony w bieżącym katalogu roboczym w podfolderze o nazwie `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Określ liczbę jednoczesnych operacji można uruchomić

Opcja `/NC` określa liczbę jednoczesnych kopii operacji. Domyślnie narzędzie AzCopy uruchamia określonej liczby jednoczesnych operacji w celu zwiększenia przepływności transferu danych. Operacjach tabeli liczba jednoczesnych operacji jest równa liczbie procesorów, które masz. Dla operacji obiektów Blob i pliku, liczba jednoczesnych operacji jest równa 8 razy liczba procesorów posiadanego. Jeśli używasz narzędzia AzCopy w niskiej przepustowości sieci, można określić mniejszą liczbę dla /NC uniknąć błąd spowodowany konkurencji zasobów.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Uruchom narzędzie AzCopy dla emulatora magazynu Azure

Możesz uruchomić narzędzie AzCopy przed [emulatora magazynu Azure](storage-use-emulator.md) dla obiektów blob:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Można również uruchomić dla tabel:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

### <a name="automatically-determine-content-type-of-a-blob"></a>Automatycznie określić typ zawartości obiektu Blob

Narzędzie AzCopy Określa typ zawartości obiektu blob na podstawie pliku JSON zawierający typ zawartości do mapowania rozszerzenia pliku. Ten plik JSON o nazwie AzCopyConfig.json i znajduje się w katalogu narzędzia AzCopy. Jeśli typ pliku, który nie jest na liście można dołączyć mapowania do pliku JSON:

```
{
  "MIMETypeMapping": {
    ".myext": "text/mycustomtype",
    .
    .
  }
}
```     

## <a name="azcopy-parameters"></a>Parametry AzCopy

Poniżej opisano parametry narzędzia AzCopy. Można również wpisać jedną z następujących poleceń z poziomu wiersza polecenia, aby uzyskać pomoc przy użyciu narzędzia AzCopy:

* Aby uzyskać szczegółową pomoc wiersza polecenia AzCopy: `AzCopy /?`
* Szczegółowe informacje o żadnych parametrów narzędzia AzCopy: `AzCopy /?:SourceKey`
* Przykłady wiersza polecenia: `AzCopy /?:Sample`

### <a name="sourcesource"></a>/ Źródła: "source"

Określa źródło danych do skopiowania. Źródło może być katalogu w systemie plików, kontenera obiektów blob, katalog wirtualny obiektów blob, udział pliku magazynu, katalog pliku magazynu lub tabeli platformy Azure.

**Dotyczy:** obiektów blob, plików, tabel

### <a name="destdestination"></a>/ Dest: "miejsce docelowe"

Określa miejsce docelowe kopiowania. Miejsce docelowe może być katalogu w systemie plików, kontenera obiektów blob, katalog wirtualny obiektów blob, udział pliku magazynu, katalog pliku magazynu lub tabeli platformy Azure.

**Dotyczy:** obiektów blob, plików, tabel

### <a name="patternfile-pattern"></a>/ Wzorca: "wzorzec pliku"

Określa wzorzec pliku, która wskazuje plików do skopiowania. Zachowanie parametru /Pattern zależy od lokalizacji źródła danych i obecności w trybie cykliczne. Za pomocą opcji opcji/s. jest określony tryb cykliczne

Jeśli określone źródło jest katalogiem w systemie plików, a następnie obowiązują standardowe symbole wieloznaczne i podać wzorzec pliku jest dopasowywana do plików w katalogu. Jeśli opcja/s jest określana, następnie AzCopy zgodny określony wzorzec względem wszystkich plików w wszystkie podfoldery znajdujące się poniżej katalogu.

Jeśli określone źródło jest kontenera obiektów blob lub katalogu wirtualnego, symbole wieloznaczne nie są stosowane. Jeśli opcja/s jest określana, następnie AzCopy interpretuje wzorzec określony plik jako prefiks obiektu blob. Jeśli opcja /S nie zostanie określony, następnie AzCopy zgodny z wzorcem pliku nazwami dokładne obiektów blob.

Jeśli określone źródło jest udział plików na platformę Azure, należy określić nazwę pliku dokładne, (np. abc.txt) do skopiowania pojedynczy plik, lub określ opcję/s do skopiowania wszystkich plików w udziale rekursywnie. Próba Określ zarówno pliku wzorca i opcja /S razem wyników błąd.

AzCopy korzysta z uwzględnieniem wielkości liter dopasowanie, jeśli / Source jest kontenera obiektów blob lub katalogu wirtualnego obiektów blob i korzysta z odpowiadającym bez uwzględniania wielkości liter we wszystkich innych przypadkach.

Plik domyślny wzorzec używany, jeśli nie określono żadnych wzorzec pliku jest *.* dla lokalizacji systemu plików lub pustego prefiksu lokalizacji magazynu Azure. Nie można określać wielu wzorców pliku.

**Dotyczy:** obiektów blob, pliki

### <a name="destkeystorage-key"></a>/DestKey:"storage-key"

Określa klucz konta magazynu dla zasobu docelowego.

**Dotyczy:** obiektów blob, plików, tabel

### <a name="destsassas-token"></a>/ DestSAS: "tokenu sygnatury dostępu współdzielonego"

Określa dostępu sygnatury dostępu Współdzielonego z uprawnieniami odczytu i zapisu dla miejsca docelowego (jeśli dotyczy). Ująć SAS z cudzysłowami podwójnymi, jak mogą zawiera znaki specjalne wiersza polecenia.

Zasób docelowy jest kontenera obiektów blob, udziału plików lub tabeli, można określić tę opcję, a następnie tokenu sygnatury dostępu Współdzielonego, czy jako element docelowy kontener obiektów blob, udziału plików lub tabeli identyfikatora URI, bez tej opcji można określić sygnatury dostępu Współdzielonego.

Jeśli na źródłowym i docelowym są oba obiekty BLOB, docelowego obiektu blob musi znajdować się w tym samym kontem magazynu obiektów blob źródła.

**Dotyczy:** obiektów blob, plików, tabel

### <a name="sourcekeystorage-key"></a>/SourceKey:"storage-key"

Określa klucz konta magazynu dla zasobu źródła.

**Dotyczy:** obiektów blob, plików, tabel

### <a name="sourcesassas-token"></a>/ SourceSAS: "tokenu sygnatury dostępu współdzielonego"

Określa sygnaturę dostępu współdzielonego z uprawnieniami odczytu i listy dla źródła (jeśli dotyczy). Ująć SAS z cudzysłowami podwójnymi, jak mogą zawiera znaki specjalne wiersza polecenia.

Jeśli zasób źródła jest kontenera obiektów blob, a podano klucz ani sygnatury dostępu Współdzielonego, kontenera obiektów blob jest odczytu za pomocą dostęp anonimowy.

Jeśli źródło jest udział plików lub tabeli, należy podać klucz lub sygnatury dostępu Współdzielonego.

**Dotyczy:** obiektów blob, plików, tabel

### <a name="s"></a>/S

Określa tryb cyklicznych operacji kopiowania. W trybie cykliczne AzCopy kopiuje wszystkie obiekty BLOB lub pliki zgodne ze wzorcem określonego pliku, włącznie z zawartymi w podfolderach.

**Dotyczy:** obiektów blob, pliki

### <a name="blobtypeblock--page--append"></a>/ BlobType: "block" | "page" | "Dołącz"

Określa, czy docelowego obiektu blob jest blokowych obiektów blob i stronicowych obiektów blob, uzupełnialny obiekt blob. Ta opcja ma zastosowanie tylko wtedy, gdy przekazujesz obiektu blob. W przeciwnym razie zostanie wygenerowany błąd. Jeśli obiektem docelowym jest obiektu blob, a ta opcja nie jest określona, domyślnie AzCopy tworzy blokowych obiektów blob.

**Dotyczy:** obiektów blob

### <a name="checkmd5"></a>/ CheckMD5

Oblicza Skrót MD5 danych pobrane i weryfikuje, czy skrótu MD5 przechowywana w obiekcie blob lub właściwość Content-MD5 pliku odpowiada obliczona wartość skrótu. Wyboru MD5 jest domyślnie wyłączona, dlatego należy określić tę opcję, aby sprawdzić MD5 podczas pobierania danych.

Należy pamiętać, że usługi Azure Storage nie gwarantuje, że skrótu MD5 przechowywane dla obiekt blob, czy plik jest aktualny. Odpowiada klienta MD5 aktualizowania obiektów blob lub plik jest modyfikowany.

Narzędzie AzCopy zawsze ustawia właściwość Content-MD5 dla pliku lub obiektów blob platformy Azure po przekazać go do usługi.  

**Dotyczy:** obiektów blob, pliki

### <a name="snapshot"></a>/ Migawki

Wskazuje, czy należy przesłać migawki. Ta opcja jest prawidłowy tylko w przypadku, gdy źródłem jest obiektu blob.

Migawki obiektu blob przekazanych zostały zmienione w następującym formacie: .extension nazwy obiektów blob (migawka time)

Domyślnie nie są kopiowane migawki.

**Dotyczy:** obiektów blob

### <a name="vverbose-log-file"></a>/ V: [plików pełnego dziennika]

Komunikaty stanu pełne dane wyjściowe do pliku dziennika.

Domyślnie plik dziennika pełne nosi nazwę AzCopyVerbose.log w `%LocalAppData%\Microsoft\Azure\AzCopy`. Jeśli określisz istniejącej lokalizacji plików dla tej opcji, pełny dziennik jest dołączany do tego pliku.  

**Dotyczy:** obiektów blob, plików, tabel

### <a name="zjournal-file-folder"></a>/Z:[journal-file-folder]

Określa folder plików dziennika dla wznawia działanie.

Narzędzie AzCopy zawsze obsługuje wznawianie, jeśli operacja została przerwana.

Jeśli ta opcja nie jest określony lub został określony bez ścieżki folderu, następnie AzCopy tworzy plik dziennika w lokalizacji domyślnej, czyli % LocalAppData%\Microsoft\Azure\AzCopy.

Zawsze, gdy wydać polecenie do narzędzia AzCopy, sprawdza czy istnieje w pliku dziennika w domyślnym folderze, lub czy istnieje w folderze określonej za pomocą tej opcji. Jeśli plik dziennika nie istnieje w każdym miejscu, AzCopy traktuje jako nowe działanie i generuje nowy plik dziennika.

Jeśli plik dziennika istnieje, narzędzie AzCopy sprawdza, czy wiersz polecenia, który możesz wpisać zgodny wiersz polecenia w pliku dziennika. Jeśli dwa wiersze poleceń są zgodne, AzCopy wznawia działanie niekompletne. Jeśli nie są zgodne, zostanie wyświetlony monit albo zastąpić plik dziennika, można uruchomić nowej operacji lub Anuluj bieżącą operację.

Plik dziennika jest usuwany po pomyślnym zakończeniu operacji.

Należy pamiętać, że wznawia działanie z pliku dziennika utworzonego przez poprzednią wersję programu AzCopy nie jest obsługiwany.

**Dotyczy:** obiektów blob, plików, tabel

### <a name="parameter-file"></a>/@:"parameter-File"

Określa plik, który zawiera parametry. Narzędzie AzCopy przetwarza parametrów w pliku, tak jakby były one określone w wierszu polecenia.

W pliku odpowiedzi można określić wiele parametrów w jednym wierszu lub określ każdego parametru w osobnym wierszu. Należy pamiętać, że poszczególne parametru nie może obejmować wiele wierszy.

Pliki odpowiedzi mogą obejmować komentarze wierszy, które zaczynają się od symbolu #.

Można określić wiele plików odpowiedzi. Jednak należy pamiętać, że narzędzie AzCopy nie obsługuje zagnieżdżone pliki odpowiedzi.

**Dotyczy:** obiektów blob, plików, tabel

### <a name="y"></a>/ Y

Pomija wszystkie monity potwierdzenie narzędzia AzCopy. Ta opcja umożliwia także użycie tokeny sygnatury dostępu Współdzielonego tylko do zapisu w przypadku scenariuszy przekazywania danych, gdy nie określono /XO i /XN.

**Dotyczy:** obiektów blob, plików, tabel

### <a name="l"></a>/L

Określa listę operację. dane nie zostaną skopiowane.

AzCopy interpretuje korzystanie z tej opcji jako symulacji do uruchomienia wiersza polecenia, bez stosowania tej opcji /L i liczby ile obiekty są kopiowane, można określić opcję /V w tym samym czasie, aby sprawdzić, które obiekty są kopiowane w pełnego dziennika.

Zachowanie tej opcji również jest określana przez lokalizację źródła danych i obecność cyklicznego opcji/s i pliku wzorca w trybie /Pattern.

Narzędzie AzCopy musi mieć uprawnienie listy i odczytu tej lokalizacji źródła przy użyciu tej opcji.

**Dotyczy:** obiektów blob, pliki

### <a name="mt"></a>/MT

Ustawia czas ostatniej modyfikacji pobrany plik na taki sam jak źródłowego obiektu blob lub pliku.

**Dotyczy:** obiektów blob, pliki

### <a name="xn"></a>/XN

Wyklucza nowszej zasobów źródła. Zasób nie jest kopiowana, jeśli godzina ostatniej modyfikacji źródła jest taka sama lub nowsza niż docelowy.

**Dotyczy:** obiektów blob, pliki

### <a name="xo"></a>/XO
Wyklucza starszych zasobów źródła. Zasób nie jest kopiowana, jeśli godzina ostatniej modyfikacji źródła jest taka sama lub starsza niż docelowy.

**Dotyczy:** obiektów blob, pliki

### <a name="a"></a>/A

Wysyła tylko pliki, które mają ustawiony atrybut archiwum.

**Dotyczy:** obiektów blob, pliki

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Wysyła tylko pliki mające zestawu określonych atrybutów.

Dostępne atrybuty obejmują:

* R = plików tylko do odczytu
* = Plik gotowy do archiwizacji
* S = System plików
* H = ukryte pliki
* C = pliki skompresowane
* N = normalne pliki
* E = zaszyfrowane pliki
* T = pliki tymczasowe
* O = plików trybu Offline
* I = indeksowane inne niż pliki

**Dotyczy:** obiektów blob, pliki

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]

Umożliwia wykluczenie plików, które nie ma żadnej z zestaw określonych atrybutów.

Dostępne atrybuty obejmują:

* R = plików tylko do odczytu
* = Plik gotowy do archiwizacji
* S = System plików
* H = ukryte pliki
* C = pliki skompresowane
* N = normalne pliki
* E = zaszyfrowane pliki
* T = pliki tymczasowe
* O = plików trybu Offline
* I = indeksowane inne niż pliki

**Dotyczy:** obiektów blob, pliki

### <a name="delimiterdelimiter"></a>/Delimiter:"delimiter"

Wskazuje znak ogranicznika używany do ograniczania katalogów wirtualnych w nazwie obiektu blob.

Domyślnie używa narzędzia AzCopy / jako znak ogranicznika. AzCopy obsługuje jednak przy użyciu znaków wspólnych (takich jak @, # lub %) jako ogranicznik. Jeśli potrzebujesz zawiera jeden z tych znaków specjalnych w wierszu polecenia, należy wpisać nazwę pliku z cudzysłowami podwójnymi.

Ta opcja ma zastosowanie tylko do pobrania obiektów blob.

**Dotyczy:** obiektów blob

### <a name="ncnumber-of-concurrent-operations"></a>/ NC: "numer--równoczesnych operacji"

Określa liczbę jednoczesnych operacji.

Narzędzie AzCopy domyślnie uruchamia określonej liczby jednoczesnych operacji w celu zwiększenia przepływności transferu danych. Należy pamiętać, że dużą liczbą jednoczesnych operacji w środowisku niskiej przepustowości może przeciąży połączenie sieciowe i zapobiec pełni ukończenie operacji. Ograniczenie przepustowości jednoczesnych operacji oparte na rzeczywiste dostępnej przepustowości sieci.

Górny limit liczby jednoczesnych operacji wynosi 512.

**Dotyczy:** obiektów blob, plików, tabel

### <a name="sourcetypeblob--table"></a>/ Źródłowa: "Blob" | "Tabela"

Określa, że `source` zasób jest dostępny w środowisku programistycznym lokalne uruchamianie w emulatorze magazynu obiektów blob.

**Dotyczy:** obiektów blob, tabel

### <a name="desttypeblob--table"></a>/ DestType: "Blob" | "Tabela"

Określa, że `destination` zasób jest dostępny w środowisku programistycznym lokalne uruchamianie w emulatorze magazynu obiektów blob.

**Dotyczy:** obiektów blob, tabel

### <a name="pkrskey1key2key3"></a>/ PKRS: "key1 klucz2 # klucz3 #..."

Dzieli zakresem kluczy partycji umożliwia eksportowanie danych z tabeli równolegle, co przyspiesza operacji eksportowania.

Jeśli ta opcja nie jest określona, następnie AzCopy używa pojedynczego wątku do wyeksportowania jednostek tabeli. Na przykład, jeśli użytkownik określi /PKRS: "aa #bb", a następnie AzCopy uruchamia trzy jednoczesnych operacji.

Każda operacja eksportuje jednego z trzech zakresów klucza partycji, jak pokazano poniżej:

  [pierwszej partycji klucza, aa)

  [aa, bb)

  [bb, ostatnie partycji klucza]

**Dotyczy:** tabel

### <a name="splitsizefile-size"></a>/SplitSize:"file-size"

Określa wyeksportowany plik podzielić rozmiar w MB, minimalnym dozwolona wartość to 32.

Jeśli ta opcja nie jest określona, narzędzie AzCopy eksportuje dane tabeli w pojedynczym pliku.

Jeśli dane w tabeli są eksportowane do obiektu blob, a rozmiar wyeksportowany plik osiągnie limit 200 GB rozmiar obiektu blob, następnie AzCopy dzieli wyeksportowany plik, nawet jeśli ta opcja nie jest określona.

**Dotyczy:** tabel

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Określa zachowanie importu danych tabeli.

* InsertOrSkip - pomija istniejącej jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.
* InsertOrMerge - scala istniejącej jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.
* InsertOrReplace - zastępuje istniejącej jednostki lub wstawia nową jednostkę, jeśli nie istnieje w tabeli.

**Dotyczy:** tabel

### <a name="manifestmanifest-file"></a>/Manifest:"manifest-file"

Określa plik manifestu dla tabeli eksportowania i importowania operacji.

Ta opcja jest opcjonalny podczas operacji eksportowania, AzCopy generuje plik manifestu z wstępnie zdefiniowanej nazwy, jeśli ta opcja nie jest określona.

Ta opcja jest wymagana podczas operacji importowania do lokalizowania plików danych.

**Dotyczy:** tabel

### <a name="synccopy"></a>/SyncCopy

Wskazuje, czy synchronicznie kopiowania obiektów blob lub plików między dwoma punktami końcowymi usługi Azure Storage.

Narzędzie AzCopy domyślnie używa kopii asynchronicznych po stronie serwera. Wybierz tę opcję w celu wykonania kopii synchroniczne, który pobiera obiektów blob lub pliki do pamięci lokalnej i przekazuje je do magazynu Azure.

Tej opcji można użyć podczas kopiowania plików w magazynie obiektów Blob, Magazyn plików, lub z magazynu obiektów Blob do przechowywania plików lub na odwrót.

**Dotyczy:** obiektów blob, pliki

### <a name="setcontenttypecontent-type"></a>/ SetContentType: "content-type"

Określa typ zawartości MIME docelowym obiektów blob lub plików.

Domyślnie narzędzie AzCopy ustawia typ zawartości dla obiekt blob lub pliku application/octet-stream. Typ zawartości dla wszystkich obiektów blob lub plików można ustawić jawnie określając wartość dla tej opcji.

Jeśli zostanie określona opcja bez wartości, AzCopy ustawia każdy obiekt blob lub typ zawartości pliku, zgodnie z jego rozszerzenie pliku.

**Dotyczy:** obiektów blob, pliki

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: "JSON" | "CSV"

Określa format pliku wyeksportowanych danych tabeli.

Jeśli ta opcja nie jest określona, domyślnie AzCopy eksportuje plik danych tabeli w formacie JSON.

**Dotyczy:** tabel

## <a name="known-issues-and-best-practices"></a>Znane problemy i najlepsze rozwiązania

Spójrzmy na kilka znanych problemów i najlepsze rozwiązania.

### <a name="limit-concurrent-writes-while-copying-data"></a>Limit równoczesnych zapisów podczas kopiowania danych

Podczas kopiowania obiektów blob lub pliki z narzędzia AzCopy, należy pamiętać, że inna aplikacja może modyfikować danych podczas kopiowania go. Jeśli to możliwe upewnij się, że dane, które są kopiowane nie jest modyfikowana podczas operacji kopiowania. Na przykład podczas kopiowania wirtualnego dysku twardego skojarzonego z maszyny wirtualnej platformy Azure, upewnij się, że żadne inne aplikacje są obecnie zapisywania wirtualnego dysku twardego. Dobrym sposobem na to jest dzierżawa zasobów do skopiowania. Alternatywnie można najpierw Utwórz migawkę wirtualnego dysku twardego, a następnie skopiuj migawki.

Jeśli nie można zapobiec inne aplikacje, zapisywania do obiektów blob lub plików, gdy są kopiowane, należy pamiętać, że w czasie, który kończy zadanie, kopiowanych zasobów może nie masz już pełna parzystości z zasobami źródła.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Włącz Algorytmy MD5 zgodne ze standardem FIPS dla narzędzia AzCopy podczas możesz "Użyj zgodnych algorytmów FIPS dla celów szyfrowania, mieszania i podpisywania."

AzCopy domyślnie używana implementacja .NET MD5 do obliczenia MD5 podczas kopiowania obiektów, ale istnieją pewne wymagania dotyczące zabezpieczeń, które AzCopy jest potrzebny do ustawienie MD5 zgodne ze standardem FIPS.

Można utworzyć pliku app.config `AzCopy.exe.config` z właściwością `AzureStorageUseV1MD5` i umieszcza je Zarezerwuj z AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Dla właściwości "AzureStorageUseV1MD5":

* Wartość true — wartość domyślna to narzędzie AzCopy używa implementacji .NET MD5.
* FAŁSZ — AzCopy używa algorytmu MD5 zgodne ze standardem FIPS.

Domyślnie w systemie Windows są wyłączone zgodnych algorytmów FIPS. Można zmienić to ustawienie zasad na tym komputerze. W oknie uruchamiania (Windows + R), wpisz secpol.msc, aby otworzyć **zasady zabezpieczeń lokalnych** okna. W **ustawienia zabezpieczeń** okna, przejdź do **ustawienia zabezpieczeń** > **zasady lokalne** > **opcje zabezpieczeń**. Zlokalizuj **Kryptografia systemu: Użyj zgodnych algorytmów FIPS dla celów szyfrowania, mieszania i podpisywania** zasad. Kliknij dwukrotnie zasadę, aby wyświetlić wartość wyświetlana w **ustawienie zabezpieczeń** kolumny.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure Storage i narzędzia AzCopy, zapoznaj się z następującymi zasobami:

### <a name="azure-storage-documentation"></a>Dokumentacja magazynu Azure:
* [Wprowadzenie do usługi Azure Storage](../storage-introduction.md)
* [Jak używać magazynu obiektów Blob w .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Jak używać magazynu plików w .NET](../storage-dotnet-how-to-use-files.md)
* [Jak używać magazynu tabel w .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Sposobu tworzenia, zarządzania i usuwania konta magazynu](../storage-create-storage-account.md)
* [Transferowanie danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Wpisy blogu magazynu Azure:
* [Wprowadzenie Podgląd Biblioteka przepływu danych usługi Azure Storage](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Wprowadzenie do kopiowania synchroniczne i dostosowane typ zawartości](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [Narzędzie AzCopy: Announcing ogólne dostępności 3.0 AzCopy oraz wersji zapoznawczej AzCopy 4.0 z tabeli i plik pomocy technicznej](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [Narzędzie AzCopy: Zoptymalizowana pod kątem scenariuszy kopii na dużą skalę](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Obsługa dostęp do odczytu magazynu geograficznie nadmiarowego](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [Narzędzie AzCopy: Transfer danych za pomocą trybu ponownego uruchamiania i tokenu sygnatury dostępu Współdzielonego](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [Narzędzia AzCopy: Blob kopiowania między konta przy użyciu](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [Narzędzie AzCopy: Przekazywanie pobieranie plików dla obiektów blob Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
