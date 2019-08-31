---
title: Azure Resource Manager usuwania trybu pełnego według typu zasobu
description: Pokazuje, jak typy zasobów obsługują usuwanie w trybie pełnym w szablonach Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/04/2019
ms.author: tomfitz
ms.openlocfilehash: d783ab9c12952393f0a74b245109dda58bff3927
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194397"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Usuwanie zasobów platformy Azure na potrzeby wdrożeń w trybie pełnym

W tym artykule opisano sposób, w jaki typy zasobów obsługują usuwanie, gdy nie znajduje się w szablonie wdrożonym w trybie kompletnym.

Typy zasobów oznaczone **wartością tak** są usuwane, gdy typ nie jest w szablonie wdrożonym z trybem kompletnym.

Typy zasobów oznaczone atrybutem **no** nie są automatycznie usuwane, gdy nie znajdują się w szablonie; Jednak są usuwane, jeśli zasób nadrzędny zostanie usunięty. Pełny opis zachowania można znaleźć w temacie [Azure Resource Manager Modes Deployments](deployment-modes.md).

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. AADDomainServices](#microsoftaaddomainservices)
> - [Microsoft. Dodatki](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
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
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
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
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. datacegły](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
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
> - [Microsoft. ManagedLab](#microsoftmanagedlab)
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
> | DomainServices | Tak |
> | DomainServices/oucontainer | Nie |
> | DomainServices/ReplicaSets | Tak |

## <a name="microsoftaaddomainservices"></a>Microsoft. AADDomainServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | domeny | Nie |

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
> | actionRules | Tak |
> | alerts | Nie |
> | alertsList | Nie |
> | alertsMetaData | Nie |
> | alertsSummary | Nie |
> | alertsSummaryList | Nie |
> | opinii | Nie |
> | smartDetectorAlertRules | Nie |
> | smartDetectorRuntimeEnvironments | Nie |
> | smartGroups | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | serwerów | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | reportFeedback | Nie |
> | usługa | Tak |
> | validateServiceName | Nie |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | configurationStores | Tak |
> | configurationStores/eventGridFilters | Nie |

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
> | automationAccounts | Tak |
> | automationAccounts/konfiguracje | Tak |
> | automationAccounts/zadania | Nie |
> | automationAccounts/elementy Runbook | Tak |
> | automationAccounts/softwareUpdateConfigurations | Nie |
> | automationAccounts/elementy webhook | Nie |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | configurationStores | Tak |
> | configurationStores/eventGridFilters | Nie |

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
> | b2ctenants | Nie |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | sqlServerRegistrations | Tak |
> | sqlServerRegistrations/SQLServers | Nie |

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
> | billingAccounts/umowy | Nie |
> | billingAccounts/billingProfiles | Nie |
> | billingAccounts/billingProfiles/billingSubscriptions | Nie |
> | billingAccounts/billingProfiles/faktury | Nie |
> | billingAccounts/billingProfiles/faktury/arkusza cen | Nie |
> | billingAccounts/billingProfiles/invoiceSections | Nie |
> | billingAccounts/BillingProfiles/patchOperations | Nie |
> | billingAccounts/billingProfiles/paymentMethods | Nie |
> | billingAccounts/billingProfiles/zasady | Nie |
> | billingAccounts/billingProfiles/pricesheet | Nie |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Nie |
> | billingAccounts/billingProfiles/produkty | Nie |
> | billingAccounts/billingProfiles/Transactions | Nie |
> | billingAccounts/billingSubscriptions | Nie |
> | billingAccounts/createInvoiceSectionOperations | Nie |
> | billingAccounts/klienci | Nie |
> | billingAccounts/klienci/billingSubscriptions | Nie |
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
> | validateAddress | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | mapApis | Tak |
> | updateCommunicationPreference | Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | BizTalk | Tak |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | blockchainMembers | Tak |
> | obserwatorzy | Tak |

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
> | Języki | Nie |
> | szablony | Nie |

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
> | rezerwacje | Nie |
> | zasoby | Nie |
> | validateReservationOrder | Nie |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nie |
> | CdnWebApplicationFirewallPolicies | Tak |
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
> | domainNames | Tak |
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
> | virtualMachines | Tak |
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
> | networkSecurityGroups | Tak |
> | quotas | Nie |
> | reservedIps | Tak |
> | virtualNetworks | Tak |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nie |
> | virtualNetworks/virtualNetworkPeerings | Nie |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwość | Nie |
> | dyski | Nie |
> | obrazy | Nie |
> | osImages | Nie |
> | osPlatformImages | Nie |
> | publicImages | Nie |
> | quotas | Nie |
> | storageAccounts | Tak |
> | storageAccounts/metricDefinitions | Nie |
> | storageAccounts/metryki | Nie |
> | storageAccounts/usługi | Nie |
> | storageAccounts/usługi/diagnosticSettings | Nie |
> | storageAccounts/usługi/metricDefinitions | Nie |
> | storageAccounts/usługi/metryki | Nie |
> | storageAccounts/vmImages | Nie |
> | vmImages | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta | Tak |

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
> | availabilitySets | Tak |
> | diskEncryptionSets | Tak |
> | dyski | Tak |
> | znajduj | Tak |
> | Galerie/aplikacje | Tak |
> | Galerie/aplikacje/wersje | Tak |
> | Galerie/obrazy | Tak |
> | Galerie/obrazy/wersje | Tak |
> | hostGroups | Tak |
> | hostGroups/hosty | Tak |
> | obrazy | Tak |
> | proximityPlacementGroups | Tak |
> | restorePointCollections | Tak |
> | restorePointCollections/restorePoints | Nie |
> | sharedVMImages | Tak |
> | sharedVMImages/wersje | Tak |
> | migawki | Tak |
> | virtualMachines | Tak |
> | virtualMachines/rozszerzenia | Tak |
> | virtualMachines/metricDefinitions | Nie |
> | virtualMachines/scriptJobs | Nie |
> | virtualMachines/softwareUpdateDeployments | Nie |
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
> | Opłaty | Nie |
> | CostTags | Nie |
> | środki | Nie |
> | zdarzenia | Nie |
> | Prognozy | Nie |
> | ilości | Nie |
> | Platform handlowych | Nie |
> | Pricesheets | Nie |
> | produkty | Nie |
> | ReservationDetails | Nie |
> | ReservationRecommendations | Nie |
> | ReservationSummaries | Nie |
> | ReservationTransactions | Nie |
> | `Tags` | Nie |
> | dzierżaw | Nie |
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
> | rejestry/buildTasks | Tak |
> | registries/buildTasks/steps | Nie |
> | registries/eventGridFilters | Nie |
> | registries/getBuildSourceUploadUrl | Nie |
> | rejestry/getpoświadczenia | Nie |
> | registries/importImage | Nie |
> | rejestry/queueBuild | Nie |
> | registries/regenerateCredential | Nie |
> | rejestry/regenerateCredentials | Nie |
> | rejestry/replikacje | Tak |
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
> | containerServices | Tak |
> | managedClusters | Tak |
> | openShiftManagedClusters | Tak |

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
> | konta | Tak |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Alerty | Nie |
> | BillingAccounts | Nie |
> | Budżety | Nie |
> | CloudConnectors | Nie |
> | Łączniki | Tak |
> | Działy | Nie |
> | Wymiary | Nie |
> | EnrollmentAccounts | Nie |
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
> | Prognoza | Nie |
> | Zapytanie | Nie |
> | zarejestrować | Nie |
> | Reportconfigs | Nie |
> | Raporty | Nie |
> | Ustawienia | Nie |
> | showbackRules | Nie |
> | Widoki | Nie |

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
> | resourceProviders | Tak |

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
> | obszary robocze | Tak |
> | workspaces/virtualNetworkPeerings | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wykazy | Tak |
> | wykazy | Tak |
> | webcatalogs/scantargets | Nie |
> | webcatalogs/scantargets/zbiory danych | Nie |

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
> | datafactors/metricDefinitions | Nie |
> | dataFactorySchema | Nie |
> | fabryki | Tak |
> | fabryki/integrationRuntimes | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta | Tak |
> | accounts/dataLakeStoreAccounts | Nie |
> | konta/storageAccounts | Nie |
> | konta/storageAccounts/kontenery | Nie |
> | konta/transferAnalyticsUnits | Nie |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta | Tak |
> | accounts/eventGridFilters | Nie |
> | konta/firewallRules | Nie |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Tak |
> | usługi/projekty | Tak |
> | czasów | Tak |

## <a name="microsoftdatashare"></a>Microsoft. dataudział

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta | Tak |
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
> | serwerów | Tak |
> | serwery/doradcy | Nie |
> | serwery/queryTexts | Nie |
> | serwery/recoverableServers | Nie |
> | servers/topQueryStatistics | Nie |
> | servers/virtualNetworkRules | Nie |
> | serwery/waitStatistics | Nie |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | serwerów | Tak |
> | serwery/doradcy | Nie |
> | serwery/queryTexts | Nie |
> | serwery/recoverableServers | Nie |
> | servers/topQueryStatistics | Nie |
> | servers/virtualNetworkRules | Nie |
> | serwery/waitStatistics | Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | serverGroups | Tak |
> | serwerów | Tak |
> | serwery/doradcy | Nie |
> | serwery/queryTexts | Nie |
> | serwery/recoverableServers | Nie |
> | servers/topQueryStatistics | Nie |
> | servers/virtualNetworkRules | Nie |
> | serwery/waitStatistics | Nie |
> | serversv2 | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | artifactSources | Tak |
> | wprowadzanie | Tak |
> | servicetopologie | Tak |
> | servicetopologie/usługi | Tak |
> | servicetopologie/usługi/serviceunits | Tak |
> | kroki | Tak |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationgroups | Tak |
> | applicationgroups/aplikacje | Nie |
> | applicationgroups/assignedusers | Nie |
> | applicationgroups/startmenuitems | Nie |
> | hostpools | Tak |
> | hostpools/sessionhosts | Nie |
> | hostpools/sessionhosts/usersessions | Nie |
> | hostpools/usersessions | Nie |
> | obszary robocze | Tak |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | ElasticPools | Tak |
> | ElasticPools/IotHubTenants | Tak |
> | IotHubs | Tak |
> | IotHubs/eventGridFilters | Nie |
> | ProvisioningServices | Tak |
> | użycia | Nie |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | potoki | Tak |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Rejestrowanie | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | labcenters | Tak |
> | Labs | Tak |
> | laboratoria/środowiska | Tak |
> | labs/serviceRunners | Tak |
> | Labs/virtualMachines | Tak |
> | Uruchamianie | Tak |

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

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Tak |

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
> | przestrzenie nazw | Tak |
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
> | zasoby | Nie |
> | retrieveresourcesbyid | Nie |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta | Tak |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | guestConfigurationAssignments | Nie |
> | programy | Nie |
> | softwareUpdateProfile | Nie |
> | softwareUpdates | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hanaInstances | Tak |
> | sapMonitors | Tak |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Modułów dedicatedhsms | Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Tak |
> | klastry/aplikacje | Nie |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Tak |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | maszyny | Tak |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | datamanagers | Tak |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | komponentów | Tak |
> | networkScopes | Tak |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zadania | Tak |

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
> | IoTApps | Tak |

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
> | hsmPools | Tak |
> | magazynów | Tak |
> | magazyny/accessPolicies | Nie |
> | magazyny/eventGridFilters | Nie |
> | magazyny/wpisy tajne | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Tak |
> | klastry/attacheddatabaseconfigurations | Nie |
> | klastry/bazy danych | Nie |
> | klastry/bazy danych/połączenia Databases | Nie |
> | klastry/bazy danych/eventhubconnections | Nie |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | labaccounts | Tak |
> | Użytkownicy | Nie |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hostingEnvironments | Tak |
> | integrationAccounts | Tak |
> | integrationServiceEnvironments | Tak |
> | isolatedEnvironments | Tak |
> | przebieg | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | commitmentPlans | Tak |
> | webServices | Tak |
> | Obszary robocze | Tak |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | obszary robocze | Tak |
> | obszary robocze/obliczenia | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Tożsamości | Nie |
> | Resourceidentity | Tak |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | labaccounts | Tak |

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
> | zasoby | Nie |
> | startTenantBackfill | Nie |
> | tenantBackfillStatus | Nie |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta | Tak |
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
> | produkty | Nie |
> | wydawcy | Nie |
> | wydawcy/oferty | Nie |
> | wydawcy/oferty/zmiany | Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | classicDevServices | Tak |
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
> | MediaServices | Tak |
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

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appClusters | Tak |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | assessmentProjects | Tak |
> | migrateprojects | Tak |
> | projektami | Tak |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | remoteRenderingAccounts | Tak |
> | spatialAnchorsAccounts | Tak |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | netAppAccounts | Tak |
> | netAppAccounts/capacityPools | Tak |
> | netAppAccounts/capacityPools/woluminy | Tak |
> | netAppAccounts/capacityPools/woluminy/mountTargets | Tak |
> | netAppAccounts/capacityPools/woluminy/migawki | Tak |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationGateways | Tak |
> | applicationGatewayWebApplicationFirewallPolicies | Tak |
> | applicationSecurityGroups | Tak |
> | azureFirewallFqdnTags | Nie |
> | azureFirewalls | Tak |
> | bastionHosts | Tak |
> | bgpServiceCommunities | Nie |
> | połączenia | Tak |
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
> | expressRouteCircuits | Tak |
> | expressRouteCrossConnections | Tak |
> | expressRouteGateways | Tak |
> | expressRoutePorts | Tak |
> | expressRouteServiceProviders | Nie |
> | firewallPolicies | Tak |
> | usługi Frontdoor | Tak |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nie |
> | frontdoorWebApplicationFirewallPolicies | Tak |
> | getDnsResourceReference | Nie |
> | internalNotify | Nie |
> | loadBalancers | Tak |
> | localNetworkGateways | Tak |
> | natGateways | Tak |
> | networkIntentPolicies | Tak |
> | networkInterfaces | Tak |
> | networkProfiles | Tak |
> | networkSecurityGroups | Tak |
> | networkWatchers | Tak |
> | networkWatchers/connectionMonitors | Tak |
> | networkWatchers/soczewki | Tak |
> | networkWatchers/pingMeshes | Tak |
> | p2sVpnGateways | Tak |
> | privateDnsOperationStatuses | Nie |
> | privateDnsZones | Tak |
> | privateDnsZones/A | Nie |
> | privateDnsZones/AAAA | Nie |
> | privateDnsZones/wszystkie | Nie |
> | privateDnsZones/CNAME | Nie |
> | privateDnsZones/MX | Nie |
> | privateDnsZones/PTR | Nie |
> | privateDnsZones/SOA | Nie |
> | privateDnsZones/SRV | Nie |
> | privateDnsZones/TXT | Nie |
> | privateDnsZones/virtualNetworkLinks | Tak |
> | privateEndpoints | Tak |
> | privateLinkServices | Tak |
> | publicIPAddresses | Tak |
> | publicIPPrefixes | Tak |
> | routeFilters | Tak |
> | routeTables | Tak |
> | secureGateways | Tak |
> | serviceEndpointPolicies | Tak |
> | trafficManagerGeographicHierarchies | Nie |
> | trafficmanagerprofiles | Tak |
> | trafficmanagerprofiles/map cieplnych | Nie |
> | trafficManagerUserMetricsKeys | Nie |
> | virtualHubs | Tak |
> | virtualNetworkGateways | Tak |
> | virtualNetworks | Tak |
> | virtualNetworkTaps | Tak |
> | virtualWans | Tak |
> | vpnGateways | Tak |
> | vpnSites | Tak |
> | webApplicationFirewallPolicies | Tak |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przestrzenie nazw | Tak |
> | przestrzenie nazw/notificationHubs | Tak |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | HyperVSites | Tak |
> | ImportSites | Tak |
> | ServerSites | Tak |
> | VMwareSites | Tak |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | urządzenia | Nie |
> | linkTargets | Nie |
> | storageInsightConfigs | Nie |
> | obszary robocze | Tak |
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
> | wyświetlenia | Tak |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | legacyPeerings | Nie |
> | peerAsns | Nie |
> | komunikacje równorzędne | Tak |

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
> | pulpitów nawigacyjnych | Tak |
> | userSettings | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | workspaceCollections | Tak |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | produkcyjnych | Tak |

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
> | przestrzenie nazw | Tak |
> | przestrzenie nazw/reguł autoryzacji | Nie |
> | przestrzenie nazw/hybridconnections | Nie |
> | przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie |
> | przestrzenie nazw/wcfrelays | Nie |
> | przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta | Nie |
> | Kolekcje | Tak |
> | Kolekcje/aplikacje | Nie |
> | Kolekcje/SecurityPrincipals | Nie |
> | templateImages | Nie |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | — zapytania | Tak |
> | resourceChangeDetails | Nie |
> | resourceChanges | Nie |
> | zasoby | Nie |
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
> | powiadomienia | Nie |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wdrożenia | Nie |
> | wdrożenia/operacje | Nie |
> | linki | Nie |
> | notifyResourceJobs | Nie |
> | dostawcy | Nie |
> | resourceGroups | Nie |
> | zasoby | Nie |
> | subskrypcji | Nie |
> | subskrypcje/dostawcy | Nie |
> | subskrypcje/resourceGroups | Nie |
> | subskrypcje/ResourceGroups/zasoby | Nie |
> | subskrypcje/zasoby | Nie |
> | subskrypcje/TagName | Nie |
> | subscriptions/tagNames/tagValues | Nie |
> | tags | Nie |
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
> | przepływy | Tak |
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
> | adaptiveNetworkHardenings | Nie |
> | advancedThreatProtectionSettings | Nie |
> | alerts | Nie |
> | allowedConnections | Nie |
> | applicationWhitelistings | Nie |
> | assessmentMetadata | Nie |
> | oceny | Nie |
> | AutoProvisioningSettings | Nie |
> | Zachowania zgodności prawnymi | Nie |
> | dataCollectionAgents | Nie |
> | deviceSecurityGroups | Nie |
> | discoveredSecuritySolutions | Nie |
> | externalSecuritySolutions | Nie |
> | InformationProtectionPolicies | Nie |
> | iotSecuritySolutions | Tak |
> | iotSecuritySolutions/analyticsModels | Nie |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Nie |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Nie |
> | jitNetworkAccessPolicies | Nie |
> | playbookConfigurations | Tak |
> | Zasady | Nie |
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
> | ustawienia | Nie |
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
> | Reguły alertów | Nie |
> | zakładki | Nie |
> | padkach | Nie |
> | Połączenia dataconnecters | Nie |
> | jednostki | Nie |
> | entityQueries | Nie |
> | officeConsents | Nie |
> | ustawienia | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przestrzenie nazw | Tak |
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
> | aplikacje | Tak |
> | oparty | Tak |
> | klastry/aplikacje | Nie |
> | containerGroups | Tak |
> | containerGroupSets | Tak |
> | edgeclusters | Tak |
> | edgeclusters/aplikacje | Nie |
> | sieci | Tak |
> | secretstores | Tak |
> | secretstores/certyfikaty | Nie |
> | secretstores/wpisy tajne | Nie |
> | woluminy | Tak |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Tak |
> | containerGroups | Tak |
> | bram | Tak |
> | sieci | Tak |
> | wpisy tajne | Tak |
> | woluminy | Tak |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | providerRegistrations | Nie |
> | providerRegistrations/resourceTypeRegistrations | Nie |
> | wprowadzanie | Tak |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SignalR | Tak |
> | Sygnalizujący/eventGridFilters | Nie |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SiteRecoveryVault | Tak |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hybridUseBenefits | Nie |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationDefinitions | Tak |
> | aplikacje | Tak |
> | jitRequests | Tak |

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
> | serwerów | Tak |
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
> | storageAccounts/usługi/metricDefinitions | Nie |
> | storageAccounts/tableServices | Nie |
> | użycia | Nie |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | pamięci podręcznych | Tak |
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
> | storageSyncServices | Tak |
> | storageSyncServices/registeredServers | Nie |
> | storageSyncServices/syncGroups | Nie |
> | storageSyncServices/syncGroups/cloudEndpoints | Nie |
> | storageSyncServices/syncGroups/serverEndpoints | Nie |
> | storageSyncServices/przepływy pracy | Nie |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageSyncServices | Tak |
> | storageSyncServices/registeredServers | Nie |
> | storageSyncServices/syncGroups | Nie |
> | storageSyncServices/syncGroups/cloudEndpoints | Nie |
> | storageSyncServices/syncGroups/serverEndpoints | Nie |
> | storageSyncServices/przepływy pracy | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageSyncServices | Tak |
> | storageSyncServices/registeredServers | Nie |
> | storageSyncServices/syncGroups | Nie |
> | storageSyncServices/syncGroups/cloudEndpoints | Nie |
> | storageSyncServices/syncGroups/serverEndpoints | Nie |
> | storageSyncServices/przepływy pracy | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | skonfigurowany | Tak |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | streamingjobs | Tak |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | anuluj | Nie |
> | Anulowanie subskrypcji | Nie |
> | zmień nazwę | Nie |
> | SubscriptionDefinitions | Nie |
> | SubscriptionOperations | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wiejski | Tak |
> | środowiska/accessPolicies | Nie |
> | środowiska/źródła zdarzeń | Tak |
> | environments/referenceDataSets | Tak |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Tak |
> | dedicatedCloudServices | Tak |
> | virtualMachines | Tak |

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
> | Certyfikaty | Tak |
> | connectionGateways | Tak |
> | połączenia | Tak |
> | customApis | Tak |
> | deletedSites | Nie |
> | — funkcje | Nie |
> | hostingEnvironments | Tak |
> | hostingEnvironments/multiRolePools | Nie |
> | hostingEnvironments/workerPools | Nie |
> | publishingUsers | Nie |
> | Zalecenia | Nie |
> | resourceHealthMetadata | Nie |
> | Runtime | Nie |
> | Dopuszczalna | Tak |
> | Dopuszczalna/eventGridFilters | Nie |
> | lokacje | Tak |
> | Lokacje/konfiguracja  | Nie |
> | Lokacje/eventGridFilters | Nie |
> | sites/hostNameBindings | Nie |
> | Lokacje/networkConfig | Nie |
> | sites/premieraddons | Tak |
> | Lokacje/miejsca | Tak |
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
> | DeviceServices | Tak |

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
