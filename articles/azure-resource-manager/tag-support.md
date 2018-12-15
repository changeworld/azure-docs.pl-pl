---
title: Menedżer zasobów tag pomocy technicznej platformy Azure dla zasobów
description: Pokazuje, jakie typy zasobów platformy Azure obsługuje tagi. Zawiera szczegółowe informacje dla wszystkich usług platformy Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: 580955d3c6fd7a33c152e49e601d8078eb169a22
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409670"
---
# <a name="tag-support-for-azure-resources"></a>Obsługa tagów dla zasobów platformy Azure
W tym artykule opisano, czy typ zasobu obsługuje [znakowanie](resource-group-using-tags.md).

## <a name="aad-domain-services"></a>AAD Domain Services
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| domeny | Nie | 

## <a name="ad-hybrid-health-service"></a>Usługa kondycji AD hybrydowych
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| services | Nie | 
| addsservices | Nie | 
| konfiguracja | Nie | 
| agenci | Nie | 
| aadsupportcases | Nie | 
| raporty | Nie | 
| servicehealthmetrics | Nie | 
| dzienniki | Nie | 
| anonymousapiusers | Nie | 

## <a name="analysis-services"></a>Analysis Services
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| serwerów | Yes | 

## <a name="api-hubs"></a>Koncentratory interfejsu API
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| apiManagementAccounts | Nie | 
| apiManagementAccounts/connectionProviders | Nie | 
| apiManagementAccounts/połączeń | Nie | 
| apiManagementAccounts/connectionAcls | Nie | 
| apiManagementAccounts/connectionProviderAcls | Nie | 
| apiManagementAccounts/interfejsów API | Nie | 

## <a name="api-management"></a>API Management
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| usługa | Yes | 

## <a name="automation"></a>Automatyzacja
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| AutomationAccounts | Yes | 
| automationAccounts/elementów runbook | Yes | 
| automationAccounts/konfiguracji | Yes | 
| automationAccounts/elementów webhook | Nie | 
| automationAccounts/softwareUpdateConfigurations | Nie | 
| automationAccounts/zadań | Nie | 

## <a name="batch"></a>Batch
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| batchAccounts | Yes | 

## <a name="bing-maps"></a>Mapy Bing
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| mapApis | Yes | 

## <a name="biztalk-services"></a>BizTalk Services
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| BizTalk | Yes | 

## <a name="cache"></a>Pamięć podręczna
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Redis | Yes | 

## <a name="cdn"></a>CDN
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Profile | Yes | 
| Profile/punktów końcowych | Yes | 
| punkty końcowe/profile/źródła | Nie | 
| punkty końcowe/profile/customdomains | Nie | 
| validateProbe | Nie | 
| edgenodes | Nie | 

## <a name="classic-compute"></a>Klasyczne obliczeniowe
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| domainNames | Nie | 
| domainNames/miejsca | Nie | 
| domainNames/miejsc/role | Nie | 
| maszyn wirtualnych | Nie | 
| maszyn wirtualnych/diagnosticSettings | Nie | 
| maszyn wirtualnych/metricDefinitions | Nie | 
| maszyn wirtualnych/metryki | Nie | 

## <a name="classic-infrastructure-migrate"></a>Migrowanie klasycznej infrastruktury
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| classicInfrastructureResources | Nie | 

## <a name="classic-network"></a>Klasycznej sieci
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| virtualNetworks | Nie | 
| virtualNetworks/virtualNetworkPeerings | Nie | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nie | 

## <a name="classic-storage"></a>Klasycznego magazynu
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| storageAccounts/services | Nie | 
| storageAccounts/services/diagnosticSettings | Nie | 

## <a name="compute"></a>Wystąpienia obliczeniowe
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| availabilitySets | Yes | 
| maszyn wirtualnych | Yes | 
| maszyn wirtualnych/rozszerzenia | Yes | 
| virtualMachineScaleSets | Yes | 
| virtualMachineScaleSets/rozszerzenia | Nie | 
| virtualMachineScaleSets/maszyn wirtualnych | Nie | 
| virtualMachineScaleSets/interfejsy | Nie | 
| Interfejsy virtualMachineScaleSets/maszyn wirtualnych | Nie | 
| virtualMachineScaleSets/publicIPAddresses | Nie | 
| restorePointCollections | Yes | 
| restorePointCollections/restorePoints | Nie | 
| maszyn wirtualnych/diagnosticSettings | Nie | 
| maszyn wirtualnych/metricDefinitions | Nie | 
| sharedVMImages | Yes | 
| sharedVMImages/wersji | Yes | 
| Dyski | Yes | 
| migawki | Yes | 
| images | Yes | 

