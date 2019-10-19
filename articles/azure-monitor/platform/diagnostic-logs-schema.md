---
title: Usługa Azure Diagnostic Logs rejestruje obsługiwane usługi i schematy
description: Poznaj obsługiwane usługi i schemat zdarzeń dla dzienników diagnostycznych platformy Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/11/2018
ms.openlocfilehash: f2d71972fa8acc930800a70193f688246ee7415c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555559"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Obsługiwane usługi, schematy i kategorie dla dzienników diagnostycznych platformy Azure

[Dzienniki diagnostyczne Azure monitor](../../azure-monitor/platform/resource-logs-overview.md) są wyemitowane przez usługi platformy Azure opisujące operacje tych usług lub zasobów. Wszystkie dzienniki diagnostyczne dostępne za pomocą Azure Monitor współdzielą wspólny schemat najwyższego poziomu, z elastycznością dla każdej usługi do emitowania unikatowych właściwości dla własnych zdarzeń.

Kombinacja typu zasobu (dostępna we właściwości `resourceId`) i `category` jednoznacznie identyfikują schemat. W tym artykule opisano schemat najwyższego poziomu dla dzienników diagnostycznych i linki do schematu dla każdej usługi.

## <a name="top-level-diagnostic-logs-schema"></a>Schemat dzienników diagnostycznych najwyższego poziomu

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

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Schematy dotyczące usługi dla dzienników diagnostycznych zasobów
Schemat dzienników diagnostycznych zasobów różni się w zależności od kategorii zasobów i dzienników. Ta lista zawiera wszystkie usługi, które udostępniają dostępne dzienniki diagnostyczne i linki do usługi i schematu specyficznego dla kategorii, gdzie są dostępne.

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
| Azure Data Explorer | [Dzienniki diagnostyczne usługi Azure Eksplorator danych](../../data-explorer/using-diagnostic-logs.md) |
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
| Kubernetes Service |[Rejestrowanie usługi Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
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

## <a name="supported-log-categories-per-resource-type"></a>Obsługiwane kategorie dzienników według typu zasobu
|Typ zasobu|Kategoria|Nazwa wyświetlana kategorii|
|---|---|---|
|Microsoft. AnalysisServices/serwery|Wyszukiwarce|Wyszukiwarce|
|Microsoft. AnalysisServices/serwery|Usługa|Usługa|
|Microsoft.ApiManagement/service|GatewayLogs|Dzienniki powiązane z bramą ApiManagement|
|Microsoft. Automation/automationAccounts|JobLogs|Dzienniki zadań|
|Microsoft. Automation/automationAccounts|JobStreams|Strumienie zadań|
|Microsoft. Automation/automationAccounts|DscNodeStatus|Stan węzła DSC|
|Microsoft. Batch/batchAccounts|ServiceLog|Dzienniki usług|
|Microsoft. CDN/profile/punkty końcowe|CoreAnalytics|Pobiera metryki punktu końcowego, np. przepustowość, ruch wychodzący itp.|
|Microsoft. ClassicNetwork/networksecuritygroups|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|
|Microsoft. CognitiveServices/konta|Inspekcja|Dzienniki inspekcji|
|Microsoft. CognitiveServices/konta|RequestResponse|Dzienniki żądań i odpowiedzi|
|Microsoft. ContainerService/managedClusters|polecenia — apiserver|Serwer interfejsu API Kubernetes|
|Microsoft. ContainerService/managedClusters|polecenia-Controller-Manager|Menedżer kontrolera Kubernetes|
|Microsoft. ContainerService/managedClusters|klaster — Autoskalowanie|Automatyczne skalowanie klastra Kubernetes|
|Microsoft. ContainerService/managedClusters|polecenia — Scheduler|Harmonogram Kubernetes|
|Microsoft. ContainerService/managedClusters|chroni|Element webhook uwierzytelniania|
|Microsoft. CustomerInsights/Hubs|AuditEvents|AuditEvents|
|Microsoft. DataFactory/fabryki|ActivityRuns|Dziennik uruchomienia działania potoku|
|Microsoft. DataFactory/fabryki|PipelineRuns|Dziennik przebiegów potoku|
|Microsoft. DataFactory/fabryki|TriggerRuns|Dziennik uruchomienia wyzwalacza|
|Microsoft. DataLakeAnalytics/konta|Inspekcja|Dzienniki inspekcji|
|Microsoft. DataLakeAnalytics/konta|Żądania|Dzienniki żądań|
|Microsoft. kontach datalakestore/konta|Inspekcja|Dzienniki inspekcji|
|Microsoft. kontach datalakestore/konta|Żądania|Dzienniki żądań|
|Microsoft. DBforMySQL/serwery|MySqlSlowLogs|Dzienniki serwera MySQL|
|Microsoft. DBforPostgreSQL/serwery|PostgreSQLLogs|Dzienniki serwera PostgreSQL|
|Microsoft. Devices/IotHubs|Połączenia|Połączenia|
|Microsoft. Devices/IotHubs|DeviceTelemetry|Dane telemetryczne urządzenia|
|Microsoft. Devices/IotHubs|C2DCommands|Polecenia C2D|
|Microsoft. Devices/IotHubs|DeviceIdentityOperations|Operacje tożsamości urządzeń|
|Microsoft. Devices/IotHubs|FileUploadOperations|Operacje przekazywania plików|
|Microsoft. Devices/IotHubs|Trasy|Trasy|
|Microsoft. Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft. Devices/IotHubs|C2DTwinOperations|C2D — operacje na Przędzce|
|Microsoft. Devices/IotHubs|TwinQueries|Zapytania bliźniaczye|
|Microsoft. Devices/IotHubs|JobsOperations|Operacje zadań|
|Microsoft. Devices/IotHubs|DirectMethods|Metody bezpośrednie|
|Microsoft. Devices/IotHubs|E2EDiagnostics|Diagnostyka E2E (wersja zapoznawcza)|
|Microsoft. Devices/IotHubs|Konfiguracje|Konfiguracje|
|Microsoft. Devices/provisioningServices|DeviceOperations|Operacje na urządzeniach|
|Microsoft. Devices/provisioningServices|Operacje serviceoperations|Operacje usługi|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft. EventHub/przestrzenie nazw|ArchiveLogs|Archiwizuj dzienniki|
|Microsoft. EventHub/przestrzenie nazw|OperationalLogs|Dzienniki operacyjne|
|Microsoft. EventHub/przestrzenie nazw|AutoScaleLogs|Automatyczne skalowanie dzienników|
|Microsoft. Insights/AutoscaleSettings|AutoscaleEvaluations|Obliczenia automatycznego skalowania|
|Microsoft. Insights/AutoscaleSettings|AutoscaleScaleActions|Akcje skalowania automatycznego skalowania|
|Microsoft. IoTSpaces/Graph|Ślad|Ślad|
|Microsoft. IoTSpaces/Graph|Działał|Działał|
|Microsoft. IoTSpaces/Graph|Inspekcja|Inspekcja|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|
|Microsoft. IoTSpaces/Graph|Ruch wychodzący|Ruch wychodzący|
|Microsoft./magazyny kluczy|AuditEvent|Dzienniki inspekcji|
|Microsoft. Logic/przepływy pracy|Element|Zdarzenia diagnostyczne środowiska uruchomieniowego przepływu pracy|
|Microsoft. Logic/integrationAccounts|IntegrationAccountTrackingEvents|Zdarzenia śledzenia konta integracji|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupEvent|Zdarzenie sieciowej grupy zabezpieczeń|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Licznik reguły sieciowej grupy zabezpieczeń|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Zdarzenia alertu Load Balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer stan kondycji sondy|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Powiadomienia o ochronie DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Dzienniki przepływów decyzji o łagodzeniu DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Raporty dotyczące środków zaradczych DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alerty ochrony maszyny wirtualnej|
|Microsoft. Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway dziennika dostępu|
|Microsoft. Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway dziennik wydajności|
|Microsoft. Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway dziennika zapory|
|Microsoft. Network/securegateways|AzureFirewallApplicationRule|Reguła aplikacji zapory platformy Azure|
|Microsoft. Network/securegateways|AzureFirewallNetworkRule|Reguła sieci zapory platformy Azure|
|Microsoft. Network/azurefirewalls|AzureFirewallApplicationRule|Reguła aplikacji zapory platformy Azure|
|Microsoft. Network/azurefirewalls|AzureFirewallNetworkRule|Reguła sieci zapory platformy Azure|
|Microsoft. Network/virtualNetworkGateways|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|
|Microsoft. Network/virtualNetworkGateways|TunnelDiagnosticLog|Dzienniki diagnostyczne tunelu|
|Microsoft. Network/virtualNetworkGateways|RouteDiagnosticLog|Kierowanie dzienników diagnostycznych|
|Microsoft. Network/virtualNetworkGateways|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|
|Microsoft. Network/virtualNetworkGateways|P2SDiagnosticLog|P2S dzienników diagnostycznych|
|Microsoft. Network/trafficManagerProfiles|ProbeHealthStatusEvents|Zdarzenie Traffic Manager wyników kondycji sondy|
|Microsoft. Network/expressRouteCircuits|PeeringRouteLog|Dzienniki tabel tras komunikacji równorzędnej|
|Microsoft. Network/usługi frontdoor|FrontdoorAccessLog|Dziennik dostępu usługa frontdoor|
|Microsoft. Network/usługi frontdoor|FrontdoorWebApplicationFirewallLog|Dziennik zapory aplikacji sieci Web usługa frontdoor|
|Microsoft. PowerBIDedicated/pojemności|Wyszukiwarce|Wyszukiwarce|
|Microsoft. RecoveryServices/magazyny|AzureBackupReport|Azure Backup dane raportowania|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryJobs|Zadania Azure Site Recovery|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryEvents|Zdarzenia Azure Site Recovery|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryReplicatedItems|Azure Site Recovery zreplikowane elementy|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryReplicationStats|Statystyka replikacji Azure Site Recovery|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery punktów odzyskiwania|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryReplicationDataUploadRate|Szybkość przekazywania danych replikacji Azure Site Recovery|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery zmienionych danych dysku chronionego|
|Microsoft. Search/searchServices|OperationLogs|Dzienniki operacji|
|Microsoft. ServiceBus/przestrzenie nazw|OperationalLogs|Dzienniki operacyjne|
|Microsoft. SQL/serwery/bazy danych|SQLInsights|Szczegółowe informacje SQL|
|Microsoft. SQL/serwery/bazy danych|AutomaticTuning|Automatyczne dostrajanie|
|Microsoft. SQL/serwery/bazy danych|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań|
|Microsoft. SQL/serwery/bazy danych|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań|
|Microsoft. SQL/serwery/bazy danych|Błędy|Błędy|
|Microsoft. SQL/serwery/bazy danych|DatabaseWaitStatistics|Statystyka oczekiwania bazy danych|
|Microsoft. SQL/serwery/bazy danych|Limity czasu|Limity czasu|
|Microsoft. SQL/serwery/bazy danych|Propagowan|Propagowan|
|Microsoft. SQL/serwery/bazy danych|Zakleszczenia|Zakleszczenia|
|Microsoft. SQL/serwery/bazy danych|Inspekcja|Dzienniki inspekcji|
|Microsoft. SQL/serwery/bazy danych|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|
|Microsoft. SQL/serwery/bazy danych|DmsWorkers|Pracownicy DMS|
|Microsoft. SQL/serwery/bazy danych|ExecRequests|Żądania exec|
|Microsoft. SQL/serwery/bazy danych|RequestSteps|Kroki żądania|
|Microsoft. SQL/serwery/bazy danych|Sqlrequests|Żądania SQL|
|Microsoft. SQL/serwery/bazy danych|Czeka|Czeka|
|Microsoft. SQL/managedInstances|ResourceUsageStats|Statystyka użycia zasobów|
|Microsoft. SQL/managedInstances|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|
|Microsoft. SQL/managedInstances/bazy danych|SQLInsights|Szczegółowe informacje SQL|
|Microsoft. SQL/managedInstances/bazy danych|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań|
|Microsoft. SQL/managedInstances/bazy danych|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań|
|Microsoft. SQL/managedInstances/bazy danych|Błędy|Błędy|
|Microsoft. StreamAnalytics/streamingjobs|Wykonanie|Wykonanie|
|Microsoft. StreamAnalytics/streamingjobs|Tworzenie|Tworzenie|
|Microsoft. Web/witryny|FunctionExecutionLogs|Dzienniki wykonywania funkcji|
|Microsoft. Web/Sites/miejsca|FunctionExecutionLogs|Dzienniki wykonywania funkcji|

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach diagnostycznych](../../azure-monitor/platform/resource-logs-overview.md)
* [Przesyłaj strumieniowo dzienniki diagnostyczne zasobów do **Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Zmienianie ustawień diagnostycznych zasobów przy użyciu interfejsu API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analizowanie dzienników z usługi Azure Storage za pomocą Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
