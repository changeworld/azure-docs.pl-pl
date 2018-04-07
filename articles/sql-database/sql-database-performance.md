---
title: Monitorowanie i poprawianie wydajności — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Baza danych SQL Azure udostępnia wydajności narzędzia pomocne w identyfikacji obszarów, które może poprawić wydajność kwerend bieżącego.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c97aa3aba9b705eeb0e3f40f289af389e6f380b0
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-and-improve-performance"></a>Monitorowanie i poprawianie wydajności
Baza danych SQL Azure identyfikuje potencjalne problemy w bazie danych i zalecane akcje, które może poprawić wydajność obciążenia przez podanie inteligentnego akcje dostosowywania i zalecenia.

Aby przejrzeć wydajność bazy danych, należy użyć **wydajności** kafelka na stronie Przegląd lub przejdź do "Obsługa + Rozwiązywanie problemów" sekcji:

   ![Widok wydajności](./media/sql-database-performance/entries.png)

W "Obsługa + Rozwiązywanie problemów" sekcji, można użyć następujących stron:


1. [Omówienie wydajności](#performance-overview) monitorowanie wydajności bazy danych. 
2. [Zalecenia dotyczące wydajności](#performance-recommendations) można znaleźć zalecenia dotyczące wydajności, które może poprawić wydajność obciążenia.
3. [Szczegółowe informacje o wydajności zapytań](#query-performance-insight) odnaleźć zasobu najwyższego korzystanie z zapytania.
4. [Automatycznego dostrajania](#automatic-tuning) pozwala automatycznie zoptymalizować bazę danych i bazy danych SQL Azure.

## <a name="performance-overview"></a>Wydajności — omówienie
Ten widok zawiera podsumowanie wydajność bazy danych i pomaga wydajności dostrajanie i rozwiązywania problemów. 

![Wydajność](./media/sql-database-performance/performance.png)

* **Zalecenia** Kafelek zawiera podział dostrajanie zalecenia dotyczące bazy danych (trzy najważniejsze zalecenia są wyświetlane czy więcej). Kliknięcie tego kafelka przejście do  **[zaleceń](#performance-recommendations)**. 
* **Dostrajanie działania** kafelka zawiera podsumowanie bieżących i zakończonych, dostrajanie akcji dla bazy danych, umożliwiając zapewnia szybki wgląd w historię dostrajanie działania. Kliknięcie tego kafelka przejście do pełnego widoku historii dostrajania dla Twojej bazy danych.
* **Autostrojenie** kafelka pokazuje [automatycznego dostrajania konfiguracji](sql-database-automatic-tuning-enable.md) bazy danych (dostrajanie opcje, które są automatycznie stosowane do bazy danych). Kliknięcie tego kafelka otwiera okno dialogowe konfiguracji automatyzacji.
* **Zapytań bazy danych** kafelka zawiera zestawienie wydajności kwerendy dla bazy danych (ogólną jednostek dtu w warstwie użycia i od góry zasobów korzystających z kwerendy). Kliknięcie tego kafelka przejście do  **[szczegółowe informacje o wydajności zapytań](#query-performance-insight)**.

## <a name="performance-recommendations"></a>Zalecenie dotyczące wydajności
Ta strona zawiera inteligentnego [dostrajanie zalecenia](sql-database-advisor.md) które może poprawić wydajność bazy danych. Następujące typy zalecenia są wyświetlane na tej stronie:

* Zalecenia dotyczące indeksów, które można utworzyć lub usunąć.
* Zalecenia dotyczące problemów schematu są identyfikowane w bazie danych.
* Zalecenia dotyczące zapytań mogą korzystać z zapytania parametrycznego.

![Wydajność](./media/sql-database-performance/recommendations.png)

Można również znaleźć pełnej historii dostrajanie akcje, które zostały zastosowane w przeszłości.

Dowiedz się, jak znaleźć Zastosuj zalecenia dotyczące wydajności w [Znajdowanie i stosować zalecenia wydajności](sql-database-advisor-portal.md) artykułu.

## <a name="automatic-tuning"></a>Automatyczne dostrajanie
Bazy danych SQL Azure mogą automatycznie dostrajania wydajności bazy danych poprzez zastosowanie [zaleceń](sql-database-advisor.md). Aby dowiedzieć się więcej, przeczytaj [automatycznego dostrajania artykułu](sql-database-automatic-tuning.md). Aby go włączyć, przeczytaj [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań
[Szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) umożliwia poświęcają mniej czasu Rozwiązywanie problemów z wydajnością bazy danych przez zapewnienie:

* Bardziej szczegółowe informacje na temat użycia zasobów (bazy danych DTU) bazy danych. 
* Górny procesora CPU, korzystanie z zapytań, które potencjalnie można przedstawić zwiększonej wydajności. 
* Możliwość Przechodzenie do szczegółów zapytania. 

  ![pulpit nawigacyjny wydajności](./media/sql-database-query-performance/performance.png)

Znaleźć więcej informacji na temat tej strony w artykule  **[sposób użycia szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Azure wytyczne dotyczące wydajności bazy danych SQL dla pojedynczych baz danych](sql-database-performance-guidance.md)
* [Kiedy należy użyć puli elastycznej?](sql-database-elastic-pool-guidance.md)

