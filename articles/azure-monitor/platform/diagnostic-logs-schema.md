---
title: Dzienniki diagnostyczne platformy Azure obsługiwane usługi i schematy
description: Opis obsługiwanych schematu usług i zdarzeń z systemem dzienniki diagnostyczne platformy Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: 21eec5ee2fef185a927f6a416732303765e02b1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65789319"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Obsługiwane usługi, schematów i kategorie dzienników diagnostycznych platformy Azure

[Dzienniki diagnostyczne platformy Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md) to Dzienniki tworzone przez usługi platformy Azure, które opisują działania tych usług lub zasobów. Wszystkie dzienniki diagnostyczne, dostępne za pośrednictwem usługi Azure Monitor udostępniać wspólny schemat najwyższego poziomu, elastyczność dla każdej usługi emitować unikatowe właściwości dla ich własnych zdarzeń.

Połączenie typu zasobu (dostępne w `resourceId` właściwości) i `category` jednoznacznie zidentyfikować schematu. W tym artykule opisano schemat najwyższego poziomu dla dzienników diagnostycznych i linki do schematu dla każdej usługi.

## <a name="top-level-diagnostic-logs-schema"></a>Schemat najwyższego poziomu dzienników diagnostycznych

| Name (Nazwa) | Wymagane/opcjonalne | Opis |
|---|---|---|
| time | Wymagane | Sygnatura czasowa (UTC) zdarzenia. |
| resourceId | Wymagane | Identyfikator zasobu zasobu, do którego emitowane zdarzenia. W przypadku usług dzierżawy jest to /tenants/tenant-id/providers/provider-name formularza. |
| tenantId | Wymagane dla dzienników dzierżawy | Identyfikator dzierżawy dzierżawy usługi Active Directory, która to zdarzenie jest powiązane. Ta właściwość jest używana tylko dla dzienników na poziomie dzierżawy, nie ma w dziennikach poziom zasobów. |
| operationName | Wymagane | Nazwa operacji reprezentowany przez to zdarzenie. Zdarzenie reprezentuje operację RBAC, to czy nazwy operacji RBAC (np.) Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Zazwyczaj w modelu w postaci operacji usługi Resource Manager, nawet jeśli nie są udokumentowane operacje usługi Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Optional (Opcjonalność) | Wersja interfejsu api skojarzone z operacją, jeśli operationName została wykonana przy użyciu interfejsu API (np.) `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Jeśli nie ma żadnych interfejsów API, który odnosi się do tej operacji, wersja reprezentuje wersję tej operacji w przypadku, gdy właściwości skojarzone z operacją zmiany w przyszłości. |
| category | Wymagane | Kategoria dziennika zdarzeń. Kategoria jest stopień szczegółowości, w którym można włączać lub wyłączać dzienniki dla określonego zasobu. Właściwości, które są wyświetlane w obiekcie blob właściwości zdarzenia są takie same, w ramach typu dziennika w określonej kategorii i zasobów. Rejestruj typowe kategorie są "do inspekcji" "działa" "Wykonanie" i "Żądania". |
| resultType | Optional (Opcjonalność) | Stan zdarzenia. Typowe wartości to uruchomiona, w toku, zakończone powodzeniem, nie powiodło się, aktywny i rozwiązany. |
| resultSignature | Optional (Opcjonalność) | Stan podrzędny zdarzenia. Ta operacja odpowiada wywołaniu interfejsu API REST, to kod stanu HTTP odpowiedniego wywołania REST. |
| resultDescription | Optional (Opcjonalność) | Opis tekst statyczny tej operacji, np. "Pobierz plik magazynu". |
| durationMs | Optional (Opcjonalność) | Czas trwania działania (w milisekundach). |
| callerIpAddress | Optional (Opcjonalność) | Adres IP obiektu wywołującego, gdy operacja odpowiada wywołaniu interfejsu API, nadchodzące z jednostki z publicznie dostępnego adresu IP. |
| correlationId | Optional (Opcjonalność) | Identyfikator GUID służący do grupowania zestawu powiązanych zdarzeń. Zwykle Jeśli dwa zdarzenia mają ten sam operationName, ale dwa różne stany (np.) "Wprowadzenie" i "Sukces"), współużytkują one ten sam identyfikator korelacji. To może również reprezentować relacje między zdarzeniami. |
| identity | Optional (Opcjonalność) | Obiekt blob JSON, który opisuje tożsamość użytkownika lub aplikacji, który wykonał operację. Zwykle dotyczy to autoryzację i oświadczenia / token JWT z usługi active directory. |
| Poziom | Optional (Opcjonalność) | Poziom ważności zdarzenia. Musi być jednym z informacyjny "," ostrzeżenie "," błąd "lub" krytyczne. |
| location | Optional (Opcjonalność) | Region zasobu wysyłających zdarzenia, np. "Wschodnie stany USA" lub "Francja Południowa" |
| properties | Optional (Opcjonalność) | Dowolny rozszerzone właściwości powiązanych z tym określonej kategorii zdarzenia. Wszystkie właściwości niestandardowe/unikalnego muszą znajdować się w tym "B część" schematu. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Specyficzne dla usługi schematy dla dzienników diagnostycznych zasobów
Schemat dla dzienników diagnostycznych zasobów zależy od kategorii zasobów i dziennika. Ta lista zawiera wszystkich usług, które ułatwiają dostępne dzienniki diagnostyczne i linki do usługi i schematu specyficznego dla kategorii, gdzie są dostępne.

| Usługa | Schemat i dokumenty |
| --- | --- |
| Usługa Azure Active Directory | [Omówienie](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schemat dziennika inspekcji](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) i [schematu logowania](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Dzienniki diagnostyczne usługi API Management](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Bramy aplikacji |[Rejestrowanie diagnostyczne bramy Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Usługa log analytics dla usługi Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Usługa Azure Batch rejestrowanie diagnostyczne](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Usługa Azure Database for MySQL — dzienniki diagnostyczne](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Usługa Azure Database for postgresql w warstwie dzienników diagnostycznych](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | Schemat nie jest dostępna. |
| Content Delivery Network | [Dzienniki diagnostyczne platformy Azure dla usługi CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Rejestrowanie usługi Azure Cosmos DB](../../cosmos-db/logging.md) |
| Fabryka danych | [Monitorowanie fabryki danych przy użyciu usługi Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accessing diagnostic logs for Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics) |
| Data Lake Store |[Uzyskiwanie dostępu do dzienników diagnostycznych usługi Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Dzienniki diagnostyczne platformy Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schemat nie jest dostępna. |
| Azure Firewall | Schemat nie jest dostępna. |
| Usługa IoT Hub | [Operacje usługi IoT Hub](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Usługa Key Vault |[Funkcja rejestrowania usługi Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Moduł równoważenia obciążenia |[Analiza dzienników dotyczących usługi Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps — niestandardowy schemat śledzenia B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupy zabezpieczeń sieci |[Usługa Log Analytics dla sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Ochrona przed atakami DDOS | [Zarządzanie standardowa ochrona platformy Azure przed atakami DDoS](../../virtual-network/manage-ddos-protection.md) |
| PowerBI Dedicated | [Rejestrowanie diagnostyczne dla usługi Power BI Embedded na platformie Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Model danych usługi Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Wyszukiwanie |[Włączanie i korzystanie z analizy ruchu wyszukiwania](../../search/search-traffic-analytics.md) |
| Service Bus |[Dzienniki diagnostyczne platformy Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Usługa Azure SQL Database rejestrowania diagnostycznego](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Dzienniki diagnostyczne zadania](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Schemat dziennika usługi Traffic Manager](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Sieci wirtualne | Schemat nie jest dostępna. |
| Bramy sieci wirtualnej | Schemat nie jest dostępna. |

## <a name="supported-log-categories-per-resource-type"></a>Obsługiwane kategorie dzienników dla typu zasobu
|Typ zasobu|Category|Nazwa wyświetlana kategorii|
|---|---|---|
|Microsoft.AnalysisServices/servers|Aparat|Aparat|
|Microsoft.AnalysisServices/servers|Usługa|Usługa|
|Microsoft.ApiManagement/service|GatewayLogs|Dzienniki związane z bramą usługi ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Dzienniki zadań|
|Microsoft.Automation/automationAccounts|JobStreams|Strumienie zadania|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stan węzła DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Dzienniki usługi|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Pobiera metryki punktu końcowego, np. przepustowość, ruch wychodzący itd.|
|Microsoft.ClassicNetwork/networksecuritygroups|Zdarzenie przepływu reguł sieciowej grupy zabezpieczeń|Zdarzenie przepływu reguł sieciowej grupy zabezpieczeń|
|Microsoft.CognitiveServices/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.CognitiveServices/accounts|RequestResponse|Żądania i odpowiedzi dzienników|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Serwera interfejsu API rozwiązania Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Kubernetes Controller Manager|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Skalowanie klastra usługi Kubernetes|
|Microsoft.ContainerService/managedClusters|Usługa scheduler rozwiązania kubernetes|Kubernetes Scheduler|
|Microsoft.ContainerService/managedClusters|Ochrona|Uwierzytelnianie elementu Webhook|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|Uruchomień działania|Dziennik uruchomienia działania potoku|
|Microsoft.DataFactory/factories|PipelineRuns|Potok jest uruchamiany dziennika|
|Microsoft.DataFactory/factories|TriggerRuns|Wyzwalacz jest uruchamiany dziennika|
|Microsoft.DataLakeAnalytics/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.DataLakeAnalytics/accounts|Żądania|Dziennik żądań|
|Microsoft.DataLakeStore/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.DataLakeStore/accounts|Żądania|Dziennik żądań|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Dzienniki serwera MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Dzienniki serwera PostgreSQL|
|Microsoft.Devices/IotHubs|Połączenia|Połączenia|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Danych Telemetrycznych z urządzenia|
|Microsoft.Devices/IotHubs|C2DCommands|Polecenia C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operacje dotyczące tożsamości urządzenia|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operacje przekazywania plików|
|Microsoft.Devices/IotHubs|Trasy|Trasy|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operacje bliźniaczej reprezentacji C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Zapytania dotyczące bliźniaczych reprezentacji|
|Microsoft.Devices/IotHubs|JobsOperations|Operacje dotyczące zadań|
|Microsoft.Devices/IotHubs|DirectMethods|Metody bezpośrednie|
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnostyka E2E (wersja zapoznawcza)|
|Microsoft.Devices/IotHubs|Konfiguracje|Konfiguracje|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operacje dotyczące urządzenia|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operacje usługi|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Archiwizuj dzienniki|
|Microsoft.EventHub/namespaces|OperationalLogs|Dzienniki operacyjne|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Dzienniki skalowania automatycznego|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Obliczanie automatyczne skalowanie|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Akcje skalowania automatycznego skalowania|
|Microsoft.IoTSpaces/Graph|Ślad|Ślad|
|Microsoft.IoTSpaces/Graph|Operacyjne|Operacyjne|
|Microsoft.IoTSpaces/Graph|Inspekcja|Inspekcja|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Ruch przychodzący|Ruch przychodzący|
|Microsoft.IoTSpaces/Graph|Ruch wychodzący|Ruch wychodzący|
|Microsoft.KeyVault/vaults|AuditEvent|Dzienniki inspekcji|
|Microsoft.Logic/workflows|WorkflowRuntime|Zdarzenia diagnostyczne środowiska wykonawczego przepływów pracy|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Zdarzenia śledzenia konta integracji|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Zdarzenie sieciowej grupy zabezpieczeń|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Licznik reguły sieciowej grupy zabezpieczeń|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Zdarzenia alarmowe modułu równoważenia obciążenia|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stan usługi Load Balancer sondy kondycji|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Usługa DDoS protection powiadomienia|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Dzienniki decyzje dotyczące ograniczania ryzyka ataków DDoS przepływu|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Raporty środki zaradcze przed atakami DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alerty ochrony maszyny Wirtualnej|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Dziennik bramy Application Gateway dostępu|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Dziennik wydajności bramy Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Dziennik bramy Application Gateway zapory|
|Microsoft.Network/securegateways|AzureFirewallApplicationRule|Reguły aplikacji zapory platformy Azure|
|Microsoft.Network/securegateways|AzureFirewallNetworkRule|Reguła sieci zapory platformy Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Reguły aplikacji zapory platformy Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Reguła sieci zapory platformy Azure|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Dzienniki diagnostyczne tunelu|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Przekierowywanie dzienników diagnostycznych|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Dzienniki diagnostyczne protokołu IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Dzienniki diagnostyczne P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Zdarzenie wyniki usługi Traffic Manager sondy kondycji|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Komunikacja równorzędna dzienniki tabel tras|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Dziennik dostępu Frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Dziennik zapory aplikacji sieci Web Frontdoor|
|Microsoft.PowerBIDedicated/capacities|Aparat|Aparat|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Raportowania danych usługi Azure Backup|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery Jobs|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Zdarzeń usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Usługa Azure Site Recovery zreplikowane elementy|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Stany replikacji programu usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Punkty odzyskiwania odzyskiwania lokacji platformy Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Szybkość przesyłania danych replikacji usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Usługa Azure Site Recovery chronione współczynnik zmian danych na dysku|
|Microsoft.Search/searchServices|OperationLogs|Dzienniki operacji|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Dzienniki operacyjne|
|Microsoft.Sql/servers/databases|SQLInsights|Szczegółowe informacje SQL|
|Microsoft.Sql/servers/databases|AutomaticTuning|Automatyczne dostrajanie|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Statystyki środowiska uruchomieniowego Query Store|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Statystyki oczekiwania Query Store|
|Microsoft.Sql/servers/databases|Błędy|Błędy|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Statystyki oczekiwania bazy danych|
|Microsoft.Sql/servers/databases|Limity czasu|Limity czasu|
|Microsoft.Sql/servers/databases|bloki|bloki|
|Microsoft.Sql/servers/databases|Zakleszczenia|Zakleszczenia|
|Microsoft.Sql/servers/databases|Inspekcja|Dzienniki inspekcji|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Zdarzenia inspekcji zabezpieczeń SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Usługa dms pracowników|
|Microsoft.Sql/servers/databases|ExecRequests|Exec żądań|
|Microsoft.Sql/servers/databases|RequestSteps|Kroki żądania|
|Microsoft.Sql/servers/databases|SqlRequests|Żądania SQL|
|Microsoft.Sql/servers/databases|W tym czasie czeka|W tym czasie czeka|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Statystyki użycia zasobów|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Zdarzenia inspekcji zabezpieczeń SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|Szczegółowe informacje SQL|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Statystyki środowiska uruchomieniowego Query Store|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Statystyki oczekiwania Query Store|
|Microsoft.Sql/managedInstances/databases|Błędy|Błędy|
|Microsoft.StreamAnalytics/streamingjobs|Wykonanie|Wykonanie|
|Microsoft.StreamAnalytics/streamingjobs|Tworzenie|Tworzenie|
|Microsoft.Web/Sites|FunctionExecutionLogs|Dzienniki wykonywania — funkcja|
|Microsoft.Web/Sites/Slots|FunctionExecutionLogs|Dzienniki wykonywania — funkcja|

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat dzienników diagnostycznych](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Stream dzienników diagnostycznych zasobów do **usługi Event Hubs**](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)
* [Zmień ustawienia diagnostyczne zasobów przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analizowanie dzienników z usługi Azure storage za pomocą usługi Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)

