---
title: Węzły w Azure Database for PostgreSQL — funkcja do skalowania (Citus)
description: Zapoznaj się z dwoma typami węzłów, koordynatorem i pracownikami w grupie serwerów w Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 5348c8bbe432cd024a8da93ca866ae4458f956e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468093"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Węzły w Azure Database for PostgreSQL — funkcja do skalowania (Citus)

Typ hostujący (Citus) Azure Database for PostgreSQL umożliwia współrzędnie serwerów (nazywanych węzłami) w architekturze "udostępnione Nothing". Węzły w grupie serwerów wspólnie przechowują więcej danych i wykorzystują więcej rdzeni procesora CPU niż jest to możliwe na jednym serwerze. Architektura umożliwia również skalowanie bazy danych przez dodanie większej liczby węzłów do grupy serwerów.

## <a name="coordinator-and-workers"></a>Koordynator i procesy robocze

Każda grupa serwerów ma węzeł koordynatora i wielu procesów roboczych. Aplikacje wysyłają swoje zapytania do węzła koordynatora, który przekazuje go do odpowiednich pracowników i gromadzi wyniki. Aplikacje nie mogą łączyć się bezpośrednio z pracownikami.

Dla każdego zapytania koordynator kieruje go do jednego węzła procesu roboczego lub parallelizes go w kilka w zależności od tego, czy wymagane dane znajdują się na pojedynczym lub wielu węzłach. Koordynator decyduje o tym, co należy zrobić, aby poznać tabele metadanych. Te tabele śledzą nazwy DNS i kondycję węzłów procesu roboczego oraz dystrybucję danych między węzłami.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak węzły przechowują [rozproszone dane](concepts-hyperscale-distributed-data.md)
