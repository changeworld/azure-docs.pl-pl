---
title: Obsługa tagów dla zasobów
description: Pokazuje, które typy zasobów platformy Azure obsługują tagi. Zawiera szczegółowe informacje dotyczące wszystkich usług platformy Azure.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: c971d3af102faf99f97aac261882797460d71f37
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255031"
---
# <a name="tag-support-for-azure-resources"></a>Obsługa tagów dla zasobów platformy Azure
W tym artykule opisano, czy typ zasobu obsługuje [znaczniki](tag-resources.md). Kolumna oznaczona **tagami Obsługuje** wskazuje, czy typ zasobu ma właściwość dla tagu. Kolumna oznaczona etykietą **Znacznik w raporcie kosztów** wskazuje, czy ten typ zasobu przekazuje znacznik do raportu kosztów. Koszty można wyświetlać według tagów w [analizie kosztów zarządzania kosztami](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) oraz [fakturach rozliczeniowych platformy Azure i danych dotyczących dziennego użycia](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Aby uzyskać te same dane co plik wartości oddzielonych przecinkami, pobierz [plik tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Przechodzenie do obszaru nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft.aAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Usługa Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Doradca firmy Microsoft](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Konfiguracja aplikacji Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Platforma Microsoft.AppPlatform](#microsoftappplatform)
> - [Zaświadczenie firmy Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Automatyzacja firmy Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Genewa](#microsoftazuregeneva)
> - [Program Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Witryna Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Rozliczenia firmy Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Łańcuch bloków firmy Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Plan firmy Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Pamięć podręczna firmy Microsoft.Cache](#microsoftcache)
> - [Pojemność firmy Microsoft](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Rejestracja certyfikatów firmy Microsoft.Certificate](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Program Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Usługa Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Zużycie przez firmę Microsoft](#microsoftconsumption)
> - [Insstancja kontenera firmy Microsoft](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Usługa kontenera firmy Microsoft.ContainerService](#microsoftcontainerservice)
> - [Zarządzanie kosztami firmy Microsoft](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox (Skrzynka z blokadą klienta firmy Microsoft)](#microsoftcustomerlockbox)
> - [Usługi niestandardowe firmy Microsoft.Custom](#microsoftcustomproviders)
> - [Skrzynka z danymi firmy Microsoft](#microsoftdatabox)
> - [Plik Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Baza danych Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopWirtualizacja](#microsoftdesktopvirtualization)
> - [Urządzenia firmy Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Baza danych microsoft.documentdb](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Galeria Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Konfiguracja witryny Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridDana](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportEksport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Centrum IoT Firmy Microsoft](#microsoftiotcentral)
> - [Przestrzenie IoT firmy Microsoft](#microsoftiotspaces)
> - [Usługa Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Logika firmy Microsoft.Logic](#microsoftlogic)
> - [Uczenie się maszyn firmy Microsoft.Machine](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Usługi zarządzane przez firmę Microsoft.Managed](#microsoftmanagedservices)
> - [Zarządzanie microsoftem](#microsoftmanagement)
> - [Microsoft.Maps (Mapy firmy Microsoft)](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality (Reality mieszane firmy Microsoft.MixedReality)](#microsoftmixedreality)
> - [Microsoft.NetApp (Microsoft.NetApp)](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Notesy firmy Microsoft.Notess](#microsoftnotebooks)
> - [Witryna Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Sklep Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Dane operacyjne firmy Microsoft.Operational](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Komunikacja równorzędna firmy Microsoft](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Program Microsoft.PowerBI](#microsoftpowerbi)
> - [Program Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Usługi Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Aplikacja Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Zasoby firmy Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Wyszukiwanie microsoft.search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph (Microsoft.SecurityGraph)](#microsoftsecuritygraph)
> - [Witryny zabezpieczeń firmy Microsoft.Security](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Usługa Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Rozwiązania firmy Microsoft.Solutions](#microsoftsolutions)
> - [Usługa Microsoft.SpoolService](#microsoftspoolservice)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Usługa Microsoft.StorageCache](#microsoftstoragecache)
> - [Aplikacja Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [System Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Program Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalityka](#microsoftstreamanalytics)
> - [Subskrypcja firmy Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Menedżer microsoft.Vnf](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.aAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Usługi w domenie | Tak | Tak |
> | Usługi w domenie / oucontainer | Nie | Nie |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | wsparcieDarzy | Nie | Nie |

## <a name="microsoftadhybridhealthservice"></a>Usługa Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nie | Nie |
> | dodaje usługi | Nie | Nie |
> | Agentów | Nie | Nie |
> | anonimowoapiusers | Nie | Nie |
> | konfiguracja | Nie | Nie |
> | dzienniki | Nie | Nie |
> | reports | Nie | Nie |
> | servicehealthmetrics | Nie | Nie |
> | services | Nie | Nie |

## <a name="microsoftadvisor"></a>Doradca firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konfiguracji | Nie | Nie |
> | generowaniezamiasje | Nie | Nie |
> | metadane | Nie | Nie |
> | zalecenia | Nie | Nie |
> | Suppressions | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | actionRules (Zasady działania) | Tak | Tak |
> | alerts | Nie | Nie |
> | lista alertów | Nie | Nie |
> | alertyMetaData | Nie | Nie |
> | alertySummary | Nie | Nie |
> | alertsSummaryList | Nie | Nie |
> | zasady inteligentnego FiltraAlert | Tak | Tak |
> | grupy smartGroups | Nie | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.analysisservices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Serwerów | Tak | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nie | Nie |
> | usługa | Tak | Tak |
> | validateServiceName (nazwa usługi) | Nie | Nie |

## <a name="microsoftappconfiguration"></a>Konfiguracja aplikacji Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | konfiguracjaSklepy | Tak | Tak |
> | konfiguracjaSklepy / eventGridFilters | Nie | Nie |

## <a name="microsoftappplatform"></a>Platforma Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Spring | Tak | Tak |

## <a name="microsoftattestation"></a>Zaświadczenie firmy Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | zaświadczenieProvidery | Nie | Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Nie | Nie |
> | dataAliases (łańcuchy danych) | Nie | Nie |
> | denyAssignments (odmowa) | Nie | Nie |
> | elevateAccess | Nie | Nie |
> | znajdźOrphanRoleZasypisy | Nie | Nie |
> | Blokad | Nie | Nie |
> | Uprawnienia | Nie | Nie |
> | zmiany zasadAssignments | Nie | Nie |
> | Policydefinitions | Nie | Nie |
> | zasadySetDefinitions | Nie | Nie |
> | providerOperations | Nie | Nie |
> | roleAssignments | Nie | Nie |
> | roleAssignmentsUsageMetrics | Nie | Nie |
> | definicje ról | Nie | Nie |

## <a name="microsoftautomation"></a>Automatyzacja firmy Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | automationKonta | Tak | Tak |
> | automationKony / konfiguracje | Tak | Tak |
> | automationKonta / oferty pracy | Nie | Nie |
> | automationKonta / privateEndpointConnectionProxies | Nie | Nie |
> | automationKonta / privateEndpointConnections | Nie | Nie |
> | automationKonta / privateLinkResources | Nie | Nie |
> | automationKonty / elementy runbook | Tak | Tak |
> | automationKonta / oprogramowanieKonfiguracje aktualizacji | Nie | Nie |
> | automationKony / elementy elementów webhook | Nie | Nie |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | konfiguracjaSklepy | Tak | Tak |
> | konfiguracjaSklepy / eventGridFilters | Nie | Nie |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Genewa

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Środowiskach | Nie | Nie |
> | środowiska / konta | Nie | Nie |
> | środowiska / konta / przestrzenie nazw | Nie | Nie |
> | środowiska / konta / przestrzenie nazw / konfiguracje | Nie | Nie |

## <a name="microsoftazureactivedirectory"></a>Program Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | b2cKatory | Tak | Nie |
> | b2ctenants | Nie | Nie |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Tak | Tak |
> | PostgresInstances (PostgresInstances) | Tak | Tak |
> | SqlInstances (Instancje sql) | Tak | Tak |
> | Sqlserverregistrations | Tak | Tak |
> | sqlServerRegistrations / sqlServers | Nie | Nie |

## <a name="microsoftazurestack"></a>Witryna Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Nie | Nie |
> | Rejestracji | Tak | Tak |
> | rejestracje / customerSubscriptions | Nie | Nie |
> | rejestracje / produkty | Nie | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | konta batchAccounts | Tak | Tak |

## <a name="microsoftbilling"></a>Rozliczenia firmy Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | rozliczeniaKonta | Nie | Nie |
> | rozliczeniaKonta / umowy | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaPeriedje | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / billingPermissions | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / rozliczeniaRoleZapisy | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / billingRoleDefinitions | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySubskrypcje | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / tworzenieBillingRoleAssignment | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / klienci | Nie | Nie |
> | billingKonta / rozliczeniaProfile / instrukcje | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / faktury | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / faktury / arkusz cen | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / faktury / transakcje | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWysekcje / rozliczeniaWypusty | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje / rozliczeniaRoleAssignments | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWykreślenia / rozliczeniaRoleDefinitions | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWysekcje / fakturySubskrypty | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje / tworzenieBillingRoleAssignment | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWysekcje / inicjowanieTransfer | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje / produkty | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje / produkty / transfer | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje / produkty / aktualizacjaAutoRenew | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWysekcje / transakcje | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWysekcje / przelewy | Nie | Nie |
> | rozliczeniaKonta / RozliczeniaProfile / patchOperacje | Nie | Nie |
> | billingKonta / rozliczeniaProfile / paymentMetody | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / zasady | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / arkusz cen | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / pricesheetDownloadOperations | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / produkty | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / transakcje | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaZapisyrolasy | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaRoleDefinitions | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaSubskrypcje | Nie | Nie |
> | rozliczeniaKonta / rozliczeniaSubskrypcje / faktury | Nie | Nie |
> | rozliczeniaKonta / tworzenieZaliczanie | Nie | Nie |
> | rozliczeniaKonta / tworzenieWykonanie fakturOperacje | Nie | Nie |
> | rozliczeniaKonta / klienci | Nie | Nie |
> | rozliczeniaKonta / klienci / rozliczeniaPeriedje | Nie | Nie |
> | rozliczeniaKonta / klienci / fakturySubskrypcje | Nie | Nie |
> | rozliczeniaKonta / klienci / inicjująTransfer | Nie | Nie |
> | rozliczeniaKonta / klienci / zasady | Nie | Nie |
> | rozliczeniaKonta / klienci / produkty | Nie | Nie |
> | rozliczeniaKonta / klienci / transakcje | Nie | Nie |
> | rozliczeniaKonta / klienci / przelewy | Nie | Nie |
> | rozliczeniaKonta / działy | Nie | Nie |
> | rozliczeniaKonta / rejestracjaKonta ścięcie | Nie | Nie |
> | rozliczeniaKonta / faktury | Nie | Nie |
> | rozliczeniaKonta / fakturaSekcje | Nie | Nie |
> | rozliczeniaKonta / fakturaSekcje / billingSubscriptionMoveOperations | Nie | Nie |
> | rozliczeniaKonta / fakturySekcje / fakturySubskrypcje | Nie | Nie |
> | rozliczeniaKonta / fakturySekcje / fakturySubskrypcje / przelew | Nie | Nie |
> | rozliczeniaKonta / fakturaSekcje / podniesienie wysokości | Nie | Nie |
> | rozliczeniaKonta / fakturySekcje / inicjująTransfer | Nie | Nie |
> | rozliczeniaKonta / fakturaSekcje / patchOperacje | Nie | Nie |
> | rozliczeniaKonta / fakturaSekcje / productMoveOperations | Nie | Nie |
> | rozliczeniaKonta / fakturySekcje / produkty | Nie | Nie |
> | rozliczeniaKonta / fakturySekcje / produkty / transfer | Nie | Nie |
> | rozliczeniaKonta / fakturaSekcje / produkty / aktualizacjaAutoRenew | Nie | Nie |
> | rozliczeniaKonta / fakturySekcje / transakcje | Nie | Nie |
> | rozliczeniaKonta / fakturySekcje / przelewy | Nie | Nie |
> | rozliczeniaKonta / wierszeKredyt | Nie | Nie |
> | rozliczeniaKonta / patchOperacje | Nie | Nie |
> | rozliczeniaKonta / płatnośćMetody | Nie | Nie |
> | rozliczeniaKonta / produkty | Nie | Nie |
> | rozliczeniaKonta / transakcje | Nie | Nie |
> | okresy rozliczeniowe | Nie | Nie |
> | rozliczeniaWydarzenia | Nie | Nie |
> | billingNierowadstwo | Nie | Nie |
> | rozliczeniaRoleZasyty | Nie | Nie |
> | rozliczeniaZapomnianiarol | Nie | Nie |
> | tworzenieZastawieniaRolassignment | Nie | Nie |
> | Działów | Nie | Nie |
> | enrollmentKonta | Nie | Nie |
> | Faktury | Nie | Nie |
> | Transfery | Nie | Nie |
> | transfery / acceptTransfer | Nie | Nie |
> | transfery / spadekTransfer | Nie | Nie |
> | transfery / operacjaStatus | Nie | Nie |
> | transfery / validateTransfer | Nie | Nie |
> | validateAddress | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | mapaApis | Tak | Tak |
> | updateCommunicationPreference | Nie | Nie |

## <a name="microsoftblockchain"></a>Łańcuch bloków firmy Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | członkowie łańcucha bloków | Tak | Tak |
> | cordaPoczęcie | Tak | Tak |
> | Obserwatorów | Tak | Tak |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Usługi TokenServices | Tak | Tak |
> | TokenServices / BlockchainNetworks | Nie | Nie |
> | TokenServices / Grupy | Nie | Nie |
> | TokenServices / Grupy / Konta | Nie | Nie |
> | TokenServices / TokenTemplates | Nie | Nie |

## <a name="microsoftblueprint"></a>Plan firmy Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | planyAsigny | Nie | Nie |
> | planySpisy / przydziałOperacje | Nie | Nie |
> | planyAssignments / operacje | Nie | Nie |
> | Plany | Nie | Nie |
> | plany / artefakty | Nie | Nie |
> | plany / wersje | Nie | Nie |
> | plany / wersje / artefakty | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | botUsługi | Tak | Tak |
> | botSługi / kanały | Nie | Nie |
> | botSługi / połączenia | Nie | Nie |
> | języki | Nie | Nie |
> | szablonów | Nie | Nie |

## <a name="microsoftcache"></a>Pamięć podręczna firmy Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Redis | Tak | Tak |

## <a name="microsoftcapacity"></a>Pojemność firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | appliedReservations (Rezerwacje) | Nie | Nie |
> | autoQuotaIncrease | Nie | Nie |
> | CalculateExchange (Zmiany obliczania) | Nie | Nie |
> | obliczcena | Nie | Nie |
> | obliczaćzakcena z zakupem | Nie | Nie |
> | Katalogi | Nie | Nie |
> | reklamaReservationOrders | Nie | Nie |
> | Exchange | Nie | Nie |
> | placeZakrzzakędowy zakup | Nie | Nie |
> | rezerwacja Ramiarki | Nie | Nie |
> | rezerwacjaOrders / calculateRefund | Nie | Nie |
> | rezerwacja Ramiony / scalanie | Nie | Nie |
> | rezerwacjaDery / rezerwacje | Nie | Nie |
> | rezerwacjaOrdery / rezerwacje / rewizje | Nie | Nie |
> | rezerwacja Ramiarki / zwrot | Nie | Nie |
> | rezerwacjaOrders / split | Nie | Nie |
> | rezerwacja Ramiarki / swap | Nie | Nie |
> | Rezerwacje | Nie | Nie |
> | resourceProviders (Dostawcy zasobów) | Nie | Nie |
> | zasoby | Nie | Nie |
> | validateReservationOrder | Nie | Nie |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Zestawy CdnWebApplicationFirewallManagedRuleSets | Nie | Nie |
> | CdnWebApplicationFirewallPolicies | Tak | Tak |
> | edgenodes (własno | Nie | Nie |
> | Profile | Tak | Tak |
> | profile / punkty końcowe | Tak | Tak |
> | profile / punkty końcowe / customdoains | Nie | Nie |
> | profile / punkty końcowe / grupy origin | Nie | Nie |
> | profile / punkty końcowe / początki | Nie | Nie |
> | validateProbe (validateProbe) | Nie | Nie |

## <a name="microsoftcertificateregistration"></a>Rejestracja certyfikatów firmy Microsoft.Certificate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | CertyfikatY | Tak | Tak |
> | certyfikatyOrdery / certyfikaty | Nie | Nie |
> | validateCertificateRegistrationInformation | Nie | Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Możliwości | Nie | Nie |
> | nazwy domen | Nie | Nie |
> | domainNames / możliwości | Nie | Nie |
> | domainNames / internalLoadBalancers | Nie | Nie |
> | domainNames / serviceCertyfikaty | Nie | Nie |
> | domainNames / sloty | Nie | Nie |
> | domainNames / sloty / role | Nie | Nie |
> | domainNames / sloty / role / metrykaDefinitions | Nie | Nie |
> | domainNames / sloty / role / metryki | Nie | Nie |
> | moveSubscriptionResources | Nie | Nie |
> | operacyjnySystemFamilies | Nie | Nie |
> | operatingSystems (systemy operacyjne) | Nie | Nie |
> | quotas | Nie | Nie |
> | typy zasobów | Nie | Nie |
> | validateSubscriptionMoveAvailability | Nie | Nie |
> | wirtualnematyny | Nie | Nie |
> | virtualMachines / diagnostykaStawienie | Nie | Nie |
> | virtualMachines / metrykaDefinicje | Nie | Nie |
> | virtualMachines / metryki | Nie | Nie |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureZaskładnia | Nie | Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Możliwości | Nie | Nie |
> | expressRouteCrossConnections (Połączenie ekspresowe) | Nie | Nie |
> | expressRouteCrossConnections / peerings | Nie | Nie |
> | gatewaySupportedDevices | Nie | Nie |
> | networkSecurityGroups | Nie | Nie |
> | quotas | Nie | Nie |
> | reservedIps | Nie | Nie |
> | wirtualne sieci | Nie | Nie |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nie | Nie |
> | virtualNetworks / virtualNetworkPeerings | Nie | Nie |

## <a name="microsoftclassicstorage"></a>Program Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Możliwości | Nie | Nie |
> | Dysków | Nie | Nie |
> | images | Nie | Nie |
> | osImages (Zdjęcia) | Nie | Nie |
> | osPlatformImages (Plucie) | Nie | Nie |
> | publiczneMatygi | Nie | Nie |
> | quotas | Nie | Nie |
> | konta przechowywania | Nie | Nie |
> | storageKonta / blobSługi | Nie | Nie |
> | storageKonta / fileServices | Nie | Nie |
> | storageKonta / metryKadefinicje | Nie | Nie |
> | storageKonta / metryki | Nie | Nie |
> | storageKonta / kolejkiUsługi | Nie | Nie |
> | przechowywanieKonta / usługi | Nie | Nie |
> | storageKonta / usługi / diagnostykaZakzywnie | Nie | Nie |
> | storageKonta / usługi / metryKadefunkowania | Nie | Nie |
> | storageKonta / usługi / metryki | Nie | Nie |
> | storageKonta / tabelaUsługi | Nie | Nie |
> | przechowywanieKonta / vmImages | Nie | Nie |
> | vmImages (zdjęcia wirtualne) | Nie | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |

## <a name="microsoftcommerce"></a>Usługa Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | RateCard | Nie | Nie |
> | UsageAggregates (Agregates) | Nie | Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Tak | Tak |
> | zestawy descryption | Tak | Tak |
> | Dysków | Tak | Tak |
> | Galerie | Tak | Tak |
> | galerie / aplikacje | Nie | Nie |
> | galerie / aplikacje / wersje | Nie | Nie |
> | galerie / zdjęcia | Nie | Nie |
> | galerie / obrazy / wersje | Nie | Nie |
> | grupy hostów | Tak | Tak |
> | hostGrupy / hosty | Tak | Tak |
> | images | Tak | Tak |
> | proximityPlacementGrupy | Tak | Tak |
> | restorePointCollections (RestorePointCollections) | Tak | Tak |
> | restorePointCollections / restorePoints | Nie | Nie |
> | sharedVMWysekcje | Tak | Tak |
> | sharedVMWysekcje / wersje | Nie | Nie |
> | sharedVMImages | Tak | Tak |
> | sharedVMImages / wersje | Nie | Nie |
> | Migawki | Tak | Tak |
> | sshPublicKeys (Klucze publiczne) | Tak | Tak |
> | wirtualnematyny | Tak | Tak |
> | virtualMachines / rozszerzenia | Tak | Tak |
> | virtualMachines / metrykaDefinicje | Nie | Nie |
> | zestawy virtualMachineScaleSets | Tak | Tak |
> | virtualMachineScaleSets / rozszerzenia | Nie | Nie |
> | virtualMachineScaleSets / siećIntertwy | Nie | Nie |
> | virtualMachineScaleSets / publicIPAddresses | Nie | Nie |
> | virtualMachineScaleSets / virtualMachines | Nie | Nie |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nie | Nie |

## <a name="microsoftconsumption"></a>Zużycie przez firmę Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Zagregowana pochówek | Nie | Nie |
> | Salda | Nie | Nie |
> | Budżety | Nie | Nie |
> | Opłaty | Nie | Nie |
> | Znaczniki kosztów | Nie | Nie |
> | Kredyty | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | Prognozy | Nie | Nie |
> | Wiele | Nie | Nie |
> | Rynkach | Nie | Nie |
> | Arkusze cen | Nie | Nie |
> | Produktów | Nie | Nie |
> | Rezerwacjeszczetółki | Nie | Nie |
> | RezerwacjeZamieczenia | Nie | Nie |
> | RezerwacjeSummaries | Nie | Nie |
> | RezerwacjeTransakcje | Nie | Nie |
> | Tagi | Nie | Nie |
> | Najemców | Nie | Nie |
> | Warunki | Nie | Nie |
> | Szczegóły użycia | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Insstancja kontenera firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | kontenerGrupy | Tak | Tak |
> | serwisAssociationLinks | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Rejestrów | Tak | Tak |
> | rejestry / agentPools | Tak | Tak |
> | rejestry / buduje | Nie | Nie |
> | rejestry / buduje / anulują | Nie | Nie |
> | rejestry / buduje / getLogLink | Nie | Nie |
> | rejestry / budowanieZadań | Tak | Tak |
> | rejestry / buildTasks / kroki | Nie | Nie |
> | rejestry / eventGridFilters | Nie | Nie |
> | rejestry / generateCredentials | Nie | Nie |
> | rejestry / getBuildSourceUploadUrl | Nie | Nie |
> | rejestry / GetCredentials | Nie | Nie |
> | rejestry / importImage | Nie | Nie |
> | rejestry / privateEndpointConnectionProxies | Nie | Nie |
> | rejestry / privateEndpointConnectionProxies / validate | Nie | Nie |
> | rejestry / privateEndpointConnections | Nie | Nie |
> | rejestry / privateLinkResources | Nie | Nie |
> | rejestry / queueBuild | Nie | Nie |
> | rejestry / regenerateCredential | Nie | Nie |
> | rejestry / regenerateCredentials | Nie | Nie |
> | rejestry / replikacje | Tak | Tak |
> | rejestry / biegi | Nie | Nie |
> | rejestry / biegi / anulować | Nie | Nie |
> | rejestry / harmonogramRun | Nie | Nie |
> | rejestry / zakresMapy | Nie | Nie |
> | rejestry / taskRuns | Tak | Tak |
> | rejestry / zadania | Tak | Tak |
> | rejestry / żetony | Nie | Nie |
> | rejestry / aktualizacjaPolityka | Nie | Nie |
> | rejestry / hook | Tak | Tak |
> | rejestry / webhooks / getCallbackConfig | Nie | Nie |
> | rejestry / webhooks / ping | Nie | Nie |

## <a name="microsoftcontainerservice"></a>Usługa kontenera firmy Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | kontenerSługi | Tak | Tak |
> | zarządzaneKlustery | Tak | Tak |
> | openShiftManagedClusters | Tak | Tak |

## <a name="microsoftcostmanagement"></a>Zarządzanie kosztami firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Alerty | Nie | Nie |
> | Rozliczeniakonta | Nie | Nie |
> | Budżety | Nie | Nie |
> | Połączenia w chmurze | Nie | Nie |
> | Łączniki | Tak | Tak |
> | Działy | Nie | Nie |
> | Wymiary | Nie | Nie |
> | Konta enrollmentaccounts | Nie | Nie |
> | Wywozu | Nie | Nie |
> | Zewnętrzne rachunki billingowe | Nie | Nie |
> | Zewnętrzne Rachunki / Alerty | Nie | Nie |
> | Zewnętrzne Rachunki / Wymiary | Nie | Nie |
> | Zewnętrzne Rachunki / Prognoza | Nie | Nie |
> | Zewnętrzne rachunki / kwerenda | Nie | Nie |
> | Zewnętrznesubskrypcje | Nie | Nie |
> | ZewnętrzneSubskrypcje / Alerty | Nie | Nie |
> | Zewnętrznesubskrypcje / wymiary | Nie | Nie |
> | Zewnętrznesubskrypcje / Prognoza | Nie | Nie |
> | Zewnętrznesubskrypcje / Zapytanie | Nie | Nie |
> | Prognoza | Nie | Nie |
> | Zapytanie | Nie | Nie |
> | zarejestruj | Nie | Nie |
> | Reportconfigs (Reportconfigs) | Nie | Nie |
> | Raporty | Nie | Nie |
> | Ustawienia | Nie | Nie |
> | showbackRules (reguły showback) | Nie | Nie |
> | Widoki | Nie | Nie |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox (Skrzynka z blokadą klienta firmy Microsoft)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Żądania | Nie | Nie |

## <a name="microsoftcustomproviders"></a>Usługi niestandardowe firmy Microsoft.Custom

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Stowarzyszenia | Nie | Nie |
> | resourceProviders (Dostawcy zasobów) | Tak | Tak |

## <a name="microsoftdatabox"></a>Skrzynka z danymi firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Zadania | Tak | Tak |

## <a name="microsoftdataboxedge"></a>Plik Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeUrządzenia | Tak | Tak |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | obszary robocze | Tak | Nie |
> | obszary robocze / dbPrzesienia robocze | Nie | Nie |
> | obszary robocze / magazynSzyfracja | Nie | Nie |
> | obszary robocze / virtualNetworkPeerings | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Katalogi | Tak | Tak |
> | datacatalogs (przetwarzanie danych) | Tak | Tak |
> | datacatalogs / datasources | Nie | Nie |
> | datacatalogs / datasources / skany | Nie | Nie |
> | datacatalogs / datasources / skany / zestawy danych | Nie | Nie |
> | datacatalogs / datasources / skany / wyzwalacze | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | daneFactory | Tak | Nie |
> | dataFactory / diagnostykaStawienie | Nie | Nie |
> | dataFactory / metryKadefinicje | Nie | Nie |
> | dataFactorySchema | Nie | Nie |
> | Fabryk | Tak | Nie |
> | fabryki / integracjaRuntimes | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |
> | konta / dataLakeStoreKony | Nie | Nie |
> | konta / magazynKonta | Nie | Nie |
> | konta / przechowywanieKonta / kontenery | Nie | Nie |
> | konta / transferAnalyticsJedny | Nie | Nie |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |
> | konta / eventGridFilters | Nie | Nie |
> | konta / firewallRules | Nie | Nie |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | services | Nie | Nie |
> | usługi / projekty | Nie | Nie |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |
> | rachunki / udziały | Nie | Nie |
> | rachunki / udziały / zbiory danych | Nie | Nie |
> | rachunki / akcje / zaproszenia | Nie | Nie |
> | rachunki / akcje / dostawcysharesubscriptions | Nie | Nie |
> | rachunki / udziały / synchronizacjaSwilki | Nie | Nie |
> | rachunki / sharesubscriptions | Nie | Nie |
> | konta / sharesubscriptions / consumerSourceDataSets | Nie | Nie |
> | rachunki / sharesubscriptions / mapy zestawów danych | Nie | Nie |
> | rachunki / sharesubscriptions / wyzwalacze | Nie | Nie |

## <a name="microsoftdbformariadb"></a>Baza danych Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Serwerów | Tak | Tak |
> | serwery / doradcy | Nie | Nie |
> | serwery / klucze | Nie | Nie |
> | serwery / privateEndpointConnectionProxies | Nie | Nie |
> | serwery / privateEndpointConnections | Nie | Nie |
> | serwery / privateLinkResources | Nie | Nie |
> | serwery / queryTekty | Nie | Nie |
> | serwery /odzyskiwalneSerwery | Nie | Nie |
> | serwery / topQueryStatystyka | Nie | Nie |
> | serwery / virtualNetworkRules | Nie | Nie |
> | serwery / waitStatistics | Nie | Nie |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Serwerów | Tak | Tak |
> | serwery / doradcy | Nie | Nie |
> | serwery / klucze | Nie | Nie |
> | serwery / privateEndpointConnectionProxies | Nie | Nie |
> | serwery / privateEndpointConnections | Nie | Nie |
> | serwery / privateLinkResources | Nie | Nie |
> | serwery / queryTekty | Nie | Nie |
> | serwery /odzyskiwalneSerwery | Nie | Nie |
> | serwery / topQueryStatystyka | Nie | Nie |
> | serwery / virtualNetworkRules | Nie | Nie |
> | serwery / waitStatistics | Nie | Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Servergroups | Tak | Tak |
> | Serwerów | Tak | Tak |
> | serwery / doradcy | Nie | Nie |
> | serwery / klucze | Nie | Nie |
> | serwery / privateEndpointConnectionProxies | Nie | Nie |
> | serwery / privateEndpointConnections | Nie | Nie |
> | serwery / privateLinkResources | Nie | Nie |
> | serwery / queryTekty | Nie | Nie |
> | serwery /odzyskiwalneSerwery | Nie | Nie |
> | serwery / topQueryStatystyka | Nie | Nie |
> | serwery / virtualNetworkRules | Nie | Nie |
> | serwery / waitStatistics | Nie | Nie |
> | serweryv2 | Tak | Tak |
> | singleServers | Tak | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | artifactSources | Tak | Tak |
> | Wdrożenia | Tak | Tak |
> | serwisTopologie | Tak | Tak |
> | serviceTopologie / usługi | Tak | Tak |
> | serviceTopologie / usługi / usługiJednostki | Tak | Tak |
> | kroki | Tak | Tak |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopWirtualizacja

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | grupy aplikacji | Tak | Tak |
> | applicationgroups / aplikacje | Nie | Nie |
> | applicationgroups / komputery stacjonarne | Nie | Nie |
> | applicationgroups / startmenuitems | Nie | Nie |
> | hostpoole | Tak | Tak |
> | hostpools / hosty sesji | Nie | Nie |
> | hostpools / sessionhosts / usersessions | Nie | Nie |
> | hostpools / usersessions | Nie | Nie |
> | obszary robocze | Tak | Tak |

## <a name="microsoftdevices"></a>Urządzenia firmy Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Baseny elastyczne | Tak | Tak |
> | Baseny elastyczne / IotHubTenants | Tak | Tak |
> | ElasticPools / IotHubTenants / securitySettings | Nie | Nie |
> | IotHubs ( IotHubs ) | Tak | Tak |
> | IotHubs / eventGridFilters | Nie | Nie |
> | IotHubs / securitySettings | Nie | Nie |
> | Usługi inicjowania obsługi administracyjnej | Tak | Tak |
> | Zwyczaje | Nie | Nie |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Rurociągów | Tak | Tak |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | kontrolery | Tak | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | centra laboratoryjne | Tak | Tak |
> | Labs | Tak | Tak |
> | laboratoria / środowiska | Tak | Tak |
> | laboratoria / serwisRunners | Tak | Tak |
> | laboratoria / wirtualneMatyny | Tak | Tak |
> | Harmonogramy | Tak | Tak |

## <a name="microsoftdocumentdb"></a>Baza danych microsoft.documentdb

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | nazwy konta danych | Nie | Nie |
> | konta danych | Tak | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Domen | Tak | Tak |
> | domeny / domainOwnershipIdentifiers | Nie | Nie |
> | generujeSsoRequest | Nie | Nie |
> | topLevelDomains | Nie | Nie |
> | sprawdzanie poprawnościInformacji DomainRegistrationInformation | Nie | Nie |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nie | Nie |
> | lcsprojects / chmuryrozwają | Nie | Nie |
> | lcsprojects / złącza | Nie | Nie |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Domen | Tak | Tak |
> | domeny / tematy | Nie | Nie |
> | eventSubscriptions | Nie | Nie |
> | extensionTopics | Nie | Nie |
> | przestrzenie partnername | Tak | Tak |
> | partnerNamespaces / eventKanały | Nie | Nie |
> | rejestracje partnerów | Tak | Tak |
> | partnerTopics | Tak | Tak |
> | partnerTopics / eventSubscriptions | Nie | Nie |
> | systemTopy | Tak | Tak |
> | systemTopics / eventSubscriptions | Nie | Nie |
> | Tematy | Tak | Tak |
> | topicTypy | Nie | Nie |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Klastrów | Tak | Tak |
> | Obszary nazw | Tak | Tak |
> | przestrzenie nazw / zasady autoryzacji | Nie | Nie |
> | przestrzenie nazw / disasterrecoveryconfigs | Nie | Nie |
> | przestrzenie nazw / eventhubs | Nie | Nie |
> | przestrzenie nazw / eventhubs / zasady autoryzacji | Nie | Nie |
> | przestrzenie nazw / eventhubs / grupy konsumentów | Nie | Nie |
> | przestrzenie nazw / zasady sieciowe | Nie | Nie |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary nazw | Tak | Tak |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | funkcjaProvidery | Nie | Nie |
> | funkcje | Nie | Nie |
> | dostawców | Nie | Nie |
> | subskrypcjaFeatureRegistrations | Nie | Nie |

## <a name="microsoftgallery"></a>Galeria Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Zarejestrować | Nie | Nie |
> | budynki galerii | Nie | Nie |
> | generateartifactaccessuri | Nie | Nie |
> | myareas | Nie | Nie |
> | myareas / obszary | Nie | Nie |
> | myareas / obszary / obszary | Nie | Nie |
> | myareas / obszary / obszary / galleryitems | Nie | Nie |
> | myareas / obszary / galleryitems | Nie | Nie |
> | myareas / galleryitems | Nie | Nie |
> | zarejestruj | Nie | Nie |
> | zasoby | Nie | Nie |
> | retrieveresourcesbyid | Nie | Nie |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |

## <a name="microsoftguestconfiguration"></a>Konfiguracja witryny Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | konta automanagedAccounts | Tak | Tak |
> | autoManagedVmConfigurationProfiles | Tak | Tak |
> | konfiguracjaProfileAssignments | Nie | Nie |
> | guestConfigurationAssignments (Konfiguracje gości) | Nie | Nie |
> | Oprogramowania | Nie | Nie |
> | softwareUpdateProfile | Nie | Nie |
> | oprogramowanieUpdy | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Tak | Tak |
> | sapMonitors (SapMonitors) | Tak | Tak |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | dedykowane moduły | Tak | Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Klastrów | Tak | Tak |
> | klastry / aplikacje | Nie | Nie |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | services | Tak | Tak |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Maszyny | Tak | Tak |
> | maszyny / rozszerzenia | Tak | Tak |

## <a name="microsofthybriddata"></a>Microsoft.HybridDana

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | menedżery danych | Tak | Tak |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Składniki | Tak | Tak |
> | siećZakskopy | Tak | Tak |

## <a name="microsoftimportexport"></a>Microsoft.ImportEksport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Zadania | Tak | Tak |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | actionGroups (Grupy akcji) | Tak | Tak |
> | activityLogAlerts (Blogi blogowe) | Tak | Tak |
> | zasady alertów | Tak | Tak |
> | automatyczne skalowanie | Tak | Tak |
> | Składniki | Tak | Tak |
> | komponenty / linkedStorageKony | Nie | Nie |
> | składniki / ProactiveDetectionConfigs | Nie | Nie |
> | diagnostykaStawienie | Nie | Nie |
> | guestDiagnosticSettings | Tak | Tak |
> | guestDiagnosticSettingsAssociation | Tak | Tak |
> | logofile | Tak | Tak |
> | metryczneAlerts | Tak | Tak |
> | privateLinkScopes | Tak | Tak |
> | privateLinkScopes / privateEndpointConnections | Nie | Nie |
> | privateLinkScopes / scopedResources | Nie | Nie |
> | pakiety zapytań | Tak | Tak |
> | queryPacks / zapytania | Nie | Nie |
> | scheduledQueryRules | Tak | Tak |
> | testy internetowe | Tak | Tak |
> | skoroszyty | Tak | Tak |
> | zeszyty | Tak | Tak |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | diagnostykaStawienie | Nie | Nie |
> | diagnostykaKategorie | Nie | Nie |

## <a name="microsoftiotcentral"></a>Centrum IoT Firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nie | Nie |
> | IoTApps | Tak | Tak |

## <a name="microsoftiotspaces"></a>Przestrzenie IoT firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Graph | Tak | Tak |

## <a name="microsoftkeyvault"></a>Usługa Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nie | Nie |
> | Puli hsm | Tak | Tak |
> | Sklepienia | Tak | Tak |
> | skarbce / dostępPolicies | Nie | Nie |
> | przechowalnia / eventGridFilters | Nie | Nie |
> | skarbce / sekrety | Nie | Nie |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | connectedClusters (łączonykwierarze) | Tak | Tak |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Klastrów | Tak | Tak |
> | klastry / dołączone konfiguracje baz danych | Nie | Nie |
> | klastry / bazy danych | Nie | Nie |
> | klastry / bazy danych / połączenia danych | Nie | Nie |
> | klastry / bazy danych / eventhubconnections | Nie | Nie |
> | klastry / bazy danych / principalassignments | Nie | Nie |
> | klastry / połączenia danych | Nie | Nie |
> | klastry / główne przypisania | Nie | Nie |
> | klastry / współużytkowania | Nie | Nie |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | konta labaccounts | Tak | Tak |
> | użytkownicy | Nie | Nie |

## <a name="microsoftlogic"></a>Logika firmy Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hostingŚrodowiska | Tak | Tak |
> | integrationKontacje | Tak | Tak |
> | integracjaServiceEnvironments | Tak | Tak |
> | integrationServiceEnvironments / managedApis | Tak | Tak |
> | isolatedŚrodowiska | Tak | Tak |
> | Przepływy pracy | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Uczenie się maszyn firmy Microsoft.Machine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | zobowiązaniePlany | Tak | Tak |
> | Webservices | Tak | Tak |
> | Obszary robocze | Tak | Tak |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | obszary robocze | Tak | Tak |
> | obszary robocze / obliczenia | Nie | Nie |
> | obszary robocze / eventGridFilters | Nie | Nie |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | applyUpdates (stosowaniewzszy i poucza | Nie | Nie |
> | konfiguracjaAssignments | Nie | Nie |
> | konfiguracje obsługi | Tak | Tak |
> | aktualizacje | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Tożsamości | Nie | Nie |
> | użytkownikAssignedIdentities | Tak | Tak |

## <a name="microsoftmanagedservices"></a>Usługi zarządzane przez firmę Microsoft.Managed

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions (RejestracjeDefinicji) | Nie | Nie |
> | rejestracjaAssignments | Nie | Nie |
> | rejestracjaDefinicje | Nie | Nie |

## <a name="microsoftmanagement"></a>Zarządzanie microsoftem

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | getEntities (władać) | Nie | Nie |
> | zarządzanieGrupy | Nie | Nie |
> | managementGrupy / ustawienia | Nie | Nie |
> | zasoby | Nie | Nie |
> | startTenantBackfill | Nie | Nie |
> | tenantBackfillStatus | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft.Maps (Mapy firmy Microsoft)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |
> | konta / eventGridFilters | Nie | Nie |
> | konta / privateAtlases | Tak | Tak |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Oferuje | Nie | Nie |
> | offerTypes (Typy oferty) | Nie | Nie |
> | offerTypes / wydawcy | Nie | Nie |
> | offerTypes / wydawcy / oferty | Nie | Nie |
> | offerTypes / wydawcy / oferty / plany | Nie | Nie |
> | offerTypes / wydawcy / oferty / plany / umowy | Nie | Nie |
> | offerTypes / wydawcy / oferty / plany / configs | Nie | Nie |
> | offerTypes / wydawcy / oferty / plany / configs / importImage | Nie | Nie |
> | privategalleryitems | Nie | Nie |
> | privateStoreClient (Magazyn prywatnyClient) | Nie | Nie |
> | prywatneSklepy | Nie | Nie |
> | privateStores / oferty | Nie | Nie |
> | Produktów | Nie | Nie |
> | Wydawców | Nie | Nie |
> | wydawcy / oferty | Nie | Nie |
> | wydawcy / oferty / poprawki | Nie | Nie |
> | zarejestruj | Nie | Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | klasycznyDevSługa | Tak | Tak |
> | updateCommunicationPreference | Nie | Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Umów | Nie | Nie |
> | rodzaje ofert | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | usługi mediasce | Tak | Tak |
> | mediaservices / accountFilters | Nie | Nie |
> | usługi mediasce / aktywa | Nie | Nie |
> | mediaservices / assets / assetFiltry | Nie | Nie |
> | mediaservices / contentKeyPolicies | Nie | Nie |
> | mediaservices / eventGridFilters | Nie | Nie |
> | mediaservices / liveEventOperations | Nie | Nie |
> | mediaservices / liveEvents | Tak | Tak |
> | mediaservices / liveEvents / liveOutputs | Nie | Nie |
> | mediaservices / liveOutputOperations | Nie | Nie |
> | mediaservices / mediaGrafy | Nie | Nie |
> | mediaservices / streamingEndpointOperations | Nie | Nie |
> | mediaservices / streamingEndpoints | Tak | Tak |
> | mediaservices / streamingLocators | Nie | Nie |
> | mediaservices / streamingPolicies | Nie | Nie |
> | usługi medialne / przekształca | Nie | Nie |
> | mediaservices / przekształca / praca | Nie | Nie |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | appClusters | Tak | Tak |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | ocenyProjekty | Tak | Tak |
> | migrowanieprojektów | Tak | Tak |
> | moveKollections (MoveCollections) | Tak | Tak |
> | Projektów | Tak | Tak |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality (Reality mieszane firmy Microsoft.MixedReality)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastKontacje | Tak | Tak |
> | obiektUnderstandingKontacje | Tak | Tak |
> | konta zdalnegorenderingu | Tak | Tak |
> | przestrzenne Konta na kontach | Tak | Tak |

## <a name="microsoftnetapp"></a>Microsoft.NetApp (Microsoft.NetApp)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | netAppKony | Tak | Nie |
> | netAppKony / kontoBackups | Nie | Nie |
> | netAppKony / pojemnośćPuly | Tak | Nie |
> | netAppKony / pojemnośćPuly / woluminy | Tak | Nie |
> | netAppAccounts / pojemnośćPuly / woluminy / migawki | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Tak | Tak |
> | aplikacjaGatewayWebApplicationFirewallPolicies | Tak | Tak |
> | applicationSecurityGroups | Tak | Tak |
> | azureFirewallFqdnTags | Nie | Nie |
> | azureFirewalls | Tak | Nie |
> | bastionHosts | Tak | Nie |
> | bgpSpołeczności usług | Nie | Nie |
> | Połączenia | Tak | Tak |
> | ddosCustomPolicies | Tak | Tak |
> | plany ochrony ddos | Tak | Tak |
> | dnsOperationStatuses | Nie | Nie |
> | strefy dns | Tak | Tak |
> | dnszones / A | Nie | Nie |
> | dnszones / AAAA | Nie | Nie |
> | dnszones / wszystkie | Nie | Nie |
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
> | expressRouteCrossConnections (Połączenie ekspresowe) | Tak | Tak |
> | expressRouteGateways | Tak | Tak |
> | expressRoutePorts | Tak | Tak |
> | expressRouteServiceProviders expressRouteServiceProviders expressRouteServiceProviders expressRo | Nie | Nie |
> | firewallPolicies | Tak | Tak |
> | przednie drzwi | Tak, ale ograniczone (patrz [uwaga poniżej)](#frontdoor) | Tak |
> | frontdoorWebApplicationFirewallManagedRuleSets | Tak, ale ograniczone (patrz [uwaga poniżej)](#frontdoor) | Nie |
> | frontdoorWebApplicationFirewallPolicies | Tak, ale ograniczone (patrz [uwaga poniżej)](#frontdoor) | Tak |
> | wniosek o źródła getDnsResourceReference | Nie | Nie |
> | internalNotify | Nie | Nie |
> | obciążeniaBalancery | Tak | Tak |
> | localNetworkGateways | Tak | Tak |
> | natGateways ( natGateways ) | Tak | Tak |
> | siećIntentPolicies | Tak | Tak |
> | siećPowierzchnie | Tak | Tak |
> | networkProfiles | Tak | Tak |
> | networkSecurityGroups | Tak | Tak |
> | networkWatchers (obserwatorzy sieci) | Tak | Tak |
> | networkWatchers / connectionMonitors | Tak | Nie |
> | networkWatchers / flowLogs | Nie | Nie |
> | networkWatchers / soczewki | Tak | Nie |
> | networkWatchers / pingMeshes | Tak | Nie |
> | p2sVpnGateways | Tak | Tak |
> | privateDnsOperationStatuss | Nie | Nie |
> | prywatneDnsZones | Tak | Tak |
> | privateDnsZones / A | Nie | Nie |
> | prywatneDnsZones / AAAA | Nie | Nie |
> | privateDnsZones / wszystkie | Nie | Nie |
> | privateDnsZones / CNAME | Nie | Nie |
> | privateDnsZones / MX | Nie | Nie |
> | privateDnsZones / PTR | Nie | Nie |
> | privateDnsZones / SOA | Nie | Nie |
> | privateDnsZones / SRV | Nie | Nie |
> | privateDnsZones / TXT | Nie | Nie |
> | privateDnsZones / virtualNetworkLinks | Tak | Tak |
> | privateEndpoints | Tak | Tak |
> | privateLinkServices (usługi prywatnelink) | Tak | Tak |
> | publicYSadresy | Tak | Tak |
> | publicIPPrefixes | Tak | Tak |
> | routeFiltry | Tak | Tak |
> | routeTables (routetables) | Tak | Tak |
> | serviceEndpointPolicies | Tak | Tak |
> | trafficManagerGeographicHierarchies | Nie | Nie |
> | profile trafficmanager | Tak | Tak |
> | profil/ciepło TrafficmanagerMaps | Nie | Nie |
> | klawisze trafficManagerUserMetricsKeys | Nie | Nie |
> | wirtualneshuby | Tak | Tak |
> | virtualNetworkGateways | Tak | Tak |
> | wirtualne sieci | Tak | Tak |
> | virtualNetworkTaps (Wirtualnetapy sieciowe) | Tak | Tak |
> | wirtualne wich | Tak | Nie |
> | vpnGateways | Tak | Tak |
> | vpnWitryny | Tak | Tak |
> | webApplicationFirewallPolicies | Tak | Tak |

<a id="frontdoor" />

> [!NOTE]
> W przypadku usługi Azure Front Door Service można zastosować tagi podczas tworzenia zasobu, ale aktualizowanie lub dodawanie tagów nie jest obecnie obsługiwane.


## <a name="microsoftnotebooks"></a>Notesy firmy Microsoft.Notess

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | NotebookProxies (NotebookProxies) | Nie | Nie |

## <a name="microsoftnotificationhubs"></a>Witryna Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary nazw | Tak | Nie |
> | przestrzenie nazw / notificationHubs | Tak | Nie |

## <a name="microsoftobjectstore"></a>Sklep Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Tak | Tak |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Tak | Tak |
> | Miejsca importu | Tak | Tak |
> | Miejsca serwera | Tak | Tak |
> | Miejsca wirtualne | Tak | Tak |

## <a name="microsoftoperationalinsights"></a>Dane operacyjne firmy Microsoft.Operational

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Klastrów | Tak | Tak |
> | łącze celowe | Nie | Nie |
> | pamięć masowaInsightConfigs | Nie | Nie |
> | obszary robocze | Tak | Tak |
> | obszary robocze / daneRozprzemysł | Nie | Nie |
> | obszary robocze / daneŹródła | Nie | Nie |
> | obszary robocze / linkedServices | Nie | Nie |
> | obszary robocze / linkedStorageKony | Nie | Nie |
> | obszary robocze / kwerenda | Nie | Nie |
> | obszary robocze / scopedPrivateLinkProxies | Nie | Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | zarządzaniami | Nie | Nie |
> | konfiguracje zarządzania | Tak | Tak |
> | rozwiązania | Tak | Tak |
> | widoki | Tak | Tak |

## <a name="microsoftpeering"></a>Komunikacja równorzędna firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nie | Nie |
> | peerAsns (równorzędny) | Nie | Nie |
> | komunikacja równorzędna | Tak | Tak |
> | peeringServiceCountries (nie można ego) | Nie | Nie |
> | usługi równorzędneProvidery | Nie | Nie |
> | usługi komunikacji równorzędnej | Tak | Tak |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | politykaWzdowyzdowy | Nie | Nie |
> | policyMetadata (Meada polityki) | Nie | Nie |
> | policyStates | Nie | Nie |
> | policyTrackedResources | Nie | Nie |
> | środki zaradcze | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konsole programu | Nie | Nie |
> | pulpity nawigacyjne | Tak | Tak |
> | userSettings | Nie | Nie |

## <a name="microsoftpowerbi"></a>Program Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | workspaceKolekcje | Tak | Tak |

## <a name="microsoftpowerbidedicated"></a>Program Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Pojemności | Tak | Tak |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | accounts | Tak | Tak |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary robocze | Tak | Tak |

## <a name="microsoftrecoveryservices"></a>Usługi Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nie | Nie |
> | Sklepienia | Tak | Tak |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary nazw | Tak | Tak |
> | przestrzenie nazw / zasady autoryzacji | Nie | Nie |
> | przestrzenie nazw / połączenia hybrydowe | Nie | Nie |
> | przestrzenie nazw / połączenia hybrydowe / zasady autoryzacji | Nie | Nie |
> | przestrzenie nazw / wcfrelays | Nie | Nie |
> | przestrzenie nazw / wcfrelays / autoryzacjarule | Nie | Nie |

## <a name="microsoftremoteapp"></a>Aplikacja Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | accounts | Nie | Nie |
> | Kolekcje | Tak | Tak |
> | kolekcje / aplikacje | Nie | Nie |
> | kolekcje / securityprincipals | Nie | Nie |
> | templateImages | Nie | Nie |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Kwerendy | Tak | Tak |
> | resourceChangeDetails | Nie | Nie |
> | zmiany zasobów | Nie | Nie |
> | zasoby | Nie | Nie |
> | zasobyHistoria | Nie | Nie |
> | subskrypcjeStatus | Nie | Nie |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | dostępnośćStatusy | Nie | Nie |
> | childDostępnośćStatusy | Nie | Nie |
> | childResources | Nie | Nie |
> | nowe | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | wpływResources | Nie | Nie |
> | metadane | Nie | Nie |
> | powiadomienia | Nie | Nie |

## <a name="microsoftresources"></a>Zasoby firmy Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Wdrożeń | Tak | Nie |
> | wdrożenia / operacje | Nie | Nie |
> | skrypty rozmieszczenia | Tak | Tak |
> | deploymentScripts / dzienniki | Nie | Nie |
> | Linki | Nie | Nie |
> | notifyResourceJobs | Nie | Nie |
> | dostawców | Nie | Nie |
> | zasobyGrupy | Tak | Nie |
> | Subskrypcji | Tak | Nie |
> | Najemców | Nie | Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aplikacje | Tak | Tak |
> | saasresources | Nie | Nie |

## <a name="microsoftsearch"></a>Wyszukiwanie microsoft.search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | zasobyHealthMetadata | Nie | Nie |
> | wyszukiwanie Usługi | Tak | Tak |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nie | Nie |
> | zaawansowaneThreatProtectionSettings | Nie | Nie |
> | alerts | Nie | Nie |
> | dozwolone Połączenia | Nie | Nie |
> | aplikacjaWhitelistings | Nie | Nie |
> | assessmentMetadata | Nie | Nie |
> | oceny | Nie | Nie |
> | autoDismissAlertsRules | Nie | Nie |
> | Automatyki | Tak | Tak |
> | Automatyczne ustawianie obsługi | Nie | Nie |
> | Zgodność z przepisami | Nie | Nie |
> | dataCollectionAgents | Nie | Nie |
> | deviceSecurityGroups (Grupy zabezpieczeń) | Nie | Nie |
> | discoveredSecuritySolutions | Nie | Nie |
> | zewnętrzneSecuritySolutions | Nie | Nie |
> | InformacjeProtectionPolicies | Nie | Nie |
> | rozwiązania iotSecuritySolutions | Tak | Tak |
> | iotSecuritySolutions / analyticsModels | Nie | Nie |
> | iotSecuritySolutions / analyticsModels / zagregowaneAlerts | Nie | Nie |
> | iotSecuritySolutions / analyticsModels / zagregowaneZamiany | Nie | Nie |
> | jitNetworkAccessPolicies | Nie | Nie |
> | networkData | Nie | Nie |
> | zasady | Nie | Nie |
> | ceny | Nie | Nie |
> | zgodność z przepisamiStandardy | Nie | Nie |
> | zgodność z przepisamiStandardy / regulatoryComplianceControls | Nie | Nie |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nie | Nie |
> | secureScoreControlDefinitions | Nie | Nie |
> | secureScoreControls | Nie | Nie |
> | secureScores (bezpieczneszechanie) | Nie | Nie |
> | secureScores / secureScoreControls | Nie | Nie |
> | securityContacts | Nie | Nie |
> | zabezpieczeniaRozwiązywanie | Nie | Nie |
> | zabezpieczeniaSolutionsReferenceData | Nie | Nie |
> | zabezpieczeniaStatusy | Nie | Nie |
> | zabezpieczeniaStatusesSummaries | Nie | Nie |
> | serverVulnerabilityAssessments serverVulnerabilityassessments serverVulnerabilityAssessments server | Nie | Nie |
> | settings | Nie | Nie |
> | podsypów | Nie | Nie |
> | zadania | Nie | Nie |
> | Topologii | Nie | Nie |
> | miejsca roboczeStawienia | Nie | Nie |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph (Microsoft.SecurityGraph)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | diagnostykaStawienie | Nie | Nie |
> | diagnostykaKategorie | Nie | Nie |

## <a name="microsoftsecurityinsights"></a>Witryny zabezpieczeń firmy Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Agregacji | Nie | Nie |
> | alertRules (alertRules) | Nie | Nie |
> | alertRuleTemplates | Nie | Nie |
> | zakładki | Nie | Nie |
> | Przypadkach | Nie | Nie |
> | połączenia danych | Nie | Nie |
> | Wymagania kontroli połączeń danych | Nie | Nie |
> | Podmioty | Nie | Nie |
> | entityQueries (Wyeksle) | Nie | Nie |
> | Incydentów | Nie | Nie |
> | OfficeConsents | Nie | Nie |
> | settings | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary nazw | Tak | Tak |
> | przestrzenie nazw / zasady autoryzacji | Nie | Nie |
> | przestrzenie nazw / disasterrecoveryconfigs | Nie | Nie |
> | przestrzenie nazw / eventgridfilters | Nie | Nie |
> | przestrzenie nazw / zasady sieciowe | Nie | Nie |
> | przestrzenie nazw / kolejki | Nie | Nie |
> | przestrzenie nazw / kolejki / zasady autoryzacji | Nie | Nie |
> | przestrzenie nazw / tematy | Nie | Nie |
> | przestrzenie nazw / tematy / zasady autoryzacji | Nie | Nie |
> | przestrzenie nazw / tematy / subskrypcje | Nie | Nie |
> | przestrzenie nazw / tematy / subskrypcje / zasady | Nie | Nie |
> | premiumMessagingRegje | Nie | Nie |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aplikacje | Tak | Tak |
> | Klastrów | Tak | Tak |
> | klastry / aplikacje | Nie | Nie |
> | kontenerGrupy | Tak | Tak |
> | containerGroupSets (Zestawy grup kontenerów) | Tak | Tak |
> | edgeclusters | Tak | Tak |
> | edgeclusters / aplikacje | Nie | Nie |
> | zarządzane clusters | Tak | Tak |
> | zarządzaneklura / typy węzłów | Nie | Nie |
> | Sieci | Tak | Tak |
> | sklepy sekretowe | Tak | Tak |
> | secretstores / certyfikaty | Nie | Nie |
> | secretstores / tajemnice | Nie | Nie |
> | volumes | Tak | Tak |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aplikacje | Tak | Tak |
> | kontenerGrupy | Tak | Tak |
> | Bramy | Tak | Tak |
> | Sieci | Tak | Tak |
> | wpisy tajne | Tak | Tak |
> | volumes | Tak | Tak |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | rejestracje dostawców | Nie | Nie |
> | providerRegistrations / resourceTypeRegistrations | Nie | Nie |
> | Wdrożenia | Tak | Tak |

## <a name="microsoftsignalrservice"></a>Usługa Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | SignalR | Tak | Tak |
> | SignalR / eventGridFilters | Nie | Nie |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Tak | Tak |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nie | Nie |

## <a name="microsoftsolutions"></a>Rozwiązania firmy Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions (Zdefiniowanie aplikacji) | Tak | Tak |
> | aplikacje | Tak | Tak |
> | jitRequests | Tak | Tak |

## <a name="microsoftspoolservice"></a>Usługa Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | zarejestrowaneSubskrypcje | Nie | Nie |
> | Szpule | Tak | Tak |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | zarządzaneInstności | Tak | Tak |
> | zarządzaneInstności / bazy danych | Tak (patrz [uwaga poniżej)](#sqlnote) | Tak |
> | zarządzaneInstacja / bazy danych / backupShortTermRetentionPolicies | Nie | Nie |
> | zarządzaneInstności / bazy danych / schematy / tabele / kolumny / sensitivityLabels | Nie | Nie |
> | zarządzaneInstyty / bazy danych / podatnośćA na zagrożeniaSzewaga | Nie | Nie |
> | zarządzaneInstacji / bazy danych / podatnośćaosuty / zasady / linie bazowe | Nie | Nie |
> | zarządzaneInstekcje / szyfrowanieOtektor | Nie | Nie |
> | zarządzaneInstyki / klucze | Nie | Nie |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nie | Nie |
> | zarządzaneInstyty / podatnośćA na zagrożenia | Nie | Nie |
> | Serwerów | Tak | Tak |
> | serwery / administratorzy | Nie | Nie |
> | serwery / komunikacjaLinks | Nie | Nie |
> | serwery / bazy danych | Tak (patrz [uwaga poniżej)](#sqlnote) | Tak |
> | serwery / szyfrowanieProtector | Nie | Nie |
> | serwery / firewallRules | Nie | Nie |
> | serwery / klucze | Nie | Nie |
> | serwery / przywracalneDroppedDatabases | Nie | Nie |
> | serwery / usługiobiekty | Nie | Nie |
> | serwery / tdeCertificates | Nie | Nie |
> | virtualClusters (Wirtualneklustery) | Nie | Nie |

<a id="sqlnote" />

> [!NOTE]
> Główna baza danych nie obsługuje tagów, ale inne bazy danych, w tym bazy danych usługi Azure SQL Data Warehouse, obsługują tagi. Bazy danych usługi Azure SQL Data Warehouse muszą być w stanie aktywnym (nie wstrzymane).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGrupy | Tak | Tak |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nie | Nie |
> | SqlVirtualMachines | Tak | Tak |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | konta przechowywania | Tak | Tak |
> | storageKonta / blobSługi | Nie | Nie |
> | storageKonta / fileServices | Nie | Nie |
> | storageKonta / kolejkiUsługi | Nie | Nie |
> | przechowywanieKonta / usługi | Nie | Nie |
> | storageKonta / usługi / metryKadefunkowania | Nie | Nie |
> | storageKonta / tabelaUsługi | Nie | Nie |
> | Zwyczaje | Nie | Nie |

## <a name="microsoftstoragecache"></a>Usługa Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Buforuje | Tak | Tak |
> | pamięci podręczne / storageTargets | Nie | Nie |
> | usageModels | Nie | Nie |

## <a name="microsoftstoragereplication"></a>Aplikacja Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | grupy replikacji | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | usługi storageSyncServices | Tak | Tak |
> | storageSyncServices / registeredServers | Nie | Nie |
> | storageSyncServices / syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices / przepływy pracy | Nie | Nie |

## <a name="microsoftstoragesyncdev"></a>System Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | usługi storageSyncServices | Tak | Tak |
> | storageSyncServices / registeredServers | Nie | Nie |
> | storageSyncServices / syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices / przepływy pracy | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Program Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | usługi storageSyncServices | Tak | Tak |
> | storageSyncServices / registeredServers | Nie | Nie |
> | storageSyncServices / syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices / przepływy pracy | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Menedżerów | Tak | Tak |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalityka

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Tak (patrz uwaga poniżej) | Tak |

> [!NOTE]
> Nie można dodać tagu, gdy jest uruchomiony streamingjobs. Zatrzymaj zasób, aby dodać znacznik.

## <a name="microsoftsubscription"></a>Subskrypcja firmy Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Anuluj | Nie | Nie |
> | Createsubscription | Nie | Nie |
> | Włącz | Nie | Nie |
> | zmień nazwę | Nie | Nie |
> | SubskrypcjeDefinicje | Nie | Nie |
> | Działanie subskrypcji | Nie | Nie |
> | Subskrypcji | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Środowiskach | Tak | Nie |
> | środowiska / accessPolicies | Nie | Nie |
> | środowiska / źródła zdarzeń | Tak | Nie |
> | środowiska / referenceDataSets | Tak | Nie |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | dedykowaneCloudNodes | Tak | Tak |
> | dedykowaneCloudServices | Tak | Tak |
> | wirtualnematyny | Tak | Tak |

## <a name="microsoftvnfmanager"></a>Menedżer microsoft.Vnf

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | urządzenia | Tak | Tak |
> | zarejestrowaneSubskrypcje | Nie | Nie |
> | Dostawców | Nie | Nie |
> | sprzedawcy / skus | Nie | Nie |
> | dostawców / vnfs | Nie | Nie |
> | virtualNetworkFunctionSkus | Nie | Nie |
> | vnfs ( vnfs ) | Tak | Tak |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | konta apiManagementAccounts | Nie | Nie |
> | apiManagementKonta / apiAcls | Nie | Nie |
> | apiManagementKonta / apis | Nie | Nie |
> | apiManagementKonta / apis / apiAcls | Nie | Nie |
> | apiManagementKonta / apis / connectionAcls | Nie | Nie |
> | apiManagementKonta / apis / połączenia | Nie | Nie |
> | apiManagementKonta / apis / połączenia / connectionAcls | Nie | Nie |
> | apiManagementKonta / apis / zlokalizowaneDedefitions | Nie | Nie |
> | apiManagementKonta / connectionAcls | Nie | Nie |
> | apiManagementKonta / połączenia | Nie | Nie |
> | stopy rozliczeniowe | Nie | Nie |
> | certyfikaty | Tak | Tak |
> | connectionGateways | Tak | Tak |
> | Połączenia | Tak | Tak |
> | zwyczajApis | Tak | Tak |
> | deletedSites | Nie | Nie |
> | hostingŚrodowiska | Tak | Tak |
> | hostingŚrodowiska / eventGridFilters | Nie | Nie |
> | hostingŚrodowiska / baseny wielozadaniowe | Nie | Nie |
> | hostingŚrodowiska / pracownicy Baseny | Nie | Nie |
> | kubeŚrodowiska | Tak | Tak |
> | publikowanieUżytnicy | Nie | Nie |
> | zalecenia | Nie | Nie |
> | zasobyHealthMetadata | Nie | Nie |
> | środowiska wykonawcze | Nie | Nie |
> | serwerFary | Tak | Tak |
> | serwerFary / eventGridFilters | Nie | Nie |
> | lokacje | Tak | Tak |
> | strony / config  | Nie | Nie |
> | strony / eventGridFilters | Nie | Nie |
> | witryn / hostNameBindings | Nie | Nie |
> | strony / siećConfig | Nie | Nie |
> | strony / premieraddons | Tak | Tak |
> | witryny / szczeliny | Tak | Tak |
> | strony / sloty / eventGridFilters | Nie | Nie |
> | strony / sloty / hostNameBindings | Nie | Nie |
> | strony / sloty / siećConfig | Nie | Nie |
> | źródłoKontrole | Nie | Nie |
> | staicWitryny | Tak | Tak |
> | Sprawdzania poprawności | Nie | Nie |
> | verifyHostingEnvironmentVnet | Nie | Nie |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | diagnostykaStawienie | Nie | Nie |
> | diagnostykaKategorie | Nie | Nie |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Usługi urządzenia | Tak | Tak |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje znaczniki | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Składniki | Nie | Nie |
> | komponentySummary | Nie | Nie |
> | MonitorowanieInstacji | Nie | Nie |
> | monitorInstancesSummary | Nie | Nie |
> | Monitory | Nie | Nie |
> | powiadomieniaUstawienia | Nie | Nie |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak stosować znaczniki do zasobów, zobacz [Organizowanie zasobów platformy Azure za pomocą tagów](tag-resources.md).
