---
title: Rekomendacje synapse SQL
description: Dowiedz się więcej o zaleceniach Synapse SQL i sposobie ich generowania
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 17877a1ef5d949fbbee080b6157844ac5b516fe7
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633683"
---
# <a name="synapse-sql-recommendations"></a>Rekomendacje synapse SQL

W tym artykule opisano zalecenia języka SQL synapse obsługiwane za pośrednictwem usługi Azure Advisor.  

Sql Analytics zawiera zalecenia, aby upewnić się, że obciążenie magazynu danych jest konsekwentnie zoptymalizowany pod kątem wydajności. Zalecenia są ściśle zintegrowane z [usługą Azure Advisor,](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) aby zapewnić najlepsze rozwiązania bezpośrednio w [witrynie Azure portal.](https://aka.ms/Azureadvisor) Usługa SQL Analytics zbiera dane telemetryczne i powierzchnie zalecenia dotyczące aktywnego obciążenia w codziennym rytmie. Obsługiwane scenariusze rekomendacji przedstawiono poniżej wraz z instrukcjami stosowania zalecanych działań.

Możesz [sprawdzić swoje rekomendacje](https://aka.ms/Azureadvisor) już dziś! Obecnie ta funkcja dotyczy tylko magazynów danych Gen2.

## <a name="data-skew"></a>Pochylenie danych

Pochylenie danych może spowodować dodatkowe przenoszenie danych lub wąskie gardła zasobów podczas uruchamiania obciążenia. W poniższej dokumentacji opisano pokaż, aby zidentyfikować pochylenie danych i zapobiec ich wystąpieniu, wybierając optymalny klucz dystrybucji.

- [Identyfikowanie i usuwanie pochylenia](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Brak lub nieaktualne statystyki

Posiadanie nieoptymalnych statystyk może poważnie wpłynąć na wydajność kwerendy, ponieważ może to spowodować, że optymalizator zapytań SQL wygeneruje nieoptymalne plany zapytań. W poniższej dokumentacji opisano najlepsze rozwiązania dotyczące tworzenia i aktualizowania statystyk:

- [Tworzenie i aktualizowanie statystyk tabeli](sql-data-warehouse-tables-statistics.md)

Aby wyświetlić listę tabel, na które te zalecenia mają wpływ, uruchom następujący [skrypt T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor stale uruchamia ten sam skrypt T-SQL do generowania tych zaleceń.

## <a name="replicate-tables"></a>Replikowanie tabel

W przypadku zaleceń dotyczących tabel replikowanych program Advisor wykrywa kandydatów do składania tabel na podstawie następujących cech fizycznych:

- Rozmiar tabeli replikowanej
- Liczba kolumn
- Typ dystrybucji tabeli
- Liczba partycji

Doradca stale wykorzystuje heurystyki oparte na obciążeniu, takie jak częstotliwość dostępu do tabeli, wiersze zwracane średnio i progi dotyczące rozmiaru magazynu danych i działania, aby zapewnić generowanie zaleceń wysokiej jakości.

Poniżej opisano heurystykę opartą na obciążeniu, którą można znaleźć w witrynie Azure portal dla każdego zalecenia tabeli replikowanej:

- Skanowanie — średni procent wierszy zwróconych z tabeli dla każdego dostępu do tabeli w ciągu ostatnich siedmiu dni
- Częste odczytywanie, brak aktualizacji - wskazuje, że tabela nie została zaktualizowana w ciągu ostatnich siedmiu dni, podczas gdy pokazuje aktywność dostępu
- Współczynnik odczytu/aktualizacji - stosunek częstości uzyskiwania dostępu do tabeli w stosunku do czasu jej aktualizacji w ciągu ostatnich siedmiu dni
- Działanie — mierzy użycie na podstawie działania dostępu. W ten sposób porównano działanie dostępu do tabeli względem średniego działania dostępu do tabeli w magazynie danych w ciągu ostatnich siedmiu dni.

Obecnie Advisor będzie wyświetlać tylko co najwyżej czterech replikowanych kandydatów tabeli naraz z indeksami klastrowanego magazynu kolumn priorytetów najwyższej aktywności.

> [!IMPORTANT]
> Zalecenie zreplikowanej tabeli nie jest pełnym dowodem i nie uwzględnia operacji przenoszenia danych. Pracujemy nad dodaniem tego jako heurystyki, ale w międzyczasie należy zawsze sprawdzić poprawność obciążenia po zastosowaniu zalecenia. Skontaktuj sqldwadvisor@service.microsoft.com się, jeśli wykryjesz zalecenia tabeli replikowanej, która powoduje, że obciążenie do regresu. Aby dowiedzieć się więcej o tabelach replikowanych, zapoznaj się z następującą [dokumentacją](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).
