---
title: Dzienniki diagnostyczne platformy Azure obsługiwane usługi i schematy
description: Opis obsługiwanych schematu usług i zdarzeń z systemem dzienniki diagnostyczne platformy Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 8/21/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 06d9fda01a89340eb019b4900c02e321e0b73cf5
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818968"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Obsługiwane usługi, schematów i kategorie dzienników diagnostycznych platformy Azure

[Dzienniki diagnostyczne platformy Azure Monitor](monitoring-overview-of-diagnostic-logs.md) to Dzienniki tworzone przez usługi platformy Azure, które opisują działania tych usług lub zasobów. Wszystkie dzienniki diagnostyczne, dostępne za pośrednictwem usługi Azure Monitor udostępniać wspólny schemat najwyższego poziomu, elastyczność dla każdej usługi emitować unikatowe właściwości dla ich własnych zdarzeń.

Połączenie typu zasobu (dostępne w `resourceId` właściwości) i `category` jednoznacznie zidentyfikować schematu. W tym artykule opisano schemat najwyższego poziomu dla dzienników diagnostycznych i linki do schematu dla każdej usługi.

## <a name="top-level-diagnostic-logs-schema"></a>Schemat najwyższego poziomu dzienników diagnostycznych

| Name (Nazwa) | Wymagane/opcjonalne | Opis |
|---|---|---|
| time | Wymagane | Sygnatura czasowa (UTC) zdarzenia. |
| resourceId | Wymagane | Identyfikator zasobu zasobu, do którego emitowane zdarzenia. W przypadku usług dzierżawy jest to /tenants/tenant-id/providers/provider-name formularza. |
| Identyfikator dzierżawy | Wymagane dla dzienników dzierżawy | Identyfikator dzierżawy dzierżawy usługi Active Directory, która to zdarzenie jest powiązane. Ta właściwość jest używana tylko dla dzienników na poziomie dzierżawy, nie ma w dziennikach poziom zasobów. |
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
| Usługa Azure Active Directory | [Omówienie](../active-directory/reports-monitoring/overview-activity-logs-in-azure-monitor.md), [schemat dziennika inspekcji](../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) i [Sign-Ins schematu](../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Dzienniki diagnostyczne usługi API Management](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Bramy aplikacji |[Rejestrowanie diagnostyczne bramy Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Usługa log analytics dla usługi Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Usługa Azure Batch rejestrowanie diagnostyczne](../batch/batch-diagnostics.md) |
| Cognitive Services | Schemat nie jest dostępna. |
| Content Delivery Network | [Dzienniki diagnostyczne platformy Azure dla usługi CDN](../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Rejestrowanie usługi Azure Cosmos DB](../cosmos-db/logging.md) |
| Fabryka danych | [Monitorowanie fabryki danych przy użyciu usługi Azure Monitor](../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accessing diagnostic logs for Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics) |
| Data Lake Store |[Uzyskiwanie dostępu do dzienników diagnostycznych usługi Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| DB dla PostgreSQL |  Schemat nie jest dostępna. |
| Event Hubs |[Dzienniki diagnostyczne platformy Azure Event Hubs](../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schemat nie jest dostępna. |
| Azure Firewall | Schemat nie jest dostępna. |
| Usługa IoT Hub | [Operacje usługi IoT Hub](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Usługa Key Vault |[Funkcja rejestrowania usługi Azure Key Vault](../key-vault/key-vault-logging.md) |
| Moduł równoważenia obciążenia |[Analiza dzienników dotyczących usługi Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps — niestandardowy schemat śledzenia B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupy zabezpieczeń sieci |[Usługa Log Analytics dla sieciowych grup zabezpieczeń](../virtual-network/virtual-network-nsg-manage-log.md) |
| Ochrona przed atakami DDOS | [Zarządzanie standardowa ochrona platformy Azure przed atakami DDoS](../virtual-network/manage-ddos-protection.md) |
| Usługi Power BI w wersji dedykowanej | [Rejestrowanie diagnostyczne dla usługi Power BI Embedded na platformie Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Model danych usługi Azure Backup](../backup/backup-azure-reports-data-model.md)|
| Wyszukiwanie |[Włączanie i korzystanie z analizy ruchu wyszukiwania](../search/search-traffic-analytics.md) |
| Service Bus |[Dzienniki diagnostyczne platformy Azure Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Usługa Azure SQL Database rejestrowania diagnostycznego](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Dzienniki diagnostyczne zadania](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | Schemat nie jest dostępna. |
| Sieci wirtualne | Schemat nie jest dostępna. |
| Bramy sieci wirtualnej | Schemat nie jest dostępna. |

## <a name="supported-log-categories-per-resource-type"></a>Obsługiwane kategorie dzienników dla typu zasobu
|Typ zasobu|Kategoria|Nazwa wyświetlana kategorii|
|---|---|---|
|Microsoft.AnalysisServices/servers|Aparat|Aparat|
|Microsoft.AnalysisServices/servers|Usługa|Usługa|
|Microsoft.ApiManagement/service|GatewayLogs|Dzienniki związane z bramą usługi ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Dzienniki zadań|
|Microsoft.Automation/automationAccounts|JobStreams|Strumienie zadania|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stan węzła DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Dzienniki usługi|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Pobiera metryki punktu końcowego (np. przepustowość, ruch wychodzący itd.).|
|Microsoft.ClassicNetwork/networksecuritygroups|Zdarzenie przepływu reguł sieciowej grupy zabezpieczeń|Zdarzenie przepływu reguł sieciowej grupy zabezpieczeń|
|Microsoft.CognitiveServices/accounts|Inspekcja|Inspekcja|
|Microsoft.ContainerService/managedClusters|apiserver rozwiązania kubernetes|Serwera interfejsu API rozwiązania Kubernetes|
|Microsoft.ContainerService/managedClusters|menedżerem w przypadku kontroli rozwiązania kubernetes|Menedżerem kontroli rozwiązania Kubernetes|
|Microsoft.ContainerService/managedClusters|Usługa scheduler rozwiązania kubernetes|Harmonogram usługi Kubernetes|
|Microsoft.ContainerService/managedClusters|Ochrona|Uwierzytelnianie elementu Webhook|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Dziennik uruchomienia działania potoku|
|Microsoft.DataFactory/factories|PipelineRuns|Potok jest uruchamiany dziennika|
|Microsoft.DataFactory/factories|TriggerRuns|Wyzwalacz jest uruchamiany dziennika|
|Microsoft.DataLakeAnalytics/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.DataLakeAnalytics/accounts|Żądania|Dziennik żądań|
|Microsoft.DataLakeStore/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.DataLakeStore/accounts|Żądania|Dziennik żądań|
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
|Microsoft.Devices/provisioningServices|Serviceoperation|Operacje usługi|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Archiwizuj dzienniki|
|Microsoft.EventHub/namespaces|OperationalLogs|Dzienniki operacyjne|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Dzienniki skalowania automatycznego|
|Microsoft.KeyVault/vaults|AuditEvent|Dzienniki inspekcji|
|Microsoft.Logic/workflows|WorkflowRuntime|Zdarzenia diagnostyczne środowiska wykonawczego przepływów pracy|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Zdarzenia śledzenia konta integracji|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Zdarzenie sieciowej grupy zabezpieczeń|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Licznik reguły sieciowej grupy zabezpieczeń|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Zdarzenia alarmowe modułu równoważenia obciążenia|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stan usługi Load Balancer sondy kondycji|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Usługa DDoS protection powiadomienia|
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
|Microsoft.PowerBIDedicated/capacities|Aparat|Aparat|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Raportowania danych usługi Azure Backup|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery Jobs|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Zdarzeń usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Usługa Azure Site Recovery zreplikowane elementy|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Stany replikacji programu usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Punkty odzyskiwania odzyskiwania lokacji platformy Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Szybkość przesyłania danych replikacji usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Usługa Azure Site Recovery chronione współczynnik zmian danych na dysku|
|Microsoft.Search/searchServices|OperationLogs|Dzienniki operacji |
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
|Microsoft.Sql/servers/databases|SqlDw_Requests|Żądania usługi Magazyn danych SQL|
|Microsoft.Sql/servers/databases|SqlDw_RequestSteps|Kroki żądania magazynu danych SQL|
|Microsoft.StreamAnalytics/streamingjobs|Wykonanie|Wykonanie|
|Microsoft.StreamAnalytics/streamingjobs|Tworzenie|Tworzenie|

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat dzienników diagnostycznych](monitoring-overview-of-diagnostic-logs.md)
* [Stream dzienników diagnostycznych zasobów do **usługi Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Zmień ustawienia diagnostyczne zasobów przy użyciu interfejsu API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analizowanie dzienników z usługi Azure storage za pomocą usługi Log Analytics](../log-analytics/log-analytics-azure-storage.md)
