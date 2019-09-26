---
title: Usługa Azure Diagnostic Logs rejestruje obsługiwane usługi i schematy
description: Poznaj obsługiwane usługi i schemat zdarzeń dla dzienników diagnostycznych platformy Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: fdcfcbaf99d48a345d2be4da297be1c9139da15c
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71308109"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Obsługiwane usługi, schematy i kategorie dla dzienników diagnostycznych platformy Azure

[Dzienniki diagnostyczne Azure monitor](../../azure-monitor/platform/resource-logs-overview.md) są wyemitowane przez usługi platformy Azure opisujące operacje tych usług lub zasobów. Wszystkie dzienniki diagnostyczne dostępne za pomocą Azure Monitor współdzielą wspólny schemat najwyższego poziomu, z elastycznością dla każdej usługi do emitowania unikatowych właściwości dla własnych zdarzeń.

Kombinacja typu zasobu (dostępnego we `resourceId` właściwości) `category` i unikatowo identyfikujący schemat. W tym artykule opisano schemat najwyższego poziomu dla dzienników diagnostycznych i linki do schematu dla każdej usługi.

## <a name="top-level-diagnostic-logs-schema"></a>Schemat dzienników diagnostycznych najwyższego poziomu

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

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Schematy dotyczące usługi dla dzienników diagnostycznych zasobów
Schemat dzienników diagnostycznych zasobów różni się w zależności od kategorii zasobów i dzienników. Ta lista zawiera wszystkie usługi, które udostępniają dostępne dzienniki diagnostyczne i linki do usługi i schematu specyficznego dla kategorii, gdzie są dostępne.

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
| Kubernetes Service |[Rejestrowanie usługi Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
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

## <a name="supported-log-categories-per-resource-type"></a>Obsługiwane kategorie dzienników według typu zasobu
|Typ zasobu|Category|Nazwa wyświetlana kategorii|
|---|---|---|
|Microsoft.AnalysisServices/servers|Aparat|Aparat|
|Microsoft.AnalysisServices/servers|Usługa|Usługa|
|Microsoft.ApiManagement/service|GatewayLogs|Dzienniki powiązane z bramą ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Dzienniki zadań|
|Microsoft.Automation/automationAccounts|JobStreams|Strumienie zadań|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stan węzła DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Dzienniki usług|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Pobiera metryki punktu końcowego, np. przepustowość, ruch wychodzący itp.|
|Microsoft.ClassicNetwork/networksecuritygroups|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|
|Microsoft.CognitiveServices/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.CognitiveServices/accounts|RequestResponse|Dzienniki żądań i odpowiedzi|
|Microsoft.ContainerService/managedClusters|polecenia — apiserver|Serwer interfejsu API usługi Kubernetes|
|Microsoft.ContainerService/managedClusters|polecenia-Controller-Manager|Menedżer kontrolera usługi Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Moduł automatycznego skalowania klastra usługi Kubernetes|
|Microsoft.ContainerService/managedClusters|polecenia — Scheduler|Harmonogram usługi Kubernetes|
|Microsoft.ContainerService/managedClusters|chroni|Element webhook uwierzytelniania|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Dziennik uruchomienia działania potoku|
|Microsoft.DataFactory/factories|PipelineRuns|Dziennik przebiegów potoku|
|Microsoft.DataFactory/factories|TriggerRuns|Dziennik uruchomienia wyzwalacza|
|Microsoft.DataLakeAnalytics/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.DataLakeAnalytics/accounts|Żądania|Dzienniki żądań|
|Microsoft.DataLakeStore/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.DataLakeStore/accounts|Żądania|Dzienniki żądań|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Dzienniki serwera MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Dzienniki serwera PostgreSQL|
|Microsoft.Devices/IotHubs|Połączenia|Połączenia|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Dane telemetryczne urządzenia|
|Microsoft.Devices/IotHubs|C2DCommands|Polecenia C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operacje tożsamości urządzeń|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operacje przekazywania plików|
|Microsoft.Devices/IotHubs|Trasy|Trasy|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D — operacje na Przędzce|
|Microsoft.Devices/IotHubs|twinQueries|Zapytania bliźniaczye|
|Microsoft.Devices/IotHubs|JobsOperations|Operacje zadań|
|Microsoft.Devices/IotHubs|DirectMethods|Metody bezpośrednie|
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnostyka E2E (wersja zapoznawcza)|
|Microsoft.Devices/IotHubs|Konfiguracje|Konfiguracje|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operacje urządzeń|
|Microsoft.Devices/provisioningServices|Operacje serviceoperations|Operacje usługi|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Archiwizuj dzienniki|
|Microsoft.EventHub/namespaces|OperationalLogs|Dzienniki operacyjne|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Automatyczne skalowanie dzienników|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Autoskalowanie ocen|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Autoskalowanie akcji skalowania|
|Microsoft.IoTSpaces/Graph|Ślad|Ślad|
|Microsoft.IoTSpaces/Graph|Działa|Działa|
|Microsoft.IoTSpaces/Graph|Inspekcja|Inspekcja|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Ruch przychodzący|Ruch przychodzący|
|Microsoft.IoTSpaces/Graph|Ruch wychodzący|Ruch wychodzący|
|Microsoft.KeyVault/vaults|AuditEvent|Dzienniki inspekcji|
|Microsoft.Logic/workflows|WorkflowRuntime|Zdarzenia diagnostyczne środowiska uruchomieniowego przepływu pracy|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Zdarzenia śledzenia konta integracji|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Zdarzenie sieciowej grupy zabezpieczeń|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Licznik reguły sieciowej grupy zabezpieczeń|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Zdarzenia alertu Load Balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer stan kondycji sondy|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Powiadomienia o ochronie DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Dzienniki przepływów decyzji o łagodzeniu DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Raporty dotyczące środków zaradczych DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alerty ochrony maszyny wirtualnej|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway dziennika dostępu|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway dziennik wydajności|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway dziennika zapory|
|Microsoft. Network/securegateways|AzureFirewallApplicationRule|Reguła aplikacji zapory platformy Azure|
|Microsoft. Network/securegateways|AzureFirewallNetworkRule|Reguła sieci zapory platformy Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Reguła aplikacji zapory platformy Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Reguła sieci zapory platformy Azure|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Dzienniki diagnostyczne tunelu|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Kierowanie dzienników diagnostycznych|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S dzienników diagnostycznych|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Zdarzenie Traffic Manager wyników kondycji sondy|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Dzienniki tabel tras komunikacji równorzędnej|
|Microsoft. Network/usługi frontdoor|FrontdoorAccessLog|Dziennik dostępu usługa frontdoor|
|Microsoft. Network/usługi frontdoor|FrontdoorWebApplicationFirewallLog|Dziennik zapory aplikacji sieci Web usługa frontdoor|
|Microsoft.PowerBIDedicated/capacities|Aparat|Aparat|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dane raportowania usługi Azure Backup|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery Jobs|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Zdarzenia usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Zreplikowane elementy usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Stany replikacji usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Punkty odzyskiwania usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Szybkość przekazywania danych replikacji Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery zmienionych danych dysku chronionego|
|Microsoft.Search/searchServices|OperationLogs|Dzienniki operacji|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Dzienniki operacyjne|
|Microsoft.Sql/servers/databases|SQLInsights|Szczegółowe informacje SQL|
|Microsoft.Sql/servers/databases|AutomaticTuning|Dostrajanie automatyczne|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań|
|Microsoft.Sql/servers/databases|Błędy|Błędy|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Statystyka oczekiwania bazy danych|
|Microsoft.Sql/servers/databases|Limity czasu|Limity czasu|
|Microsoft.Sql/servers/databases|Propagowan|Propagowan|
|Microsoft.Sql/servers/databases|Zakleszczenia|Zakleszczenia|
|Microsoft.Sql/servers/databases|Inspekcja|Dzienniki inspekcji|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Pracownicy DMS|
|Microsoft.Sql/servers/databases|ExecRequests|Żądania exec|
|Microsoft.Sql/servers/databases|RequestSteps|Kroki żądania|
|Microsoft.Sql/servers/databases|Sqlrequests|Żądania SQL|
|Microsoft.Sql/servers/databases|Czeka|Czeka|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Statystyka użycia zasobów|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|Szczegółowe informacje SQL|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań|
|Microsoft.Sql/managedInstances/databases|Błędy|Błędy|
|Microsoft.StreamAnalytics/streamingjobs|Wykonanie|Wykonanie|
|Microsoft.StreamAnalytics/streamingjobs|Tworzenie|Tworzenie|
|Microsoft. Web/witryny|FunctionExecutionLogs|Dzienniki wykonywania funkcji|
|Microsoft. Web/Sites/miejsca|FunctionExecutionLogs|Dzienniki wykonywania funkcji|

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach diagnostycznych](../../azure-monitor/platform/resource-logs-overview.md)
* [Stream dzienników diagnostycznych zasobów do **usługi Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Zmień ustawienia diagnostyczne zasobów przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analizowanie dzienników z usługi Azure storage za pomocą usługi Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
