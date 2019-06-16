---
title: Monitorowania i poprawiania wydajności — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Azure SQL Database oferuje narzędzi do ułatwiający zidentyfikowanie obszarów, które może poprawić wydajność zapytań bieżącej oceny wydajności.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: adbccd5f9cfd5ddd1912e304f800f3ebe04912c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584832"
---
# <a name="monitor-and-improve-performance"></a>Monitorowania i poprawiania wydajności

Usługa Azure SQL Database znajduje potencjalne problemy w bazie danych i zaleca akcje, które może poprawić wydajność przetwarzania obciążenia, zapewniając inteligentne akcje dostrajania i zalecenia.

Aby zapoznać się z wydajność bazy danych, należy użyć **wydajności** kafelków na stronie Przegląd lub przejdź do "Pomoc techniczna i rozwiązywanie problemów" sekcji:

   ![Wyświetl wydajność](./media/sql-database-performance/entries.png)

W "Pomoc techniczna i rozwiązywanie problemów" sekcji, można użyć następujących stron:


1. [Przegląd wydajności](#performance-overview) monitorowanie wydajności bazy danych. 
2. [Zalecenia dotyczące wydajności](#performance-recommendations) można znaleźć zaleceń dotyczących wydajności, które może poprawić wydajność przetwarzania obciążenia.
3. [Szczegółowe informacje o wydajności zapytań](#query-performance-insight) odnaleźć najważniejsze zapytania korzystające z zasobów.
4. [Automatyczne dostrajanie](#automatic-tuning) aby umożliwić usłudze Azure SQL Database automatycznie Zoptymalizuj bazę danych.

## <a name="performance-overview"></a>Przegląd wydajności

Ten widok zawiera podsumowanie informacji o wydajności bazy danych i pomaga dostosowywania wydajności i rozwiązywania problemów. 

![Wydajność](./media/sql-database-performance/performance.png)

* **Zalecenia** Kafelek zawiera podział zaleceń dotyczących bazy danych dostosowywania (trzy pierwsze zalecenia są wyświetlane czy więcej). Kliknięcie tego kafelka spowoduje przejście do  **[zalecenia dotyczące wydajności](#performance-recommendations)** . 
* **Działanie dostrajania** Kafelek zawiera podsumowanie informacji o bieżące i ukończone, dostrajanie akcje w przypadku bazy danych, zapewniając szybki wgląd w historię działanie dostrajania. Kliknięcie tego kafelka spowoduje przejście do pełnego widoku Historia dostrajania bazy danych.
* **Automatycznego dostrajania** Kafelek pokazuje [automatycznego dostrajania konfiguracji](sql-database-automatic-tuning-enable.md) bazy danych (Dostosowywanie opcji, które są automatycznie stosowane do bazy danych). Kliknięcie tego kafelka spowoduje otwarcie okna dialogowego konfiguracji usługi automation.
* **Zapytań bazy danych** Kafelek pokazuje, podsumowania wydajność zapytań dla bazy danych (Ogólne jednostek DTU użycia i od góry zasobu zapytań zużywających najwięcej zasobów). Kliknięcie tego kafelka spowoduje przejście do  **[Query Performance Insight](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności

Ta strona zawiera inteligentne [zalecenia dotyczące dostrajania](sql-database-advisor.md) , może poprawić wydajność bazy danych. Następujące rodzaje zalecenia są wyświetlane na tej stronie:

* Zalecenia dotyczące indeksów, które można utworzyć lub usunąć.
* Zalecenia, gdy problemy ze schematem są identyfikowane w bazie danych.
* Zalecenia dotyczące zapytania mogą korzystać z sparametryzowanych zapytań.

![Wydajność](./media/sql-database-performance/recommendations.png)

Można również znaleźć pełną historię dostrajania akcje, które zostały zastosowane w przeszłości.

Dowiedz się, jak znaleźć Zastosuj zalecenia dotyczące wydajności w [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md) artykułu.

## <a name="automatic-tuning"></a>Automatyczne dostrajanie

Bazy danych SQL platformy Azure może automatycznie dostrajania wydajności bazy danych, stosując [zalecenia dotyczące wydajności](sql-database-advisor.md). Aby dowiedzieć się więcej, przeczytaj [automatycznego dostrajania artykułu](sql-database-automatic-tuning.md). Aby ją włączyć, przeczytaj [jak włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań

[Szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) pozwala poświęcać mniej czasu na rozwiązywanie problemów z wydajnością bazy danych, zapewniając:

* Lepszy wgląd w swoje użycie zasobów (DTU) baz danych. 
* Najważniejsze procesora CPU, korzystanie z zapytań, które potencjalnie mogą być dostosowane w celu zwiększenia wydajności. 
* Przechodzenie do szczegółów do szczegółów zapytania. 

  ![pulpit nawigacyjny wydajności](./media/sql-database-query-performance/performance.png)

Więcej informacji o tej stronie można znaleźć w artykule  **[sposób używania Query Performance Insight](sql-database-query-performance.md)** .

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Usługa Azure wskazówki dotyczące wydajności bazy danych SQL Database dla pojedynczych baz danych](sql-database-performance-guidance.md)
* [Kiedy należy użyć puli elastycznej?](sql-database-elastic-pool-guidance.md)

