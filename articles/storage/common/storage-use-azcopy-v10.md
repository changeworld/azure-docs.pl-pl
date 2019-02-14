---
title: Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą narzędzia AzCopy v10 (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Użyj AzCopy v10 (wersja zapoznawcza) narzędzia do przeniesienia lub skopiowania danych do lub z obiektu blob, usługa data lake i zawartości pliku. Kopiowanie danych do usługi Azure Storage z lokalnych plików lub kopiowania danych w ramach lub między kontami magazynu. Łatwo Migruj dane do usługi Azure Storage.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: c9009e898b00212dba4dec9bf38af2bfa057b8ea
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244610"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>Transferowanie danych za pomocą AzCopy v10 (wersja zapoznawcza)

Narzędzie AzCopy v10 (wersja zapoznawcza) to narzędzie wiersza polecenia następnej generacji do kopiowania danych z magazynu obiektów Blob platformy Azure firmy Microsoft i plik, który oferuje przeprojektowany interfejs wiersza polecenia i architektura dla transferów danych niezawodne o wysokiej wydajności. Przy użyciu narzędzia AzCopy możesz skopiować dane między systemem plików i konto magazynu lub między kontami magazynu.

## <a name="whats-new-in-azcopy-v10"></a>What's new in v10 narzędzia AzCopy

- Synchronizuj system plików, do obiektów Blob platformy Azure lub na odwrót. Użyj `azcopy sync <source> <destination>`. Idealne rozwiązanie dla scenariuszy kopiowania przyrostowego.
- Obsługuje interfejsy API usługi Azure Data Lake Storage Gen2. Użyj `myaccount.dfs.core.windows.net` jako identyfikator URI do wywoływania interfejsów API Gen2 usługi ADLS.
- Obsługuje kopiowanie całego konta (tylko w przypadku usługi obiektów Blob) do innego konta.
- Konto do skopiowania konta jest teraz za pomocą nowego [Put z adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) interfejsów API. Żaden transfer danych do klienta jest wymagana, co sprawia, że transfer szybciej!
- Listy i usuwać pliki i obiekty BLOB w podanej ścieżce.
- Obsługuje także — obejmują i — Wyklucz flagi wzorców symboli wieloznacznych w ścieżce.
- Większa odporność: każde wystąpienie narzędzia AzCopy utworzy kolejność zadań i plików dziennika powiązanych. Można przeglądać i uruchom ponownie poprzedniego zadania i Wznów zadania zakończone niepowodzeniem. Narzędzie AzCopy również automatycznie ponowi próbę transferu po awarii.
- Ulepszenia ogólnej wydajności.

## <a name="download-and-install-azcopy"></a>Pobierz i zainstaluj narzędzia AzCopy

### <a name="latest-preview-version-v10"></a>Najnowszej wersji zapoznawczej (v10)

Pobierz najnowszą wersję zapoznawczą narzędzia AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>Najnowsza wersja produkcyjna (w wersji 8.1)

Pobierz [najnowszą wersję produkcyjną narzędzia AzCopy dla Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>Usługa Table storage (v7.3) obsługuje narzędzia AzCopy

Pobierz [v7.3 AzCopy obsługa kopiowania danych do i z usługi Microsoft Azure Table storage](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Kroki po instalacji

Narzędzie AzCopy v10 nie wymaga instalacji. Otwórz preferowaną aplikacji wiersza polecenia i przejdź do folderu, gdzie `azcopy.exe` wykonywalny znajduje się. Jeśli to konieczne, można dodać lokalizacji folderu Narzędzia AzCopy do ścieżki systemowej.

## <a name="authentication-options"></a>Opcje uwierzytelniania

V10 narzędzia AzCopy można użyć następujących opcji uwierzytelniania w usłudze Azure Storage:
- **Usługa Azure Active Directory [obsługiwane usługi obiektów Blob i Azure Data Lake Store Gen2]**. Użyj ```.\azcopy login``` logować się za pomocą usługi Azure Active Directory.  Użytkownik powinien mieć [przypisaną rolę "Współautor danych obiektu Blob magazynu"](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) można zapisać do magazynu obiektów Blob przy użyciu uwierzytelniania usługi Azure Active Directory.
- **Sygnatury dostępu Współdzielonego tokeny [obsługiwane usługi obiektów Blob i plików]**. Dołącz token sygnatury dostępu Współdzielonego do ścieżka obiektu blob w wierszu polecenia z niego korzystać. Można wygenerować tokenu sygnatury dostępu Współdzielonego przy użyciu witryny Azure Portal [Eksploratora usługi Storage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), lub innych wybranych przez siebie narzędzi. Aby uzyskać więcej informacji, zobacz [przykłady](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

> [!IMPORTANT]
> Podczas przesyłania żądania pomocy technicznej Microsoft Support (lub tego problemu, obejmujące trzecich 3), udział, które zostały zredagowane wersję polecenie, które próbujesz wykonać, aby upewnić się, sygnatury dostępu Współdzielonego jest przypadkowo nieudostępniany nikomu. Możesz znaleźć zostały zredagowane wersji na początku pliku dziennika. Zapoznaj się z sekcją rozwiązywanie problemów w dalszej części tego artykułu, aby uzyskać więcej informacji.

## <a name="getting-started"></a>Wprowadzenie

Narzędzie AzCopy v10 ma prostą składnię własnym udokumentowane. Ogólna składnia wygląda następująco, po zalogowaniu się do usługi Azure Active Directory:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you are using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Poniżej przedstawiono, jak można uzyskać listę dostępnych poleceń:

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

Aby wyświetlić stronę pomocy i przykłady dla określonego polecenia uruchom poniższe polecenie:

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Utwórz kontener obiektów Blob lub udziału plików 

**Utwórz kontener obiektów Blob**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Utwórz udział plików**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Utwórz kontener obiektów Blob za pomocą Gen2 Azure Data Lake Store**

Po włączeniu hierarchiczne przestrzenie nazw w ramach konta magazynu obiektów blob, można użyć następującego polecenia, aby utworzyć nowy system plików (kontener obiektów Blob), dzięki czemu możesz przekazać pliki do niego.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Kopiowanie danych do usługi Azure Storage

Polecenie kopiowania do przenoszenia danych ze źródła do miejsca docelowego. Lokalizacja źródłowa/docelowa może być Odp.:
- Lokalny system plików
- Usługa Azure Blob/wirtualnego katalogu/kontenera identyfikatora URI
- Usługa Azure identyfikator URI udziału plików/katalogów/plików
- Usługa Azure Data Lake Storage Gen2 systemu plików/katalogów/plików z identyfikatora URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Następujące polecenie przekazuje wszystkie pliki w folderze `C:\local\path` rekursywnie do kontenera `mycontainer1` tworzenia `path` katalogu w kontenerze:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Następujące polecenie przekazuje wszystkie pliki w folderze `C:\local\path` (bez recursing do podkatalogów) do kontenera `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Aby uzyskać więcej przykładów, użyj następującego polecenia:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Kopiowanie danych między dwa konta magazynu

Kopiowanie danych między dwoma kontami magazynu używa [umieścić blok z adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) interfejsu API i wykorzystanie przepustowości sieci maszyny klienta. Dane są kopiowane między dwoma serwerami usługi Azure Storage bezpośrednio, gdy narzędzie AzCopy jest po prostu organizuje operacji kopiowania. Ta opcja obecnie jest dostępna tylko dla magazynu obiektów Blob.

Aby skopiować dane między dwa konta magazynu, użyj następującego polecenia:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> Polecenie spowoduje wyliczyć wszystkie kontenery obiektów blob i skopiuj je do konta docelowego. W tej chwili AzCopy v10 obsługuje kopiowanie tylko blokowych obiektów blob między kontami magazynu dwa. Wszystkie inne obiekty kont magazynu (uzupełnialnych obiektów blob, stronicowe obiekty BLOB, pliki, tabele i kolejki) zostaną pominięte.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Kopiowanie obrazu dysku VHD do konta magazynu

Narzędzie AzCopy v10 domyślnie przekazuje dane do blokowych obiektów blob. Jednak jeśli plik źródłowy ma rozszerzenie wirtualnego dysku twardego, narzędzia AzCopy v10 domyślnie przekazać go do stronicowych obiektów blob. To zachowanie jest obecnie można konfigurować.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Synchronizacja: przyrostowa kopia i delete (tylko w przypadku magazynu obiektów Blob)

> [!NOTE]
> Polecenie synchronizacji synchronizuje zawartość ze źródła do miejsca docelowego i obejmuje usunięcie pliki docelowe, te nie istnieją w źródle. Upewnij się, że używasz miejsce docelowe, które zamierzasz synchronizować.

Aby zsynchronizować lokalnego systemu plików na konto magazynu, użyj następującego polecenia:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

W ten sam sposób można synchronizować kontener obiektów Blob do lokalnego systemu plików:

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

To polecenie umożliwia przyrostowo synchronizacji źródła do miejsca docelowego, na podstawie ostatniej modyfikacji sygnatur czasowych. Jeśli dodawanie lub usuwanie pliku w źródle, narzędzia AzCopy v10 będzie się tak samo w miejscu docelowym. Przed usunięciem narzędzia AzCopy wyświetli monit o potwierdzenie usunięcia plików.

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

### <a name="configure-proxy-settings"></a>Skonfiguruj ustawienia serwera proxy

Aby skonfigurować ustawienia serwera proxy do v10 narzędzia AzCopy, ustaw https_proxy zmiennych środowiskowych za pomocą następującego polecenia:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Optymalizowanie przepływności

Ustaw zmienną środowiskową AZCOPY_CONCURRENCY_VALUE, aby skonfigurować liczbę jednoczesnych żądań i kontrolować wydajność przepustowości i zużycia zasobów. Wartość jest domyślnie do 300. Zmniejszenie wartości ograniczy przepustowość i użycie Procesora przez narzędzie AzCopy v10.

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

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Narzędzie AzCopy v10 tworzy pliki dziennika i plan dla wszystkich zadań. Dzienniki można użyć, aby zbadać i rozwiązać wszelkie potencjalne problemy. Dzienniki będą zawierać stanu błędu (UPLOADFAILED COPYFAILED i DOWNLOADFAILED), pełną ścieżkę i przyczynę błędu. Plan plików i dzienników zadań znajdują się w folderze % USERPROFILE\\.azcopy folder Windows lub $HOME\\.azcopy folderu na komputerach Mac i Linux.

> [!IMPORTANT]
> Podczas przesyłania żądania pomocy technicznej Microsoft Support (lub tego problemu, obejmujące trzecich 3), udział, które zostały zredagowane wersję polecenie, które próbujesz wykonać, aby upewnić się, sygnatury dostępu Współdzielonego jest przypadkowo nieudostępniany nikomu. Możesz znaleźć zostały zredagowane wersji na początku pliku dziennika.

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
# If the value is blank then the default value is currently in use
```

### <a name="review-the-logs-for-errors"></a>Przejrzyj dzienniki błędów

Następujące polecenie pobierze wszystkie błędy ze stanem UPLOADFAILED z dziennika 04dc9ca9-158f-7945-5933-564021086c79:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

### <a name="view-and-resume-jobs"></a>Wyświetlanie i wznawianie prac

Każda operacja przeniesienia utworzy zadanie narzędzia AzCopy. Możesz wyświetlić historię zadania przy użyciu następującego polecenia:

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

Można wznowić zadania nie powiodło się/anulowane za pomocą jego identyfikatora, wraz z tokenu sygnatury dostępu Współdzielonego (nie jest trwały ze względów bezpieczeństwa):

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

### <a name="change-the-default-log-level"></a>Zmień domyślny poziom rejestrowania

Domyślnie poziom dziennika narzędzia AzCopy jest ustawiony na INFO. Jeśli chcesz zmniejszyć poziom szczegółowości dziennika, aby zaoszczędzić miejsce na dysku, należy zastąpić ustawienie przy użyciu ``--log-level`` opcji. Poziomy dziennika dostępne są następujące: DEBUGOWANIA, informacje, ostrzeżenie, błąd, PANIKĘ i błąd krytyczny

## <a name="next-steps"></a>Kolejne kroki

Twoja opinia jest zawsze przyjęte. Jeśli masz jakieś pytania, problemów lub ogólnej opinii dotyczącej Prześlij je na https://github.com/Azure/azure-storage-azcopy. Dziękujemy!
