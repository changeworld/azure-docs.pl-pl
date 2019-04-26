---
title: Optymalizowanie zbieranie statystyki zapytań na usługi Azure Database for postgresql w warstwie serwera
description: W tym artykule opisano, jak można optymalizować zbieranie statystyki zapytań na usługi Azure Database for postgresql w warstwie serwera.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 10/25/2018
ms.date: 02/18/2019
ms.openlocfilehash: 428a22f79008130448bef65f14322d6880b1b367
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422711"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql-server"></a>Optymalizowanie zapytań zbieranie statystyk na usługi Azure Database for postgresql w warstwie serwera 
W tym artykule opisano, jak zoptymalizować zbieranie statystyk zapytań na usługi Azure Database for postgresql w warstwie serwera.

## <a name="use-pgstatsstatements"></a>Użyj pg_stats_statements
**Pg_stat_statements** to rozszerzenie PostgreSQL, które jest domyślnie włączona, w usłudze Azure Database for PostgreSQL. Rozszerzenie udostępnia środki do śledzenia wykonania statystyki dla wszystkich instrukcji SQL, wykonywane przez serwer. Ten moduł punkty zaczepienia do każdego wykonania zapytań i jest dostarczany z nietrywialnymi negatywnie na wydajność. Włączanie **pg_stat_statements** wymusza zapytania zapisów tekstu do plików na dysku.

Jeśli masz unikatowy zapytania z tekstem długie zapytania lub nie aktywnie monitoruje **pg_stat_statements**, wyłącz **pg_stat_statements** uzyskać najlepszą wydajność. Aby to zrobić, należy zmienić ustawienie, aby `pg_stat_statements.track = NONE`.

Zauważyliśmy już niektórych obciążeń klienta do poprawy wydajności 50 procent podczas **pg_stat_statements** jest wyłączona. Kompromisem za ułatwienie wprowadzone po wyłączeniu pg_stat_statements to niemożność Rozwiązywanie problemów z wydajnością.

Aby ustawić `pg_stat_statements.track = NONE`:

- W witrynie Azure portal przejdź do [zarządzania zasobami PostgreSQL strony i wybierz blok parametrów serwera](howto-configure-server-parameters-using-portal.md).

  ![Blok parametrów serwera PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Użyj [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md) az postgres server Konfiguracja ustawiona na `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Użyj Store zapytania 
[Query Store](concepts-query-store.md) funkcji w usłudze Azure Database for PostgreSQL zapewnia bardziej efektywną metodę, aby śledzić statystyki zapytań. Firma Microsoft zaleca tej funkcji jako alternatywa dla użycia *pg_stats_statements*. 

## <a name="next-steps"></a>Kolejne kroki
Rozważ ustawienie `pg_stat_statements.track = NONE` w [witryny Azure portal](howto-configure-server-parameters-using-portal.md) lub za pomocą [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

Aby uzyskać więcej informacji, zobacz: 
- [Scenariusze użycia magazynu zapytań](concepts-query-store-scenarios.md) 
- [Najlepsze rozwiązania Query Store](concepts-query-store-best-practices.md) 
