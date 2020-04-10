---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008355"
---
Na razie tylko dyski ultra i dyski SSD premium mogą włączyć dyski współdzielone. Różne rozmiary dysków `maxShares` mogą mieć inny limit, `maxShares` którego nie można przekroczyć podczas ustawiania wartości. W przypadku dysków SSD w wersji premium rozmiary dysków obsługujące udostępnianie dysków są p15 i większe.

Dla każdego dysku można `maxShares` zdefiniować wartość reprezentującą maksymalną liczbę węzłów, które mogą jednocześnie współużytkować dysk. Na przykład, jeśli planujesz skonfigurować 2-węzłowy klaster `maxShares=2`trybu failover, należy ustawić . Maksymalna wartość jest górną granicą. Węzły mogą dołączyć lub opuścić klaster (zamontować lub odinstalować dysk), o `maxShares` ile liczba węzłów jest niższa niż określona wartość.

> [!NOTE]
> Wartość `maxShares` można ustawić lub edytować tylko wtedy, gdy dysk jest odłączony od wszystkich węzłów.

### <a name="premium-ssd-ranges"></a>Seria premium SSD

W poniższej tabeli przedstawiono `maxShares` dopuszczalne wartości maksymalne dla rozmiarów dysków w wersji premium:

|Rozmiary dysków  |limit maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

`maxShares` Wartość we/wy i limity przepustowości dysku nie mają wpływu. Na przykład maksymalna liczba operacji We/Wy dysku P15 wynosi 1100, niezależnie od tego, czy maxShares = 1 lub maxShares > 1.

### <a name="ultra-disk-ranges"></a>Zakresy dysków ultra

Minimalna `maxShares` wartość wynosi 1, `maxShares` podczas gdy maksymalna wartość wynosi 5. Nie ma żadnych ograniczeń rozmiaru na dyskach ultra, dowolny `maxShares`rozmiar ultra dysku może używać dowolnej wartości dla , do wartości maksymalnej włącznie.