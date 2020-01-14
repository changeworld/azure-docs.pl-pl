---
title: Dostęp przez wiele protokołów w Azure Data Lake Storage | Microsoft Docs
description: Używaj interfejsów API obiektów blob i aplikacji, które używają interfejsów API obiektów blob z Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f34c5d5069a158579864320d0fbf965de8936d9c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896108"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Dostęp z wieloprotokołem do Azure Data Lake Storage

Interfejsy API obiektów BLOB działają teraz z kontami, które mają hierarchiczną przestrzeń nazw. Powoduje to odblokowanie ekosystemu narzędzi, aplikacji i usług, a także kilku funkcji magazynu obiektów BLOB dla kont, które mają hierarchiczną przestrzeń nazw.

Do tej pory mogło być konieczne przechowywanie oddzielnych rozwiązań magazynu dla magazynu obiektów i magazynu analizy. Dzieje się tak, ponieważ Azure Data Lake Storage Gen2 miała ograniczoną obsługę ekosystemu. Miał także ograniczony dostęp do funkcji Blob service, takich jak rejestrowanie diagnostyczne. Pofragmentowane rozwiązanie magazynu jest trudne do utrzymania, ponieważ trzeba przenieść dane między kontami, aby osiągnąć różne scenariusze. Nie musisz już tego robić.

W przypadku dostępu z użyciem protokołu wieloprotokołowego do Data Lake Storage można korzystać z danych przy użyciu ekosystemu narzędzi, aplikacji i usług. Obejmuje to również narzędzia i aplikacje innych firm. Można wskazać te konta, które mają hierarchiczną przestrzeń nazw bez konieczności ich modyfikacji. Aplikacje te działają *podobnie jak* w przypadku wywoływania interfejsów API obiektów blob, ponieważ interfejsy API obiektów BLOB mogą teraz operować na danych na kontach z hierarchiczną przestrzenią nazw.

Funkcje magazynu obiektów blob, takie jak [Rejestrowanie diagnostyki](../common/storage-analytics-logging.md), [warstwy dostępu](storage-blob-storage-tiers.md)i [zasady zarządzania cyklem życia magazynu obiektów BLOB](storage-lifecycle-management-concepts.md) , działają teraz z kontami, które mają hierarchiczną przestrzeń nazw. W związku z tym można włączyć hierarchiczne przestrzenie nazw na kontach magazynu obiektów Blob bez utraty dostępu do tych ważnych funkcji. 

> [!NOTE]
> Dostęp do wieloprotokołowy na Data Lake Storage jest ogólnie dostępny i jest dostępny we wszystkich regionach. Niektóre funkcje usług platformy Azure lub usługi BLOB Storage obsługiwane przez wieloprotokołowy dostęp pozostają w wersji zapoznawczej. Aby uzyskać więcej informacji, Zobacz tabele w poszczególnych sekcjach tego artykułu. 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Jak działa dostęp przez wiele protokołów w usłudze Data Lake Storage

Interfejsy API obiektów blob i interfejsy API Data Lake Storage Gen2 mogą działać na tych samych danych na kontach magazynu, które mają hierarchiczną przestrzeń nazw. Data Lake Storage Gen2 kieruje interfejsy API obiektów BLOB za pomocą hierarchicznej przestrzeni nazw, aby można było korzystać z zalet operacji katalogu pierwszej klasy i list kontroli dostępu zgodnych ze standardem POSIX (ACL). 

![Dostęp z wieloprotokołem do Data Lake Storage koncepcyjnych](./media/data-lake-storage-interop/interop-concept.png) 

Istniejące narzędzia i aplikacje korzystające z interfejsu API obiektów BLOB uzyskują te korzyści automatycznie. Deweloperzy nie będą musieli ich modyfikować. Data Lake Storage Gen2 konsekwentnie stosuje katalogi i listy ACL na poziomie plików, niezależnie od protokołu używanego przez narzędzia i aplikacje do uzyskiwania dostępu do danych. 

## <a name="blob-storage-feature-support"></a>Obsługa funkcji magazynu obiektów BLOB

Dostęp do wielu protokołów w Data Lake Storage umożliwia korzystanie z większej liczby funkcji magazynu obiektów blob z Data Lake Storage. Ta tabela zawiera listę funkcji, które są włączane przez wieloprotokołowy dostęp do Data Lake Storage. 

