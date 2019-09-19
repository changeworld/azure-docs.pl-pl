---
title: Dzienniki serwera w Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób, w jaki Azure Database for PostgreSQL — pojedynczy serwer generuje zapytania i dzienniki błędów oraz sposób skonfigurowania przechowywania dzienników.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: b295ab442e70772a86d6699e1063c7a1c728f1a7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091116"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Dzienniki serwera w Azure Database for PostgreSQL — pojedynczy serwer
Usługa Azure Database for PostgreSQL generuje dzienniki zapytań i błędów. Dzienniki zapytań i błędów mogą służyć do identyfikowania, rozwiązywania problemów i naprawy błędów konfiguracji oraz nieoptymalnej wydajności. (Dostęp do dzienników transakcji nie jest uwzględniony). 

## <a name="configure-logging"></a>Konfigurowanie rejestrowania 
Rejestrowanie na serwerze można skonfigurować przy użyciu parametrów serwera rejestrowania. Na każdym nowym serwerze **log_checkpoints** i **log_connections** są domyślnie włączone. Istnieją dodatkowe parametry, które można dostosować, aby odpowiadały potrzebom rejestrowania: 

![Azure Database for PostgreSQL — parametry rejestrowania](./media/concepts-server-logs/log-parameters.png)

Aby uzyskać więcej informacji na temat tych parametrów, zobacz artykuł [raportowanie błędów i dokumentacja rejestrowania](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) w programie PostgreSQL. Aby dowiedzieć się, jak skonfigurować Azure Database for PostgreSQL parametry, zapoznaj się z [dokumentacją portalu](howto-configure-server-parameters-using-portal.md) lub [dokumentacją interfejsu wiersza polecenia](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Dostęp do dzienników serwera za poorednictwem portalu lub interfejsu wiersza polecenia
Jeśli włączono dzienniki, możesz uzyskać do nich dostęp z magazynu dzienników Azure Database for PostgreSQL przy użyciu [Azure Portal](howto-configure-server-logs-in-portal.md), [interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-using-cli.md)i interfejsów API REST platformy Azure. Pliki dziennika są zmieniane co 1 godzinę lub 100 MB, w zależności od tego, co zostanie osiągnięte wcześniej. Okres przechowywania dla tego magazynu dzienników można ustawić przy użyciu parametru  **okresu przechowywania\_\_dziennika**skojarzonego z serwerem. Wartość domyślna to 3 dni; Maksymalna wartość to 7 dni. Serwer musi mieć wystarczającą ilość przydzieloną magazyn do przechowywania plików dziennika. (Ten parametr przechowywania nie kontroluje dzienników diagnostycznych platformy Azure).


## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Azure Database for PostgreSQL jest zintegrowana z Azure Monitor dzienników diagnostycznych. Po włączeniu dzienników na serwerze PostgreSQL można wybrać opcję ich emisji do [dzienników Azure monitor](../azure-monitor/log-query/log-query-overview.md), Event Hubs lub Azure Storage. 

> [!IMPORTANT]
> Ta funkcja diagnostyczna dla dzienników serwera jest dostępna tylko w warstwach cenowych [](concepts-pricing-tiers.md)ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

Aby włączyć dzienniki diagnostyczne przy użyciu Azure Portal:

   1. W portalu przejdź do pozycji *Ustawienia diagnostyczne* w menu nawigacji serwera Postgres.
   2. Wybierz pozycję *Dodaj ustawienie diagnostyczne*.
   3. Nadaj nazwę temu ustawieniu. 
   4. Wybierz preferowaną lokalizację przechowania (konto magazynu, centrum zdarzeń, Analiza dzienników). 
   5. Wybierz odpowiednie typy danych.
   6. Zapisz ustawienie.

W poniższej tabeli opisano, co znajduje się w każdym dzienniku. W zależności od wybranego wyjściowego punktu końcowego pola zawarte i kolejność ich wyświetlania mogą się różnić. 

|**Pole** | **Opis** |
|---|---|
| TenantId | Identyfikator dzierżawy |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Sygnatura czasowa, gdy dziennik został zarejestrowany w formacie UTC |
| Type | Typ dziennika. zawsze `AzureDiagnostics` |
| SubscriptionId | Identyfikator GUID subskrypcji, do której należy serwer |
| Grupa zasobów | Nazwa grupy zasobów, do której należy serwer |
| ResourceProvider | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Identyfikator URI zasobu |
| Resource | Nazwa serwera |
| Kategoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Zmienna | Poziom rejestrowania, przykład: DZIENNIK, BŁĄD, POWIADOMIENIE |
| Message | Podstawowy komunikat dziennika | 
| Domain | Wersja serwera, przykład: Postgres-10 |
| Szczegóły | Dodatkowy komunikat dziennika (jeśli dotyczy) |
| ColumnName | Nazwa kolumny (jeśli dotyczy) |
| SchemaName | Nazwa schematu (jeśli dotyczy) |
| DatatypeName | Nazwa typu danych (jeśli dotyczy) |
| LogicalServerName | Nazwa serwera | 
| _ResourceId | Identyfikator URI zasobu |
| Prefiks | Prefiks wiersza dziennika |



## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o uzyskiwaniu dostępu do dzienników z [Azure Portal](howto-configure-server-logs-in-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-logs-using-cli.md).
- Dowiedz się więcej o [cenach Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
