---
title: Ważność obciążeń
description: Wskazówki dotyczące ustawiania ważności dla zapytań w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 28d239d47b46a5aafdf65c72ef826a0efb79f52b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974637"
---
# <a name="azure-sql-data-warehouse-workload-importance"></a>Ważność obciążenia Azure SQL Data Warehouse

W tym artykule wyjaśniono, jak ważność obciążenia może mieć wpływ na kolejność wykonywania żądań SQL Data Warehouse.

## <a name="importance"></a>Znaczenie

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Potrzeby biznesowe mogą wymagać, aby obciążenia związane z magazynem danych były ważniejsze niż inne.  Rozważmy scenariusz, w którym dane sprzedaży o kluczowym znaczeniu są ładowane przed zamknięciem okresu obrachunkowego.  Ładowanie danych dla innych źródeł, takich jak dane pogodowe, nie mają ścisłych umowy SLA. Ustawienie wysokiego znaczenia dla żądania załadowania danych sprzedaży i niskiej ważności do żądania załadowania, czy dane zapewniają, że ładowanie danych sprzedaży uzyskuje pierwszy dostęp do zasobów i kończy się szybciej.

## <a name="importance-levels"></a>Poziomy ważności

Istnieje pięć poziomów ważności: niski, below_normal, normalny, above_normal i wysoki.  Żądania, które nie ustawiają znaczenia, są przypisane do domyślnego poziomu normalnego. Żądania o takim samym poziomie ważności mają takie samo zachowanie dotyczące planowania, które już istnieje.

## <a name="importance-scenarios"></a>Scenariusze ważności

Poza podstawowym scenariuszem ważności opisanym powyżej z danymi dotyczącymi sprzedaży i pogody istnieją inne scenariusze, w których ważność obciążenia pomaga spełnić wymagania związane z przetwarzaniem danych i wykonywaniem zapytań.

### <a name="locking"></a>Blokowanie

Dostęp do blokad dla działania odczytu i zapisu jest jednym obszarem naturalnej rywalizacji. Działania takie jak [przełączanie partycji](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) lub [zmiana nazwy obiektu](/sql/t-sql/statements/rename-transact-sql) wymagają podniesionych blokad.  Bez znaczenia obciążenia SQL Data Warehouse optymalizuje pod kątem przepływności.  Optymalizacja pod kątem przepływności oznacza, że gdy uruchomione i kolejkowane żądania mają takie same potrzeby blokowania i dostępne są zasoby, żądania kolejkowane mogą pomijać żądania o wyższych wymaganiach blokowania, które dotarły wcześniej do kolejki żądań.  Po zastosowaniu ważności obciążenia do żądań o wyższych wymaganiach dotyczących blokowania, żądania o wyższym znaczeniu zostaną uruchomione przed żądaniem o niższej ważności.

Rozważmy następujący przykład:

Aktywnie działa i wybierasz dane z SalesFact.
Q2 oczekuje na ukończenie kolejki Q1.  Został przesłany w 9:00 i trwa próba przełączenia na partycje nowych danych do SalesFact.
Q3 jest przesyłany o 01am i chce wybierać dane z SalesFact.

Jeśli Q2 i Q3 mają takie same znaczenie i jest nadal wykonywane, Q3 rozpocznie wykonywanie. Q2 będzie nadal czekać na wyłączne blokady SalesFact.  Jeśli Q2 ma wyższy priorytet niż Q3, Q3 czeka na zakończenie kwartału.

### <a name="non-uniform-requests"></a>Niejednorodne żądania

W innym scenariuszu, w którym istotność może pomóc spełnić wymagania dotyczące zapytań, są przesyłane żądania z różnymi klasami zasobów.  Jak wcześniej wspomniano, w tym samym znaczeniu, SQL Data Warehouse optymalizuje pod kątem przepływności. Gdy żądania o rozmiarze mieszanym (takie jak smallrc lub mediumrc) są umieszczane w kolejce, SQL Data Warehouse wybierze najwcześniejsze żądanie, które mieści się w dostępnych zasobach. Jeśli jest stosowana ważność obciążenia, żądanie o najwyższym znaczeniu zostanie zaplanowane dalej.
  
Rozważmy następujący przykład na DW500c:

W kwartale Q1, Q2, Q3 i kwartale są uruchomione zapytania smallrc.
Q5 jest przesyłana z klasą zasobów mediumrc w 9:00.
Q6 jest przesyłana z klasą zasobów smallrc o godzinie 9:01am.

Ponieważ Q5 jest mediumrc, wymaga dwóch miejsc współbieżności. Q5 musi czekać na zakończenie dwóch z uruchomionych zapytań.  Jednak po zakończeniu jednego z uruchomionych zapytań (Q1-kwartale) Q6 jest zaplanowana natychmiast, ponieważ zasoby istnieją do wykonania zapytania.  Jeśli Q5 ma wyższy priorytet niż Q6, Q6 czeka, aż do momentu rozpoczęcia wykonywania przez Q5.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat tworzenia klasyfikatora, zobacz [Tworzenie KLASYFIKATORA obciążenia (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Aby uzyskać więcej informacji na temat klasyfikacji obciążeń SQL Data Warehouse, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).  
- Zobacz Przewodnik Szybki Start dotyczący [tworzenia obciążenia](quickstart-create-a-workload-classifier-tsql.md) , aby utworzyć klasyfikator obciążeń.
- Zapoznaj się z artykułami z artykułu jak, aby [skonfigurować ważność obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md) oraz jak [zarządzać i monitorować zarządzanie obciążeniami](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Zobacz sekcję [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) , aby wyświetlić zapytania i przypisane znaczenie.