## <a name="container"></a>Kontener
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| containerGroups | Yes | 

## <a name="container-instance"></a>Wystąpienie kontenera
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| containerGroups | Yes | 
| serviceAssociationLinks | Nie | 

## <a name="container-service"></a>Container Service
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| containerServices | Yes | 

## <a name="cortana-analytics"></a>Cortana Analytics
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="cosmos-db"></a>Cosmos DB
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| databaseAccounts | Yes | 
| databaseAccountNames | Nie | 

## <a name="cost-management"></a>Cost Management
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Łączniki | Yes | 

## <a name="data-box-edge"></a>Data Box Edge
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| DataBoxEdgeDevices | Yes | 

## <a name="data-catalog"></a>Data Catalog
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| katalogi | Yes | 

## <a name="data-connect"></a>Łączenie danych
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| connectionManagers | Yes | 

## <a name="data-factory"></a>Fabryka danych
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| dataFactories | Yes | 
| fabryki | Yes | 
| fabryki/integrationRuntimes | Nie | 
| dataFactories/diagnosticSettings | Nie | 
| dataFactories/metricDefinitions | Nie | 
| dataFactorySchema | Nie | 

## <a name="devices"></a>Urządzenia
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| IotHubs | Yes | 
| IotHubs/eventGridFilters | Nie | 
| ProvisioningServices | Yes | 

## <a name="devspaces"></a>Devspaces
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Kontrolery | Yes | 

## <a name="devtest-lab"></a>Laboratorium Devtest Labs
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Warsztaty | Yes | 
| Harmonogramy | Yes | 
| laboratoria/maszyn wirtualnych | Yes | 
| laboratoria/serviceRunners | Yes | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| lcsprojects | Nie | 
| lcsprojects/łączników | Nie | 
| lcsprojects/clouddeployments | Nie | 

## <a name="event-grid"></a>Event Grid
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| eventSubscriptions | Nie | 
| Tematy | Yes | 
| domeny | Yes | 
| domeny/tematów | Nie | 
| topicTypes | Nie | 
| extensionTopics | Nie | 

## <a name="event-hub"></a>Centrum zdarzeń
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Przestrzenie nazw | Yes | 
| Klastry | Yes | 

## <a name="hana-on-azure"></a>Hana na platformie Azure
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| hanaInstances | Yes | 

## <a name="hdinsight"></a>HDInsight
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Klastry | Yes | 
| klastry/aplikacji | Nie | 

## <a name="import-export"></a>Importowanie i eksportowanie
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| zadania | Yes | 

## <a name="insights"></a>Insights
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Składniki | Yes | 
| składniki/zapytania | Nie | 
| składniki/metryki | Nie | 
| składniki/zdarzenia | Nie | 
| testy internetowe | Yes | 
| — zapytania | Nie | 
| scheduledqueryrules | Yes | 
| składniki/pricingPlans | Nie | 
| migrateToNewPricingModel | Nie | 
| rollbackToLegacyPricingModel | Nie | 
| automatedExportSettings | Nie | 
| Skoroszyty | Yes | 
| myWorkbooks | Nie | 
| dzienniki | Nie | 

## <a name="key-vault"></a>Usługa Key Vault
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Magazyny | Yes | 
| Magazyny/klucze tajne | Nie | 
| Magazyny/accessPolicies | Nie | 
| deletedVaults | Nie | 

## <a name="log-analytics"></a>Log Analytics
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| dzienniki | Nie | 

## <a name="logic"></a>Logika
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Przepływy pracy | Yes | 
| integrationAccounts | Yes | 

## <a name="machine-learning-services"></a>Machine Learning Services
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| obszary robocze | Yes | 
| obszary robocze/usługi obliczeniowe | Nie | 

## <a name="managed-identity"></a>Tożsamość zarządzana
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Tożsamości | Nie | 
| userAssignedIdentities | Yes | 

## <a name="mariadb"></a>MariaDB
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| serwerów | Yes | 
| serwery/recoverableServers | Nie | 
| serwery/virtualNetworkRules | Nie | 

## <a name="marketplace-apps"></a>Aplikacje z portalu Marketplace
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| classicDevServices | Yes | 

