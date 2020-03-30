---
title: Dzienniki — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Zawiera opis konfiguracji rejestrowania, magazynu i analizy w usłudze Azure Database for PostgreSQL — single server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280497"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Loguje się w usłudze Azure Database for PostgreSQL — pojedynczy serwer
Usługa Azure Database for PostgreSQL umożliwia konfigurowanie i uzyskiwanie dostępu do standardowych dzienników postgres. Dzienniki mogą służyć do identyfikowania, rozwiązywania problemów i naprawy błędów konfiguracji i nieoptymalnej wydajności. Rejestrowanie informacji, które można skonfigurować i uzyskać dostęp, obejmuje błędy, informacje o kwerendach, rekordy autovacuum, połączenia i punkty kontrolne. (Dostęp do dzienników transakcji nie jest dostępny).

Rejestrowanie inspekcji jest udostępniane za pośrednictwem rozszerzenia Postgres, pgaudit. Aby dowiedzieć się więcej, odwiedź artykuł [dotyczące pojęć inspekcji.](concepts-audit.md)


## <a name="configure-logging"></a>Konfigurowanie rejestrowania 
Standardowe rejestrowanie postgresu można skonfigurować na serwerze przy użyciu parametrów serwera rejestrowania. Na każdym serwerze usługi Azure Database `log_checkpoints` `log_connections` for PostgreSQL i są domyślnie włączone. Istnieją dodatkowe parametry, które można dostosować do swoich potrzeb związanych z rejestrowaniem: 

![Usługa Azure Database for PostgreSQL — parametry rejestrowania](./media/concepts-server-logs/log-parameters.png)

Aby dowiedzieć się więcej o parametrach dziennika postgres, odwiedź [sekcje When To Log](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) i [What To Logs](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) w dokumentacji Postgres. Większość, ale nie wszystkie, parametry rejestrowania postgres są dostępne do skonfigurowania w usłudze Azure Database dla PostgreSQL.

Aby dowiedzieć się, jak skonfigurować parametry w usłudze Azure Database for PostgreSQL, zobacz [dokumentację portalu](howto-configure-server-parameters-using-portal.md) lub [dokumentację interfejsu wiersza polecenia.](howto-configure-server-parameters-using-cli.md) 

> [!NOTE]
> Konfigurowanie dużej liczby dzienników, na przykład rejestrowanie instrukcji, może zwiększyć obciążenie wydajnością. 

## <a name="access-log-files"></a>Dostęp do plików dziennika
Domyślnym formatem dziennika w usłudze Azure Database for PostgreSQL jest domena .log. Przykładowy wiersz z tego dziennika wygląda następująco:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Usługa Azure Database for PostgreSQL zapewnia krótkoterminową lokalizację magazynu dla plików dziennika. Nowy plik rozpoczyna się co 1 godzinę lub 100 MB, w zależności od tego, co nastąpi wcześniej. Dzienniki są dołączane do bieżącego pliku, ponieważ są emitowane z Postgres.  

Można ustawić okres przechowywania dla tego magazynu dziennika `log_retention_period` krótkoterminowego przy użyciu parametru. Wartość domyślna to 3 dni, a maksymalna to 7 dni. Krótkoterminowa lokalizacja magazynu może pomieścić do 1 GB plików dziennika. Po 1 GB najstarsze pliki, niezależnie od okresu przechowywania, zostaną usunięte, aby zrobić miejsce dla nowych dzienników. 

W przypadku długoterminowego przechowywania dzienników i analizy dzienników można pobrać pliki dziennika i przenieść je do usługi innej firmy. Można pobrać pliki za pomocą [witryny Azure portal](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md). Alternatywnie można skonfigurować ustawienia diagnostyczne usługi Azure Monitor, które automatycznie emitują dzienniki (w formacie JSON) do lokalizacji długoterminowych. Dowiedz się więcej o tej opcji w poniższej sekcji. 

Można zatrzymać generowanie plików .log, `logging_collector` ustawiając parametr na OFF. Wyłączenie generowania pliku dziennika jest zalecane, jeśli używasz ustawień diagnostycznych usługi Azure Monitor. Ta konfiguracja zmniejszy wpływ dodatkowego rejestrowania na wydajność.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Usługa Azure Database for PostgreSQL jest zintegrowana z ustawieniami diagnostycznymi usługi Azure Monitor. Ustawienia diagnostyczne umożliwiają wysyłanie dzienników postgres w formacie JSON do dzienników usługi Azure Monitor w celu analizy i alertów, centrów zdarzeń do przesyłania strumieniowego i usługi Azure Storage do archiwizacji. 

