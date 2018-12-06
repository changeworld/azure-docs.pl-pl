---
title: Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą narzędzia AzCopy v10 (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Użyj AzCopy v10 (wersja zapoznawcza) narzędzia do przeniesienia lub skopiowania danych do lub z obiektu blob, tabeli i zawartości pliku. Kopiowanie danych do usługi Azure Storage z lokalnych plików lub kopiowania danych w ramach lub między kontami magazynu. Łatwo Migruj dane do usługi Azure Storage.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.component: common
ms.openlocfilehash: 2ab933506ea03ae72198113d70888460e5001a6d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958426"
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
- [Z systemem MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>Najnowsza wersja produkcyjna (w wersji 8.1)

Pobierz [najnowszą wersję produkcyjną narzędzia AzCopy dla Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>Usługa Table storage (v7.3) obsługuje narzędzia AzCopy

Pobierz [v7.3 AzCopy obsługa kopiowania danych do i z usługi Microsoft Azure Table storage](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Kroki po instalacji

Narzędzie AzCopy v10 nie wymaga instalacji. Otwórz preferowaną aplikacji wiersza polecenia i przejdź do folderu, gdzie `azcopy.exe` wykonywalny znajduje się. Jeśli to konieczne, można dodać lokalizacji folderu Narzędzia AzCopy do ścieżki systemowej.

## <a name="authentication-options"></a>Opcje uwierzytelniania

V10 narzędzia AzCopy można użyć następujących opcji uwierzytelniania w usłudze Azure Storage:
- Usługa Azure Active Directory. Użyj ```.\azcopy login``` logować się za pomocą usługi Azure Active Directory.  Użytkownik powinien mieć [przypisaną rolę "Współautor danych obiektu Blob magazynu"](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) można zapisać do magazynu obiektów Blob przy użyciu uwierzytelniania usługi Azure Active Directory.
- Token sygnatury dostępu Współdzielonego, który ma zostać dołączona do ścieżka obiektu Blob. Można wygenerować tokenu sygnatury dostępu Współdzielonego przy użyciu witryny Azure Portal [Eksploratora usługi Storage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestorageblobsastoken?view=azurermps-6.9.0), lub innych wybranych przez siebie narzędzi. Aby uzyskać więcej informacji, zobacz [przykłady](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Wprowadzenie

Narzędzie AzCopy v10 ma prostą składnię własnym udokumentowane. Ogólna składnia wygląda następująco:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Example:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/containersastoken" --recursive=true
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

## <a name="create-a-file-system-azure-data-lake-storage-gen2-only"></a>Tworzenie systemu plików (usługi Azure Data Lake Storage Gen2 — tylko)

Po włączeniu hierarchiczne przestrzenie nazw w ramach konta magazynu obiektów blob, można użyć następującego polecenia, aby utworzyć nowy system plików, plików do pobrania można przekazać do niego.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name" --recursive=true
```

``account`` Fragment tego ciągu jest nazwa konta magazynu. ``top-level-resource-name`` Część ten ciąg jest nazwą system plików, który chcesz utworzyć.

## <a name="copy-data-to-azure-storage"></a>Kopiowanie danych do usługi Azure Storage

Polecenie kopiowania do przenoszenia danych ze źródła do miejsca docelowego. Lokalizacja źródłowa/docelowa może być Odp.:
- Lokalny system plików
- Usługa Azure Blob/wirtualnego katalogu/kontenera identyfikatora URI
- Usługa Azure identyfikator URI udziału plików/katalogów/plików
- Usługa Azure Data Lake Storage Gen2 systemu plików/katalogów/plików z identyfikatora URI

> [!NOTE]
> W tej chwili AzCopy v10 obsługuje kopiowanie tylko blokowych obiektów blob między kontami magazynu dwa.

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Następujące polecenie przekazuje wszystkie pliki w folderze rekursywnie C:\local\path do kontenera "mycontainer1":

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Po włączeniu hierarchiczne przestrzenie nazw w ramach konta magazynu obiektów blob, służy następujące polecenie do przekazywania plików do systemu plików:

```azcopy
.\azcopy cp "C:\local\path" "https://myaccount.dfs.core.windows.net/myfolder<sastoken>" --recursive=true
```

Następujące polecenie przekazuje wszystkie pliki w folderze C:\local\path (bez recursing do podkatalogów) do kontenera "mycontainer1":

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Po włączeniu hierarchiczne przestrzenie nazw w ramach konta magazynu obiektów blob, można użyć następującego polecenia:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/myfolder<sastoken>"
```

Aby uzyskać więcej przykładów, użyj następującego polecenia:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Kopiowanie danych między dwa konta magazynu

Kopiowanie danych między dwoma kontami magazynu używa [umieścić blok z adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) interfejsu API i wykorzystanie przepustowości sieci maszyny klienta. Dane są kopiowane między dwoma serwerami usługi Azure Storage bezpośrednio, gdy narzędzie AzCopy jest po prostu organizuje operacji kopiowania. 

Aby skopiować dane między dwa konta magazynu, użyj następującego polecenia:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

Aby pracować z kont usługi blob storage, które mają włączonych hierarchicznej przestrzeni nazw, Zastąp ciąg ``blob.core.windows.net`` z ``dfs.core.windows.net`` w tych przykładach.

> [!NOTE]
> Polecenie spowoduje wyliczyć wszystkie kontenery obiektów blob i skopiuj je do konta docelowego. W tej chwili AzCopy v10 obsługuje kopiowanie tylko blokowych obiektów blob między kontami magazynu dwa. Wszystkie inne obiekty kont magazynu (uzupełnialnych obiektów blob, stronicowe obiekty BLOB, pliki, tabele i kolejki) zostaną pominięte.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Kopiowanie obrazu dysku VHD do konta magazynu

Narzędzie AzCopy v10 domyślnie przekazuje dane do blokowych obiektów blob. Jednak jeśli plik źródłowy ma rozszerzenie wirtualnego dysku twardego, narzędzia AzCopy v10 domyślnie przekazać go do stronicowych obiektów blob. To zachowanie nie jest konfigurowalne.

## <a name="sync-incremental-copy-and-delete"></a>Synchronizacja: przyrostowe Kopiuj i usuwaj

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

To polecenie umożliwia przyrostowo synchronizacji źródła do miejsca docelowego, na podstawie ostatniej modyfikacji sygnatur czasowych. Jeśli dodawanie lub usuwanie pliku w źródle, narzędzia AzCopy v10 będzie się tak samo w miejscu docelowym.

[!NOTE] Aby pracować z kont usługi blob storage, które mają włączonych hierarchicznej przestrzeni nazw, Zastąp ciąg ``blob.core.windows.net`` z ``dfs.core.windows.net`` w tych przykładach.

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
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Narzędzie AzCopy v10 tworzy pliki dziennika i plan dla wszystkich zadań. Dzienniki można użyć, aby zbadać i rozwiązać wszelkie potencjalne problemy. Dzienniki będą zawierać stanu błędu (UPLOADFAILED COPYFAILED i DOWNLOADFAILED), pełną ścieżkę i przyczynę błędu. Plan plików i dzienników zadań znajdują się w folderze % USERPROFILE\\.azcopy folderu.

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

## <a name="next-steps"></a>Kolejne kroki

Twoja opinia jest zawsze przyjęte. Jeśli masz jakieś pytania, problemów lub ogólnej opinii dotyczącej Prześlij je na https://github.com/Azure/azure-storage-azcopy. Dziękujemy!