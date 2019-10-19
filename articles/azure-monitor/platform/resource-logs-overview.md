---
title: Omówienie dzienników zasobów platformy Azure | Microsoft Docs
description: Poznaj obsługiwane usługi i schemat zdarzeń dla dzienników zasobów platformy Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 09/20/2019
ms.openlocfilehash: a418e3d1a59379284422d1d24c1457ab61d84a4c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551750"
---
# <a name="azure-resource-logs-overview"></a>Omówienie dzienników zasobów platformy Azure
Dzienniki zasobów platformy Azure [są](platform-logs-overview.md) wyemitowane przez zasoby platformy Azure, które opisują ich operacje wewnętrzne. Wszystkie dzienniki zasobów udostępniają wspólny schemat najwyższego poziomu z elastycznością dla każdej usługi do emisji unikatowych właściwości dla własnych zdarzeń.

> [!NOTE]
> Dzienniki zasobów były wcześniej znane jako dzienniki diagnostyczne.

## <a name="collecting-resource-logs"></a>Zbieranie dzienników zasobów
Dzienniki zasobów są generowane automatycznie przez obsługiwane zasoby platformy Azure, ale nie są zbierane, chyba że zostaną skonfigurowane przy użyciu [Ustawienia diagnostycznego](diagnostic-settings.md). Utwórz ustawienia diagnostyczne dla każdego zasobu platformy Azure, aby przekazywać dzienniki do następujących miejsc docelowych:

| Cel | Scenariusz |
|:---|:---|:---|
| [Obszar roboczy usługi Log Analytics](resource-logs-collect-storage.md) | Analizuj dzienniki z innymi danymi monitorowania i korzystaj z funkcji Azure Monitor, takich jak zapytania dzienników i alerty dzienników. |
| [Magazyn platformy Azure](archive-diagnostic-logs.md) | Archiwizuj dzienniki na potrzeby inspekcji lub tworzenia kopii zapasowych. |
| [Centrum zdarzeń](resource-logs-stream-event-hubs.md) | Przesyłanie strumieniowe dzienników do systemów rejestrowania i telemetrii innych firm.  |

