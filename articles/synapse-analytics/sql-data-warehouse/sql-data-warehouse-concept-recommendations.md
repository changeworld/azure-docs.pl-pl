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
ms.openlocfilehash: c4bbd98cc28b242be5310fab76521a80de8fcb7c
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584122"
---
# <a name="synapse-sql-recommendations"></a>Rekomendacje synapse SQL

W tym artykule opisano zalecenia języka SQL synapse obsługiwane za pośrednictwem usługi Azure Advisor.  

Synapse SQL zawiera zalecenia, aby upewnić się, że obciążenie magazynu danych jest konsekwentnie zoptymalizowane pod kątem wydajności. Zalecenia są ściśle zintegrowane z [usługą Azure Advisor,](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) aby zapewnić najlepsze rozwiązania bezpośrednio w [witrynie Azure portal.](https://aka.ms/Azureadvisor) Synapse SQL zbiera dane telemetryczne i powierzchnie zalecenia dla aktywnego obciążenia na dzienny rytm. Obsługiwane scenariusze rekomendacji przedstawiono poniżej wraz z instrukcjami stosowania zalecanych działań.

Możesz [sprawdzić swoje rekomendacje](https://aka.ms/Azureadvisor) już dziś! Obecnie ta funkcja dotyczy tylko magazynów danych Gen2. 

## <a name="data-skew"></a>Pochylenie danych

Pochylenie danych może spowodować dodatkowe przenoszenie danych lub wąskie gardła zasobów podczas uruchamiania obciążenia. W poniższej dokumentacji opisano pokaż, aby zidentyfikować pochylenie danych i zapobiec ich wystąpieniu, wybierając optymalny klucz dystrybucji.

- [Identyfikowanie i usuwanie pochylenia](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Brak lub nieaktualne statystyki

Posiadanie nieoptymalnych statystyk może poważnie wpłynąć na wydajność kwerendy, ponieważ może to spowodować, że optymalizator zapytań SQL wygeneruje nieoptymalne plany zapytań. W poniższej dokumentacji opisano najlepsze rozwiązania dotyczące tworzenia i aktualizowania statystyk:

- [Tworzenie i aktualizowanie statystyk tabeli](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

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
> Zalecenie zreplikowanej tabeli nie jest pełnym dowodem i nie uwzględnia operacji przenoszenia danych. Pracujemy nad dodaniem tego jako heurystyki, ale w międzyczasie należy zawsze sprawdzić poprawność obciążenia po zastosowaniu zalecenia. Skontaktuj sqldwadvisor@service.microsoft.com się, jeśli wykryjesz zalecenia tabeli replikowanej, która powoduje, że obciążenie do regresu. Aby dowiedzieć się więcej o tabelach replikowanych, zapoznaj się z następującą [dokumentacją](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
