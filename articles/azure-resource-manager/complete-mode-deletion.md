---
title: Azure Resource Manager usuwania trybu pełnego według typu zasobu
description: Pokazuje, jak typy zasobów obsługują usuwanie w trybie pełnym w szablonach Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 5ac442c0ae1e397fd1e8b58fdbcf61eb8712046c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302840"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Usuwanie zasobów platformy Azure na potrzeby wdrożeń w trybie pełnym
W tym artykule opisano sposób, w jaki typy zasobów obsługują usuwanie, gdy nie znajduje się w szablonie wdrożonym w trybie kompletnym.

Typy zasobów oznaczone za pomocą `Yes` są usuwane, gdy typ nie znajduje się w szablonie wdrożonym z trybem kompletnym. 

Typy zasobów oznaczone atrybutem `No` nie są automatycznie usuwane, gdy nie znajdują się w szablonie, jednak są usuwane, jeśli zasób nadrzędny zostanie usunięty. Pełny opis zachowania można znaleźć w temacie [Azure Resource Manager Modes Deployments](deployment-modes.md).


## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DomainServices | Yes | 
> | DomainServices/oucontainer | Nie | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie | 
> | diagnosticSettingsCategories | Nie | 

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | supportProviders | Nie | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aadsupportcases | Nie | 
> | addsservices | Nie | 
> | pracownicy | Nie | 
> | anonymousapiusers | Nie | 
> | konfiguracja | Nie | 
> | logs | Nie | 
> | raporty | Nie | 
> | services | Nie | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | komputerów | Nie | 
> | generateRecommendations | Nie | 
> | Zalecenia | Nie | 
> | pominięć | Nie | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | actionRules | Nie | 
> | alerts | Nie | 
> | alertsList | Nie | 
> | alertsSummary | Nie | 
> | alertsSummaryList | Nie | 
> | smartDetectorAlertRules | Nie | 
> | smartDetectorRuntimeEnvironments | Nie | 
> | smartGroups | Nie | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Serwerem | Tak | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | reportFeedback | Nie | 
> | usługa | Tak | 
> | validateServiceName | Nie | 

