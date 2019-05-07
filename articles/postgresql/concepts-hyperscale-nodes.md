---
title: Węzły w usłudze Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)
description: Dwa typy węzłów w grupy serwerów.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077278"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Węzły w usłudze Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)

Hiperskali (Citus) (wersja zapoznawcza), typ hostingu umożliwia — Azure Database dla serwerów MySQL (nazywane węzłami) koordynują się ze sobą w architekturze "bez udostępniania". Węzły w grupie serwerów zbiorczo przechowywać więcej danych i używać więcej rdzeni procesora CPU nie byłoby możliwe na jednym serwerze. Architektura umożliwia również bazy danych skalować, dodając więcej węzłów do grupy serwerów.

## <a name="coordinator-and-workers"></a>Koordynator i procesów roboczych

Każda grupa serwera ma węzeł koordynatora i wielu procesów roboczych. Aplikacje wysłać ich zapytań do węzła koordynatora, który przekazuje je do odpowiednich pracowników i gromadzi ich wyniki. Aplikacje nie są mogli połączyć się bezpośrednio z procesów roboczych.

Dla każdego zapytania koordynator kieruje je do węzła pojedynczego procesu roboczego albo parallelizes go dla kilku w zależności od tego, czy wymagane dane są przechowywane w jednym węźle lub wielu. Koordynator decyduje, co należy zrobić, sprawdzając tabel metadanych. Te tabele śledzenia nazwy DNS i kondycję węzłów procesu roboczego oraz rozkład danych między węzłami.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak przechowywać węzłów [rozproszonych danych](concepts-hyperscale-distributed-data.md)
