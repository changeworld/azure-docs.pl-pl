---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9000cab026ec08bd78d8a8f334c8967a8a747c95
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202296"
---
Na razie tylko dysków SSD Premium może włączyć dyski udostępnione. Rozmiary dysków, które obsługują tę funkcję, są P15 i większe. Różne rozmiary dysków mogą mieć inny limit `maxShares`, którego nie można przekroczyć podczas ustawiania `maxShares` wartości.

Dla każdego dysku można zdefiniować wartość `maxShares`, która reprezentuje maksymalną liczbę węzłów, które mogą jednocześnie udostępniać dysk. Na przykład jeśli planujesz skonfigurować klaster trybu failover z 2 węzłami, należy ustawić `maxShares=2`. Wartość maksymalna jest górną granicą. Węzły mogą dołączyć lub opuścić klaster (zainstalować lub odinstalować dysk) tak długo, jak liczba węzłów jest mniejsza niż określona wartość `maxShares`.

> [!NOTE]
> Wartość `maxShares` można ustawić lub edytować tylko wtedy, gdy dysk jest odłączony od wszystkich węzłów.

W poniższej tabeli przedstawiono dozwolone maksymalne wartości `maxShares` według rozmiaru dysku:

|Rozmiary dysków  |limit maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Wartość `maxShares` nie ma wpływ na limity IOPS i przepustowość dla dysku. Na przykład maksymalna liczba operacji we/wy dysku P15 to 1100, czy maxShares = 1 lub maxShares > 1.