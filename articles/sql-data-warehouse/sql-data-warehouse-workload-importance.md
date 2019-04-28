---
title: Znaczenie obciążenie | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące konfigurowania znaczenie dla zapytań w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 12e7d9bc22eff14bbf302aed50080412d04a40d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474706"
---
# <a name="sql-data-warehouse-workload-importance-preview"></a>Znaczenie obciążenia usługa SQL Data Warehouse (wersja zapoznawcza)

W tym artykule wyjaśniono, jak znaczenie obciążenia mogą mieć wpływ na kolejność wykonywania żądań usługa SQL Data Warehouse.

> [!Note]
> Klasyfikacja obciążenia jest dostępna w wersji zapoznawczej na Gen2 magazynu danych SQL. Podgląd obciążenia zarządzania klasyfikacji i znaczenie jest dla kompilacji z datą 9 kwietnia 2019 lub nowszej wersji.  Użytkownikom należy unikać kompilacje starszych niż ta data do testowania zarządzania obciążeniem.  Aby ustalić, czy zarządzanie obciążeniami zdolne do kompilacji, uruchom wybierz @@version po podłączeniu do wystąpienia usługi SQL Data Warehouse.

## <a name="importance"></a>Ważność

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Potrzeby biznesowe mogą wymagać obciążeń się ważniejsze niż inne magazynowania danych.  Rozważmy scenariusz, załadunku danych sprzedaży o krytycznym znaczeniu przed obrachunkowego Zamykanie okresu.  Dane są ładowane w przypadku innych źródeł, takich jak dane o pogodzie nie ma rygorystyczne umowy SLA.   Ustawienie o wysokiej ważności dla żądania załadować dane dotyczące sprzedaży i niskiej ważności na żądanie, aby załadować czy danych zapewnia ładowania danych sprzedaży pobiera pierwszego dostępu do zasobów i zakończeniu szybszy.

## <a name="importance-levels"></a>Poziomy ważności

Istnieje pięć poziomów ważności: Niski, below_normal, normalny, above_normal i wysoki.  Żądania, które nie jest ustawiana znaczenie są przypisywane domyślny poziom normalny.  Żądania, które mają ten sam poziom ważności mają takie samo zachowanie planowania, czy istnieje już dziś.

## <a name="importance-scenarios"></a>Scenariusze znaczenie

Poza opisanych powyżej sprzedaży i danych o pogodzie w scenariuszu podstawowe znaczenie istnieją inne scenariusze, w której obciążenie znaczenie pomaga zaspokoić potrzeby dotyczące przetwarzania danych i zapytań na potrzeby.

### <a name="locking"></a>Blokowanie

Dostęp do blokady do odczytu i zapisu działanie jest jeden obszar naturalnych rywalizacji o zasoby.  Takie działania, jak [przełączanie partycji](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) lub [Zmień nazwę obiektu](/sql/t-sql/statements/rename-transact-sql) wymagają blokad z podwyższonym poziomem uprawnień.  Bez znaczenia obciążenia usługa SQL Data Warehouse jest zoptymalizowana przepływności.  Optymalizowanie przepływności oznacza, że podczas uruchamiania i żądań w kolejce mają ten sam potrzeby blokowania i są dostępne zasoby, żądań w kolejce można pominąć żądań z wyższym wymaga blokowania, które wcześniej dostarczona w kolejce żądań.  Po zastosowaniu znaczenie obciążenia żądań z wyższym blokowania potrzebuje. Wniosek o ważności wyższym będą uruchamiane przed żądaniem niższe ważności.

Rozważmy następujący przykład:

Q1 jest aktywnie uruchomiona i wybierając dane z SalesFact.
Q2 znajduje się w kolejce oczekiwania na 1. zakończyć.  On przesłano o 9: 00 i podejmuje próbę partycji przełącznika nowe dane do SalesFact.
K3 miało miejsce o 9:01:00 i chce wybrać dane z SalesFact.

Jeśli K2 i K3 mają takie samo znaczenie i Q1 jest nadal wykonywane, K3 rozpoczyna wykonywanie. Q2 będą w dalszym ciągu poczekaj na SalesFact blokady na wyłączność.  Jeśli Q2 ważności wyższym niż K3, K3 czeka na zakończenie Q2 zostało zakończone przed rozpoczęciem jego wykonywania.

### <a name="non-uniform-requests"></a>Żądania obsługi niejednolitego

Inny scenariusz, w którym znaczenie może pomóc odpowiada potrzebom związanym z zapytań jest po przesłaniu żądania z różnych zasobów klasy.  Jak wcześniej wspomniano, w obszarze tak samo ważne, usługa SQL Data Warehouse jest zoptymalizowana przepływności.  Gdy są umieszczane w kolejce żądań mieszanej wielkości (na przykład smallrc lub mediumrc), SQL Data Warehouse wybierze nadchodzących tak szybko, jak żądania, które mieści się w dostępne zasoby.  Jeśli zastosowano znaczenie obciążenia, najwyższy żądania znaczenie zaplanowano dalej.
  
Rozważmy następujący przykład na DW500c:

K1, K2, K3 i kwartale 4 działają smallrc zapytania.
Q5 jest przesyłany z klasą zasobów mediumrc o 9: 00.
Q6 jest przesyłany z klasą zasobów smallrc o 9:01:00.

Ponieważ Q5 mediumrc, wymaga dwóch gniazd współbieżności.  Q5 musi czekać na dwa uruchomionych zapytań, aby zakończyć.  Jednak po zakończeniu jednego z uruchomionych zapytań (kwartał 1-4), Q6 zaplanowano od razu, ponieważ istnieją zasoby, aby wykonać zapytanie.  Jeśli Q5 ważności wyższym niż Q6, Q6 czeka, aż Q5 jest uruchomiony, przed rozpoczęciem wykonywania.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat klasyfikacji obciążenia usługa SQL Data Warehouse, zobacz [klasyfikacji obciążenia magazynu danych SQL](sql-data-warehouse-workload-classification.md) i [tworzenie klasyfikatora obciążenia](quickstart-create-a-workload-classifier-tsql.md). Zobacz [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) służy do wyświetlania kwerend i ważność przypisana.
