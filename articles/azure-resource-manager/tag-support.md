---
title: Obsługa tagów Azure Resource Manager dla zasobów
description: Pokazuje, które typy zasobów platformy Azure obsługują Tagi. Zawiera szczegółowe informacje dotyczące wszystkich usług platformy Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 72bb11cd064c90c2bbe1e9e6452dcbf07fe37817
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304867"
---
# <a name="tag-support-for-azure-resources"></a>Obsługa tagów dla zasobów platformy Azure
W tym artykule opisano, czy typ zasobu obsługuje [Tagi](resource-group-using-tags.md). Kolumna z etykietą **obsługuje znaczniki** wskazuje, czy typ zasobu ma właściwość dla tagu. Kolumna **tag z etykietą w raporcie koszt** wskazuje, czy ten typ zasobu przekazuje tag do raportu kosztów.

Aby uzyskać te same dane jak plik z wartościami rozdzielanymi przecinkami, Pobierz [tag-support. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| DomainServices | Tak | Tak |
| DomainServices/oucontainer | Nie | Nie |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nie |  Nie |
| diagnosticSettingsCategories | Nie |  Nie |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| supportProviders | Nie |  Nie |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| aadsupportcases | Nie |  Nie |
| addsservices | Nie |  Nie |
| pracownicy | Nie |  Nie |
| anonymousapiusers | Nie |  Nie |
| konfiguracja | Nie |  Nie |
| logs | Nie |  Nie |
| raporty | Nie |  Nie |
| services | Nie |  Nie |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| komputerów | Nie |  Nie |
| generateRecommendations | Nie |  Nie |
| Zalecenia | Nie |  Nie |
| pominięć | Nie |  Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
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
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Serwerem | Tak | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| reportFeedback | Nie |  Nie |
| usługa | Tak | Tak |
| validateServiceName | Nie |  Nie |

## <a name="microsoftattestation"></a>Microsoft. zaświadczanie
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| attestationProviders | Nie |  Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| classicAdministrators | Nie |  Nie |
| denyAssignments | Nie |  Nie |
| elevateAccess | Nie |  Nie |
| Zamki | Nie |  Nie |
| uprawnienia | Nie |  Nie |
| policyAssignments | Nie |  Nie |
| policyDefinitions | Nie |  Nie |
| policySetDefinitions | Nie |  Nie |
| providerOperations | Nie |  Nie |
| roleAssignments | Nie |  Nie |
| roleDefinitions | Nie |  Nie |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| automationAccounts | Yes | Tak |
| automationAccounts/konfiguracje | Yes | Tak |
| automationAccounts/zadania | Nie |  Nie |
| automationAccounts/elementy Runbook | Tak | Tak |
| automationAccounts/softwareUpdateConfigurations | Nie | Nie |
| automationAccounts/elementy webhook | Nie |  Nie |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| wiejski | Nie |  Nie |
| środowiska/konta | Nie |  Nie |
| środowiska/konta/przestrzenie nazw | Nie |  Nie |
| środowiska/konta/przestrzenie nazw/konfiguracje | Nie |  Nie |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| b2cDirectories | Yes | Nie |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| rejestracje | Tak | Yes |
| rejestracje/customerSubscriptions | Nie |  Nie |
| rejestracje/produkty | Nie |  Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| batchAccounts | Yes | Tak |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| billingAccounts | Nie |  Nie |
| billingAccounts/billingProfiles | Nie |  Nie |
| billingAccounts/billingProfiles/billingSubscriptions | Nie |  Nie |
| billingAccounts/billingProfiles/faktury | Nie |  Nie |
| billingAccounts/billingProfiles/faktury/arkusza cen | Nie |  Nie |
| billingAccounts/billingProfiles/operationStatus | Nie |  Nie |
| billingAccounts/billingProfiles/paymentMethods | Nie |  Nie |
| billingAccounts/billingProfiles/zasady | Nie |  Nie |
| billingAccounts/billingProfiles/pricesheet | Nie |  Nie |
| billingAccounts/billingProfiles/produkty | Nie |  Nie |
| billingAccounts/billingProfiles/Transactions | Nie |  Nie |
| billingAccounts/billingSubscriptions | Nie |  Nie |
| billingAccounts/działy | Nie |  Nie |
| billingAccounts/eligibleOffers | Nie |  Nie |
| billingAccounts/enrollmentAccounts | Nie |  Nie |
| billingAccounts/faktury | Nie |  Nie |
| billingAccounts/invoiceSections | Nie |  Nie |
| billingAccounts/invoiceSections/billingSubscriptions | Nie |  Nie |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Nie |  Nie |
| billingAccounts/invoiceSections/importRequests | Nie |  Nie |
| billingAccounts/invoiceSections/initiateImportRequest | Nie |  Nie |
| billingAccounts/invoiceSections/initiateTransfer | Nie |  Nie |
| billingAccounts/invoiceSections/operationStatus | Nie |  Nie |
| billingAccounts/invoiceSections/products | Nie |  Nie |
| billingAccounts/invoiceSections/transfery | Nie |  Nie |
| billingAccounts/produkty | Nie |  Nie |
| billingAccounts/projekty | Nie |  Nie |
| billingAccounts/projects/billingSubscriptions | Nie |  Nie |
| billingAccounts/projects/importRequests | Nie |  Nie |
| billingAccounts/projects/initiateImportRequest | Nie |  Nie |
| billingAccounts/projects/operationStatus | Nie |  Nie |
| billingAccounts/projekty/produkty | Nie |  Nie |
| billingAccounts/transakcje | Nie |  Nie |
| billingPeriods | Nie |  Nie |
| BillingPermissions | Nie |  Nie |
| billingProperty | Nie |  Nie |
| BillingRoleAssignments | Nie |  Nie |
| BillingRoleDefinitions | Nie |  Nie |
| CreateBillingRoleAssignment | Nie |  Nie |
| działów | Nie |  Nie |
| enrollmentAccounts | Nie |  Nie |
| importRequests | Nie |  Nie |
| importRequests/acceptImportRequest | Nie |  Nie |
| importRequests/declineImportRequest | Nie |  Nie |
| faktur | Nie |  Nie |
| sunięcia | Nie |  Nie |
| transfery/acceptTransfer | Nie |  Nie |
| transfery/declineTransfer | Nie |  Nie |
| transfery/operationStatus | Nie |  Nie |
| usagePlans | Nie |  Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| mapApis | Tak | Tak |
| updateCommunicationPreference | Nie |  Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| BizTalk | Tak | Yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Nie |  Nie |
| blueprintAssignments/assignmentOperations | Nie |  Nie |
| blueprintAssignments/operacje | Nie |  Nie |
| plany | Nie |  Nie |
| plany/artefakty | Nie |  Nie |
| plany/wersje | Nie |  Nie |
| plany/wersje/artefakty | Nie |  Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| botServices | Yes | Yes |
| botServices/kanały | Nie |  Nie |
| botServices/połączenia | Nie |  Nie |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Redis | Tak | Tak |
| RedisConfigDefinition | Nie |  Nie |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| appliedReservations | Nie |  Nie |
| calculatePrice | Nie |  Nie |
| wykazy | Nie |  Nie |
| commercialReservationOrders | Nie |  Nie |
| reservationOrders | Nie |  Nie |
| reservationOrders/calculateRefund | Nie |  Nie |
| reservationOrders/Scal | Nie |  Nie |
| reservationOrders/rezerwacje | Nie |  Nie |
| reservationOrders/rezerwacje/poprawki | Nie |  Nie |
| reservationOrders/Return | Nie |  Nie |
| reservationOrders/Split | Nie |  Nie |
| reservationOrders/swap | Nie |  Nie |
| dokonując | Nie |  Nie |
| Produkcyjnych | Nie |  Nie |
| validateReservationOrder | Nie |  Nie |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| edgenodes | Nie |  Nie |
| profiles | Tak | Tak |
| Profile/punkty końcowe | Yes | Yes |
| Profile/punkty końcowe/customdomains | Nie |  Nie |
| Profile/punkty końcowe/źródła | Nie |  Nie |
| validateProbe | Nie |  Nie |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| certificateOrders | Tak | Yes |
| certificateOrders/certyfikaty | Nie |  Nie |
| validateCertificateRegistrationInformation | Nie |  Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| możliwość | Nie |  Nie |
| domainNames | Nie |  Nie |
| domainNames/możliwości | Nie |  Nie |
| domainNames/internalLoadBalancers | Nie |  Nie |
| domainNames/servicecertificates | Nie |  Nie |
| domainNames/miejsca | Nie |  Nie |
| domainNames/gniazda/role | Nie |  Nie |
| moveSubscriptionResources | Nie |  Nie |
| operatingSystemFamilies | Nie |  Nie |
| operatingSystems | Nie |  Nie |
| quotas | Nie |  Nie |
| resourceTypes | Nie |  Nie |
| validateSubscriptionMoveAvailability | Nie |  Nie |
| VirtualMachines | Nie |  Nie |
| virtualMachines/diagnosticSettings | Nie |  Nie |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Nie |  Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| możliwość | Nie |  Nie |
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
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| możliwość | Nie |  Nie |
| Dysku | Nie |  Nie |
| images | Nie |  Nie |
| osImages | Nie |  Nie |
| osPlatformImages | Nie |  Nie |
| publicImages | Nie |  Nie |
| quotas | Nie |  Nie |
| storageAccounts | Nie |  Nie |
| storageAccounts/usługi | Nie |  Nie |
| storageAccounts/usługi/diagnosticSettings | Nie |  Nie |
| storageAccounts/vmImages | Nie |  Nie |
| vmImages | Nie |  Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Tak |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| RateCard | Nie |  Nie |
| UsageAggregates | Nie |  Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| availabilitySets | Tak | Yes |
| Dysku | Tak | Yes |
| images | Tak | Tak |
| restorePointCollections | Yes | Yes |
| restorePointCollections/restorePoints | Nie |  Nie |
| sharedVMImages | Tak | Tak |
| sharedVMImages/wersje | Tak | Tak |
| Migawki | Tak | Tak |
| VirtualMachines | Yes | Yes |
| virtualMachines/diagnosticSettings | Nie |  Nie |
| virtualMachines/rozszerzenia | Tak | Yes |
| virtualMachineScaleSets | Yes | Yes |
| virtualMachineScaleSets/rozszerzenia | Nie |  Nie |
| virtualMachineScaleSets/networkInterfaces | Nie |  Nie |
| virtualMachineScaleSets/publicIPAddresses | Nie |  Nie |
| virtualMachineScaleSets/virtualMachines | Nie |  Nie |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nie |  Nie |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| AggregatedCost | Nie |  Nie |
| Salda | Nie |  Nie |
| Budżety | Nie |  Nie |
| Odsetk | Nie |  Nie |
| CostTags | Nie |  Nie |
| środki | Nie |  Nie |
| zdarzenia | Nie |  Nie |
| Prognozy | Nie |  Nie |
| ilości | Nie |  Nie |
| Platform handlowych | Nie |  Nie |
| Pricesheets | Nie |  Nie |
| wyrobów | Nie |  Nie |
| ReservationDetails | Nie |  Nie |
| ReservationRecommendations | Nie |  Nie |
| ReservationSummaries | Nie |  Nie |
| ReservationTransactions | Nie |  Nie |
| `Tags` | Nie |  Nie |
| Warunki | Nie |  Nie |
| UsageDetails | Nie |  Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| containerGroups | Tak | Tak |
| serviceAssociationLinks | Nie |  Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| wołuje | Tak | Tak |
| rejestry/kompilacje | Nie |  Nie |
| rejestry/kompilacje/anulowanie | Nie |  Nie |
| rejestry/kompilacje/getLogLink | Nie |  Nie |
| rejestry/buildTasks | Yes | Yes |
| registries/buildTasks/steps | Nie |  Nie |
| registries/eventGridFilters | Nie |  Nie |
| registries/getBuildSourceUploadUrl | Nie |  Nie |
| rejestry/getpoświadczenia | Nie |  Nie |
| registries/importImage | Nie |  Nie |
| rejestry/queueBuild | Nie |  Nie |
| registries/regenerateCredential | Nie |  Nie |
| rejestry/regenerateCredentials | Nie |  Nie |
| rejestry/replikacje | Tak | Tak |
| rejestry/uruchomienia | Nie |  Nie |
| rejestry/uruchomienia/Anuluj | Nie |  Nie |
| rejestry/scheduleRun | Nie |  Nie |
| rejestry/zadania | Tak | Yes |
| registries/updatePolicies | Nie |  Nie |
| rejestry/elementy webhook | Tak | Tak |
| registries/webhooks/getCallbackConfig | Nie |  Nie |
| rejestry/elementy webhook/polecenie ping | Nie |  Nie |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| containerServices | Tak | Yes |
| managedClusters | Tak | Tak |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| aplikacje | Tak | Tak |
| updateCommunicationPreference | Nie |  Nie |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Yes | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Alerty | Nie |  Nie |
| billingAccounts | Nie |  Nie |
| Łączniki | Yes | Yes |
| działów | Nie |  Nie |
| Wymiary | Nie |  Nie |
| enrollmentAccounts | Nie |  Nie |
| Zapytanie | Nie |  Nie |
| Zarejestrować | Nie |  Nie |
| Reportconfigs | Nie |  Nie |
| Raporty | Nie |  Nie |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| węzłów | Yes | Tak |
| Centra/authorizationPolicies | Nie |  Nie |
| Centra/łączniki | Nie |  Nie |
| Centra/łączniki/mapowania | Nie |  Nie |
| Centra/interakcje | Nie |  Nie |
| hubs/kpi | Nie |  Nie |
| Centra/linki | Nie |  Nie |
| Centra/profile | Nie |  Nie |
| Centra/roleAssignments | Nie |  Nie |
| Centra/role | Nie |  Nie |
| hubs/suggestTypeSchema | Nie |  Nie |
| Centra/widoki | Nie |  Nie |
| hubs/widgetTypes | Nie |  Nie |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| zadania | Tak | Tak |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Tak | Tak |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Obszarów roboczych | Tak | Nie |
| workspaces/virtualNetworkPeerings | Nie |  Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| wykazy | Yes | Tak |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| connectionManagers | Tak | Tak |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Fabryki datafactors | Yes | Nie |
| datafactors/diagnosticSettings | Nie |  Nie |
| dataFactorySchema | Nie |  Nie |
| fabryki | Yes | Nie |
| fabryki/integrationRuntimes | Nie |  Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Tak |
| accounts/dataLakeStoreAccounts | Nie |  Nie |
| konta/storageAccounts | Nie |  Nie |
| konta/storageAccounts/kontenery | Nie |  Nie |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Yes | Tak |
| accounts/eventGridFilters | Nie |  Nie |
| konta/firewallRules | Nie |  Nie |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| services | Tak | Yes |
| usługi/projekty | Tak | Tak |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Serwerem | Tak | Yes |
| serwery/recoverableServers | Nie |  Nie |
| servers/virtualNetworkRules | Nie |  Nie |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Serwerem | Tak | Yes |
| serwery/recoverableServers | Nie |  Nie |
| servers/virtualNetworkRules | Nie |  Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Serwerem | Tak | Yes |
| serwery/doradcy | Nie |  Nie |
| serwery/queryTexts | Nie |  Nie |
| serwery/recoverableServers | Nie |  Nie |
| servers/topQueryStatistics | Nie |  Nie |
| servers/virtualNetworkRules | Nie |  Nie |
| serwery/waitStatistics | Nie |  Nie |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| IotHubs | Tak | Tak |
| IotHubs/eventGridFilters | Nie |  Nie |
| ProvisioningServices | Tak | Yes |
| użycia | Nie |  Nie |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Rejestrowanie | Yes | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Labs | Yes | Tak |
| labs/serviceRunners | Yes | Tak |
| Labs/virtualMachines | Tak | Tak |
| Uruchamianie | Tak | Tak |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Nie |  Nie |
| databaseAccounts | Tak | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| domeny | Tak | Tak |
| domeny/domainOwnershipIdentifiers | Nie |  Nie |
| generateSsoRequest | Nie |  Nie |
| topLevelDomains | Nie |  Nie |
| validateDomainRegistrationInformation | Nie |  Nie |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| lcsprojects | Nie |  Nie |
| lcsprojects/clouddeployments | Nie |  Nie |
| lcsprojects/łączniki | Nie |  Nie |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| domeny | Tak | Nie |
| domeny/tematy | Nie |  Nie |
| eventSubscriptions | Nie |  Nie |
| extensionTopics | Nie |  Nie |
| opisano | Tak | Nie |
| topicTypes | Nie |  Nie |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| oparty | Tak | Nie |
| Przestrzeni | Tak | Nie |
| przestrzenie nazw/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/disasterrecoveryconfigs | Nie |  Nie |
| przestrzenie nazw/eventhubs | Nie |  Nie |
| przestrzenie nazw/eventhubs/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/eventhubs/consumergroups | Nie |  Nie |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| danych | Nie |  Nie |
| dostawcy | Nie |  Nie |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| pisaną | Nie |  Nie |
| galleryitems | Nie |  Nie |
| generateartifactaccessuri | Nie |  Nie |
| Moje obszary | Nie |  Nie |
| Moje obszary/obszary | Nie |  Nie |
| Moje obszary/obszary/obszary | Nie |  Nie |
| Moje obszary/obszary/obszary/galleryitems | Nie |  Nie |
| Moje obszary/obszary/galleryitems | Nie |  Nie |
| Moje obszary/galleryitems | Nie |  Nie |
| Zarejestrować | Nie |  Nie |
| Produkcyjnych | Nie |  Nie |
| retrieveresourcesbyid | Nie |  Nie |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Nie |  Nie |
| Programy | Nie |  Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| hanaInstances | Yes |  Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| oparty | Tak | Tak |
| klastry/aplikacje | Nie |  Nie |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| zadania | Tak | Yes |

## <a name="microsoftinformationprotection"></a>Microsoft. InformationProtection
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| labelGroups | Nie |  Nie |
| labelGroups/etykiety | Nie |  Nie |
| labelGroups/etykiety/warunki | Nie |  Nie |
| labelGroups/etykiety/etykiety | Nie |  Nie |
| labelGroups/labels/subLabels/conditions | Nie |  Nie |

## <a name="microsoftinsights"></a>Microsoft. Insights
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| actiongroups | Tak | Yes |
| activityLogAlerts | Tak | Tak |
| alertrules | Yes | Tak |
| automatedExportSettings | Nie |  Nie |
| autoscalesettings | Tak | Tak |
| punkcie | Nie |  Nie |
| calculatebaseline | Nie |  Nie |
| Komponentów | Tak | Tak |
| składniki/zdarzenia | Nie |  Nie |
| składniki/pricingPlans | Nie |  Nie |
| składniki/zapytanie | Nie |  Nie |
| diagnosticSettings | Nie |  Nie |
| diagnosticSettingsCategories | Nie |  Nie |
| eventCategories | Nie |  Nie |
| Typ zdarzenia | Nie |  Nie |
| extendedDiagnosticSettings | Nie |  Nie |
| logDefinitions | Nie |  Nie |
| logprofiles | Nie |  Nie |
| logs | Nie |  Nie |
| metricAlerts | Tak | Tak |
| migrateToNewPricingModel | Nie |  Nie |
| Moje skoroszyty | Nie |  Nie |
| — zapytania | Nie |  Nie |
| rollbackToLegacyPricingModel | Nie |  Nie |
| scheduledqueryrules | Yes | Tak |
| vmInsightsOnboardingStatuses | Nie |  Nie |
| testów internetowych | Tak | Yes |
| Otwiera | Tak | Yes |

## <a name="microsoftintune"></a>Microsoft. Intune
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nie |  Nie |
| diagnosticSettingsCategories | Nie |  Nie |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| IoTApps | Tak | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Graph | Tak | Tak |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| deletedVaults | Nie |  Nie |
| magazynów | Yes | Tak |
| magazyny/accessPolicies | Nie |  Nie |
| magazyny/wpisy tajne | Nie |  Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| oparty | Tak | Tak |
| klastry/bazy danych | Nie |  Nie |
| klastry/bazy danych/połączenia Databases | Nie |  Nie |
| klastry/bazy danych/eventhubconnections | Nie |  Nie |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| labaccounts | Tak | Yes |
| użytkownicy | Nie |  Nie |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Tak |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Yes | Tak |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| logs | Nie |  Nie |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| integrationAccounts | Tak | Tak |
| przebieg | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| commitmentPlans | Tak | Yes |
| webServices | Tak | Tak |
| Obszary robocze | Yes | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Tak |
| konta/obszary robocze | Tak | Yes |
| konta/obszary robocze/projekty | Tak | Yes |
| teamAccounts | Tak | Yes |
| teamAccounts/obszary robocze | Yes | Tak |
| teamAccounts/obszary robocze/projekty | Tak | Tak |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Obszarów roboczych | Tak | Tak |
| obszary robocze/obliczenia | Nie |  Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Osob | Nie |  Nie |
| Resourceidentity | Tak | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| getentities | Nie |  Nie |
| managementGroups | Nie |  Nie |
| Produkcyjnych | Nie |  Nie |
| startTenantBackfill | Nie |  Nie |
| tenantBackfillStatus | Nie |  Nie |

## <a name="microsoftmaps"></a>Microsoft. Maps
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Tak |
| accounts/eventGridFilters | Nie |  Nie |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Otrzymane | Nie |  Nie |
| offerTypes | Nie |  Nie |
| offerTypes/wydawcy | Nie |  Nie |
| offerTypes/wydawcy/oferty | Nie |  Nie |
| offerTypes/wydawcy/oferty/plany | Nie |  Nie |
| offerTypes/wydawcy/oferty/plany/umowy | Nie |  Nie |
| offerTypes/wydawcy/oferty/plany/konfiguracje | Nie |  Nie |
| offerTypes/publishers/offers/plans/configs/importImage | Nie |  Nie |
| privategalleryitems | Nie |  Nie |
| wyrobów | Nie |  Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| classicDevServices | Tak | Tak |
| updateCommunicationPreference | Nie |  Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| kontrakt | Nie |  Nie |
| offertypes | Nie |  Nie |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| MediaServices | Tak | Tak |
| MediaServices/accountFilters | Nie |  Nie |
| MediaServices/zasoby | Nie |  Nie |
| MediaServices/assetFilters | Nie |  Nie |
| MediaServices/contentKeyPolicies | Nie |  Nie |
| MediaServices/eventGridFilters | Nie |  Nie |
| mediaservices/liveEventOperations | Nie |  Nie |
| MediaServices/liveEvents | Yes | Yes |
| MediaServices/liveEvents/liveOutputs | Nie |  Nie |
| MediaServices/liveOutputOperations | Nie |  Nie |
| MediaServices/streamingEndpointOperations | Nie |  Nie |
| MediaServices/streamingEndpoints | Tak | Yes |
| MediaServices/streamingLocators | Nie |  Nie |
| MediaServices/streamingPolicies | Nie |  Nie |
| MediaServices/transformacje | Nie |  Nie |
| MediaServices/transformacje/zadania | Nie |  Nie |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| projektami | Tak | Yes |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| applicationGateways | Tak | Nie |
| applicationSecurityGroups | Tak | Tak |
| azureFirewallFqdnTags | Nie |  Nie |
| azureFirewalls | Tak | Nie |
| bgpServiceCommunities | Nie |  Nie |
| Licznik | Tak | Tak |
| ddosCustomPolicies | Yes | Tak |
| ddosProtectionPlans | Tak | Yes |
| dnsOperationStatuses | Nie |  Nie |
| dnszones | Yes | Tak |
| dnszones/A | Nie |  Nie |
| dnszones/AAAA | Nie |  Nie |
| dnszones/wszystkie | Nie |  Nie |
| dnszones/CAA | Nie |  Nie |
| dnszones/CNAME | Nie |  Nie |
| dnszones/MX | Nie |  Nie |
| dnszones/NS | Nie |  Nie |
| dnszones/PTR | Nie |  Nie |
| dnszones/zestawy rekordów | Nie |  Nie |
| dnszones/SOA | Nie |  Nie |
| dnszones/SRV | Nie |  Nie |
| dnszones/TXT | Nie |  Nie |
| expressRouteCircuits | Tak  | Nie |
| expressRouteServiceProviders | Nie |  Nie |
| usługi frontdoor | Tak, ale ograniczone (patrz [Uwaga poniżej](#frontdoor)) | Tak |
| frontdoorWebApplicationFirewallPolicies | Tak, ale ograniczone (patrz [Uwaga poniżej](#frontdoor)) | Yes |
| getDnsResourceReference | Nie |  Nie |
| interfaceEndpoints | Yes | Yes |
| internalNotify | Nie |  Nie |
| loadBalancers | Tak | Nie |
| localNetworkGateways | Tak | Tak |
| natGateways | Tak | Tak |
| networkIntentPolicies | Yes | Tak |
| networkInterfaces | Tak | Tak |
| networkProfiles | Tak | Tak |
| networkSecurityGroups | Tak | Yes |
| networkWatchers | Tak | Nie |
| networkWatchers/connectionMonitors | Tak | Nie |
| networkWatchers/soczewki | Tak | Nie |
| networkWatchers/pingMeshes | Yes | Nie |
| privateLinkServices | Yes | Tak |
| publicIPAddresses | Tak | Tak |
| publicIPPrefixes | Tak | Yes |
| routeFilters | Tak | Yes |
| routeTables | Tak | Tak |
| serviceEndpointPolicies | Tak | Tak |
| trafficManagerGeographicHierarchies | Nie |  Nie |
| trafficmanagerprofiles | Yes | Tak |
| trafficmanagerprofiles/map cieplnych | Nie |  Nie |
| virtualHubs | Yes | Tak |
| virtualNetworkGateways | Yes | Yes |
| virtualNetworks | Yes | Tak |
| virtualNetworks/subnets | Nie |  Nie |
| virtualNetworkTaps | Tak | Tak |
| virtualWans | Yes | Yes |
| vpnGateways | Tak | Nie |
| vpnSites | Yes | Yes |
| webApplicationFirewallPolicies | Yes | Tak |

<a id="frontdoor" />

W przypadku usługi Azure front-drzwi można zastosować Tagi podczas tworzenia zasobu, ale aktualizowanie lub Dodawanie tagów nie jest obecnie obsługiwane.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Przestrzeni | Tak | Nie |
| przestrzenie nazw/notificationHubs | Tak | Nie |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| urządzenia | Nie |  Nie |
| linkTargets | Nie |  Nie |
| storageInsightConfigs | Nie |  Nie |
| Obszarów roboczych | Tak | Yes |
| obszary robocze/źródła danych | Nie |  Nie |
| workspaces/linkedServices | Nie |  Nie |
| obszary robocze/zapytanie | Nie |  Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| managementassociations | Nie |  Nie |
| managementconfigurations | Yes | Tak |
| rozwiązania | Yes | Tak |
| Widoki | Tak | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| policyEvents | Nie |  Nie |
| policyStates | Nie |  Nie |
| policyTrackedResources | Nie |  Nie |
| korygowania | Nie |  Nie |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| konsol | Nie |  Nie |
| Pulpitów nawigacyjnych | Tak | Tak |
| userSettings | Nie |  Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| workspaceCollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| produkcyjnych | Tak | Tak |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| accounts | Tak | Tak |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Nie |  Nie |
| magazynów | Tak | Tak |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Przestrzeni | Yes | Tak |
| przestrzenie nazw/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/hybridconnections | Nie |  Nie |
| przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/wcfrelays | Nie |  Nie |
| przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie |  Nie |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Produkcyjnych | Nie |  Nie |
| subscriptionsStatus | Nie |  Nie |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Nie |  Nie |
| childAvailabilityStatuses | Nie |  Nie |
| childResources | Nie |  Nie |
| zdarzenia | Nie |  Nie |
| impactedResources | Nie |  Nie |
| Złożone | Nie |  Nie |

## <a name="microsoftresources"></a>Microsoft.Resources
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| komputerów | Nie |  Nie |
| wdrożenia/operacje | Nie |  Nie |
| linki | Nie |  Nie |
| notifyResourceJobs | Nie |  Nie |
| dostawcy | Nie |  Nie |
| resourceGroups | Nie |  Nie |
| Produkcyjnych | Nie |  Nie |
| opłaty | Nie |  Nie |
| subskrypcje/dostawcy | Nie |  Nie |
| subskrypcje/resourceGroups | Nie |  Nie |
| subskrypcje/ResourceGroups/zasoby | Nie |  Nie |
| subskrypcje/zasoby | Nie |  Nie |
| subskrypcje/TagName | Nie |  Nie |
| subscriptions/tagNames/tagValues | Nie |  Nie |
| dzierżaw | Nie |  Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| aplikacje | Tak | Tak |
| saasresources | Nie |  Nie |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| przekazywania | Yes | Yes |
| jobcollections | Yes | Tak |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Nie |  Nie |
| searchServices | Yes | Tak |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Nie |  Nie |
| alerts | Nie |  Nie |
| allowedConnections | Nie |  Nie |
| gotowania | Nie |  Nie |
| applicationWhitelistings | Nie |  Nie |
| AutoProvisioningSettings | Nie |  Nie |
| Zachowania zgodności prawnymi | Nie |  Nie |
| dataCollectionAgents | Nie |  Nie |
| discoveredSecuritySolutions | Nie |  Nie |
| externalSecuritySolutions | Nie |  Nie |
| InformationProtectionPolicies | Nie |  Nie |
| jitNetworkAccessPolicies | Nie |  Nie |
| Kontrolą | Nie |  Nie |
| monitorowanie/złośliwe oprogramowanie | Nie |  Nie |
| monitorowanie/linia bazowa | Nie |  Nie |
| monitorowanie/poprawka | Nie |  Nie |
| Jazd | Nie |  Nie |
| cen | Nie |  Nie |
| securityContacts | Nie |  Nie |
| securitySolutions | Nie |  Nie |
| securitySolutionsReferenceData | Nie |  Nie |
| securityStatus | Nie |  Nie |
| securityStatus/punkty końcowe | Nie |  Nie |
| securityStatus/podsieci | Nie |  Nie |
| securityStatus/virtualMachines | Nie |  Nie |
| securityStatuses | Nie |  Nie |
| securityStatusesSummaries | Nie |  Nie |
| settings | Nie |  Nie |
| zadania | Nie |  Nie |
| replikacji | Nie |  Nie |
| workspaceSettings | Nie |  Nie |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nie |  Nie |
| diagnosticSettingsCategories | Nie |  Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Przestrzeni | Tak | Nie |
| przestrzenie nazw/reguł autoryzacji | Nie |  Nie |
| przestrzenie nazw/disasterrecoveryconfigs | Nie |  Nie |
| przestrzenie nazw/eventgridfilters | Nie |  Nie |
| przestrzenie nazw/kolejki | Nie |  Nie |
| przestrzenie nazw/kolejki/reguł autoryzacji | Nie |  Nie |
| obszary nazw/tematy | Nie |  Nie |
| przestrzenie nazw/tematy/reguł autoryzacji | Nie |  Nie |
| obszary nazw/tematy/subskrypcje | Nie |  Nie |
| obszary nazw/tematy/subskrypcje/reguły | Nie |  Nie |
| premiumMessagingRegions | Nie |  Nie |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| oparty | Tak | Tak |
| klastry/aplikacje | Nie |  Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| aplikacje | Tak | Tak |
| bram | Tak | Yes |
| sieci | Yes | Yes |
| wpisy tajne | Yes | Tak |
| volumes | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| SignalR | Tak | Yes |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Yes | Yes |
| gotowania | Tak | Yes |
| applicationDefinitions | Yes | Tak |
| aplikacje | Tak | Yes |
| jitRequests | Yes | Yes |

## <a name="microsoftsql"></a>Microsoft.SQL
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| managedInstances | Tak | Tak |
| managedInstances/bazy danych | Tak (patrz Uwaga poniżej) | Yes |
| managedInstances/databases/backupShortTermRetentionPolicies | Nie | Nie |
| managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels | Nie | Nie |
| managedInstances/Databases/vulnerabilityAssessments | Nie | Nie |
| managedInstances/bazy danych/vulnerabilityAssessments/reguły/linie bazowe | Nie | Nie |
| managedInstances/encryptionProtector | Nie | Nie |
| managedInstances/klucze | Nie | Nie |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nie | Nie |
| managedInstances/vulnerabilityAssessments | Nie | Nie |
| Serwerem | Yes | Yes |
| serwery/Administratorzy | Nie |  Nie |
| serwery/communicationLinks | Nie |  Nie |
| Serwery/bazy danych | Tak (patrz Uwaga poniżej) | Tak |
| serwery/encryptionProtector | Nie |  Nie |
| serwery/firewallRules | Nie |  Nie |
| serwery/klucze | Nie |  Nie |
| servers/restorableDroppedDatabases | Nie |  Nie |
| serwery/cele | Nie |  Nie |
| serwery/tdeCertificates | Nie |  Nie |

> [!NOTE]
> Baza danych Master nie obsługuje tagów, ale inne bazy danych, w tym bazy danych Azure SQL Data Warehouse, obsługują Tagi. Azure SQL Data Warehouse bazy danych muszą znajdować się w stanie aktywnym (niewstrzymanym).


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Yes | Tak |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nie |  Nie |
| SqlVirtualMachines | Tak | Tak |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| storageAccounts | Yes | Tak |
| storageAccounts/blobServices | Nie |  Nie |
| storageAccounts/fileServices | Nie |  Nie |
| storageAccounts/queueServices | Nie |  Nie |
| storageAccounts/usługi | Nie |  Nie |
| storageAccounts/tableServices | Nie |  Nie |
| użycia | Nie |  Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| storageSyncServices | Tak | Yes |
| storageSyncServices/registeredServers | Nie |  Nie |
| storageSyncServices/syncGroups | Nie |  Nie |
| storageSyncServices/syncGroups/cloudEndpoints | Nie |  Nie |
| storageSyncServices/syncGroups/serverEndpoints | Nie |  Nie |
| storageSyncServices/przepływy pracy | Nie |  Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| skonfigurowany | Tak | Tak |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| streamingjobs | Tak (patrz Uwaga poniżej) | Yes |
| streamingjobs/diagnosticSettings | Nie |  Nie |

> [!NOTE]
> Nie można dodać znacznika, gdy streamingjobs jest uruchomiony. Zatrzymaj zasób, aby dodać tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Anulowanie subskrypcji | Nie |  Nie |
| SubscriptionDefinitions | Nie |  Nie |
| SubscriptionOperations | Nie |  Nie |

## <a name="microsoftsupport"></a>Microsoft. Support
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| supporttickets | Nie |  Nie |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| providerRegistrations | Yes | Tak |
| Produkcyjnych | Yes | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| wiejski | Tak | Nie |
| środowiska/accessPolicies | Nie |  Nie |
| środowiska/źródła zdarzeń | Tak | Nie |
| environments/referenceDataSets | Tak | Nie |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| account | Yes | Yes |
| konto/rozszerzenie | Yes | Yes |
| konto/projekt | Tak | Tak |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
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
| Przystawki | Tak | Tak |
| connectionGateways | Yes | Yes |
| Licznik | Yes | Tak |
| customApis | Tak | Tak |
| deletedSites | Nie |  Nie |
| — funkcje | Nie |  Nie |
| hostingEnvironments | Tak | Yes |
| hostingEnvironments/multiRolePools | Nie |  Nie |
| hostingEnvironments/multiRolePools/Instances | Nie |  Nie |
| hostingEnvironments/workerPools | Nie |  Nie |
| hostingEnvironments/workerPools/Instances | Nie |  Nie |
| publishingUsers | Nie |  Nie |
| Zalecenia | Nie |  Nie |
| resourceHealthMetadata | Nie |  Nie |
| Runtime | Nie |  Nie |
| Dopuszczalna | Tak | Yes |
| Dopuszczalna/procesy robocze | Nie |  Nie |
| teren | Tak | Tak |
| Lokacje/domainOwnershipIdentifiers | Nie |  Nie |
| sites/hostNameBindings | Nie |  Nie |
| Lokacje/wystąpienia | Nie |  Nie |
| Lokacje/wystąpienia/rozszerzenia | Nie |  Nie |
| sites/premieraddons | Tak | Tak |
| Lokacje/zalecenia | Nie |  Nie |
| sites/resourceHealthMetadata | Nie |  Nie |
| Lokacje/miejsca | Yes | Yes |
| sites/slots/hostNameBindings | Nie |  Nie |
| Lokacje/miejsca/wystąpienia | Nie |  Nie |
| Lokacje/miejsca/wystąpienia/rozszerzenia | Nie |  Nie |
| sourceControls | Nie |  Nie |
| legalizacj | Nie |  Nie |
| verifyHostingEnvironmentVnet | Nie |  Nie |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nie |  Nie |
| diagnosticSettingsCategories | Nie |  Nie |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| DeviceServices | Tak | Tak |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
| ------------- | ----------- | ----------- |
| Komponentów | Nie |  Nie |
| componentsSummary | Nie |  Nie |
| monitorInstances | Nie |  Nie |
| monitorInstancesSummary | Nie |  Nie |
| monitora | Nie |  Nie |
| notificationSettings | Nie |  Nie |

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak zastosować Tagi do zasobów, zobacz [Używanie tagów do organizowania zasobów platformy Azure](resource-group-using-tags.md).
