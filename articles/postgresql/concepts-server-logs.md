---
title: Dzienniki serwera w bazie danych Azure database for PostgreSQL
description: W tym artykule opisano, jak usługi Azure Database dla PostgreSQL generuje dzienników zapytań i błąd i jak rejestrować przechowywania jest skonfigurowany.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: 99deef907818ffdb1ce858c8e988e26cbd53a1a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871537"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Dzienników serwera w usłudze Azure Database for PostgreSQL 
Usługa Azure Database for postgresql w warstwie generuje zapytania i błąd dzienników. Dziennikami zapytań, błędów i może służyć do identyfikowania, rozwiązywanie problemów i naprawić błędy konfiguracji i nieoptymalne wydajności. (Dostęp do dzienników transakcji nie jest dołączony). 

## <a name="configure-logging"></a>Konfigurowanie rejestrowania 
Można skonfigurować rejestrowanie na serwerze przy użyciu parametrów serwera rejestrowania. Na każdym serwerze, nowe **log_checkpoints** i **log_connections** są domyślnie włączone. Dostępne są dodatkowe parametry, które można dostosować do własnych potrzeb rejestrowania: 

![Azure Database for PostgreSQL — parametry rejestrowania](./media/concepts-server-logs/log-parameters.png)

Aby uzyskać więcej informacji na temat tych parametrów, zobacz PostgreSQL [raportowanie błędów i rejestrowanie](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) dokumentacji. Aby dowiedzieć się, jak skonfigurować — Azure Database for postgresql w warstwie parametrów, zobacz [dokumentacja portalu](howto-configure-server-parameters-using-portal.md) lub [dokumentacji interfejsu wiersza polecenia](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Dzienniki serwera dostępowego za pośrednictwem portalu lub interfejsu wiersza polecenia
Po włączeniu dzienniki użytkownik może uzyskiwać do nich dostęp z usługi Azure Database for PostgreSQL dziennika magazynu przy użyciu [witryny Azure portal](howto-configure-server-logs-in-portal.md), [wiersza polecenia platformy Azure](howto-configure-server-logs-using-cli.md)i interfejsów API REST platformy Azure. Obróć pliki dziennika, co godzinę lub rozmiarze 100MB, osiągnięta jako pierwsza. Możesz ustawić okres przechowywania dla tego dziennika magazynu przy użyciu **dziennika\_przechowywania\_okres** parametru skojarzonego z serwerem. Wartość domyślna to 3 dni; wartość maksymalna to 7 dni. Serwer musi mieć wystarczające przydzielonej pamięci do przechowywania plików dziennika. (Ten parametr przechowywania nie kontroluje dzienniki diagnostyczne platformy Azure).


## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Usługa Azure Database for PostgreSQL jest zintegrowany z dzienników diagnostycznych usługi Azure Monitor. Po włączeniu dzienniki na Twoim serwerze PostgreSQL, możesz je emitowany do [dzienniki usługi Azure Monitor](../azure-monitor/log-query/log-query-overview.md), usługa Event Hubs lub usługi Azure Storage. Aby dowiedzieć się więcej na temat włączania dzienników diagnostycznych, zobacz sekcję porad [dokumentacja dzienników diagnostycznych](../azure-monitor/platform/diagnostic-logs-overview.md). 

> [!IMPORTANT]
> Ta funkcja diagnostyki dla dzienników serwera jest dostępna tylko w ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci [warstw cenowych](concepts-pricing-tiers.md).

W poniższej tabeli opisano, co znajduje się w każdym dzienniku. W zależności od wyjściowy punkt końcowy możesz wybrać, zakres i kolejność, w jakiej są wyświetlane, mogą się różnić. 

|**Pole** | **Opis** |
|---|---|
| TenantId | Identyfikator dzierżawy |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Sygnatura czasowa podczas rejestrowania w formacie UTC |
| Type | Typ dziennika. zawsze `AzureDiagnostics` |
| SubscriptionId | Identyfikator GUID dla subskrypcji, do której należy serwer |
| ResourceGroup | Nazwa grupy zasobów, do której należy serwer |
| ResourceProvider | Nazwa dostawcy zasobów. zawsze `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Identyfikator URI zasobu |
| Resource | Nazwa serwera |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Zmienna środowiskowa errorLevel | Przykład poziomu rejestrowania: DZIENNIK BŁĘDÓW, POWIADOMIENIA |
| Komunikat | Komunikat w dzienniku podstawowego | 
| Domain | Wersja serwera, na przykład: postgres 10 |
| Szczegół | Komunikat w dzienniku dodatkowej (jeśli dotyczy) |
| ColumnName | Nazwa kolumny (jeśli dotyczy) |
| SchemaName | Nazwa schematu (jeśli dotyczy) |
| DatatypeName | Nazwa typu danych (jeśli dotyczy) |
| LogicalServerName | Nazwa serwera | 
| _ResourceId | Identyfikator URI zasobu |

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej na temat uzyskiwania dostępu do dzienników z [witryny Azure portal](howto-configure-server-logs-in-portal.md) lub [wiersza polecenia platformy Azure](howto-configure-server-logs-using-cli.md).
- Dowiedz się więcej o [cennika usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
