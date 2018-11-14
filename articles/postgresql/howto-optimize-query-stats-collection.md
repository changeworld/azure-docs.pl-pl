---
title: Optymalizowanie zbieranie statystyki zapytań w usłudze Azure Database dla kolekcji statystyki zapytań serwera PostgreSQL
description: W tym artykule opisano, jak można optymalizować zbieranie statystyki zapytań na usługi Azure Database for postgresql w warstwie serwera.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8b8d1a5a16a948953838f51edca091121dc14df1
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629125"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>Optymalizowanie zbieranie statystyki zapytań w usłudze Azure Database dla serwera PostgreSQL 
W tym artykule opisano w celu zoptymalizowania zbieranie statystyki zapytań w usługi Azure Database for postgresql w warstwie serwera.

## <a name="using-pgstatsstatements"></a>Za pomocą pg_stats_statements
**Pg_stat_statements** to rozszerzenie PostgreSQL, które jest domyślnie włączona, w usłudze Azure Database for PostgreSQL. Rozszerzenie udostępnia środki do śledzenia statystyki wykonania instrukcji SQL wszystkie wykonane przez serwer. Jednak ten moduł punkty zaczepienia do każdego wykonania zapytań i jest dostarczany z nietrywialnymi negatywnie na wydajność. Włączanie **pg_stat_statements** wymusza zapytania zapisów tekstu do plików na dysku.

Klienci, którzy mają unikatowe zapytań przy użyciu tekstu zapytania długi lub nie są aktywnie monitoruje **pg_stat_statements**, zaleca się wyłączenie **pg_stat_statements** uzyskać najlepszą wydajność, ustawiając `pg_stat_statements.track = NONE`.

W niektórych obciążeń klientów Zaobserwowaliśmy do poprawy wydajności 50 procent, wyłączając **pg_stat_statements**. Kompromisem za ułatwienie przez jeden, wyłączając pg_stat_statements jest jednak niemożność Rozwiązywanie problemów z wydajnością.

Aby ustawić `pg_stat_statements.track = NONE`:

- W witrynie Azure portal przejdź do [zarządzania zasobami PostgreSQL strony i wybierz blok parametrów serwera](howto-configure-server-parameters-using-portal.md).

![Blok parametrów serwera PostgreSQL](.\media\howto-optimize-query-stats-collection\pg_stats_statements_portal.png)

- Za pomocą [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md), az postgres server configuration set `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="using-query-store"></a>Za pomocą Query Store 
[Query Store](concepts-query-store.md) funkcji w usłudze Azure Database for PostgreSQL zapewnia więcej metodę wydajne, aby śledzić statystyki zapytań i jest zalecane jako alternatywa dla użycia *pg_stats_statements*. 

## <a name="next-steps"></a>Kolejne kroki
Rozważ ustawienie `pg_stat_statements.track = NONE` w [witryny Azure Portal](howto-configure-server-parameters-using-portal.md) lub za pomocą [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

Zobacz [scenariusze użycia Query Store](concepts-query-store-scenarios.md) i [Query Store najlepszych rozwiązań](concepts-query-store-best-practices.md) Aby uzyskać więcej informacji. 
