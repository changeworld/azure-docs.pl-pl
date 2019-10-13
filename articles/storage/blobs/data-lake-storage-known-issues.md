---
title: Znane problemy dotyczące Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informacje o ograniczeniach i znanych problemach związanych z Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 300da59aa1a16bb2c4cfeaf8035cbe882ae83358
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300241"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Znane problemy z Azure Data Lake Storage Gen2

W tym artykule wymieniono funkcje i narzędzia, które nie są jeszcze obsługiwane lub są obsługiwane tylko częściowo w przypadku kont magazynu, które mają hierarchiczną przestrzeń nazw (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Interfejsy API Blob Storage

Interfejsy API Blob Storage są wyłączone, aby zapobiec problemom z gotowości funkcji, które mogą wystąpić, ponieważ Blob Storage interfejsy API nie współdziałają z Azure Data Lake interfejsów API Gen2.

> [!NOTE]
> Korzystając z publicznej wersji zapoznawczej dostępu wieloprotokołowego Data Lake Storage, interfejsy API obiektów blob i interfejsy API Data Lake Storage Gen2 mogą działać na tych samych danych. Aby dowiedzieć się więcej, zobacz [dostęp do wielu protokołów w Data Lake Storage](data-lake-storage-multi-protocol-access.md).

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Co zrobić z istniejącymi narzędziami, aplikacjami i usługami

Jeśli którykolwiek z tych użyje interfejsów API obiektów blob i chcesz użyć ich do pracy ze wszystkimi zawartością na Twoim koncie, będziesz mieć dwie opcje.

* **Opcja 1**: nie włączaj hierarchicznej przestrzeni nazw na koncie BLOB Storage, dopóki nie będzie ogólnie dostępny [dostęp do usługi Data Lake Storage](data-lake-storage-multi-protocol-access.md) , a interfejsy API usługi BLOB stają się w pełni współdziałać z Azure Data Lakeami interfejsów API Gen2. [Dostęp do wieloprotokołowego Data Lake Storage](data-lake-storage-multi-protocol-access.md) jest obecnie w publicznej wersji zapoznawczej.  Użycie konta magazynu **bez** hierarchicznej przestrzeni nazw oznacza, że nie będziesz mieć dostępu do Data Lake Storage Gen2 określonych funkcji, takich jak katalogi i listy kontroli dostępu do kontenera.

* **Opcja 2**: Włącz hierarchiczne przestrzenie nazw. Dzięki publicznej wersji zapoznawczej [wieloprotokołowego dostępu do Data Lake Storage](data-lake-storage-multi-protocol-access.md), narzędzia i aplikacje, które wywołują interfejsy API obiektów blob, a także funkcje BLOB Storage, takie jak dzienniki diagnostyczne, mogą współdziałać z kontami z hierarchiczną przestrzenią nazw. Zapoznaj się z tym artykułem, aby zapoznać się ze znanymi problemami i ograniczeniami.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Co zrobić, jeśli używasz interfejsów API obiektów BLOB do ładowania danych przed wyłączeniem interfejsów API obiektów BLOB

Jeśli te interfejsy API zostały użyte do załadowania danych przed ich wyłączeniem i masz wymaganą produkcję do uzyskiwania dostępu do tych danych, skontaktuj się z pomoc techniczna firmy Microsoft, podając następujące informacje:

> [!div class="checklist"]
> * Identyfikator subskrypcji (identyfikator GUID, a nie nazwa).
> * Nazwy kont magazynu.
> * Czy chcesz aktywnie wpływać na środowisko produkcyjne, a jeśli tak, dla których kont magazynu?
> * Nawet jeśli nie ma aktywnie wpływu na środowisko produkcyjne, poinformuj nas, czy potrzebne dane mają być kopiowane na inne konto magazynu z jakiegoś powodu, a jeśli tak, dlaczego?

W tych okolicznościach możemy przywrócić dostęp do interfejsu API obiektów BLOB przez ograniczony czas, aby można było skopiować te dane na konto magazynu, w którym nie jest włączona funkcja hierarchicznej przestrzeni nazw.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Problemy i ograniczenia dotyczące używania interfejsów API obiektów BLOB na kontach z hierarchiczną przestrzenią nazw

Korzystając z publicznej wersji zapoznawczej dostępu wieloprotokołowego Data Lake Storage, interfejsy API obiektów blob i interfejsy API Data Lake Storage Gen2 mogą działać na tych samych danych.

W tej sekcji opisano problemy i ograniczenia dotyczące używania interfejsów API obiektów blob i interfejsów API Data Lake Storage Gen2 do działania na tych samych danych.

* Nie można używać zarówno interfejsów API obiektów blob, jak i Data Lake Storage interfejsów API do zapisu w tym samym wystąpieniu pliku.

* W przypadku zapisywania do pliku przy użyciu Data Lake Storage Gen2 interfejsów API, bloki tego pliku nie będą widoczne dla wywołań interfejsu API [pobierania listy zablokowanych](https://docs.microsoft.com/rest/api/storageservices/get-block-list) .

* Plik można zastąpić za pomocą interfejsów API Data Lake Storage Gen2 lub interfejsów API obiektów BLOB. Nie wpłynie to na właściwości pliku.

* W przypadku korzystania z operacji [list obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) bez określania ogranicznika wyniki będą obejmować zarówno katalogi, jak i obiekty blob.

  Jeśli zdecydujesz się użyć ogranicznika, użyj tylko ukośnika (`/`). Jest to jedyny obsługiwany ogranicznik.

* Jeśli używasz interfejsu API [usuwania obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) do usuwania katalogu, ten katalog zostanie usunięty tylko wtedy, gdy jest pusty.

  Oznacza to, że nie można używać usługi Blob API Delete katalogów rekursywnie.

Te interfejsy API REST obiektów BLOB nie są obsługiwane:

* [Umieść obiekt BLOB (strona)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Umieść stronę](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Pobierz zakresy stron](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Obiekt BLOB kopiowania przyrostowego](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Umieść stronę na podstawie adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put obiekt BLOB (append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Dołącz blok](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Dołącz blok z adresu URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>Problemy z niezarządzanymi dyskami maszyn wirtualnych

Niezarządzane dyski maszyny wirtualnej nie są obsługiwane na kontach z hierarchiczną przestrzenią nazw. Jeśli chcesz włączyć hierarchiczną przestrzeń nazw na koncie magazynu, umieść niezarządzane dyski maszyn wirtualnych na koncie magazynu, w którym nie jest włączona funkcja hierarchicznej przestrzeni nazw.


## <a name="support-for-other-blob-storage-features"></a>Obsługa innych funkcji Blob Storage

W poniższej tabeli wymieniono wszystkie inne funkcje i narzędzia, które nie są jeszcze obsługiwane lub tylko częściowo obsługiwane z kontami magazynu, które mają hierarchiczną przestrzeń nazw (Azure Data Lake Storage Gen2).

| Funkcja/narzędzie    | Więcej informacji    |
|--------|-----------|
| **Interfejsy API dla kont usługi Data Lake Storage Gen2 Storage** | Częściowo obsługiwane <br><br>Dostęp do wieloprotokołowego Data Lake Storage jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza umożliwia korzystanie z interfejsów API obiektów BLOB w językach .NET, Java i Python SDK z kontami, które mają hierarchiczną przestrzeń nazw.  Zestawy SDK nie zawierają jeszcze interfejsów API, które umożliwiają korzystanie z katalogów lub ustawianie list kontroli dostępu (ACL). Aby wykonać te funkcje, można użyć Data Lake Storage Gen2 interfejsów API **rest** . |
| **Narzędzie AzCopy** | Obsługa specyficzna dla wersji <br><br>Użyj tylko najnowszej wersji AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Wcześniejsze wersje AzCopy, takie jak AzCopy v 8.1, nie są obsługiwane.|
| **Zasady zarządzania cyklem życia Blob Storage platformy Azure** | Obsługiwane przez dostęp do usługi [Data Lake Storage w](data-lake-storage-multi-protocol-access.md) wersji zapoznawczej. Warstwy dostępu chłodna i archiwalna są obsługiwane tylko w wersji zapoznawczej. Usuwanie migawek obiektów BLOB nie jest jeszcze obsługiwane. |
| **Azure Content Delivery Network (CDN)** | Jeszcze nieobsługiwane|
| **Usługa Azure Search** |Obsługiwane przez dostęp do usługi [Data Lake Storage w](data-lake-storage-multi-protocol-access.md) wersji zapoznawczej.|
| **Azure Storage Explorer** | Obsługa specyficzna dla wersji <br><br>Używaj tylko wersji `1.6.0` lub nowszej. <br>Wersja `1.6.0` jest dostępna bezpłatnie do [pobrania](https://azure.microsoft.com/features/storage-explorer/).|
| **Listy ACL kontenera obiektów BLOB** |Jeszcze nieobsługiwane|
| **Blobfuse** |Jeszcze nieobsługiwane|
| **Niestandardowe domeny** |Jeszcze nieobsługiwane|
| **Eksplorator systemu plików** | Ograniczona pomoc techniczna |
| **Rejestrowanie diagnostyczne** |Dzienniki diagnostyczne są obsługiwane przez dostęp do usługi [Data Lake Storage w](data-lake-storage-multi-protocol-access.md) wersji zapoznawczej. <br><br>Włączanie dzienników w Azure Portal nie jest obecnie obsługiwane. Oto przykład sposobu włączania dzienników przy użyciu programu PowerShell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Upewnij się, że określono `Blob` jako wartość parametru `-ServiceType`, jak pokazano w tym przykładzie. <br><br>Obecnie nie można używać Eksplorator usługi Azure Storage do wyświetlania dzienników diagnostycznych. Aby wyświetlić dzienniki, użyj AzCopy lub zestawów SDK.
| **Niezmienny magazyn** |Jeszcze nieobsługiwane <br><br>Niezmienny magazyn umożliwia przechowywanie danych w [robaku (zapis jeden raz, odczyt wielu)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Warstwy na poziomie obiektów** |Warstwy chłodna i archiwalna są obsługiwane przez [dostęp wieloprotokołowy](data-lake-storage-multi-protocol-access.md) do wersji zapoznawczej Data Lake Storage. <br><br> Wszystkie inne warstwy dostępu nie są jeszcze obsługiwane.|
| **Obsługa programu PowerShell i interfejsu wiersza polecenia** | Ograniczona funkcjonalność <br><br>Obsługiwane są operacje zarządzania, takie jak tworzenie konta. Operacje płaszczyzny danych, takie jak przekazywanie i pobieranie plików, są w publicznej wersji zapoznawczej w ramach [dostępu do wieloprotokołowego Data Lake Storage](data-lake-storage-multi-protocol-access.md). Praca z katalogami i ustawianie list kontroli dostępu (ACL) nie jest jeszcze obsługiwana. |
| **Statyczne witryny sieci Web** |Jeszcze nieobsługiwane <br><br>W oddzielnym zakresie możliwość obsługiwania plików do [statycznych witryn sieci Web](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Aplikacje innych firm** | Ograniczona pomoc techniczna <br><br>Aplikacje innych firm, które używają interfejsów API REST do pracy, będą nadal działały, jeśli są używane z Data Lake Storage Gen2. <br>Aplikacje wywołujące interfejsy API obiektów BLOB prawdopodobnie będą korzystać z publicznej wersji zapoznawczej [wieloprotokołowego dostępu do Data Lake Storage](data-lake-storage-multi-protocol-access.md). 
| **Funkcje obsługi wersji** |Jeszcze nieobsługiwane <br><br>Dotyczy to [migawek](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) i [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|


