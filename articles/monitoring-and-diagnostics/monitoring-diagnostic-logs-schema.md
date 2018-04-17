---
title: Diagnostyka Azure dzienniki obsługiwane usługi i schematów | Dokumentacja firmy Microsoft
description: Zrozumienie obsługiwanych schematu usług i zdarzeń do dzienników diagnostycznych platformy Azure.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/12/2018
ms.author: johnkem
ms.openlocfilehash: 91c3f1507bb4fb64d5395917e8e431951f77e72b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Obsługiwane usługi, schematy i kategorie dzienników diagnostycznych platformy Azure

[Dzienniki diagnostyczne zasobów platformy Azure](monitoring-overview-of-diagnostic-logs.md) są dzienniki emitowane przez zasobów platformy Azure, opisujących operacji tego zasobu. Te dzienniki są typu zasobu określonego. W tym artykule firma Microsoft konspektu zestaw obsługiwanych usług i schematu zdarzeń dla zdarzenia emitowane przez poszczególnych usług. Ten artykuł zawiera także pełną listę kategorie dziennika dostępne na typ zasobu.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>Obsługiwane usługi i schematy do dzienników diagnostycznych zasobu
Schemat dla dzienników diagnostycznych zasobu różni się w zależności od kategorii zasobów i dziennika.   