## <a name="marketplace-ordering"></a>Porządkowanie witryny Marketplace
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Umowy | Nie | 
| offertypes | Nie | 

## <a name="media"></a>Multimedia
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| mediaservices | Yes | 
| mediaservices/zasobów | Nie | 
| mediaservices/contentKeyPolicies | Nie | 
| mediaservices/streamingLocators | Nie | 
| mediaservices/streamingPolicies | Nie | 
| mediaservices/eventGridFilters | Nie | 
| mediaservices/przekształceń | Nie | 
| zadania mediaservices/przekształceń | Nie | 
| mediaservices/punkty | Yes | 
| mediaservices/liveEvents | Yes | 
| mediaservices/liveEvents/liveOutputs | Nie | 
| mediaservices/streamingEndpointOperations | Nie | 
| mediaservices/liveEventOperations | Nie | 
| mediaservices/liveOutputOperations | Nie | 
| zasobów/mediaservices/assetFilters | Nie | 
| mediaservices/accountFilters | Nie | 

## <a name="mysql"></a>MySQL
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| serwerów | Yes | 
| serwery/recoverableServers | Nie | 
| serwery/virtualNetworkRules | Nie | 

## <a name="network"></a>Sieć
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| virtualNetworks | Yes | 
| publicIPAddresses | Yes | 
| Interfejsy | Yes | 
| interfaceEndpoints | Yes | 
| modułami modułami | Yes | 
| networkSecurityGroups | Yes | 
| Applicationsecuritygroup | Yes | 
| serviceEndpointPolicies | Yes | 
| networkIntentPolicies | Yes | 
| routeTables | Yes | 
| publicIPPrefixes | Yes | 
| networkWatchers | Yes | 
| networkWatchers/connectionMonitors | Yes | 
| networkWatchers/obiektywów | Yes | 
| networkWatchers/pingMeshes | Yes | 
| elementów Virtualnetworkgateway | Yes | 
| localNetworkGateways | Yes | 
| połączenia | Yes | 
| applicationGateways | Yes | 
| expressRouteCircuits | Yes | 
| routeFilters | Yes | 
| virtualWans | Yes | 
| vpnSites | Yes | 
| virtualHubs | Yes | 
| vpnGateways | Yes | 
| azureFirewalls | Yes | 
| virtualNetworkTaps | Yes | 
| privateLinkServices | Yes | 
| ddosProtectionPlans | Yes | 
| networkProfiles | Yes | 
| frontdoors | Yes | 
| frontdoorWebApplicationFirewallPolicies | Yes | 
| webApplicationFirewallPolicies | Yes | 

## <a name="notification-hubs"></a>Notification Hubs
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Przestrzenie nazw | Yes | 
| przestrzenie nazw/notificationHubs | Yes | 

## <a name="portal"></a>Portal
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Pulpity nawigacyjne | Yes | 

## <a name="portal-sdk"></a>Portal zestawu SDK
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| rootResources | Yes | 

## <a name="postgresql"></a>PostgreSQL
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| serwerów | Yes | 
| serwery/recoverableServers | Nie | 
| serwery/virtualNetworkRules | Nie | 
| serwery/topQueryStatistics | Nie | 
| serwery/queryTexts | Nie | 
| serwery/waitStatistics | Nie | 
| serwery/doradców | Nie | 

## <a name="power-bi"></a>Power BI
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| workspaceCollections | Yes | 

## <a name="recovery-services"></a>Recovery Services
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Magazyny | Yes | 
| backupProtectedItems | Nie | 

## <a name="relay"></a>Usługa Relay
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Przestrzenie nazw | Yes | 

## <a name="resources"></a>Zasoby
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| resourceGroups | Yes | 
| subskrypcji/grup zasobów | Yes | 

## <a name="scheduler"></a>Scheduler
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| kolekcjach | Yes | 
| przepływy | Yes | 

## <a name="search"></a>Wyszukiwanie
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| searchServices | Yes | 
| resourceHealthMetadata | Nie | 

## <a name="security"></a>Bezpieczeństwo
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| dataCollectionAgents | Nie | 

## <a name="service-bus"></a>Service Bus
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Przestrzenie nazw | Yes | 
| przestrzenie nazw/eventgridfilters | Nie | 

## <a name="service-fabric"></a>Service Fabric
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Klastry | Yes | 
| klastry/aplikacji | Nie | 

