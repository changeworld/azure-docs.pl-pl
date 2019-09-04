---
title: Azure Resource Manager dostawców zasobów według usług platformy Azure
description: Wyświetla listę wszystkich przestrzeni nazw dostawcy zasobów dla Azure Resource Manager i pokazuje usługę platformy Azure dla tej przestrzeni nazw.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: tomfitz
ms.openlocfilehash: 3df481245db8d737032d1abf5906a2ffe949a2fe
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275705"
---
# <a name="resource-providers-for-azure-services"></a>Dostawcy zasobów dla usług platformy Azure

W tym artykule pokazano, jak przestrzenie nazw dostawcy zasobów są mapowane na usługi platformy Azure.

## <a name="match-resource-provider-to-service"></a>Dopasuj dostawcę zasobów do usługi

| Przestrzeń nazw dostawcy zasobów | Usługa platformy Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../active-directory-domain-services/index.yml) |
| microsoft.aadiam | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Addons | rdzeń |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure Advisor](../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [API Management](../api-management/index.yml) |
| Microsoft.AppConfiguration | rdzeń |
| Microsoft.Authorization | [Azure Resource Manager](index.yml) |
| Microsoft.Automation | [Automatyzacja](../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../active-directory-b2c/index.yml) |
| Microsoft.AzureStack | rdzeń |
| Microsoft.Batch | [Batch](../batch/index.yml) |
| Microsoft.Billing | [Billing](/azure/billing/) |
| Microsoft.BingMaps | [Mapy Bing](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.BizTalkServices | [BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md) |
| Microsoft. łańcucha bloków | [Usługa Azure łańcucha bloków](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Plany platformy Azure](/azure/governance/blueprints/) |
| Microsoft.BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft.Cache | [Azure Cache for Redis](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | rdzeń |
| Microsoft.Cdn | [Content Delivery Network](../cdn/index.yml) |
| Microsoft.CertificateRegistration | [App Service certyfikaty](../app-service/web-sites-purchase-ssl-web-site.md) |
| Microsoft.ClassicCompute | Maszyna wirtualna klasycznego modelu wdrażania |
| Microsoft.ClassicInfrastructureMigrate | Migracja klasycznego modelu wdrażania |
| Microsoft.ClassicNetwork | Sieć wirtualna klasycznego modelu wdrażania |
| Microsoft.ClassicStorage | Magazyn klasycznych modeli wdrażania |
| Microsoft.ClassicSubscription | Klasyczny model wdrażania |
| Microsoft.CognitiveServices | [Cognitive Services](/azure/cognitive-services/) |
| Microsoft.Commerce | rdzeń |
| Microsoft.Compute | [Virtual Machines](/azure/virtual-machines/) |
| Microsoft.Consumption | [Cost Management](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Container Instances](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Container Registry](/azure/container-registry/) |
| Microsoft.ContainerService | [Usługa Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft.ContentModerator | [Content Moderator platformy Azure](../cognitive-services/content-moderator/index.yml) |
| Microsoft.CostManagement | [Cost Management](/azure/cost-management/) |
| Microsoft.CustomerInsights | Customer Insights |
| Microsoft. CustomerLockbox | Skrytka klienta Microsoft Azure |
| Microsoft.CustomProviders | [Dostawcy niestandardowi platformy Azure](../managed-applications/custom-providers-overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Data Box Edge](../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Store](../storage/blobs/data-lake-storage-introduction.md) |
| Migracja Microsoft. | [Azure Database Migration Service](/azure/dms/) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Azure Database for MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| Microsoft.DeploymentManager | [Menedżer wdrażania platformy Azure](deployment-manager-overview.md) |
| Microsoft.Devices | [IoT Hub](/azure/iot-hub/)<br />[Usługa IoT Hub Device Provisioning](/azure/iot-dps/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [App Service](/azure/app-service/) |
| Microsoft.EnterpriseKnowledgeGraph | Wykres merytoryczny dla przedsiębiorstw |
| Microsoft.EventGrid | [Event Grid](/azure/event-grid/) |
| Microsoft.EventHub | [Event Hubs](../event-hubs/index.yml) |
| Microsoft.Features | [Azure Resource Manager](index.yml) |
| Microsoft.Genomics | [Microsoft Genomics](/azure/genomics/) |
| Microsoft.GuestConfiguration | [Azure Policy](../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA na platformie Azure](../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft. HardwareSecurityModules | [Dedykowany moduł HSM platformy Azure](../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [Interfejs API platformy Azure dla usługi FHIR](../healthcare-apis/index.yml) |
| Microsoft.ImportExport | [Azure Import/Export](../storage/common/storage-import-export-service.md) |
| Microsoft. Insights | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft. Intune | [Intune](/intune/) |
| Microsoft.IoTCentral | [IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Azure Digital bliźniaczych reprezentacji](../digital-twins/index.yml) |
| Microsoft.KeyVault | [Usługa Key Vault](../key-vault/index.yml) |
| Microsoft.Kusto | [Azure Data Explorer](../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.LocationBasedServices | [Azure Maps](../azure-maps/index.yml) |
| Microsoft.LocationServices | rdzeń |
| Microsoft.LogAnalytics | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.Logic | [Logic Apps](../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningCompute | [Usługa Machine Learning](../machine-learning/index.yml) |
| Microsoft.MachineLearningModelManagement | [Usługa Machine Learning](../machine-learning/index.yml) |
| Microsoft.MachineLearningServices | [Usługa Machine Learning](../machine-learning/index.yml) |
| Microsoft.ManagedIdentity | [Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.Management | [Grupy zarządzania](/azure/governance/management-groups/) |
| Microsoft. Maps | [Azure Maps](../azure-maps/index.yml) |
| Microsoft.Marketplace | rdzeń |
| Microsoft.MarketplaceApps | rdzeń |
| Microsoft.MarketplaceOrdering | rdzeń |
| Microsoft.Media | [Media Services](../media-services/index.yml) |
| Microsoft.Migrate | [Azure Migrate](../migrate/migrate-overview.md) |
| Microsoft.MixedReality | [Kotwice przestrzenne platformy Azure](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../azure-netapp-files/index.yml) |
| Microsoft.Network | [Virtual Network](../virtual-network/index.yml)<br />[Load Balancer](../load-balancer/index.yml)<br />[Application Gateway](../application-gateway/index.yml)<br />[Azure DNS](../dns/index.yml)<br />[ExpressRoute](../expressroute/index.yml)<br />[VPN Gateway](../vpn-gateway/index.yml)<br />[Traffic Manager](../traffic-manager/index.yml)<br />[Network Watcher](../network-watcher/index.yml)<br />[Azure Firewall](../firewall/index.yml)<br />[Azure Front Door Service](../frontdoor/index.yml)<br />[Azure bastionu](/azure/bastion/) |
| Microsoft.NotificationHubs | [Notification Hubs](../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure Migrate](../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../governance/policy/index.yml) |
| Microsoft.Portal | [Azure Portal](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Site Recovery](../site-recovery/index.yml) |
| Microsoft.Relay | [Azure Relay](../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Wykres zasobów platformy Azure](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | rdzeń |
| Microsoft.Resources | [Azure Resource Manager](index.yml) |
| Microsoft.SaaS | rdzeń |
| Microsoft.Scheduler | [Scheduler](/azure/scheduler/) |
| Microsoft.Search | [Azure Search](../search/index.yml) |
| Microsoft.Security | [Security Center](../security-center/index.yml) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Siatka Service Fabric](../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Usługa sygnałów platformy Azure](../azure-signalr/index.yml) |
| Microsoft.SiteRecovery | [Site Recovery](../site-recovery/index.yml) |
| Microsoft.Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../sql-database/index.yml) |
| Microsoft.SqlVirtualMachine | [Program SQL Server na maszynach wirtualnych platformy Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Storage](../storage/index.yml) |
| Microsoft.StorageSync | [Storage](../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Stream Analytics](../stream-analytics/index.yml) |
| Microsoft.Subscription | rdzeń |
| Microsoft. Support | rdzeń |
| Microsoft.TimeSeriesInsights | [Time Series Insights](../time-series-insights/index.yml) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft. VMwareCloudSimple | [Rozwiązanie VMware firmy Azure według CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [App Service](../app-service/index.yml)<br />[Funkcje](../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) |
| Microsoft.WindowsIoT | [Windows 10 IoT Core Services](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../azure-monitor/index.yml) |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat dostawców zasobów, zobacz [dostawcy zasobów platformy Azure i typy](resource-manager-supported-services.md)
