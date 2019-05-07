---
title: Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą narzędzia AzCopy w systemie Linux | Dokumentacja firmy Microsoft
description: Narzędzia AzCopy można użyć w narzędzie systemu Linux, do przeniesienia lub skopiowania danych do lub z obiektów blob i zawartości pliku. Kopiowanie danych do usługi Azure Storage z lokalnych plików lub kopiowania danych w ramach lub między kontami magazynu. Łatwo Migruj dane do usługi Azure Storage.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 9e97171d493e452f18bf805defad7ef6f480af0b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149016"
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Transferowanie danych za pomocą narzędzia AzCopy w systemie Linux

AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych z magazynu obiektów Blob Azure firmy Microsoft i plików, przy użyciu prostych poleceń zaprojektowane pod kątem optymalnej wydajności. Dane można kopiować między systemem plików i kontem magazynu lub między kontami magazynu.  

> [!IMPORTANT]
> W tym artykule opisano starszą wersję narzędzia AzCopy.
>Aby zainstalować najnowszą wersję narzędzia AzCopy, zobacz [AzCopy v10](storage-use-azcopy-v10.md).

Jeśli zdecydujesz się zainstalować starszą wersję programu AzCopy (AzCopy v8.1), następnie istnieje wiele wersji narzędzia AzCopy w wersji 8.1, którą można pobrać. Narzędzie AzCopy w systemie Linux jest przeznaczony dla platform Linux, oferuje opcje wiersza polecenia w stylu POSIX. [Narzędzie AzCopy w Windows](../storage-use-azcopy.md) oferuje Windows style opcje wiersza polecenia. W tym artykule opisano narzędzia AzCopy w systemie Linux. 

> [!NOTE]  
> Począwszy od wersji AzCopy 7.2 zależności platformy .NET Core, są dostarczane przy użyciu pakietu Narzędzia AzCopy. Jeśli używasz wersji w wersji 7.2 lub później, nie potrzebujesz już do zainstalowania platformy .NET Core to wymaganie wstępne.

## <a name="download-and-install-azcopy"></a>Pobierz i zainstaluj narzędzia AzCopy

### <a name="installation-on-linux"></a>Instalacja w systemie Linux

> [!NOTE]
> Może być konieczne zainstalowanie zależności platformy .NET Core 2.1 wyróżnione w tym [artykułu warunków wstępnych programu .NET Core](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) w zależności od Twojej dystrybucji. 
>
> Dystrybucje systemu RHEL 7 należy zainstalować ICU i libunwind zależności: ```yum install -y libunwind icu```

Instalowanie narzędzia AzCopy w systemie Linux (v7.2 lub nowsza) jest równie proste jak wyodrębnianie pakietu tar i uruchomienie skryptu instalacji. 

