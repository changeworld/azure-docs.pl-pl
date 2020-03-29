---
title: Optymalizuj zbieranie statystyk zapytań — usługa Azure Database for PostgreSQL — single server
description: W tym artykule opisano, jak zoptymalizować zbieranie statystyk zapytań w bazie danych azure dla postgreSQL — pojedynczy serwer
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770173"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Optymalizuj zbieranie statystyk zapytań w bazie danych azure dla postgreSql — pojedynczy serwer
W tym artykule opisano sposób optymalizacji kolekcji statystyk zapytań w bazie danych Azure dla serwera PostgreSQL.

## <a name="use-pg_stats_statements"></a>Użyj pg_stats_statements
**Pg_stat_statements** jest rozszerzeniem PostgreSQL, które jest domyślnie włączone w usłudze Azure Database for PostgreSQL. Rozszerzenie zapewnia środki do śledzenia statystyk wykonania dla wszystkich instrukcji SQL wykonywanych przez serwer. Ten moduł łączy się z każdym wykonaniem kwerendy i jest wyposażony w nietrywialny koszt wydajności. Włączenie **pg_stat_statements** wymusza zapisywanie tekstu zapytania w plikach na dysku.

Jeśli masz unikatowe zapytania z długim tekstem zapytania lub nie monitorujesz aktywnie **pg_stat_statements,** wyłącz **pg_stat_statements,** aby uzyskać najlepszą wydajność. Aby to zrobić, zmień `pg_stat_statements.track = NONE`ustawienie na .

Niektóre obciążenia klientów odnotowały do 50 procent poprawy wydajności, gdy **pg_stat_statements** jest wyłączona. Kompromisem, który wprowadzasz po wyłączeniu pg_stat_statements jest niezdolność do rozwiązywania problemów z wydajnością.

Aby `pg_stat_statements.track = NONE`ustawić:

- W witrynie Azure portal przejdź do [strony zarządzania zasobami PostgreSQL i wybierz blok parametrów serwera](howto-configure-server-parameters-using-portal.md).

  ![Blok parametrów serwera PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Użyj konfiguracji serwera poczty az postgres platformy [Azure](howto-configure-server-parameters-using-cli.md) ustawionej na `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Korzystanie z Magazynu zapytań 
Funkcja [Magazynu zapytań](concepts-query-store.md) w usłudze Azure Database for PostgreSQL zapewnia bardziej skuteczną metodę śledzenia statystyk zapytań. Zalecamy tę funkcję jako alternatywę dla korzystania *z pg_stats_statements*. 

## <a name="next-steps"></a>Następne kroki
Należy `pg_stat_statements.track = NONE` rozważyć ustawienie w [witrynie Azure portal](howto-configure-server-parameters-using-portal.md) lub przy użyciu [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

Aby uzyskać więcej informacji, zobacz: 
- [Scenariusze użycia magazynu zapytań](concepts-query-store-scenarios.md) 
- [Najlepsze rozwiązania dotyczące magazynu zapytań](concepts-query-store-best-practices.md) 
