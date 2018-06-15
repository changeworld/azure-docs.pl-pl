---
title: Kopiowanie lub przenoszenie danych do magazynu Azure z narzędzia AzCopy w systemie Linux | Dokumentacja firmy Microsoft
description: W systemie AzCopy Linux narzędzia do przenoszenia lub kopiowania danych do lub z obiektu blob i zawartości pliku. Kopiowanie danych do magazynu Azure z lokalnych plików lub kopiowania danych w ramach urządzeń magazynujących lub między kontami magazynu. Łatwo przeprowadzić migrację danych do usługi Azure Storage.
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
ms.date: 04/26/2018
ms.author: seguler
ms.openlocfilehash: 80b112de1fd8417dd64d9d95b7a037ec876d18c7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32194672"
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Transfer danych za pomocą narzędzia AzCopy w systemie Linux

Narzędzie AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych z magazynu obiektów Blob Microsoft Azure i plików, przy użyciu prostego polecenia przeznaczone do uzyskania optymalnej wydajności. Dane można kopiować między systemem plików i kontem magazynu lub między kontami magazynu.  

Istnieją dwie wersje programu AzCopy, który można pobrać. Narzędzie AzCopy w systemie Linux jest przeznaczony dla platformy Linux oferty styl POSIX opcje wiersza polecenia. [Narzędzie AzCopy w systemie Windows](../storage-use-azcopy.md) oferuje opcje wiersza polecenia Styl systemu Windows. W tym artykule omówiono AzCopy w systemie Linux. 

> [!NOTE]  
> Począwszy od wersji AzCopy 7.2 zależności .NET Core są dostarczane z pakietu Narzędzia AzCopy. Jeśli używasz wersji 7,2 lub później, nie należy zainstalować oprogramowanie .NET Core jako warunek wstępny.

## <a name="download-and-install-azcopy"></a>Pobierz i zainstaluj narzędzie AzCopy

### <a name="installation-on-linux"></a>Instalacja w systemie Linux

> [!NOTE]
> Może być konieczne zainstalowanie zależności .NET Core 2.1 wyróżnione to [artykułu .NET Core wstępne](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) w zależności od dystrybucji. Typowe dystrybucji, takich jak Ubuntu 16.04 i RHEL 7 to zwykle nie jest potrzebna.

Instalowanie narzędzia AzCopy w systemie Linux (v7.2 lub nowszym) jest tak proste, jak wyodrębnianie pakietu tar i uruchomienie skryptu instalacji. 