## <a name="microsoftattestation"></a>Microsoft. zaświadczanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | attestationProviders | Nie | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | classicAdministrators | Nie | 
> | denyAssignments | Nie | 
> | elevateAccess | Nie | 
> | Zamki | Nie | 
> | uprawnienia | Nie | 
> | policyAssignments | Nie | 
> | policyDefinitions | Nie | 
> | policySetDefinitions | Nie | 
> | providerOperations | Nie | 
> | roleAssignments | Nie | 
> | roleDefinitions | Nie | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | automationAccounts | Yes | 
> | automationAccounts/konfiguracje | Tak | 
> | automationAccounts/zadania | Nie | 
> | automationAccounts/elementy Runbook | Tak | 
> | automationAccounts/softwareUpdateConfigurations | Nie | 
> | automationAccounts/elementy webhook | Nie | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wiejski | Nie | 
> | środowiska/konta | Nie | 
> | środowiska/konta/przestrzenie nazw | Nie | 
> | środowiska/konta/przestrzenie nazw/konfiguracje | Nie | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | b2cDirectories | Tak | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | rejestracje | Tak | 
> | rejestracje/customerSubscriptions | Nie | 
> | rejestracje/produkty | Nie | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | batchAccounts | Tak | 

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | billingAccounts | Nie | 
> | billingAccounts/billingProfiles | Nie | 
> | billingAccounts/billingProfiles/billingSubscriptions | Nie | 
> | billingAccounts/billingProfiles/faktury | Nie | 
> | billingAccounts/billingProfiles/faktury/arkusza cen | Nie | 
> | billingAccounts/billingProfiles/operationStatus | Nie | 
> | billingAccounts/billingProfiles/paymentMethods | Nie | 
> | billingAccounts/billingProfiles/zasady | Nie | 
> | billingAccounts/billingProfiles/pricesheet | Nie | 
> | billingAccounts/billingProfiles/produkty | Nie | 
> | billingAccounts/billingProfiles/Transactions | Nie | 
> | billingAccounts/billingSubscriptions | Nie | 
> | billingAccounts/działy | Nie | 
> | billingAccounts/eligibleOffers | Nie | 
> | billingAccounts/enrollmentAccounts | Nie | 
> | billingAccounts/faktury | Nie | 
> | billingAccounts/invoiceSections | Nie | 
> | billingAccounts/invoiceSections/billingSubscriptions | Nie | 
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nie | 
> | billingAccounts/invoiceSections/importRequests | Nie | 
> | billingAccounts/invoiceSections/initiateImportRequest | Nie | 
> | billingAccounts/invoiceSections/initiateTransfer | Nie | 
> | billingAccounts/invoiceSections/operationStatus | Nie | 
> | billingAccounts/invoiceSections/products | Nie | 
> | billingAccounts/invoiceSections/transfery | Nie | 
> | billingAccounts/produkty | Nie | 
> | billingAccounts/projekty | Nie | 
> | billingAccounts/projects/billingSubscriptions | Nie | 
> | billingAccounts/projects/importRequests | Nie | 
> | billingAccounts/projects/initiateImportRequest | Nie | 
> | billingAccounts/projects/operationStatus | Nie | 
> | billingAccounts/projekty/produkty | Nie | 
> | billingAccounts/transakcje | Nie | 
> | billingPeriods | Nie | 
> | BillingPermissions | Nie | 
> | billingProperty | Nie | 
> | BillingRoleAssignments | Nie | 
> | BillingRoleDefinitions | Nie | 
> | CreateBillingRoleAssignment | Nie | 
> | działów | Nie | 
> | enrollmentAccounts | Nie | 
> | importRequests | Nie | 
> | importRequests/acceptImportRequest | Nie | 
> | importRequests/declineImportRequest | Nie | 
> | faktur | Nie | 
> | sunięcia | Nie | 
> | transfery/acceptTransfer | Nie | 
> | transfery/declineTransfer | Nie | 
> | transfery/operationStatus | Nie | 
> | usagePlans | Nie | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | mapApis | Yes | 
> | updateCommunicationPreference | Nie | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | BizTalk | Tak | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | blueprintAssignments | Nie | 
> | blueprintAssignments/assignmentOperations | Nie | 
> | blueprintAssignments/operacje | Nie | 
> | plany | Nie | 
> | plany/artefakty | Nie | 
> | plany/wersje | Nie | 
> | plany/wersje/artefakty | Nie | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | botServices | Tak | 
> | botServices/kanały | Nie | 
> | botServices/połączenia | Nie | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Redis | Tak | 
> | RedisConfigDefinition | Nie | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appliedReservations | Nie | 
> | calculatePrice | Nie | 
> | wykazy | Nie | 
> | commercialReservationOrders | Nie | 
> | reservationOrders | Nie | 
> | reservationOrders/calculateRefund | Nie | 
> | reservationOrders/Scal | Nie | 
> | reservationOrders/rezerwacje | Nie | 
> | reservationOrders/rezerwacje/poprawki | Nie | 
> | reservationOrders/Return | Nie | 
> | reservationOrders/Split | Nie | 
> | reservationOrders/swap | Nie | 
> | dokonując | Nie | 
> | Produkcyjnych | Nie | 
> | validateReservationOrder | Nie | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | edgenodes | Nie | 
> | profiles | Tak | 
> | Profile/punkty końcowe | Tak | 
> | Profile/punkty końcowe/customdomains | Nie | 
> | Profile/punkty końcowe/źródła | Nie | 
> | validateProbe | Nie | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | certificateOrders | Tak | 
> | certificateOrders/certyfikaty | Nie | 
> | validateCertificateRegistrationInformation | Nie | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwość | Nie | 
> | domainNames | Nie | 
> | domainNames/możliwości | Nie | 
> | domainNames/internalLoadBalancers | Nie | 
> | domainNames/servicecertificates | Nie | 
> | domainNames/miejsca | Nie | 
> | domainNames/gniazda/role | Nie | 
> | moveSubscriptionResources | Nie | 
> | operatingSystemFamilies | Nie | 
> | operatingSystems | Nie | 
> | quotas | Nie | 
> | resourceTypes | Nie | 
> | validateSubscriptionMoveAvailability | Nie | 
> | VirtualMachines | Nie | 
> | virtualMachines/diagnosticSettings | Nie | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nie | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwość | Nie | 
> | expressRouteCrossConnections | Nie | 
> | expressRouteCrossConnections/peerings | Nie | 
> | gatewaySupportedDevices | Nie | 
> | networkSecurityGroups | Nie | 
> | quotas | Nie | 
> | reservedIps | Nie | 
> | virtualNetworks | Nie | 
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nie | 
> | virtualNetworks/virtualNetworkPeerings | Nie | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwość | Nie | 
> | Dysku | Nie | 
> | images | Nie | 
> | osImages | Nie | 
> | osPlatformImages | Nie | 
> | publicImages | Nie | 
> | quotas | Nie | 
> | storageAccounts | Nie | 
> | storageAccounts/usługi | Nie | 
> | storageAccounts/usługi/diagnosticSettings | Nie | 
> | storageAccounts/vmImages | Nie | 
> | vmImages | Nie | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Yes | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | RateCard | Nie | 
> | UsageAggregates | Nie | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | availabilitySets | Yes | 
> | Dysku | Yes | 
> | images | Tak | 
> | restorePointCollections | Tak | 
> | restorePointCollections/restorePoints | Nie | 
> | sharedVMImages | Tak | 
> | sharedVMImages/wersje | Yes | 
> | Migawki | Yes | 
> | VirtualMachines | Tak | 
> | virtualMachines/diagnosticSettings | Nie | 
> | virtualMachines/rozszerzenia | Tak | 
> | virtualMachineScaleSets | Tak | 
> | virtualMachineScaleSets/rozszerzenia | Nie | 
> | virtualMachineScaleSets/networkInterfaces | Nie | 
> | virtualMachineScaleSets/publicIPAddresses | Nie | 
> | virtualMachineScaleSets/virtualMachines | Nie | 
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nie | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | AggregatedCost | Nie | 
> | Salda | Nie | 
> | Budżety | Nie | 
> | Odsetk | Nie | 
> | CostTags | Nie | 
> | środki | Nie | 
> | zdarzenia | Nie | 
> | Prognozy | Nie | 
> | ilości | Nie | 
> | Platform handlowych | Nie | 
> | Pricesheets | Nie | 
> | wyrobów | Nie | 
> | ReservationDetails | Nie | 
> | ReservationRecommendations | Nie | 
> | ReservationSummaries | Nie | 
> | ReservationTransactions | Nie | 
> | `Tags` | Nie | 
> | Warunki | Nie | 
> | UsageDetails | Nie | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | containerGroups | Tak | 
> | serviceAssociationLinks | Nie | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wołuje | Tak | 
> | rejestry/kompilacje | Nie | 
> | rejestry/kompilacje/anulowanie | Nie | 
> | rejestry/kompilacje/getLogLink | Nie | 
> | rejestry/buildTasks | Yes | 
> | registries/buildTasks/steps | Nie | 
> | registries/eventGridFilters | Nie | 
> | registries/getBuildSourceUploadUrl | Nie | 
> | rejestry/getpoświadczenia | Nie | 
> | registries/importImage | Nie | 
> | rejestry/queueBuild | Nie | 
> | registries/regenerateCredential | Nie | 
> | rejestry/regenerateCredentials | Nie | 
> | rejestry/replikacje | Yes | 
> | rejestry/uruchomienia | Nie | 
> | rejestry/uruchomienia/Anuluj | Nie | 
> | rejestry/scheduleRun | Nie | 
> | rejestry/zadania | Tak | 
> | registries/updatePolicies | Nie | 
> | rejestry/elementy webhook | Tak | 
> | registries/webhooks/getCallbackConfig | Nie | 
> | rejestry/elementy webhook/polecenie ping | Nie | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | containerServices | Yes | 
> | managedClusters | Yes | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Tak | 
> | updateCommunicationPreference | Nie | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Alerty | Nie | 
> | billingAccounts | Nie | 
> | Łączniki | Tak | 
> | działów | Nie | 
> | Wymiary | Nie | 
> | enrollmentAccounts | Nie | 
> | Zapytanie | Nie | 
> | Zarejestrować | Nie | 
> | Reportconfigs | Nie | 
> | Raporty | Nie | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | węzłów | Tak | 
> | Centra/authorizationPolicies | Nie | 
> | Centra/łączniki | Nie | 
> | Centra/łączniki/mapowania | Nie | 
> | Centra/interakcje | Nie | 
> | hubs/kpi | Nie | 
> | Centra/linki | Nie | 
> | Centra/profile | Nie | 
> | Centra/roleAssignments | Nie | 
> | Centra/role | Nie | 
> | hubs/suggestTypeSchema | Nie | 
> | Centra/widoki | Nie | 
> | hubs/widgetTypes | Nie | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zadania | Tak | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Tak | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszarów roboczych | Yes | 
> | workspaces/virtualNetworkPeerings | Nie | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wykazy | Tak | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | connectionManagers | Tak | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Fabryki datafactors | Tak | 
> | datafactors/diagnosticSettings | Nie | 
> | dataFactorySchema | Nie | 
> | fabryki | Tak | 
> | fabryki/integrationRuntimes | Nie | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Yes | 
> | accounts/dataLakeStoreAccounts | Nie | 
> | konta/storageAccounts | Nie | 
> | konta/storageAccounts/kontenery | Nie | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Yes | 
> | accounts/eventGridFilters | Nie | 
> | konta/firewallRules | Nie | 

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Tak | 
> | usługi/projekty | Tak | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Serwerem | Tak | 
> | serwery/recoverableServers | Nie | 
> | servers/virtualNetworkRules | Nie | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Serwerem | Tak | 
> | serwery/recoverableServers | Nie | 
> | servers/virtualNetworkRules | Nie | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Serwerem | Tak | 
> | serwery/doradcy | Nie | 
> | serwery/queryTexts | Nie | 
> | serwery/recoverableServers | Nie | 
> | servers/topQueryStatistics | Nie | 
> | servers/virtualNetworkRules | Nie | 
> | serwery/waitStatistics | Nie | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | IotHubs | Tak | 
> | IotHubs/eventGridFilters | Nie | 
> | ProvisioningServices | Tak | 
> | użycia | Nie | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Rejestrowanie | Tak | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Labs | Yes | 
> | labs/serviceRunners | Yes | 
> | Labs/virtualMachines | Tak | 
> | Uruchamianie | Yes | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | databaseAccountNames | Nie | 
> | databaseAccounts | Tak | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | domeny | Tak | 
> | domeny/domainOwnershipIdentifiers | Nie | 
> | generateSsoRequest | Nie | 
> | topLevelDomains | Nie | 
> | validateDomainRegistrationInformation | Nie | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | lcsprojects | Nie | 
> | lcsprojects/clouddeployments | Nie | 
> | lcsprojects/łączniki | Nie | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | domeny | Tak | 
> | domeny/tematy | Nie | 
> | eventSubscriptions | Nie | 
> | extensionTopics | Nie | 
> | opisano | Tak | 
> | topicTypes | Nie | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Tak | 
> | Przestrzeni | Tak | 
> | przestrzenie nazw/reguł autoryzacji | Nie | 
> | przestrzenie nazw/disasterrecoveryconfigs | Nie | 
> | przestrzenie nazw/eventhubs | Nie | 
> | przestrzenie nazw/eventhubs/reguł autoryzacji | Nie | 
> | przestrzenie nazw/eventhubs/consumergroups | Nie | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | danych | Nie | 
> | dostawcy | Nie | 

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | pisaną | Nie | 
> | galleryitems | Nie | 
> | generateartifactaccessuri | Nie | 
> | Moje obszary | Nie | 
> | Moje obszary/obszary | Nie | 
> | Moje obszary/obszary/obszary | Nie | 
> | Moje obszary/obszary/obszary/galleryitems | Nie | 
> | Moje obszary/obszary/galleryitems | Nie | 
> | Moje obszary/galleryitems | Nie | 
> | Zarejestrować | Nie | 
> | Produkcyjnych | Nie | 
> | retrieveresourcesbyid | Nie | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | guestConfigurationAssignments | Nie | 
> | Programy | Nie | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hanaInstances | Yes | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Tak | 
> | klastry/aplikacje | Nie | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zadania | Yes | 

