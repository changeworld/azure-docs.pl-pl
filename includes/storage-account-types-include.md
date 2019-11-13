---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 1a9cebe334b266dfcf7f06608fd6a526ded7179b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73998418"
---
Usługa Azure Storage oferuje kilka typów kont magazynu. Każdy typ obsługuje różne funkcje i ma własny model cen. Te różnice należy wziąć pod uwagę przed utworzeniem konta magazynu w celu określenia typu konta najlepszego dla aplikacji. Oto typy kont magazynu:

- **Konta ogólnego przeznaczenia w wersji 2**: typ konta magazynu podstawowego dla obiektów blob, plików, kolejek i tabel. Zalecane w przypadku większości scenariuszy korzystających z usługi Azure Storage.
- **Konta ogólnego przeznaczenia w wersji 1**: typ konta starsze dla obiektów blob, plików, kolejek i tabel. Zamiast tego używaj kont ogólnego przeznaczenia w wersji 2.
- **Konta BlockBlobStorage**: konta magazynu tylko dla obiektów blob z charakterystyką wydajności Premium. Zalecane w scenariuszach z wysokimi stawkami transakcji, przy użyciu mniejszych obiektów lub wymagających ciągłej niewielkim opóźnieniem magazynu.
- **Konta FileStorage**: tylko pliki magazynu z charakterystyką wydajności Premium. Zalecane w przypadku aplikacji do skalowania w przedsiębiorstwie lub wysokiej wydajności.
- **Konta BlobStorage**: starsze konta magazynu tylko na poziomie obiektów BLOB. Zamiast tego używaj kont ogólnego przeznaczenia w wersji 2.

W poniższej tabeli opisano typy kont magazynu i ich możliwości:

| Typ konta magazynu | Obsługiwane usługi                       | Obsługiwane warstwy wydajności      | Obsługiwane warstwy dostępu         | Opcje replikacji               | Model wdrażania<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Szyfrowanie<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Ogólnego przeznaczenia w wersji 2   | Obiekt BLOB, plik, kolejka, tabela i dysk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gorąca, chłodna, archiwalna<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (wersja zapoznawcza), RA-GZRS (wersja zapoznawcza)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Zaszyfrowane              |
| Ogólnego przeznaczenia w wersji 1   | Obiekt BLOB, plik, kolejka, tabela i dysk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Nie dotyczy                            | LRS, GRS, RA-GRS                  | Menedżer zasobów, klasyczny    | Zaszyfrowane              |
| BlockBlobStorage   | Obiekt BLOB (Blokuj obiekty blob i Dołącz tylko obiekty blob) | Premium                       | Nie dotyczy                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Zaszyfrowane              |
| FileStorage   | Tylko pliki | Premium                       | Nie dotyczy                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Zaszyfrowane              |
| BlobStorage         | Obiekt BLOB (Blokuj obiekty blob i Dołącz tylko obiekty blob) | Standardowa (Standard)                      | Gorąca, chłodna, archiwalna<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Zaszyfrowane              |

<div id="deployment-model"><sup>1</sup> Zalecane jest korzystanie z modelu wdrażania Azure Resource Manager. Konta magazynu korzystające z klasycznego modelu wdrażania można nadal tworzyć w niektórych lokalizacjach, a istniejące konta klasyczne są nadal obsługiwane. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager a wdrożenie klasyczne: Omówienie modeli wdrażania i stanu zasobów</a>.</div>

<div id="encryption"><sup>2</sup> Wszystkie konta magazynu są szyfrowane przy użyciu szyfrowanie usługi Storage (SSE) dla danych przechowywanych. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">szyfrowanie usługi Storage platformy Azure dla danych przechowywanych w spoczynku</a>.</div>

<div id="archive"><sup>3</sup> Warstwa archiwum jest dostępna tylko na poziomie pojedynczego obiektu BLOB, a nie na poziomie konta magazynu. Można archiwizować tylko blokowe obiekty blob i dołączanie obiektów BLOB. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: warstwy magazynowania gorąca, chłodna i archiwalna</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup> Magazyn strefowo nadmiarowy (ZRS) i geograficznie nadmiarowy magazyn (GZRS/RA-GZRS) (wersja zapoznawcza) są dostępne tylko dla kont standardowych ogólnego przeznaczenia w wersji 2, BlockBlobStorage i FileStorage w określonych regionach. Aby uzyskać więcej informacji na temat ZRS, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">Magazyn strefowo nadmiarowy (ZRS): wysoce dostępne aplikacje usługi Azure Storage</a>. Aby uzyskać więcej informacji na temat GZRS/RA-GZRS, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">Geograficznie nadmiarowy magazyn w celu zapewnienia wysokiej dostępności i maksymalnej trwałości (wersja zapoznawcza)</a>. Aby uzyskać więcej informacji na temat innych opcji replikacji, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Replikacja usługi Azure Storage</a>.</div>

<div id="premium-performance"><sup>5</sup> Wydajność warstwy Premium dla kont ogólnego przeznaczenia w wersji 2 i ogólnego przeznaczenia w wersji 1 jest dostępna tylko dla dysków i stron obiektów BLOB.</div>
