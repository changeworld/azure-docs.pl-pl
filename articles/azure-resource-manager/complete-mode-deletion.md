---
title: Usuwanie w trybie pełnym
description: Pokazuje, jak typy zasobów obsługują usuwanie w trybie pełnym w szablonach Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/27/2019
ms.openlocfilehash: e8e473ba697dd64d2562a2e1efc03f1ba5b384e6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149927"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Usuwanie zasobów platformy Azure na potrzeby wdrożeń w trybie pełnym

W tym artykule opisano sposób, w jaki typy zasobów obsługują usuwanie, gdy nie znajduje się w szablonie wdrożonym w trybie kompletnym.

Typy zasobów oznaczone **wartością tak** są usuwane, gdy typ nie jest w szablonie wdrożonym z trybem kompletnym.

Typy zasobów oznaczone atrybutem **no** nie są automatycznie usuwane, gdy nie znajdują się w szablonie; Jednak są usuwane, jeśli zasób nadrzędny zostanie usunięty. Pełny opis zachowania można znaleźć w temacie [Azure Resource Manager Modes Deployments](deployment-modes.md).

W przypadku wdrożenia w [więcej niż jednej grupie zasobów w szablonie](resource-manager-cross-resource-group-deployment.md)zasoby w grupie zasobów określonej w operacji wdrażania mogą zostać usunięte. Zasoby w dodatkowych grupach zasobów nie są usuwane.

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. Dodatki](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. zaświadczanie](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Genewa](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. rozliczenia](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft. plan](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. Pojemność](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. zużycie](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. datacegły](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Migracja Microsoft.](#microsoftdatamigration)
> - [Microsoft. dataudział](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrowanie](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. Komunikacja równorzędna](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. Monitor obciążenia został](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DomainServices | Yes |
> | DomainServices/oucontainer | Nie |
> | DomainServices/ReplicaSets | Yes |

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
> | dzienniki | Nie |
> | raporty | Nie |
> | servicehealthmetrics | Nie |
> | services | Nie |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | komputerów | Nie |
> | generateRecommendations | Nie |
> | metadane | Nie |
> | Zalecenia | Nie |
> | pominięć | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | actionRules | Yes |
> | alerts | Nie |
> | alertsList | Nie |
> | alertsMetaData | Nie |
> | alertsSummary | Nie |
> | alertsSummaryList | Nie |
> | Opinii | Nie |
> | smartDetectorAlertRules | Yes |
> | smartDetectorRuntimeEnvironments | Nie |
> | smartGroups | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | serwery | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | reportFeedback | Nie |
> | usługa | Yes |
> | validateServiceName | Nie |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | configurationStores | Yes |
> | configurationStores/eventGridFilters | Nie |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Spring | Yes |

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
> | aliasy | Nie |
> | denyAssignments | Nie |
> | elevateAccess | Nie |
> | findOrphanRoleAssignments | Nie |
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
> | automationAccounts/konfiguracje | Yes |
> | automationAccounts/zadania | Nie |
> | automationAccounts/elementy Runbook | Yes |
> | automationAccounts/softwareUpdateConfigurations | Nie |
> | automationAccounts/elementy webhook | Nie |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | configurationStores | Yes |
> | configurationStores/eventGridFilters | Nie |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Wiejski | Nie |
> | środowiska/konta | Nie |
> | środowiska/konta/przestrzenie nazw | Nie |
> | środowiska/konta/przestrzenie nazw/konfiguracje | Nie |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | b2cDirectories | Yes |
> | b2ctenants | Nie |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hybridDataManagers | Yes |
> | postgresInstances | Yes |
> | sqlBigDataClusters | Yes |
> | Wystąpienia sqlinstances | Yes |
> | sqlServerRegistrations | Yes |
> | sqlServerRegistrations/SQLServers | Nie |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | rejestracje | Yes |
> | rejestracje/customerSubscriptions | Nie |
> | rejestracje/produkty | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | batchAccounts | Yes |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | billingAccounts | Nie |
> | billingAccounts/umowy | Nie |
> | billingAccounts/billingPermissions | Nie |
> | billingAccounts/billingProfiles | Nie |
> | billingAccounts/billingProfiles/billingPermissions | Nie |
> | billingAccounts/billingProfiles/billingRoleAssignments | Nie |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Nie |
> | billingAccounts/billingProfiles/billingSubscriptions | Nie |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Nie |
> | billingAccounts/billingProfiles/klienci | Nie |
> | billingAccounts/billingProfiles/faktury | Nie |
> | billingAccounts/billingProfiles/faktury/arkusza cen | Nie |
> | billingAccounts/billingProfiles/invoiceSections | Nie |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Nie |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Nie |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Nie |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Nie |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Nie |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Nie |
> | billingAccounts/billingProfiles/invoiceSections/produkty | Nie |
> | billingAccounts/billingProfiles/invoiceSections/produkty/transfer | Nie |
> | billingAccounts/billingProfiles/invoiceSections/produkty/updateAutoRenew | Nie |
> | billingAccounts/billingProfiles/invoiceSections/Transactions | Nie |
> | billingAccounts/billingProfiles/invoiceSections/transfery | Nie |
> | billingAccounts/BillingProfiles/patchOperations | Nie |
> | billingAccounts/billingProfiles/paymentMethods | Nie |
> | billingAccounts/billingProfiles/zasady | Nie |
> | billingAccounts/billingProfiles/pricesheet | Nie |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Nie |
> | billingAccounts/billingProfiles/produkty | Nie |
> | billingAccounts/billingProfiles/Transactions | Nie |
> | billingAccounts/billingRoleAssignments | Nie |
> | billingAccounts/billingRoleDefinitions | Nie |
> | billingAccounts/billingSubscriptions | Nie |
> | billingAccounts/createBillingRoleAssignment | Nie |
> | billingAccounts/createInvoiceSectionOperations | Nie |
> | billingAccounts/klienci | Nie |
> | billingAccounts/klienci/billingPermissions | Nie |
> | billingAccounts/klienci/billingSubscriptions | Nie |
> | billingAccounts/klienci/initiateTransfer | Nie |
> | billingAccounts/klienci/zasady | Nie |
> | billingAccounts/klienci/produkty | Nie |
> | billingAccounts/klienci/transakcje | Nie |
> | billingAccounts/klienci/transfery | Nie |
> | billingAccounts/działy | Nie |
> | billingAccounts/enrollmentAccounts | Nie |
> | billingAccounts/faktury | Nie |
> | billingAccounts/invoiceSections | Nie |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Nie |
> | billingAccounts/invoiceSections/billingSubscriptions | Nie |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nie |
> | billingAccounts/invoiceSections/Podnieś poziom | Nie |
> | billingAccounts/invoiceSections/initiateTransfer | Nie |
> | billingAccounts/invoiceSections/patchOperations | Nie |
> | billingAccounts/invoiceSections/productMoveOperations | Nie |
> | billingAccounts/invoiceSections/products | Nie |
> | billingAccounts/invoiceSections/produkty/transfer | Nie |
> | billingAccounts/invoiceSections/produkty/updateAutoRenew | Nie |
> | billingAccounts/invoiceSections/Transactions | Nie |
> | billingAccounts/invoiceSections/transfery | Nie |
> | billingAccounts/lineOfCredit | Nie |
> | billingAccounts/patchOperations | Nie |
> | billingAccounts/paymentMethods | Nie |
> | billingAccounts/produkty | Nie |
> | billingAccounts/transakcje | Nie |
> | billingPeriods | Nie |
> | billingPermissions | Nie |
> | billingProperty | Nie |
> | billingRoleAssignments | Nie |
> | billingRoleDefinitions | Nie |
> | createBillingRoleAssignment | Nie |
> | działów | Nie |
> | enrollmentAccounts | Nie |
> | faktur | Nie |
> | sunięcia | Nie |
> | transfery/acceptTransfer | Nie |
> | transfery/declineTransfer | Nie |
> | transfery/operationStatus | Nie |
> | transfery/validateTransfer | Nie |
> | validateAddress | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | mapApis | Yes |
> | updateCommunicationPreference | Nie |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | cordaMembers | Yes |
> | obserwatorzy | Yes |

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
> | botServices | Yes |
> | botServices/kanały | Nie |
> | botServices/połączenia | Nie |
> | Języki | Nie |
> | szablonów | Nie |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Redis | Yes |
> | RedisConfigDefinition | Nie |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appliedReservations | Nie |
> | calculateExchange | Nie |
> | calculatePrice | Nie |
> | calculatePurchasePrice | Nie |
> | wykazy | Nie |
> | commercialReservationOrders | Nie |
> | zamian | Nie |
> | placePurchaseOrder | Nie |
> | reservationOrders | Nie |
> | reservationOrders/calculateRefund | Nie |
> | reservationOrders/Scal | Nie |
> | reservationOrders/rezerwacje | Nie |
> | reservationOrders/rezerwacje/poprawki | Nie |
> | reservationOrders/Return | Nie |
> | reservationOrders/Split | Nie |
> | reservationOrders/swap | Nie |
> | dokonując | Nie |
> | produkcyjnych | Nie |
> | validateReservationOrder | Nie |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nie |
> | CdnWebApplicationFirewallPolicies | Yes |
> | edgenodes | Nie |
> | profiles | Yes |
> | Profile/punkty końcowe | Yes |
> | Profile/punkty końcowe/customdomains | Nie |
> | Profile/punkty końcowe/źródła | Nie |
> | validateProbe | Nie |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | certificateOrders | Yes |
> | certificateOrders/certyfikaty | Nie |
> | validateCertificateRegistrationInformation | Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwość | Nie |
> | domainNames | Yes |
> | domainNames/możliwości | Nie |
> | domainNames/internalLoadBalancers | Nie |
> | domainNames/servicecertificates | Nie |
> | domainNames/miejsca | Nie |
> | domainNames/gniazda/role | Nie |
> | domainNames/szczeliny/role/metricDefinitions | Nie |
> | domainNames/szczeliny/role/metryki | Nie |
> | moveSubscriptionResources | Nie |
> | operatingSystemFamilies | Nie |
> | operatingSystems | Nie |
> | quotas | Nie |
> | resourceTypes | Nie |
> | validateSubscriptionMoveAvailability | Nie |
> | virtualMachines | Yes |
> | virtualMachines/diagnosticSettings | Nie |
> | virtualMachines/metricDefinitions | Nie |
> | virtualMachines/metryki | Nie |

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
> | networkSecurityGroups | Yes |
> | quotas | Nie |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nie |
> | virtualNetworks/virtualNetworkPeerings | Nie |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwość | Nie |
> | dysku | Nie |
> | images | Nie |
> | osImages | Nie |
> | osPlatformImages | Nie |
> | publicImages | Nie |
> | quotas | Nie |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | Nie |
> | storageAccounts/fileServices | Nie |
> | storageAccounts/metricDefinitions | Nie |
> | storageAccounts/metryki | Nie |
> | storageAccounts/queueServices | Nie |
> | storageAccounts/usługi | Nie |
> | storageAccounts/usługi/diagnosticSettings | Nie |
> | storageAccounts/usługi/metricDefinitions | Nie |
> | storageAccounts/usługi/metryki | Nie |
> | storageAccounts/tableServices | Nie |
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
> | diskEncryptionSets | Yes |
> | dysku | Yes |
> | znajduj | Yes |
> | Galerie/aplikacje | Nie |
> | Galerie/aplikacje/wersje | Nie |
> | Galerie/obrazy | Nie |
> | Galerie/obrazy/wersje | Nie |
> | hostGroups | Yes |
> | hostGroups/hosty | Yes |
> | images | Yes |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections/restorePoints | Nie |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions/wersje | Nie |
> | sharedVMImages | Yes |
> | sharedVMImages/wersje | Nie |
> | Migawki | Yes |
> | virtualMachines | Yes |
> | virtualMachines/rozszerzenia | Yes |
> | virtualMachines/metricDefinitions | Nie |
> | virtualMachineScaleSets | Yes |
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
> | Opłaty | Nie |
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
> | Tagi | Nie |
> | Dzierżaw | Nie |
> | Warunki | Nie |
> | UsageDetails | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | containerGroups | Yes |
> | serviceAssociationLinks | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wołuje | Yes |
> | rejestry/kompilacje | Nie |
> | rejestry/kompilacje/anulowanie | Nie |
> | rejestry/kompilacje/getLogLink | Nie |
> | rejestry/buildTasks | Yes |
> | registries/buildTasks/steps | Nie |
> | registries/eventGridFilters | Nie |
> | rejestry/generateCredentials | Nie |
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
> | rejestry/scopeMaps | Nie |
> | rejestry/zadania | Yes |
> | rejestry/tokeny | Nie |
> | registries/updatePolicies | Nie |
> | rejestry/elementy webhook | Yes |
> | registries/webhooks/getCallbackConfig | Nie |
> | rejestry/elementy webhook/polecenie ping | Nie |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | containerServices | Yes |
> | managedClusters | Yes |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Alerty | Nie |
> | billingAccounts | Nie |
> | Budżety | Nie |
> | CloudConnectors | Nie |
> | Łączniki | Yes |
> | działów | Nie |
> | Wymiary | Nie |
> | enrollmentAccounts | Nie |
> | Eksporty | Nie |
> | ExternalBillingAccounts | Nie |
> | ExternalBillingAccounts/alerty | Nie |
> | ExternalBillingAccounts/wymiary | Nie |
> | ExternalBillingAccounts/Prognoza | Nie |
> | ExternalBillingAccounts/zapytanie | Nie |
> | ExternalSubscriptions | Nie |
> | ExternalSubscriptions/alerty | Nie |
> | ExternalSubscriptions/wymiary | Nie |
> | ExternalSubscriptions/Prognoza | Nie |
> | ExternalSubscriptions/zapytanie | Nie |
> | Prognozy | Nie |
> | Zapytanie | Nie |
> | zarejestrować | Nie |
> | Reportconfigs | Nie |
> | Raporty | Nie |
> | Ustawienia | Nie |
> | showbackRules | Nie |
> | Widoki | Nie |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Żądania | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | związku | Nie |
> | resourceProviders | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zadania | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | obszarów roboczych | Yes |
> | workspaces/virtualNetworkPeerings | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wykazy | Yes |
> | wykazy | Yes |
> | wykazy danych/źródła danych | Nie |
> | wykazy danych/źródła danych/skany | Nie |
> | wykazy danych/źródła danych/skany/zestawy danych | Nie |
> | wykazy danych/źródła danych/skanowania/wyzwalacze | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Fabryki datafactors | Yes |
> | datafactors/diagnosticSettings | Nie |
> | datafactors/metricDefinitions | Nie |
> | dataFactorySchema | Nie |
> | fabryki | Yes |
> | fabryki/integrationRuntimes | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts/dataLakeStoreAccounts | Nie |
> | konta/storageAccounts | Nie |
> | konta/storageAccounts/kontenery | Nie |
> | konta/transferAnalyticsUnits | Nie |

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
> | services | Yes |
> | usługi/projekty | Yes |

## <a name="microsoftdatashare"></a>Microsoft. dataudział

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Yes |
> | konta/udziały | Nie |
> | konta/udziały/zestawy danych | Nie |
> | konta/udziały/zaproszenia | Nie |
> | konta/udziały/providersharesubscriptions | Nie |
> | konta/udziały/synchronizationSettings | Nie |
> | konta/sharesubscriptions | Nie |
> | konta/sharesubscriptions/consumerSourceDataSets | Nie |
> | konta/sharesubscriptions/datasetmappings | Nie |
> | konta/sharesubscriptions/wyzwalacze | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | serwery | Yes |
> | serwery/doradcy | Nie |
> | serwery/privateEndpointConnectionProxies | Nie |
> | serwery/privateEndpointConnections | Nie |
> | serwery/privateLinkResources | Nie |
> | serwery/queryTexts | Nie |
> | serwery/recoverableServers | Nie |
> | servers/topQueryStatistics | Nie |
> | servers/virtualNetworkRules | Nie |
> | serwery/waitStatistics | Nie |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | serwery | Yes |
> | serwery/doradcy | Nie |
> | serwery/privateEndpointConnectionProxies | Nie |
> | serwery/privateEndpointConnections | Nie |
> | serwery/privateLinkResources | Nie |
> | serwery/queryTexts | Nie |
> | serwery/recoverableServers | Nie |
> | servers/topQueryStatistics | Nie |
> | servers/virtualNetworkRules | Nie |
> | serwery/waitStatistics | Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | serverGroups | Yes |
> | serwery | Yes |
> | serwery/doradcy | Nie |
> | serwery/klucze | Nie |
> | serwery/privateEndpointConnectionProxies | Nie |
> | serwery/privateEndpointConnections | Nie |
> | serwery/privateLinkResources | Nie |
> | serwery/queryTexts | Nie |
> | serwery/recoverableServers | Nie |
> | servers/topQueryStatistics | Nie |
> | servers/virtualNetworkRules | Nie |
> | serwery/waitStatistics | Nie |
> | serversv2 | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | artifactSources | Yes |
> | wprowadzanie | Yes |
> | servicetopologie | Yes |
> | servicetopologie/usługi | Yes |
> | servicetopologie/usługi/serviceunits | Yes |
> | kroki | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationgroups | Yes |
> | applicationgroups/aplikacje | Nie |
> | applicationgroups/komputery stacjonarne | Nie |
> | applicationgroups/startmenuitems | Nie |
> | hostpools | Yes |
> | hostpools/sessionhosts | Nie |
> | hostpools/sessionhosts/usersessions | Nie |
> | hostpools/usersessions | Nie |
> | obszarów roboczych | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | ElasticPools | Yes |
> | ElasticPools/IotHubTenants | Yes |
> | IotHubs | Yes |
> | IotHubs/eventGridFilters | Nie |
> | ProvisioningServices | Yes |
> | użycia | Nie |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | potoki | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Rejestrowanie | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | labcenters | Yes |
> | Labs | Yes |
> | laboratoria/środowiska | Yes |
> | labs/serviceRunners | Yes |
> | Labs/virtualMachines | Yes |
> | Uruchamianie | Yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | databaseAccountNames | Nie |
> | databaseAccounts | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | domeny | Yes |
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

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | domeny | Yes |
> | domeny/tematy | Nie |
> | eventSubscriptions | Nie |
> | extensionTopics | Nie |
> | opisano | Yes |
> | topicTypes | Nie |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Yes |
> | przestrzeni | Yes |
> | przestrzenie nazw/reguł autoryzacji | Nie |
> | przestrzenie nazw/disasterrecoveryconfigs | Nie |
> | przestrzenie nazw/eventhubs | Nie |
> | przestrzenie nazw/eventhubs/reguł autoryzacji | Nie |
> | przestrzenie nazw/eventhubs/consumergroups | Nie |
> | przestrzenie nazw/networkrulesets | Nie |

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
> | zarejestrować | Nie |
> | produkcyjnych | Nie |
> | retrieveresourcesbyid | Nie |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | autoManagedVmConfigurationProfiles | Yes |
> | configurationProfileAssignments | Nie |
> | guestConfigurationAssignments | Nie |
> | programy | Nie |
> | softwareUpdateProfile | Nie |
> | softwareUpdates | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitors | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Modułów dedicatedhsms | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Yes |
> | klastry/aplikacje | Nie |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Win64 | Yes |
> | Maszyny/rozszerzenia | Yes |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | datamanagers | Yes |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | komponentów | Yes |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zadania | Yes |

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
> | appTemplates | Nie |
> | IoTApps | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Graph | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | deletedVaults | Nie |
> | hsmPools | Yes |
> | magazynów | Yes |
> | magazyny/accessPolicies | Nie |
> | magazyny/eventGridFilters | Nie |
> | magazyny/wpisy tajne | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Yes |
> | klastry/attacheddatabaseconfigurations | Nie |
> | klastry/bazy danych | Nie |
> | klastry/bazy danych/połączenia Databases | Nie |
> | klastry/bazy danych/eventhubconnections | Nie |
> | klastry/sharedidentities | Nie |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | użytkownicy | Nie |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | integrationAccounts | Yes |
> | integrationServiceEnvironments | Yes |
> | integrationServiceEnvironments/managedApis | Yes |
> | isolatedEnvironments | Yes |
> | przebieg | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | commitmentPlans | Yes |
> | webServices | Yes |
> | Obszary robocze | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | obszarów roboczych | Yes |
> | obszary robocze/obliczenia | Nie |
> | obszary robocze/eventGridFilters | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Osob | Nie |
> | Resourceidentity | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Nie |
> | registrationAssignments | Nie |
> | registrationDefinitions | Nie |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | getentities | Nie |
> | managementGroups | Nie |
> | produkcyjnych | Nie |
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
> | otrzymane | Nie |
> | offerTypes | Nie |
> | offerTypes/wydawcy | Nie |
> | offerTypes/wydawcy/oferty | Nie |
> | offerTypes/wydawcy/oferty/plany | Nie |
> | offerTypes/wydawcy/oferty/plany/umowy | Nie |
> | offerTypes/wydawcy/oferty/plany/konfiguracje | Nie |
> | offerTypes/publishers/offers/plans/configs/importImage | Nie |
> | privategalleryitems | Nie |
> | wyrobów | Nie |
> | wydawcy | Nie |
> | wydawcy/oferty | Nie |
> | wydawcy/oferty/zmiany | Nie |

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
> | MediaServices/liveEvents | Yes |
> | MediaServices/liveEvents/liveOutputs | Nie |
> | MediaServices/liveOutputOperations | Nie |
> | MediaServices/mediaGraphs | Nie |
> | MediaServices/streamingEndpointOperations | Nie |
> | MediaServices/streamingEndpoints | Yes |
> | MediaServices/streamingLocators | Nie |
> | MediaServices/streamingPolicies | Nie |
> | MediaServices/transformacje | Nie |
> | MediaServices/transformacje/zadania | Nie |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appClusters | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | assessmentProjects | Yes |
> | migrateprojects | Yes |
> | projektami | Yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | Yes |
> | surfaceReconstructionAccounts | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts/backupPolicies | Yes |
> | netAppAccounts/capacityPools | Yes |
> | netAppAccounts/capacityPools/woluminy | Yes |
> | netAppAccounts/capacityPools/woluminy/kopie zapasowe | Nie |
> | netAppAccounts/capacityPools/woluminy/mountTargets | Yes |
> | netAppAccounts/capacityPools/woluminy/migawki | Yes |
> | netAppAccounts/magazyny | Nie |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationGateways | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | Nie |
> | azureFirewalls | Yes |
> | bastionHosts | Yes |
> | bgpServiceCommunities | Nie |
> | Licznik | Yes |
> | ddosCustomPolicies | Yes |
> | ddosProtectionPlans | Yes |
> | dnsOperationStatuses | Nie |
> | dnszones | Yes |
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
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | Nie |
> | firewallPolicies | Yes |
> | usługi frontdoor | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nie |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | Nie |
> | internalNotify | Nie |
> | loadBalancers | Yes |
> | localNetworkGateways | Yes |
> | natGateways | Yes |
> | networkIntentPolicies | Yes |
> | networkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers/connectionMonitors | Yes |
> | networkWatchers/soczewki | Yes |
> | networkWatchers/pingMeshes | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | Nie |
> | privateDnsZones | Yes |
> | privateDnsZones/A | Nie |
> | privateDnsZones/AAAA | Nie |
> | privateDnsZones/wszystkie | Nie |
> | privateDnsZones/CNAME | Nie |
> | privateDnsZones/MX | Nie |
> | privateDnsZones/PTR | Nie |
> | privateDnsZones/SOA | Nie |
> | privateDnsZones/SRV | Nie |
> | privateDnsZones/TXT | Nie |
> | privateDnsZones/virtualNetworkLinks | Yes |
> | privateEndpoints | Yes |
> | privateLinkServices | Yes |
> | publicIPAddresses | Yes |
> | publicIPPrefixes | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | Nie |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles/map cieplnych | Nie |
> | trafficManagerUserMetricsKeys | Nie |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworkTaps | Yes |
> | virtualWans | Yes |
> | vpnGateways | Yes |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przestrzeni | Yes |
> | przestrzenie nazw/notificationHubs | Yes |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | osNamespaces | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | HyperVSites | Yes |
> | ImportSites | Yes |
> | ServerSites | Yes |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Yes |
> | urządzenia | Nie |
> | linkTargets | Nie |
> | storageInsightConfigs | Nie |
> | obszarów roboczych | Yes |
> | obszary robocze/źródła danych | Nie |
> | workspaces/linkedServices | Nie |
> | obszary robocze/zapytanie | Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | managementassociations | Nie |
> | managementconfigurations | Yes |
> | rozwiązania | Yes |
> | Widoki | Yes |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | legacyPeerings | Nie |
> | peerAsns | Nie |
> | komunikacje równorzędne | Yes |
> | peeringServiceProviders | Nie |
> | peeringServices | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | policyEvents | Nie |
> | policyMetadata | Nie |
> | policyStates | Nie |
> | policyTrackedResources | Nie |
> | korygowania | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konsol | Nie |
> | pulpitów nawigacyjnych | Yes |
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
> | produkcyjnych | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | backupProtectedItems | Nie |
> | magazynów | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przestrzeni | Yes |
> | przestrzenie nazw/reguł autoryzacji | Nie |
> | przestrzenie nazw/hybridconnections | Nie |
> | przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie |
> | przestrzenie nazw/wcfrelays | Nie |
> | przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Nie |
> | Kolekcje | Yes |
> | Kolekcje/aplikacje | Nie |
> | Kolekcje/SecurityPrincipals | Nie |
> | templateImages | Nie |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | — zapytania | Yes |
> | resourceChangeDetails | Nie |
> | resourceChanges | Nie |
> | produkcyjnych | Nie |
> | resourcesHistory | Nie |
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
> | metadane | Nie |
> | złożone | Nie |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | komputerów | Nie |
> | wdrożenia/operacje | Nie |
> | deploymentScripts | Yes |
> | deploymentScripts/dzienniki | Nie |
> | linki | Nie |
> | notifyResourceJobs | Nie |
> | dostawcy | Nie |
> | resourceGroups | Nie |
> | produkcyjnych | Nie |
> | opłaty | Nie |
> | subskrypcje/dostawcy | Nie |
> | subskrypcje/resourceGroups | Nie |
> | subskrypcje/ResourceGroups/zasoby | Nie |
> | subskrypcje/zasoby | Nie |
> | subskrypcje/TagName | Nie |
> | subscriptions/tagNames/tagValues | Nie |
> | Dzierżaw | Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Yes |
> | saasresources | Nie |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | jobcollections | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nie |
> | searchServices | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Nie |
> | advancedThreatProtectionSettings | Nie |
> | alerts | Nie |
> | allowedConnections | Nie |
> | applicationWhitelistings | Nie |
> | assessmentMetadata | Nie |
> | oceny | Nie |
> | automatyzacji | Yes |
> | AutoProvisioningSettings | Nie |
> | Zachowania zgodności prawnymi | Nie |
> | dataCollectionAgents | Nie |
> | deviceSecurityGroups | Nie |
> | discoveredSecuritySolutions | Nie |
> | externalSecuritySolutions | Nie |
> | InformationProtectionPolicies | Nie |
> | iotSecuritySolutions | Yes |
> | iotSecuritySolutions/analyticsModels | Nie |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Nie |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Nie |
> | jitNetworkAccessPolicies | Nie |
> | networkData | Nie |
> | playbookConfigurations | Yes |
> | jazd | Nie |
> | cen | Nie |
> | regulatoryComplianceStandards | Nie |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Nie |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Nie |
> | securityContacts | Nie |
> | securitySolutions | Nie |
> | securitySolutionsReferenceData | Nie |
> | securityStatuses | Nie |
> | securityStatusesSummaries | Nie |
> | serverVulnerabilityAssessments | Nie |
> | settings | Nie |
> | podoceny | Nie |
> | zadania | Nie |
> | replikacji | Nie |
> | workspaceSettings | Nie |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie |
> | diagnosticSettingsCategories | Nie |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | agregacji | Nie |
> | alertRules | Nie |
> | alertRuleTemplates | Nie |
> | zakładki | Nie |
> | padkach | Nie |
> | Połączenia dataconnecters | Nie |
> | obiekty | Nie |
> | entityQueries | Nie |
> | officeConsents | Nie |
> | settings | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przestrzeni | Yes |
> | przestrzenie nazw/reguł autoryzacji | Nie |
> | przestrzenie nazw/disasterrecoveryconfigs | Nie |
> | przestrzenie nazw/eventgridfilters | Nie |
> | przestrzenie nazw/networkrulesets | Nie |
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
> | aplikacje | Yes |
> | oparty | Yes |
> | klastry/aplikacje | Nie |
> | containerGroups | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters/aplikacje | Nie |
> | sieci | Yes |
> | secretstores | Yes |
> | secretstores/certyfikaty | Nie |
> | secretstores/wpisy tajne | Nie |
> | volumes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Yes |
> | containerGroups | Yes |
> | bram | Yes |
> | sieci | Yes |
> | wpisy tajne | Yes |
> | volumes | Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | providerRegistrations | Nie |
> | providerRegistrations/resourceTypeRegistrations | Nie |
> | wprowadzanie | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SignalR | Yes |
> | Sygnalizujący/eventGridFilters | Nie |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SiteRecoveryVault | Yes |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hybridUseBenefits | Nie |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationDefinitions | Yes |
> | aplikacje | Yes |
> | jitRequests | Yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances/bazy danych | Yes |
> | managedInstances/databases/backupShortTermRetentionPolicies | Nie |
> | managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels | Nie |
> | managedInstances/Databases/vulnerabilityAssessments | Nie |
> | managedInstances/bazy danych/vulnerabilityAssessments/reguły/linie bazowe | Nie |
> | managedInstances/encryptionProtector | Nie |
> | managedInstances/klucze | Nie |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nie |
> | managedInstances/vulnerabilityAssessments | Nie |
> | serwery | Yes |
> | serwery/Administratorzy | Nie |
> | serwery/communicationLinks | Nie |
> | Serwery/bazy danych | Yes |
> | serwery/encryptionProtector | Nie |
> | serwery/firewallRules | Nie |
> | serwery/klucze | Nie |
> | servers/restorableDroppedDatabases | Nie |
> | serwery/cele | Nie |
> | serwery/tdeCertificates | Nie |
> | virtualClusters | Nie |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Nie |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | Nie |
> | storageAccounts/fileServices | Nie |
> | storageAccounts/queueServices | Nie |
> | storageAccounts/usługi | Nie |
> | storageAccounts/usługi/metricDefinitions | Nie |
> | storageAccounts/tableServices | Nie |
> | użycia | Nie |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | pamięci podręcznych | Yes |
> | pamięci podręczne/storageTargets | Nie |
> | usageModels | Nie |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | replicationGroups | Nie |

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

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | Nie |
> | storageSyncServices/syncGroups | Nie |
> | storageSyncServices/syncGroups/cloudEndpoints | Nie |
> | storageSyncServices/syncGroups/serverEndpoints | Nie |
> | storageSyncServices/przepływy pracy | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

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
> | streamingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Anuluj | Nie |
> | Anulowanie subskrypcji | Nie |
> | mogły | Nie |
> | ZmieńNazwę | Nie |
> | SubscriptionDefinitions | Nie |
> | SubscriptionOperations | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Wiejski | Yes |
> | środowiska/accessPolicies | Nie |
> | środowiska/źródła zdarzeń | Yes |
> | environments/referenceDataSets | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Yes |
> | dedicatedCloudServices | Yes |
> | virtualMachines | Yes |

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
> | przystawki | Yes |
> | connectionGateways | Yes |
> | Licznik | Yes |
> | customApis | Yes |
> | deletedSites | Nie |
> | — funkcje | Nie |
> | hostingEnvironments | Yes |
> | hostingEnvironments/multiRolePools | Nie |
> | hostingEnvironments/workerPools | Nie |
> | publishingUsers | Nie |
> | Zalecenia | Nie |
> | resourceHealthMetadata | Nie |
> | Runtime | Nie |
> | Dopuszczalna | Yes |
> | Dopuszczalna/eventGridFilters | Nie |
> | teren | Yes |
> | Lokacje/konfiguracja  | Nie |
> | Lokacje/eventGridFilters | Nie |
> | sites/hostNameBindings | Nie |
> | Lokacje/networkConfig | Nie |
> | sites/premieraddons | Yes |
> | Lokacje/miejsca | Yes |
> | Lokacje/miejsca/eventGridFilters | Nie |
> | sites/slots/hostNameBindings | Nie |
> | Lokacje/miejsca/networkConfig | Nie |
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
> | DeviceServices | Yes |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | komponentów | Nie |
> | componentsSummary | Nie |
> | monitorInstances | Nie |
> | monitorInstancesSummary | Nie |
> | monitora | Nie |
> | notificationSettings | Nie |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać te same dane jak plik z wartościami rozdzielanymi przecinkami, Pobierz [Complete-Mode-deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