## <a name="service-fabric-mesh"></a>Service Fabric Mesh
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| aplikacje | Yes | 
| Sieci | Yes | 
| volumes | Yes | 

## <a name="signalr-service"></a>SignalR Service
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| SignalR | Yes | 

## <a name="site-recovery"></a>Site Recovery
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| SiteRecoveryVault | Yes | 

## <a name="solutions"></a>Rozwiązania
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| aplikacje | Yes | 
| applicationDefinitions | Yes | 
| jitRequests | Yes | 

## <a name="sql-virtual-machine"></a>Maszyny wirtualnej SQL
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| DWVM | Yes | 

## <a name="storage"></a>Magazyn
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| storageAccounts | Yes | 
| storageAccounts/blobServices | Nie | 
| storageAccounts/tableServices | Nie | 
| storageAccounts/queueServices | Nie | 
| storageAccounts/fileServices | Nie | 
| storageAccounts/services | Nie | 
| storageAccounts/services/metricDefinitions | Nie | 

## <a name="storage-sync"></a>Synchronizację magazynu
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| storageSyncServices | Yes | 
| storageSyncServices/syncGroups | Nie | 
| storageSyncServices/syncGroups/cloudEndpoints | Nie | 
| storageSyncServices/syncGroups/serverEndpoints | Nie | 
| storageSyncServices/registeredServers | Nie | 
| storageSyncServices/przepływów pracy | Nie | 

## <a name="storsimple"></a>Usługi Storsimple
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Menedżerowie | Yes | 

## <a name="stream-analytics"></a>Stream Analytics
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| streamingjobs | Yes | 
| streamingjobs/diagnosticSettings | Nie | 
| streamingjobs/metricDefinitions | Nie | 

## <a name="subscription"></a>Subskrypcja
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| SubscriptionDefinitions | Nie | 
| SubscriptionOperations | Nie | 

## <a name="support"></a>Pomoc techniczna
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| supporttickets | Nie | 

## <a name="visual-studio"></a>Visual Studio
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| account | Yes | 
| konto lub projekt docelowy | Yes | 
| konta i rozszerzeń | Yes | 
| account | Yes | 
| konto lub projekt docelowy | Yes | 
| konta i rozszerzeń | Yes | 

## <a name="web"></a>Sieć Web
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Lokacje/wystąpieniach | Nie | 
| Lokacje/miejsc/wystąpieniach | Nie | 
| / wystąpień/rozszerzenia usługi witryny | Nie | 
| / miejsc/wystąpień/rozszerzenia usługi witryny | Nie | 
| publishingUsers | Nie | 
| Sprawdzanie poprawności | Nie | 
| sourceControls | Nie | 
| Lokacje/hostNameBindings | Nie | 
| Lokacje/domainOwnershipIdentifiers | Nie | 
| Lokacje/miejsc/hostNameBindings | Nie | 
| Certyfikaty | Yes | 
| farm serwerów | Yes | 
| farm serwerów/procesów roboczych | Nie | 
| Lokacje | Yes | 
| Lokacje/miejsca | Yes | 
| Lokacje/metryki | Nie | 
| Lokacje/miejsc/metryki | Nie | 
| Lokacje/premieraddons | Yes | 
| hostingEnvironments | Yes | 
| hostingEnvironments/multiRolePools | Nie | 
| hostingEnvironments/workerPools | Nie | 
| hostingEnvironments/metryki | Nie | 
| functions | Nie | 
| deletedSites | Nie | 
| apiManagementAccounts | Nie | 
| apiManagementAccounts/połączeń | Nie | 
| apiManagementAccounts/connectionAcls | Nie | 
| apiManagementAccounts/API/połączeń/connectionAcls | Nie | 
| apiManagementAccounts/API/connectionAcls | Nie | 
| apiManagementAccounts/apiAcls | Nie | 
| apiManagementAccounts/API/apiAcls | Nie | 
| apiManagementAccounts/interfejsów API | Nie | 
| apiManagementAccounts/API/localizedDefinitions | Nie | 
| apiManagementAccounts/API/połączeń | Nie | 
| połączenia | Yes | 
| customApis | Yes | 
| connectionGateways | Yes | 
| billingMeters | Nie | 
| verifyHostingEnvironmentVnet | Nie | 

## <a name="xrm"></a>XRM
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| organizations | Nie | 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak stosowanie tagów do zasobów, zobacz [organizowania zasobów platformy Azure za pomocą tagów](resource-group-using-tags.md).
