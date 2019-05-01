---
title: Menedżer zasobów tag pomocy technicznej platformy Azure dla zasobów
description: Pokazuje, jakie typy zasobów platformy Azure obsługuje tagi. Zawiera szczegółowe informacje dla wszystkich usług platformy Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/26/2019
ms.author: tomfitz
ms.openlocfilehash: 32156f39d36d8485c76dada5fd00f9ea0918598c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575457"
---
# <a name="tag-support-for-azure-resources"></a>Obsługa tagów dla zasobów platformy Azure
W tym artykule opisano, czy typ zasobu obsługuje [tagi](resource-group-using-tags.md).

Aby uzyskać te same dane w formacie wartości rozdzielanych przecinkami, Pobierz [tag support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| DomainServices | Yes | 
| DomainServices/oucontainer | Nie | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| diagnosticSettings | Nie | 
| diagnosticSettingsCategories | Nie | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| supportProviders | Nie | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Konfiguracje | Nie | 
| generateRecommendations | Nie | 
| zalecenia | Nie | 
| pominięć | Nie | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| serwerów | Yes | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| reportFeedback | Nie | 
| usługa | Yes | 
| validateServiceName | Nie | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| attestationProviders | Nie | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| automationAccounts | Yes | 
| automationAccounts/configurations | Yes | 
| automationAccounts/zadań | Nie | 
| automationAccounts/runbooks | Yes | 
| automationAccounts/softwareUpdateConfigurations | Nie | 
| automationAccounts/webhooks | Nie | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Środowiska | Nie | 
| środowiska/kont | Nie | 
| środowiska/kont/przestrzeni nazw | Nie | 
| środowiska/kont/przestrzenie nazw/konfiguracji | Nie | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| b2cDirectories | Yes | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| rejestracje | Yes | 
| registrations/customerSubscriptions | Nie | 
| rejestracje/produktów | Nie | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| batchAccounts | Yes | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| mapApis | Yes | 
| updateCommunicationPreference | Nie | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| BizTalk | Yes | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| blueprintAssignments | Nie | 
| blueprintAssignments/assignmentOperations | Nie | 
| blueprintAssignments/operations | Nie | 
| Plany | Nie | 
| schematy/artefaktów | Nie | 
| schematy/wersji | Nie | 
| schematy/wersji/artefaktów | Nie | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| botServices | Yes | 
| botServices/kanałów | Nie | 
| botServices/połączeń | Nie | 

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Redis | Yes | 
| RedisConfigDefinition | Nie | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| edgenodes | Nie | 
| Profile | Yes | 
| Profile/punktów końcowych | Yes | 
| punkty końcowe/profile/customdomains | Nie | 
| punkty końcowe/profile/źródła | Nie | 
| validateProbe | Nie | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| certificateOrders | Yes | 
| zamówień certyfikatów/certyfikaty | Nie | 
| validateCertificateRegistrationInformation | Nie | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| classicInfrastructureResources | Nie | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Usługi RateCard | Nie | 
| UsageAggregates | Nie | 

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| containerGroups | Yes | 
| serviceAssociationLinks | Nie | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| containerServices | Yes | 
| managedClusters | Yes | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| aplikacje | Yes | 
| updateCommunicationPreference | Nie | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| zadania | Yes | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| DataBoxEdgeDevices | Yes | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| obszary robocze | Yes | 
| workspaces/virtualNetworkPeerings | Nie | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| katalogi | Yes | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| connectionManagers | Yes | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| dataFactories | Yes | 
| dataFactories/diagnosticSettings | Nie | 
| dataFactorySchema | Nie | 
| fabryki | Yes | 
| fabryki/integrationRuntimes | Nie | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 
| accounts/dataLakeStoreAccounts | Nie | 
| accounts/storageAccounts | Nie | 
| accounts/storageAccounts/containers | Nie | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 
| accounts/eventGridFilters | Nie | 
| konta/firewallRules | Nie | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| services | Yes | 
| usługi/projekty | Yes | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| serwerów | Yes | 
| servers/recoverableServers | Nie | 
| servers/virtualNetworkRules | Nie | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| serwerów | Yes | 
| servers/recoverableServers | Nie | 
| servers/virtualNetworkRules | Nie | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| serwerów | Yes | 
| serwery/doradców | Nie | 
| serwery/queryTexts | Nie | 
| servers/recoverableServers | Nie | 
| servers/topQueryStatistics | Nie | 
| servers/virtualNetworkRules | Nie | 
| serwery/waitStatistics | Nie | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| IotHubs | Yes | 
| IotHubs/eventGridFilters | Nie | 
| ProvisioningServices | Yes | 
| Sposoby użycia | Nie | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Kontrolery | Yes | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Warsztaty | Yes | 
| labs/serviceRunners | Yes | 
| laboratoria/maszyn wirtualnych | Yes | 
| Harmonogramy | Yes | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| databaseAccountNames | Nie | 
| databaseAccounts | Yes | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| domeny | Yes | 
| domeny/domainOwnershipIdentifiers | Nie | 
| generateSsoRequest | Nie | 
| topLevelDomains | Nie | 
| validateDomainRegistrationInformation | Nie | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| lcsprojects | Nie | 
| lcsprojects/clouddeployments | Nie | 
| lcsprojects/łączników | Nie | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| domeny | Yes | 
| domeny/tematów | Nie | 
| eventSubscriptions | Nie | 
| extensionTopics | Nie | 
| Tematy | Yes | 
| topicTypes | Nie | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Klastry | Yes | 
| przestrzenie nazw | Yes | 
| przestrzenie nazw/reguł autoryzacji | Nie | 
| przestrzenie nazw/disasterrecoveryconfigs | Nie | 
| przestrzenie nazw/eventhubs | Nie | 
| przestrzenie nazw/eventhubs/reguł autoryzacji | Nie | 
| przestrzenie nazw/eventhubs/consumergroups | Nie | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| danych | Nie | 
| dostawcy | Nie | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| guestConfigurationAssignments | Nie | 
| Oprogramowanie | Nie | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| hanaInstances | Yes | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Klastry | Yes | 
| klastry/aplikacji | Nie | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| zadania | Yes | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| labelGroups | Nie | 
| labelGroups/etykiety | Nie | 
| labelGroups/labels/conditions | Nie | 
| labelGroups/labels/subLabels | Nie | 
| labelGroups/labels/subLabels/conditions | Nie | 

## <a name="microsoftinsights"></a>microsoft.insights
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| diagnosticSettings | Nie | 
| diagnosticSettingsCategories | Nie | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| IoTApps | Yes | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Graph | Yes | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| deletedVaults | Nie | 
| Magazyny | Yes | 
| Magazyny/accessPolicies | Nie | 
| Magazyny/klucze tajne | Nie | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Klastry | Yes | 
| klastry/baz danych | Nie | 
| klastry/baz danych/połączeń danych | Nie | 
| klastry/baz danych/eventhubconnections | Nie | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| labaccounts | Yes | 
| użytkownicy | Nie | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| dzienniki | Nie | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| integrationAccounts | Yes | 
| Przepływy pracy | Yes | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| commitmentPlans | Yes | 
| webServices | Yes | 
| Obszary robocze | Yes | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 
| konta/obszarów roboczych | Yes | 
| obszary robocze/kont/projektów | Yes | 
| teamAccounts | Yes | 
| teamAccounts/obszarów roboczych | Yes | 
| obszary robocze/teamAccounts/projektów | Yes | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| obszary robocze | Yes | 
| obszary robocze/usługi obliczeniowe | Nie | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Tożsamości | Nie | 
| userAssignedIdentities | Yes | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| getEntities | Nie | 
| managementGroups | Nie | 
| zasoby | Nie | 
| startTenantBackfill | Nie | 
| tenantBackfillStatus | Nie | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 
| accounts/eventGridFilters | Nie | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| classicDevServices | Yes | 
| updateCommunicationPreference | Nie | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Umowy | Nie | 
| offertypes | Nie | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Projekty | Yes | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ zasobu | Obsługa tagów |
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
| expressRouteCircuits | Tak (wartość tagu nie wyglądają obecnie w raporcie kosztów) | 
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
| networkWatchers | Tak (wartość tagu nie wyglądają obecnie w raporcie kosztów) | 
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
| virtualNetworks/subnets | Nie | 
| virtualNetworkTaps | Yes | 
| virtualWans | Yes | 
| vpnGateways | Tak (wartość tagu nie wyglądają obecnie w raporcie kosztów) | 
| vpnSites | Yes | 
| webApplicationFirewallPolicies | Yes | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| przestrzenie nazw | Yes | 
| namespaces/notificationHubs | Yes | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| urządzenia | Nie | 
| linkTargets | Nie | 
| storageInsightConfigs | Nie | 
| obszary robocze | Yes | 
| workspaces/dataSources | Nie | 
| workspaces/linkedServices | Nie | 
| workspaces/query | Nie | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| managementassociations | Nie | 
| managementconfigurations | Yes | 
| rozwiązania | Yes | 
| wyświetlenia | Yes | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| policyEvents | Nie | 
| policyStates | Nie | 
| policyTrackedResources | Nie | 
| korygowania funkcję | Nie | 

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| konsole | Nie | 
| Pulpity nawigacyjne | Yes | 
| userSettings | Nie | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| workspaceCollections | Yes | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Pojemności | Yes | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| accounts | Yes | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| backupProtectedItems | Nie | 
| Magazyny | Yes | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| przestrzenie nazw | Yes | 
| przestrzenie nazw/reguł autoryzacji | Nie | 
| przestrzenie nazw/hybridconnections | Nie | 
| przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie | 
| namespaces/wcfrelays | Nie | 
| przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| zasoby | Nie | 
| subscriptionsStatus | Nie | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| availabilityStatuses | Nie | 
| childAvailabilityStatuses | Nie | 
| childResources | Nie | 
| zdarzenia | Nie | 
| impactedResources | Nie | 
| powiadomienia | Nie | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| aplikacje | Yes | 
| saasresources | Nie | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| przepływy | Yes | 
| kolekcjach | Yes | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| resourceHealthMetadata | Nie | 
| searchServices | Yes | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| diagnosticSettings | Nie | 
| diagnosticSettingsCategories | Nie | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Klastry | Yes | 
| klastry/aplikacji | Nie | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| aplikacje | Yes | 
| Bramy | Yes | 
| Sieci | Yes | 
| wpisy tajne | Yes | 
| volumes | Yes | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| SignalR | Yes | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| applianceDefinitions | Yes | 
| urządzenia | Yes | 
| applicationDefinitions | Yes | 
| aplikacje | Yes | 
| jitRequests | Yes | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Typ zasobu | Obsługa tagów |
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
> Wzorzec bazy danych nie obsługuje tagi, ale innych baz danych, w tym baz danych Azure SQL Data Warehouse, obsługuje tagi. Baz danych Azure SQL Data Warehouse musi znajdować się w aktywny (nie jest wstrzymana) stanu.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| SqlVirtualMachineGroups | Yes | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nie | 
| SqlVirtualMachines | Yes | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| storageAccounts | Yes | 
| storageAccounts/blobServices | Nie | 
| storageAccounts/fileServices | Nie | 
| storageAccounts/queueServices | Nie | 
| storageAccounts/services | Nie | 
| storageAccounts/tableServices | Nie | 
| Sposoby użycia | Nie | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| storageSyncServices | Yes | 
| storageSyncServices/registeredServers | Nie | 
| storageSyncServices/syncGroups | Nie | 
| storageSyncServices/syncGroups/cloudEndpoints | Nie | 
| storageSyncServices/syncGroups/serverEndpoints | Nie | 
| storageSyncServices/workflows | Nie | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Menedżerowie | Yes | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| streamingjobs | Tak (zobacz uwaga poniżej) | 
| streamingjobs/diagnosticSettings | Nie | 

> [!NOTE]
> Nie można dodać tag, gdy streamingjobs jest uruchomiony. Zatrzymaj zasobów, aby dodać tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| CreateSubscription | Nie | 
| SubscriptionDefinitions | Nie | 
| SubscriptionOperations | Nie | 

## <a name="microsoftsupport"></a>microsoft.support
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| supporttickets | Nie | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| providerRegistrations | Yes | 
| zasoby | Yes | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Środowiska | Yes | 
| środowiska/accessPolicies | Nie | 
| środowiska/eventsources | Yes | 
| environments/referenceDataSets | Yes | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| account | Yes | 
| konta i rozszerzeń | Yes | 
| konto lub projekt docelowy | Yes | 

## <a name="microsoftweb"></a>Microsoft.Web
| Typ zasobu | Obsługa tagów |
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
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| diagnosticSettings | Nie | 
| diagnosticSettingsCategories | Nie | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| DeviceServices | Yes | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Typ zasobu | Obsługa tagów |
| ------------- | ----------- |
| Składniki | Nie | 
| componentsSummary | Nie | 
| monitorInstances | Nie | 
| monitorInstancesSummary | Nie | 
| Monitory | Nie | 
| notificationSettings | Nie | 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak stosowanie tagów do zasobów, zobacz [organizowania zasobów platformy Azure za pomocą tagów](resource-group-using-tags.md).