**Dystrybucje oparte na RHEL 6**: [link pobierania](https://aka.ms/downloadazcopylinuxrhel6)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinuxrhel6
tar -xf azcopy.tar.gz
sudo ./install.sh
```

**Inne dystrybucje systemu Linux**: [link pobierania](https://aka.ms/downloadazcopylinux64)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Po zainstalowaniu narzędzia AzCopy w systemie Linux, możesz usunąć wyodrębnionych plików. Alternatywnie, jeśli nie masz uprawnienia superużytkownika można również uruchomić `azcopy` za pomocą narzędzia azcopy skrypt powłoki w wyodrębnionego folderu.

### <a name="alternative-installation-on-ubuntu"></a>Alternatywne instalacji w systemie Ubuntu

**Ubuntu 14.04**

Dodawanie źródła apt repozytorium produktu firmy Microsoft, Linux i zainstalować narzędzia AzCopy:

```bash
sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod/ trusty main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

Dodawanie źródła apt repozytorium produktu firmy Microsoft, Linux i zainstalować narzędzia AzCopy:

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod/ xenial main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>Pisanie pierwszego polecenia narzędzia AzCopy
Podstawowa składnia polecenia narzędzia AzCopy jest:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

W poniższych przykładach pokazano różne scenariusze do kopiowania danych z obiektów blob systemu Azure firmy Microsoft i plików. Zapoznaj się `azcopy --help` menu, aby uzyskać szczegółowy opis parametrów użytych w każdym przykładzie.

## <a name="blob-download"></a>Blob: Do pobrania
### <a name="download-single-blob"></a>Pobieranie pojedynczego obiektu blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Jeśli folder `/mnt/myfiles` nie istnieje, narzędzie AzCopy utworzy go i pobiera `abc.txt` do nowego folderu. 

### <a name="download-single-blob-from-secondary-region"></a>Pobierz pojedynczy obiekt blob z regionu pomocniczego

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

Przyjęto założenie, że następujących obiektów blob znajdują się w określonym kontenerze:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

Po zakończeniu operacji pobierania katalogu `/mnt/myfiles` zawiera następujące pliki:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Jeśli nie określisz opcji `--recursive`, zostaną pobrane nie obiektu blob.

### <a name="download-blobs-with-specified-prefix"></a>Pobieranie obiektów blob za pomocą określonego prefiksu

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Załóżmy, że następujących obiektów blob znajdują się w określonym kontenerze. Wszystkie obiekty BLOB rozpoczynający się od prefiksu `a` zostaną pobrane.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

Po zakończeniu operacji pobierania folderu `/mnt/myfiles` zawiera następujące pliki:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

Prefiks mają zastosowanie do katalogu wirtualnego, który stanowi pierwszą część nazwy obiektu blob. W powyższym przykładzie katalog wirtualny jest niezgodny określonego prefiksu, nie obiektu blob jest pobierany. Ponadto jeśli opcja `--recursive` nie zostanie określony, narzędzie AzCopy nie pobiera wszystkie obiekty BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Ustaw czas ostatniej modyfikacji wyeksportowany plik, aby była taka sama jak obiekty BLOB źródła

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Można również wykluczyć obiekty BLOB z operacji pobierania, w oparciu o czas ostatniej modyfikacji. Na przykład, jeśli chcesz wykluczyć obiekty BLOB, w których ostatni czas modyfikacji jest tą samą lub nowszą niż plik docelowy, Dodaj `--exclude-newer` opcji:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Lub jeśli chcesz wykluczyć obiekty BLOB, w których ostatni czas modyfikacji tego samego lub starszy niż plik docelowy, Dodaj `--exclude-older` opcji:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Blob: Upload
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

Jeśli określony katalog wirtualny nie istnieje, narzędzie AzCopy przekazuje plik do dołączenia katalogu wirtualnego w nazwie obiektu blob (*np.*, `vd/abc.txt` w powyższym przykładzie).

### <a name="redirect-from-stdin"></a>Przekieruj ze stdin

```azcopy
gzip myarchive.tar -c | azcopy \
    --destination https://myaccount.blob.core.windows.net/mycontainer/mydir/myarchive.tar.gz \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Przekazywanie wszystkich plików

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Określenie opcji `--recursive` przesyła zawartość określonego katalogu do rekursywnie magazynu obiektów Blob, co oznacza, że wszystkie podfoldery i pliki również zostaną przekazane. Na przykład załóżmy następujące pliki znajdują się w folderze `/mnt/myfiles`:

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

Gdy opcja `--recursive` nie zostanie określony, zostaną przekazane następujące trzy pliki:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Przekaż pliki zgodne z określonym wzorcem

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

Gdy opcja `--recursive` nie zostanie określony, narzędzie AzCopy pomija plików znajdujących się w katalogach podrzędnych:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Określ typ zawartości MIME docelowego obiektu blob
Domyślnie narzędzie AzCopy ustawia typ zawartości do docelowego obiektu blob `application/octet-stream`. Jednak jawnie określić typ zawartości za pomocą opcji `--set-content-type [content-type]`. Ta składnia ustawia typ zawartości dla wszystkich obiektów blob w operacji przekazywania.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Jeśli opcja `--set-content-type` jest określony bez wartości, a następnie ustawia narzędzia AzCopy, każdy obiekt blob lub typ zawartości pliku, zgodnie z jego rozszerzenie pliku.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

### <a name="customizing-the-mime-content-type-mapping"></a>Dostosowywanie mapowania typ zawartości MIME
Narzędzie AzCopy używa pliku konfiguracji, który zawiera mapowanie rozszerzenia pliku, typu zawartości. Można dostosować to mapowanie i dodać nowe pary, zgodnie z potrzebami. Mapowanie znajduje się w  ```/usr/lib/azcopy/AzCopyConfig.json```

## <a name="blob-copy"></a>Blob: Copy
### <a name="copy-single-blob-within-storage-account"></a>Skopiuj jeden obiekt blob w ramach konta magazynu

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Podczas kopiowania obiektu blob bez — opcja kopia synchroniczna [kopiowania po stronie serwera](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-single-blob-across-storage-accounts"></a>Skopiuj jeden obiekt blob na kontach magazynu

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Podczas kopiowania obiektu blob bez — opcja kopia synchroniczna [kopiowania po stronie serwera](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Skopiuj jeden obiekt blob z regionu pomocniczego do regionu podstawowego

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Należy pamiętać, że użytkownik musi mieć dostęp do odczytu magazynu geograficznie nadmiarowego włączone.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Skopiuj jeden obiekt blob i jego migawek między kontami magazynu

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Po zakończeniu operacji kopiowania kontener docelowy zawiera obiekt blob i jego migawek. Kontener zawiera następujący obiekt blob i jego migawek:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Synchronicznie kopiować obiekty BLOB na kontach magazynu
Narzędzie AzCopy domyślnie kopiuje dane między dwoma punktami końcowymi magazynu asynchronicznie. W związku z tym uruchamia operację kopiowania w tle przy użyciu pojemności nieużywanej przepustowości, która ma pod względem sposobu szybkiego obiektu blob umowa SLA nie jest kopiowany. 

`--sync-copy` Opcja gwarantuje, że operacji kopiowania pobiera szybkość spójne. Narzędzie AzCopy wykonuje synchroniczne kopiowania pobieranie obiektów blob, aby skopiować pochodzących z określonego źródła do pamięci lokalnej, a następnie przekazaniu ich do docelowego magazynu obiektów Blob.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` może generować koszty dodatkowe ruchu wychodzącego w porównaniu z kopiowania asynchronicznego. Zalecanym podejściem jest użycie tej opcji w Maszynie wirtualnej platformy Azure, który znajduje się w tym samym regionie, co źródłowego konta magazynu w celu uniknięcia koszty ruchu wychodzącego.

## <a name="file-download"></a>Plik: Do pobrania
### <a name="download-single-file"></a>Pobierz jeden plik

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Jeśli określone źródło udziału plików platformy Azure, wówczas należy albo określić dokładnej nazwy pliku, (*np.* `abc.txt`) do pobrania jednego pliku lub wybierz opcję `--recursive` można pobrać wszystkich plików w lokalizacji udziału. Podjęto próbę Określ wzorzec pliku i opcji `--recursive` razem będzie skutkowało błędem.

### <a name="download-all-files"></a>Pobierz wszystkie pliki

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Należy zauważyć, że wszelkie puste foldery nie są pobierane.

## <a name="file-upload"></a>Plik: Upload
### <a name="upload-single-file"></a>Przekaż pojedynczy plik

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Przekazywanie wszystkich plików

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Należy pamiętać, że wszelkie puste foldery nie są przekazywane.

### <a name="upload-files-matching-specified-pattern"></a>Przekaż pliki zgodne z określonym wzorcem

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Plik: Copy
### <a name="copy-across-file-shares"></a>Kopiowanie między udziałami plików

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Podczas kopiowania plików między udziałami plików [kopiowania po stronie serwera](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-from-file-share-to-blob"></a>Kopiowanie z udziału plików do obiektu blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Podczas kopiowania pliku z udziału plików do obiektów blob, [kopiowania po stronie serwera](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="copy-from-blob-to-file-share"></a>Kopiowanie z obiektu blob do udziału plików

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Podczas kopiowania pliku z magazynu obiektów blob do udziału plików [kopiowania po stronie serwera](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operacja została wykonana.

### <a name="synchronously-copy-files"></a>Synchronicznie kopiowanie plików
Można określić `--sync-copy` opcję, aby skopiować dane z usługi File Storage do usługi File Storage z usługi File Storage do usługi Blob Storage i z magazynu obiektów Blob do przechowywania plików synchronicznie. Narzędzie AzCopy działa ta operacja pobierania danych źródłowych do pamięci lokalnej, a następnie przekazać go do miejsca docelowego. W tym przypadku standardowe wyjście koszt dotyczy.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Podczas kopiowania z usługi File Storage do magazynu obiektów Blob, domyślnym typem obiektów blob jest blokowy obiekt blob, użytkownik może określić opcję `--blob-type page` zmienić docelowy typ obiektu blob. Dostępne typy to `page | block | append`.

Należy pamiętać, że `--sync-copy` może generować wyjście dodatkowych kosztów, porównywanie asynchronicznych kopii. Zalecanym podejściem jest użycie tej opcji w Maszynie wirtualnej platformy Azure, który znajduje się w tym samym regionie, co źródłowego konta magazynu w celu uniknięcia koszty ruchu wychodzącego.

## <a name="other-azcopy-features"></a>Inne funkcje programu AzCopy
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Kopiuj tylko te dane, które nie istnieje w miejscu docelowym
`--exclude-older` i `--exclude-newer` parametry umożliwiają Wyklucz zasoby źródłowe starszego lub nowszego kopiowaniu, odpowiednio. Jeśli chcesz skopiować zasoby źródłowe, które nie istnieją w miejscu docelowym, można określić obu parametrów w poleceniu narzędzia AzCopy:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Użyj pliku konfiguracji, aby określić parametry wiersza polecenia

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Parametry wiersza polecenia narzędzia AzCopy można uwzględnić w pliku konfiguracji. Narzędzie AzCopy przetwarza parametry w pliku tak, jakby były one określone w wierszu polecenia wykonywania bezpośrednie podstawianie przy użyciu zawartości pliku.

Przyjęto założenie, plik konfiguracji o nazwie `copyoperation`, który zawiera następujące wiersze. Każdy parametr narzędzia AzCopy można określić w jednym wierszu.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

lub w postaci odrębnych, wiersze:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

Narzędzie AzCopy nie działa, jeśli parametr można podzielić na dwa wiersze, jak pokazano poniżej, aby uzyskać `--source-key` parametru:

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

Można również określić sygnatury dostępu Współdzielonego dla kontenera identyfikatora URI:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Folder plików dziennika
Każdorazowo, gdy wydać polecenie do narzędzia AzCopy, sprawdza czy istnieje plik dziennika w folderze domyślnym, lub czy istnieje w folderze, który określono za pomocą tej opcji. Jeśli plik dziennika nie istnieje w dowolnym miejscu, narzędzia AzCopy traktuje operacji jako nowe i generuje nowy plik dziennika.

Jeśli plik dziennika istnieje, narzędzie AzCopy sprawdza, czy wiersza polecenia, które należy wprowadzić pasuje do wiersza polecenia w pliku dziennika. Jeśli dwa wiersze poleceń są zgodne, narzędzia AzCopy Wznawia Nieukończona operacja. Jeśli nie są zgodne, narzędzia AzCopy monituje użytkownika albo zastąpienia pliku dziennika, można uruchomić nowej operacji lub Anuluj bieżącą operację.

Jeśli chcesz użyć domyślnej lokalizacji pliku dziennika:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Jeżeli pominięto opcję `--resume`, lub wybierz opcję `--resume` bez ścieżki folderu, jak pokazano powyżej, narzędzia AzCopy tworzy plik dziennika w lokalizacji domyślnej, która jest `~\Microsoft\Azure\AzCopy`. Jeśli plik dziennika już istnieje, narzędzia AzCopy wznawia działanie na podstawie pliku dziennika.

Jeśli chcesz określić niestandardową lokalizację pliku dziennika:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Ten przykład tworzy plik dziennika, jeśli jeszcze nie istnieje. Jeśli istnieje, narzędzie AzCopy wznawia działanie na podstawie pliku dziennika.

Jeśli chcesz wznowić działanie narzędzia AzCopy, powtórz to samo polecenie. Narzędzie AzCopy w systemie Linux następnie wyświetli monit o potwierdzenie:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Dzienniki pełnych danych wyjściowych

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Określ liczbę jednoczesnych operacji, aby rozpocząć
Opcja `--parallel-level` określa liczby operacji jednoczesnych kopii. Domyślnie narzędzie AzCopy uruchamia niektórych liczby operacji jednoczesnych, aby zwiększyć przepływność transferu danych. Liczba równoczesnych operacji jest równy 8 razy liczba procesorów, masz. Jeśli używasz narzędzia AzCopy za pośrednictwem sieci o niskiej przepustowości, można określić niższy numer dla — poziom równolegle w celu uniknięcia błąd spowodowany konkurencji zasobów.

>[!TIP]
>Aby wyświetlić pełną listę parametrów narzędzia AzCopy, zapoznaj się z "narzędzia azcopy--help" menu.

## <a name="installation-steps-for-azcopy-71-and-earlier-versions"></a>Kroki instalacji narzędzia AzCopy 7.1 i wcześniejszych wersji

Narzędzie AzCopy w systemie Linux (wersji 7.1 i wcześniej tylko) wymaga platformy .NET Core. Instrukcje dotyczące instalacji są dostępne na [instalacji platformy .NET Core](https://www.microsoft.com/net/core#linuxubuntu) strony.

Na przykład Rozpocznij od instalacją programu .NET Core w systemie Ubuntu 16.10. Najnowszy przewodnik instalacji można znaleźć [platformy .NET Core w systemie Linux](https://www.microsoft.com/net/core#linuxubuntu) strona instalacji.


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

Po zainstalowaniu narzędzia AzCopy w systemie Linux, możesz usunąć wyodrębnionych plików. Alternatywnie Jeśli nie masz uprawnień administratora, można również uruchomić `azcopy` za pomocą narzędzia azcopy skrypt powłoki w wyodrębnionego folderu.

## <a name="known-issues-and-best-practices"></a>Znane problemy i najlepsze rozwiązania
### <a name="error-installing-azcopy"></a>Błąd podczas instalowania programu AzCopy
Jeśli wystąpią problemy z instalacją programu AzCopy, możesz spróbować do uruchamiania narzędzia AzCopy, za pomocą skryptu powłoki systemowej w wyodrębnionych `azcopy` folderu.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Limit współbieżne operacje podczas kopiowania danych
Podczas kopiowania obiektów blob lub plików za pomocą narzędzia AzCopy, należy pamiętać o tym, że inna aplikacja może być modyfikowania danych podczas kopiowania go. Jeśli to możliwe upewnij się, że dane, które są kopiowane nie jest modyfikowana podczas operacji kopiowania. Na przykład podczas kopiowania wirtualnego dysku twardego skojarzonego z maszyną wirtualną platformy Azure, upewnij się, że żadne inne aplikacje są obecnie zapisywania wirtualnego dysku twardego. Dobrym sposobem na to jest dzierżawa zasobu, który ma być skopiowany. Alternatywnie można najpierw utworzyć migawki wirtualnego dysku twardego, a następnie skopiuj migawki.

Jeśli nie uniemożliwiają innych aplikacji zapisywanie do obiektów blob lub pliki podczas są kopiowane, następnie należy pamiętać o tym, czy do czasu zakończenia zadania, skopiowanych zasobów może nie masz już pełną parzystości z zasobami źródła.

### <a name="running-multiple-azcopy-processes"></a>Uruchamianie wielu procesów programu AzCopy
Wiele procesów AzCopy mogą być uruchamiane w pojedynczego klienta, zapewniając, że używasz innego arkusza folderów. Za pomocą folderu pojedynczego dziennika dla wielu procesów narzędzie AzCopy nie jest obsługiwana.

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

### <a name="azure-storage-documentation"></a>Dokumentacja usługi Azure Storage:
* [Wprowadzenie do usługi Azure Storage](../storage-introduction.md)
* [Tworzenie konta magazynu](../storage-create-storage-account.md)
* [Zarządzanie obiektami blob za pomocą Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Używanie interfejsu wiersza polecenia platformy Azure z usługą Azure Storage](../storage-azure-cli.md)
* [Jak używać magazynu obiektów Blob w języku C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Używanie usługi Blob Storage w języku Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Używanie usługi Blob Storage w oprogramowaniu Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Używanie usługi Blob Storage w języku Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Wpisy blogu magazynu platformy Azure:
* [Ogłoszenie narzędzie AzCopy w systemie Linux (wersja zapoznawcza)](https://azure.microsoft.com/blog/announcing-azcopy-on-linux-preview/)
* [Wprowadzenie do usługi Azure Storage Data przenoszenia Library w wersji zapoznawczej](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Wprowadzenie do kopii synchroniczne i dostosowanego typu zawartości](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Ogłoszenie ogólnego dostępności 3.0 narzędzie AzCopy oraz wersję narzędzia AzCopy 4.0, tabela i plik pomocy technicznej w wersji zapoznawczej](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Zoptymalizowane pod kątem scenariuszy kopiowania na dużą skalę](https://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Obsługa magazynu geograficznie nadmiarowego do odczytu](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transferowanie danych za pomocą trybie ponownego uruchamiania i tokenu sygnatury dostępu Współdzielonego](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Przy użyciu obiektu Blob kopiowania między konta](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Trwa przekazywanie/pobieranie plików obiektów blob platformy Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
