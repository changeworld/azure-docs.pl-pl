---
title: Tag support for resources
description: Shows which Azure resource types support tags. Provides details for all Azure services.
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 5b3e26d914887496eedde609404eaf0b380dbcc0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422141"
---
# <a name="tag-support-for-azure-resources"></a>Tag support for Azure resources
This article describes whether a resource type supports [tags](resource-group-using-tags.md). The column labeled **Supports tags** indicates whether the resource type has a property for the tag. The column labeled **Tag in cost report** indicates whether that resource type passes the tag to the cost report. You can view costs by tags in the [Cost Management cost analysis](../cost-management/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) and the [Azure billing invoice and daily usage data](../billing/billing-download-azure-invoice-daily-usage-date.md).

To get the same data as a file of comma-separated values, download [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DomainServices | Tak | Tak |
> | DomainServices / oucontainer | Nie | Nie |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nie | Nie |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nie | Nie |
> | addsservices | Nie | Nie |
> | agents | Nie | Nie |
> | anonymousapiusers | Nie | Nie |
> | konfiguracja | Nie | Nie |
> | dzienniki | Nie | Nie |
> | reports | Nie | Nie |
> | servicehealthmetrics | Nie | Nie |
> | services | Nie | Nie |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurations | Nie | Nie |
> | generateRecommendations | Nie | Nie |
> | metadane | Nie | Nie |
> | recommendations | Nie | Nie |
> | suppressions | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | actionRules | Tak | Tak |
> | alerts | Nie | Nie |
> | alertsList | Nie | Nie |
> | alertsMetaData | Nie | Nie |
> | alertsSummary | Nie | Nie |
> | alertsSummaryList | Nie | Nie |
> | feedback | Nie | Nie |
> | smartDetectorAlertRules | Tak | Tak |
> | smartDetectorRuntimeEnvironments | Nie | Nie |
> | smartGroups | Nie | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serwery | Tak | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nie | Nie |
> | usługa | Tak | Tak |
> | validateServiceName | Nie | Nie |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Tak | Tak |
> | configurationStores / eventGridFilters | Nie | Nie |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Spring | Tak | Tak |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Nie | Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Nie | Nie |
> | dataAliases | Nie | Nie |
> | denyAssignments | Nie | Nie |
> | elevateAccess | Nie | Nie |
> | findOrphanRoleAssignments | Nie | Nie |
> | locks | Nie | Nie |
> | permissions | Nie | Nie |
> | policyAssignments | Nie | Nie |
> | policyDefinitions | Nie | Nie |
> | policySetDefinitions | Nie | Nie |
> | providerOperations | Nie | Nie |
> | roleAssignments | Nie | Nie |
> | roleDefinitions | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Tak | Tak |
> | automationAccounts / configurations | Tak | Tak |
> | automationAccounts / jobs | Nie | Nie |
> | automationAccounts / runbooks | Tak | Tak |
> | automationAccounts / softwareUpdateConfigurations | Nie | Nie |
> | automationAccounts / webhooks | Nie | Nie |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Tak | Tak |
> | configurationStores / eventGridFilters | Nie | Nie |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Nie | Nie |
> | environments / accounts | Nie | Nie |
> | environments / accounts / namespaces | Nie | Nie |
> | environments / accounts / namespaces / configurations | Nie | Nie |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Tak | Nie |
> | b2ctenants | Nie | Nie |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Tak | Tak |
> | postgresInstances | Tak | Tak |
> | sqlBigDataClusters | Tak | Tak |
> | sqlInstances | Tak | Tak |
> | sqlServerRegistrations | Tak | Tak |
> | sqlServerRegistrations / sqlServers | Nie | Nie |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registrations | Tak | Tak |
> | registrations / customerSubscriptions | Nie | Nie |
> | registrations / products | Nie | Nie |
> | verificationKeys | Nie | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Tak | Tak |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nie | Nie |
> | billingAccounts / agreements | Nie | Nie |
> | billingAccounts / billingPermissions | Nie | Nie |
> | billingAccounts / billingProfiles | Nie | Nie |
> | billingAccounts / billingProfiles / billingPermissions | Nie | Nie |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nie | Nie |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nie | Nie |
> | billingAccounts / billingProfiles / billingSubscriptions | Nie | Nie |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nie | Nie |
> | billingAccounts / billingProfiles / customers | Nie | Nie |
> | billingAccounts / billingProfiles / invoices | Nie | Nie |
> | billingAccounts / billingProfiles / invoices / pricesheet | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / products | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nie | Nie |
> | billingAccounts / BillingProfiles / patchOperations | Nie | Nie |
> | billingAccounts / billingProfiles / paymentMethods | Nie | Nie |
> | billingAccounts / billingProfiles / policies | Nie | Nie |
> | billingAccounts / billingProfiles / pricesheet | Nie | Nie |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nie | Nie |
> | billingAccounts / billingProfiles / products | Nie | Nie |
> | billingAccounts / billingProfiles / transactions | Nie | Nie |
> | billingAccounts / billingRoleAssignments | Nie | Nie |
> | billingAccounts / billingRoleDefinitions | Nie | Nie |
> | billingAccounts / billingSubscriptions | Nie | Nie |
> | billingAccounts / billingSubscriptions / invoices | Nie | Nie |
> | billingAccounts / createBillingRoleAssignment | Nie | Nie |
> | billingAccounts / createInvoiceSectionOperations | Nie | Nie |
> | billingAccounts / customers | Nie | Nie |
> | billingAccounts / customers / billingPermissions | Nie | Nie |
> | billingAccounts / customers / billingSubscriptions | Nie | Nie |
> | billingAccounts / customers / initiateTransfer | Nie | Nie |
> | billingAccounts / customers / policies | Nie | Nie |
> | billingAccounts / customers / products | Nie | Nie |
> | billingAccounts / customers / transactions | Nie | Nie |
> | billingAccounts / customers / transfers | Nie | Nie |
> | billingAccounts / departments | Nie | Nie |
> | billingAccounts / enrollmentAccounts | Nie | Nie |
> | billingAccounts / invoices | Nie | Nie |
> | billingAccounts / invoiceSections | Nie | Nie |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nie | Nie |
> | billingAccounts / invoiceSections / billingSubscriptions | Nie | Nie |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Nie | Nie |
> | billingAccounts / invoiceSections / elevate | Nie | Nie |
> | billingAccounts / invoiceSections / initiateTransfer | Nie | Nie |
> | billingAccounts / invoiceSections / patchOperations | Nie | Nie |
> | billingAccounts / invoiceSections / productMoveOperations | Nie | Nie |
> | billingAccounts / invoiceSections / products | Nie | Nie |
> | billingAccounts / invoiceSections / products / transfer | Nie | Nie |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Nie | Nie |
> | billingAccounts / invoiceSections / transactions | Nie | Nie |
> | billingAccounts / invoiceSections / transfers | Nie | Nie |
> | billingAccounts / lineOfCredit | Nie | Nie |
> | billingAccounts / patchOperations | Nie | Nie |
> | billingAccounts / paymentMethods | Nie | Nie |
> | billingAccounts / products | Nie | Nie |
> | billingAccounts / transactions | Nie | Nie |
> | billingPeriods | Nie | Nie |
> | billingPermissions | Nie | Nie |
> | billingProperty | Nie | Nie |
> | billingRoleAssignments | Nie | Nie |
> | billingRoleDefinitions | Nie | Nie |
> | createBillingRoleAssignment | Nie | Nie |
> | departments | Nie | Nie |
> | enrollmentAccounts | Nie | Nie |
> | invoices | Nie | Nie |
> | transfers | Nie | Nie |
> | transfers / acceptTransfer | Nie | Nie |
> | transfers / declineTransfer | Nie | Nie |
> | transfers / operationStatus | Nie | Nie |
> | transfers / validateTransfer | Nie | Nie |
> | validateAddress | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mapApis | Tak | Tak |
> | updateCommunicationPreference | Nie | Nie |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Tak | Tak |
> | cordaMembers | Tak | Tak |
> | obserwatorzy | Tak | Tak |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nie | Nie |
> | blueprintAssignments / assignmentOperations | Nie | Nie |
> | blueprintAssignments / operations | Nie | Nie |
> | blueprints | Nie | Nie |
> | blueprints / artifacts | Nie | Nie |
> | blueprints / versions | Nie | Nie |
> | blueprints / versions / artifacts | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | botServices | Tak | Tak |
> | botServices / channels | Nie | Nie |
> | botServices / connections | Nie | Nie |
> | languages | Nie | Nie |
> | szablonów | Nie | Nie |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Redis | Tak | Tak |
> | RedisConfigDefinition | Nie | Nie |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nie | Nie |
> | calculateExchange | Nie | Nie |
> | calculatePrice | Nie | Nie |
> | calculatePurchasePrice | Nie | Nie |
> | catalogs | Nie | Nie |
> | commercialReservationOrders | Nie | Nie |
> | exchange | Nie | Nie |
> | placePurchaseOrder | Nie | Nie |
> | reservationOrders | Nie | Nie |
> | reservationOrders / calculateRefund | Nie | Nie |
> | reservationOrders / merge | Nie | Nie |
> | reservationOrders / reservations | Nie | Nie |
> | reservationOrders / reservations / revisions | Nie | Nie |
> | reservationOrders / return | Nie | Nie |
> | reservationOrders / split | Nie | Nie |
> | reservationOrders / swap | Nie | Nie |
> | reservations | Nie | Nie |
> | resources | Nie | Nie |
> | validateReservationOrder | Nie | Nie |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nie | Nie |
> | CdnWebApplicationFirewallPolicies | Tak | Tak |
> | edgenodes | Nie | Nie |
> | profiles | Tak | Tak |
> | profiles / endpoints | Tak | Tak |
> | profiles / endpoints / customdomains | Nie | Nie |
> | profiles / endpoints / origins | Nie | Nie |
> | validateProbe | Nie | Nie |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Tak | Tak |
> | certificateOrders / certificates | Nie | Nie |
> | validateCertificateRegistrationInformation | Nie | Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nie | Nie |
> | domainNames | Nie | Nie |
> | domainNames / capabilities | Nie | Nie |
> | domainNames / internalLoadBalancers | Nie | Nie |
> | domainNames / serviceCertificates | Nie | Nie |
> | domainNames / slots | Nie | Nie |
> | domainNames / slots / roles | Nie | Nie |
> | domainNames / slots / roles / metricDefinitions | Nie | Nie |
> | domainNames / slots / roles / metrics | Nie | Nie |
> | moveSubscriptionResources | Nie | Nie |
> | operatingSystemFamilies | Nie | Nie |
> | operatingSystems | Nie | Nie |
> | quotas | Nie | Nie |
> | resourceTypes | Nie | Nie |
> | validateSubscriptionMoveAvailability | Nie | Nie |
> | virtualMachines | Nie | Nie |
> | virtualMachines / diagnosticSettings | Nie | Nie |
> | virtualMachines / metricDefinitions | Nie | Nie |
> | virtualMachines / metrics | Nie | Nie |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nie | Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nie | Nie |
> | expressRouteCrossConnections | Nie | Nie |
> | expressRouteCrossConnections / peerings | Nie | Nie |
> | gatewaySupportedDevices | Nie | Nie |
> | networkSecurityGroups | Nie | Nie |
> | quotas | Nie | Nie |
> | reservedIps | Nie | Nie |
> | virtualNetworks | Nie | Nie |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nie | Nie |
> | virtualNetworks / virtualNetworkPeerings | Nie | Nie |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nie | Nie |
> | disks | Nie | Nie |
> | images | Nie | Nie |
> | osImages | Nie | Nie |
> | osPlatformImages | Nie | Nie |
> | publicImages | Nie | Nie |
> | quotas | Nie | Nie |
> | storageAccounts | Nie | Nie |
> | storageAccounts / blobServices | Nie | Nie |
> | storageAccounts / fileServices | Nie | Nie |
> | storageAccounts / metricDefinitions | Nie | Nie |
> | storageAccounts / metrics | Nie | Nie |
> | storageAccounts / queueServices | Nie | Nie |
> | storageAccounts / services | Nie | Nie |
> | storageAccounts / services / diagnosticSettings | Nie | Nie |
> | storageAccounts / services / metricDefinitions | Nie | Nie |
> | storageAccounts / services / metrics | Nie | Nie |
> | storageAccounts / tableServices | Nie | Nie |
> | storageAccounts / vmImages | Nie | Nie |
> | vmImages | Nie | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | RateCard | Nie | Nie |
> | UsageAggregates | Nie | Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Tak | Tak |
> | diskEncryptionSets | Tak | Tak |
> | disks | Tak | Tak |
> | galleries | Tak | Tak |
> | galleries / applications | Nie | Nie |
> | galleries / applications / versions | Nie | Nie |
> | galleries / images | Nie | Nie |
> | galleries / images / versions | Nie | Nie |
> | hostGroups | Tak | Tak |
> | hostGroups / hosts | Tak | Tak |
> | images | Tak | Tak |
> | proximityPlacementGroups | Tak | Tak |
> | restorePointCollections | Tak | Tak |
> | restorePointCollections / restorePoints | Nie | Nie |
> | sharedVMImages | Tak | Tak |
> | sharedVMImages / versions | Nie | Nie |
> | Migawki | Tak | Tak |
> | virtualMachines | Tak | Tak |
> | virtualMachines / extensions | Tak | Tak |
> | virtualMachines / metricDefinitions | Nie | Nie |
> | virtualMachineScaleSets | Tak | Tak |
> | virtualMachineScaleSets / extensions | Nie | Nie |
> | virtualMachineScaleSets / networkInterfaces | Nie | Nie |
> | virtualMachineScaleSets / publicIPAddresses | Nie | Nie |
> | virtualMachineScaleSets / virtualMachines | Nie | Nie |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nie | Nie |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nie | Nie |
> | Salda | Nie | Nie |
> | Budżety | Nie | Nie |
> | Opłaty | Nie | Nie |
> | CostTags | Nie | Nie |
> | credits | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | Prognozy | Nie | Nie |
> | lots | Nie | Nie |
> | Marketplaces | Nie | Nie |
> | Pricesheets | Nie | Nie |
> | products | Nie | Nie |
> | ReservationDetails | Nie | Nie |
> | ReservationRecommendations | Nie | Nie |
> | ReservationSummaries | Nie | Nie |
> | ReservationTransactions | Nie | Nie |
> | Tagi | Nie | Nie |
> | tenants | Nie | Nie |
> | Warunki | Nie | Nie |
> | UsageDetails | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerGroups | Tak | Tak |
> | serviceAssociationLinks | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registries | Tak | Tak |
> | registries / builds | Nie | Nie |
> | registries / builds / cancel | Nie | Nie |
> | registries / builds / getLogLink | Nie | Nie |
> | registries / buildTasks | Tak | Tak |
> | registries / buildTasks / steps | Nie | Nie |
> | registries / eventGridFilters | Nie | Nie |
> | registries / generateCredentials | Nie | Nie |
> | registries / getBuildSourceUploadUrl | Nie | Nie |
> | registries / GetCredentials | Nie | Nie |
> | registries / importImage | Nie | Nie |
> | registries / queueBuild | Nie | Nie |
> | registries / regenerateCredential | Nie | Nie |
> | registries / regenerateCredentials | Nie | Nie |
> | registries / replications | Tak | Tak |
> | registries / runs | Nie | Nie |
> | registries / runs / cancel | Nie | Nie |
> | registries / scheduleRun | Nie | Nie |
> | registries / scopeMaps | Nie | Nie |
> | registries / taskRuns | Tak | Tak |
> | registries / tasks | Tak | Tak |
> | registries / tokens | Nie | Nie |
> | registries / updatePolicies | Nie | Nie |
> | registries / webhooks | Tak | Tak |
> | registries / webhooks / getCallbackConfig | Nie | Nie |
> | registries / webhooks / ping | Nie | Nie |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerServices | Tak | Tak |
> | managedClusters | Tak | Tak |
> | openShiftManagedClusters | Tak | Tak |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Alerty | Nie | Nie |
> | BillingAccounts | Nie | Nie |
> | Budżety | Nie | Nie |
> | CloudConnectors | Nie | Nie |
> | Konektory | Tak | Tak |
> | Departments | Nie | Nie |
> | Wymiary | Nie | Nie |
> | EnrollmentAccounts | Nie | Nie |
> | Eksporty | Nie | Nie |
> | ExternalBillingAccounts | Nie | Nie |
> | ExternalBillingAccounts / Alerts | Nie | Nie |
> | ExternalBillingAccounts / Dimensions | Nie | Nie |
> | ExternalBillingAccounts / Forecast | Nie | Nie |
> | ExternalBillingAccounts / Query | Nie | Nie |
> | ExternalSubscriptions | Nie | Nie |
> | ExternalSubscriptions / Alerts | Nie | Nie |
> | ExternalSubscriptions / Dimensions | Nie | Nie |
> | ExternalSubscriptions / Forecast | Nie | Nie |
> | ExternalSubscriptions / Query | Nie | Nie |
> | Forecast | Nie | Nie |
> | Zapytanie | Nie | Nie |
> | register | Nie | Nie |
> | Reportconfigs | Nie | Nie |
> | Raporty | Nie | Nie |
> | Ustawienia | Nie | Nie |
> | showbackRules | Nie | Nie |
> | Widoki | Nie | Nie |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | requests | Nie | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | associations | Nie | Nie |
> | resourceProviders | Tak | Tak |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Tak | Tak |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Tak | Tak |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Tak | Nie |
> | workspaces / virtualNetworkPeerings | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | catalogs | Tak | Tak |
> | datacatalogs | Tak | Tak |
> | datacatalogs / datasources | Nie | Nie |
> | datacatalogs / datasources / scans | Nie | Nie |
> | datacatalogs / datasources / scans / datasets | Nie | Nie |
> | datacatalogs / datasources / scans / triggers | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataFactories | Tak | Nie |
> | dataFactories / diagnosticSettings | Nie | Nie |
> | dataFactories / metricDefinitions | Nie | Nie |
> | dataFactorySchema | Nie | Nie |
> | factories | Tak | Nie |
> | factories / integrationRuntimes | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |
> | accounts / dataLakeStoreAccounts | Nie | Nie |
> | accounts / storageAccounts | Nie | Nie |
> | accounts / storageAccounts / containers | Nie | Nie |
> | accounts / transferAnalyticsUnits | Nie | Nie |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |
> | accounts / eventGridFilters | Nie | Nie |
> | accounts / firewallRules | Nie | Nie |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Nie | Nie |
> | services / projects | Nie | Nie |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |
> | accounts / shares | Nie | Nie |
> | accounts / shares / datasets | Nie | Nie |
> | accounts / shares / invitations | Nie | Nie |
> | accounts / shares / providersharesubscriptions | Nie | Nie |
> | accounts / shares / synchronizationSettings | Nie | Nie |
> | accounts / sharesubscriptions | Nie | Nie |
> | accounts / sharesubscriptions / consumerSourceDataSets | Nie | Nie |
> | accounts / sharesubscriptions / datasetmappings | Nie | Nie |
> | accounts / sharesubscriptions / triggers | Nie | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serwery | Tak | Tak |
> | servers / advisors | Nie | Nie |
> | servers / privateEndpointConnectionProxies | Nie | Nie |
> | servers / privateEndpointConnections | Nie | Nie |
> | servers / privateLinkResources | Nie | Nie |
> | servers / queryTexts | Nie | Nie |
> | servers / recoverableServers | Nie | Nie |
> | servers / topQueryStatistics | Nie | Nie |
> | servers / virtualNetworkRules | Nie | Nie |
> | servers / waitStatistics | Nie | Nie |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serwery | Tak | Tak |
> | servers / advisors | Nie | Nie |
> | servers / privateEndpointConnectionProxies | Nie | Nie |
> | servers / privateEndpointConnections | Nie | Nie |
> | servers / privateLinkResources | Nie | Nie |
> | servers / queryTexts | Nie | Nie |
> | servers / recoverableServers | Nie | Nie |
> | servers / topQueryStatistics | Nie | Nie |
> | servers / virtualNetworkRules | Nie | Nie |
> | servers / waitStatistics | Nie | Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serverGroups | Tak | Tak |
> | serwery | Tak | Tak |
> | servers / advisors | Nie | Nie |
> | servers / keys | Nie | Nie |
> | servers / privateEndpointConnectionProxies | Nie | Nie |
> | servers / privateEndpointConnections | Nie | Nie |
> | servers / privateLinkResources | Nie | Nie |
> | servers / queryTexts | Nie | Nie |
> | servers / recoverableServers | Nie | Nie |
> | servers / topQueryStatistics | Nie | Nie |
> | servers / virtualNetworkRules | Nie | Nie |
> | servers / waitStatistics | Nie | Nie |
> | serversv2 | Tak | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | artifactSources | Tak | Tak |
> | rollouts | Tak | Tak |
> | serviceTopologies | Tak | Tak |
> | serviceTopologies / services | Tak | Tak |
> | serviceTopologies / services / serviceUnits | Tak | Tak |
> | kroki | Tak | Tak |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Tak | Tak |
> | applicationgroups / applications | Nie | Nie |
> | applicationgroups / desktops | Nie | Nie |
> | applicationgroups / startmenuitems | Nie | Nie |
> | hostpools | Tak | Tak |
> | hostpools / sessionhosts | Nie | Nie |
> | hostpools / sessionhosts / usersessions | Nie | Nie |
> | hostpools / usersessions | Nie | Nie |
> | workspaces | Tak | Tak |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Tak | Tak |
> | ElasticPools / IotHubTenants | Tak | Tak |
> | IotHubs | Tak | Tak |
> | IotHubs / eventGridFilters | Nie | Nie |
> | ProvisioningServices | Tak | Tak |
> | usages | Nie | Nie |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | pipelines | Tak | Tak |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | controllers | Tak | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labcenters | Tak | Tak |
> | labs | Tak | Tak |
> | labs / environments | Tak | Tak |
> | labs / serviceRunners | Tak | Tak |
> | labs / virtualMachines | Tak | Tak |
> | schedules | Tak | Tak |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nie | Nie |
> | databaseAccounts | Tak | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Tak | Tak |
> | domains / domainOwnershipIdentifiers | Nie | Nie |
> | generateSsoRequest | Nie | Nie |
> | topLevelDomains | Nie | Nie |
> | validateDomainRegistrationInformation | Nie | Nie |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nie | Nie |
> | lcsprojects / clouddeployments | Nie | Nie |
> | lcsprojects / connectors | Nie | Nie |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Tak | Tak |
> | domains / topics | Nie | Nie |
> | eventSubscriptions | Nie | Nie |
> | extensionTopics | Nie | Nie |
> | topics | Tak | Tak |
> | topicTypes | Nie | Nie |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Tak | Tak |
> | namespaces | Tak | Tak |
> | namespaces / authorizationrules | Nie | Nie |
> | namespaces / disasterrecoveryconfigs | Nie | Nie |
> | namespaces / eventhubs | Nie | Nie |
> | namespaces / eventhubs / authorizationrules | Nie | Nie |
> | namespaces / eventhubs / consumergroups | Nie | Nie |
> | namespaces / networkrulesets | Nie | Nie |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | danych | Nie | Nie |
> | providers | Nie | Nie |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | enroll | Nie | Nie |
> | galleryitems | Nie | Nie |
> | generateartifactaccessuri | Nie | Nie |
> | myareas | Nie | Nie |
> | myareas / areas | Nie | Nie |
> | myareas / areas / areas | Nie | Nie |
> | myareas / areas / areas / galleryitems | Nie | Nie |
> | myareas / areas / galleryitems | Nie | Nie |
> | myareas / galleryitems | Nie | Nie |
> | register | Nie | Nie |
> | resources | Nie | Nie |
> | retrieveresourcesbyid | Nie | Nie |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | Nie | Nie |
> | guestConfigurationAssignments | Nie | Nie |
> | software | Nie | Nie |
> | softwareUpdateProfile | Nie | Nie |
> | softwareUpdates | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Tak | Tak |
> | sapMonitors | Tak | Tak |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Tak | Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Tak | Tak |
> | clusters / applications | Nie | Nie |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Tak | Tak |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | machines | Tak | Tak |
> | machines / extensions | Tak | Tak |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataManagers | Tak | Tak |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Tak | Tak |
> | networkScopes | Tak | Tak |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Tak | Tak |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nie | Nie |
> | diagnosticSettingsCategories | Nie | Nie |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nie | Nie |
> | IoTApps | Tak | Tak |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Graf | Tak | Tak |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nie | Nie |
> | hsmPools | Tak | Tak |
> | vaults | Tak | Tak |
> | vaults / accessPolicies | Nie | Nie |
> | vaults / eventGridFilters | Nie | Nie |
> | vaults / secrets | Nie | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Tak | Tak |
> | clusters / attacheddatabaseconfigurations | Nie | Nie |
> | clusters / databases | Nie | Nie |
> | clusters / databases / dataconnections | Nie | Nie |
> | clusters / databases / eventhubconnections | Nie | Nie |
> | clusters / sharedidentities | Nie | Nie |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labaccounts | Tak | Tak |
> | liczby użytkowników | Nie | Nie |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Tak | Tak |
> | integrationAccounts | Tak | Tak |
> | integrationServiceEnvironments | Tak | Tak |
> | integrationServiceEnvironments / managedApis | Tak | Tak |
> | isolatedEnvironments | Tak | Tak |
> | workflows | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Tak | Tak |
> | webServices | Tak | Tak |
> | Obszary robocze | Tak | Tak |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Tak | Tak |
> | workspaces / computes | Nie | Nie |
> | workspaces / eventGridFilters | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Identities | Nie | Nie |
> | userAssignedIdentities | Tak | Tak |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nie | Nie |
> | registrationAssignments | Nie | Nie |
> | registrationDefinitions | Nie | Nie |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | getEntities | Nie | Nie |
> | managementGroups | Nie | Nie |
> | resources | Nie | Nie |
> | startTenantBackfill | Nie | Nie |
> | tenantBackfillStatus | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |
> | accounts / eventGridFilters | Nie | Nie |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | offers | Nie | Nie |
> | offerTypes | Nie | Nie |
> | offerTypes / publishers | Nie | Nie |
> | offerTypes / publishers / offers | Nie | Nie |
> | offerTypes / publishers / offers / plans | Nie | Nie |
> | offerTypes / publishers / offers / plans / agreements | Nie | Nie |
> | offerTypes / publishers / offers / plans / configs | Nie | Nie |
> | offerTypes / publishers / offers / plans / configs / importImage | Nie | Nie |
> | privategalleryitems | Nie | Nie |
> | products | Nie | Nie |
> | publishers | Nie | Nie |
> | publishers / offers | Nie | Nie |
> | publishers / offers / amendments | Nie | Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Tak | Tak |
> | updateCommunicationPreference | Nie | Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | agreements | Nie | Nie |
> | offertypes | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mediaservices | Tak | Tak |
> | mediaservices / accountFilters | Nie | Nie |
> | mediaservices / assets | Nie | Nie |
> | mediaservices / assets / assetFilters | Nie | Nie |
> | mediaservices / contentKeyPolicies | Nie | Nie |
> | mediaservices / eventGridFilters | Nie | Nie |
> | mediaservices / liveEventOperations | Nie | Nie |
> | mediaservices / liveEvents | Tak | Tak |
> | mediaservices / liveEvents / liveOutputs | Nie | Nie |
> | mediaservices / liveOutputOperations | Nie | Nie |
> | mediaservices / mediaGraphs | Nie | Nie |
> | mediaservices / streamingEndpointOperations | Nie | Nie |
> | mediaservices / streamingEndpoints | Tak | Tak |
> | mediaservices / streamingLocators | Nie | Nie |
> | mediaservices / streamingPolicies | Nie | Nie |
> | mediaservices / transforms | Nie | Nie |
> | mediaservices / transforms / jobs | Nie | Nie |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appClusters | Tak | Tak |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Tak | Tak |
> | migrateprojects | Tak | Tak |
> | projects | Tak | Tak |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Tak | Tak |
> | objectUnderstandingAccounts | Tak | Tak |
> | remoteRenderingAccounts | Tak | Tak |
> | spatialAnchorsAccounts | Tak | Tak |
> | surfaceReconstructionAccounts | Tak | Tak |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Tak | Nie |
> | netAppAccounts / capacityPools | Tak | Nie |
> | netAppAccounts / capacityPools / volumes | Tak | Nie |
> | netAppAccounts / capacityPools / volumes / mountTargets | Tak | Nie |
> | netAppAccounts / capacityPools / volumes / snapshots | Tak | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Tak | Tak |
> | applicationGatewayWebApplicationFirewallPolicies | Tak | Tak |
> | applicationSecurityGroups | Tak | Tak |
> | azureFirewallFqdnTags | Nie | Nie |
> | azureFirewalls | Tak | Nie |
> | bastionHosts | Tak | Tak |
> | bgpServiceCommunities | Nie | Nie |
> | connections | Tak | Tak |
> | ddosCustomPolicies | Tak | Tak |
> | ddosProtectionPlans | Tak | Tak |
> | dnsOperationStatuses | Nie | Nie |
> | dnszones | Tak | Tak |
> | dnszones / A | Nie | Nie |
> | dnszones / AAAA | Nie | Nie |
> | dnszones / all | Nie | Nie |
> | dnszones / CAA | Nie | Nie |
> | dnszones / CNAME | Nie | Nie |
> | dnszones / MX | Nie | Nie |
> | dnszones / NS | Nie | Nie |
> | dnszones / PTR | Nie | Nie |
> | dnszones / recordsets | Nie | Nie |
> | dnszones / SOA | Nie | Nie |
> | dnszones / SRV | Nie | Nie |
> | dnszones / TXT | Nie | Nie |
> | expressRouteCircuits | Tak | Tak |
> | expressRouteCrossConnections | Tak | Tak |
> | expressRouteGateways | Tak | Tak |
> | expressRoutePorts | Tak | Tak |
> | expressRouteServiceProviders | Nie | Nie |
> | firewallPolicies | Tak | Tak |
> | frontdoors | Yes, but limited (see [note below](#frontdoor)) | Tak |
> | frontdoorWebApplicationFirewallManagedRuleSets | Yes, but limited (see [note below](#frontdoor)) | Nie |
> | frontdoorWebApplicationFirewallPolicies | Yes, but limited (see [note below](#frontdoor)) | Tak |
> | getDnsResourceReference | Nie | Nie |
> | internalNotify | Nie | Nie |
> | loadBalancers | Tak | Nie |
> | localNetworkGateways | Tak | Tak |
> | natGateways | Tak | Tak |
> | networkIntentPolicies | Tak | Tak |
> | networkInterfaces | Tak | Tak |
> | networkProfiles | Tak | Tak |
> | networkSecurityGroups | Tak | Tak |
> | networkWatchers | Tak | Nie |
> | networkWatchers / connectionMonitors | Tak | Nie |
> | networkWatchers / lenses | Tak | Nie |
> | networkWatchers / pingMeshes | Tak | Nie |
> | p2sVpnGateways | Tak | Tak |
> | privateDnsOperationStatuses | Nie | Nie |
> | privateDnsZones | Tak | Tak |
> | privateDnsZones / A | Nie | Nie |
> | privateDnsZones / AAAA | Nie | Nie |
> | privateDnsZones / all | Nie | Nie |
> | privateDnsZones / CNAME | Nie | Nie |
> | privateDnsZones / MX | Nie | Nie |
> | privateDnsZones / PTR | Nie | Nie |
> | privateDnsZones / SOA | Nie | Nie |
> | privateDnsZones / SRV | Nie | Nie |
> | privateDnsZones / TXT | Nie | Nie |
> | privateDnsZones / virtualNetworkLinks | Tak | Tak |
> | privateEndpoints | Tak | Tak |
> | privateLinkServices | Tak | Tak |
> | publicIPAddresses | Tak | Tak |
> | publicIPPrefixes | Tak | Tak |
> | routeFilters | Tak | Tak |
> | routeTables | Tak | Tak |
> | serviceEndpointPolicies | Tak | Tak |
> | trafficManagerGeographicHierarchies | Nie | Nie |
> | trafficmanagerprofiles | Tak | Tak |
> | trafficmanagerprofiles/heatMaps | Nie | Nie |
> | trafficManagerUserMetricsKeys | Nie | Nie |
> | virtualHubs | Tak | Tak |
> | virtualNetworkGateways | Tak | Tak |
> | virtualNetworks | Tak | Tak |
> | virtualNetworkTaps | Tak | Tak |
> | virtualWans | Tak | Tak |
> | vpnGateways | Tak | Nie |
> | vpnSites | Tak | Tak |
> | webApplicationFirewallPolicies | Tak | Tak |

<a id="frontdoor" />

> [!NOTE]
> For Azure Front Door Service, you can apply tags when creating the resource, but updating or adding tags is not currently supported.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Tak | Nie |
> | namespaces / notificationHubs | Tak | Nie |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Tak | Tak |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Tak | Tak |
> | ImportSites | Tak | Tak |
> | ServerSites | Tak | Tak |
> | VMwareSites | Tak | Tak |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Tak | Tak |
> | urządzenia | Nie | Nie |
> | linkTargets | Nie | Nie |
> | storageInsightConfigs | Nie | Nie |
> | workspaces | Tak | Tak |
> | workspaces / dataSources | Nie | Nie |
> | workspaces / linkedServices | Nie | Nie |
> | workspaces / query | Nie | Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nie | Nie |
> | managementconfigurations | Tak | Tak |
> | rozwiązania | Tak | Tak |
> | views | Tak | Tak |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nie | Nie |
> | peerAsns | Nie | Nie |
> | peerings | Tak | Tak |
> | peeringServiceProviders | Nie | Nie |
> | peeringServices | Tak | Tak |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | policyEvents | Nie | Nie |
> | policyMetadata | Nie | Nie |
> | policyStates | Nie | Nie |
> | policyTrackedResources | Nie | Nie |
> | remediations | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | consoles | Nie | Nie |
> | dashboards | Tak | Tak |
> | userSettings | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Tak | Tak |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capacities | Tak | Tak |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nie | Nie |
> | vaults | Tak | Tak |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Tak | Tak |
> | namespaces / authorizationrules | Nie | Nie |
> | namespaces / hybridconnections | Nie | Nie |
> | namespaces / hybridconnections / authorizationrules | Nie | Nie |
> | namespaces / wcfrelays | Nie | Nie |
> | namespaces / wcfrelays / authorizationrules | Nie | Nie |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Nie | Nie |
> | collections | Tak | Tak |
> | collections / applications | Nie | Nie |
> | collections / securityprincipals | Nie | Nie |
> | templateImages | Nie | Nie |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | — zapytania | Tak | Tak |
> | resourceChangeDetails | Nie | Nie |
> | resourceChanges | Nie | Nie |
> | resources | Nie | Nie |
> | resourcesHistory | Nie | Nie |
> | subscriptionsStatus | Nie | Nie |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nie | Nie |
> | childAvailabilityStatuses | Nie | Nie |
> | childResources | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | impactedResources | Nie | Nie |
> | metadane | Nie | Nie |
> | notifications | Nie | Nie |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deployments | Tak | Nie |
> | deployments / operations | Nie | Nie |
> | deploymentScripts | Tak | Tak |
> | deploymentScripts / logs | Nie | Nie |
> | links | Nie | Nie |
> | notifyResourceJobs | Nie | Nie |
> | providers | Nie | Nie |
> | resourceGroups | Tak | Nie |
> | subscriptions | Nie | Nie |
> | tenants | Nie | Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aplikacji | Tak | Tak |
> | saasresources | Nie | Nie |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobcollections | Tak | Tak |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nie | Nie |
> | searchServices | Tak | Tak |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nie | Nie |
> | advancedThreatProtectionSettings | Nie | Nie |
> | alerts | Nie | Nie |
> | allowedConnections | Nie | Nie |
> | applicationWhitelistings | Nie | Nie |
> | assessmentMetadata | Nie | Nie |
> | assessments | Nie | Nie |
> | autoDismissAlertsRules | Nie | Nie |
> | automations | Tak | Tak |
> | AutoProvisioningSettings | Nie | Nie |
> | Compliances | Nie | Nie |
> | dataCollectionAgents | Nie | Nie |
> | deviceSecurityGroups | Nie | Nie |
> | discoveredSecuritySolutions | Nie | Nie |
> | externalSecuritySolutions | Nie | Nie |
> | InformationProtectionPolicies | Nie | Nie |
> | iotSecuritySolutions | Tak | Tak |
> | iotSecuritySolutions / analyticsModels | Nie | Nie |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nie | Nie |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nie | Nie |
> | jitNetworkAccessPolicies | Nie | Nie |
> | networkData | Nie | Nie |
> | policies | Nie | Nie |
> | pricings | Nie | Nie |
> | regulatoryComplianceStandards | Nie | Nie |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nie | Nie |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nie | Nie |
> | securityContacts | Nie | Nie |
> | securitySolutions | Nie | Nie |
> | securitySolutionsReferenceData | Nie | Nie |
> | securityStatuses | Nie | Nie |
> | securityStatusesSummaries | Nie | Nie |
> | serverVulnerabilityAssessments | Nie | Nie |
> | settings | Nie | Nie |
> | subAssessments | Nie | Nie |
> | zadania | Nie | Nie |
> | topologies | Nie | Nie |
> | workspaceSettings | Nie | Nie |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nie | Nie |
> | diagnosticSettingsCategories | Nie | Nie |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aggregations | Nie | Nie |
> | alertRules | Nie | Nie |
> | alertRuleTemplates | Nie | Nie |
> | bookmarks | Nie | Nie |
> | cases | Nie | Nie |
> | dataConnectors | Nie | Nie |
> | entities | Nie | Nie |
> | entityQueries | Nie | Nie |
> | officeConsents | Nie | Nie |
> | settings | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Tak | Nie |
> | namespaces / authorizationrules | Nie | Nie |
> | namespaces / disasterrecoveryconfigs | Nie | Nie |
> | namespaces / eventgridfilters | Nie | Nie |
> | namespaces / networkrulesets | Nie | Nie |
> | namespaces / queues | Nie | Nie |
> | namespaces / queues / authorizationrules | Nie | Nie |
> | namespaces / topics | Nie | Nie |
> | namespaces / topics / authorizationrules | Nie | Nie |
> | namespaces / topics / subscriptions | Nie | Nie |
> | namespaces / topics / subscriptions / rules | Nie | Nie |
> | premiumMessagingRegions | Nie | Nie |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aplikacji | Tak | Tak |
> | clusters | Tak | Tak |
> | clusters / applications | Nie | Nie |
> | containerGroups | Tak | Tak |
> | containerGroupSets | Tak | Tak |
> | edgeclusters | Tak | Tak |
> | edgeclusters / applications | Nie | Nie |
> | networks | Tak | Tak |
> | secretstores | Tak | Tak |
> | secretstores / certificates | Nie | Nie |
> | secretstores / secrets | Nie | Nie |
> | volumes | Tak | Tak |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aplikacji | Tak | Tak |
> | containerGroups | Tak | Tak |
> | gateways | Tak | Tak |
> | networks | Tak | Tak |
> | wpisy tajne | Tak | Tak |
> | volumes | Tak | Tak |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nie | Nie |
> | providerRegistrations / resourceTypeRegistrations | Nie | Nie |
> | rollouts | Tak | Tak |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SignalR | Tak | Tak |
> | SignalR / eventGridFilters | Nie | Nie |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Tak | Tak |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nie | Nie |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Tak | Tak |
> | aplikacji | Tak | Tak |
> | jitRequests | Tak | Tak |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managedInstances | Tak | Tak |
> | managedInstances / databases | Nie | Nie |
> | managedInstances / databases / backupShortTermRetentionPolicies | Nie | Nie |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Nie | Nie |
> | managedInstances / databases / vulnerabilityAssessments | Nie | Nie |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Nie | Nie |
> | managedInstances / encryptionProtector | Nie | Nie |
> | managedInstances / keys | Nie | Nie |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nie | Nie |
> | managedInstances / vulnerabilityAssessments | Nie | Nie |
> | serwery | Tak | Tak |
> | servers / administrators | Nie | Nie |
> | servers / communicationLinks | Nie | Nie |
> | servers / databases | Yes (see [note below](#sqlnote)) | Tak |
> | servers / encryptionProtector | Nie | Nie |
> | servers / firewallRules | Nie | Nie |
> | servers / keys | Nie | Nie |
> | servers / restorableDroppedDatabases | Nie | Nie |
> | servers / serviceobjectives | Nie | Nie |
> | servers / tdeCertificates | Nie | Nie |
> | virtualClusters | Nie | Nie |

<a id="sqlnote" />

> [!NOTE]
> The Master database doesn't support tags, but other databases, including Azure SQL Data Warehouse databases, support tags. Azure SQL Data Warehouse databases must be in Active (not Paused) state.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Tak | Tak |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nie | Nie |
> | SqlVirtualMachines | Tak | Tak |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Tak | Tak |
> | storageAccounts / blobServices | Nie | Nie |
> | storageAccounts / fileServices | Nie | Nie |
> | storageAccounts / queueServices | Nie | Nie |
> | storageAccounts / services | Nie | Nie |
> | storageAccounts / services / metricDefinitions | Nie | Nie |
> | storageAccounts / tableServices | Nie | Nie |
> | usages | Nie | Nie |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | caches | Tak | Tak |
> | caches / storageTargets | Nie | Nie |
> | usageModels | Nie | Nie |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Tak | Tak |
> | storageSyncServices / registeredServers | Nie | Nie |
> | storageSyncServices / syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices / workflows | Nie | Nie |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Tak | Tak |
> | storageSyncServices / registeredServers | Nie | Nie |
> | storageSyncServices / syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices / workflows | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Tak | Tak |
> | storageSyncServices / registeredServers | Nie | Nie |
> | storageSyncServices / syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices / workflows | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managers | Tak | Tak |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Yes (see note below) | Tak |

> [!NOTE]
> You can't add a tag when streamingjobs is running. Stop the resource to add a tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | cancel | Nie | Nie |
> | CreateSubscription | Nie | Nie |
> | enable | Nie | Nie |
> | rename | Nie | Nie |
> | SubscriptionDefinitions | Nie | Nie |
> | SubscriptionOperations | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Tak | Nie |
> | environments / accessPolicies | Nie | Nie |
> | environments / eventsources | Tak | Nie |
> | environments / referenceDataSets | Tak | Nie |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Tak | Tak |
> | dedicatedCloudServices | Tak | Tak |
> | virtualMachines | Tak | Tak |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nie | Nie |
> | apiManagementAccounts / apiAcls | Nie | Nie |
> | apiManagementAccounts / apis | Nie | Nie |
> | apiManagementAccounts / apis / apiAcls | Nie | Nie |
> | apiManagementAccounts / apis / connectionAcls | Nie | Nie |
> | apiManagementAccounts / apis / connections | Nie | Nie |
> | apiManagementAccounts / apis / connections / connectionAcls | Nie | Nie |
> | apiManagementAccounts / apis / localizedDefinitions | Nie | Nie |
> | apiManagementAccounts / connectionAcls | Nie | Nie |
> | apiManagementAccounts / connections | Nie | Nie |
> | billingMeters | Nie | Nie |
> | certificates | Tak | Tak |
> | connectionGateways | Tak | Tak |
> | connections | Tak | Tak |
> | customApis | Tak | Tak |
> | deletedSites | Nie | Nie |
> | functions | Nie | Nie |
> | hostingEnvironments | Tak | Tak |
> | hostingEnvironments / multiRolePools | Nie | Nie |
> | hostingEnvironments / workerPools | Nie | Nie |
> | publishingUsers | Nie | Nie |
> | recommendations | Nie | Nie |
> | resourceHealthMetadata | Nie | Nie |
> | runtimes | Nie | Nie |
> | serverFarms | Tak | Tak |
> | serverFarms / eventGridFilters | Nie | Nie |
> | sites | Tak | Tak |
> | sites / config  | Nie | Nie |
> | sites / eventGridFilters | Nie | Nie |
> | sites / hostNameBindings | Nie | Nie |
> | sites / networkConfig | Nie | Nie |
> | sites / premieraddons | Tak | Tak |
> | sites / slots | Tak | Tak |
> | sites / slots / eventGridFilters | Nie | Nie |
> | sites / slots / hostNameBindings | Nie | Nie |
> | sites / slots / networkConfig | Nie | Nie |
> | sourceControls | Nie | Nie |
> | validate | Nie | Nie |
> | verifyHostingEnvironmentVnet | Nie | Nie |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nie | Nie |
> | diagnosticSettingsCategories | Nie | Nie |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Tak | Tak |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Nie | Nie |
> | componentsSummary | Nie | Nie |
> | monitorInstances | Nie | Nie |
> | monitorInstancesSummary | Nie | Nie |
> | monitors | Nie | Nie |
> | notificationSettings | Nie | Nie |

## <a name="next-steps"></a>Następne kroki

To learn how to apply tags to resources, see [Use tags to organize your Azure resources](resource-group-using-tags.md).
