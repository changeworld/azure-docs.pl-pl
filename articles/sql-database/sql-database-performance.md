---
title: Monitorowanie i Poprawianie wydajności
description: Azure SQL Database zawiera narzędzia do oceny wydajności, które ułatwiają zidentyfikowanie obszarów, które mogą poprawić bieżącą wydajność zapytań.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/14/2019
ms.openlocfilehash: c710e2b7c4376fc1725032c86cd02d9a6c8cafac
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090350"
---
# <a name="monitor-and-improve-performance"></a>Monitorowanie i Poprawianie wydajności

Azure SQL Database identyfikuje potencjalne problemy w bazie danych i zaleca akcje, które mogą poprawić wydajność obciążeń, zapewniając inteligentne akcje dostrajania i zalecenia.

## <a name="performance-tuning-options"></a>Opcje dostrajania wydajności

Opcje dostrajania wydajności dostępne Azure SQL Database są dostępne w menu nawigacji bazy danych w obszarze "Inteligentna wydajność":

| Opcja dostrajania wydajności | Obsługa pojedynczej bazy danych i bazy danych w puli | Obsługa bazy danych wystąpień |
| :----------------------------- | ----- | ----- |
| **[Przegląd wydajności](#performance-overview)** — monitorowanie wszystkich działań związanych z wydajnością bazy danych. | Tak | Nie | 
| **[Zalecenia dotyczące wydajności](#performance-recommendations)** — pokazuje zalecenia dotyczące wydajności, które mogą poprawić wydajność obciążeń. | Tak | Nie | 
| **[Szczegółowe informacje o wydajności zapytań](#query-performance-insight)** — pokazuje wydajność najczęściej zużywanych zapytań w bazie danych. | Tak | Nie | 
| **[Dostrajanie automatyczne](#automatic-tuning)** — Użyj Azure SQL Database, aby automatycznie zoptymalizować wydajność bazy danych. | Tak | Nie | 

## <a name="performance-overview"></a>Przegląd wydajności

Ten widok zawiera podsumowanie wydajności bazy danych oraz ułatwia dostrajanie wydajności i rozwiązywanie problemów. 

![Wydajność](./media/sql-database-performance/performance.png)

* Kafelek **rekomendacje** zawiera podział zaleceń dotyczących dostrajania dla bazy danych (w przypadku większej liczby najważniejszych zaleceń są wyświetlane trzy zalecenia). Kliknięcie tego kafelka pozwala uzyskać **[zalecenia dotyczące wydajności](#performance-recommendations)** . 
* Kafelek **działania dostrajania** zawiera podsumowanie bieżących i ukończonych akcji dostrajania dla bazy danych, dzięki czemu można szybko wyświetlić historię działania dostrajania. Kliknięcie tego kafelka spowoduje przejście do widoku historii pełnego dostrajania dla bazy danych.
* Kafelek **autodostrajania** pokazuje [konfigurację](sql-database-automatic-tuning-enable.md) autodostrajania dla bazy danych (opcje dostrajania, które są automatycznie stosowane do bazy danych). Kliknięcie tego kafelka spowoduje otwarcie okna dialogowego konfiguracji automatyzacji.
* Kafelek **zapytania bazy danych** pokazuje podsumowanie wydajności zapytań dla bazy danych (ogólne użycie jednostek DTU i najpopularniejsze zapytania zużywające zasoby). Kliknięcie tego kafelka spowoduje przejście do **[szczegółowe informacje o wydajności zapytań](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności

Ta strona zawiera zalecenia inteligentnego [dostrajania](sql-database-advisor.md) , które mogą poprawić wydajność bazy danych. Na tej stronie przedstawiono następujące typy zaleceń:

* Zalecenia dotyczące tego, które indeksy należy utworzyć lub usunąć.
* Zalecenia dotyczące identyfikowania problemów ze schematami w bazie danych programu.
* Zalecenia, gdy zapytania mogą korzystać z zapytań parametrycznych.

![Wydajność](./media/sql-database-performance/recommendations.png)

Możesz również znaleźć pełną historię działań dostrajania, które zostały zastosowane w przeszłości.

Dowiedz się, jak znaleźć zalecenia dotyczące wydajności w artykule [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md) .

## <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań

[Szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) pozwala poświęcać mniej czasu na rozwiązywanie problemów z wydajnością bazy danych, zapewniając:

* Dokładniejsze informacje o zużyciu zasobów baz danych (DTU). 
* Najpopularniejsze zapytania zużywające procesor CPU, które mogą być dostrojone w celu zwiększenia wydajności. 
* Możliwość przechodzenia do szczegółów zapytania. 

  ![Pulpit nawigacyjny wydajności](./media/sql-database-query-performance/performance.png)

Więcej informacji na temat tej strony znajdziesz w artykule **[jak korzystać z szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md)** .

## <a name="automatic-tuning"></a>Automatyczne dostrajanie

Bazy danych Azure SQL Database mogą automatycznie dostosowywać wydajność bazy danych, stosując [zalecenia dotyczące wydajności](sql-database-advisor.md). Aby dowiedzieć się więcej, przeczytaj artykuł dotyczący [dostrajania automatycznego](sql-database-automatic-tuning.md). Aby ją włączyć, przeczytaj artykuł [jak włączyć dostrajanie automatyczne](sql-database-automatic-tuning-enable.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Azure SQL Database wskazówki dotyczące wydajności dla pojedynczych baz danych](sql-database-performance-guidance.md)
* [Kiedy należy używać puli elastycznej?](sql-database-elastic-pool-guidance.md)
