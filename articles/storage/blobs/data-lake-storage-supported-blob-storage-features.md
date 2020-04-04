---
title: Funkcje magazynu obiektów Blob dostępne w usłudze Azure Data Lake Storage Gen2 | Dokumenty firmy Microsoft
description: Dowiedz się, jakich funkcji magazynu obiektów Blob można używać w usłudze Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b270ce3cd15dbd1e8dd53bd60376a87d6e08f75c
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637213"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funkcje magazynu obiektów Blob dostępne w usłudze Azure Data Lake Storage Gen2

Funkcje magazynu obiektów blob, takie jak rejestrowanie [diagnostyczne, warstwy dostępu](storage-blob-storage-tiers.md)i zasady [zarządzania cyklem](../common/storage-analytics-logging.md) [życia magazynu obiektów blob,](storage-lifecycle-management-concepts.md) działają teraz z kontami, które mają hierarchiczny obszar nazw. W związku z tym można włączyć hierarchiczne przestrzenie nazw na kontach magazynu obiektów Blob bez utraty dostępu do tych funkcji.

W tej tabeli wymieniono funkcje magazynu obiektów Blob, których można używać z usługą Azure Data Lake Storage Gen2. Elementy, które pojawiają się w tych tabelach będą się zmieniać wraz z czasem, gdy obsługa nadal się rozwija.

## <a name="supported-blob-storage-features"></a>Obsługiwane funkcje magazynu obiektów blob

> [!NOTE]
> Poziom pomocy technicznej odnosi się tylko do sposobu, w jaki funkcja jest obsługiwana w u źródła danych Usługi Data Lake Storage Gen2.

|Funkcja Magazynu obiektów blob |Poziom wsparcia |Pokrewne artykuły: |
|---------------|-------------------|---|
|Warstwa dostępu Gorąca|Ogólnie dostępne|[Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum](storage-blob-storage-tiers.md)|
|Warstwa dostępu Chłodna|Ogólnie dostępne|[Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum](storage-blob-storage-tiers.md)|
|Zdarzenia|Ogólnie dostępne|[Reagowanie na zdarzenia usługi Blob Storage](storage-blob-event-overview.md)|
|Metryki (klasyczny)|Ogólnie dostępne|[Metryki analizy usługi Azure Storage (klasyczne)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metryki w usłudze Azure Monitor|Ogólnie dostępne|[Metryki usługi Azure Storage w usłudze Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Polecenia programu PowerShell magazynu obiektów blob|Ogólnie dostępne|[Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą programu PowerShell](storage-quickstart-blobs-powershell.md)|
|Polecenia interfejsu wiersza polecenia platformy CLI usługi Blob storage Azure|Ogólnie dostępne|[Szybki start: tworzenie, pobieranie i wystawianie obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure](storage-quickstart-blobs-cli.md)|
|Interfejsy API magazynu obiektów blob|Ogólnie dostępne|[Szybki start: biblioteka klienta magazynu obiektów Blob platformy Azure w wersji 12 dla platformy .NET](storage-quickstart-blobs-dotnet.md)<br>[Szybki start: zarządzanie obiektami blob za pomocą sdk Java w wersji 12](storage-quickstart-blobs-java.md)<br>[Szybki start: zarządzanie obiektami blob za pomocą modułu SDK języka Python w wersji 12](storage-quickstart-blobs-python.md)<br>[Szybki start: zarządzanie obiektami blob za pomocą kodu JavaScript w wersji 12 SDK w pliku Node.js](storage-quickstart-blobs-nodejs.md)|
|Warstwa dostępu archiwum|Wersja zapoznawcza|[Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum](storage-blob-storage-tiers.md)|
|Zasady zarządzania cyklem życia|Wersja zapoznawcza|[Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)|
|Dzienniki diagnostyczne|Ogólnie dostępne|[Rejestrowanie analityki magazynu platformy Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Źródło zmian|Jeszcze nie jest obsługiwana|[Obsługa kanału informacyjnego zmiany w magazynie obiektów Blob platformy Azure](storage-blob-change-feed.md)|
|Przewijenie awaryjne konta|Jeszcze nie jest obsługiwana|[Odzyskiwanie po awarii i przewijanie awaryjne konta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Kontener obiektów blob ACL|Jeszcze nie jest obsługiwana|[Ustawianie listy ACL kontenera](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domeny niestandardowe|Jeszcze nie jest obsługiwana|[Mapowanie domeny niestandardowej do punktu końcowego magazynu obiektów Blob platformy Azure](storage-custom-domain-name.md)|
|Niezmienna pamięć masowa|Jeszcze nie jest obsługiwana|[Przechowywanie danych obiektów blob o znaczeniu krytycznym dla firmy z niezmiennym magazynem](storage-blob-immutable-storage.md)|
|Migawki|Jeszcze nie jest obsługiwana|[Tworzenie migawki obiektu blob i zarządzanie nim w pliku .NET](storage-blob-snapshots.md)|
|Usuwanie nietrwałe|Jeszcze nie jest obsługiwana|[Soft delete for Azure Storage blobs](storage-blob-soft-delete.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)|
|Statyczne witryny internetowe|Jeszcze nie jest obsługiwana|[Hostowanie statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website.md)|
|Logowanie w usłudze Azure Monitor|Jeszcze nie jest obsługiwana|Jeszcze niedostępne|
|Obiekty blob bloków premium|Jeszcze nie jest obsługiwana|[Tworzenie konta BlockBlobStorage](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Zobacz też

- [Znane problemy z usługą Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Usługi platformy Azure obsługujące usługę Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Platformy open source obsługujące usługę Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Dostęp do wielu protokołów w usłudze Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)