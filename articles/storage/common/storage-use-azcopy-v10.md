---
title: Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą narzędzia AzCopy v10 (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Użyj AzCopy v10 (wersja zapoznawcza) narzędzia wiersza polecenia do przeniesienia lub skopiowania danych do lub z obiektu blob, usługa data lake i zawartości pliku. Kopiowanie danych do usługi Azure Storage z lokalnych plików lub kopiowania danych w ramach lub między kontami magazynu. Łatwo Migruj dane do usługi Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: ffd448db86c8658619da5339cd34eb9dba7e05ce
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278432"
---
# <a name="transfer-data-with-azcopy-v10-preview"></a>Transferowanie danych za pomocą narzędzia AzCopy v10 (wersja zapoznawcza)

Narzędzie AzCopy v10 (wersja zapoznawcza) to narzędzie wiersza polecenia do kopiowania danych do / z magazynu obiektów Blob Azure firmy Microsoft i plików. Narzędzie AzCopy v10 oferuje przeprojektowany interfejs wiersza polecenia i transferuje nowej architektury wiarygodnych danych. Za pomocą narzędzia AzCopy, ale dane można kopiować między systemem plików i konto magazynu lub między kontami magazynu.

## <a name="whats-new-in-azcopy-v10"></a>What's new in v10 narzędzia AzCopy

- Synchronizuje systemy plików w usłudze Azure Blob storage ani odwrotnie. Użyj `azcopy sync <source> <destination>`. Idealne rozwiązanie dla scenariuszy kopiowania przyrostowego.
- Obsługuje interfejsy API usługi Azure Data Lake Storage Gen2. Użyj `myaccount.dfs.core.windows.net` jako identyfikator URI do wywoływania interfejsów API programu Data Lake Storage Gen2.
- Obsługuje kopiowanie całego konta (tylko w przypadku usługi obiektów Blob) do innego konta.
- Obsługuje kopiowanie danych z zasobnika Amazon S3 usługi sieci Web.
- Używa nowego [umieszczania bloku z adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) interfejsów API do obsługi kopię na konto. Transfer danych jest szybsza, ponieważ przesyłanie danych do klienta nie jest wymagana.
- Wyświetla lub usuwa pliki i obiekty BLOB w podanej ścieżce.
- Obsługuje wzorców symboli wieloznacznych w ścieżkę i — Wyklucz flag.
- Tworzy kolejność zadań oraz w pliku dziennika powiązane z każdego wystąpienia narzędzia AzCopy. Możesz wyświetlić i ponownie uruchomić poprzedniego zadania i wznowić zadania zakończone niepowodzeniem. Narzędzie AzCopy również automatycznie ponowi próbę transferu po awarii.
- Ulepszenia ogólnej wydajności funkcji.

## <a name="download-and-install-azcopy"></a>Pobierz i zainstaluj narzędzia AzCopy

### <a name="latest-preview-version-v10"></a>Najnowszej wersji zapoznawczej (v10)

Pobierz najnowszą wersję zapoznawczą narzędzia AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (docelowy)
- [System MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>Najnowsza wersja produkcyjna (w wersji 8.1)

Pobierz [najnowszą wersję produkcyjną narzędzia AzCopy dla Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>Usługa Table storage (v7.3) obsługuje narzędzia AzCopy

Pobierz [v7.3 AzCopy obsługa kopiowania danych do i z usługi Microsoft Azure Table storage](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Kroki po instalacji

Narzędzie AzCopy v10 nie wymaga instalacji. Otwórz preferowaną aplikacji wiersza polecenia i przejdź do folderu, gdzie `azcopy.exe` znajduje się. Jeśli to konieczne, możesz dodać do ścieżki systemowej w celu ułatwienia lokalizacji folderu Narzędzia AzCopy.

## <a name="authentication-options"></a>Opcje uwierzytelniania

Podczas uwierzytelniania w usłudze Azure Storage, narzędzia AzCopy v10 obsługuje następujące opcje:
- **Usługa Azure Active Directory** (obsługiwane w przypadku **usług obiektów Blob i Data Lake Storage Gen2**). Użyj ```.\azcopy login``` zalogować się przy użyciu usługi Azure Active Directory.  Użytkownik powinien mieć [przypisaną rolę "Współautor danych obiektu Blob magazynu"](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) można zapisać do magazynu obiektów Blob przy użyciu uwierzytelniania usługi Azure Active Directory. W przypadku uwierzytelniania za pomocą tożsamości zarządzanych zasobów platformy Azure, użyj `azcopy login --identity`.
- **Udostępnione tokenów sygnatur dostępu [obsługiwane usługi obiektów Blob i plików]**. Dołącz token sygnatury (SAS) dostępu do ścieżki obiektu blob w wierszu polecenia z niego korzystać. Można generować tokeny sygnatur dostępu Współdzielonego za pomocą witryny Azure portal [Eksploratora usługi Storage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), lub innych wybranych przez siebie narzędzi. Aby uzyskać więcej informacji, zobacz [przykłady](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Wprowadzenie

> [!TIP]
> **Wolisz graficzny interfejs użytkownika?**
>
> [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), klienta stacjonarnego, która upraszcza zarządzanie danymi usługi Azure Storage, używa teraz narzędzia AzCopy, aby przyspieszyć transfer danych do i z usługi Azure Storage.
>
> Włącz narzędzie AzCopy w Eksploratorze usługi Storage w ramach **Podgląd** menu.
> ![Włącz narzędzia AzCopy jako aparat transferu w Eksploratorze usługi Azure Storage](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

Narzędzie AzCopy v10 ma własny udokumentowanego składnię. Po zalogowaniu się do usługi Azure Active Directory, ogólna składnia wygląda następująco:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D"
```

Poniżej przedstawiono, jak można uzyskać listę dostępnych poleceń:

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

Aby wyświetlić stronę pomocy i przykłady dla danego polecenia, uruchom następujące polecenie:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Tworzenie obiektu blob kontener lub udział plików 

**Utwórz kontener obiektów blob**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Tworzenie udziału plików**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Utwórz kontener obiektów blob za pomocą usługi Azure Data Lake Storage Gen2**

Po włączeniu hierarchiczne przestrzenie nazw w ramach konta magazynu obiektów Blob, można użyć następującego polecenia, aby utworzyć nowy kontener obiektów blob przekazywania plików.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Kopiowanie danych do usługi Azure Storage

Polecenie kopiowania do przenoszenia danych ze źródła do miejsca docelowego. Źródłowy lub docelowy może być Odp.:
- Lokalny system plików
- Usługa Azure Blob/wirtualnego katalogu/kontenera identyfikatora URI
- Usługa Azure identyfikator URI udziału plików/katalogów/plików
- Usługa Azure Data Lake Storage Gen2 systemu plików/katalogów/plików z identyfikatora URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Następujące polecenie przekazuje wszystkie pliki w folderze `C:\local\path` rekursywnie do kontenera `mycontainer1`, tworzenie `path` katalogu w kontenerze. Gdy `--put-md5` podano flagi, narzędzia AzCopy oblicza i przechowuje skrót md5 każdego pliku w `Content-md5` właściwości odpowiedniego obiektu blob na potrzeby późniejszego użycia.

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true --put-md5
```

Następujące polecenie przekazuje wszystkie pliki w folderze `C:\local\path` (bez recursing do podkatalogów) do kontenera `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --put-md5
```

Aby uzyskać więcej przykładów, użyj następującego polecenia:

```azcopy
.\azcopy cp -h
```

## <a name="copy-blob-data-between-two-storage-accounts"></a>Kopiowanie danych obiektów Blob między dwa konta magazynu

Kopiowanie danych między dwoma kontami magazynu używa [umieścić blok z adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) interfejsu API, a nie korzysta z przepustowości sieci maszyny klienta. Dane są kopiowane między dwoma serwerami usługi Azure Storage bezpośrednio, gdy narzędzie AzCopy jest po prostu organizuje operacji kopiowania. Ta opcja jest obecnie tylko dostępne dla magazynu obiektów Blob.

Aby skopiować wszystkie dane obiektów Blob między dwa konta magazynu, użyj następującego polecenia:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

Aby skopiować kontener obiektów Blob do innego kontenera obiektów Blob, użyj następującego polecenia:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/mycontainer/<sastoken>" "https://myotheraccount.blob.core.windows.net/mycontainer/<sastoken>" --recursive=true
```

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Kopiowanie obrazu dysku VHD do konta magazynu

Narzędzie AzCopy domyślnie przekazuje dane do blokowych obiektów blob. Aby przekazać pliki jako obiekty BLOB dołączania lub stronicowe obiekty BLOB, Użyj flagi `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

```azcopy
.\azcopy cp "C:\local\path\mydisk.vhd" "https://myotheraccount.blob.core.windows.net/mycontainer/mydisk.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Synchronizacja: przyrostowa kopia i delete (tylko w przypadku magazynu obiektów Blob)

Polecenie synchronizacji synchronizuje zawartość katalogu źródłowego do katalogu w miejscu docelowym, porównywanie nazwy pliku i Data ostatniej modyfikacji sygnatur czasowych. Ta operacja zawiera opcjonalne usunięcie pliki docelowe, jeśli te nie istnieją w źródle podczas `--delete-destination=prompt|true` podano flagi. Domyślne zachowanie dotyczące usuwania jest wyłączona. 

> [!NOTE] 
> Użyj `--delete-destination` flagi z rozwagą. Włącz [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkcji przed włączeniem zachowanie dotyczące usuwania synchronizację, aby zapobiec przypadkowym na Twoim koncie. 
>
> Gdy `--delete-destination` jest ustawiona na wartość true, narzędzia AzCopy spowoduje usunięcie plików, które nie istnieją w źródle z docelowego bez żadnych monitu dla użytkownika. Aby wyświetlić monit o potwierdzenie, należy użyć `--delete-destination=prompt`.

Aby zsynchronizować lokalnego systemu plików na konto magazynu, użyj następującego polecenia:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Można również synchronizować kontener obiektów blob do lokalnego systemu plików:

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

To polecenie synchronizuje przyrostowo źródła do miejsca docelowego, na podstawie ostatniej modyfikacji sygnatur czasowych. Jeśli dodawanie lub usuwanie pliku w źródle, narzędzia AzCopy v10 będzie się tak samo w miejscu docelowym. Przed usunięciem narzędzia AzCopy spowoduje wyświetlenie monitu o potwierdzenie.

## <a name="copy-data-from-amazon-web-services-aws-s3"></a>Kopiowanie danych z usługi Amazon Web Services (AWS) S3

Aby uwierzytelniać się przy użyciu zasobnika AWS S3, ustaw następujące zmienne środowiskowe:

```
# For Windows:
set AWS_ACCESS_KEY_ID=<your AWS access key>
set AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For Linux:
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For MacOS
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
```

Aby skopiować pakietu do kontenera obiektów Blob, należy wydać następujące polecenie:

```
.\azcopy cp "https://s3.amazonaws.com/mybucket" "https://myaccount.blob.core.windows.net/mycontainer?<sastoken>" --recursive
```

Szczegółowe informacje na temat kopiowania danych z usługi AWS S3 przy użyciu narzędzia AzCopy, zobacz stronę [tutaj](https://github.com/Azure/azure-storage-azcopy/wiki/Copy-from-AWS-S3).

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

### <a name="configure-proxy-settings"></a>Skonfiguruj ustawienia serwera proxy

Aby skonfigurować ustawienia serwera proxy do v10 narzędzia AzCopy, należy ustawić https_proxy zmiennych środowiskowych za pomocą następującego polecenia:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Optymalizowanie przepływności

Ustaw zmienną środowiskową AZCOPY_CONCURRENCY_VALUE, aby skonfigurować liczbę współbieżnych żądań, a także do kontrolowania użycia przepustowości wydajności i zasobów. Wartość jest domyślnie do 300. Zmniejszenie wartości ograniczy przepustowość i użycie Procesora przez narzędzie AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>Zmień lokalizację plików dziennika

Możesz zmienić lokalizację plików dziennika, jeśli to konieczne, lub aby zapobiec przepełnieniu dysku systemu operacyjnego.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>Zmień domyślny poziom rejestrowania 

Domyślnie poziom dziennika narzędzia AzCopy jest ustawiony na INFO. Jeśli chcesz zmniejszyć poziom szczegółowości dziennika, aby zaoszczędzić miejsce na dysku, należy zastąpić ustawienie przy użyciu ``--log-level`` opcji. Poziomy dziennika dostępne są następujące: DEBUGOWANIA, informacje, ostrzeżenie, błąd, PANIKĘ i błąd krytyczny.

### <a name="review-the-logs-for-errors"></a>Przejrzyj dzienniki błędów

Następujące polecenie pobierze wszystkie błędy ze stanem UPLOADFAILED z dziennika 04dc9ca9-158f-7945-5933-564021086c79:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>Rozwiązywanie problemów

Narzędzie AzCopy v10 tworzy pliki dziennika i plan dla każdego zadania. Dzienniki można użyć, aby zbadać i rozwiązać wszelkie potencjalne problemy. Dzienniki będą zawierać stanu błędu (UPLOADFAILED COPYFAILED i DOWNLOADFAILED), pełną ścieżkę i przyczynę błędu. Plan plików i dzienników zadań znajdują się w folderze % USERPROFILE\\.azcopy folder Windows lub $HOME\\.azcopy folderu na komputerach Mac i Linux.

> [!IMPORTANT]
> Podczas przesyłania żądania do firmy Microsoft Support (lub tego problemu, obejmujące żadnym podmiotom trzecim), udostępnianie zostały zredagowane wersję polecenie, które chcesz wykonać. Gwarantuje to, że sygnatury dostępu Współdzielonego przypadkowo nie są udostępniane nikomu. Możesz znaleźć zostały zredagowane wersji na początku pliku dziennika.

### <a name="view-and-resume-jobs"></a>Wyświetlanie i wznawianie prac

Każda operacja przeniesienia utworzy zadanie narzędzia AzCopy. Aby wyświetlić historię zadań, użyj następującego polecenia:

```azcopy
.\azcopy jobs list
```

Aby wyświetlić statystyki zadania, użyj następującego polecenia:

```azcopy
.\azcopy jobs show <job-id>
```

Aby filtrować transfery według stanu, użyj następującego polecenia:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Użyj następującego polecenia, aby wznowić zadanie nie powiodło się/anulowane. To polecenie używa identyfikatora wraz z tokenu sygnatury dostępu Współdzielonego, ponieważ nie jest trwały ze względów bezpieczeństwa:

```azcopy
.\azcopy jobs resume <jobid> --source-sas="<sastokenhere>"
.\azcopy jobs resume <jobid> --destination-sas="<sastokenhere>"
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz pytania, problemów lub ogólne opinie, zgłoś je [w serwisie GitHub](https://github.com/Azure/azure-storage-azcopy).


