---
title: Rejestrowanie inspekcji — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Pojęcia dotyczące rejestrowania inspekcji pgAudit w usłudze Azure Database dla postgreSql — single server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842473"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Inspekcja rejestrowania w usłudze Azure Database dla postgreSQL — pojedynczy serwer

Inspekcja rejestrowania działań bazy danych w usłudze Azure Database for PostgreSQL — pojedynczy serwer jest dostępny za pośrednictwem rozszerzenia inspekcji PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit zapewnia szczegółowe rejestrowanie inspekcji sesji i/lub obiektów.

> [!NOTE]
> pgAudit jest w wersji zapoznawczej w usłudze Azure Database for PostgreSQL.
> Rozszerzenie można włączyć tylko na serwerach ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

Jeśli chcesz dzienniki na poziomie zasobów platformy Azure dla operacji, takich jak skalowanie obliczeniowe i magazynowe, zobacz [dziennik aktywności platformy Azure.](../azure-monitor/platform/platform-logs-overview.md)

## <a name="usage-considerations"></a>Zagadnienia dotyczące użycia
Domyślnie instrukcje rejestrowania rozszerzenia pgAudit są emitowane wraz z normalnymi instrukcjami rejestrowania przy użyciu standardowej funkcji rejestrowania bazy danych Postgres. W usłudze Azure Database for PostgreSQL pliki LOG można pobrać z poziomu witryny Azure Portal lub interfejsu wiersza polecenia. Maksymalna ilość miejsca do magazynowania plików wynosi 1 GB, a każdy plik jest dostępny przez maksymalnie siedem dni (wartość domyślna to trzy dni). Ta usługa jest opcją przechowywania krótkoterminowego.

Alternatywnie można skonfigurować wszystkie dzienniki, które mają być emitowane do usługi dziennika diagnostycznego usługi usługi Azure Monitor. Jeśli włączysz rejestrowanie diagnostyczne usługi Azure Monitor, dzienniki będą automatycznie wysyłane (w formacie JSON) do usługi Azure Storage, usługi Event Hubs i/lub dzienników usługi Azure Monitor, w zależności od wyboru.

Włączenie rozszerzenia pgAudit powoduje generowanie dużej liczby dzienników na serwerze, co ma wpływ na wydajność i magazyn dzienników. Zalecamy korzystanie z usługi dzienników diagnostycznych platformy Azure, która oferuje długoterminowe opcje magazynowania, a także funkcje analizy i alertów. Zalecamy wyłączenie standardowego rejestrowania, aby zmniejszyć wpływ dodatkowego rejestrowania na wydajność:

   1. Ustaw parametr `logging_collector` na OFF. 
   2. Uruchom ponownie serwer, aby zastosować tę zmianę.

Aby dowiedzieć się, jak skonfigurować rejestrowanie w usłudze Azure Storage, Usługi Event Hubs lub Azure Monitor, odwiedź sekcję dzienników diagnostycznych [dziennika serwera.](concepts-server-logs.md)

## <a name="installing-pgaudit"></a>Instalacja pgAudit

Aby zainstalować pgAudit, należy dołączyć go do udostępnionych bibliotek wstępnego ładowania serwera. Zmiana parametru `shared_preload_libraries` Postgres wymaga ponownego uruchomienia serwera. Parametry można zmieniać za pomocą [witryny Azure portal,](howto-configure-server-parameters-using-portal.md) [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md)lub interfejsu API [REST](/rest/api/postgresql/configurations/createorupdate).

