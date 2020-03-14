---
title: Znane problemy związane z usługi Azure Data Lake Storage Gen2 | Dokumentacja firmy Microsoft
description: Poznaj ograniczenia i znane problemy dotyczące Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7fd76be8d17dc1c632e555a56d038d4f5c1e1486
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268511"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Znane problemy związane z usługi Azure Data Lake Storage Gen2

W tym artykule opisano ograniczenia i znane problemy dotyczące Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Obsługiwane funkcje magazynu obiektów BLOB

Rosnąca liczba funkcji magazynu obiektów BLOB działa teraz z kontami, które mają hierarchiczną przestrzeń nazw. Aby uzyskać pełną listę, zobacz [BLOB Storage funkcje dostępne w programie Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Obsługiwane integracje usług platformy Azure

Data Lake Storage Gen2 obsługuje kilka usług platformy Azure, których można użyć do pozyskiwania danych, wykonywania analiz i tworzenia reprezentacji wizualnych. Aby uzyskać listę obsługiwanych usług platformy Azure, zobacz [usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Zobacz [usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Obsługiwane platformy open source

Wiele platform typu open source obsługi Gen2 magazynu programu Data Lake. Aby uzyskać pełną listę, zobacz [platformy typu "open source" obsługujące Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Zobacz [platformę Open Source, która obsługuje Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>Magazyn obiektów blob interfejsów API

Interfejsy API obiektów blob i interfejsy API Data Lake Storage Gen2 mogą działać na tych samych danych.

W tej sekcji opisano problemy i ograniczenia dotyczące używania interfejsów API obiektów blob i interfejsów API Data Lake Storage Gen2 do działania na tych samych danych.

* Nie można używać zarówno interfejsów API obiektów blob, jak i Data Lake Storage interfejsów API do zapisu w tym samym wystąpieniu pliku. W przypadku zapisywania do pliku przy użyciu Data Lake Storage Gen2 interfejsów API, bloki tego pliku nie będą widoczne dla wywołań interfejsu API [pobierania listy zablokowanych](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . Plik można zastąpić za pomocą interfejsów API Data Lake Storage Gen2 lub interfejsów API obiektów BLOB. Nie wpłynie to na właściwości pliku.

* W przypadku korzystania z operacji [list obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) bez określania ogranicznika wyniki będą obejmować zarówno katalogi, jak i obiekty blob. Jeśli zdecydujesz się użyć ogranicznika, użyj tylko ukośnika (`/`). Jest to jedyny obsługiwany ogranicznik.

* Jeśli używasz interfejsu API [usuwania obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) do usuwania katalogu, ten katalog zostanie usunięty tylko wtedy, gdy jest pusty. Oznacza to, że nie można używać usługi Blob API Delete katalogów rekursywnie.

Te interfejsy API REST obiektów BLOB nie są obsługiwane:

* [Umieść obiekt BLOB (strona)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Umieść stronę](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Pobierz zakresy stron](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Obiekt BLOB kopiowania przyrostowego](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Umieść stronę na podstawie adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put obiekt BLOB (append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Dołącz blok](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Dołącz blok z adresu URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Niezarządzane dyski maszyny wirtualnej nie są obsługiwane na kontach z hierarchiczną przestrzenią nazw. Jeśli chcesz włączyć hierarchiczną przestrzeń nazw na koncie magazynu, umieść niezarządzane dyski maszyn wirtualnych na koncie magazynu, w którym nie jest włączona funkcja hierarchicznej przestrzeni nazw.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Obsługa systemu plików w zestawach SDK

- [Programy .NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) i [Python](data-lake-storage-directory-file-acl-python.md)oraz obsługa [języka JavaScript](data-lake-storage-directory-file-acl-javascript.md) i pomocy technicznej są dostępne w publicznej wersji zapoznawczej. Inne zestawy SDK nie są obecnie obsługiwane.
- Operacje pobierania i ustawiania listy ACL nie są obecnie cykliczne.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Obsługa systemu plików w programie PowerShell i interfejsie wiersza polecenia platformy Azure

- Obsługa [programu PowerShell](data-lake-storage-directory-file-acl-powershell.md) i [interfejsu wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md) znajduje się w publicznej wersji zapoznawczej.
- Operacje pobierania i ustawiania listy ACL nie są obecnie cykliczne.

## <a name="lifecycle-management-policies"></a>Zasady zarządzania cyklem życia

* Usuwanie migawek obiektów BLOB nie jest jeszcze obsługiwane.  

* Obecnie niektóre usterki mają wpływ na zasady zarządzania cyklem życia i warstwę dostępu archiwizowania. 

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Eksplorator usługi Azure Storage 1.10. x nie można używać do wyświetlania dzienników diagnostycznych. Aby wyświetlić dzienniki, użyj AzCopy lub zestawów SDK.

## <a name="blobfuse"></a>Blobfuse

Blobfuse nie jest obsługiwana.



<a id="known-issues-tools" />

## <a name="azcopy"></a>Narzędzie AzCopy

Użyj tylko najnowszej wersji AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Wcześniejsze wersje AzCopy, takie jak AzCopy v 8.1, nie są obsługiwane.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Używaj tylko wersji `1.6.0` lub nowszej. Obecnie istnieje usterka magazynu wpływająca na `1.11.0`wersji  , która może powodować błędy uwierzytelniania w niektórych scenariuszach. Poprawka dotycząca usterki magazynu jest wdrażana, ale jako obejście zalecamy użycie wersji `1.10.x` dostępnej jako [bezpłatnego pobrania](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). Usterka magazynu nie dotyczy   `1.10.x`.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Eksplorator usługi Storage w Azure Portal

Listy ACL nie są jeszcze obsługiwane.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Aplikacje innych firm

Aplikacje innych firm, które używają interfejsów API REST do pracy, będą nadal działały w przypadku używania ich z Data Lake Storage Gen2 aplikacjami, które wywołują interfejsy API obiektów blob, prawdopodobnie będą działały.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Listy kontroli dostępu (ACL) i anonimowy dostęp do odczytu

Jeśli [anonimowy dostęp do odczytu](storage-manage-access-to-resources.md) został przyznany do kontenera, listy ACL nie mają wpływu na ten kontener ani pliki w tym kontenerze.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Sterownik Azure Storage Blob systemu Windows (WASB)

Obecnie istnieje kilka problemów skojarzonych z używaniem sterownika WASB wraz z kontami z hierarchiczną przestrzenią nazw. Zalecamy używanie sterownika [systemu plików obiektów blob platformy Azure (ABFS)](data-lake-storage-abfs-driver.md) w obciążeniach. 





