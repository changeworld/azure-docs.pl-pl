---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/02/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b278cce8f885f31f9d59fd389261812e04e58405
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2019
ms.locfileid: "54066994"
---
Usługa Azure Storage oferuje trzy rodzaje kont magazynu. Każdy typ obsługuje różne funkcje i ma swój własny model cen. Przed utworzeniem konta magazynu, aby określić typ konta, które sprawdza się najlepiej w aplikacjach, należy wziąć pod uwagę te różnice. Są trzy typy kont magazynu:

* **Konta ogólnego przeznaczenia v2**: Typ konta magazynu podstawowego dla obiektów blob, plików, kolejek i tabel. Zalecane w przypadku większości scenariuszy za pomocą usługi Azure Storage.
* **Konta ogólnego przeznaczenia w wersji 1**: Typ konta starszej wersji dla obiektów blob, plików, kolejek i tabel. Zamiast tego użyj kont ogólnego przeznaczenia w wersji 2, gdy jest to możliwe.
* **Konta usługi blob storage**: Konta magazynu tylko dla obiektów blob. Zamiast tego użyj kont ogólnego przeznaczenia w wersji 2, gdy jest to możliwe. 

W poniższej tabeli opisano typy kont magazynu i ich funkcji:

| Typ konta magazynu | Obsługiwane usługi                       | Warstwy wydajności obsługiwane | Warstwy dostępu obsługiwane               | Opcje replikacji                                                | Model wdrażania<sup>1</sup>  | Szyfrowanie<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------------|--------------------------------------------------------------------|-------------------|------------|
| Ogólnego przeznaczenia w wersji 2   | Obiekt blob, plików, kolejki, tabeli i dysku       | Standard i Premium           | Gorąca, chłodna, archiwum<sup>3</sup> | MAGAZYN LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager | Zaszyfrowane  |
| Ogólnego przeznaczenia w wersji 1   | Obiekt blob, plików, kolejki, tabeli i dysku       | Standard i Premium           | ND                                  | LRS, GRS, RA-GRS                                                   | Klasyczna usługa Resource Manager  | Zaszyfrowane  |
| Blob Storage         | Obiekt blob (blokowe obiekty BLOB i uzupełnialnych obiektów blob tylko) | Standardowa (Standard)                    | Gorąca, chłodna, archiwum<sup>3</sup>                            | LRS, GRS, RA-GRS                                                   | Resource Manager  | Zaszyfrowane  |

<sup>1</sup>zaleca się użycie modelu wdrażania usługi Azure Resource Manager. W niektórych lokalizacjach, nadal można utworzyć konta magazynu przy użyciu klasycznego modelu wdrażania, a istniejące konta klasycznego w dalszym ciągu obsługiwana. Aby uzyskać więcej informacji, zobacz [usługi Azure Resource Manager a klasycznym wdrożeniu: Modele wdrażania i stanu zasobów](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>wszystkie konta magazynu są szyfrowane przy użyciu szyfrowania usługi Storage (SSE) dla danych magazynowanych. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>warstwy archiwum jest dostępna na poziomie obiektu blob poszczególnych tylko, nie na poziomie konta magazynu. Tylko blokowe obiekty BLOB i uzupełnialnych obiektów blob można znaleźć w temacie. Aby uzyskać więcej informacji, zobacz [usługi Azure Blob storage: Gorąca, chłodna i archiwalnego](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>magazyn strefowo nadmiarowy (ZRS) jest dostępna tylko dla kont magazynu ogólnego przeznaczenia standard w wersji 2. Aby uzyskać więcej informacji na temat magazynu ZRS, zobacz [magazyn strefowo nadmiarowy (ZRS): Aplikacje usługi Azure Storage o wysokiej dostępności](../articles/storage/common/storage-redundancy-zrs.md). Aby uzyskać więcej informacji na temat innych opcji replikacji, zobacz [replikacja usługi Azure Storage](../articles/storage/common/storage-redundancy.md).
