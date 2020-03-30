---
title: Znaczenie obciążenia
description: Wskazówki dotyczące ustawiania ważności zapytań usługi SQL Analytics w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 3dde2ad4af17313bcfce28964f8be1e831317a5a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349957"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Znaczenie obciążenia usługi Azure Synapse Analytics

W tym artykule wyjaśniono, jak ważność obciążenia może wpływać na kolejność wykonywania żądań usługi SQL Analytics w usłudze Azure Synapse.

## <a name="importance"></a>Ważność

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Potrzeby biznesowe mogą wymagać, aby obciążenia związane z magazynowaniami danych były ważniejsze niż inne.  Należy wziąć pod uwagę scenariusz, w którym dane sprzedaży o znaczeniu krytycznym są ładowane przed zamknięciem okresu obrachunkowego.  Obciążenia danych dla innych źródeł, takich jak dane pogodowe, nie mają ścisłych łas. Ustawienie dużej wagi dla żądania, aby załadować dane sprzedaży i niskie znaczenie do żądania, aby załadować dane pogodowe zapewnia obciążenie danych sprzedaży uzyskuje pierwszy dostęp do zasobów i kończy się szybciej.

## <a name="importance-levels"></a>Poziomy ważności

Istnieje pięć poziomów ważności: niski, below_normal, normalny, above_normal i wysoki.  Żądania, które nie ustawiają znaczenia, są przypisywane domyślny poziom normalny. Żądania, które mają ten sam poziom ważności mają takie samo zachowanie planowania, które istnieje dzisiaj.

## <a name="importance-scenarios"></a>Scenariusze ważności

Poza podstawowym scenariuszem ważności opisanym powyżej z danymi sprzedaży i pogody, istnieją inne scenariusze, w których ważność obciążenia pomaga spełnić potrzeby przetwarzania danych i zapytań.

### <a name="locking"></a>Blokowanie

Dostęp do blokad dla aktywności odczytu i zapisu jest jednym z obszarów rywalizacji naturalnej. Działania, takie jak [przełączanie partycji](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) lub [RENAME OBJECT](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest) wymagają blokad z podwyższonym poziomem uprawnień.  Bez znaczności obciążenia usługa SQL Analytics w usłudze Azure Synapse optymalizuje przepływność. Optymalizacja pod kątem przepływności oznacza, że podczas uruchamiania i kolejkowania żądań mają takie same potrzeby blokowania i zasoby są dostępne, żądania w kolejce można pominąć żądania z wyższymi potrzebami blokowania, które pojawiły się w kolejce żądań wcześniej. Gdy ważność obciążenia jest stosowana do żądań o wyższych potrzebach blokowania. Żądanie o wyższym znaczeniu zostanie uruchomione przed żądaniem o niższym znaczeniu.

Rozważmy następujący przykład:

- Q1 aktywnie działa i wybiera dane z SalesFact.
- Q2 jest w kolejce czekając na Q1, aby zakończyć.  Został on złożony o 9 rano i próbuje podzielić się na nowe dane do SalesFact.
- Q3 jest przesyłany na 9:01am i chce wybrać dane z SalesFact.

Jeśli Q2 i Q3 mają takie samo znaczenie, a Q1 nadal jest wykonywany, Q3 rozpocznie wykonywanie. Q2 będzie nadal czekać na ekskluzywną blokadę na SalesFact.  Jeśli Q2 ma większe znaczenie niż Q3, Q3 będzie czekać do Q2 jest gotowy, zanim będzie można rozpocząć wykonywanie.

### <a name="non-uniform-requests"></a>Nieujemne wnioski

Innym scenariuszem, w którym ważność może pomóc w spełnieniu wymagań kwerendy jest, gdy żądania z różnych klas zasobów są przesyłane.  Jak już wcześniej wspomniano, w tym samym znaczeniu, SQL Analytics w usłudze Azure Synapse optymalizuje przepływność. Gdy żądania o mieszanym rozmiarze (takie jak smallrc lub mediumrc) są umieszczane w kolejce, usługa SQL Analytics wybierze najwcześniejsze przychodzące żądanie, które mieści się w dostępnych zasobach. Jeśli zastosowanie jest istotne dla obciążenia, żądanie najwyższego znaczenia jest zaplanowane w następnej kolejności.
  
Rozważmy następujący przykład na DW500c:

- Q1, Q2, Q3 i Q4 są uruchomione smallrc zapytań.
- Q5 jest przesyłany z klasy zasobów mediumrc na 9 rano.
- Q6 jest przesyłany z klasy zasobów smallrc na 9:01am.

Ponieważ Q5 jest mediumrc, wymaga dwóch gniazd współbieżności. Q5 musi czekać na dwa uruchomione kwerendy do ukończenia.  Jednak po zakończeniu jednego z uruchomionych kwerend (Q1-Q4), Q6 jest zaplanowane natychmiast, ponieważ istnieją zasoby do wykonania kwerendy.  Jeśli Q5 ma większe znaczenie niż Q6, Q6 czeka, aż Q5 jest uruchomiony, zanim będzie można rozpocząć wykonywanie.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat tworzenia klasyfikatora, zobacz [CREAT WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Aby uzyskać więcej informacji na temat klasyfikacji obciążeń, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).  
- Zobacz [klasyfikator tworzenia obciążenia](quickstart-create-a-workload-classifier-tsql.md) szybki start, aby dowiedzieć się, jak utworzyć klasyfikator obciążenia. 
- Zobacz artykuły infigurajnokonfigurowanie [ważność obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md) oraz sposób [zarządzania zarządzaniem obciążeniami i monitorowania.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- Zobacz [sys.dm_pdw_exec_requests,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) aby wyświetlić zapytania i przypisane znaczenie.