Elementy, które pojawiają się w tej tabeli, zmienią się w miarę upływu czasu, ponieważ obsługa funkcji usługi BLOB Storage będzie nadal większa. 

> [!NOTE]
> Mimo że dostęp oparty na protokole Data Lake Storage jest ogólnie dostępny, obsługa niektórych z tych funkcji pozostaje w wersji zapoznawczej. 

|Funkcja magazynu obiektów BLOB | Poziom pomocy technicznej |
|---|---|
|[Warstwa dostępu chłodna](storage-blob-storage-tiers.md)|Ogólnie dostępna|
|Interfejsy API REST obiektów BLOB|Ogólnie dostępna|
|Zestawy SDK obiektów BLOB |Ogólnie dostępna|
|[PowerShell (BLOB)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |Ogólnie dostępna|
|[Interfejs wiersza polecenia (BLOB)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |Ogólnie dostępna|
|[Powiadomienia za pośrednictwem Azure Event Grid](data-lake-storage-events.md)|Ogólnie dostępna|
|Zestawy SDK obiektów blob z semantyką systemu plików ([.net](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-dotnet) &vert; [Python](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-python) &vert; [Java](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-java))|Wersja zapoznawcza|
|[Program PowerShell z semantyką systemu plików](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-powershell)|Wersja zapoznawcza|
|[Interfejs wiersza polecenia z semantyką systemu plików](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-cli)|Wersja zapoznawcza|
|[Dzienniki diagnostyczne](../common/storage-analytics-logging.md)| Wersja zapoznawcza|
|[Zasady zarządzania cyklem życia](storage-lifecycle-management-concepts.md)| Wersja zapoznawcza|
|[Warstwa dostępu Archiwum](storage-blob-storage-tiers.md)| Wersja zapoznawcza|
|[blobfuse](storage-how-to-mount-container-linux.md)|Jeszcze nieobsługiwane|
|[Niezmienny magazyn](storage-blob-immutable-storage.md)|Jeszcze nieobsługiwane|
|[Migawki](storage-blob-snapshots.md)|Jeszcze nieobsługiwane|
|[Usuwanie nietrwałe](storage-blob-soft-delete.md)|Jeszcze nieobsługiwane|
|[Statyczne witryny sieci Web](storage-blob-static-website.md)|Jeszcze nieobsługiwane|

Aby dowiedzieć się więcej o ogólnych znanych problemach i ograniczeniach dotyczących Azure Data Lake Storage Gen2, zobacz [znane problemy](data-lake-storage-known-issues.md).

## <a name="azure-ecosystem-support"></a>Obsługa ekosystemów platformy Azure

Dostęp do wielu protokołów w Data Lake Storage umożliwia również łączenie większej liczby usług platformy Azure z Data Lake Storage. Ta tabela zawiera listę usług włączonych przez wieloprotokołowy dostęp do Data Lake Storage. 

Podobnie jak lista obsługiwanych funkcji magazynu obiektów blob, elementy wyświetlane w tej tabeli zmienią się w miarę upływu czasu, gdy obsługa usług platformy Azure zostanie powiększana. 

> [!NOTE]
> Mimo że dostęp oparty na protokole Data Lake Storage jest ogólnie dostępny, obsługa niektórych z tych usług pozostaje w wersji zapoznawczej. 

|Usługa platformy Azure | Poziom pomocy technicznej |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|Ogólnie dostępna|
|[Przechwytywanie Event Hubs platformy Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|Ogólnie dostępna|
|[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|Ogólnie dostępna|
|[IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|Ogólnie dostępna|
|[Aplikacje logiki](https://azure.microsoft.com/services/logic-apps/)|Ogólnie dostępna|
|[Wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|Wersja zapoznawcza|

Pełną listę obsługi ekosystemów platformy Azure dla Data Lake Storage Gen2 można znaleźć w temacie [integracja Azure Data Lake Storage z usługami platformy Azure](data-lake-storage-integrate-with-azure-services.md).

Aby dowiedzieć się więcej o ogólnych znanych problemach i ograniczeniach dotyczących Azure Data Lake Storage Gen2, zobacz [znane problemy](data-lake-storage-known-issues.md).

## <a name="next-steps"></a>Następne kroki

Zobacz [znane problemy](data-lake-storage-known-issues.md)




