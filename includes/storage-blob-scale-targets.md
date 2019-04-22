---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/11/2019
ms.author: tamram
ms.openlocfilehash: b3e2f018a3f1ba2563ba8cf2df6dfd4959be592e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737220"
---
| Zasób | Środowisko docelowe        |
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

<sup>1</sup> przepływność pojedynczego obiektu zależy od wielu czynników, w tym między innymi: współbieżność, rozmiar operacji, warstwy wydajności, szybkości źródła przekazywania i lokalizację docelową dla plików do pobrania.