Korzystanie z [portalu Azure:](https://portal.azure.com)

   1. Wybierz serwer usługi Azure Database for PostgreSQL.
   2. Na pasku bocznym wybierz pozycję **Parametry serwera**.
   3. Wyszukaj `shared_preload_libraries` parametr.
   4. Wybierz **pgaudit**.
   5. Uruchom ponownie serwer, aby zastosować zmianę.

   6. Połącz się z serwerem za pomocą klienta (np. psql) i włącz rozszerzenie pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Jeśli widzisz błąd, upewnij się, że ponownie `shared_preload_libraries`uruchomiono serwer po zapisaniu .

## <a name="pgaudit-settings"></a>ustawienia pgAudit

pgAudit umożliwia skonfigurowanie rejestrowania inspekcji sesji lub obiektów. [Rejestrowanie inspekcji sesji](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emituje szczegółowe dzienniki wykonanych instrukcji. [Rejestrowanie inspekcji obiektów](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) jest inspekcji zakres do określonych relacji. Można skonfigurować jeden lub oba typy rejestrowania. 

> [!NOTE]
> ustawienia pgAudit są określone gloabally i nie mogą być określone na poziomie bazy danych lub roli.

Po [zainstalowaniu pgAudit,](#installing-pgaudit)można skonfigurować jego parametry, aby rozpocząć rejestrowanie. [Dokumentacja pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) zawiera definicję każdego parametru. Najpierw przetestuj parametry i upewnij się, że otrzymujesz oczekiwane zachowanie.

> [!NOTE]
> Ustawienie `pgaudit.log_client` on spowoduje przekierowanie dzienników do procesu klienta (np. psql) zamiast zapisania do pliku. To ustawienie powinno być zwykle wyłączone. <br> <br>
> `pgaudit.log_level`jest włączona `pgaudit.log_client` tylko wtedy, gdy jest włączona.

> [!NOTE]
> W usłudze Azure Database for `pgaudit.log` PostgreSQL nie można ustawić przy użyciu skrótu do znaku (minus) zgodnie z opisem `-` w dokumentacji pgAudit. Wszystkie wymagane klasy instrukcji (READ, WRITE itp.) powinny zostać określone indywidualnie.

### <a name="audit-log-format"></a>Format dziennika inspekcji
Każdy wpis inspekcji jest `AUDIT:` wskazywany przez blisko początku wiersza dziennika. Format pozostałej części wpisu jest szczegółowo opisany w [dokumentacji pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Jeśli potrzebujesz innych pól, aby spełnić wymagania inspekcji, `log_line_prefix`użyj parametru Postgres . `log_line_prefix`jest ciągiem wyjściowym na początku każdego wiersza dziennika Postgres. Na przykład następujące `log_line_prefix` ustawienie zawiera sygnaturę czas, nazwę użytkownika, nazwę bazy danych i identyfikator procesu:

```
t=%m u=%u db=%d pid=[%p]:
```

Aby dowiedzieć `log_line_prefix`się więcej o , odwiedź [dokumentację PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Wprowadzenie
Aby szybko rozpocząć, `pgaudit.log` `WRITE`ustaw na , i otwórz dzienniki, aby przejrzeć dane wyjściowe. 

## <a name="viewing-audit-logs"></a>Wyświetlanie dzienników inspekcji
Jeśli używasz plików .log, dzienniki inspekcji zostaną uwzględnione w tym samym pliku, co dzienniki błędów PostgreSQL. Pliki dziennika można pobierać z [witryny](howto-configure-server-logs-in-portal.md) Azure portal lub [interfejsu wiersza polecenia.](howto-configure-server-logs-using-cli.md) 

Jeśli używasz rejestrowania diagnostycznego platformy Azure, sposób uzyskiwania dostępu do dzienników zależy od wybranego punktu końcowego. W przypadku usługi Azure Storage zobacz artykuł [o koncie magazynu dzienników.](../azure-monitor/platform/resource-logs-collect-storage.md) W przypadku centrów zdarzeń zobacz artykuł [dzienników platformy Azure strumienia.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

W przypadku dzienników usługi Azure Monitor dzienniki są wysyłane do wybranego obszaru roboczego. Dzienniki postgres używają trybu kolekcji **AzureDiagnostics,** dzięki czemu można je wyszukiwać z tabeli AzureDiagnostics. Pola w tabeli są opisane poniżej. Dowiedz się więcej o kwerendach i alertach w omówieniu [kwerend dzienników usługi Azure Monitor.](../azure-monitor/log-query/log-query-overview.md)

Aby rozpocząć pracę, można użyć tej kwerendy. Alerty można konfigurować na podstawie zapytań.

Wyszukaj wszystkie dzienniki Postgres dla określonego serwera w ostatnim dniu
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej o logowaniu do bazy danych Usługi Azure dla postgreSQL](concepts-server-logs.md)
- Dowiedz się, jak ustawić parametry przy użyciu [witryny Azure portal,](howto-configure-server-parameters-using-portal.md) [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md)lub interfejsu API [REST](/rest/api/postgresql/configurations/createorupdate).
