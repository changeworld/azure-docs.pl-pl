---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114800"
---
| Resource | Cel        |
|----------|---------------|
| Maksymalny rozmiar pojedynczego obiektu blob kontenera | Takie same jak maksymalna pojemność konta |
| Maksymalna liczba bloków w bloku usługi blob lub uzupełnialnego obiektu blob | 50 000 bloków |
| Maksymalny rozmiar bloku w przypadku blokowych obiektów blob | 100 MiB |
| Maksymalny rozmiar blokowego obiektu blob | 50 000 x 100 MiB (około 4.75 TiB) |
| Maksymalny rozmiar bloku w uzupełnialnym obiekcie blob | 4 MiB |
| Maksymalny rozmiar uzupełnialnego obiektu blob | 50 000 x 4 MiB (około 195 GiB) |
| Maksymalny rozmiar stronicowych obiektów blob | 8 TiB |
| Maksymalna liczba przechowywanych zasad dostępu dla kontenera obiektów blob | 5 |
|Docelowa przepustowość dla pojedynczego obiektu blob |Maksymalnie limity ruchem przychodzącym/wychodzącym konta magazynu<sup>1</sup> |

<sup>1</sup> przepływność pojedynczego obiektu zależy od wielu czynników, w tym między innymi: współbieżność, rozmiar żądania, warstwy wydajności, szybkości źródła przekazywania i lokalizację docelową dla plików do pobrania. Aby móc korzystać z [o wysokiej przepływności blokowych obiektów blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) ulepszenia wydajności używany rozmiar żądania Put obiektów Blob lub umieścić blok > 4 MiB (> 256 KiB usługi wydajność warstwy premium block blob storage lub Data Lake Storage Gen2).
