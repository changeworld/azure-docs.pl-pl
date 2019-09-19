---
title: Inspekcja rejestrowania za pomocą pgAudit w Azure Database for PostgreSQL-pojedynczym serwerze
description: Pojęcia związane z rejestrowaniem inspekcji pgAudit w Azure Database for PostgreSQL-pojedynczym serwerze.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 8b4cbe309e310ef1fc384224c952a6f04385b1dd
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092263"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Rejestrowanie inspekcji w Azure Database for PostgreSQL — pojedynczy serwer

Rejestrowanie inspekcji działań bazy danych w ramach Azure Database for PostgreSQL-pojedynczego serwera jest dostępne za pomocą rozszerzenia inspekcji PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit zawiera szczegółowe informacje o rejestrowaniu sesji i/lub inspekcji obiektów.

> [!NOTE]
> pgAudit można włączyć tylko na serwerach Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

## <a name="usage-considerations"></a>Zagadnienia dotyczące użycia
Domyślnie instrukcje log pgAudit są emitowane wraz z regularnymi instrukcjami dzienników przy użyciu funkcji rejestrowania standardowego Postgres. W Azure Database for PostgreSQL pliki. log można pobrać za pomocą Azure Portal lub interfejsu wiersza polecenia. Maksymalny rozmiar magazynu dla kolekcji plików to 1 GB, a każdy plik jest dostępny przez maksymalnie siedem dni (wartość domyślna to trzy dni). Ta usługa jest krótkoterminową opcją magazynu.

Alternatywnie można skonfigurować wszystkie dzienniki, które mają być emitowane do usługi Dziennik diagnostyczny Azure Monitor. W przypadku włączenia rejestrowania diagnostycznego Azure Monitor dzienniki będą automatycznie wysyłane (w formacie JSON) do usługi Azure Storage, Event Hubs i/lub dzienników Azure Monitor, w zależności od wybranej opcji.

Włączenie pgAudit generuje dużą liczbę dzienników na serwerze, który ma wpływ na wydajność i magazyn dzienników. Zalecamy korzystanie z usługi dzienników diagnostycznych platformy Azure, która oferuje więcej opcji magazynu, a także funkcje analizy i alertów. Zalecamy wyłączenie standardowego rejestrowania, aby zmniejszyć wpływ dodatkowego rejestrowania na wydajność:

   1. Ustaw parametr `logging_collector` na off. 
   2. Uruchom ponownie serwer, aby zastosować tę zmianę.

Aby dowiedzieć się, jak skonfigurować rejestrowanie do usługi Azure Storage, Event Hubs lub dzienników Azure Monitor, odwiedź sekcję dzienniki diagnostyczne w [artykule Dzienniki serwera](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Instalowanie pgAudit

Aby zainstalować pgAudit, należy dołączyć go do udostępnionych bibliotek wstępnego ładowania serwera. Zmiana `shared_preload_libraries` parametru Postgres wymaga ponownego uruchomienia serwera. Parametry można zmienić za pomocą [Azure Portal](howto-configure-server-parameters-using-portal.md), interfejsu [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md)lub [API REST](/rest/api/postgresql/configurations/createorupdate).

Przy użyciu [Azure Portal](https://portal.azure.com):

   1. Wybierz serwer usługi Azure Database for PostgreSQL.
   2. Na pasku bocznym wybierz opcję **parametry serwera**.
   3. `shared_preload_libraries` Wyszukaj parametr.
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
> `pgaudit.log_level`jest włączona tylko wtedy `pgaudit.log_client` , gdy jest włączona. Ponadto w Azure Portal występuje obecnie usterka z `pgaudit.log_level`: pole kombi jest wyświetlane, co oznacza, że można wybrać wiele poziomów. Należy jednak wybrać tylko jeden poziom. 

> [!NOTE]
> W Azure Database for PostgreSQL `pgaudit.log` nie można ustawić `-` za pomocą skrótu podpisywania (minus), zgodnie z opisem w dokumentacji pgAudit. Wszystkie wymagane klasy instrukcji (odczyt, zapis itp.) powinny być określone indywidualnie.

### <a name="audit-log-format"></a>Format dziennika inspekcji
Każdy wpis inspekcji jest wskazywany `AUDIT:` przez blisko początku wiersza dziennika. Format reszty wpisu jest szczegółowo opisany w [dokumentacji pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Jeśli potrzebujesz innych pól do spełnienia wymagań dotyczących inspekcji, użyj parametru `log_line_prefix`Postgres. `log_line_prefix`jest ciągiem, który jest wyprowadzany na początku każdego wiersza dziennika Postgres. Na przykład następujące `log_line_prefix` ustawienie zawiera sygnaturę czasową, nazwę użytkownika, nazwa bazy danych i identyfikator procesu:

```
t=%m u=%u db=%d pid=[%p]:
```

Aby dowiedzieć się `log_line_prefix`więcej, odwiedź [dokumentację PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Wprowadzenie
Aby szybko rozpocząć pracę, ustaw `pgaudit.log` na `WRITE`, a następnie otwórz dzienniki, aby przejrzeć dane wyjściowe. 


## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej o rejestrowaniu w Azure Database for PostgreSQL](concepts-server-logs.md)
- Dowiedz się, jak ustawiać parametry za pomocą [Azure Portal](howto-configure-server-parameters-using-portal.md), interfejsu [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md)lub [API REST](/rest/api/postgresql/configurations/createorupdate).
