---
title: Usługi Azure Resource Manager w trybie usunięcia według typu zasobów
description: Pokazuje, jak typy zasobów obsługuje usuwania w trybie w szablonach usługi Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 21b3972a96c1601b15c403275474d58873753b08
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712995"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Usuwanie zasobów platformy Azure w przypadku wdrożeń w trybie
W tym artykule opisano, jak typy zasobów obsługuje usuwania, gdy nie w szablonie, które zostało wdrożone w trybie.

Typy zasobów oznaczone `Yes` są usuwane, gdy typ nie jest w szablonie wdrażane w trybie. 

Typy zasobów oznaczone `No` nie są automatycznie usuwane nie w tym szablonie; jednak są one usunięte usunięcie zasobu nadrzędnego. Aby uzyskać pełny opis zachowania, zobacz [tryby wdrażania usługi Azure Resource Manager](deployment-modes.md).

Aby uzyskać te same dane w formacie wartości rozdzielanych przecinkami, Pobierz [ukończenia — tryb deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| DomainServices | Tak | 
| DomainServices/oucontainer | Nie | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| diagnosticSettings | Nie | 
| diagnosticSettingsCategories | Nie | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| supportProviders | Nie | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| aadsupportcases | Nie | 
| addsservices | Nie | 
| Agenci | Nie | 
| anonymousapiusers | Nie | 
| konfiguracja | Nie | 
| logs | Nie | 
| raporty | Nie | 
| services | Nie | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Konfiguracje | Nie | 
| generateRecommendations | Nie | 
| Zalecenia | Nie | 
| pominięć | Nie | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| actionRules | Nie | 
| alerts | Nie | 
| alertsList | Nie | 
| alertsSummary | Nie | 
| alertsSummaryList | Nie | 
| smartDetectorAlertRules | Nie | 
| smartDetectorRuntimeEnvironments | Nie | 
| smartGroups | Nie | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Serwery | Tak | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| reportFeedback | Nie | 
| usługa | Yes | 
| validateServiceName | Nie | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| attestationProviders | Nie | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| classicAdministrators | Nie | 
| denyAssignments | Nie | 
| elevateAccess | Nie | 
| Blokady | Nie | 
| uprawnienia | Nie | 
| policyAssignments | Nie | 
| policyDefinitions | Nie | 
| policySetDefinitions | Nie | 
| providerOperations | Nie | 
| Liczba przypisań ról | Nie | 
| roleDefinitions | Nie | 

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| automationAccounts | Yes | 
| automationAccounts/configurations | Tak | 
| automationAccounts/zadań | Nie | 
| automationAccounts/runbooks | Tak | 
| automationAccounts/softwareUpdateConfigurations | Nie | 
| automationAccounts/webhooks | Nie | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Środowiska | Nie | 
| środowiska/kont | Nie | 
| środowiska/kont/przestrzeni nazw | Nie | 
| środowiska/kont/przestrzenie nazw/konfiguracji | Nie | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| b2cDirectories | Tak | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| rejestracje | Tak | 
| registrations/customerSubscriptions | Nie | 
| rejestracje/produktów | Nie | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| batchAccounts | Tak | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| billingAccounts | Nie | 
| billingAccounts/billingProfiles | Nie | 
| billingAccounts/billingProfiles/billingSubscriptions | Nie | 
| billingAccounts/billingProfiles/invoices | Nie | 
| billingAccounts/billingProfiles/invoices/pricesheet | Nie | 
| billingAccounts/billingProfiles/operationStatus | Nie | 
| billingAccounts/billingProfiles/paymentMethods | Nie | 
| billingAccounts/billingProfiles/policies | Nie | 
| billingAccounts/billingProfiles/pricesheet | Nie | 
| billingAccounts/billingProfiles/products | Nie | 
| billingAccounts/billingProfiles/transactions | Nie | 
| billingAccounts/billingSubscriptions | Nie | 
| billingAccounts/działów | Nie | 
| billingAccounts/eligibleOffers | Nie | 
| billingAccounts/enrollmentAccounts | Nie | 
| billingAccounts/invoices | Nie | 
| billingAccounts/invoiceSections | Nie | 
| billingAccounts/invoiceSections/billingSubscriptions | Nie | 
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Nie | 
| billingAccounts/invoiceSections/importRequests | Nie | 
| billingAccounts/invoiceSections/initiateImportRequest | Nie | 
| billingAccounts/invoiceSections/initiateTransfer | Nie | 
| billingAccounts/invoiceSections/operationStatus | Nie | 
| billingAccounts/invoiceSections/products | Nie | 
| billingAccounts/invoiceSections/transfers | Nie | 
| billingAccounts/products | Nie | 
| billingAccounts/projects | Nie | 
| billingAccounts/projects/billingSubscriptions | Nie | 
| billingAccounts/projects/importRequests | Nie | 
| billingAccounts/projects/initiateImportRequest | Nie | 
| billingAccounts/projects/operationStatus | Nie | 
| billingAccounts/projects/products | Nie | 
| billingAccounts/transakcje | Nie | 
| billingPeriods | Nie | 
| BillingPermissions | Nie | 
| billingProperty | Nie | 
| BillingRoleAssignments | Nie | 
| BillingRoleDefinitions | Nie | 
| CreateBillingRoleAssignment | Nie | 
| działy | Nie | 
| enrollmentAccounts | Nie | 
| importRequests | Nie | 
| importRequests/acceptImportRequest | Nie | 
| importRequests/declineImportRequest | Nie | 
| Faktury | Nie | 
| Transfery | Nie | 
| transfery/acceptTransfer | Nie | 
| transfery/declineTransfer | Nie | 
| transfers/operationStatus | Nie | 
| usagePlans | Nie | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| mapApis | Tak | 
| updateCommunicationPreference | Nie | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| BizTalk | Tak | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| blueprintAssignments | Nie | 
| blueprintAssignments/assignmentOperations | Nie | 
| blueprintAssignments/operations | Nie | 
| Plany | Nie | 
| schematy/artefaktów | Nie | 
| schematy/wersji | Nie | 
| schematy/wersji/artefaktów | Nie | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| botServices | Tak | 
| botServices/kanałów | Nie | 
| botServices/połączeń | Nie | 

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Redis | Yes | 
| RedisConfigDefinition | Nie | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| appliedReservations | Nie | 
| calculatePrice | Nie | 
| katalogi | Nie | 
| commercialReservationOrders | Nie | 
| reservationOrders | Nie | 
| reservationOrders/calculateRefund | Nie | 
| reservationOrders/merge | Nie | 
| reservationOrders/rezerwacji | Nie | 
| reservationOrders/reservations/revisions | Nie | 
| reservationOrders/return | Nie | 
| reservationOrders/split | Nie | 
| reservationOrders/wymiany | Nie | 
| zastrzeżenia | Nie | 
| Zasoby | Nie | 
| validateReservationOrder | Nie | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| edgenodes | Nie | 
| Profile | Tak | 
| Profile/punktów końcowych | Tak | 
| punkty końcowe/profile/customdomains | Nie | 
| punkty końcowe/profile/źródła | Nie | 
| validateProbe | Nie | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| certificateOrders | Tak | 
| zamówień certyfikatów/certyfikaty | Nie | 
| validateCertificateRegistrationInformation | Nie | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Możliwości | Nie | 
| domainNames | Nie | 
| domainNames/funkcji | Nie | 
| domainNames/internalLoadBalancers | Nie | 
| domainNames/serviceCertificates | Nie | 
| domainNames/miejsca | Nie | 
| domainNames/miejsc/role | Nie | 
| moveSubscriptionResources | Nie | 
| operatingSystemFamilies | Nie | 
| operatingSystems | Nie | 
| quotas | Nie | 
| resourceTypes | Nie | 
| validateSubscriptionMoveAvailability | Nie | 
| maszyn wirtualnych | Nie | 
| maszyn wirtualnych/diagnosticSettings | Nie | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| classicInfrastructureResources | Nie | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Możliwości | Nie | 
| expressRouteCrossConnections | Nie | 
| expressRouteCrossConnections/peerings | Nie | 
| gatewaySupportedDevices | Nie | 
| networkSecurityGroups | Nie | 
| quotas | Nie | 
| reservedIps | Nie | 
| virtualNetworks | Nie | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nie | 
| virtualNetworks/virtualNetworkPeerings | Nie | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Możliwości | Nie | 
| Dyski | Nie | 
| images | Nie | 
| osImages | Nie | 
| osPlatformImages | Nie | 
| publicImages | Nie | 
| quotas | Nie | 
| storageAccounts | Nie | 
| storageAccounts/services | Nie | 
| storageAccounts/services/diagnosticSettings | Nie | 
| storageAccounts/vmImages | Nie | 
| Dozwolona | Nie | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Usługi RateCard | Nie | 
| UsageAggregates | Nie | 

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| availabilitySets | Yes | 
| Dyski | Tak | 
| images | Tak | 
| restorePointCollections | Tak | 
| restorePointCollections/restorePoints | Nie | 
| sharedVMImages | Yes | 
| sharedVMImages/wersji | Tak | 
| Migawki | Tak | 
| maszyn wirtualnych | Yes | 
| maszyn wirtualnych/diagnosticSettings | Nie | 
| virtualMachines/extensions | Tak | 
| virtualMachineScaleSets | Tak | 
| virtualMachineScaleSets/extensions | Nie | 
| virtualMachineScaleSets/networkInterfaces | Nie | 
| virtualMachineScaleSets/publicIPAddresses | Nie | 
| virtualMachineScaleSets/virtualMachines | Nie | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nie | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| AggregatedCost | Nie | 
| Salda | Nie | 
| Budżety | Nie | 
| Opłaty za | Nie | 
| CostTags | Nie | 
| Środki na korzystanie z | Nie | 
| zdarzenia | Nie | 
| Prognozy | Nie | 
| partie | Nie | 
| Marketplaces | Nie | 
| Pricesheets | Nie | 
| Produkty | Nie | 
| ReservationDetails | Nie | 
| ReservationRecommendations | Nie | 
| ReservationSummaries | Nie | 
| ReservationTransactions | Nie | 
| `Tags` | Nie | 
| Warunki | Nie | 
| UsageDetails | Nie | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| containerGroups | Tak | 
| serviceAssociationLinks | Nie | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| rejestry | Yes | 
| rejestry/kompilacji | Nie | 
| rejestry/kompilacji/anulować | Nie | 
| registries/builds/getLogLink | Nie | 
| registries/buildTasks | Yes | 
| registries/buildTasks/steps | Nie | 
| registries/eventGridFilters | Nie | 
| registries/getBuildSourceUploadUrl | Nie | 
| registries/GetCredentials | Nie | 
| registries/importImage | Nie | 
| rejestry/queueBuild | Nie | 
| registries/regenerateCredential | Nie | 
| registries/regenerateCredentials | Nie | 
| rejestry/replikacji | Yes | 
| rejestry/przebiegów | Nie | 
| rejestry/uruchomienia/anulować | Nie | 
| registries/scheduleRun | Nie | 
| rejestry/zadań | Tak | 
| registries/updatePolicies | Nie | 
| rejestry/elementów webhook | Tak | 
| registries/webhooks/getCallbackConfig | Nie | 
| elementy webhook/rejestrów/ping | Nie | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| containerServices | Tak | 
| managedClusters | Yes | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| aplikacje | Tak | 
| updateCommunicationPreference | Nie | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Alerty | Nie | 
| BillingAccounts | Nie | 
| Łączniki | Tak | 
| działy | Nie | 
| Wymiary | Nie | 
| enrollmentAccounts | Nie | 
| Zapytanie | Nie | 
| register | Nie | 
| Reportconfigs | Nie | 
| Raporty | Nie | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Koncentratory | Yes | 
| hubs/authorizationPolicies | Nie | 
| Koncentratory/łączników | Nie | 
| Koncentratory/łączników/mapowania | Nie | 
| hubs/interactions | Nie | 
| hubs/kpi | Nie | 
| hubs/links | Nie | 
| Koncentratory/profile | Nie | 
| Koncentratory/liczba przypisań ról | Nie | 
| Koncentratory/role | Nie | 
| hubs/suggestTypeSchema | Nie | 
| Koncentratory/widoków | Nie | 
| hubs/widgetTypes | Nie | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Zadania | Tak | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| DataBoxEdgeDevices | Yes | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Obszary robocze | Yes | 
| workspaces/virtualNetworkPeerings | Nie | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| katalogi | Tak | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| connectionManagers | Yes | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| dataFactories | Yes | 
| dataFactories/diagnosticSettings | Nie | 
| dataFactorySchema | Nie | 
| fabryki | Tak | 
| fabryki/integrationRuntimes | Nie | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Tak | 
| accounts/dataLakeStoreAccounts | Nie | 
| accounts/storageAccounts | Nie | 
| accounts/storageAccounts/containers | Nie | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Tak | 
| accounts/eventGridFilters | Nie | 
| konta/firewallRules | Nie | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| services | Tak | 
| usługi/projekty | Tak | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Serwery | Yes | 
| servers/recoverableServers | Nie | 
| servers/virtualNetworkRules | Nie | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Serwery | Tak | 
| servers/recoverableServers | Nie | 
| servers/virtualNetworkRules | Nie | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Serwery | Tak | 
| serwery/doradców | Nie | 
| serwery/queryTexts | Nie | 
| servers/recoverableServers | Nie | 
| servers/topQueryStatistics | Nie | 
| servers/virtualNetworkRules | Nie | 
| serwery/waitStatistics | Nie | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| IotHubs | Tak | 
| IotHubs/eventGridFilters | Nie | 
| ProvisioningServices | Yes | 
| Sposoby użycia | Nie | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Kontrolery | Tak | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Warsztaty | Tak | 
| labs/serviceRunners | Tak | 
| laboratoria/maszyn wirtualnych | Yes | 
| Harmonogramy | Tak | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| databaseAccountNames | Nie | 
| databaseAccounts | Tak | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| domeny | Tak | 
| domeny/domainOwnershipIdentifiers | Nie | 
| generateSsoRequest | Nie | 
| topLevelDomains | Nie | 
| validateDomainRegistrationInformation | Nie | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| lcsprojects | Nie | 
| lcsprojects/clouddeployments | Nie | 
| lcsprojects/łączników | Nie | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| domeny | Yes | 
| domeny/tematów | Nie | 
| eventSubscriptions | Nie | 
| extensionTopics | Nie | 
| Tematy | Tak | 
| topicTypes | Nie | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Klastry | Tak | 
| Przestrzenie nazw | Tak | 
| przestrzenie nazw/reguł autoryzacji | Nie | 
| przestrzenie nazw/disasterrecoveryconfigs | Nie | 
| przestrzenie nazw/eventhubs | Nie | 
| przestrzenie nazw/eventhubs/reguł autoryzacji | Nie | 
| przestrzenie nazw/eventhubs/consumergroups | Nie | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| danych | Nie | 
| dostawcy | Nie | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Rejestrowanie | Nie | 
| galleryitems | Nie | 
| generateartifactaccessuri | Nie | 
| myareas | Nie | 
| myareas/obszarów | Nie | 
| myareas/obszarów/obszarów | Nie | 
| myareas/areas/areas/galleryitems | Nie | 
| myareas/areas/galleryitems | Nie | 
| myareas/galleryitems | Nie | 
| register | Nie | 
| Zasoby | Nie | 
| retrieveresourcesbyid | Nie | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| guestConfigurationAssignments | Nie | 
| Oprogramowanie | Nie | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| hanaInstances | Tak | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Klastry | Tak | 
| klastry/aplikacji | Nie | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Zadania | Yes | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| labelGroups | Nie | 
| labelGroups/etykiety | Nie | 
| labelGroups/labels/conditions | Nie | 
| labelGroups/labels/subLabels | Nie | 
| labelGroups/labels/subLabels/conditions | Nie | 

## <a name="microsoftinsights"></a>microsoft.insights
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| actiongroups | Yes | 
| activityLogAlerts | Yes | 
| alertrules | Yes | 
| automatedExportSettings | Nie | 
| wartość autoscalesettings | Yes | 
| Punkt odniesienia | Nie | 
| calculatebaseline | Nie | 
| Składniki | Tak | 
| składniki/zdarzenia | Nie | 
| składniki/pricingPlans | Nie | 
| składniki/zapytania | Nie | 
| diagnosticSettings | Nie | 
| diagnosticSettingsCategories | Nie | 
| eventCategories | Nie | 
| eventtypes | Nie | 
| extendedDiagnosticSettings | Nie | 
| logDefinitions | Nie | 
| logprofiles | Nie | 
| logs | Nie | 
| metricAlerts | Tak |
| migrateToNewPricingModel | Nie | 
| myWorkbooks | Nie | 
| — zapytania | Nie | 
| rollbackToLegacyPricingModel | Nie | 
| scheduledqueryrules | Tak | 
| vmInsightsOnboardingStatuses | Nie | 
| testy internetowe | Yes | 
| Skoroszyty | Yes | 

## <a name="microsoftintune"></a>Microsoft.Intune
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| diagnosticSettings | Nie | 
| diagnosticSettingsCategories | Nie | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| IoTApps | Yes | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Graph | Tak | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| deletedVaults | Nie | 
| Magazyny | Tak | 
| Magazyny/accessPolicies | Nie | 
| Magazyny/klucze tajne | Nie | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Klastry | Tak | 
| klastry/baz danych | Nie | 
| klastry/baz danych/połączeń danych | Nie | 
| klastry/baz danych/eventhubconnections | Nie | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| labaccounts | Tak | 
| użytkownicy | Nie | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Tak | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Tak | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| logs | Nie | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| integrationAccounts | Tak | 
| Przepływy pracy | Yes | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| commitmentPlans | Tak | 
| webServices | Tak | 
| Obszary robocze | Tak | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Tak | 
| konta/obszarów roboczych | Tak | 
| obszary robocze/kont/projektów | Tak | 
| teamAccounts | Tak | 
| teamAccounts/obszarów roboczych | Yes | 
| obszary robocze/teamAccounts/projektów | Tak | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Tak | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Obszary robocze | Tak | 
| obszary robocze/usługi obliczeniowe | Nie | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Tożsamości | Nie | 
| userAssignedIdentities | Tak | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| getEntities | Nie | 
| managementGroups | Nie | 
| Zasoby | Nie | 
| startTenantBackfill | Nie | 
| tenantBackfillStatus | Nie | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 
| accounts/eventGridFilters | Nie | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Oferty | Nie | 
| offerTypes | Nie | 
| offerTypes/wydawcy | Nie | 
| wydawcy/offerTypes/oferty | Nie | 
| offerTypes/wydawcy/oferty/plany | Nie | 
| offerTypes/wydawcy/oferty/planów/umowy | Nie | 
| offerTypes/publishers/offers/plans/configs | Nie | 
| offerTypes/publishers/offers/plans/configs/importImage | Nie | 
| privategalleryitems | Nie | 
| Produkty | Nie | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| classicDevServices | Tak | 
| updateCommunicationPreference | Nie | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Umowy | Nie | 
| offertypes | Nie | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| mediaservices | Tak | 
| mediaservices/accountFilters | Nie | 
| mediaservices/zasobów | Nie | 
| zasobów/mediaservices/assetFilters | Nie | 
| mediaservices/contentKeyPolicies | Nie | 
| mediaservices/eventGridFilters | Nie | 
| mediaservices/liveEventOperations | Nie | 
| mediaservices/liveEvents | Tak | 
| mediaservices/liveEvents/liveOutputs | Nie | 
| mediaservices/liveOutputOperations | Nie | 
| mediaservices/streamingEndpointOperations | Nie | 
| mediaservices/punkty | Tak | 
| mediaservices/streamingLocators | Nie | 
| mediaservices/streamingPolicies | Nie | 
| mediaservices/przekształceń | Nie | 
| zadania mediaservices/przekształceń | Nie | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Projekty | Yes | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| applicationGateways | Tak | 
| applicationSecurityGroups | Tak | 
| azureFirewallFqdnTags | Nie | 
| azureFirewalls | Yes | 
| bgpServiceCommunities | Nie | 
| Połączenia | Yes | 
| ddosCustomPolicies | Tak | 
| ddosProtectionPlans | Tak | 
| dnsOperationStatuses | Nie | 
| dnszones | Tak | 
| dnszones/A | Nie | 
| dnszones/AAAA | Nie | 
| dnszones/all | Nie | 
| dnszones/CAA | Nie | 
| dnszones/CNAME | Nie | 
| dnszones/MX | Nie | 
| dnszones/NS | Nie | 
| dnszones/PTR | Nie | 
| dnszones/recordsets | Nie | 
| dnszones/SOA | Nie | 
| dnszones/SRV | Nie | 
| dnszones/TXT | Nie | 
| expressRouteCircuits | Yes | 
| expressRouteServiceProviders | Nie | 
| frontdoors | Yes | 
| frontdoorWebApplicationFirewallPolicies | Yes | 
| getDnsResourceReference | Nie | 
| interfaceEndpoints | Yes | 
| internalNotify | Nie | 
| loadBalancers | Tak | 
| localNetworkGateways | Tak | 
| natGateways | Tak | 
| networkIntentPolicies | Tak | 
| Interfejsy | Tak | 
| networkProfiles | Tak | 
| networkSecurityGroups | Tak | 
| networkWatchers | Yes | 
| networkWatchers/connectionMonitors | Tak | 
| networkWatchers/lenses | Yes | 
| networkWatchers/pingMeshes | Tak | 
| privateLinkServices | Yes | 
| publicIPAddresses | Tak | 
| publicIPPrefixes | Tak | 
| routeFilters | Tak | 
| routeTables | Tak | 
| serviceEndpointPolicies | Tak | 
| trafficManagerGeographicHierarchies | Nie | 
| trafficmanagerprofiles | Yes | 
| trafficmanagerprofiles/heatMaps | Nie | 
| virtualHubs | Yes | 
| virtualNetworkGateways | Tak | 
| virtualNetworks | Yes | 
| virtualNetworkTaps | Tak | 
| virtualWans | Yes | 
| vpnGateways | Tak | 
| vpnSites | Tak | 
| webApplicationFirewallPolicies | Tak | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Przestrzenie nazw | Yes | 
| namespaces/notificationHubs | Tak | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| urządzenia | Nie | 
| linkTargets | Nie | 
| storageInsightConfigs | Nie | 
| Obszary robocze | Tak | 
| workspaces/dataSources | Nie | 
| workspaces/linkedServices | Nie | 
| workspaces/query | Nie | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| managementassociations | Nie | 
| managementconfigurations | Yes | 
| rozwiązania | Tak | 
| Widoki | Yes | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| policyEvents | Nie | 
| policyStates | Nie | 
| policyTrackedResources | Nie | 
| korygowania funkcję | Nie | 

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| konsole | Nie | 
| Pulpity nawigacyjne | Tak | 
| userSettings | Nie | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| workspaceCollections | Yes | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Pojemności | Tak | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| backupProtectedItems | Nie | 
| Magazyny | Tak | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Przestrzenie nazw | Tak | 
| przestrzenie nazw/reguł autoryzacji | Nie | 
| przestrzenie nazw/hybridconnections | Nie | 
| przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie | 
| namespaces/wcfrelays | Nie | 
| przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Zasoby | Nie | 
| subscriptionsStatus | Nie | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| availabilityStatuses | Nie | 
| childAvailabilityStatuses | Nie | 
| childResources | Nie | 
| zdarzenia | Nie | 
| impactedResources | Nie | 
| Powiadomienia | Nie | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Wdrożenia | Nie | 
| Wdrożenia/operations | Nie | 
| Łącza | Nie | 
| notifyResourceJobs | Nie | 
| dostawcy | Nie | 
| resourceGroups | Nie | 
| Zasoby | Nie | 
| Subskrypcje | Nie | 
| Subskrypcje/dostawców | Nie | 
| subscriptions/resourceGroups | Nie | 
| zasobów/resourcegroups/subskrypcji | Nie | 
| Zasobów/subskrypcji | Nie | 
| subscriptions/tagnames | Nie | 
| subscriptions/tagNames/tagValues | Nie | 
| Dzierżaw | Nie | 

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| aplikacje | Tak | 
| saasresources | Nie | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| przepływy | Tak | 
| kolekcjach | Yes | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| resourceHealthMetadata | Nie | 
| searchServices | Tak | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| advancedThreatProtectionSettings | Nie | 
| alerts | Nie | 
| allowedConnections | Nie | 
| urządzenia | Nie | 
| applicationWhitelistings | Nie | 
| AutoProvisioningSettings | Nie | 
| Zachowania zgodności z prawnymi | Nie | 
| dataCollectionAgents | Nie | 
| discoveredSecuritySolutions | Nie | 
| externalSecuritySolutions | Nie | 
| InformationProtectionPolicies | Nie | 
| jitNetworkAccessPolicies | Nie | 
| Monitorowanie | Nie | 
| monitorowanie/ochrony przed złośliwym kodem | Nie | 
| monitorowanie/punkt odniesienia | Nie | 
| monitorowanie poprawki | Nie | 
| Zasady | Nie | 
| cen | Nie | 
| securityContacts | Nie | 
| securitySolutions | Nie | 
| securitySolutionsReferenceData | Nie | 
| securityStatus | Nie | 
| securityStatus/punktów końcowych | Nie | 
| securityStatus/subnets | Nie | 
| securityStatus/maszyn wirtualnych | Nie | 
| securityStatuses | Nie | 
| securityStatusesSummaries | Nie | 
| settings | Nie | 
| zadania | Nie | 
| Topologie | Nie | 
| workspaceSettings | Nie | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| diagnosticSettings | Nie | 
| diagnosticSettingsCategories | Nie | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Przestrzenie nazw | Tak | 
| przestrzenie nazw/reguł autoryzacji | Nie | 
| przestrzenie nazw/disasterrecoveryconfigs | Nie | 
| przestrzenie nazw/eventgridfilters | Nie | 
| namespaces/queues | Nie | 
| przestrzenie nazw/kolejki/reguł autoryzacji | Nie | 
| przestrzenie nazw/tematów | Nie | 
| przestrzenie nazw/tematów/reguł autoryzacji | Nie | 
| przestrzenie nazw/tematów/subskrypcji | Nie | 
| przestrzenie nazw/tematów/subskrypcji/reguły | Nie | 
| premiumMessagingRegions | Nie | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Klastry | Tak | 
| klastry/aplikacji | Nie | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| aplikacje | Tak | 
| Bramy | Tak | 
| Sieci | Yes | 
| wpisy tajne | Yes | 
| volumes | Tak | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| SignalR | Tak | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| applianceDefinitions | Yes | 
| urządzenia | Tak | 
| applicationDefinitions | Yes | 
| aplikacje | Tak | 
| jitRequests | Yes | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| managedInstances | Tak |
| managedInstances/baz danych | Tak (zobacz uwaga poniżej) |
| managedInstances/databases/backupShortTermRetentionPolicies | Nie |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nie |
| managedInstances/databases/vulnerabilityAssessments | Nie |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Nie |
| managedInstances/encryptionProtector | Nie |
| managedInstances/kluczy | Nie |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nie |
| managedInstances/vulnerabilityAssessments | Nie |
| Serwery | Tak | 
| serwery/administratorów | Nie | 
| serwery/communicationLinks | Nie | 
| serwery/baz danych | Tak (zobacz uwaga poniżej) | 
| servers/encryptionProtector | Nie | 
| serwery/firewallRules | Nie | 
| serwery/kluczy | Nie | 
| servers/restorableDroppedDatabases | Nie | 
| serwery/serviceobjectives | Nie | 
| serwery/tdeCertificates | Nie | 

> [!NOTE]
> Wzorzec bazy danych nie obsługuje tagi, ale innych baz danych, w tym w przypadku baz danych magazynu danych obsługuje tagi.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| SqlVirtualMachineGroups | Tak | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nie | 
| SqlVirtualMachines | Yes | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| storageAccounts | Tak | 
| storageAccounts/blobServices | Nie | 
| storageAccounts/fileServices | Nie | 
| storageAccounts/queueServices | Nie | 
| storageAccounts/services | Nie | 
| storageAccounts/tableServices | Nie | 
| Sposoby użycia | Nie | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| storageSyncServices | Yes | 
| storageSyncServices/registeredServers | Nie | 
| storageSyncServices/syncGroups | Nie | 
| storageSyncServices/syncGroups/cloudEndpoints | Nie | 
| storageSyncServices/syncGroups/serverEndpoints | Nie | 
| storageSyncServices/workflows | Nie | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Menedżerowie | Yes | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| streamingjobs | Tak (zobacz uwaga poniżej) | 
| streamingjobs/diagnosticSettings | Nie | 

> [!NOTE]
> Nie można dodać tag, gdy streamingjobs jest uruchomiony. Zatrzymaj zasobów, aby dodać tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| CreateSubscription | Nie | 
| SubscriptionDefinitions | Nie | 
| SubscriptionOperations | Nie | 

## <a name="microsoftsupport"></a>microsoft.support
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| supporttickets | Nie | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| providerRegistrations | Yes | 
| Zasoby | Tak | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Środowiska | Tak | 
| środowiska/accessPolicies | Nie | 
| środowiska/eventsources | Tak | 
| environments/referenceDataSets | Tak | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| account | Tak | 
| konta i rozszerzeń | Tak | 
| konto lub projekt docelowy | Tak | 

## <a name="microsoftweb"></a>Microsoft.Web
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| apiManagementAccounts | Nie | 
| apiManagementAccounts/apiAcls | Nie | 
| apiManagementAccounts/apis | Nie | 
| apiManagementAccounts/apis/apiAcls | Nie | 
| apiManagementAccounts/apis/connectionAcls | Nie | 
| apiManagementAccounts/apis/connections | Nie | 
| apiManagementAccounts/apis/connections/connectionAcls | Nie | 
| apiManagementAccounts/apis/localizedDefinitions | Nie | 
| apiManagementAccounts/connectionAcls | Nie | 
| apiManagementAccounts/connections | Nie | 
| billingMeters | Nie | 
| Certyfikaty | Tak | 
| connectionGateways | Yes | 
| Połączenia | Yes | 
| customApis | Tak | 
| deletedSites | Nie | 
| — funkcje | Nie | 
| hostingEnvironments | Yes | 
| hostingEnvironments/multiRolePools | Nie | 
| hostingEnvironments/multiRolePools/wystąpieniach | Nie | 
| hostingEnvironments/workerPools | Nie | 
| hostingEnvironments/workerPools/instances | Nie | 
| publishingUsers | Nie | 
| Zalecenia | Nie | 
| resourceHealthMetadata | Nie | 
| środowiska uruchomieniowe | Nie | 
| farm serwerów | Tak | 
| farm serwerów/procesów roboczych | Nie | 
| Lokacje | Yes | 
| Lokacje/domainOwnershipIdentifiers | Nie | 
| sites/hostNameBindings | Nie | 
| Lokacje/wystąpieniach | Nie | 
| / wystąpień/rozszerzenia usługi witryny | Nie | 
| sites/premieraddons | Tak | 
| Lokacje/zalecenia | Nie | 
| sites/resourceHealthMetadata | Nie | 
| Lokacje/miejsca | Tak | 
| sites/slots/hostNameBindings | Nie | 
| Lokacje/miejsc/wystąpieniach | Nie | 
| / miejsc/wystąpień/rozszerzenia usługi witryny | Nie | 
| sourceControls | Nie | 
| Sprawdzanie poprawności | Nie | 
| verifyHostingEnvironmentVnet | Nie | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| diagnosticSettings | Nie | 
| diagnosticSettingsCategories | Nie | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| DeviceServices | Tak | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Składniki | Nie | 
| componentsSummary | Nie | 
| monitorInstances | Nie | 
| monitorInstancesSummary | Nie | 
| Monitory | Nie | 
| notificationSettings | Nie | 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak stosowanie tagów do zasobów, zobacz [organizowania zasobów platformy Azure za pomocą tagów](resource-group-using-tags.md).