> [!IMPORTANT]
> Ta funkcja diagnostyczna dla dzienników serwera jest dostępna tylko w [warstwach cenowych](concepts-pricing-tiers.md)Ogólnego przeznaczenia i zoptymalizowanej pod kątem pamięci.


### <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych
Ustawienia diagnostyczne serwera Postgres można włączyć przy użyciu witryny Azure portal, interfejsu wiersza polecenia, interfejsu API REST i programu Powershell. Kategorią dziennika do wyboru jest **PostgreSQLLogs**. (Istnieją inne dzienniki, które można skonfigurować, jeśli używasz [Query Store](concepts-query-store.md).)

Aby włączyć dzienniki diagnostyczne przy użyciu witryny Azure portal:

   1. W portalu przejdź do *ustawień diagnostycznych* w menu nawigacyjnym serwera Postgres.
   2. Wybierz *pozycję Dodaj ustawienie diagnostyczne*.
   3. Nazwij to ustawienie. 
   4. Wybierz preferowany punkt końcowy (konto magazynu, centrum zdarzeń, analityka dzienników). 
   5. Wybierz typ dziennika **PostgreSQLLogs**.
   7. Zapisz swoje ustawienie.

Aby włączyć dzienniki diagnostyczne przy użyciu interfejsu API programu Powershell, CLI lub REST, odwiedź artykuł [o ustawieniach diagnostycznych.](../azure-monitor/platform/diagnostic-settings.md)

### <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

Sposób uzyskiwania dostępu do dzienników zależy od wybranego punktu końcowego. W przypadku usługi Azure Storage zobacz artykuł [o koncie magazynu dzienników.](../azure-monitor/platform/resource-logs-collect-storage.md) W przypadku centrów zdarzeń zobacz artykuł [dzienników platformy Azure strumienia.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

W przypadku dzienników usługi Azure Monitor dzienniki są wysyłane do wybranego obszaru roboczego. Dzienniki postgres używają trybu kolekcji **AzureDiagnostics,** dzięki czemu można je wyszukiwać z tabeli AzureDiagnostics. Pola w tabeli są opisane poniżej. Dowiedz się więcej o kwerendach i alertach w omówieniu [kwerend dzienników usługi Azure Monitor.](../azure-monitor/log-query/log-query-overview.md)

Poniżej przedstawiono kwerendy, które można wypróbować, aby rozpocząć. Alerty można konfigurować na podstawie zapytań.

Wyszukaj wszystkie dzienniki Postgres dla określonego serwera w ostatnim dniu
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

Wyszukiwanie wszystkich prób połączenia nielokacyjno-lokalnego
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
Powyższa kwerenda będzie wyświetlać wyniki w ciągu ostatnich 6 godzin dla dowolnego rejestrowania serwera Postgres w tym obszarze roboczym.

### <a name="log-format"></a>Format dziennika

W poniższej tabeli opisano pola dla typu **PostgreSQLLogs.** W zależności od wybranego punktu końcowego danych wyjściowych uwzględnione pola i kolejność ich pojawiania mogą się różnić. 

|**Pole** | **Opis** |
|---|---|
| TenantId | Identyfikator dzierżawy |
| SourceSystem | `Azure` |
| Czasgenerowany [UTC] | Sygnatura czasowa, gdy dziennik został zarejestrowany w utc |
| Typ | Typ dziennika. Zawsze`AzureDiagnostics` |
| SubscriptionId | Identyfikator GUID dla subskrypcji, do której należy serwer |
| ResourceGroup | Nazwa grupy zasobów, do której należy serwer |
| ResourceProvider | Nazwa dostawcy zasobów. Zawsze`MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Identyfikator URI zasobu |
| Zasób | Nazwa serwera |
| Kategoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Errorlevel | Poziom rejestrowania, przykład: LOG, ERROR, NOTICE |
| Komunikat | Podstawowy komunikat dziennika | 
| Domain | Wersja serwera, przykład: postgres-10 |
| Szczegół | Pomocniczy komunikat dziennika (jeśli dotyczy) |
| nazwa_kolumny | Nazwa kolumny (jeśli dotyczy) |
| SchemaName | Nazwa schematu (jeśli dotyczy) |
| Nazwa typu danych | Nazwa typu danych (jeśli dotyczy) |
| Nazwa serwera logicznego | Nazwa serwera | 
| _ResourceId | Identyfikator URI zasobu |
| Prefiks | Prefiks wiersza dziennika |


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o uzyskiwaniu dostępu do dzienników z [witryny Azure portal](howto-configure-server-logs-in-portal.md) lub [interfejsu wiersza polecenia platformy Azure.](howto-configure-server-logs-using-cli.md)
- Dowiedz się więcej o [cenach usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit.md)
