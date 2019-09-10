---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "67183443"
---
| Resource | Cel        |
|----------|---------------|
| Maksymalny rozmiar pojedynczego kontenera obiektów BLOB | Taka sama jak Maksymalna pojemność konta magazynu |
| Maksymalna liczba bloków w blokowym obiekcie blob lub dołączeniu obiektu BLOB | bloki 50 000 |
| Maksymalny rozmiar bloku w blokowym obiekcie blob | 100 MiB |
| Maksymalny rozmiar bloku obiektu BLOB | 50 000 X 100 MiB (około 4,75 TiB) |
| Maksymalny rozmiar bloku w obiekcie blob dołączania | 4 MiB |
| Maksymalny rozmiar obiektu BLOB dołączania | 50 000 x 4 MiB (około 195 GiB) |
| Maksymalny rozmiar obiektu BLOB stronicowania | 8 TiB |
| Maksymalna liczba przechowywanych zasad dostępu dla kontenera obiektów BLOB | 5 |
|Docelowa przepływność dla pojedynczego obiektu BLOB |Limity ruchu przychodzącego/wychodzącego do konta magazynu<sup>1</sup> |

<sup>1</sup> przepływność pojedynczego obiektu zależy od kilku czynników, w tym między innymi: współbieżność, rozmiar żądania, warstwa wydajności, szybkość źródła dla operacji przekazywania oraz miejsce docelowe do pobrania. Aby skorzystać z ulepszeń wydajności [blokowych obiektów BLOB o wysokiej przepływności](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) , Użyj rozmiaru obiektu BLOB lub bloku Put dla żądania > 4 MiB (> 256 KiB dla magazynu obiektów BLOB w warstwie premium lub Data Lake Storage Gen2).
