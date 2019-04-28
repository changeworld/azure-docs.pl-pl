---
title: Sposób użycia sys_schema dostrajania wydajności i konserwacji bazy danych w usłudze Azure Database for MySQL
description: W tym artykule opisano, jak za pomocą sys_schema znajdowanie problemów z wydajnością i obsługa bazy danych w usłudze Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/01/2018
ms.openlocfilehash: 993c77056c09c1dc21d5317ddbfe8e937341718d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422375"
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Jak używać sys_schema dla wydajności dostosowywania oraz konserwacji bazy danych w usłudze Azure Database for MySQL

MySQL performance_schema, pierwsza dostępna w wersji 5.5 MySQL, zawiera Instrumentację do wielu zasobów od serwera, takich jak alokacji pamięci, programy przechowywane, metadane, blokowanie, itd. Jednak performance_schema zawiera więcej niż 80 tabele i niezbędne informacje często wymaga przyłączanie tabel w performance_schema, jak również tabele z information_schema. Opierając się na performance_schema i information_schema, sys_schema udostępnia zaawansowany zbiór [przyjazny dla użytkownika widoków](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) w bazie danych tylko do odczytu i w pełni włączony w usłudze Azure Database for MySQL w wersji 5.7.

![Widoki sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

W sys_schema są widoki 52, a każdy widok zawiera jedną z następujących prefiksów:

- Host_summary lub operacji We/Wy: Operacje We/Wy związane z opóźnieniami poniżej.
- InnoDB: Stan buforu aparatu InnoDB i blokad.
- Pamięć: Użycie pamięci przez hosta i użytkowników.
- Schemat: Schematu, powiązane informacje, takie jak automatyczne zwiększanie, indeksy itp.
- Instrukcja: Informacji na temat instrukcji języka SQL; może to być instrukcja, która spowodowała pełne skanowanie tabeli lub długi czas wykonywania zapytania.
- Użytkownik: Zasoby używane i pogrupowanych według użytkowników. Przykłady to plik operacji We/Wy, połączeń i pamięci.
- Oczekiwania: Poczekaj zdarzeń pogrupowanych wg hosta lub użytkownika.

Teraz Spójrzmy na niektóre typowe wzorce użycia sys_schema. Najpierw zostanie pogrupujemy wzorców użycia na dwie kategorie: **Dostrajanie wydajności** i **baza danych konserwacji**.

## <a name="performance-tuning"></a>Dostosowywanie wydajności

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

We/Wy jest najbardziej kosztownych operacji w bazie danych. Firma Microsoft można znaleźć średni czas oczekiwania operacji We/Wy, badając *sys.user_summary_by_file_io* widoku. Przy użyciu domyślnego 125 GB aprowizowanego magazynu, Moje czas oczekiwania operacji We/Wy jest około 15 sekund.

![czas oczekiwania operacji We/Wy: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Ponieważ — Azure Database for MySQL umożliwia skalowanie operacji We/Wy w odniesieniu do magazynu, po zwiększeniu ilości do 1 TB aprowizowanego magazynu, Moje czas oczekiwania operacji We/Wy zmniejsza 571 MS.

![czas oczekiwania operacji We/Wy: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

Pomimo starannego planowania wiele zapytań nadal może spowodować skanowania pełną tabelę. Aby uzyskać dodatkowe informacje o typach indeksy i sposobu ich optymalizacji mogą odwoływać się do tego artykułu: [Jak rozwiązywać problemy z wydajnością zapytań](./howto-troubleshoot-query-performance.md). Tabela pełnego skanowania są dużej ilości zasobów i obniżyć wydajność bazy danych. Najszybszym sposobem znalezienia tabele zawierające pełne skanowanie tabeli jest do kwerendy *sys.schema_tables_with_full_table_scans* widoku.

![Tabela pełnego skanowania](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

Aby rozwiązać problemy z wydajnością bazy danych, może być korzystne ustalić zdarzenia wykonywane w bazie danych, a za pomocą *sys.user_summary_by_statement_type* widok może po prostu robią lewy.

![Podsumowanie przez instrukcję](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

W tym przykładzie — Azure Database for MySQL poświęcony 53 minut opróżniania dziennika zapytań slog 44579 razy. To dużo czasu i wiele z systemem IOs. Można zmniejszyć tego działania, albo Wyłącz dziennik wolnych zapytań lub zmniejszyć częstotliwość wolnych zapytań logowania w witrynie Azure portal.

## <a name="database-maintenance"></a>Konserwacji bazy danych

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

Pula buforów aparatu InnoDB znajduje się w pamięci i mechanizm głównej pamięci podręcznej między bazami danych i magazynem. Rozmiar puli bufora aparatu InnoDB jest powiązany z warstwy wydajności i nie można zmienić, o ile nie zostanie wybrany inny produkt jednostki SKU. Podobnie jak w przypadku pamięci w systemie operacyjnym, starej strony są wymieniane aby zwolnić miejsce dla fresher danych. Aby dowiedzieć się, tabel, które zużywają większość pamięci puli buforów aparatu InnoDB, można tworzyć zapytania *sys.innodb_buffer_stats_by_table* widoku.

![Stan buforu aparatu InnoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

Na rysunku powyżej jest jasne, że inne niż tabele systemowe i widoki, każdej tabeli w bazie danych mysqldatabase033 której zainstalowany jeden z moich witryn WordPress, zajmuje 16 KB lub 1 stronę danych w pamięci.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indeksy są doskonałe narzędzia pozwalające zwiększyć wydajność odczytu, ale pociągnąć za sobą dodatkowe koszty dla instrukcji INSERT i magazynu. *Sys.schema_unused_indexes* i *sys.schema_redundant_indexes* niezastąpione nieużywany lub zduplikowany indeksów.

![nieużywane indeksów](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![nadmiarowe indeksy](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Podsumowanie

Podsumowując sys_schema jest doskonałym narzędziem do obu wydajności dostosowywania oraz konserwacji bazy danych. Pamiętaj skorzystać z tej funkcji w usłudze Azure Database for MySQL. 

## <a name="next-steps"></a>Kolejne kroki
- Aby znaleźć elementu równorzędnego odpowiedzi na pytania dotyczące najbardziej interesujących lub zadać nowe pytanie/odpowiedź, odwiedź stronę [forum MSDN dotyczącym](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
