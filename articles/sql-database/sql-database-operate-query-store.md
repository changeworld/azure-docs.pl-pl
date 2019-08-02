---
title: Magazyn zapytań operacyjnych w Azure SQL Database
description: Dowiedz się, jak obsługiwać magazyn zapytań w Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: b4f999818fe3b3517ee3fb48c22e616ee50f2d88
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567147"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Działanie magazynu zapytań w Azure SQL Database

Magazyn zapytań na platformie Azure to funkcja w pełni zarządzanej bazy danych, która ciągle zbiera i przedstawia szczegółowe informacje historyczne o wszystkich zapytaniach. Możesz zastanowić się nad magazynem zapytań podobnym do rejestratora danych lotów samolotu, który znacznie upraszcza Rozwiązywanie problemów z wydajnością zapytań zarówno w przypadku klientów w chmurze, jak i lokalnych. W tym artykule wyjaśniono określone aspekty magazynu zapytań operacyjnych na platformie Azure. Korzystając z tych wstępnie zebranych danych zapytań, można szybko diagnozować i rozwiązywać problemy z wydajnością, a tym samym poświęcać więcej czasu na ich działalność. 

Magazyn zapytań jest [dostępny globalnie](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) w Azure SQL Database od listopada 2015. Magazyn zapytań jest podstawą do analizy wydajności i funkcji dostrajania, takich jak [SQL Database Advisor i pulpit nawigacyjny wydajności](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). W momencie publikowania tego artykułu magazyn zapytań działa w ponad 200 000 bazach danych użytkowników na platformie Azure, zbierając informacje dotyczące zapytania przez kilka miesięcy bez przeszkód.

> [!IMPORTANT]
> Firma Microsoft jest w trakcie aktywowania magazynu zapytań dla wszystkich baz danych Azure SQL Database (istniejących i nowych). 

## <a name="optimal-query-store-configuration"></a>Optymalna Konfiguracja magazynu zapytań

Ta sekcja zawiera opis optymalnych ustawień domyślnych konfiguracji, które zostały zaprojektowane w celu zapewnienia niezawodnej obsługi magazynu zapytań i funkcji zależnych, takich jak [SQL Database Advisor i pulpit nawigacyjny wydajności](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Konfiguracja domyślna jest zoptymalizowana pod kątem ciągłej zbierania danych, czyli minimalnej czasu poświęcanego na Stany wyłączone/READ_ONLY.

| Konfigurowanie | Opis | Domyślny | Komentarz |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Określa limit przestrzeni danych, którą magazyn zapytań może wykonać w ramach bazy danych klienta |100 |Wymuszane dla nowych baz danych |
| INTERVAL_LENGTH_MINUTES |Definiuje rozmiar przedziału czasu, w którym zbierane statystyki środowiska uruchomieniowego dla planów zapytania są agregowane i utrwalane. Każdy aktywny plan zapytania ma co najwyżej jeden wiersz w danym okresie zdefiniowanym przy użyciu tej konfiguracji |60 |Wymuszane dla nowych baz danych |
| STALE_QUERY_THRESHOLD_DAYS |Zasady oczyszczania oparte na czasie, które sterują okresem przechowywania utrwalonych statystyk środowiska uruchomieniowego i nieaktywnych zapytań |30 |Wymuszone dla nowych baz danych i baz danych z poprzednią wartością domyślną (367) |
| SIZE_BASED_CLEANUP_MODE |Określa, czy automatyczne czyszczenie danych ma miejsce, gdy rozmiar danych magazynu zapytań zbliża się do limitu |AUTOMATYCZNIE |Wymuszane dla wszystkich baz danych |
| QUERY_CAPTURE_MODE |Określa, czy są śledzone wszystkie zapytania lub tylko podzbiór zapytań |AUTOMATYCZNIE |Wymuszane dla wszystkich baz danych |
| FLUSH_INTERVAL_SECONDS |Określa maksymalny okres, w którym przechwycone statystyki środowiska uruchomieniowego są przechowywane w pamięci, przed przeprowadzeniem opróżniania na dysk |900 |Wymuszane dla nowych baz danych |
|  | | | |

> [!IMPORTANT]
> Te wartości domyślne są automatycznie stosowane w końcowym etapie aktywacji magazynu zapytań we wszystkich bazach danych Azure SQL (patrz Poprzednia ważna Uwaga). Po tym znaczeniu Azure SQL Database nie zmienią wartości konfiguracyjnych ustawionych przez klientów, o ile nie wpłyną one negatywnie na podstawowe obciążenie lub niezawodne operacje magazynu zapytań.

Jeśli chcesz pozostać w ustawieniach niestandardowych, użyj [opcji ALTER DATABASE with Query Store](https://msdn.microsoft.com/library/bb522682.aspx) , aby przywrócić konfigurację do poprzedniego stanu. Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi [magazynu zapytań,](https://msdn.microsoft.com/library/mt604821.aspx) aby dowiedzieć się, jak najlepiej wybrać optymalne parametry konfiguracji.

## <a name="next-steps"></a>Następne kroki

[SQL Database szczegółowe informacje o wydajności](sql-database-performance.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zapoznaj się z następującymi artykułami:

- [Rejestrator danych lotu dla bazy danych](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Monitorowanie wydajności za pomocą magazynu zapytań](https://msdn.microsoft.com/library/dn817826.aspx)
- [Scenariusze użycia magazynu zapytań](https://msdn.microsoft.com/library/mt614796.aspx)
