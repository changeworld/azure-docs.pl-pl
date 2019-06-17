---
title: Menedżer zasobów tag pomocy technicznej platformy Azure dla zasobów
description: Pokazuje, jakie typy zasobów platformy Azure obsługuje tagi. Zawiera szczegółowe informacje dla wszystkich usług platformy Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 8b7e6d234984e84f5b238d657281dd8b1b9ec423
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056882"
---
# <a name="tag-support-for-azure-resources"></a>Obsługa tagów dla zasobów platformy Azure
W tym artykule opisano, czy typ zasobu obsługuje [tagi](resource-group-using-tags.md). Kolumnę z etykietą **obsługuje tagi** wskazuje, czy typ zasobu ma właściwość dla tagu. Kolumnę z etykietą **tagu w raporcie koszt** wskazuje, czy ten typ zasobu przekazuje tagu do raportów kosztów.

Aby uzyskać te same dane w formacie wartości rozdzielanych przecinkami, Pobierz [tag support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| DomainServices | Tak | Tak |
| DomainServices/oucontainer | Nie | Nie |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nie |  Nie |
| diagnosticSettingsCategories | Nie |  Nie |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| supportProviders | Nie |  Nie |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| aadsupportcases | Nie |  Nie |
| addsservices | Nie |  Nie |
| Agenci | Nie |  Nie |
| anonymousapiusers | Nie |  Nie |
| konfiguracja | Nie |  Nie |
| logs | Nie |  Nie |
| raporty | Nie |  Nie |
| services | Nie |  Nie |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Konfiguracje | Nie |  Nie |
| generateRecommendations | Nie |  Nie |
| Zalecenia | Nie |  Nie |
| pominięć | Nie |  Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| actionRules | Nie |  Nie |
| alerts | Nie |  Nie |
| alertsList | Nie |  Nie |
| alertsSummary | Nie |  Nie |
| alertsSummaryList | Nie |  Nie |
| smartDetectorAlertRules | Nie |  Nie |
| smartDetectorRuntimeEnvironments | Nie |  Nie |
| smartGroups | Nie |  Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Serwery | Yes | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| reportFeedback | Nie |  Nie |
| usługa | Tak | Tak |
| validateServiceName | Nie |  Nie |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| attestationProviders | Nie |  Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| classicAdministrators | Nie |  Nie |
| denyAssignments | Nie |  Nie |
| elevateAccess | Nie |  Nie |
| Blokady | Nie |  Nie |
| uprawnienia | Nie |  Nie |
| policyAssignments | Nie |  Nie |
| policyDefinitions | Nie |  Nie |
| policySetDefinitions | Nie |  Nie |
| providerOperations | Nie |  Nie |
| Liczba przypisań ról | Nie |  Nie |
| roleDefinitions | Nie |  Nie |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| automationAccounts | Tak | Tak |
| automationAccounts/configurations | Yes | Yes |
| automationAccounts/zadań | Nie |  Nie |
| automationAccounts/runbooks | Tak | Tak |
| automationAccounts/softwareUpdateConfigurations | Nie | Nie |
| automationAccounts/webhooks | Nie |  Nie |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Środowiska | Nie |  Nie |
| środowiska/kont | Nie |  Nie |
| środowiska/kont/przestrzeni nazw | Nie |  Nie |
| środowiska/kont/przestrzenie nazw/konfiguracji | Nie |  Nie |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| b2cDirectories | Yes | Nie |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| rejestracje | Tak | Tak |
| registrations/customerSubscriptions | Nie |  Nie |
| rejestracje/produktów | Nie |  Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| batchAccounts | Tak | Yes |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| billingAccounts | Nie |  Nie |
| billingAccounts/billingProfiles | Nie |  Nie |
| billingAccounts/billingProfiles/billingSubscriptions | Nie |  Nie |
| billingAccounts/billingProfiles/invoices | Nie |  Nie |
| billingAccounts/billingProfiles/invoices/pricesheet | Nie |  Nie |
| billingAccounts/billingProfiles/operationStatus | Nie |  Nie |
| billingAccounts/billingProfiles/paymentMethods | Nie |  Nie |
| billingAccounts/billingProfiles/policies | Nie |  Nie |
| billingAccounts/billingProfiles/pricesheet | Nie |  Nie |
| billingAccounts/billingProfiles/products | Nie |  Nie |
| billingAccounts/billingProfiles/transactions | Nie |  Nie |
| billingAccounts/billingSubscriptions | Nie |  Nie |
| billingAccounts/działów | Nie |  Nie |
| billingAccounts/eligibleOffers | Nie |  Nie |
| billingAccounts/enrollmentAccounts | Nie |  Nie |
| billingAccounts/invoices | Nie |  Nie |
| billingAccounts/invoiceSections | Nie |  Nie |
| billingAccounts/invoiceSections/billingSubscriptions | Nie |  Nie |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Nie |  Nie |
| billingAccounts/invoiceSections/importRequests | Nie |  Nie |
| billingAccounts/invoiceSections/initiateImportRequest | Nie |  Nie |
| billingAccounts/invoiceSections/initiateTransfer | Nie |  Nie |
| billingAccounts/invoiceSections/operationStatus | Nie |  Nie |
| billingAccounts/invoiceSections/products | Nie |  Nie |
| billingAccounts/invoiceSections/transfers | Nie |  Nie |
| billingAccounts/products | Nie |  Nie |
| billingAccounts/projects | Nie |  Nie |
| billingAccounts/projects/billingSubscriptions | Nie |  Nie |
| billingAccounts/projects/importRequests | Nie |  Nie |
| billingAccounts/projects/initiateImportRequest | Nie |  Nie |
| billingAccounts/projects/operationStatus | Nie |  Nie |
| billingAccounts/projects/products | Nie |  Nie |
| billingAccounts/transakcje | Nie |  Nie |
| billingPeriods | Nie |  Nie |
| BillingPermissions | Nie |  Nie |
| billingProperty | Nie |  Nie |
| BillingRoleAssignments | Nie |  Nie |
| BillingRoleDefinitions | Nie |  Nie |
| CreateBillingRoleAssignment | Nie |  Nie |
| działy | Nie |  Nie |
| enrollmentAccounts | Nie |  Nie |
| importRequests | Nie |  Nie |
| importRequests/acceptImportRequest | Nie |  Nie |
| importRequests/declineImportRequest | Nie |  Nie |
| Faktury | Nie |  Nie |
| Transfery | Nie |  Nie |
| transfery/acceptTransfer | Nie |  Nie |
| transfery/declineTransfer | Nie |  Nie |
| transfers/operationStatus | Nie |  Nie |
| usagePlans | Nie |  Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| mapApis | Tak | Tak |
| updateCommunicationPreference | Nie |  Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| BizTalk | Tak | Tak |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Nie |  Nie |
| blueprintAssignments/assignmentOperations | Nie |  Nie |
| blueprintAssignments/operations | Nie |  Nie |
| Plany | Nie |  Nie |
| schematy/artefaktów | Nie |  Nie |
| schematy/wersji | Nie |  Nie |
| schematy/wersji/artefaktów | Nie |  Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| botServices | Tak | Tak |
| botServices/kanałów | Nie |  Nie |
| botServices/połączeń | Nie |  Nie |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Redis | Tak | Yes |
| RedisConfigDefinition | Nie |  Nie |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| appliedReservations | Nie |  Nie |
| calculatePrice | Nie |  Nie |
| katalogi | Nie |  Nie |
| commercialReservationOrders | Nie |  Nie |
| reservationOrders | Nie |  Nie |
| reservationOrders/calculateRefund | Nie |  Nie |
| reservationOrders/merge | Nie |  Nie |
| reservationOrders/rezerwacji | Nie |  Nie |
| reservationOrders/reservations/revisions | Nie |  Nie |
| reservationOrders/return | Nie |  Nie |
| reservationOrders/split | Nie |  Nie |
| reservationOrders/wymiany | Nie |  Nie |
| zastrzeżenia | Nie |  Nie |
| Zasoby | Nie |  Nie |
| validateReservationOrder | Nie |  Nie |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| edgenodes | Nie |  Nie |
| Profile | Tak | Tak |
| Profile/punktów końcowych | Tak | Tak |
| punkty końcowe/profile/customdomains | Nie |  Nie |
| punkty końcowe/profile/źródła | Nie |  Nie |
| validateProbe | Nie |  Nie |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| certificateOrders | Yes | Tak |
| zamówień certyfikatów/certyfikaty | Nie |  Nie |
| validateCertificateRegistrationInformation | Nie |  Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Możliwości | Nie |  Nie |
| domainNames | Nie |  Nie |
| domainNames/funkcji | Nie |  Nie |
| domainNames/internalLoadBalancers | Nie |  Nie |
| domainNames/serviceCertificates | Nie |  Nie |
| domainNames/miejsca | Nie |  Nie |
| domainNames/miejsc/role | Nie |  Nie |
| moveSubscriptionResources | Nie |  Nie |
| operatingSystemFamilies | Nie |  Nie |
| operatingSystems | Nie |  Nie |
| quotas | Nie |  Nie |
| resourceTypes | Nie |  Nie |
| validateSubscriptionMoveAvailability | Nie |  Nie |
| maszyn wirtualnych | Nie |  Nie |
| maszyn wirtualnych/diagnosticSettings | Nie |  Nie |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Nie |  Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Możliwości | Nie |  Nie |
| expressRouteCrossConnections | Nie |  Nie |
| expressRouteCrossConnections/peerings | Nie |  Nie |
| gatewaySupportedDevices | Nie |  Nie |
| networkSecurityGroups | Nie |  Nie |
| quotas | Nie |  Nie |
| reservedIps | Nie |  Nie |
| virtualNetworks | Nie |  Nie |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nie |  Nie |
| virtualNetworks/virtualNetworkPeerings | Nie |  Nie |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Możliwości | Nie |  Nie |
| Dyski | Nie |  Nie |
| images | Nie |  Nie |
| osImages | Nie |  Nie |
| osPlatformImages | Nie |  Nie |
| publicImages | Nie |  Nie |
| quotas | Nie |  Nie |
| storageAccounts | Nie |  Nie |
| storageAccounts/services | Nie |  Nie |
| storageAccounts/services/diagnosticSettings | Nie |  Nie |
| storageAccounts/vmImages | Nie |  Nie |
| Dozwolona | Nie |  Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Yes |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Usługi RateCard | Nie |  Nie |
| UsageAggregates | Nie |  Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| availabilitySets | Yes | Tak |
| Dyski | Tak | Tak |
| images | Yes | Tak |
| restorePointCollections | Tak | Tak |
| restorePointCollections/restorePoints | Nie |  Nie |
| sharedVMImages | Tak | Tak |
| sharedVMImages/wersji | Tak | Tak |
| Migawki | Tak | Tak |
| maszyn wirtualnych | Tak | Yes |
| maszyn wirtualnych/diagnosticSettings | Nie |  Nie |
| virtualMachines/extensions | Tak | Yes |
| virtualMachineScaleSets | Yes | Tak |
| virtualMachineScaleSets/extensions | Nie |  Nie |
| virtualMachineScaleSets/networkInterfaces | Nie |  Nie |
| virtualMachineScaleSets/publicIPAddresses | Nie |  Nie |
| virtualMachineScaleSets/virtualMachines | Nie |  Nie |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nie |  Nie |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| AggregatedCost | Nie |  Nie |
| Salda | Nie |  Nie |
| Budżety | Nie |  Nie |
| Opłaty za | Nie |  Nie |
| CostTags | Nie |  Nie |
| Środki na korzystanie z | Nie |  Nie |
| zdarzenia | Nie |  Nie |
| Prognozy | Nie |  Nie |
| partie | Nie |  Nie |
| Marketplaces | Nie |  Nie |
| Pricesheets | Nie |  Nie |
| Produkty | Nie |  Nie |
| ReservationDetails | Nie |  Nie |
| ReservationRecommendations | Nie |  Nie |
| ReservationSummaries | Nie |  Nie |
| ReservationTransactions | Nie |  Nie |
| `Tags` | Nie |  Nie |
| Warunki | Nie |  Nie |
| UsageDetails | Nie |  Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| containerGroups | Tak | Tak |
| serviceAssociationLinks | Nie |  Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| rejestry | Tak | Tak |
| rejestry/kompilacji | Nie |  Nie |
| rejestry/kompilacji/anulować | Nie |  Nie |
| registries/builds/getLogLink | Nie |  Nie |
| registries/buildTasks | Yes | Yes |
| registries/buildTasks/steps | Nie |  Nie |
| registries/eventGridFilters | Nie |  Nie |
| registries/getBuildSourceUploadUrl | Nie |  Nie |
| registries/GetCredentials | Nie |  Nie |
| registries/importImage | Nie |  Nie |
| rejestry/queueBuild | Nie |  Nie |
| registries/regenerateCredential | Nie |  Nie |
| registries/regenerateCredentials | Nie |  Nie |
| rejestry/replikacji | Tak | Tak |
| rejestry/przebiegów | Nie |  Nie |
| rejestry/uruchomienia/anulować | Nie |  Nie |
| registries/scheduleRun | Nie |  Nie |
| rejestry/zadań | Tak | Yes |
| registries/updatePolicies | Nie |  Nie |
| rejestry/elementów webhook | Tak | Tak |
| registries/webhooks/getCallbackConfig | Nie |  Nie |
| elementy webhook/rejestrów/ping | Nie |  Nie |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| containerServices | Tak | Tak |
| managedClusters | Yes | Tak |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| aplikacje | Yes | Yes |
| updateCommunicationPreference | Nie |  Nie |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Yes | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Alerty | Nie |  Nie |
| BillingAccounts | Nie |  Nie |
| Łączniki | Tak | Yes |
| działy | Nie |  Nie |
| Wymiary | Nie |  Nie |
| enrollmentAccounts | Nie |  Nie |
| Zapytanie | Nie |  Nie |
| register | Nie |  Nie |
| Reportconfigs | Nie |  Nie |
| Raporty | Nie |  Nie |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Koncentratory | Tak | Yes |
| hubs/authorizationPolicies | Nie |  Nie |
| Koncentratory/łączników | Nie |  Nie |
| Koncentratory/łączników/mapowania | Nie |  Nie |
| hubs/interactions | Nie |  Nie |
| hubs/kpi | Nie |  Nie |
| hubs/links | Nie |  Nie |
| Koncentratory/profile | Nie |  Nie |
| Koncentratory/liczba przypisań ról | Nie |  Nie |
| Koncentratory/role | Nie |  Nie |
| hubs/suggestTypeSchema | Nie |  Nie |
| Koncentratory/widoków | Nie |  Nie |
| hubs/widgetTypes | Nie |  Nie |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Zadania | Tak | Tak |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Yes | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Obszary robocze | Yes | Nie |
| workspaces/virtualNetworkPeerings | Nie |  Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| katalogi | Tak | Tak |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| connectionManagers | Yes | Tak |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| dataFactories | Tak | Nie |
| dataFactories/diagnosticSettings | Nie |  Nie |
| dataFactorySchema | Nie |  Nie |
| fabryki | Tak | Nie |
| fabryki/integrationRuntimes | Nie |  Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Tak |
| accounts/dataLakeStoreAccounts | Nie |  Nie |
| accounts/storageAccounts | Nie |  Nie |
| accounts/storageAccounts/containers | Nie |  Nie |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Tak |
| accounts/eventGridFilters | Nie |  Nie |
| konta/firewallRules | Nie |  Nie |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| services | Tak | Tak |
| usługi/projekty | Tak | Tak |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Serwery | Tak | Tak |
| servers/recoverableServers | Nie |  Nie |
| servers/virtualNetworkRules | Nie |  Nie |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Serwery | Tak | Tak |
| servers/recoverableServers | Nie |  Nie |
| servers/virtualNetworkRules | Nie |  Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Serwery | Tak | Tak |
| serwery/doradców | Nie |  Nie |
| serwery/queryTexts | Nie |  Nie |
| servers/recoverableServers | Nie |  Nie |
| servers/topQueryStatistics | Nie |  Nie |
| servers/virtualNetworkRules | Nie |  Nie |
| serwery/waitStatistics | Nie |  Nie |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| IotHubs | Tak | Tak |
| IotHubs/eventGridFilters | Nie |  Nie |
| ProvisioningServices | Tak | Tak |
| Sposoby użycia | Nie |  Nie |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Kontrolery | Tak | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Warsztaty | Yes | Tak |
| labs/serviceRunners | Tak | Yes |
| laboratoria/maszyn wirtualnych | Tak | Tak |
| Harmonogramy | Yes | Tak |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Nie |  Nie |
| databaseAccounts | Tak | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| domeny | Tak | Tak |
| domeny/domainOwnershipIdentifiers | Nie |  Nie |
| generateSsoRequest | Nie |  Nie |
| topLevelDomains | Nie |  Nie |
| validateDomainRegistrationInformation | Nie |  Nie |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| lcsprojects | Nie |  Nie |
| lcsprojects/clouddeployments | Nie |  Nie |
| lcsprojects/łączników | Nie |  Nie |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| domeny | Tak | Nie |
| domeny/tematów | Nie |  Nie |
| eventSubscriptions | Nie |  Nie |
| extensionTopics | Nie |  Nie |
| Tematy | Tak | Nie |
| topicTypes | Nie |  Nie |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Klastry | Tak | Nie |
| Przestrzenie nazw | Yes | Nie |
| przestrzenie nazw/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/disasterrecoveryconfigs | Nie |  Nie |
| przestrzenie nazw/eventhubs | Nie |  Nie |
| przestrzenie nazw/eventhubs/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/eventhubs/consumergroups | Nie |  Nie |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| danych | Nie |  Nie |
| dostawcy | Nie |  Nie |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Rejestrowanie | Nie |  Nie |
| galleryitems | Nie |  Nie |
| generateartifactaccessuri | Nie |  Nie |
| myareas | Nie |  Nie |
| myareas/obszarów | Nie |  Nie |
| myareas/obszarów/obszarów | Nie |  Nie |
| myareas/areas/areas/galleryitems | Nie |  Nie |
| myareas/areas/galleryitems | Nie |  Nie |
| myareas/galleryitems | Nie |  Nie |
| register | Nie |  Nie |
| Zasoby | Nie |  Nie |
| retrieveresourcesbyid | Nie |  Nie |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Nie |  Nie |
| Oprogramowanie | Nie |  Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| hanaInstances | Tak |  Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Klastry | Yes | Yes |
| klastry/aplikacji | Nie |  Nie |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Zadania | Tak | Tak |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| labelGroups | Nie |  Nie |
| labelGroups/etykiety | Nie |  Nie |
| labelGroups/labels/conditions | Nie |  Nie |
| labelGroups/labels/subLabels | Nie |  Nie |
| labelGroups/labels/subLabels/conditions | Nie |  Nie |

## <a name="microsoftinsights"></a>microsoft.insights
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| actiongroups | Tak | Yes |
| activityLogAlerts | Tak | Tak |
| alertrules | Yes | Tak |
| automatedExportSettings | Nie |  Nie |
| wartość autoscalesettings | Yes | Tak |
| Punkt odniesienia | Nie |  Nie |
| calculatebaseline | Nie |  Nie |
| Składniki | Yes | Tak |
| składniki/zdarzenia | Nie |  Nie |
| składniki/pricingPlans | Nie |  Nie |
| składniki/zapytania | Nie |  Nie |
| diagnosticSettings | Nie |  Nie |
| diagnosticSettingsCategories | Nie |  Nie |
| eventCategories | Nie |  Nie |
| eventtypes | Nie |  Nie |
| extendedDiagnosticSettings | Nie |  Nie |
| logDefinitions | Nie |  Nie |
| logprofiles | Nie |  Nie |
| logs | Nie |  Nie |
| metricAlerts | Yes | Tak |
| migrateToNewPricingModel | Nie |  Nie |
| myWorkbooks | Nie |  Nie |
| — zapytania | Nie |  Nie |
| rollbackToLegacyPricingModel | Nie |  Nie |
| scheduledqueryrules | Yes | Tak |
| vmInsightsOnboardingStatuses | Nie |  Nie |
| testy internetowe | Tak | Tak |
| Skoroszyty | Tak | Tak |

## <a name="microsoftintune"></a>Microsoft.Intune
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nie |  Nie |
| diagnosticSettingsCategories | Nie |  Nie |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| IoTApps | Tak | Tak |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Graph | Tak | Tak |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| deletedVaults | Nie |  Nie |
| Magazyny | Tak | Yes |
| Magazyny/accessPolicies | Nie |  Nie |
| Magazyny/klucze tajne | Nie |  Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Klastry | Tak | Tak |
| klastry/baz danych | Nie |  Nie |
| klastry/baz danych/połączeń danych | Nie |  Nie |
| klastry/baz danych/eventhubconnections | Nie |  Nie |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| labaccounts | Yes | Tak |
| użytkownicy | Nie |  Nie |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Yes |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Yes | Yes |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| logs | Nie |  Nie |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| integrationAccounts | Tak | Tak |
| Przepływy pracy | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| commitmentPlans | Yes | Tak |
| webServices | Tak | Yes |
| Obszary robocze | Tak | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Tak |
| konta/obszarów roboczych | Tak | Yes |
| obszary robocze/kont/projektów | Tak | Tak |
| teamAccounts | Tak | Yes |
| teamAccounts/obszarów roboczych | Tak | Tak |
| obszary robocze/teamAccounts/projektów | Tak | Yes |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Obszary robocze | Tak | Tak |
| obszary robocze/usługi obliczeniowe | Nie |  Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Tożsamości | Nie |  Nie |
| userAssignedIdentities | Yes | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| getEntities | Nie |  Nie |
| managementGroups | Nie |  Nie |
| Zasoby | Nie |  Nie |
| startTenantBackfill | Nie |  Nie |
| tenantBackfillStatus | Nie |  Nie |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Yes |
| accounts/eventGridFilters | Nie |  Nie |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Oferty | Nie |  Nie |
| offerTypes | Nie |  Nie |
| offerTypes/wydawcy | Nie |  Nie |
| wydawcy/offerTypes/oferty | Nie |  Nie |
| offerTypes/wydawcy/oferty/plany | Nie |  Nie |
| offerTypes/wydawcy/oferty/planów/umowy | Nie |  Nie |
| offerTypes/publishers/offers/plans/configs | Nie |  Nie |
| offerTypes/publishers/offers/plans/configs/importImage | Nie |  Nie |
| privategalleryitems | Nie |  Nie |
| Produkty | Nie |  Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| classicDevServices | Tak | Tak |
| updateCommunicationPreference | Nie |  Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Umowy | Nie |  Nie |
| offertypes | Nie |  Nie |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| mediaservices | Tak | Tak |
| mediaservices/accountFilters | Nie |  Nie |
| mediaservices/zasobów | Nie |  Nie |
| zasobów/mediaservices/assetFilters | Nie |  Nie |
| mediaservices/contentKeyPolicies | Nie |  Nie |
| mediaservices/eventGridFilters | Nie |  Nie |
| mediaservices/liveEventOperations | Nie |  Nie |
| mediaservices/liveEvents | Tak | Yes |
| mediaservices/liveEvents/liveOutputs | Nie |  Nie |
| mediaservices/liveOutputOperations | Nie |  Nie |
| mediaservices/streamingEndpointOperations | Nie |  Nie |
| mediaservices/punkty | Tak | Yes |
| mediaservices/streamingLocators | Nie |  Nie |
| mediaservices/streamingPolicies | Nie |  Nie |
| mediaservices/przekształceń | Nie |  Nie |
| zadania mediaservices/przekształceń | Nie |  Nie |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Projekty | Tak | Yes |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| applicationGateways | Tak | Nie |
| applicationSecurityGroups | Tak | Tak |
| azureFirewallFqdnTags | Nie |  Nie |
| azureFirewalls | Tak | Nie |
| bgpServiceCommunities | Nie |  Nie |
| Połączenia | Tak | Tak |
| ddosCustomPolicies | Yes | Yes |
| ddosProtectionPlans | Tak | Tak |
| dnsOperationStatuses | Nie |  Nie |
| dnszones | Tak | Yes |
| dnszones/A | Nie |  Nie |
| dnszones/AAAA | Nie |  Nie |
| dnszones/all | Nie |  Nie |
| dnszones/CAA | Nie |  Nie |
| dnszones/CNAME | Nie |  Nie |
| dnszones/MX | Nie |  Nie |
| dnszones/NS | Nie |  Nie |
| dnszones/PTR | Nie |  Nie |
| dnszones/recordsets | Nie |  Nie |
| dnszones/SOA | Nie |  Nie |
| dnszones/SRV | Nie |  Nie |
| dnszones/TXT | Nie |  Nie |
| expressRouteCircuits | Tak  | Nie |
| expressRouteServiceProviders | Nie |  Nie |
| frontdoors | Yes | Yes |
| frontdoorWebApplicationFirewallPolicies | Yes | Tak |
| getDnsResourceReference | Nie |  Nie |
| interfaceEndpoints | Yes | Yes |
| internalNotify | Nie |  Nie |
| loadBalancers | Tak | Nie |
| localNetworkGateways | Tak | Tak |
| natGateways | Yes | Yes |
| networkIntentPolicies | Tak | Yes |
| Interfejsy | Tak | Tak |
| networkProfiles | Yes | Tak |
| networkSecurityGroups | Tak | Tak |
| networkWatchers | Yes | Nie |
| networkWatchers/connectionMonitors | Yes | Nie |
| networkWatchers/lenses | Tak | Nie |
| networkWatchers/pingMeshes | Tak | Nie |
| privateLinkServices | Tak | Tak |
| publicIPAddresses | Yes | Yes |
| publicIPPrefixes | Yes | Tak |
| routeFilters | Tak | Tak |
| routeTables | Tak | Tak |
| serviceEndpointPolicies | Tak | Yes |
| trafficManagerGeographicHierarchies | Nie |  Nie |
| trafficmanagerprofiles | Tak | Tak |
| trafficmanagerprofiles/heatMaps | Nie |  Nie |
| virtualHubs | Tak | Yes |
| virtualNetworkGateways | Tak | Nie |
| virtualNetworks | Tak | Yes |
| virtualNetworks/subnets | Nie |  Nie |
| virtualNetworkTaps | Tak | Yes |
| virtualWans | Tak | Yes |
| vpnGateways | Tak | Nie |
| vpnSites | Tak | Tak |
| webApplicationFirewallPolicies | Yes | Yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Przestrzenie nazw | Tak | Nie |
| namespaces/notificationHubs | Yes | Nie |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| urządzenia | Nie |  Nie |
| linkTargets | Nie |  Nie |
| storageInsightConfigs | Nie |  Nie |
| Obszary robocze | Tak | Tak |
| workspaces/dataSources | Nie |  Nie |
| workspaces/linkedServices | Nie |  Nie |
| workspaces/query | Nie |  Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| managementassociations | Nie |  Nie |
| managementconfigurations | Yes | Tak |
| rozwiązania | Tak | Yes |
| Widoki | Yes | Tak |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| policyEvents | Nie |  Nie |
| policyStates | Nie |  Nie |
| policyTrackedResources | Nie |  Nie |
| korygowania funkcję | Nie |  Nie |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| konsole | Nie |  Nie |
| Pulpity nawigacyjne | Tak | Tak |
| userSettings | Nie |  Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| workspaceCollections | Tak | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Pojemności | Tak | Yes |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Yes | Tak |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Nie |  Nie |
| Magazyny | Tak | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Przestrzenie nazw | Yes | Tak |
| przestrzenie nazw/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/hybridconnections | Nie |  Nie |
| przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie |  Nie |
| namespaces/wcfrelays | Nie |  Nie |
| przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie |  Nie |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Zasoby | Nie |  Nie |
| subscriptionsStatus | Nie |  Nie |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Nie |  Nie |
| childAvailabilityStatuses | Nie |  Nie |
| childResources | Nie |  Nie |
| zdarzenia | Nie |  Nie |
| impactedResources | Nie |  Nie |
| Powiadomienia | Nie |  Nie |

## <a name="microsoftresources"></a>Microsoft.Resources
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Wdrożenia | Nie |  Nie |
| Wdrożenia/operations | Nie |  Nie |
| Łącza | Nie |  Nie |
| notifyResourceJobs | Nie |  Nie |
| dostawcy | Nie |  Nie |
| resourceGroups | Nie |  Nie |
| Zasoby | Nie |  Nie |
| Subskrypcje | Nie |  Nie |
| Subskrypcje/dostawców | Nie |  Nie |
| subscriptions/resourceGroups | Nie |  Nie |
| zasobów/resourcegroups/subskrypcji | Nie |  Nie |
| Zasobów/subskrypcji | Nie |  Nie |
| subscriptions/tagnames | Nie |  Nie |
| subscriptions/tagNames/tagValues | Nie |  Nie |
| Dzierżaw | Nie |  Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| aplikacje | Yes | Tak |
| saasresources | Nie |  Nie |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| przepływy | Yes | Tak |
| kolekcjach | Tak | Tak |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Nie |  Nie |
| searchServices | Tak | Tak |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Nie |  Nie |
| alerts | Nie |  Nie |
| allowedConnections | Nie |  Nie |
| urządzenia | Nie |  Nie |
| applicationWhitelistings | Nie |  Nie |
| AutoProvisioningSettings | Nie |  Nie |
| Zachowania zgodności z prawnymi | Nie |  Nie |
| dataCollectionAgents | Nie |  Nie |
| discoveredSecuritySolutions | Nie |  Nie |
| externalSecuritySolutions | Nie |  Nie |
| InformationProtectionPolicies | Nie |  Nie |
| jitNetworkAccessPolicies | Nie |  Nie |
| Monitorowanie | Nie |  Nie |
| monitorowanie/ochrony przed złośliwym kodem | Nie |  Nie |
| monitorowanie/punkt odniesienia | Nie |  Nie |
| monitorowanie poprawki | Nie |  Nie |
| Zasady | Nie |  Nie |
| cen | Nie |  Nie |
| securityContacts | Nie |  Nie |
| securitySolutions | Nie |  Nie |
| securitySolutionsReferenceData | Nie |  Nie |
| securityStatus | Nie |  Nie |
| securityStatus/punktów końcowych | Nie |  Nie |
| securityStatus/subnets | Nie |  Nie |
| securityStatus/maszyn wirtualnych | Nie |  Nie |
| securityStatuses | Nie |  Nie |
| securityStatusesSummaries | Nie |  Nie |
| settings | Nie |  Nie |
| zadania | Nie |  Nie |
| Topologie | Nie |  Nie |
| workspaceSettings | Nie |  Nie |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nie |  Nie |
| diagnosticSettingsCategories | Nie |  Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Przestrzenie nazw | Tak | Nie |
| przestrzenie nazw/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/disasterrecoveryconfigs | Nie |  Nie |
| przestrzenie nazw/eventgridfilters | Nie |  Nie |
| namespaces/queues | Nie |  Nie |
| przestrzenie nazw/kolejki/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/tematów | Nie |  Nie |
| przestrzenie nazw/tematów/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/tematów/subskrypcji | Nie |  Nie |
| przestrzenie nazw/tematów/subskrypcji/reguły | Nie |  Nie |
| premiumMessagingRegions | Nie |  Nie |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Klastry | Tak | Tak |
| klastry/aplikacji | Nie |  Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| aplikacje | Tak | Tak |
| Bramy | Tak | Tak |
| Sieci | Tak | Tak |
| wpisy tajne | Tak | Tak |
| volumes | Tak | Tak |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| SignalR | Tak | Tak |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Yes | Tak |
| urządzenia | Tak | Yes |
| applicationDefinitions | Tak | Tak |
| aplikacje | Tak | Tak |
| jitRequests | Yes | Tak |

## <a name="microsoftsql"></a>Microsoft.SQL
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| managedInstances | Tak | Tak |
| managedInstances/baz danych | Tak (zobacz uwaga poniżej) | Tak |
| managedInstances/databases/backupShortTermRetentionPolicies | Nie | Nie |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nie | Nie |
| managedInstances/databases/vulnerabilityAssessments | Nie | Nie |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Nie | Nie |
| managedInstances/encryptionProtector | Nie | Nie |
| managedInstances/kluczy | Nie | Nie |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nie | Nie |
| managedInstances/vulnerabilityAssessments | Nie | Nie |
| Serwery | Yes | Tak |
| serwery/administratorów | Nie |  Nie |
| serwery/communicationLinks | Nie |  Nie |
| serwery/baz danych | Tak (zobacz uwaga poniżej) | Tak |
| servers/encryptionProtector | Nie |  Nie |
| serwery/firewallRules | Nie |  Nie |
| serwery/kluczy | Nie |  Nie |
| servers/restorableDroppedDatabases | Nie |  Nie |
| serwery/serviceobjectives | Nie |  Nie |
| serwery/tdeCertificates | Nie |  Nie |

> [!NOTE]
> Wzorzec bazy danych nie obsługuje tagi, ale innych baz danych, w tym baz danych Azure SQL Data Warehouse, obsługuje tagi. Baz danych Azure SQL Data Warehouse musi znajdować się w aktywny (nie jest wstrzymana) stanu.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Tak | Tak |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nie |  Nie |
| SqlVirtualMachines | Yes | Tak |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| storageAccounts | Tak | Tak |
| storageAccounts/blobServices | Nie |  Nie |
| storageAccounts/fileServices | Nie |  Nie |
| storageAccounts/queueServices | Nie |  Nie |
| storageAccounts/services | Nie |  Nie |
| storageAccounts/tableServices | Nie |  Nie |
| Sposoby użycia | Nie |  Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| storageSyncServices | Yes | Tak |
| storageSyncServices/registeredServers | Nie |  Nie |
| storageSyncServices/syncGroups | Nie |  Nie |
| storageSyncServices/syncGroups/cloudEndpoints | Nie |  Nie |
| storageSyncServices/syncGroups/serverEndpoints | Nie |  Nie |
| storageSyncServices/workflows | Nie |  Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Menedżerowie | Tak | Tak |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| streamingjobs | Tak (zobacz uwaga poniżej) | Yes |
| streamingjobs/diagnosticSettings | Nie |  Nie |

> [!NOTE]
> Nie można dodać tag, gdy streamingjobs jest uruchomiony. Zatrzymaj zasobów, aby dodać tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| CreateSubscription | Nie |  Nie |
| SubscriptionDefinitions | Nie |  Nie |
| SubscriptionOperations | Nie |  Nie |

## <a name="microsoftsupport"></a>microsoft.support
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| supporttickets | Nie |  Nie |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| providerRegistrations | Tak | Tak |
| Zasoby | Yes | Tak |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Środowiska | Tak | Nie |
| środowiska/accessPolicies | Nie |  Nie |
| środowiska/eventsources | Tak | Nie |
| environments/referenceDataSets | Tak | Nie |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| account | Tak | Yes |
| konta i rozszerzeń | Yes | Tak |
| konto lub projekt docelowy | Tak | Yes |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Nie |  Nie |
| apiManagementAccounts/apiAcls | Nie |  Nie |
| apiManagementAccounts/apis | Nie |  Nie |
| apiManagementAccounts/apis/apiAcls | Nie |  Nie |
| apiManagementAccounts/apis/connectionAcls | Nie |  Nie |
| apiManagementAccounts/apis/connections | Nie |  Nie |
| apiManagementAccounts/apis/connections/connectionAcls | Nie |  Nie |
| apiManagementAccounts/apis/localizedDefinitions | Nie |  Nie |
| apiManagementAccounts/connectionAcls | Nie |  Nie |
| apiManagementAccounts/connections | Nie |  Nie |
| billingMeters | Nie |  Nie |
| Certyfikaty | Tak | Yes |
| connectionGateways | Tak | Yes |
| Połączenia | Yes | Tak |
| customApis | Tak | Tak |
| deletedSites | Nie |  Nie |
| — funkcje | Nie |  Nie |
| hostingEnvironments | Yes | Tak |
| hostingEnvironments/multiRolePools | Nie |  Nie |
| hostingEnvironments/multiRolePools/wystąpieniach | Nie |  Nie |
| hostingEnvironments/workerPools | Nie |  Nie |
| hostingEnvironments/workerPools/instances | Nie |  Nie |
| publishingUsers | Nie |  Nie |
| Zalecenia | Nie |  Nie |
| resourceHealthMetadata | Nie |  Nie |
| środowiska uruchomieniowe | Nie |  Nie |
| farm serwerów | Yes | Tak |
| farm serwerów/procesów roboczych | Nie |  Nie |
| Lokacje | Tak | Tak |
| Lokacje/domainOwnershipIdentifiers | Nie |  Nie |
| sites/hostNameBindings | Nie |  Nie |
| Lokacje/wystąpieniach | Nie |  Nie |
| / wystąpień/rozszerzenia usługi witryny | Nie |  Nie |
| sites/premieraddons | Tak | Tak |
| Lokacje/zalecenia | Nie |  Nie |
| sites/resourceHealthMetadata | Nie |  Nie |
| Lokacje/miejsca | Tak | Tak |
| sites/slots/hostNameBindings | Nie |  Nie |
| Lokacje/miejsc/wystąpieniach | Nie |  Nie |
| / miejsc/wystąpień/rozszerzenia usługi witryny | Nie |  Nie |
| sourceControls | Nie |  Nie |
| Sprawdzanie poprawności | Nie |  Nie |
| verifyHostingEnvironmentVnet | Nie |  Nie |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nie |  Nie |
| diagnosticSettingsCategories | Nie |  Nie |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| DeviceServices | Tak | Yes |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Typ zasobu | Obsługa tagów | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Składniki | Nie |  Nie |
| componentsSummary | Nie |  Nie |
| monitorInstances | Nie |  Nie |
| monitorInstancesSummary | Nie |  Nie |
| Monitory | Nie |  Nie |
| notificationSettings | Nie |  Nie |

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak stosowanie tagów do zasobów, zobacz [organizowania zasobów platformy Azure za pomocą tagów](resource-group-using-tags.md).
