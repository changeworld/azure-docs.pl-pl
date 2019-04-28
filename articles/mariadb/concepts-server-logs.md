---
title: Dzienniki serwera usługi Azure Database dla serwera MariaDB
description: W tym artykule opisano dzienników dostępnych w usłudze Azure Database dla MariaDB oraz dostępne parametry włączenie rejestrowania różnych poziomów.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a26f61eb199d8f370e1a9dd010932dc868b74ae4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61041262"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Dzienników serwera w usłudze Azure Database dla serwera MariaDB
W usłudze Azure Database dla serwera MariaDB dziennik dotyczący wolnego zapytania jest dostępna dla użytkowników. Dostęp do dziennika transakcji nie jest obsługiwane. Dziennik dotyczący wolnego zapytania może służyć do identyfikowania wąskich gardeł wydajności w celu rozwiązywania problemów.

Aby uzyskać więcej informacji na temat w dzienniku wolnych zapytań, zobacz dokumentację MariaDB [dziennik dotyczący wolnego zapytania](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Dzienniki dostępu serwera
Można wyświetlić listę, a następnie pobrać dzienniki serwera MariaDB przy użyciu witryny Azure portal i interfejsu wiersza polecenia platformy Azure — Azure Database.

W witrynie Azure portal wybierz usługi Azure Database dla serwera MariaDB. W obszarze **monitorowanie** nagłówka, wybierz **dzienniki serwera** strony.

<!-- For more information on Azure CLI, see [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md).-->

## <a name="log-retention"></a>Przechowywanie dzienników
Dzienniki są dostępne przez maksymalnie siedem dni od ich tworzenia. Jeśli całkowity rozmiar dostępne dzienniki przekracza 7 GB, najstarsze pliki zostaną usunięte do momentu zwolnienia miejsca.

Dzienniki zostały obrócone co 24 godziny lub 7 GB, zależnie co nastąpi wcześniej.

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Domyślnie dziennik wolnych zapytań jest wyłączony. Aby ją włączyć, ustaw slow_query_log na wartość ON.

Inne parametry, które można dostosować obejmują:

- **long_query_time**: Jeśli zapytanie dłużej niż long_query_time (w sekundach) to zapytanie jest rejestrowane. Wartość domyślna to 10 sekund.
- **log_slow_admin_statements**: Jeśli ON zawiera administracyjnej instrukcji takich jak ALTER_TABLE i ANALYZE_TABLE w instrukcjach zapisywane slow_query_log.
- **log_queries_not_using_indexes**: Określa, czy zapytań, które nie korzystają z indeksów są rejestrowane slow_query_log
- **log_throttle_queries_not_using_indexes**: Ten parametr ogranicza liczbę zapytań — indeksowanie, które mogą być zapisywane w dzienniku wolnych zapytań. Ten parametr staje się skuteczny po log_queries_not_using_indexes jest ustawiona na wartość ON.

Zobacz MariaDB [wolne dokumentacji dziennika zapytań](https://mariadb.com/kb/en/library/slow-query-log-overview/) pełne opisy parametrów dziennik wolnych zapytań.

## <a name="next-steps"></a>Następne kroki
- [Jak skonfigurować i dostęp do dzienników serwera w witrynie Azure portal](howto-configure-server-logs-portal.md).
