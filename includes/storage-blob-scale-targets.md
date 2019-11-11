---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 0fda881b805eb3a967cf3b05f6c6df8c65d20730
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905792"
---
| Zasób | Środowisko docelowe        |
|----------|---------------|
| Maksymalny rozmiar pojedynczego kontenera obiektów BLOB | Taka sama jak Maksymalna pojemność konta magazynu |
| Maksymalna liczba bloków w blokowym obiekcie blob lub dołączeniu obiektu BLOB | bloki 50 000 |
| Maksymalny rozmiar bloku w blokowym obiekcie blob | 100 MiB |
| Maksymalny rozmiar bloku obiektu BLOB | 50 000 X 100 MiB (około 4,75 TiB) |
| Maksymalny rozmiar bloku w obiekcie blob dołączania | 4 MiB |
| Maksymalny rozmiar obiektu BLOB dołączania | 50 000 x 4 MiB (około 195 GiB) |
| Maksymalny rozmiar obiektu BLOB stronicowania | 8 TiB |
| Maksymalna liczba przechowywanych zasad dostępu dla kontenera obiektów BLOB | 5 |
|Docelowa stawka żądania dla pojedynczego obiektu BLOB | Do 500 żądań na sekundę |
|Docelowa przepływność dla jednostronicowego obiektu BLOB | Do 60 MiB na sekundę |
|Docelowa przepływność dla pojedynczego bloku obiektu BLOB |Limity ruchu przychodzącego/wychodzącego do konta magazynu<sup>1</sup> |

<sup>1</sup> przepływność pojedynczego obiektu zależy od kilku czynników, w tym między innymi: współbieżność, rozmiar żądania, warstwa wydajności, szybkość źródła dla operacji przekazywania oraz miejsce docelowe do pobrania. Aby skorzystać z ulepszeń wydajności [blokowych obiektów BLOB o wysokiej przepływności](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) , Użyj rozmiaru obiektu BLOB lub bloku Put dla żądania > 4 MiB (> 256 KiB dla magazynu obiektów BLOB w warstwie premium lub Data Lake Storage Gen2).
