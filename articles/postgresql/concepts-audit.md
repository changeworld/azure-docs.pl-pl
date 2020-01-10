---
title: Rejestrowanie inspekcji — Azure Database for PostgreSQL — pojedynczy serwer
description: Pojęcia związane z rejestrowaniem inspekcji pgAudit w Azure Database for PostgreSQL-pojedynczym serwerze.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: c0ce1648d7b5f7c25044ed8f66eafcca7b0009f4
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747345"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Rejestrowanie inspekcji w Azure Database for PostgreSQL — pojedynczy serwer

Rejestrowanie inspekcji działań bazy danych w ramach Azure Database for PostgreSQL-pojedynczego serwera jest dostępne za pomocą rozszerzenia inspekcji PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit zawiera szczegółowe informacje o rejestrowaniu sesji i/lub inspekcji obiektów.

> [!NOTE]
> pgAudit jest w wersji zapoznawczej na Azure Database for PostgreSQL.
> Rozszerzenie można włączyć tylko na serwerach Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

Jeśli chcesz, aby w dziennikach na poziomie zasobów platformy Azure były wykonywane operacje, takie jak skalowanie obliczeniowe i magazynowe, zobacz [Dziennik aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Zagadnienia dotyczące użycia
Domyślnie instrukcje rejestrowania rozszerzenia pgAudit są emitowane wraz z normalnymi instrukcjami rejestrowania przy użyciu standardowej funkcji rejestrowania bazy danych Postgres. W usłudze Azure Database for PostgreSQL pliki LOG można pobrać z poziomu witryny Azure Portal lub interfejsu wiersza polecenia. Maksymalny rozmiar magazynu dla kolekcji plików to 1 GB, a każdy plik jest dostępny przez maksymalnie siedem dni (wartość domyślna to trzy dni). Ta usługa jest krótkoterminową opcją magazynu.

Alternatywnie można skonfigurować wszystkie dzienniki, które mają być emitowane do usługi Dziennik diagnostyczny Azure Monitor. W przypadku włączenia rejestrowania diagnostycznego Azure Monitor dzienniki będą automatycznie wysyłane (w formacie JSON) do usługi Azure Storage, Event Hubs i/lub dzienników Azure Monitor, w zależności od wybranej opcji.

Włączenie rozszerzenia pgAudit powoduje generowanie dużej liczby dzienników na serwerze, co ma wpływ na wydajność i magazyn dzienników. Zalecamy korzystanie z usługi dzienników diagnostycznych platformy Azure, która oferuje długoterminowe opcje magazynowania, a także funkcje analizy i alertów. Zalecamy wyłączenie standardowego rejestrowania, aby zmniejszyć wpływ dodatkowego rejestrowania na wydajność:

   1. Ustaw parametr `logging_collector` na OFF. 
   2. Uruchom ponownie serwer, aby zastosować tę zmianę.

Aby dowiedzieć się, jak skonfigurować rejestrowanie do usługi Azure Storage, Event Hubs lub dzienników Azure Monitor, odwiedź sekcję dzienniki diagnostyczne w [artykule Dzienniki serwera](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Instalowanie pgAudit

Aby zainstalować pgAudit, należy dołączyć go do udostępnionych bibliotek wstępnego ładowania serwera. Zmiana parametru `shared_preload_libraries` Postgres wymaga ponownego uruchomienia serwera. Parametry można zmienić za pomocą [Azure Portal](howto-configure-server-parameters-using-portal.md), interfejsu [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md)lub [API REST](/rest/api/postgresql/configurations/createorupdate).

Przy użyciu [Azure Portal](https://portal.azure.com):

   1. Wybierz serwer usługi Azure Database for PostgreSQL.
   2. Na pasku bocznym wybierz opcję **parametry serwera**.
   3. Wyszukaj parametr `shared_preload_libraries`.
   4. Wybierz pozycję **pgaudit**.
   5. Uruchom ponownie serwer, aby zastosować zmianę.

   6. Nawiązywanie połączenia z serwerem za pomocą klienta programu (na przykład PSQL) i włączanie rozszerzenia pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Jeśli zobaczysz błąd, upewnij się, że serwer został ponownie uruchomiony po zapisaniu `shared_preload_libraries`.

## <a name="pgaudit-settings"></a>Ustawienia pgAudit

pgAudit umożliwia skonfigurowanie rejestrowania inspekcji sesji lub obiektu. [Rejestrowanie inspekcji sesji](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emituje szczegółowe dzienniki wykonanych instrukcji. [Rejestrowanie inspekcji obiektów](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) podlega inspekcji w zakresie określonych relacji. Istnieje możliwość skonfigurowania jednego lub obu typów rejestrowania. 

> [!NOTE]
> Ustawienia pgAudit są określone gloabally i nie można ich określić na poziomie bazy danych lub roli.

Po [zainstalowaniu pgAudit](#installing-pgaudit)można skonfigurować jego parametry, aby rozpocząć rejestrowanie. [Dokumentacja pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) zawiera definicje każdego z parametrów. Najpierw Przetestuj parametry i upewnij się, że otrzymujesz oczekiwane zachowanie.

> [!NOTE]
> Ustawienie `pgaudit.log_client` na włączone spowoduje przekierowanie dzienników do procesu klienta (na przykład PSQL) zamiast zapisywania do pliku. To ustawienie powinno być zwykle wyłączone.

> [!NOTE]
> `pgaudit.log_level` jest włączona tylko wtedy, gdy `pgaudit.log_client` jest włączona. Ponadto w Azure Portal istnieje obecnie usterka z `pgaudit.log_level`: pole kombi jest wyświetlane, co oznacza, że można wybrać wiele poziomów. Możliwe jest wybranie jednak tylko jednego poziomu. 

> [!NOTE]
> W Azure Database for PostgreSQL `pgaudit.log` nie można ustawić przy użyciu skrótu podpisywanie `-` (minus) zgodnie z opisem w dokumentacji pgAudit. Wszystkie wymagane klasy instrukcji (READ, WRITE itp.) powinny zostać określone indywidualnie.

### <a name="audit-log-format"></a>Format dziennika inspekcji
Każdy wpis inspekcji jest wskazywany przez `AUDIT:` blisko początku wiersza dziennika. Format reszty wpisu jest szczegółowo opisany w [dokumentacji pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Jeśli potrzebujesz innych pól do spełnienia wymagań dotyczących inspekcji, użyj parametru Postgres `log_line_prefix`. `log_line_prefix` jest ciągiem, który jest wyprowadzany na początku każdego wiersza dziennika Postgres. Na przykład następujące ustawienie `log_line_prefix` zawiera sygnaturę czasową, nazwę użytkownika, nazwa bazy danych i identyfikator procesu:

```
t=%m u=%u db=%d pid=[%p]:
```

Aby dowiedzieć się więcej na temat `log_line_prefix`, zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Wprowadzenie
Aby szybko rozpocząć pracę, ustaw `pgaudit.log` `WRITE`i Otwórz dzienniki, aby przejrzeć dane wyjściowe. 


## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej o rejestrowaniu w Azure Database for PostgreSQL](concepts-server-logs.md)
- Dowiedz się, jak ustawiać parametry za pomocą [Azure Portal](howto-configure-server-parameters-using-portal.md), interfejsu [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md)lub [API REST](/rest/api/postgresql/configurations/createorupdate).
