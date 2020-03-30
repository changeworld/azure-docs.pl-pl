---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471709"
---
Na razie tylko dyski SSD premium mogą włączyć dyski współdzielone. Rozmiary dysków obsługujące tę funkcję to P15 i większe. Różne rozmiary dysków `maxShares` mogą mieć inny limit, `maxShares` którego nie można przekroczyć podczas ustawiania wartości.

Dla każdego dysku można `maxShares` zdefiniować wartość reprezentującą maksymalną liczbę węzłów, które mogą jednocześnie współużytkować dysk. Na przykład, jeśli planujesz skonfigurować 2-węzłowy klaster `maxShares=2`trybu failover, należy ustawić . Maksymalna wartość jest górną granicą. Węzły mogą dołączyć lub opuścić klaster (zamontować lub odinstalować dysk), o `maxShares` ile liczba węzłów jest niższa niż określona wartość.

> [!NOTE]
> Wartość `maxShares` można ustawić lub edytować tylko wtedy, gdy dysk jest odłączony od wszystkich węzłów.

W poniższej tabeli przedstawiono `maxShares` dopuszczalne wartości maksymalne według rozmiaru dysku:

|Rozmiary dysków  |limit maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

`maxShares` Wartość we/wy i limity przepustowości dysku nie mają wpływu. Na przykład maksymalna liczba operacji We/Wy dysku P15 wynosi 1100, niezależnie od tego, czy maxShares = 1 lub maxShares > 1.