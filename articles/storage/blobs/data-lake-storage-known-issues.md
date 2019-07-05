---
title: Znane problemy związane z usługi Azure Data Lake Storage Gen2 | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o ograniczeniach i znanych problemach, za pomocą usługi Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: daf9199104047f714d568bd2796490b836243952
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443234"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Znane problemy związane z usługi Azure Data Lake Storage Gen2

W tym artykule wymieniono funkcje i narzędzia, które nie zostały jeszcze obsługiwany lub tylko częściowo obsługiwane z kontami magazynu, które mają hierarchicznej przestrzeni nazw (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Magazyn obiektów blob interfejsów API

Magazyn obiektów blob interfejsy API są wyłączone, aby zapobiec problemom sprawność działania funkcji, które mogą wystąpić, ponieważ interfejsy API usługi Blob Storage nie są jeszcze współpracujący z interfejsów API usługi Azure Data Lake Gen2.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Co należy zrobić z istniejącymi narzędziami, aplikacjami i usługami

Jeśli dowolny z tych użycia interfejsów API usługi Blob, a chcesz ich używać do pracy z całej zawartości, który przekaże do swojego konta, nie włączysz hierarchicznej przestrzeni nazw na konta usługi Blob storage do momentu interfejsów API usługi Blob stają się współpracujący z interfejsów API usługi Azure Data Lake Gen2.

Przy użyciu konta magazynu bez hierarchicznej przestrzeni nazw oznacza, że nie masz dostępu do Data Lake Storage Gen2 określone funkcje, takie jak listy kontroli dostępu systemu plików i katalogów następnie.

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>Co należy zrobić z dyskami niezarządzanymi maszyn wirtualnych (VM)

Te zależą od wyłączone interfejsów API Blob Storage, dlatego jeśli chcesz włączyć hierarchicznej przestrzeni nazw na koncie magazynu, należy rozważyć umieszczenie ich na konto magazynu, który nie ma włączoną funkcję hierarchicznej przestrzeni nazw.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Co zrobić, jeśli użyto interfejsów API usługi Blob do ładowania danych, zanim interfejsów API usługi Blob zostały wyłączone.

Jeśli te interfejsy API są używane do ładowania danych, zanim zostały wyłączone, a nie produkcyjne do nich dostęp, następnie skontaktuj się z Microsoft Support z następującymi informacjami:

> [!div class="checklist"]
> * Identyfikator subskrypcji (identyfikator GUID, a nie nazwę).
> * Nazwy konta magazynu.
> * Czy aktywnie dotyczy Cię w środowisku produkcyjnym, a jeśli tak, dla których kont magazynu?
> * Nawet wtedy, gdy użytkownik nie aktywnie dotyczą w środowisku produkcyjnym, powiedz nam, czy potrzebujesz tych danych do skopiowania do innego konta magazynu z jakiegoś powodu i dlaczego?

W tych okolicznościach firma Microsoft może przywrócenia dostępu do interfejsu API obiektu Blob na pewien czas tak, aby można było skopiować te dane do konta magazynu, który nie ma włączoną funkcję hierarchicznej przestrzeni nazw.

## <a name="all-other-features-and-tools"></a>Wszystkie inne funkcje i narzędzia

W poniższej tabeli wymieniono wszystkie inne funkcje i narzędzia, które nie zostały jeszcze obsługiwany lub tylko częściowo obsługiwane z kontami magazynu, które mają hierarchicznej przestrzeni nazw (Azure Data Lake Storage Gen2).

| Funkcja / narzędzia    | Więcej informacji    |
|--------|-----------|
| **Interfejsy API dla kont magazynu usługi Data Lake Storage Gen2** | Częściowo obsługiwane <br><br>Możesz użyć Data Lake Storage Gen2 **REST** interfejsów API, ale interfejsów API w innych zestawów SDK obiektów Blob, takich jak zestawy .NET, Java, Python SDK nie są jeszcze dostępne.|
| **Narzędzie AzCopy** | Obsługa określonej wersji <br><br>Użyj tylko najnowszą wersję programu AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Wcześniejszych wersjach narzędzia AzCopy takich jak narzędzia AzCopy w wersji 8.1 nie są obsługiwane.|
| **Zasady zarządzania cyklem życia magazynu Azure Blob** | Nie jest jeszcze obsługiwany |
| **Usługa Azure Content Delivery Network (CDN)** | Nie jest jeszcze obsługiwany|
| **Usługa Azure search** |Nie jest jeszcze obsługiwany|
| **Azure Storage Explorer** | Obsługa określonej wersji <br><br>Użyj tylko wersji `1.6.0` lub nowszej. <br>Wersja `1.6.0` jest dostępna jako [bezpłatne materiały do pobrania](https://azure.microsoft.com/features/storage-explorer/).|
| **Kontener obiektów blob list ACL** |Nie jest jeszcze obsługiwany|
| **Blobfuse** |Nie jest jeszcze obsługiwany|
| **Niestandardowe domeny** |Nie jest jeszcze obsługiwany|
| **Dzienniki diagnostyczne** |Nie jest jeszcze obsługiwany|
| **File System Explorer** | Ograniczona obsługa |
| **Niezmienny Magazyn** |Nie jest jeszcze obsługiwany <br><br>Niezmienne storage oferuje możliwość przechowywania danych w [ROBAK (jednokrotny zapis, przeczytaj wiele)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) stanu.|
| **Warstw na poziomie obiektu** |Nie jest jeszcze obsługiwany <br><br>Na przykład: — Wersja Premium, warstwy gorąca, zimnego i archiwum.|
| **Obsługa programu PowerShell i interfejsu wiersza polecenia** | Ograniczona funkcjonalność <br><br>Aby utworzyć konto, należy za pomocą programu Powershell lub interfejsu wiersza polecenia. Nie można wykonywać operacje lub Ustaw listy kontroli dostępu dla systemów plików, katalogów i plików.|
| **Statycznych witryn internetowych** |Nie jest jeszcze obsługiwany <br><br>W szczególności możliwość obsługi plików [statycznych witryn internetowych](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Aplikacje innych firm** | Ograniczona obsługa <br><br>Aplikacje innych firm, które używają interfejsów API REST do pracy, będą w dalszym ciągu działać, jeśli można ich użyć z Data Lake Storage Gen2. <br>W przypadku aplikacji, która używa interfejsów API usługi Blob ta aplikacja najprawdopodobniej mieć problemy, korzystając z tej aplikacji przy użyciu Data Lake Storage Gen2. Aby dowiedzieć się więcej, zobacz [Blob storage, interfejsy API są wyłączone dla kont magazynu usługi Data Lake Storage Gen2](#blob-apis-disabled) dalszej części tego artykułu.|
| **Funkcji przechowywania wersji** |Nie jest jeszcze obsługiwany <br><br>Obejmuje to [migawek](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) i [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|
|

