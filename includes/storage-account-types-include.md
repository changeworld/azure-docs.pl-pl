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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371560"
---
Usługa Azure Storage oferuje kilka typów kont magazynu. Każdy typ obsługuje różne funkcje i ma własny model cen. Zanim utworzysz konto magazynu, weź pod uwagę te różnice. Umożliwi Ci to wybranie najlepszego typu konta dla używanych aplikacji. Typy kont magazynu:

- **Konta ogólnego przeznaczenia w wersji 2**: Podstawowy typ konta magazynu dla obiektów blob, plików, kolejek i tabel. Jest on zalecany dla większości scenariuszy korzystających z usługi Azure Storage.
- **Konta ogólnego przeznaczenia w wersji 1**: Starszy typ konta dla obiektów blob, plików, kolejek i tabel. Wszędzie, gdzie jest to możliwe, zamiast tego typu używaj kont ogólnego przeznaczenia w wersji 2.
- **Konta BlockBlobStorage**: Konta magazynu z charakterystyką wydajności Premium dla blokowych obiektów blob i uzupełnialnych obiektów blob. Zalecane w scenariuszach z wysoką szybkością transakcji lub scenariuszach, które używają mniejszych obiektów bądź wymagają spójnie niskiego opóźnienia magazynu.
- **Konta FileStorage**: Konta magazynu tylko dla plików z charakterystyką wydajności Premium. Zalecane w przypadku aplikacji dla przedsiębiorstw lub skalowanych aplikacji o wysokiej wydajności.
- **Konta BlobStorage**: Starsze konta magazynu tylko dla obiektów blob. Wszędzie, gdzie jest to możliwe, zamiast tego typu używaj kont ogólnego przeznaczenia w wersji 2.

W poniższej tabeli opisano typy kont magazynu oraz ich możliwości:

| Typ konta magazynu | Obsługiwane usługi                       | Obsługiwane warstwy wydajności      | Obsługiwane warstwy dostępu         | Opcje replikacji               | Model wdrażania<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Szyfrowanie<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Ogólnego przeznaczenia w wersji 2   | Obiekty blob, pliki, kolejki, tabele, dyski i Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standardowa, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gorąca, Chłodna i Archiwum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (wersja zapoznawcza), RA-GZRS (wersja zapoznawcza)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Zaszyfrowane              |
| Ogólnego przeznaczenia w wersji 1   | Obiekty blob, pliki, kolejki, tabele i dyski       | Standardowa, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Nie dotyczy                            | LRS, GRS, RA-GRS                  | Resource Manager, model klasyczny    | Zaszyfrowane              |
| BlockBlobStorage   | Obiekty blob (tylko blokowe obiekty blob i uzupełnialne obiekty blob) | Premium                       | Nie dotyczy                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Zaszyfrowane              |
| FileStorage   | Tylko pliki | Premium                       | Nie dotyczy                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Zaszyfrowane              |
| BlobStorage         | Obiekty blob (tylko blokowe obiekty blob i uzupełnialne obiekty blob) | Standardowa (Standard)                      | Gorąca, Chłodna i Archiwum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Zaszyfrowane              |

<div id="deployment-model"><sup>1</sup>Zalecane jest użycie modelu wdrażania usługi Azure Resource Manager. Konta magazynu korzystające z klasycznego modelu wdrażania można nadal tworzyć w niektórych lokalizacjach, a istniejące konta klasyczne są nadal obsługiwane. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Porównanie usługi Azure Resource Manager i wdrożenia klasycznego: omówienie modeli wdrażania i stanu zasobów</a>.</div><br/>

<div id="encryption"><sup>2</sup>Dane magazynowane na wszystkich kontach magazynu są szyfrowane przy użyciu szyfrowania usługi Storage (SSE). Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Szyfrowanie usługi Azure Storage dla danych magazynowanych</a>.</div><br/>

<div id="archive"><sup>3</sup> Magazyn Archiwum i funkcja obsługi warstw na poziomie obiektów blob obsługują tylko blokowe obiekty blob. Warstwa magazynowania Archiwum jest dostępna tylko na poziomie poszczególnych obiektów blob. Nie jest ona dostępna na poziomie konta magazynu. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: warstwy magazynowania Gorąca, Chłodna i Archiwum</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup>Magazyn strefowo nadmiarowy (ZRS) oraz magazyn geograficznie i strefowo nadmiarowy (GZRS/RA-GZRS) (wersja zapoznawcza) są dostępne tylko dla standardowych kont ogólnego przeznaczenia w wersji 2 oraz kont BlockBlobStorage i FileStorage w niektórych regionach. Aby uzyskać więcej informacji dotyczących opcji nadmiarowości w usłudze Azure Storage, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Nadmiarowość w usłudze Azure Storage</a>.</div><br/>

<div id="premium-performance"><sup>5</sup>Wydajność warstwy Premium dla kont ogólnego przeznaczenia w wersji 2 i ogólnego przeznaczenia w wersji 1 jest dostępna tylko dla dysków i stronicowych obiektów blob. Wydajność warstwy Premium dla blokowych obiektów blob i uzupełnialnych obiektów blob jest dostępna tylko w przypadku kont BlockBlobStorage. Wydajność warstwy Premium dla plików jest dostępna tylko w przypadku kont FileStorage.</div><br/>

<div id="data-lake-gen2"><sup>6</sup>Azure Data Lake Storage Gen2 to zestaw funkcji przeznaczonych do analizy danych big data, oparty na usłudze Azure Blob Storage. Jednostka Data Lake Storage Gen2 jest obsługiwana tylko na kontach magazynu ogólnego przeznaczenia w wersji 2 z włączoną hierarchiczną przestrzenią nazw. Aby uzyskać więcej informacji na temat usługi Data Lake Storage Gen2, zobacz <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Wprowadzenie do usługi Azure Data Lake Storage Gen2</a>.</div>
