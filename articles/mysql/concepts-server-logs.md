---
title: Dzienniki serwera bazy danych Azure dla programu MySQL
description: Opisuje dzienników dostępnych w programie Azure bazy danych MySQL i dostępne parametry włączenie rejestrowania różnych poziomów.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ce6b6208b74063ea5d6e9868ca414f833b1a2045
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Do dzienników serwera w bazie danych systemu Azure dla programu MySQL
W przypadku bazy danych Azure dla programu MySQL dziennika powolne zapytania jest dostępna dla użytkowników. Dostęp do dziennika transakcji nie jest obsługiwany. Dziennik zapytań powolne może służyć do identyfikowania wąskich gardeł wydajności przy rozwiązywaniu problemów. 

Więcej informacji o dziennik powolne zapytań MySQL, można znaleźć w podręczniku odwołanie MySQL [powolna sekcji dziennika zapytań](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Dzienniki dostępu serwera
Można wyświetlić listę i pobrać bazy danych Azure do dzienników serwera MySQL przy użyciu portalu Azure i interfejsu wiersza polecenia Azure.

W portalu Azure wybierz bazy danych Azure, aby serwer MySQL. W obszarze **monitorowanie** nagłówek, wybierz **dzienniki serwera** strony.

Aby uzyskać więcej informacji dotyczących interfejsu wiersza polecenia Azure, zobacz [Konfigurowanie i dostępu do dzienników serwera przy użyciu interfejsu wiersza polecenia Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Przechowywanie dziennika
Dzienniki są dostępne przez siedem dni od ich tworzenia. Jeśli całkowity rozmiar dostępne dzienniki przekracza 7.5 GB, najstarsze pliki są usuwane, dopóki nie jest wystarczająca ilość miejsca. 

Dzienniki zostały obrócone co 24 godziny lub 7.5 GB zależności miało miejsce wcześniej.


## <a name="configure-logging"></a>Konfigurowanie rejestrowania 
Domyślnie dziennik powolne zapytań jest wyłączony. Aby ją włączyć, należy ustawić na wartość slow_query_log.

Inne parametry, które można dostosować obejmują:

- **long_query_time**: Jeśli zapytania trwa dłużej niż long_query_time (w sekundach) jest rejestrowany tego zapytania. Wartość domyślna to 10 sekund.
- **log_slow_admin_statements**: Jeśli ON zawiera instrukcje administracyjne, takie jak ALTER_TABLE i ANALYZE_TABLE w instrukcjach zapisane slow_query_log.
- **log_queries_not_using_indexes**: Określa, czy zapytania, które nie korzystają z indeksów są zalogowani slow_query_log
- **log_throttle_queries_not_using_indexes**: ten parametr ogranicza liczbę-index zapytania, które mogą być zapisywane w dzienniku powolne zapytania. Ten parametr zostanie uwzględniona, gdy log_queries_not_using_indexes jest ustawiona na wartość on.

Zobacz MySQL [powolna dokumentacji dziennika zapytań](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) pełne opisy parametrów dziennika powolne zapytania.

## <a name="next-steps"></a>Następne kroki
- [Jak skonfigurować i uzyskiwać dostęp do dzienników serwera z wiersza polecenia platformy Azure](howto-configure-server-logs-in-cli.md).