| Usługa | Schemat & dokumentów |
| --- | --- |
| Analysis Services | Schemat nie jest dostępna. |
| API Management | [Dzienniki diagnostyczne Management API](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Bramy Application Gateway |[Rejestrowania diagnostyki bramy aplikacji](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Analizy dzienników dla usługi Automatyzacja Azure](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure rejestrowania diagnostycznego partii](../batch/batch-diagnostics.md) |
| Customer Insights | Schemat nie jest dostępna. |
| Content Delivery Network | Schemat nie jest dostępna. |
| CosmosDB | [Rejestrowanie Azure rozwiązania Cosmos bazy danych](../cosmos-db/logging.md) |
| Data Lake Analytics |[Accessing diagnostic logs for Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics) |
| Data Lake Store |[Uzyskiwanie dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs dzienników diagnostycznych](../event-hubs/event-hubs-diagnostic-logs.md) |
| Usługa IoT Hub | [Operacje Centrum IoT](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Usługa Key Vault |[Funkcja rejestrowania usługi Azure Key Vault](../key-vault/key-vault-logging.md) |
| Moduł równoważenia obciążenia |[Analiza dzienników dotyczących usługi Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps — niestandardowy schemat śledzenia B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupy zabezpieczeń sieci |[Usługa Log Analytics dla sieciowych grup zabezpieczeń](../virtual-network/virtual-network-nsg-manage-log.md) |
| Ochrona przed atakami DDOS | Schemat nie jest dostępna. |
| Recovery Services | [Model danych dla kopii zapasowej systemu Azure](../backup/backup-azure-reports-data-model.md)|
| Wyszukiwanie |[Włączanie i używanie analizy ruchu wyszukiwania](../search/search-traffic-analytics.md) |
| Zarządzanie serwerem | Schemat nie jest dostępna. |
| Service Bus |[Azure Service Bus dzienników diagnostycznych](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database rejestrowania diagnostycznego](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Dzienniki diagnostyczne zadania](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Sieci wirtualne | Schemat nie jest dostępna. |

## <a name="supported-log-categories-per-resource-type"></a>Obsługiwane kategorie dziennika na typ zasobu
|Typ zasobu|Kategoria|Nazwa wyświetlana kategorii|
|---|---|---|
|Microsoft.AnalysisServices/servers|Aparat|Aparat|
|Microsoft.AnalysisServices/servers|Usługa|Usługa|
|Microsoft.ApiManagement/service|GatewayLogs|Dzienniki związane z ApiManagement bramy|
|Microsoft.Automation/automationAccounts|JobLogs|Rejestruje zadania|
|Microsoft.Automation/automationAccounts|JobStreams|Strumienie zadania|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stan węzła DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Dzienniki usługi|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Pobiera metryki punktu końcowego (np. przepustowość, ruch wychodzący itd.).|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Dziennik uruchamia aktywności w potoku|
|Microsoft.DataFactory/factories|PipelineRuns|Potok jest uruchamiane dziennika|
|Microsoft.DataFactory/factories|TriggerRuns|Wyzwalacz uruchamia dziennika|
|Microsoft.DataLakeAnalytics/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.DataLakeAnalytics/accounts|Żądania|Dzienniki żądań|
|Microsoft.DataLakeStore/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.DataLakeStore/accounts|Żądania|Dzienniki żądań|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Dzienniki serwera PostgreSQL|
|Microsoft.Devices/IotHubs|Połączenia|Połączenia|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetrii urządzenia|
|Microsoft.Devices/IotHubs|C2DCommands|Polecenia C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operacje tożsamości urządzenia|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operacje przekazywania plików|
|Microsoft.Devices/IotHubs|Trasy|Trasy|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operacje dwie C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Dwie zapytań|
|Microsoft.Devices/IotHubs|JobsOperations|Operacje zadania|
|Microsoft.Devices/IotHubs|DirectMethods|Bezpośrednie metody|
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnostyka E2E (wersja zapoznawcza)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operacje urządzenia|
|Microsoft.Devices/provisioningServices|Serviceoperation|Operacje usługi|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Dzienniki archiwum|
|Microsoft.EventHub/namespaces|OperationalLogs|Operacyjne dzienniki|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Dzienniki skalowania automatycznego|
|Microsoft.KeyVault/vaults|AuditEvent|Dzienniki inspekcji|
|Microsoft.Logic/workflows|WorkflowRuntime|Zdarzenia diagnostyczne środowiska wykonawczego przepływów pracy|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Zdarzenia śledzenia konta integracji|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Zdarzenie sieciowej grupy zabezpieczeń|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Licznik reguł sieciowej grupy zabezpieczeń|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Zdarzenia alertu modułu równoważenia obciążenia|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stan kondycji sondę modułu równoważenia obciążenia|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Powiadomienia ochrony przed atakami DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alerty ochrony maszyny Wirtualnej|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Dziennik dostępu bramy aplikacji|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Dziennik wydajności bramy aplikacji|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Dziennik zapory bramy aplikacji|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Dzienniki diagnostyczne tunelu|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Dzienniki diagnostyczne trasy|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Dzienniki diagnostyczne P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Menedżer ruchu sondy kondycji wyniki zdarzenia|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Tabela GWM liczników|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Kopia zapasowa Azure danych raportowania|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery Jobs|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Usługi Azure Site Recovery zdarzenia|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Elementy replikowane usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Usługi Azure Site Recovery replikacji statystyki|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Punkty odzyskiwania odzyskiwania lokacji Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Szybkość przekazywanie danych replikacji usługi Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Przenoszenie danych dysku chronionych usługi Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Dzienniki operacji |
|Microsoft.ServiceBus/namespaces|OperationalLogs|Operacyjne dzienniki|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Magazyn zapytań statystyk czasu wykonywania|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Magazyn zapytań oczekiwania statystyki|
|Microsoft.Sql/servers/databases|Błędy|Błędy|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Baza danych statystyki oczekiwania|
|Microsoft.Sql/servers/databases|Limity czasu|Limity czasu|
|Microsoft.Sql/servers/databases|Bloki|Bloki|
|Microsoft.Sql/servers/databases|SQLInsights|Informacje na temat technologii SQL|
|Microsoft.Sql/servers/databases|Inspekcja|Dzienniki inspekcji|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Zdarzenia inspekcji zabezpieczeń SQL|
|Microsoft.StreamAnalytics/streamingjobs|Wykonanie|Wykonanie|
|Microsoft.StreamAnalytics/streamingjobs|Tworzenie|Tworzenie|

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dzienników diagnostycznych](monitoring-overview-of-diagnostic-logs.md)
* [Strumień zasobu dzienników diagnostycznych do **centra zdarzeń**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Zmień ustawienia diagnostyczne zasobu przy użyciu interfejsu API REST Azure monitora](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analizowanie dzienników z usługi Azure storage z analizy dzienników](../log-analytics/log-analytics-azure-storage.md)
