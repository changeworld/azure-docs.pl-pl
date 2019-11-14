---
title: Znane problemy związane z usługi Azure Data Lake Storage Gen2 | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o ograniczeniach i znanych problemach, za pomocą usługi Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 78693dceaac119279b1c1d06a6c3a18cc4fdb485
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033936"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Znane problemy związane z usługi Azure Data Lake Storage Gen2

W tym artykule wymieniono funkcje i narzędzia, które nie są jeszcze obsługiwane lub są obsługiwane tylko częściowo w przypadku kont magazynu, które mają hierarchiczną przestrzeń nazw (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>Problemy i ograniczenia związane z korzystaniem z interfejsów API obiektów BLOB

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

## <a name="support-for-other-blob-storage-features"></a>Obsługa innych funkcji Blob Storage

W poniższej tabeli wymieniono wszystkie inne funkcje i narzędzia, które nie są jeszcze obsługiwane lub tylko częściowo obsługiwane z kontami magazynu, które mają hierarchiczną przestrzeń nazw (Azure Data Lake Storage Gen2).

| Funkcja/narzędzie    | Więcej informacji    |
|--------|-----------|
| **Interfejsy API Data Lake Storage Gen2** | Częściowo obsługiwane <br><br>W bieżącej wersji można używać Data Lake Storage Gen2 interfejsów API **rest** do współpracy z katalogami i ustawiania list kontroli dostępu (ACL), ale nie ma innych zestawów SDK (na przykład: .NET, Java lub Python) do wykonywania tych zadań. Aby wykonać inne zadania, takie jak przekazywanie i pobieranie plików, można użyć zestawów SDK obiektów BLOB.  |
| **Narzędzie AzCopy** | Obsługa specyficzna dla wersji <br><br>Użyj tylko najnowszej wersji AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Wcześniejsze wersje AzCopy, takie jak AzCopy v 8.1, nie są obsługiwane.|
| **Zasady zarządzania cyklem życia Blob Storage platformy Azure** | Obsługiwane są wszystkie warstwy dostępu. Warstwa dostępu archiwalnego jest obecnie w wersji zapoznawczej. Usuwanie migawek obiektów BLOB nie jest jeszcze obsługiwane. |
| **Azure Content Delivery Network (CDN)** | Jeszcze nieobsługiwane|
| **Usługa Azure Search** |Obsługiwane (wersja zapoznawcza)|
| **Azure Storage Explorer** | Obsługa specyficzna dla wersji <br><br>Używaj tylko wersji `1.6.0` przez `1.10.0`. <br> Wersja `1.10.0` jest dostępna bezpłatnie do [pobrania](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). Wersja `1.11.0` nie jest jeszcze obsługiwana.|
| **Listy ACL kontenera obiektów BLOB** |Jeszcze nieobsługiwane|
| **Blobfuse** |Jeszcze nieobsługiwane|
| **Niestandardowe domeny** |Jeszcze nieobsługiwane|
| **Eksplorator usługi Storage w Azure Portal** | Ograniczona pomoc techniczna. Listy ACL nie są jeszcze obsługiwane. |
| **Rejestrowanie diagnostyczne** |Dzienniki diagnostyczne są obsługiwane (wersja zapoznawcza).<br><br>Włączanie dzienników w Azure Portal nie jest obecnie obsługiwane. Oto przykład sposobu włączania dzienników przy użyciu programu PowerShell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Upewnij się, że określono `Blob` jako wartość parametru `-ServiceType`, jak pokazano w tym przykładzie. <br><br>Obecnie nie można używać Eksplorator usługi Azure Storage do wyświetlania dzienników diagnostycznych. Aby wyświetlić dzienniki, użyj AzCopy lub zestawów SDK.
| **Niezmienny magazyn** |Jeszcze nieobsługiwane <br><br>Niezmienny magazyn umożliwia przechowywanie danych w [robaku (zapis jeden raz, odczyt wielu)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Warstwy na poziomie obiektów** |Obsługiwane są warstwy chłodna i archiwalna. Warstwa archiwum jest w wersji zapoznawczej. Wszystkie inne warstwy dostępu nie są jeszcze obsługiwane.|
| **Obsługa programu PowerShell i interfejsu wiersza polecenia** | Ograniczona funkcjonalność <br><br>Operacje obiektów BLOB są obsługiwane. Praca z katalogami i ustawianie list kontroli dostępu (ACL) nie jest jeszcze obsługiwana. |
| **Statyczne witryny sieci Web** |Jeszcze nieobsługiwane <br><br>W oddzielnym zakresie możliwość obsługiwania plików do [statycznych witryn sieci Web](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Aplikacje innych firm** | Ograniczona pomoc techniczna <br><br>Aplikacje innych firm, które używają interfejsów API REST do pracy, będą nadal działały, jeśli są używane z Data Lake Storage Gen2. <br>Aplikacje wywołujące interfejsy API obiektów BLOB prawdopodobnie będą działały.|
|**Usuwanie nietrwałe** |Jeszcze nieobsługiwane|
| **Funkcje obsługi wersji** |Jeszcze nieobsługiwane <br><br>Dotyczy to również [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)oraz innych funkcji przechowywania wersji, takich jak [migawki](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


