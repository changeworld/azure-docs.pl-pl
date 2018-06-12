---
title: Sposób użycia sys_schema dostrajania wydajności i konserwacji bazy danych w bazie danych Azure dla programu MySQL
description: W tym artykule opisano sposób użycia sys_schema znaleźć problemy z wydajnością i konserwacji bazy danych w bazie danych Azure dla programu MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 74bb59a8db70d4a01fcd3bd07054f1cbac50bf40
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266156"
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Jak używać sys_schema dla wydajności dostrajanie oraz konserwacji bazy danych w bazie danych Azure dla programu MySQL

MySQL performance_schema, pierwsza dostępna w wersji 5.5 MySQL, zapewnia Instrumentacja dla wielu zasobów serwera niezbędne takie jak alokacji pamięci, programy przechowywane metadane blokowania itp. Jednak performance_schema zawiera więcej niż 80 tabele i pobieranie niezbędnych informacji często wymaga przyłączanie tabel w performance_schema, jak również tabele z information_schema. Kompilowanie na performance_schema i information_schema, sys_schema udostępnia zaawansowane kolekcję [przyjazną dla użytkownika widoków](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) w bazie danych tylko do odczytu i jest całkowicie włączone w bazie danych Azure dla programu MySQL wersji 5.7.

![Widoki sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Brak 52 widoków w sys_schema, a każdy widok ma jeden z następujących prefiksów:

- Host_summary lub we/wy: opóźnienia związane z operacji We/Wy.
- InnoDB: Stan buforu InnoDB i blokad.
- Pamięć: Użycie pamięci przez hosta i użytkowników.
- Schemat: Schemat informacje związane z, takie jak automatyczne zwiększanie, indeksy itp.
- Instrukcja: Informacji na temat instrukcji języka SQL; może to być instrukcja, która spowodowała tabeli pełne skanowanie lub długi czas kwerendy.
- Użytkownik: Zasoby używane i pogrupowanych według użytkowników. Przykłady to plik operacji We/Wy, połączeń i pamięci.
- Poczekaj: Poczekaj zdarzenia pogrupowane według hosta lub użytkownika.

Teraz Przyjrzyjmy się sys_schema w niektórych typowych wzorców użycia. Rozpoczynać się od znaku, firma Microsoft będzie grupy wzorców użycia na dwie kategorie: **dostrajania wydajności** i **bazy danych konserwacji**.

## <a name="performance-tuning"></a>Dostosowywanie wydajności

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

We/Wy jest najbardziej kosztowna operacja w bazie danych. Firma Microsoft, można znaleźć średni czas oczekiwania operacji We/Wy, badając *sys.user_summary_by_file_io* widoku. Przy użyciu domyślnego 125 GB zainicjowanego magazynu, Mój czas oczekiwania na We/Wy jest około 15 sekund.

![czas oczekiwania na we/wy: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Ponieważ baza danych Azure dla programu MySQL skaluje we/wy w odniesieniu do magazynu, po zwiększeniu ilości Moje zainicjowanego magazynu do 1 TB, Mój czas oczekiwania na we/wy zmniejsza 571 MS, co 26 wzrost wydajności X!

![czas oczekiwania na we/wy: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

Pomimo należy dokładnie zaplanować wiele zapytań nadal może spowodować tabeli pełnego skanowania. Aby uzyskać dodatkowe informacje o typach indeksy i sposobu ich optymalizacji mogą odwoływać się do tego artykułu: [jak rozwiązywanie problemów z wydajnością zapytania](./howto-troubleshoot-query-performance.md). Tabela pełnego skanowania są zasobów i obniżyć wydajność bazy danych. Najszybszym sposobem sprawdzenia tabel z tabeli pełne skanowanie jest zapytania *sys.schema_tables_with_full_table_scans* widoku.

![Tabela pełnego skanowania](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

Aby rozwiązać problemy z wydajnością bazy danych, mogą być przydatne do identyfikowania zdarzenia wykonywane w ramach bazy danych i przy użyciu *sys.user_summary_by_statement_type* widok może po prostu wykonaj lewy.

![Podsumowanie według instrukcji](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

W tym przykładzie baza danych Azure dla programu MySQL przeznaczonego 53 minut opróżniania dziennika zapytań slog 44579 razy. To już długo i wiele IOs. Można zmniejszyć tego działania przez Wyłącz dziennika powolne zapytania lub zmniejszyć częstotliwość powolne zapytania logowania w portalu Azure.

## <a name="database-maintenance"></a>Konserwacji bazy danych

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

Pula buforów InnoDB znajduje się w pamięci i jest mechanizm głównej pamięci podręcznej między bazami danych i magazynu. Rozmiar puli buforów InnoDB jest związany z warstwy wydajności i nie można zmienić, chyba że innego produktu wybrana jednostka SKU. Podobnie jak w przypadku pamięci w systemie operacyjnym, stare strony są wymieniane, aby zwolnić miejsce dla fresher danych. Aby dowiedzieć się, które tabele korzystać z najbardziej pamięci puli buforów InnoDB, można zbadać *sys.innodb_buffer_stats_by_table* widoku.

![Stan buforu InnoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

Na rysunku powyżej jest jasne, czy innych niż system tabele i widoki, każdej tabeli w bazie danych mysqldatabase033 który hostuje jedną Moje witryny WordPress, zajmuje 16 KB lub 1 stronę danych w pamięci.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indeksy są doskonałych narzędzi, aby zwiększyć wydajność odczytu, ale pociągnąć za sobą dodatkowe koszty dla instrukcji INSERT i magazynu. *Sys.schema_unused_indexes* i *sys.schema_redundant_indexes* wgląd w nieużywane lub zduplikowane indeksów.

![nieużywane indeksów](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![nadmiarowe indeksy](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Podsumowanie

Podsumowując sys_schema to doskonałe narzędzie dla obu wydajności dostrajanie oraz konserwacji bazy danych. Upewnij się móc korzystać z tej funkcji w bazie danych Azure dla programu MySQL. 

## <a name="next-steps"></a>Kolejne kroki
- Aby równorzędnej odpowiedzi na pytania najbardziej zainteresowanych lub post nowe pytanie/odpowiedź, odwiedź stronę [MSDN forum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) lub [przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-database-mysql).
