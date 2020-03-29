---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392411"
---
| Zasób | Środowisko docelowe        |
|----------|---------------|
| Maksymalny rozmiar pojedynczego kontenera obiektów blob | Tak samo jak maksymalna pojemność konta magazynu |
| Maksymalna liczba bloków w bloku blob lub dołącz obiekt blob | 50 000 bloków |
| Maksymalny rozmiar bloku w bloku blob | 100 MiB |
| Maksymalny rozmiar bloku blob | 50 000 X 100 MiB (około 4,75 TiB) |
| Maksymalny rozmiar bloku w doliczanym bloku blob | 4 MiB |
| Maksymalny rozmiar dołączanego obiektu blob | 50 000 x 4 MiB (około 195 GiB) |
| Maksymalny rozmiar obiektu blob strony | 8 TiB |
| Maksymalna liczba przechowywanych zasad dostępu na kontener obiektu blob | 5 |
|Stawka żądania docelowego dla pojedynczego obiektu blob | Do 500 żądań na sekundę |
|Przepływność docelowa dla pojedynczego obiektu blob | Do 60 MiB na sekundę |
|Przepływność docelowa dla pojedynczego bloku blob |Do limitów transferu danych przychodzących/wychodzących konta magazynu<sup>1</sup> |

<sup>1</sup> Przepływność dla pojedynczego obiektu blob zależy od kilku czynników, w tym, ale nie ograniczając się do: współbieżność, rozmiar żądania, warstwa wydajności, szybkość źródła przekazywania i miejsce docelowe dla pobrań. Aby skorzystać z ulepszeń wydajności [bloków bloków o wysokiej przepływie,](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)przekaż większe obiekty blob lub bloki. W szczególności wywołać [Put Blob](/rest/api/storageservices/put-blob) lub [Put Block](/rest/api/storageservices/put-block) operacji o rozmiarze obiektu blob lub bloku, który jest większy niż 4 MiB dla standardowych kont magazynu. W przypadku obiektów blob bloku w wersji premium lub kont magazynu Usługi Data Lake Storage Gen2 należy użyć rozmiaru bloku lub obiektu blob większego niż 256 KiB.