## <a name="compute-resources"></a>Zasoby obliczeniowe
Dzienniki zasobów różnią się od dzienników na poziomie systemu operacyjnego gościa w zasobach obliczeniowych platformy Azure. Zasoby obliczeniowe wymagają od agenta zebrania dzienników i metryk z systemu operacyjnego gościa, w tym takich danych jak dzienniki zdarzeń, dziennik systemowy i liczniki wydajności. Użyj [rozszerzenia diagnostyki](agents-overview.md#azure-diagnostic-extension) , aby skierować dane dziennika z maszyn wirtualnych platformy Azure i [agenta log Analytics](agents-overview.md#log-analytics-agent) do zbierania dzienników i metryk z maszyn wirtualnych na platformie Azure, w innych chmurach i lokalnych w obszarze roboczym log Analytics. Aby uzyskać szczegółowe informacje, zobacz [źródła danych monitorowania dla Azure monitor](data-sources.md) .

## <a name="resource-logs-schema"></a>Schemat dzienników zasobów
Każda usługa platformy Azure ma swój własny schemat, gdy dzienniki zasobów są zapisywane w jednym z miejsc docelowych, ale wszystkie współdzielą schemat najwyższego poziomu w poniższej tabeli. Aby uzyskać linki do schematu dla każdej usługi, zobacz poniższe [schematy dotyczące usługi](#service-specific-schemas) . 

| Nazwa | Wymagane/opcjonalne | Opis |
|---|---|---|
| time | Wymagane | Sygnatura czasowa zdarzenia (UTC). |
| resourceId | Wymagane | Identyfikator zasobu, który emituje zdarzenie. W przypadku usług dzierżawców jest to forma/tenants/tenant-ID/Providers/Provider-Name. |
| tenantId | Wymagane w przypadku dzienników dzierżawy | Identyfikator dzierżawy dzierżawy Active Directory, z którym jest powiązane to zdarzenie. Ta właściwość jest używana tylko w przypadku dzienników na poziomie dzierżawy, ale nie jest wyświetlana w dziennikach na poziomie zasobów. |
| operationName | Wymagane | Nazwa operacji reprezentowanej przez to zdarzenie. Jeśli zdarzenie reprezentuje operację RBAC, jest to nazwa operacji RBAC (np. Microsoft. Storage/storageAccounts/blobServices/obiekty blob/odczyt). Zwykle modeluje się w postaci operacji Menedżer zasobów, nawet jeśli nie są rzeczywiste udokumentowane operacje Menedżer zasobów (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Opcjonalne | Wersja interfejsu API skojarzona z operacją, jeśli operacjaname została wykonana przy użyciu interfejsu API (np. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Jeśli nie ma interfejsu API odpowiadającego tej operacji, wersja reprezentuje wersję tej operacji w przypadku, gdy właściwości skojarzone z operacją zmieniają się w przyszłości. |
| category | Wymagane | Kategoria dziennika zdarzenia. Kategoria to stopień szczegółowości, w którym można włączyć lub wyłączyć dzienniki dla określonego zasobu. Właściwości, które pojawiają się w obiekcie blob właściwości zdarzenia są takie same w określonej kategorii dziennika i typie zasobu. Typowe kategorie dzienników to "inspekcja" "działania" "wykonywanie" i "żądanie". |
| resultType | Opcjonalne | Stan zdarzenia. Typowe wartości to: rozpoczęte, w toku, zakończone powodzeniem, zakończone niepowodzeniem, aktywne i rozwiązane. |
| resultSignature | Opcjonalne | Stan podrzędny zdarzenia. Jeśli ta operacja odnosi się do wywołania interfejsu API REST, jest to kod stanu HTTP odpowiedniego wywołania REST. |
| resultDescription | Opcjonalne | Opis tekstu statycznego tej operacji, np. "Pobierz plik magazynu". |
| durationMs | Opcjonalne | Czas trwania operacji w milisekundach. |
| callerIpAddress | Opcjonalne | Adres IP wywołującego, jeśli operacja odnosi się do wywołania interfejsu API, które mogłoby pochodzić z jednostki z publicznie dostępnym adresem IP. |
| correlationId | Opcjonalne | Identyfikator GUID służący do grupowania razem z zestawem powiązanych zdarzeń. Zazwyczaj Jeśli dwa zdarzenia mają tę samą wartość OperationName, ale dwa różne stany (np. "Uruchomiono" i "powodzenie") współużytkują ten sam identyfikator korelacji. Może to również reprezentować inne relacje między zdarzeniami. |
| tożsamość | Opcjonalne | Obiekt BLOB JSON, który opisuje tożsamość użytkownika lub aplikacji, która wykonała operację. Zwykle będzie to obejmować Token autoryzacji i oświadczeń/tokenu JWT z usługi Active Directory. |
| Poziom | Opcjonalne | Poziom ważności zdarzenia. Musi to być jeden z informacji, ostrzegawczy, błąd lub krytyczny. |
| location | Opcjonalne | Region zasobu emitującego zdarzenie, np. "Wschodnie stany USA" lub "Francja Południowa" |
| properties | Opcjonalne | Wszystkie rozszerzone właściwości powiązane z tą określoną kategorią zdarzeń. Wszystkie właściwości niestandardowe/unikatowe należy umieścić w tym "części B" schematu. |

## <a name="service-specific-schemas"></a>Schematy specyficzne dla usługi
Schemat dzienników diagnostycznych zasobów zależy od typu zasobu, który jest zdefiniowany przez właściwość `resourceId`) i właściwości `category`. Na poniższej liście przedstawiono wszystkie usługi platformy Azure, które obsługują dzienniki zasobów z linkami do usługi i schematu specyficznym dla kategorii, tam gdzie jest to możliwe.

| Usługa | Dokumentacja & schematu |
| --- | --- |
| Usługa Active Directory systemu Azure | [Przegląd](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schemat dziennika inspekcji](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) i [schemat logowania](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Usługi analityczne | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management dzienników diagnostycznych](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Bramy Application Gateway |[Rejestrowanie diagnostyczne dla Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics dla Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Rejestrowanie diagnostyczne Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL dzienników diagnostycznych](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL dzienników diagnostycznych](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Usługi Cognitive Services | [Rejestrowanie diagnostyczne dla Cognitive Services platformy Azure](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [Dzienniki diagnostyczne platformy Azure dla usługi CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Rejestrowanie Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorowanie fabryk danych przy użyciu Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accessing diagnostic logs for Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics) |
| Data Lake Store |[Uzyskiwanie dostępu do dzienników diagnostycznych dla Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Centra zdarzeń |[Dzienniki diagnostyczne usługi Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Ekspresowa trasa | Schemat jest niedostępny. |
| Azure Firewall | Schemat jest niedostępny. |
| IoT Hub | [Operacje IoT Hub](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Magazyn kluczy |[Funkcja rejestrowania usługi Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Równoważenie obciążenia |[Analiza dzienników dotyczących usługi Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Aplikacje logiki |[Logic Apps — niestandardowy schemat śledzenia B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Sieciowe grupy zabezpieczeń |[Usługa Log Analytics dla sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Ochrona przed atakami DDOS | [Zarządzanie Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI — warstwa Dedykowana | [Rejestrowanie diagnostyczne dla Power BI Embedded na platformie Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Usługi odzyskiwania | [Model danych dla Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Włączanie i używanie Analiza ruchu wyszukiwania](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus dzienników diagnostycznych](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Baza danych SQL | [Rejestrowanie diagnostyczne Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Analiza strumienia |[Dzienniki diagnostyczne zadań](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager schematu dziennika](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Sieci wirtualne | Schemat jest niedostępny. |
| Bramy sieci wirtualnej | Schemat jest niedostępny. |

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat innych dzienników platformy Azure](platform-logs-overview.md) , których możesz użyć do monitorowania platformy Azure.
