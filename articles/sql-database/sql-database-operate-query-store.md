---
title: Operacyjne Store zapytania w usłudze Azure SQL Database
description: Dowiedz się, jak działają Store zapytania w usłudze Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 3ceb8569d952f2947870ce7314f869623b2d87f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584747"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Operacyjnego Store zapytania w usłudze Azure SQL Database

Query Store na platformie Azure jest funkcją w pełni zarządzana baza danych, która stale zbiera i przedstawia szczegółowe informacje historyczne na temat wszystkich zapytań. Należy rozważać Query Store, jak Rejestrator danych samolotowy, znacznie upraszcza zapytania Rozwiązywanie problemów z wydajnością zarówno dla aplikacji w chmurze i lokalnych klientów. W tym artykule opisano specyficzne aspekty operacyjne Query Store na platformie Azure. Za pomocą tego wcześniej zebranych zapytania o dane, możesz szybkie diagnozowanie i rozwiązywanie problemów z wydajnością i związku z tym poświęcić więcej czasu, skupiając się na działalność biznesową. 

Query Store zostało [dostępnie](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) w usłudze Azure SQL Database od listopada 2015 r. Query Store jest podstawą dla analizy wydajności oraz funkcji dopasowywania, takich jak [SQL Database Advisor i pulpit nawigacyjny wydajności](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). W momencie publikowania tego artykułu Query Store działa w ponad 200 000 baz danych użytkownika na platformie Azure zbieranie informacji związane z kwerendami, kilka miesięcy, bez przeszkód.

> [!IMPORTANT]
> Microsoft, Trwa aktywowanie Store zapytania dla wszystkich baz danych Azure SQL (istniejących i nowych). 

## <a name="optimal-query-store-configuration"></a>Konfiguracja optymalne Query Store

W tej sekcji opisano ustawienia domyślne optymalną konfigurację, które są przeznaczone do zapewnienia niezawodne działanie Query Store i funkcje zależne, takie jak [SQL Database Advisor i pulpit nawigacyjny wydajności](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Konfiguracja domyślna jest zoptymalizowany pod kątem zbierania danych ciągłe, który jest minimalny czas w stanie OFF/READ_ONLY.

| Konfigurowanie | Opis | Domyślne | Komentarz |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Określa limit miejsca na dane, mogący Query Store wewnątrz bazy danych klienta |100 |Wymuszane dla nowych baz danych |
| INTERVAL_LENGTH_MINUTES |Określa rozmiar przedziału czasu, w którym zagregowane i utrwalane statystyk zebranych środowiska uruchomieniowego dla planów zapytań. Każdy plan zapytanie usługi active ma co najwyżej jeden wiersz w okresie razem zdefiniowane za pomocą tej konfiguracji |60 |Wymuszane dla nowych baz danych |
| STALE_QUERY_THRESHOLD_DAYS |Zasady oparte na czasie oczyszczania, które określa okres przechowywania statystyki środowiska uruchomieniowego utrwalonego i zapytania nieaktywne |30 |Wymuszane dla nowych baz danych i bazami danych za pomocą wcześniejszy domyślny (367) |
| SIZE_BASED_CLEANUP_MODE |Określa, czy automatycznego oczyszczania ma miejsce, gdy zbliża się do limitu rozmiaru danych Query Store |AUTOMATYCZNIE |Wymuszone dla wszystkich baz danych |
| QUERY_CAPTURE_MODE |Określa, czy wszystkie zapytania lub tylko podzbiór zapytania są śledzone |AUTOMATYCZNIE |Wymuszone dla wszystkich baz danych |
| FLUSH_INTERVAL_SECONDS |Określa, że maksymalny okres, przez który przechwycone środowiska uruchomieniowego, które statystyki są przechowywane w pamięci, przed opróżnianie na dysku |900 |Wymuszane dla nowych baz danych |
|  | | | |

> [!IMPORTANT]
> Te ustawienia domyślne są automatycznie stosowane w ostatnim etapie aktywacji Query Store we wszystkich bazach danych Azure SQL (zobacz ważna uwaga poprzedzających). Po tym światła się bazy danych SQL Azure nie będzie zmieniać wartości konfiguracji Ustaw przez klientów, o ile nie mogą mieć negatywny wpływ na podstawowe obciążenie lub niezawodne operacje Store zapytania.

Jeśli chcesz pozostać przy użyciu ustawień użytkownika, użyj [ALTER DATABASE przy użyciu opcji Query Store](https://msdn.microsoft.com/library/bb522682.aspx) można przywrócić konfigurację do poprzedniego stanu. Zapoznaj się z [najlepsze rozwiązania dzięki Store zapytania](https://msdn.microsoft.com/library/mt604821.aspx) Aby dowiedzieć się, jak top wybrać optymalną konfigurację parametrów.

## <a name="next-steps"></a>Kolejne kroki

[Szczegółowe informacje o wydajności bazy danych SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, odwiedź następujące artykuły:

- [Rejestrator danych dla bazy danych](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Monitorowanie wydajności za pomocą Store zapytania](https://msdn.microsoft.com/library/dn817826.aspx)
- [Scenariusze użycia magazynu zapytań](https://msdn.microsoft.com/library/mt614796.aspx)
