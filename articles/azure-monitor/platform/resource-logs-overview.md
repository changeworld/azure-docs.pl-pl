---
title: Omówienie dzienników zasobów platformy Azure | Microsoft Docs
description: Poznaj obsługiwane usługi i schemat zdarzeń dla dzienników zasobów platformy Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 09/20/2019
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: bfcd2ded96c2679ba9177a760a8b11dc7d2c9a77
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262546"
---
# <a name="azure-resource-logs-overview"></a>Omówienie dzienników zasobów platformy Azure
Dzienniki zasobów platformy Azure [są](platform-logs-overview.md) wyemitowane przez zasoby platformy Azure, które opisują ich operacje wewnętrzne. Wszystkie dzienniki zasobów udostępniają wspólny schemat najwyższego poziomu z elastycznością dla każdej usługi do emisji unikatowych właściwości dla własnych zdarzeń.

> [!NOTE]
> Dzienniki zasobów były wcześniej znane jako dzienniki diagnostyczne.

## <a name="collecting-resource-logs"></a>Zbieranie dzienników zasobów
Dzienniki zasobów są generowane automatycznie przez obsługiwane zasoby platformy Azure, ale nie są zbierane, chyba że zostaną skonfigurowane przy użyciu [Ustawienia diagnostycznego](diagnostic-settings.md). Utwórz ustawienia diagnostyczne dla każdego zasobu platformy Azure, aby przekazywać dzienniki do następujących miejsc docelowych:

| Destination | Scenariusz |
|:---|:---|:---|
| [Obszar roboczy usługi Log Analytics](resource-logs-collect-storage.md) | Analizuj dzienniki z innymi danymi monitorowania i korzystaj z funkcji Azure Monitor, takich jak zapytania dzienników i alerty dzienników. |
| [Magazyn platformy Azure](archive-diagnostic-logs.md) | Archiwizuj dzienniki na potrzeby inspekcji lub tworzenia kopii zapasowych. |
| [Centrum zdarzeń](resource-logs-stream-event-hubs.md) | Przesyłanie strumieniowe dzienników do systemów rejestrowania i telemetrii innych firm.  |