## <a name="microsoftinformationprotection"></a>Microsoft. InformationProtection

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | labelGroups | Nie | 
> | labelGroups/etykiety | Nie | 
> | labelGroups/etykiety/warunki | Nie | 
> | labelGroups/etykiety/etykiety | Nie | 
> | labelGroups/labels/subLabels/conditions | Nie | 

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | actiongroups | Yes | 
> | activityLogAlerts | Tak | 
> | alertrules | Tak | 
> | automatedExportSettings | Nie | 
> | autoscalesettings | Yes | 
> | punkcie | Nie | 
> | calculatebaseline | Nie | 
> | Komponentów | Tak | 
> | składniki/zdarzenia | Nie | 
> | składniki/pricingPlans | Nie | 
> | składniki/zapytanie | Nie | 
> | diagnosticSettings | Nie | 
> | diagnosticSettingsCategories | Nie | 
> | eventCategories | Nie | 
> | Typ zdarzenia | Nie | 
> | extendedDiagnosticSettings | Nie | 
> | logDefinitions | Nie | 
> | logprofiles | Nie | 
> | logs | Nie | 
> | metricAlerts | Tak |
> | migrateToNewPricingModel | Nie | 
> | Moje skoroszyty | Nie | 
> | — zapytania | Nie | 
> | rollbackToLegacyPricingModel | Nie | 
> | scheduledqueryrules | Yes | 
> | vmInsightsOnboardingStatuses | Nie | 
> | testów internetowych | Tak | 
> | Otwiera | Tak | 

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie | 
> | diagnosticSettingsCategories | Nie | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | IoTApps | Yes | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Graph | Tak | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | deletedVaults | Nie | 
> | magazynów | Yes | 
> | magazyny/accessPolicies | Nie | 
> | magazyny/wpisy tajne | Nie | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Yes | 
> | klastry/bazy danych | Nie | 
> | klastry/bazy danych/połączenia Databases | Nie | 
> | klastry/bazy danych/eventhubconnections | Nie | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | labaccounts | Yes | 
> | użytkownicy | Nie | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Yes | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | logs | Nie | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | integrationAccounts | Tak | 
> | przebieg | Tak | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | commitmentPlans | Tak | 
> | webServices | Tak | 
> | Obszary robocze | Tak | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Yes | 
> | konta/obszary robocze | Yes | 
> | konta/obszary robocze/projekty | Tak | 
> | teamAccounts | Tak | 
> | teamAccounts/obszary robocze | Yes | 
> | teamAccounts/obszary robocze/projekty | Yes | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszarów roboczych | Tak | 
> | obszary robocze/obliczenia | Nie | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Osob | Nie | 
> | Resourceidentity | Tak | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | getentities | Nie | 
> | managementGroups | Nie | 
> | Produkcyjnych | Nie | 
> | startTenantBackfill | Nie | 
> | tenantBackfillStatus | Nie | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Yes | 
> | accounts/eventGridFilters | Nie | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Otrzymane | Nie | 
> | offerTypes | Nie | 
> | offerTypes/wydawcy | Nie | 
> | offerTypes/wydawcy/oferty | Nie | 
> | offerTypes/wydawcy/oferty/plany | Nie | 
> | offerTypes/wydawcy/oferty/plany/umowy | Nie | 
> | offerTypes/wydawcy/oferty/plany/konfiguracje | Nie | 
> | offerTypes/publishers/offers/plans/configs/importImage | Nie | 
> | privategalleryitems | Nie | 
> | wyrobów | Nie | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | classicDevServices | Yes | 
> | updateCommunicationPreference | Nie | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | kontrakt | Nie | 
> | offertypes | Nie | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | MediaServices | Yes | 
> | MediaServices/accountFilters | Nie | 
> | MediaServices/zasoby | Nie | 
> | MediaServices/assetFilters | Nie | 
> | MediaServices/contentKeyPolicies | Nie | 
> | MediaServices/eventGridFilters | Nie | 
> | mediaservices/liveEventOperations | Nie | 
> | MediaServices/liveEvents | Tak | 
> | MediaServices/liveEvents/liveOutputs | Nie | 
> | MediaServices/liveOutputOperations | Nie | 
> | MediaServices/streamingEndpointOperations | Nie | 
> | MediaServices/streamingEndpoints | Tak | 
> | MediaServices/streamingLocators | Nie | 
> | MediaServices/streamingPolicies | Nie | 
> | MediaServices/transformacje | Nie | 
> | MediaServices/transformacje/zadania | Nie | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | projektami | Yes | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationGateways | Yes | 
> | applicationSecurityGroups | Tak | 
> | azureFirewallFqdnTags | Nie | 
> | azureFirewalls | Tak | 
> | bgpServiceCommunities | Nie | 
> | Licznik | Tak | 
> | ddosCustomPolicies | Tak | 
> | ddosProtectionPlans | Tak | 
> | dnsOperationStatuses | Nie | 
> | dnszones | Tak | 
> | dnszones/A | Nie | 
> | dnszones/AAAA | Nie | 
> | dnszones/wszystkie | Nie | 
> | dnszones/CAA | Nie | 
> | dnszones/CNAME | Nie | 
> | dnszones/MX | Nie | 
> | dnszones/NS | Nie | 
> | dnszones/PTR | Nie | 
> | dnszones/zestawy rekordów | Nie | 
> | dnszones/SOA | Nie | 
> | dnszones/SRV | Nie | 
> | dnszones/TXT | Nie | 
> | expressRouteCircuits | Yes | 
> | expressRouteServiceProviders | Nie | 
> | usługi frontdoor | Yes | 
> | frontdoorWebApplicationFirewallPolicies | Yes | 
> | getDnsResourceReference | Nie | 
> | interfaceEndpoints | Yes | 
> | internalNotify | Nie | 
> | loadBalancers | Tak | 
> | localNetworkGateways | Tak | 
> | natGateways | Tak | 
> | networkIntentPolicies | Yes | 
> | networkInterfaces | Yes | 
> | networkProfiles | Tak | 
> | networkSecurityGroups | Tak | 
> | networkWatchers | Tak | 
> | networkWatchers/connectionMonitors | Tak | 
> | networkWatchers/soczewki | Yes | 
> | networkWatchers/pingMeshes | Tak | 
> | privateLinkServices | Tak | 
> | publicIPAddresses | Tak | 
> | publicIPPrefixes | Tak | 
> | routeFilters | Tak | 
> | routeTables | Yes | 
> | serviceEndpointPolicies | Tak | 
> | trafficManagerGeographicHierarchies | Nie | 
> | trafficmanagerprofiles | Yes | 
> | trafficmanagerprofiles/map cieplnych | Nie | 
> | virtualHubs | Tak | 
> | virtualNetworkGateways | Tak | 
> | virtualNetworks | Yes | 
> | virtualNetworkTaps | Yes | 
> | virtualWans | Tak | 
> | vpnGateways | Tak | 
> | vpnSites | Tak | 
> | webApplicationFirewallPolicies | Yes | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Przestrzeni | Yes | 
> | przestrzenie nazw/notificationHubs | Tak | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | urządzenia | Nie | 
> | linkTargets | Nie | 
> | storageInsightConfigs | Nie | 
> | Obszarów roboczych | Yes | 
> | obszary robocze/źródła danych | Nie | 
> | workspaces/linkedServices | Nie | 
> | obszary robocze/zapytanie | Nie | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | managementassociations | Nie | 
> | managementconfigurations | Tak | 
> | rozwiązania | Tak | 
> | Widoki | Yes | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | policyEvents | Nie | 
> | policyStates | Nie | 
> | policyTrackedResources | Nie | 
> | korygowania | Nie | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konsol | Nie | 
> | Pulpitów nawigacyjnych | Tak | 
> | userSettings | Nie | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | workspaceCollections | Yes | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | produkcyjnych | Tak | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | backupProtectedItems | Nie | 
> | magazynów | Tak | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Przestrzeni | Yes | 
> | przestrzenie nazw/reguł autoryzacji | Nie | 
> | przestrzenie nazw/hybridconnections | Nie | 
> | przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie | 
> | przestrzenie nazw/wcfrelays | Nie | 
> | przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Produkcyjnych | Nie | 
> | subscriptionsStatus | Nie | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | availabilityStatuses | Nie | 
> | childAvailabilityStatuses | Nie | 
> | childResources | Nie | 
> | zdarzenia | Nie | 
> | impactedResources | Nie | 
> | Złożone | Nie | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | komputerów | Nie | 
> | wdrożenia/operacje | Nie | 
> | linki | Nie | 
> | notifyResourceJobs | Nie | 
> | dostawcy | Nie | 
> | resourceGroups | Nie | 
> | Produkcyjnych | Nie | 
> | opłaty | Nie | 
> | subskrypcje/dostawcy | Nie | 
> | subskrypcje/resourceGroups | Nie | 
> | subskrypcje/ResourceGroups/zasoby | Nie | 
> | subskrypcje/zasoby | Nie | 
> | subskrypcje/TagName | Nie | 
> | subscriptions/tagNames/tagValues | Nie | 
> | dzierżaw | Nie | 

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Tak | 
> | saasresources | Nie | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przekazywania | Tak | 
> | jobcollections | Tak | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nie | 
> | searchServices | Tak | 

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | advancedThreatProtectionSettings | Nie | 
> | alerts | Nie | 
> | allowedConnections | Nie | 
> | gotowania | Nie | 
> | applicationWhitelistings | Nie | 
> | AutoProvisioningSettings | Nie | 
> | Zachowania zgodności prawnymi | Nie | 
> | dataCollectionAgents | Nie | 
> | discoveredSecuritySolutions | Nie | 
> | externalSecuritySolutions | Nie | 
> | InformationProtectionPolicies | Nie | 
> | jitNetworkAccessPolicies | Nie | 
> | Kontrolą | Nie | 
> | monitorowanie/złośliwe oprogramowanie | Nie | 
> | monitorowanie/linia bazowa | Nie | 
> | monitorowanie/poprawka | Nie | 
> | Jazd | Nie | 
> | cen | Nie | 
> | securityContacts | Nie | 
> | securitySolutions | Nie | 
> | securitySolutionsReferenceData | Nie | 
> | securityStatus | Nie | 
> | securityStatus/punkty końcowe | Nie | 
> | securityStatus/podsieci | Nie | 
> | securityStatus/virtualMachines | Nie | 
> | securityStatuses | Nie | 
> | securityStatusesSummaries | Nie | 
> | settings | Nie | 
> | zadania | Nie | 
> | replikacji | Nie | 
> | workspaceSettings | Nie | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie | 
> | diagnosticSettingsCategories | Nie | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Przestrzeni | Yes | 
> | przestrzenie nazw/reguł autoryzacji | Nie | 
> | przestrzenie nazw/disasterrecoveryconfigs | Nie | 
> | przestrzenie nazw/eventgridfilters | Nie | 
> | przestrzenie nazw/kolejki | Nie | 
> | przestrzenie nazw/kolejki/reguł autoryzacji | Nie | 
> | obszary nazw/tematy | Nie | 
> | przestrzenie nazw/tematy/reguł autoryzacji | Nie | 
> | obszary nazw/tematy/subskrypcje | Nie | 
> | obszary nazw/tematy/subskrypcje/reguły | Nie | 
> | premiumMessagingRegions | Nie | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Yes | 
> | klastry/aplikacje | Nie | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Tak | 
> | bram | Tak | 
> | sieci | Tak | 
> | wpisy tajne | Yes | 
> | volumes | Tak | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SignalR | Yes | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applianceDefinitions | Tak | 
> | gotowania | Yes | 
> | applicationDefinitions | Tak | 
> | aplikacje | Tak | 
> | jitRequests | Yes | 

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | managedInstances | Tak |
> | managedInstances/bazy danych | Tak |
> | managedInstances/databases/backupShortTermRetentionPolicies | Nie |
> | managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels | Nie |
> | managedInstances/Databases/vulnerabilityAssessments | Nie |
> | managedInstances/bazy danych/vulnerabilityAssessments/reguły/linie bazowe | Nie |
> | managedInstances/encryptionProtector | Nie |
> | managedInstances/klucze | Nie |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nie |
> | managedInstances/vulnerabilityAssessments | Nie |
> | Serwerem | Tak | 
> | serwery/Administratorzy | Nie | 
> | serwery/communicationLinks | Nie | 
> | Serwery/bazy danych | Tak | 
> | serwery/encryptionProtector | Nie | 
> | serwery/firewallRules | Nie | 
> | serwery/klucze | Nie | 
> | servers/restorableDroppedDatabases | Nie | 
> | serwery/cele | Nie | 
> | serwery/tdeCertificates | Nie | 


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Tak | 
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Nie | 
> | SqlVirtualMachines | Tak | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageAccounts | Tak | 
> | storageAccounts/blobServices | Nie | 
> | storageAccounts/fileServices | Nie | 
> | storageAccounts/queueServices | Nie | 
> | storageAccounts/usługi | Nie | 
> | storageAccounts/tableServices | Nie | 
> | użycia | Nie | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageSyncServices | Yes | 
> | storageSyncServices/registeredServers | Nie | 
> | storageSyncServices/syncGroups | Nie | 
> | storageSyncServices/syncGroups/cloudEndpoints | Nie | 
> | storageSyncServices/syncGroups/serverEndpoints | Nie | 
> | storageSyncServices/przepływy pracy | Nie | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | skonfigurowany | Yes | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | streamingjobs | Tak | 
> | streamingjobs/diagnosticSettings | Nie | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Anulowanie subskrypcji | Nie | 
> | SubscriptionDefinitions | Nie | 
> | SubscriptionOperations | Nie | 

