---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 8a8619da831dfa5b240bd93d3a046c49cc30affa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901405"
---
| Zasób | Standardowe udziały plików | Udziały plików w warstwie Premium |
|----------|---------------|------------------------------------------|
| Minimalny rozmiar udziału plików | Brak wartości minimalnej; płatność zgodnie z rzeczywistym użyciem | 100 GiB; aprowizowane |
| Maksymalny rozmiar udziału plików | 100 TiB *, 5 TiB | 100 TiB |
| Maksymalny rozmiar pliku w udziale plików | 1 TiB | 1 TiB |
| Maksymalna liczba plików w udziale plików | Bez ograniczeń | Bez ograniczeń |
| Maksymalna liczba operacji we/wy na udział | 10 000 IOPS *, 1 000 OPERACJI WE/WY | LICZBA OPERACJI WE/WY 100 000 |
| Maksymalna liczba przechowywanych zasad dostępu na udział plików | 5 | 5 |
| Docelowa przepływność dla pojedynczego udziału plików | do 300 MiB/s *, do 60 MiB/s,  | Zobacz wartości dotyczące transferu plików w warstwie Premium i ruchu wychodzącego|
| Maksymalna liczba ruchu wychodzącego dla pojedynczego udziału plików | Zobacz standardową przepływność udziału plików | Do 6 204 MiB/s |
| Maksymalna liczba danych wejściowych dla pojedynczego udziału plików | Zobacz standardową przepływność udziału plików | Do 4 136 MiB/s |
| Maksymalna liczba otwartych dojść na plik | 2 000 otwartych dojść | 2 000 otwartych dojść |
| Maksymalna liczba migawek udziałów | 200 migawek udziałów | 200 migawek udziałów |
| Maksymalna długość nazwy obiektu (katalogów i plików) | 2 048 znaków | 2 048 znaków |
| Maksymalna część ścieżki składnika (w ścieżce \A\B\C\D każda litera jest składnikiem) | 255 znaków | 255 znaków |

\* dostępne w większości regionów, zobacz [dostępność regionalna](../articles/storage/files/storage-files-planning.md#regional-availability) , aby uzyskać szczegółowe informacje na temat dostępnych regionów.