**RHEL 6 na podstawie dystrybucje**: [pobrać łącza](https://aka.ms/downloadazcopylinuxrhel6)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinuxrhel6
tar -xf azcopy.tar.gz
sudo ./install.sh
```

**Wszystkie inne dystrybucje systemu Linux**: [pobrać łącza](https://aka.ms/downloadazcopylinux64)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Po zainstalowaniu narzędzia AzCopy w systemie Linux, możesz usunąć wyodrębnione pliki. Alternatywnie, jeśli nie masz uprawnień administratora można również uruchomić `azcopy` za pomocą narzędzia azcopy skryptu powłoki w folderze wyodrębnione.

### <a name="alternative-installation-on-ubuntu"></a>Alternatywne instalacji na Ubuntu

**Ubuntu 14.04**

Dodaj stanie źródło repozytorium produktu Linux firmy Microsoft i zainstalować narzędzia AzCopy:

```bash
sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod/ trusty main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
apt-key adv --keyserver packages.microsoft.com --recv-keys B02C46DF417A0893
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

Dodaj stanie źródło repozytorium produktu Linux firmy Microsoft i zainstalować narzędzia AzCopy:

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod/ xenial main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
apt-key adv --keyserver packages.microsoft.com --recv-keys B02C46DF417A0893
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>Pisanie pierwszego polecenia AzCopy
Podstawowa składnia polecenia AzCopy jest:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

W poniższych przykładach pokazano różne scenariusze kopiowania danych do i z obiektach blob Microsoft Azure i plików. Zapoznaj się `azcopy --help` menu, aby uzyskać szczegółowy opis parametry używane w każdej próbce.

## <a name="blob-download"></a>Obiekt blob: Pobierz
### <a name="download-single-blob"></a>Pobieranie pojedynczego obiektu blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Jeśli folder `/mnt/myfiles` nie istnieje, narzędzie AzCopy tworzy go i pobiera `abc.txt ` do nowego folderu. 

### <a name="download-single-blob-from-secondary-region"></a>Pobieranie pojedynczego obiektu blob z regionu pomocniczego

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Należy pamiętać, że użytkownik musi mieć dostęp do odczytu magazynu geograficznie nadmiarowego włączone.

### <a name="download-all-blobs"></a>Pobierz wszystkie obiekty BLOB

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Przyjęto założenie, że następujące obiekty BLOB znajdują się w określonym kontenerze:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

Po wykonaniu operacji pobierania katalogu `/mnt/myfiles` zawiera następujące pliki:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Jeśli nie zostanie określona opcja `--recursive`, nie obiektu blob zostaną pobrane.

### <a name="download-blobs-with-specified-prefix"></a>Pobieranie obiektów blob z określonego prefiksu

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Załóżmy, że następujące obiekty BLOB znajdują się w określonym kontenerze. Wszystkie obiekty BLOB rozpoczynający się od prefiksu `a` zostaną pobrane.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

Po wykonaniu operacji pobierania folderu `/mnt/myfiles` zawiera następujące pliki:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

Prefiks ma zastosowanie do katalogu wirtualnego, który stanowi pierwsza część nazwy obiektu blob. W powyższym przykładzie katalogu wirtualnego jest niezgodny określony prefiks tak blob nie zostanie pobrana. Ponadto jeśli opcja `--recursive` nie zostanie określony, narzędzie AzCopy nie pobiera wszystkie obiekty BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Ustawianie czasu ostatniej modyfikacji plików wyeksportowane taka sama jak obiekty BLOB źródła

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Można również wykluczyć obiekty BLOB z operacji pobierania na podstawie ich czasu ostatniej modyfikacji. Na przykład, jeśli chcesz wykluczyć obiekty BLOB, którego ostatniej modyfikacji jest taką samą lub nowszą niż plik docelowy Dodaj `--exclude-newer` opcji:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Lub jeśli chcesz wykluczyć obiekty BLOB, którego ostatniej modyfikacji tego samego lub starsze niż plik docelowy Dodaj `--exclude-older` opcji:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Obiekt blob: Przekaż
### <a name="upload-single-file"></a>Przekaż pojedynczy plik

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Jeśli określony kontener docelowy nie istnieje, narzędzie AzCopy utworzy go i przekaże do niego plik.

### <a name="upload-single-file-to-virtual-directory"></a>Przekaż pojedynczy plik do katalogu wirtualnego

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Jeśli określony katalog wirtualny nie istnieje, narzędzie AzCopy przekazywania pliku, aby uwzględnić w nazwie obiektu blob katalogu wirtualnego (*np.*, `vd/abc.txt` w powyższym przykładzie).

### <a name="redirect-from-stdin"></a>Przekieruj stdin

```azcopy
gzip myarchive.tar -c | azcopy \
    --destination https://myaccount.blob.core.windows.net/mycontainer/mydir/myarchive.tar.gz \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Przekaż wszystkie pliki

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Określenie opcji `--recursive` przekazuje zawartość określonego katalogu rekursywnie magazynu obiektów Blob, co oznacza, jak również przekazać wszystkie podfoldery i pliki. Na przykład załóżmy następujące pliki znajdują się w folderze `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Po wykonaniu operacji przekazywania kontener zawiera następujące pliki:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Gdy opcja `--recursive` nie zostanie określony, są przekazywane tylko trzy następujące pliki:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Przekazywanie plików zgodnych z określonym wzorcem

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Załóżmy następujące pliki znajdują się w folderze `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Po wykonaniu operacji przekazywania kontener zawiera następujące pliki:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Gdy opcja `--recursive` nie zostanie określony, narzędzie AzCopy pomija pliki, które są w katalogach podrzędnych:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Określ typ zawartości MIME docelowego obiektu blob
Domyślnie narzędzie AzCopy ustawia typ zawartości do docelowego obiektu blob `application/octet-stream`. Jednak jawnie określić typ zawartości przy użyciu opcji `--set-content-type [content-type]`. Ta składnia ustawia typ zawartości dla wszystkich obiektów blob w operacji przekazywania.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Jeśli opcja `--set-content-type` jest określony bez wartości, a następnie ustawia AzCopy każdy obiekt blob lub typ zawartości pliku, zgodnie z jego rozszerzenie pliku.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>Obiekt blob: kopiowania
### <a name="copy-single-blob-within-storage-account"></a>Skopiuj pojedynczego obiektu blob w ramach konta magazynu

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Podczas kopiowania obiektu blob bez — opcja synchronizacji kopii [po stronie serwera kopii](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-single-blob-across-storage-accounts"></a>Skopiuj pojedynczego obiektu blob na kontach magazynu

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Podczas kopiowania obiektu blob bez — opcja synchronizacji kopii [po stronie serwera kopii](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Skopiuj pojedynczego obiektu blob z regionu pomocniczego do regionu podstawowego

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Należy pamiętać, że użytkownik musi mieć dostęp do odczytu magazynu geograficznie nadmiarowego włączone.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Skopiuj pojedynczego obiektu blob i jego migawek wielu kont magazynu

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Po wykonaniu operacji kopiowania kontenera docelowego obejmuje obiektu blob i jego migawek. Kontener zawiera jego migawki i następujących obiektów blob:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Synchronicznie skopiować wielu kont magazynu obiektów blob
Narzędzie AzCopy domyślnie kopiuje dane między dwoma punktami końcowymi magazynu asynchronicznie. W związku z tym jest kopiowana uruchamia operację kopiowania w tle przy użyciu pojemności nieużywanej przepustowości ma umowy dotyczącej poziomu usług pod względem sposobu szybkiego obiektu blob. 

`--sync-copy` Opcja zapewnia, że operacji kopiowania pobiera spójne szybkości. Narzędzie AzCopy wykonuje synchroniczne kopiowania pobieranie obiektów blob można skopiować z określonego źródła do pamięci lokalnej, a następnie przekazywanie ich do miejsce docelowe magazynu obiektów Blob.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` może generować dodatkowe wyjście koszt w porównaniu do kopiowania asynchronicznego. Zalecanym podejściem jest użycie tej opcji w maszynie Wirtualnej platformy Azure, który znajduje się w tym samym regionie co konta magazynu źródłowego, aby uniknąć kosztów wyjście.

## <a name="file-download"></a>Plik: Pobierz
### <a name="download-single-file"></a>Pobierz pojedynczy plik

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Jeśli określone źródło jest udział plików na platformę Azure, a następnie podaj nazwę pliku dokładną (*np.* `abc.txt`) do pobrania pojedynczy plik, lub określ opcję `--recursive` Aby pobrać wszystkie pliki w udziale rekursywnie. Podjęto próbę Określ wzorzec pliku i opcji `--recursive` razem powoduje błąd.

### <a name="download-all-files"></a>Pobierz wszystkie pliki

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Należy pamiętać, że puste foldery nie zostaną pobrane.

## <a name="file-upload"></a>Plik: Przekaż
### <a name="upload-single-file"></a>Przekaż pojedynczy plik

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Przekaż wszystkie pliki

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Należy pamiętać, że wszystkie puste foldery nie zostały przekazane.

### <a name="upload-files-matching-specified-pattern"></a>Przekazywanie plików zgodnych z określonym wzorcem

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Plik: kopiowania
### <a name="copy-across-file-shares"></a>Kopiowanie między udziałami plików

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Podczas kopiowania plików między udziałami plików [po stronie serwera kopii](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-from-file-share-to-blob"></a>Kopiowanie z udziału plików do obiektu blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Podczas kopiowania pliku z udziału plików do obiektów blob, [po stronie serwera kopii](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-from-blob-to-file-share"></a>Kopiowanie z obiektu blob do udziału plików

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Podczas kopiowania pliku z obiektu blob do udziału plików, [po stronie serwera kopii](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="synchronously-copy-files"></a>Synchronicznie kopiować pliki
Można określić `--sync-copy` opcję, aby skopiować dane z pliku magazynu do przechowywania plików, z pliku magazynu do magazynu obiektów Blob i z magazynu obiektów Blob do przechowywania plików synchronicznie. Narzędzie AzCopy uruchamia tej operacji pobierania danych źródłowych do pamięci lokalnej, a następnie przekazać go do lokalizacji docelowej. W tym przypadku standardowe wyjście koszt dotyczy.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Podczas kopiowania z pliku magazynu do magazynu obiektów Blob, domyślnym typem obiektu blob jest blokowych obiektów blob, użytkownik może określić opcję `--blob-type page` zmienić typ docelowego obiektu blob. Dostępne typy to `page | block | append`.

Należy pamiętać, że `--sync-copy` może generować wyjście dodatkowych kosztów porównanie kopii asynchroniczne. Zalecanym podejściem jest użycie tej opcji w maszynie Wirtualnej platformy Azure, który znajduje się w tym samym regionie co konta magazynu źródłowego, aby uniknąć kosztów wyjście.

## <a name="other-azcopy-features"></a>Inne funkcje AzCopy
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Tylko skopiować dane, które nie istnieje w docelowym
`--exclude-older` i `--exclude-newer` parametry umożliwiają Wyklucz zasoby starszej lub nowszej źródła zapobiega kopiowaniu odpowiednio. Jeśli chcesz skopiować źródła zasobów, które nie istnieje w docelowym, można określić oba parametry polecenia AzCopy:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Aby określić parametry wiersza polecenia przy użyciu pliku konfiguracji

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Parametry wiersza polecenia AzCopy można uwzględnić w pliku konfiguracji. Narzędzie AzCopy przetwarza parametrów w pliku tak, jakby były one określone w wierszu polecenia wykonywanie bezpośrednich podstawienia z zawartością pliku.

Załóżmy plik konfiguracji o nazwie `copyoperation`, który zawiera następujące wiersze. Każdy parametr narzędzia AzCopy można określić w jednym wierszu.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

lub na oddzielnych wierszach:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

Narzędzie AzCopy wynik negatywny, jeśli parametr można podzielić na dwa wiersze, jak pokazano poniżej, aby uzyskać `--source-key` parametru:

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Określ sygnatury dostępu współdzielonego (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-sas <SAS1> \
    --dest-sas <SAS2>
```

Można również określić sygnatury dostępu Współdzielonego w kontenerze identyfikatora URI:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Folder plików dziennika
Zawsze, gdy wydać polecenie do narzędzia AzCopy, sprawdza czy istnieje w pliku dziennika w domyślnym folderze, lub czy istnieje w folderze określonej za pomocą tej opcji. Jeśli plik dziennika nie istnieje w każdym miejscu, AzCopy traktuje jako nowe działanie i generuje nowy plik dziennika.

Jeśli plik dziennika istnieje, narzędzie AzCopy sprawdza, czy wiersz polecenia, który możesz wpisać zgodny wiersz polecenia w pliku dziennika. Jeśli dwa wiersze poleceń są zgodne, AzCopy wznawia działanie niekompletne. Jeśli nie są zgodne, AzCopy monituje użytkownika albo zastąpienie pliku dziennika można uruchomić nowej operacji lub Anuluj bieżącą operację.

Jeśli chcesz użyć domyślnej lokalizacji pliku dziennika:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Jeśli pominięto opcję `--resume`, lub określ opcję `--resume` bez ścieżkę do folderu, jak pokazano powyżej, AzCopy tworzy plik dziennika w lokalizacji domyślnej, która jest `~\Microsoft\Azure\AzCopy`. Jeśli istnieje już plik dziennika, AzCopy wznawia działanie na podstawie pliku dziennika.

Jeśli chcesz określić niestandardową lokalizację pliku dziennika:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

W tym przykładzie powoduje utworzenie pliku dziennika, jeśli jeszcze nie istnieje. Jeśli istnieje, narzędzie AzCopy wznawia działanie na podstawie pliku dziennika.

Jeśli chcesz wznowić działanie narzędzia AzCopy, powtórz tego samego polecenia. Narzędzie AzCopy w systemie Linux następnie wyświetli monit o potwierdzenie:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Dzienniki pełne dane wyjściowe

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Określ liczbę jednoczesnych operacji można uruchomić
Opcja `--parallel-level` określa liczbę jednoczesnych kopii operacji. Domyślnie narzędzie AzCopy uruchamia określonej liczby jednoczesnych operacji w celu zwiększenia przepływności transferu danych. Liczba jednoczesnych operacji jest równa osiem razy liczba procesorów masz. Jeśli używasz narzędzia AzCopy w niskiej przepustowości sieci, można określić niższą dla — poziom równolegle w celu uniknięcia błąd spowodowany konkurencji zasobów.

>[!TIP]
>Aby wyświetlić pełną listę parametrów narzędzia AzCopy, zapoznaj się "azcopy--pomocy" menu.

## <a name="installation-steps-for-azcopy-71-and-earlier-versions"></a>Procedura instalacji AzCopy 7.1 i wcześniejszych wersjach

Narzędzie AzCopy w systemie Linux (v7.1 i wcześniej tylko) wymaga platformy .NET Core. Instrukcje dotyczące instalacji są dostępne na [instalacji platformy .NET Core](https://www.microsoft.com/net/core#linuxubuntu) strony.

Na przykład Rozpocznij od zainstalowania platformy .NET Core na Ubuntu 16.10. Najnowszy przewodnik instalacji można znaleźć [.NET Core w systemie Linux](https://www.microsoft.com/net/core#linuxubuntu) strona instalacji.


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.0
```

Po zainstalowaniu platformy .NET Core, Pobierz i zainstaluj narzędzia AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Po zainstalowaniu narzędzia AzCopy w systemie Linux, możesz usunąć wyodrębnione pliki. Alternatywnie, jeśli nie masz uprawnień administratora, można również uruchomić `azcopy` za pomocą narzędzia azcopy skryptu powłoki w folderze wyodrębnione.

## <a name="known-issues-and-best-practices"></a>Znane problemy i najlepsze rozwiązania
### <a name="error-installing-azcopy"></a>Błąd podczas instalowania narzędzia AzCopy
Jeśli wystąpią problemy z instalacją AzCopy może zostanie podjęta próba uruchomienia narzędzia AzCopy przy użyciu skryptu bash w wyodrębnionego `azcopy` folderu.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Limit równoczesnych zapisów podczas kopiowania danych
Podczas kopiowania obiektów blob lub pliki z narzędzia AzCopy, należy pamiętać, że inna aplikacja może modyfikować danych podczas kopiowania go. Jeśli to możliwe upewnij się, że dane, które są kopiowane nie jest modyfikowana podczas operacji kopiowania. Na przykład podczas kopiowania wirtualnego dysku twardego skojarzonego z maszyny wirtualnej platformy Azure, upewnij się, że żadne inne aplikacje są obecnie zapisywania wirtualnego dysku twardego. Dobrym sposobem na to jest dzierżawa zasobów do skopiowania. Alternatywnie można najpierw Utwórz migawkę wirtualnego dysku twardego, a następnie skopiuj migawki.

Jeśli nie można zapobiec inne aplikacje, zapisywania do obiektów blob lub plików, gdy są kopiowane, należy pamiętać, że w czasie, który kończy zadanie, kopiowanych zasobów może nie masz już pełna parzystości z zasobami źródła.

### <a name="running-multiple-azcopy-processes"></a>Uruchamianie wielu procesów AzCopy
Można uruchamiać wiele procesów AzCopy na jednego klienta, zapewniając użycie różnych dziennika folderów. Używanie folderu dziennika jednego dla wielu procesów AzCopy nie jest obsługiwane.

1. proces:
```azcopy
azcopy \
    --source /mnt/myfiles1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles1 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal1"
```

proces 2:
```azcopy
azcopy \
    --source /mnt/myfiles2 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles2 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal2"
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat usługi Azure Storage i narzędzia AzCopy, zapoznaj się z następującymi zasobami:

### <a name="azure-storage-documentation"></a>Dokumentacja magazynu Azure:
* [Wprowadzenie do usługi Azure Storage](../storage-introduction.md)
* [Tworzenie konta magazynu](../storage-create-storage-account.md)
* [Zarządzanie obiektami blob za pomocą Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Za pomocą usługi Azure CLI 2.0 z usługą Azure Storage](../storage-azure-cli.md)
* [Jak używać magazynu obiektów Blob w języku C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Używanie usługi Blob Storage w języku Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Używanie usługi Blob Storage w oprogramowaniu Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Używanie usługi Blob Storage w języku Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Wpisy blogu magazynu Azure:
* [Anonsowanie AzCopy w wersji zapoznawczej systemu Linux](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)
* [Wprowadzenie Podgląd Biblioteka przepływu danych usługi Azure Storage](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Wprowadzenie do kopiowania synchroniczne i dostosowane typ zawartości](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [Narzędzie AzCopy: Announcing ogólne dostępności 3.0 AzCopy oraz wersji zapoznawczej AzCopy 4.0 z tabeli i plik pomocy technicznej](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [Narzędzie AzCopy: Zoptymalizowana pod kątem scenariuszy kopii na dużą skalę](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Obsługa dostęp do odczytu magazynu geograficznie nadmiarowego](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [Narzędzie AzCopy: Transfer danych za pomocą trybu ponownego uruchamiania i tokenu sygnatury dostępu Współdzielonego](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [Narzędzia AzCopy: Blob kopiowania między konta przy użyciu](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [Narzędzie AzCopy: Przekazywanie pobieranie plików dla obiektów blob Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