## <a name="compute-resources"></a>Zasoby obliczeniowe
Dzienniki zasobów różnią się od dzienników na poziomie systemu operacyjnego gościa w zasobach obliczeniowych platformy Azure. Zasoby obliczeniowe wymagają od agenta zebrania dzienników i metryk z systemu operacyjnego gościa, w tym takich danych jak dzienniki zdarzeń, dziennik systemowy i liczniki wydajności. Użyj [rozszerzenia diagnostyki](agents-overview.md#azure-diagnostic-extension) , aby skierować dane dziennika z maszyn wirtualnych platformy Azure i [agenta log Analytics](agents-overview.md#log-analytics-agent) do zbierania dzienników i metryk z maszyn wirtualnych na platformie Azure, w innych chmurach i lokalnych w obszarze roboczym log Analytics. Aby uzyskać szczegółowe informacje, zobacz [źródła danych monitorowania dla Azure monitor](data-sources.md) .

## <a name="resource-logs-schema"></a>Schemat dzienników zasobów
Każda usługa platformy Azure ma swój własny schemat, gdy dzienniki zasobów są zapisywane w jednym z miejsc docelowych, ale wszystkie współdzielą schemat najwyższego poziomu w poniższej tabeli. Aby uzyskać linki do schematu dla każdej usługi, zobacz poniższe [schematy dotyczące usługi](#service-specific-schemas) . 

| Name | Wymagane/opcjonalne | Opis |
|---|---|---|
| time | Wymagane | Sygnatura czasowa zdarzenia (UTC). |
| resourceId | Wymagane | Identyfikator zasobu, który emituje zdarzenie. W przypadku usług dzierżawców jest to forma/tenants/tenant-ID/Providers/Provider-Name. |
| TenantId | Wymagane w przypadku dzienników dzierżawy | Identyfikator dzierżawy dzierżawy Active Directory, z którym jest powiązane to zdarzenie. Ta właściwość jest używana tylko w przypadku dzienników na poziomie dzierżawy, ale nie jest wyświetlana w dziennikach na poziomie zasobów. |
| operationName | Wymagane | Nazwa operacji reprezentowanej przez to zdarzenie. Jeśli zdarzenie reprezentuje operację RBAC, jest to nazwa operacji RBAC (np. Microsoft. Storage/storageAccounts/blobServices/obiekty blob/odczyt). Zwykle modelowane w formie Menedżer zasobów operacji, nawet jeśli nie są rzeczywiste udokumentowane operacje Menedżer zasobów (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Optional | Wersja interfejsu API skojarzona z operacją, jeśli operacjaname została wykonana przy użyciu interfejsu API (np. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Jeśli nie ma interfejsu API odpowiadającego tej operacji, wersja reprezentuje wersję tej operacji w przypadku, gdy właściwości skojarzone z operacją zmieniają się w przyszłości. |
| category | Wymagane | Kategoria dziennika zdarzenia. Kategoria to stopień szczegółowości, w którym można włączyć lub wyłączyć dzienniki dla określonego zasobu. Właściwości, które pojawiają się w obiekcie blob właściwości zdarzenia są takie same w określonej kategorii dziennika i typie zasobu. Typowe kategorie dzienników to "inspekcja" "działania" "wykonywanie" i "żądanie". |
| resultType | Optional | Stan zdarzenia. Typowe wartości to: rozpoczęte, w toku, zakończone powodzeniem, zakończone niepowodzeniem, aktywne i rozwiązane. |
| resultSignature | Optional | Stan podrzędny zdarzenia. Jeśli ta operacja odnosi się do wywołania interfejsu API REST, jest to kod stanu HTTP odpowiedniego wywołania REST. |
| resultDescription | Optional | Opis tekstu statycznego tej operacji, np. "Pobierz plik magazynu". |
| durationMs | Optional | Czas trwania operacji w milisekundach. |
| callerIpAddress | Optional | Adres IP wywołującego, jeśli operacja odnosi się do wywołania interfejsu API, które mogłoby pochodzić z jednostki z publicznie dostępnym adresem IP. |
| correlationId | Optional | Identyfikator GUID służący do grupowania razem z zestawem powiązanych zdarzeń. Zazwyczaj Jeśli dwa zdarzenia mają tę samą wartość OperationName, ale dwa różne stany (np. "Uruchomiono" i "powodzenie") współużytkują ten sam identyfikator korelacji. Może to również reprezentować inne relacje między zdarzeniami. |
| identity | Optional | Obiekt BLOB JSON, który opisuje tożsamość użytkownika lub aplikacji, która wykonała operację. Zwykle będzie to obejmować Token autoryzacji i oświadczeń/tokenu JWT z usługi Active Directory. |
| Poziom | Optional | Poziom ważności zdarzenia. Musi to być jeden z informacji, ostrzegawczy, błąd lub krytyczny. |
| location | Optional | Region zasobu emitującego zdarzenie, np. "Wschodnie stany USA" lub "Francja Południowa" |
| properties | Optional | Wszystkie rozszerzone właściwości powiązane z tą określoną kategorią zdarzeń. Wszystkie właściwości niestandardowe/unikatowe należy umieścić w tym "części B" schematu. |

## <a name="service-specific-schemas"></a>Schematy specyficzne dla usługi
Schemat dzienników diagnostycznych zasobów różni się w zależności od typu zasobu, który jest zdefiniowany przez `resourceId` właściwość, `category` i właściwości. Na poniższej liście przedstawiono wszystkie usługi platformy Azure, które obsługują dzienniki zasobów z linkami do usługi i schematu specyficznym dla kategorii, tam gdzie jest to możliwe.

| Usługa | Dokumentacja & schematu |
| --- | --- |
| Usługa Azure Active Directory | [Przegląd](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schemat dziennika inspekcji](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) i [schemat logowania](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management dzienników diagnostycznych](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Bramy aplikacji |[Rejestrowanie diagnostyczne dla Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics dla Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Rejestrowanie diagnostyczne Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL dzienników diagnostycznych](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL dzienników diagnostycznych](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Rejestrowanie diagnostyczne dla Cognitive Services platformy Azure](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [Dzienniki diagnostyczne platformy Azure dla usługi CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Rejestrowanie Azure Cosmos DB](../../cosmos-db/logging.md) |
| Fabryka danych | [Monitorowanie fabryk danych przy użyciu Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accessing diagnostic logs for Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics) |
| Data Lake Store |[Uzyskiwanie dostępu do dzienników diagnostycznych dla Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Dzienniki diagnostyczne usługi Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schemat jest niedostępny. |
| Azure Firewall | Schemat jest niedostępny. |
| IoT Hub | [Operacje IoT Hub](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Usługa Key Vault |[Funkcja rejestrowania usługi Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Moduł równoważenia obciążenia |[Analiza dzienników dotyczących usługi Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps — niestandardowy schemat śledzenia B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupy zabezpieczeń sieci |[Usługa Log Analytics dla sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Ochrona przed atakami DDOS | [Zarządzanie Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI — warstwa Dedykowana | [Rejestrowanie diagnostyczne dla Power BI Embedded na platformie Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Model danych dla Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Wyszukiwanie |[Włączanie i używanie Analiza ruchu wyszukiwania](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus dzienników diagnostycznych](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Rejestrowanie diagnostyczne Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Dzienniki diagnostyczne zadań](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager schematu dziennika](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Sieci wirtualne | Schemat jest niedostępny. |
| Bramy sieci wirtualnej | Schemat jest niedostępny. |

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat innych dzienników platformy Azure](platform-logs-overview.md) , których możesz użyć do monitorowania platformy Azure.