## <a name="microsoftsupport"></a>Microsoft. Support

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | supporttickets | Nie | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | providerRegistrations | Tak | 
> | Produkcyjnych | Tak | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wiejski | Yes | 
> | środowiska/accessPolicies | Nie | 
> | środowiska/źródła zdarzeń | Tak | 
> | environments/referenceDataSets | Tak | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | account | Yes | 
> | konto/rozszerzenie | Tak | 
> | konto/projekt | Tak | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | apiManagementAccounts | Nie | 
> | apiManagementAccounts/apiAcls | Nie | 
> | apiManagementAccounts/apis | Nie | 
> | apiManagementAccounts/apis/apiAcls | Nie | 
> | apiManagementAccounts/apis/connectionAcls | Nie | 
> | apiManagementAccounts/apis/connections | Nie | 
> | apiManagementAccounts/apis/connections/connectionAcls | Nie | 
> | apiManagementAccounts/apis/localizedDefinitions | Nie | 
> | apiManagementAccounts/connectionAcls | Nie | 
> | apiManagementAccounts/connections | Nie | 
> | billingMeters | Nie | 
> | Przystawki | Tak | 
> | connectionGateways | Tak | 
> | Licznik | Tak | 
> | customApis | Tak | 
> | deletedSites | Nie | 
> | — funkcje | Nie | 
> | hostingEnvironments | Tak | 
> | hostingEnvironments/multiRolePools | Nie | 
> | hostingEnvironments/multiRolePools/Instances | Nie | 
> | hostingEnvironments/workerPools | Nie | 
> | hostingEnvironments/workerPools/Instances | Nie | 
> | publishingUsers | Nie | 
> | Zalecenia | Nie | 
> | resourceHealthMetadata | Nie | 
> | Runtime | Nie | 
> | Dopuszczalna | Tak | 
> | Dopuszczalna/procesy robocze | Nie | 
> | teren | Yes | 
> | Lokacje/domainOwnershipIdentifiers | Nie | 
> | sites/hostNameBindings | Nie | 
> | Lokacje/wystąpienia | Nie | 
> | Lokacje/wystąpienia/rozszerzenia | Nie | 
> | sites/premieraddons | Tak | 
> | Lokacje/zalecenia | Nie | 
> | sites/resourceHealthMetadata | Nie | 
> | Lokacje/miejsca | Tak | 
> | sites/slots/hostNameBindings | Nie | 
> | Lokacje/miejsca/wystąpienia | Nie | 
> | Lokacje/miejsca/wystąpienia/rozszerzenia | Nie | 
> | sourceControls | Nie | 
> | legalizacj | Nie | 
> | verifyHostingEnvironmentVnet | Nie | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie | 
> | diagnosticSettingsCategories | Nie | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DeviceServices | Tak | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Komponentów | Nie | 
> | componentsSummary | Nie | 
> | monitorInstances | Nie | 
> | monitorInstancesSummary | Nie | 
> | monitora | Nie | 
> | notificationSettings | Nie | 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać te same dane jak plik z wartościami rozdzielanymi przecinkami, Pobierz [Complete-Mode-deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).