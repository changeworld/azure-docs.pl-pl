---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371560"
---
Usługa Azure Storage oferuje kilka typów kont magazynu. Każdy typ obsługuje różne funkcje i ma swój własny model cenowy. Należy wziąć pod uwagę te różnice przed utworzeniem konta magazynu, aby określić typ konta, który jest najlepszy dla aplikacji. Typy kont magazynu to:

- **Konta ogólnego przeznaczenia w wersji 2:** Podstawowy typ konta magazynu dla obiektów blob, plików, kolejek i tabel. Zalecane w większości scenariuszy przy użyciu usługi Azure Storage.
- **Konta ogólnego przeznaczenia w wersji 1:** Starszy typ konta obiektów blob, plików, kolejek i tabel. W miarę możliwości należy używać kont ogólnego przeznaczenia w wersji 2.
- **Konta BlockBlobStorage:** Konta magazynu z charakterystyką wydajności premium dla bloków obiektów blob i dołączania obiektów blob. Zalecane w scenariuszach o wysokich stawkach transakcji lub scenariuszach, które używają mniejszych obiektów lub wymagają stale niskich opóźnień w magazynie.
- **Konta FileStorage**: Konta magazynu tylko do plików z charakterystyką wydajności premium. Zalecane dla aplikacji w skali korporacyjnej lub o wysokiej wydajności.
- **Konta obiektów BlobStorage:** starsze konta magazynu tylko do obiektów blob. W miarę możliwości należy używać kont ogólnego przeznaczenia w wersji 2.

W poniższej tabeli opisano typy kont magazynu i ich możliwości:

| Typ konta magazynu | Obsługiwane usługi                       | Obsługiwane warstwy wydajności      | Obsługiwane warstwy dostępu         | Opcje replikacji               | Model wdrażania<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Szyfrowanie<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Uniwersalny V2   | Obiekty Blob, Plik, Kolejka, Tabela, Dysk i Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gorący, Chłodny, Archiwum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (wersja zapoznawcza), RA-GZRS (wersja zapoznawcza)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Zaszyfrowane              |
| Uniwersalny V1   | Obiekty Blob, Plik, Kolejka, Tabela i Dysk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Nie dotyczy                            | LRS, GRS, RA-GRS                  | Menedżer zasobów, klasyczny    | Zaszyfrowane              |
| BlockBlobStorage   | Obiekt blob (tylko blokowe obiekty blob i dołączanie obiektów blob) | Premium                       | Nie dotyczy                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Zaszyfrowane              |
| FileStorage (FileStorage)   | Tylko plik | Premium                       | Nie dotyczy                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Zaszyfrowane              |
| BlobStorage (BlobStorage)         | Obiekt blob (tylko blokowe obiekty blob i dołączanie obiektów blob) | Standardowa                      | Gorący, Chłodny, Archiwum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Zaszyfrowane              |

<div id="deployment-model"><sup>1.</sup> Zaleca się korzystanie z modelu wdrażania usługi Azure Resource Manager. Konta magazynu przy użyciu klasycznego modelu wdrażania nadal można utworzyć w niektórych lokalizacjach, a istniejące konta klasyczne są nadal obsługiwane. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Usługa Azure Resource Manager vs. wdrożenie klasyczne: Opis modeli wdrażania i stanu zasobów.</a></div><br/>

<div id="encryption"><sup>2.</sup> Wszystkie konta magazynu są szyfrowane przy użyciu szyfrowania usługi magazynu (SSE) dla danych w stanie spoczynku. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Szyfrowanie usługi Azure Storage Service dla danych w spoczynku</a>.</div><br/>

<div id="archive"><sup>3</sup> Archiwizuj magazyn i warstwowe poziomy na poziomie obiektów blob obsługują tylko bloki blob. Warstwa Archiwum jest dostępna tylko na poziomie pojedynczego obiektu blob, a nie na poziomie konta magazynu. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">warstwy magazynu obiektów Blob platformy Azure: gorąca, chłodna i archiwizowana</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup> Magazyn strefowy (ZRS) i magazyn geograficznie nadmiarowy (GZRS/RA-GZRS) (wersja zapoznawcza) są dostępne tylko dla standardowych kont ogólnego przeznaczenia V2, BlockBlobStorage i FileStorage w niektórych regionach. Aby uzyskać więcej informacji na temat opcji nadmiarowości usługi Azure Storage, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Nadmiarowość usługi Azure Storage</a>.</div><br/>

<div id="premium-performance"><sup>5</sup> Wydajność premium dla kont ogólnego przeznaczenia w wersji 2 i ogólnego przeznaczenia w wersji 1 jest dostępna tylko dla dysków i stronicowych obiektów blob. Wydajność premium dla bloków lub dołączania obiektów blob są dostępne tylko na kontach BlockBlobStorage. Wydajność premium dla plików jest dostępna tylko na kontach FileStorage.</div><br/>

<div id="data-lake-gen2"><sup>6</sup> Usługa Azure Data Lake Storage Gen2 to zestaw funkcji dedykowanych do analizy dużych zbiorów danych, opartej na magazynie obiektów Blob platformy Azure. Data Lake Storage Gen2 jest obsługiwany tylko na kontach magazynu ogólnego przeznaczenia v2 z włączoną hierarchiczną przestrzenią nazw. Aby uzyskać więcej informacji na temat usługi Data Lake Storage Gen2, zobacz <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Wprowadzenie do usługi Azure Data Lake Storage Gen2</a>.</div>
