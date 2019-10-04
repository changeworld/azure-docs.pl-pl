---
title: Węzły w Azure Database for PostgreSQL — Citus (wersja zapoznawcza)
description: Zapoznaj się z dwoma typami węzłów, koordynatorem i pracownikami w grupie serwerów w Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 097fcdb3a7e53bb63db9dc2d352d754062df7be6
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947551"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Węzły w Azure Database for PostgreSQL — Citus (wersja zapoznawcza)

Typ hostingu Citus (wersja zapoznawcza) umożliwia Azure Database for PostgreSQL serwerów (nazywanych węzłami) do współrzędnych ze sobą w architekturze "udostępniona Nothing". Węzły w grupie serwerów wspólnie przechowują więcej danych i wykorzystują więcej rdzeni procesora CPU niż jest to możliwe na jednym serwerze. Architektura umożliwia również skalowanie bazy danych przez dodanie większej liczby węzłów do grupy serwerów.

## <a name="coordinator-and-workers"></a>Koordynator i procesy robocze

Każda grupa serwerów ma węzeł koordynatora i wielu procesów roboczych. Aplikacje wysyłają swoje zapytania do węzła koordynatora, który przekazuje go do odpowiednich pracowników i gromadzi wyniki. Aplikacje nie mogą łączyć się bezpośrednio z pracownikami.

Dla każdego zapytania koordynator kieruje go do jednego węzła procesu roboczego lub parallelizes go w kilka w zależności od tego, czy wymagane dane znajdują się na pojedynczym lub wielu węzłach. Koordynator decyduje o tym, co należy zrobić, aby poznać tabele metadanych. Te tabele śledzą nazwy DNS i kondycję węzłów procesu roboczego oraz dystrybucję danych między węzłami.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak węzły przechowują [rozproszone dane](concepts-hyperscale-distributed-data.md)
