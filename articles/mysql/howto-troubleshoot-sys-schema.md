---
title: Korzystaj z sys_schema — usługa Azure Database for MySQL
description: Dowiedz się, jak używać sys_schema do znajdowania problemów z wydajnością i obsługi bazy danych w usłudze Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: a35a586a519ff78e8b32d986b92bd008b2c6b858
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067861"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Jak używać sys_schema do dostrajania wydajności i konserwacji bazy danych w usłudze Azure Database for MySQL

Performance_schema MySQL, po raz pierwszy dostępny w MySQL 5.5, zapewnia instrumentację dla wielu ważnych zasobów serwera, takich jak alokacja pamięci, zapisane programy, blokowanie metadanych itp. Jednak performance_schema zawiera ponad 80 tabel, a uzyskanie niezbędnych informacji często wymaga łączenia tabel w performance_schema, a także tabel z information_schema. Opierając się na performance_schema i information_schema, sys_schema zapewnia zaawansowana kolekcja [przyjaznych dla użytkownika widoków](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) w bazie danych tylko do odczytu i jest w pełni włączona w usłudze Azure Database for MySQL w wersji 5.7.

![sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

W sys_schema znajdują się 52 widoki, a każdy widok ma jeden z następujących prefiksów:

- Host_summary lub IO: opóźnienia związane z we/wy.
- InnoDB: Stan buforu InnoDB i blokady.
- Pamięć: Użycie pamięci przez hosta i użytkowników.
- Schemat: Informacje związane ze schematem, takie jak automatyczne zwiększanie, indeksy itp.
- Instrukcja: Informacje o instrukcjach SQL; może to być instrukcja, która spowodowała pełne skanowanie tabeli lub długi czas kwerendy.
- Użytkownik: Zasoby zużyte i pogrupowane według użytkowników. Przykładami są we/wy plików, połączenia i pamięć.
- Czekaj: Poczekaj na zdarzenia pogrupowane według hosta lub użytkownika.

Teraz przyjrzyjmy się niektórym typowym wzorcom użycia sys_schema. Na początek pogrupujemy wzorce użycia w dwie kategorie: **dostrajanie wydajności** i **konserwacja bazy danych.**

## <a name="performance-tuning"></a>Dostosowywanie wydajności

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

We/Wy jest najdroższą operacją w bazie danych. Możemy dowiedzieć się średniego opóźnienia we/wy, przeprowadzając kwerendę w widoku *sys.user_summary_by_file_io.* Przy domyślnym 125 GB aprowizowanego magazynu moje opóźnienie we/wy wynosi około 15 sekund.

![Opóźnienie io: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Ponieważ usługa Azure Database for MySQL skaluje we/wy w odniesieniu do magazynu, po zwiększeniu mojej aprowizowanego magazynu do 1 TB, moje opóźnienie we/wy zmniejsza się do 571 ms.

![opóźnienie io: 1 TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Pomimo starannego planowania, wiele zapytań może nadal skutkować pełnym skanowaniem tabeli. Aby uzyskać dodatkowe informacje na temat typów indeksów i sposobu ich optymalizacji, można znaleźć w tym artykule: [Jak rozwiązywać problemy z wydajnością kwerendy](./howto-troubleshoot-query-performance.md). Skanowanie pełnej tabeli jest intensywnie korzystające z zasobów i obniża wydajność bazy danych. Najszybszym sposobem znalezienia tabel z pełnym skanowaniem tabeli jest zapytanie do widoku *sys.schema_tables_with_full_table_scans.*

![skanowanie pełnej tabeli](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Aby rozwiązać problemy z wydajnością bazy danych, może być korzystne zidentyfikowanie zdarzeń dzieje się wewnątrz bazy danych i przy użyciu widoku *sys.user_summary_by_statement_type* może po prostu załatwić sprawę.

![podsumowanie według zestawienia](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

W tym przykładzie usługa Azure Database for MySQL spędził 53 minut opróżniania dziennika kwerend slog 44579 razy. To długi czas i wiele IOs. Możesz zmniejszyć to działanie, wyłącz dziennik wolnych zapytań lub zmniejsz częstotliwość powolnego logowania do witryny Azure portal.

## <a name="database-maintenance"></a>Konserwacja bazy danych

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

Pula buforów InnoDB znajduje się w pamięci i jest głównym mechanizmem pamięci podręcznej między systemem dbms i magazynu. Rozmiar puli buforów InnoDB jest powiązany z warstwą wydajności i nie można go zmienić, chyba że zostanie wybrana inna jednostka SKU produktu. Podobnie jak w przypadku pamięci w systemie operacyjnym, stare strony są wymieniane, aby zrobić miejsce na świeższe dane. Aby dowiedzieć się, które tabele zużywają większość pamięci puli buforów InnoDB, można zbadać widok *sys.innodb_buffer_stats_by_table.*

![Stan buforu InnoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

Na powyższej grafice widać, że poza tabelami systemowymi i widokami, każda tabela w bazie danych mysqldatabase033, w której znajduje się jedna z moich witryn WordPress, zajmuje 16 KB lub 1 stronę danych w pamięci.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indeksy są doskonałymi narzędziami do poprawy wydajności odczytu, ale ponoszą dodatkowe koszty wstawienia i magazynu. *Sys.schema_unused_indexes* i *sys.schema_redundant_indexes* zapewniają wgląd w nieużywane lub zduplikowane indeksy.

![nieużywane indeksy](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![indeksy nadmiarowe](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Podsumowanie

Podsumowując, sys_schema jest doskonałym narzędziem zarówno dostrajania wydajności, jak i konserwacji bazy danych. Upewnij się, że skorzystasz z tej funkcji w bazie danych usługi Azure dla mysql. 

## <a name="next-steps"></a>Następne kroki
- Aby znaleźć odpowiedzi na najbardziej zainteresowanych pytań lub opublikować nowe pytanie / odpowiedź, odwiedź [forum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
