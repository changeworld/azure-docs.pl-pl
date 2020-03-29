---
title: Węzły — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Dowiedz się więcej o dwóch typach węzłów, koordynatora i pracowników, w grupie serwerów w usłudze Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974006"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Węzły w usłudze Azure Database dla postgreSQL — hiperskala (Citus)

Typ hostingu w skali hiperskali (Citus) umożliwia usłudze Azure Database dla serwerów PostgreSQL (nazywanych węzłami) koordynowanie ze sobą w architekturze "shared nothing". Węzły w grupie serwerów łącznie przechowują więcej danych i używają większej liczby rdzeni procesora NIŻ byłoby to możliwe na jednym serwerze. Architektura umożliwia również bazy danych do skalowania przez dodanie większej liczby węzłów do grupy serwerów.

## <a name="coordinator-and-workers"></a>Koordynator i pracownicy

Każda grupa serwerów ma węzeł koordynatora i wielu pracowników. Aplikacje wysyłają swoje zapytania do węzła koordynatora, który przekazuje je odpowiednim pracownikom i gromadzi ich wyniki. Aplikacje nie mogą łączyć się bezpośrednio z pracownikami.

Dla każdej kwerendy koordynator kieruje go do jednego węzła procesu roboczego lub równoległo go w kilku w zależności od tego, czy wymagane dane są ważne w jednym węźle, czy w wielu. Koordynator decyduje, co zrobić, konsultując tabele metadanych. Tabele te śledzą nazwy DNS i kondycję węzłów procesu roboczego oraz rozkład danych między węzłami.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak węzły [przechowują dane rozproszone](concepts-hyperscale-distributed-data.md)
