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
ms.openlocfilehash: d96e69fb526cff633c78e9ac8a1679762014cd4b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133193"
---
Usługa Azure Storage oferuje kilka typów kont magazynu. Każdy typ obsługuje różne funkcje i ma swój własny model cen. Przed utworzeniem konta magazynu, aby określić typ konta, które sprawdza się najlepiej w aplikacjach, należy wziąć pod uwagę te różnice. Dostępne są następujące typy kont magazynu:

- **Konta ogólnego przeznaczenia v2**: Typ konta magazynu podstawowego dla obiektów blob, plików, kolejek i tabel. Zalecane w przypadku większości scenariuszy za pomocą usługi Azure Storage.
- **Konta ogólnego przeznaczenia w wersji 1**: Typ konta starszej wersji dla obiektów blob, plików, kolejek i tabel. Zamiast tego użyj kont ogólnego przeznaczenia w wersji 2, gdy jest to możliwe.
- **Blokowanie kont usługi blob storage**: Konta magazynu tylko dla obiektów blob o charakterystyki wydajności premium. Zalecane w przypadku scenariuszy z transakcji o wysokiej szybkości, przy użyciu obiektów mniejsze lub wymagających magazynu spójnie niskie opóźnienia.
- **Konta usługi storage (wersja zapoznawcza) FileStorage**: Konta magazynu tylko do plików o charakterystyki wydajności premium. Zalecane w przypadku przedsiębiorstw lub o wysokiej wydajności aplikacji w skali.
- **Konta usługi blob storage**: Konta magazynu tylko dla obiektów blob. Zamiast tego użyj kont ogólnego przeznaczenia w wersji 2, gdy jest to możliwe.

W poniższej tabeli opisano typy kont magazynu i ich funkcji:

| Typ konta magazynu | Obsługiwane usługi                       | Warstwy wydajności obsługiwane      | Warstwy dostępu obsługiwane         | Opcje replikacji               | Model wdrażania<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Szyfrowanie<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Ogólnego przeznaczenia w wersji 2   | Obiekt blob, plików, kolejki, tabeli i dysku       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gorąca, chłodna, archiwum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Zaszyfrowane              |
| Ogólnego przeznaczenia w wersji 1   | Obiekt blob, plików, kolejki, tabeli i dysku       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | ND                            | LRS, GRS, RA-GRS                  | Klasyczna usługa Resource Manager    | Zaszyfrowane              |
| Usługi Block blob storage   | Obiekt blob (blokowe obiekty BLOB i uzupełnialnych obiektów blob tylko) | Premium                       | ND                            | LRS                               | Resource Manager             | Zaszyfrowane              |
| FileStorage (wersja zapoznawcza)   | Dozwolone są tylko pliki | Premium                       | ND                            | LRS                               | Resource Manager             | Zaszyfrowane              |
| Blob Storage         | Obiekt blob (blokowe obiekty BLOB i uzupełnialnych obiektów blob tylko) | Standardowa (Standard)                      | Gorąca, chłodna, archiwum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Zaszyfrowane              |

<div id="deployment-model"><sup>1</sup>zaleca się użycie modelu wdrażania usługi Azure Resource Manager. W niektórych lokalizacjach, nadal można utworzyć konta magazynu przy użyciu klasycznego modelu wdrażania, a istniejące konta klasycznego w dalszym ciągu obsługiwana. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">usługi Azure Resource Manager a klasycznym wdrożeniu: Modele wdrażania i stanu zasobów</a>.</div>

<div id="encryption"><sup>2</sup>wszystkie konta magazynu są szyfrowane przy użyciu szyfrowania usługi Storage (SSE) dla danych magazynowanych. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">szyfrowanie usługi Azure Storage dla danych magazynowanych</a>.</div>

<div id="archive"><sup>3</sup>warstwy archiwum jest dostępna na poziomie obiektu blob poszczególnych tylko, nie na poziomie konta magazynu. Tylko blokowe obiekty BLOB i uzupełnialnych obiektów blob można znaleźć w temacie. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">usługi Azure Blob storage: Gorąca, chłodna i archiwalnego</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup>magazyn strefowo nadmiarowy (ZRS) jest dostępna tylko dla kont magazynu ogólnego przeznaczenia standard w wersji 2. Aby uzyskać więcej informacji na temat magazynu ZRS, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">magazyn strefowo nadmiarowy (ZRS): Aplikacje usługi Azure Storage o wysokiej dostępności</a>. Aby uzyskać więcej informacji na temat innych opcji replikacji, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">replikacja usługi Azure Storage</a>.</div>

<div id="premium-performance"><sup>5</sup>wydajność warstwy premium dla kont ogólnego przeznaczenia w wersji 1 i ogólnego przeznaczenia w wersji 2 jest dostępna dla dysku i strony obiektu blob tylko.</div>
