---
title: Działania magazynu zapytań w bazie danych Azure SQL
description: Jak używać magazynu zapytań w bazie danych SQL Azure
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: bonova
ms.openlocfilehash: 4722399525b376e232f2bc7802a570836da79e29
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Działania w magazynie zapytań w bazie danych Azure SQL
Magazyn zapytań w usłudze Azure to funkcja bazy danych w pełni zarządzana, nieustannie zbiera i wyświetla szczegółowe informacje historyczne na temat wszystkich zapytań. Temat funkcji magazyn zapytań można traktować jako podobne do Rejestrator danych samolotowy, który znacznie upraszcza wydajność zapytań, zarówno dla chmury Rozwiązywanie problemów z klientami lokalnymi. W tym artykule opisano aspekty określonego systemu operacyjnego magazynu zapytań na platformie Azure. Przy użyciu to wstępnie zebrane dane, można szybkie diagnozowanie i rozwiązywanie problemów z wydajnością i w związku z tym poświęcić więcej czasu na koncentrujących się na swojej działalności. 

Magazyn zapytań był [globalnie dostępną](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) w bazie danych SQL Azure od listopada 2015 r. Magazyn zapytań jest podstawą do analizy wydajności i dostrajania funkcji, takich jak [doradcy bazy danych SQL i pulpit nawigacyjny wydajności](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). W momencie publikowania tego artykułu magazyn zapytań jest uruchomiony w więcej niż 200 000 baz danych użytkowników na platformie Azure, zbieranie informacji związanych z zapytania dla kilku miesięcy, bez przeszkód.

> [!IMPORTANT]
> Microsoft Trwa aktywowanie magazyn zapytań dla wszystkich baz danych Azure SQL (istniejących i nowych). 
> 
> 

## <a name="optimal-query-store-configuration"></a>Konfiguracja optymalna magazyn zapytań
W tej sekcji opisano ustawienia domyślne optymalną konfigurację, które są przeznaczone do zapewnienia niezawodnego działania magazynu zapytań i funkcje zależne, takie jak [doradcy bazy danych SQL i pulpit nawigacyjny wydajności](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Domyślna konfiguracja jest zoptymalizowany do zbierania danych ciągłego, to minimalny czas przeznaczony na OFF/READ_ONLY stanów.

| Konfigurowanie | Opis | Domyślne | Komentarz |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Określa limit dla obszaru danych, prowadzące przez Magazyn zapytań w bazie danych klienta |100 |Wymuszane dla nowych baz danych |
| PARAMETR INTERVAL_LENGTH_MINUTES |Określa rozmiar przedział czasu, podczas którego statystyk zebranych środowiska uruchomieniowego dla planów zapytania są agregowane i utrwalone. Każdy plan aktywnej kwerendzie ma co najwyżej jeden wiersz w danym okresie czasu zdefiniowanego w tej konfiguracji |60 |Wymuszane dla nowych baz danych |
| PARAMETR STALE_QUERY_THRESHOLD_DAYS |Zasady oparte na czasie oczyszczania, które kontrolują okres przechowywania statystyk utrwalonego środowiska uruchomieniowego i nieaktywne zapytania |30 |Wymuszane dla nowych baz danych i baz danych z poprzedniej domyślne (367) |
| SIZE_BASED_CLEANUP_MODE |Określa, czy automatyczne oczyszczania ma miejsce, gdy zbliża się do limitu rozmiaru danych w magazynie zapytań |AUTO |Wymuszone dla wszystkich baz danych |
| QUERY_CAPTURE_MODE |Określa, czy są śledzone wszystkich zapytań czy tylko podzbiór zapytań |AUTO |Wymuszone dla wszystkich baz danych |
| FLUSH_INTERVAL_SECONDS |Określa maksymalny okres, przez który przechwycone środowiska uruchomieniowego, które statystyki są przechowywane w pamięci, przed opróżnianie na dysku |900 |Wymuszane dla nowych baz danych |
|  | | | |

> [!IMPORTANT]
> Te wartości domyślne są automatycznie stosowane w ostatnim etapie aktywacji magazyn zapytań w wszystkich baz danych Azure SQL (zobacz ważna uwaga poprzedzających). Po powyższe w górę baza danych SQL Azure nie będzie zmiana wartości konfiguracji ustawione przez klientów, chyba że ich niekorzystnie wpłynąć na podstawowe obciążenie lub niezawodnego działania w magazynie zapytań.
> 
> 

Pozostanie z ustawienia niestandardowe, należy użyć [ALTER DATABASE z użyciem opcji magazynu zapytań](https://msdn.microsoft.com/library/bb522682.aspx) można przywrócić konfigurację do poprzedniego stanu. Zapoznaj się z [najlepsze rozwiązania z magazynu zapytań](https://msdn.microsoft.com/library/mt604821.aspx) Aby dowiedzieć się, jak top wybrany parametry optymalną konfigurację.

## <a name="next-steps"></a>Kolejne kroki
[Szczegółowe informacje o wydajności bazy danych SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Zasoby dodatkowe
Aby uzyskać więcej informacji o wyewidencjonowanie następujące artykuły:

* [Rejestrator danych dla bazy danych](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 
* [Monitorowanie wydajności przy użyciu magazynu zapytań](https://msdn.microsoft.com/library/dn817826.aspx)
* [Scenariusze użycia magazynu zapytań](https://msdn.microsoft.com/library/mt614796.aspx)
* [Monitorowanie wydajności przy użyciu magazynu zapytań](https://msdn.microsoft.com/library/dn817826.aspx) 

