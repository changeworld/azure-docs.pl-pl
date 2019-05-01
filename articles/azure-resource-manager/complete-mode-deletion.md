---
title: Usługi Azure Resource Manager w trybie usunięcia według typu zasobów
description: Pokazuje, jak typy zasobów obsługuje usuwania w trybie w szablonach usługi Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 21b3972a96c1601b15c403275474d58873753b08
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
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
| DomainServices | Yes | 
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
| agenci | Nie | 
| anonymousapiusers | Nie | 
| konfiguracja | Nie | 
| dzienniki | Nie | 
| raporty | Nie | 
| services | Nie | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Konfiguracje | Nie | 
| generateRecommendations | Nie | 
| zalecenia | Nie | 
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
| serwerów | Yes | 

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
| automationAccounts/configurations | Yes | 
| automationAccounts/zadań | Nie | 
| automationAccounts/runbooks | Yes | 
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
| b2cDirectories | Yes | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| rejestracje | Yes | 
| registrations/customerSubscriptions | Nie | 
| rejestracje/produktów | Nie | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| batchAccounts | Yes | 

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
| mapApis | Yes | 
| updateCommunicationPreference | Nie | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| BizTalk | Yes | 

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
| botServices | Yes | 
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
| rezerwacje | Nie | 
| zasoby | Nie | 
| validateReservationOrder | Nie | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| edgenodes | Nie | 
| Profile | Yes | 
| Profile/punktów końcowych | Yes | 
| punkty końcowe/profile/customdomains | Nie | 
| punkty końcowe/profile/źródła | Nie | 
| validateProbe | Nie | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| certificateOrders | Yes | 
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
| dyski | Nie | 
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
| dyski | Yes | 
| images | Yes | 
| restorePointCollections | Yes | 
| restorePointCollections/restorePoints | Nie | 
| sharedVMImages | Yes | 
| sharedVMImages/wersji | Yes | 
| migawki | Yes | 
| maszyn wirtualnych | Yes | 
| maszyn wirtualnych/diagnosticSettings | Nie | 
| virtualMachines/extensions | Yes | 
| virtualMachineScaleSets | Yes | 
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
| Opłaty | Nie | 
| CostTags | Nie | 
| Środki na korzystanie z | Nie | 
| zdarzenia | Nie | 
| Prognozy | Nie | 
| partie | Nie | 
| Marketplaces | Nie | 
| Pricesheets | Nie | 
| produkty | Nie | 
| ReservationDetails | Nie | 
| ReservationRecommendations | Nie | 
| ReservationSummaries | Nie | 
| ReservationTransactions | Nie | 
| Tagi | Nie | 
| Warunki | Nie | 
| UsageDetails | Nie | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| containerGroups | Yes | 
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
| rejestry/zadań | Yes | 
| registries/updatePolicies | Nie | 
| rejestry/elementów webhook | Yes | 
| registries/webhooks/getCallbackConfig | Nie | 
| elementy webhook/rejestrów/ping | Nie | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| containerServices | Yes | 
| managedClusters | Yes | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| aplikacje | Yes | 
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
| Łączniki | Yes | 
| Działy | Nie | 
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
| zadania | Yes | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| DataBoxEdgeDevices | Yes | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| obszary robocze | Yes | 
| workspaces/virtualNetworkPeerings | Nie | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| katalogi | Yes | 

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
| fabryki | Yes | 
| fabryki/integrationRuntimes | Nie | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 
| accounts/dataLakeStoreAccounts | Nie | 
| accounts/storageAccounts | Nie | 
| accounts/storageAccounts/containers | Nie | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 
| accounts/eventGridFilters | Nie | 
| konta/firewallRules | Nie | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| services | Yes | 
| usługi/projekty | Yes | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| serwerów | Yes | 
| servers/recoverableServers | Nie | 
| servers/virtualNetworkRules | Nie | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| serwerów | Yes | 
| servers/recoverableServers | Nie | 
| servers/virtualNetworkRules | Nie | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| serwerów | Yes | 
| serwery/doradców | Nie | 
| serwery/queryTexts | Nie | 
| servers/recoverableServers | Nie | 
| servers/topQueryStatistics | Nie | 
| servers/virtualNetworkRules | Nie | 
| serwery/waitStatistics | Nie | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| IotHubs | Yes | 
| IotHubs/eventGridFilters | Nie | 
| ProvisioningServices | Yes | 
| Sposoby użycia | Nie | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Kontrolery | Yes | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Warsztaty | Yes | 
| labs/serviceRunners | Yes | 
| laboratoria/maszyn wirtualnych | Yes | 
| Harmonogramy | Yes | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| databaseAccountNames | Nie | 
| databaseAccounts | Yes | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| domeny | Yes | 
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
| Tematy | Yes | 
| topicTypes | Nie | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Klastry | Yes | 
| przestrzenie nazw | Yes | 
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
| zasoby | Nie | 
| retrieveresourcesbyid | Nie | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| guestConfigurationAssignments | Nie | 
| Oprogramowanie | Nie | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| hanaInstances | Yes | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Klastry | Yes | 
| klastry/aplikacji | Nie | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| zadania | Yes | 

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
| punkt odniesienia | Nie | 
| calculatebaseline | Nie | 
| Składniki | Yes | 
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
| dzienniki | Nie | 
| metricAlerts | Yes |
| migrateToNewPricingModel | Nie | 
| myWorkbooks | Nie | 
| — zapytania | Nie | 
| rollbackToLegacyPricingModel | Nie | 
| scheduledqueryrules | Yes | 
| vmInsightsOnboardingStatuses | Nie | 
| testy internetowe | Yes | 
| skoroszyty | Yes | 

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
| Graph | Yes | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| deletedVaults | Nie | 
| Magazyny | Yes | 
| Magazyny/accessPolicies | Nie | 
| Magazyny/klucze tajne | Nie | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Klastry | Yes | 
| klastry/baz danych | Nie | 
| klastry/baz danych/połączeń danych | Nie | 
| klastry/baz danych/eventhubconnections | Nie | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| labaccounts | Yes | 
| użytkownicy | Nie | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| dzienniki | Nie | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| integrationAccounts | Yes | 
| Przepływy pracy | Yes | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| commitmentPlans | Yes | 
| webServices | Yes | 
| Obszary robocze | Yes | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 
| konta/obszarów roboczych | Yes | 
| obszary robocze/kont/projektów | Yes | 
| teamAccounts | Yes | 
| teamAccounts/obszarów roboczych | Yes | 
| obszary robocze/teamAccounts/projektów | Yes | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| obszary robocze | Yes | 
| obszary robocze/usługi obliczeniowe | Nie | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Tożsamości | Nie | 
| userAssignedIdentities | Yes | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| getEntities | Nie | 
| managementGroups | Nie | 
| zasoby | Nie | 
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
| produkty | Nie | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| classicDevServices | Yes | 
| updateCommunicationPreference | Nie | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Umowy | Nie | 
| offertypes | Nie | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| mediaservices | Yes | 
| mediaservices/accountFilters | Nie | 
| mediaservices/zasobów | Nie | 
| zasobów/mediaservices/assetFilters | Nie | 
| mediaservices/contentKeyPolicies | Nie | 
| mediaservices/eventGridFilters | Nie | 
| mediaservices/liveEventOperations | Nie | 
| mediaservices/liveEvents | Yes | 
| mediaservices/liveEvents/liveOutputs | Nie | 
| mediaservices/liveOutputOperations | Nie | 
| mediaservices/streamingEndpointOperations | Nie | 
| mediaservices/punkty | Yes | 
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
| applicationGateways | Yes | 
| applicationSecurityGroups | Yes | 
| azureFirewallFqdnTags | Nie | 
| azureFirewalls | Yes | 
| bgpServiceCommunities | Nie | 
| połączenia | Yes | 
| ddosCustomPolicies | Yes | 
| ddosProtectionPlans | Yes | 
| dnsOperationStatuses | Nie | 
| dnszones | Yes | 
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
| usługi Frontdoor | Yes | 
| frontdoorWebApplicationFirewallPolicies | Yes | 
| getDnsResourceReference | Nie | 
| interfaceEndpoints | Yes | 
| internalNotify | Nie | 
| loadBalancers | Yes | 
| localNetworkGateways | Yes | 
| natGateways | Yes | 
| networkIntentPolicies | Yes | 
| Interfejsy | Yes | 
| networkProfiles | Yes | 
| networkSecurityGroups | Yes | 
| networkWatchers | Yes | 
| networkWatchers/connectionMonitors | Yes | 
| networkWatchers/lenses | Yes | 
| networkWatchers/pingMeshes | Yes | 
| privateLinkServices | Yes | 
| publicIPAddresses | Yes | 
| publicIPPrefixes | Yes | 
| routeFilters | Yes | 
| routeTables | Yes | 
| serviceEndpointPolicies | Yes | 
| trafficManagerGeographicHierarchies | Nie | 
| trafficmanagerprofiles | Yes | 
| trafficmanagerprofiles/heatMaps | Nie | 
| virtualHubs | Yes | 
| virtualNetworkGateways | Yes | 
| virtualNetworks | Yes | 
| virtualNetworkTaps | Yes | 
| virtualWans | Yes | 
| vpnGateways | Yes | 
| vpnSites | Yes | 
| webApplicationFirewallPolicies | Yes | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| przestrzenie nazw | Yes | 
| namespaces/notificationHubs | Yes | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| urządzenia | Nie | 
| linkTargets | Nie | 
| storageInsightConfigs | Nie | 
| obszary robocze | Yes | 
| workspaces/dataSources | Nie | 
| workspaces/linkedServices | Nie | 
| workspaces/query | Nie | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| managementassociations | Nie | 
| managementconfigurations | Yes | 
| rozwiązania | Yes | 
| wyświetlenia | Yes | 

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
| Pulpity nawigacyjne | Yes | 
| userSettings | Nie | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| workspaceCollections | Yes | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Pojemności | Yes | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| backupProtectedItems | Nie | 
| Magazyny | Yes | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| przestrzenie nazw | Yes | 
| przestrzenie nazw/reguł autoryzacji | Nie | 
| przestrzenie nazw/hybridconnections | Nie | 
| przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie | 
| namespaces/wcfrelays | Nie | 
| przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| zasoby | Nie | 
| subscriptionsStatus | Nie | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| availabilityStatuses | Nie | 
| childAvailabilityStatuses | Nie | 
| childResources | Nie | 
| zdarzenia | Nie | 
| impactedResources | Nie | 
| powiadomienia | Nie | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| wdrożenia | Nie | 
| Wdrożenia/operations | Nie | 
| Łącza | Nie | 
| notifyResourceJobs | Nie | 
| dostawcy | Nie | 
| resourceGroups | Nie | 
| zasoby | Nie | 
| subskrypcji | Nie | 
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
| aplikacje | Yes | 
| saasresources | Nie | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| przepływy | Yes | 
| kolekcjach | Yes | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| resourceHealthMetadata | Nie | 
| searchServices | Yes | 

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
| monitorowanie | Nie | 
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
| przestrzenie nazw | Yes | 
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
| Klastry | Yes | 
| klastry/aplikacji | Nie | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| aplikacje | Yes | 
| Bramy | Yes | 
| Sieci | Yes | 
| wpisy tajne | Yes | 
| volumes | Yes | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| SignalR | Yes | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| applianceDefinitions | Yes | 
| urządzenia | Yes | 
| applicationDefinitions | Yes | 
| aplikacje | Yes | 
| jitRequests | Yes | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| managedInstances | Yes |
| managedInstances/baz danych | Tak (zobacz uwaga poniżej) |
| managedInstances/databases/backupShortTermRetentionPolicies | Nie |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nie |
| managedInstances/databases/vulnerabilityAssessments | Nie |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Nie |
| managedInstances/encryptionProtector | Nie |
| managedInstances/kluczy | Nie |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nie |
| managedInstances/vulnerabilityAssessments | Nie |
| serwerów | Yes | 
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
| SqlVirtualMachineGroups | Yes | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nie | 
| SqlVirtualMachines | Yes | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| storageAccounts | Yes | 
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
| zasoby | Yes | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| Środowiska | Yes | 
| środowiska/accessPolicies | Nie | 
| środowiska/eventsources | Yes | 
| environments/referenceDataSets | Yes | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ zasobu | Usuwanie w trybie pełnym |
| ------------- | ----------- |
| account | Yes | 
| konta i rozszerzeń | Yes | 
| konto lub projekt docelowy | Yes | 

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
| Certyfikaty | Yes | 
| connectionGateways | Yes | 
| połączenia | Yes | 
| customApis | Yes | 
| deletedSites | Nie | 
| functions | Nie | 
| hostingEnvironments | Yes | 
| hostingEnvironments/multiRolePools | Nie | 
| hostingEnvironments/multiRolePools/wystąpieniach | Nie | 
| hostingEnvironments/workerPools | Nie | 
| hostingEnvironments/workerPools/instances | Nie | 
| publishingUsers | Nie | 
| zalecenia | Nie | 
| resourceHealthMetadata | Nie | 
| środowiska uruchomieniowe | Nie | 
| farm serwerów | Yes | 
| farm serwerów/procesów roboczych | Nie | 
| lokacje | Yes | 
| Lokacje/domainOwnershipIdentifiers | Nie | 
| sites/hostNameBindings | Nie | 
| Lokacje/wystąpieniach | Nie | 
| / wystąpień/rozszerzenia usługi witryny | Nie | 
| sites/premieraddons | Yes | 
| Lokacje/zalecenia | Nie | 
| sites/resourceHealthMetadata | Nie | 
| Lokacje/miejsca | Yes | 
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
| DeviceServices | Yes | 

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
