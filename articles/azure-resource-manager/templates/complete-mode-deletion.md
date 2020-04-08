---
title: Usuwanie w trybie pełnym
description: Pokazuje, jak typy zasobów obsługują usuwanie trybu pełnego w szablonach usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e0250b289ce7a228d844023c3e1d1110438b3afc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802573"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Usuwanie zasobów platformy Azure w celu wdrożenia w trybie kompletnym

W tym artykule opisano, jak typy zasobów obsługują usuwanie, gdy nie jest w szablonie, który jest wdrażany w trybie kompletnym.

Typy zasobów oznaczone **tak** są usuwane, gdy typ nie znajduje się w szablonie wdrożonym w trybie kompletnym.

Typy zasobów oznaczone **nr** nie są automatycznie usuwane, gdy nie ma ich w szablonie; jednak są one usuwane, jeśli zasób nadrzędny zostanie usunięty. Aby uzyskać pełny opis zachowania, zobacz [tryby wdrażania usługi Azure Resource Manager](deployment-modes.md).

W przypadku wdrażania [w więcej niż jednej grupie zasobów w szablonie](cross-resource-group-deployment.md)zasoby w grupie zasobów określone w operacji wdrażania mogą zostać usunięte. Zasoby w dodatkowych grupach zasobów nie są usuwane.

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
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Usługi w domenie | Tak |
> | Usługi w domenie / oucontainer | Nie |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wsparcieDarzy | Nie |

## <a name="microsoftadhybridhealthservice"></a>Usługa Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aadsupportcases | Nie |
> | dodaje usługi | Nie |
> | Agentów | Nie |
> | anonimowoapiusers | Nie |
> | konfiguracja | Nie |
> | dzienniki | Nie |
> | reports | Nie |
> | servicehealthmetrics | Nie |
> | services | Nie |

## <a name="microsoftadvisor"></a>Doradca firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konfiguracji | Nie |
> | generowaniezamiasje | Nie |
> | metadane | Nie |
> | zalecenia | Nie |
> | Suppressions | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | actionRules (Zasady działania) | Tak |
> | alerts | Nie |
> | lista alertów | Nie |
> | alertyMetaData | Nie |
> | alertySummary | Nie |
> | alertsSummaryList | Nie |
> | zasady inteligentnego FiltraAlert | Tak |
> | grupy smartGroups | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.analysisservices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Serwerów | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | reportFeedback | Nie |
> | usługa | Tak |
> | validateServiceName (nazwa usługi) | Nie |

## <a name="microsoftappconfiguration"></a>Konfiguracja aplikacji Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konfiguracjaSklepy | Tak |
> | konfiguracjaSklepy / eventGridFilters | Nie |

## <a name="microsoftappplatform"></a>Platforma Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Spring | Tak |

## <a name="microsoftattestation"></a>Zaświadczenie firmy Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zaświadczenieProvidery | Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | classicAdministrators | Nie |
> | dataAliases (łańcuchy danych) | Nie |
> | denyAssignments (odmowa) | Nie |
> | elevateAccess | Nie |
> | znajdźOrphanRoleZasypisy | Nie |
> | Blokad | Nie |
> | Uprawnienia | Nie |
> | zmiany zasadAssignments | Nie |
> | Policydefinitions | Nie |
> | zasadySetDefinitions | Nie |
> | providerOperations | Nie |
> | roleAssignments | Nie |
> | roleAssignmentsUsageMetrics | Nie |
> | definicje ról | Nie |

## <a name="microsoftautomation"></a>Automatyzacja firmy Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | automationKonta | Tak |
> | automationKony / konfiguracje | Tak |
> | automationKonta / oferty pracy | Nie |
> | automationKonta / privateEndpointConnectionProxies | Nie |
> | automationKonta / privateEndpointConnections | Nie |
> | automationKonta / privateLinkResources | Nie |
> | automationKonty / elementy runbook | Tak |
> | automationKonta / oprogramowanieKonfiguracje aktualizacji | Nie |
> | automationKony / elementy elementów webhook | Nie |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konfiguracjaSklepy | Tak |
> | konfiguracjaSklepy / eventGridFilters | Nie |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Genewa

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Środowiskach | Nie |
> | środowiska / konta | Nie |
> | środowiska / konta / przestrzenie nazw | Nie |
> | środowiska / konta / przestrzenie nazw / konfiguracje | Nie |

## <a name="microsoftazureactivedirectory"></a>Program Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | b2cKatory | Tak |
> | b2ctenants | Nie |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hybridDataManagers | Tak |
> | PostgresInstances (PostgresInstances) | Tak |
> | SqlInstances (Instancje sql) | Tak |
> | Sqlserverregistrations | Tak |
> | sqlServerRegistrations / sqlServers | Nie |

## <a name="microsoftazurestack"></a>Witryna Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | cloudManifestFiles | Nie |
> | Rejestracji | Tak |
> | rejestracje / customerSubscriptions | Nie |
> | rejestracje / produkty | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta batchAccounts | Tak |

## <a name="microsoftbilling"></a>Rozliczenia firmy Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | rozliczeniaKonta | Nie |
> | rozliczeniaKonta / umowy | Nie |
> | rozliczeniaKonta / rozliczeniaPeriedje | Nie |
> | rozliczeniaKonta / rozliczeniaProfile | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / billingPermissions | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / rozliczeniaRoleZapisy | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / billingRoleDefinitions | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySubskrypcje | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / tworzenieBillingRoleAssignment | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / klienci | Nie |
> | billingKonta / rozliczeniaProfile / instrukcje | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / faktury | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / faktury / arkusz cen | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / faktury / transakcje | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWysekcje / rozliczeniaWypusty | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje / rozliczeniaRoleAssignments | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWykreślenia / rozliczeniaRoleDefinitions | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWysekcje / fakturySubskrypty | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje / tworzenieBillingRoleAssignment | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWysekcje / inicjowanieTransfer | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje / produkty | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje / produkty / transfer | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturySekcje / produkty / aktualizacjaAutoRenew | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWysekcje / transakcje | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / fakturyWysekcje / przelewy | Nie |
> | rozliczeniaKonta / RozliczeniaProfile / patchOperacje | Nie |
> | billingKonta / rozliczeniaProfile / paymentMetody | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / zasady | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / arkusz cen | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / pricesheetDownloadOperations | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / produkty | Nie |
> | rozliczeniaKonta / rozliczeniaProfile / transakcje | Nie |
> | rozliczeniaKonta / rozliczeniaZapisyrolasy | Nie |
> | rozliczeniaKonta / rozliczeniaRoleDefinitions | Nie |
> | rozliczeniaKonta / rozliczeniaSubskrypcje | Nie |
> | rozliczeniaKonta / rozliczeniaSubskrypcje / faktury | Nie |
> | rozliczeniaKonta / tworzenieZaliczanie | Nie |
> | rozliczeniaKonta / tworzenieWykonanie fakturOperacje | Nie |
> | rozliczeniaKonta / klienci | Nie |
> | rozliczeniaKonta / klienci / rozliczeniaPeriedje | Nie |
> | rozliczeniaKonta / klienci / fakturySubskrypcje | Nie |
> | rozliczeniaKonta / klienci / inicjująTransfer | Nie |
> | rozliczeniaKonta / klienci / zasady | Nie |
> | rozliczeniaKonta / klienci / produkty | Nie |
> | rozliczeniaKonta / klienci / transakcje | Nie |
> | rozliczeniaKonta / klienci / przelewy | Nie |
> | rozliczeniaKonta / działy | Nie |
> | rozliczeniaKonta / rejestracjaKonta ścięcie | Nie |
> | rozliczeniaKonta / faktury | Nie |
> | rozliczeniaKonta / fakturaSekcje | Nie |
> | rozliczeniaKonta / fakturaSekcje / billingSubscriptionMoveOperations | Nie |
> | rozliczeniaKonta / fakturySekcje / fakturySubskrypcje | Nie |
> | rozliczeniaKonta / fakturySekcje / fakturySubskrypcje / przelew | Nie |
> | rozliczeniaKonta / fakturaSekcje / podniesienie wysokości | Nie |
> | rozliczeniaKonta / fakturySekcje / inicjująTransfer | Nie |
> | rozliczeniaKonta / fakturaSekcje / patchOperacje | Nie |
> | rozliczeniaKonta / fakturaSekcje / productMoveOperations | Nie |
> | rozliczeniaKonta / fakturySekcje / produkty | Nie |
> | rozliczeniaKonta / fakturySekcje / produkty / transfer | Nie |
> | rozliczeniaKonta / fakturaSekcje / produkty / aktualizacjaAutoRenew | Nie |
> | rozliczeniaKonta / fakturySekcje / transakcje | Nie |
> | rozliczeniaKonta / fakturySekcje / przelewy | Nie |
> | rozliczeniaKonta / wierszeKredyt | Nie |
> | rozliczeniaKonta / patchOperacje | Nie |
> | rozliczeniaKonta / płatnośćMetody | Nie |
> | rozliczeniaKonta / produkty | Nie |
> | rozliczeniaKonta / transakcje | Nie |
> | okresy rozliczeniowe | Nie |
> | rozliczeniaWydarzenia | Nie |
> | billingNierowadstwo | Nie |
> | rozliczeniaRoleZasyty | Nie |
> | rozliczeniaZapomnianiarol | Nie |
> | tworzenieZastawieniaRolassignment | Nie |
> | Działów | Nie |
> | enrollmentKonta | Nie |
> | Faktury | Nie |
> | Transfery | Nie |
> | transfery / acceptTransfer | Nie |
> | transfery / spadekTransfer | Nie |
> | transfery / operacjaStatus | Nie |
> | transfery / validateTransfer | Nie |
> | validateAddress | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | mapaApis | Tak |
> | updateCommunicationPreference | Nie |

## <a name="microsoftblockchain"></a>Łańcuch bloków firmy Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | członkowie łańcucha bloków | Tak |
> | cordaPoczęcie | Tak |
> | Obserwatorów | Tak |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Usługi TokenServices | Tak |
> | TokenServices / BlockchainNetworks | Nie |
> | TokenServices / Grupy | Nie |
> | TokenServices / Grupy / Konta | Nie |
> | TokenServices / TokenTemplates | Nie |

## <a name="microsoftblueprint"></a>Plan firmy Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | planyAsigny | Nie |
> | planySpisy / przydziałOperacje | Nie |
> | planyAssignments / operacje | Nie |
> | Plany | Nie |
> | plany / artefakty | Nie |
> | plany / wersje | Nie |
> | plany / wersje / artefakty | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | botUsługi | Tak |
> | botSługi / kanały | Nie |
> | botSługi / połączenia | Nie |
> | języki | Nie |
> | szablonów | Nie |

## <a name="microsoftcache"></a>Pamięć podręczna firmy Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Redis | Tak |

## <a name="microsoftcapacity"></a>Pojemność firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appliedReservations (Rezerwacje) | Nie |
> | autoQuotaIncrease | Nie |
> | CalculateExchange (Zmiany obliczania) | Nie |
> | obliczcena | Nie |
> | obliczaćzakcena z zakupem | Nie |
> | Katalogi | Nie |
> | reklamaReservationOrders | Nie |
> | Exchange | Nie |
> | placeZakrzzakędowy zakup | Nie |
> | rezerwacja Ramiarki | Nie |
> | rezerwacjaOrders / calculateRefund | Nie |
> | rezerwacja Ramiony / scalanie | Nie |
> | rezerwacjaDery / rezerwacje | Nie |
> | rezerwacjaOrdery / rezerwacje / rewizje | Nie |
> | rezerwacja Ramiarki / zwrot | Nie |
> | rezerwacjaOrders / split | Nie |
> | rezerwacja Ramiarki / swap | Nie |
> | Rezerwacje | Nie |
> | resourceProviders (Dostawcy zasobów) | Nie |
> | zasoby | Nie |
> | validateReservationOrder | Nie |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Zestawy CdnWebApplicationFirewallManagedRuleSets | Nie |
> | CdnWebApplicationFirewallPolicies | Tak |
> | edgenodes (własno | Nie |
> | Profile | Tak |
> | profile / punkty końcowe | Tak |
> | profile / punkty końcowe / customdoains | Nie |
> | profile / punkty końcowe / grupy origin | Nie |
> | profile / punkty końcowe / początki | Nie |
> | validateProbe (validateProbe) | Nie |

## <a name="microsoftcertificateregistration"></a>Rejestracja certyfikatów firmy Microsoft.Certificate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | CertyfikatY | Tak |
> | certyfikatyOrdery / certyfikaty | Nie |
> | validateCertificateRegistrationInformation | Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Możliwości | Nie |
> | nazwy domen | Tak |
> | domainNames / możliwości | Nie |
> | domainNames / internalLoadBalancers | Nie |
> | domainNames / serviceCertyfikaty | Nie |
> | domainNames / sloty | Nie |
> | domainNames / sloty / role | Nie |
> | domainNames / sloty / role / metrykaDefinitions | Nie |
> | domainNames / sloty / role / metryki | Nie |
> | moveSubscriptionResources | Nie |
> | operacyjnySystemFamilies | Nie |
> | operatingSystems (systemy operacyjne) | Nie |
> | quotas | Nie |
> | typy zasobów | Nie |
> | validateSubscriptionMoveAvailability | Nie |
> | wirtualnematyny | Tak |
> | virtualMachines / diagnostykaStawienie | Nie |
> | virtualMachines / metrykaDefinicje | Nie |
> | virtualMachines / metryki | Nie |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | classicInfrastructureZaskładnia | Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Możliwości | Nie |
> | expressRouteCrossConnections (Połączenie ekspresowe) | Nie |
> | expressRouteCrossConnections / peerings | Nie |
> | gatewaySupportedDevices | Nie |
> | networkSecurityGroups | Tak |
> | quotas | Nie |
> | reservedIps | Tak |
> | wirtualne sieci | Tak |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nie |
> | virtualNetworks / virtualNetworkPeerings | Nie |

## <a name="microsoftclassicstorage"></a>Program Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Możliwości | Nie |
> | Dysków | Nie |
> | images | Nie |
> | osImages (Zdjęcia) | Nie |
> | osPlatformImages (Plucie) | Nie |
> | publiczneMatygi | Nie |
> | quotas | Nie |
> | konta przechowywania | Tak |
> | storageKonta / blobSługi | Nie |
> | storageKonta / fileServices | Nie |
> | storageKonta / metryKadefinicje | Nie |
> | storageKonta / metryki | Nie |
> | storageKonta / kolejkiUsługi | Nie |
> | przechowywanieKonta / usługi | Nie |
> | storageKonta / usługi / diagnostykaZakzywnie | Nie |
> | storageKonta / usługi / metryKadefunkowania | Nie |
> | storageKonta / usługi / metryki | Nie |
> | storageKonta / tabelaUsługi | Nie |
> | przechowywanieKonta / vmImages | Nie |
> | vmImages (zdjęcia wirtualne) | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak |

## <a name="microsoftcommerce"></a>Usługa Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | RateCard | Nie |
> | UsageAggregates (Agregates) | Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | availabilitySets | Tak |
> | zestawy descryption | Tak |
> | Dysków | Tak |
> | Galerie | Tak |
> | galerie / aplikacje | Nie |
> | galerie / aplikacje / wersje | Nie |
> | galerie / zdjęcia | Nie |
> | galerie / obrazy / wersje | Nie |
> | grupy hostów | Tak |
> | hostGrupy / hosty | Tak |
> | images | Tak |
> | proximityPlacementGrupy | Tak |
> | restorePointCollections (RestorePointCollections) | Tak |
> | restorePointCollections / restorePoints | Nie |
> | sharedVMWysekcje | Tak |
> | sharedVMWysekcje / wersje | Nie |
> | sharedVMImages | Tak |
> | sharedVMImages / wersje | Nie |
> | Migawki | Tak |
> | sshPublicKeys (Klucze publiczne) | Tak |
> | wirtualnematyny | Tak |
> | virtualMachines / rozszerzenia | Tak |
> | virtualMachines / metrykaDefinicje | Nie |
> | zestawy virtualMachineScaleSets | Tak |
> | virtualMachineScaleSets / rozszerzenia | Nie |
> | virtualMachineScaleSets / siećIntertwy | Nie |
> | virtualMachineScaleSets / publicIPAddresses | Nie |
> | virtualMachineScaleSets / virtualMachines | Nie |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nie |

## <a name="microsoftconsumption"></a>Zużycie przez firmę Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Zagregowana pochówek | Nie |
> | Salda | Nie |
> | Budżety | Nie |
> | Opłaty | Nie |
> | Znaczniki kosztów | Nie |
> | Kredyty | Nie |
> | zdarzenia | Nie |
> | Prognozy | Nie |
> | Wiele | Nie |
> | Rynkach | Nie |
> | Arkusze cen | Nie |
> | Produktów | Nie |
> | Rezerwacjeszczetółki | Nie |
> | RezerwacjeZamieczenia | Nie |
> | RezerwacjeSummaries | Nie |
> | RezerwacjeTransakcje | Nie |
> | Tagi | Nie |
> | Najemców | Nie |
> | Warunki | Nie |
> | Szczegóły użycia | Nie |

## <a name="microsoftcontainerinstance"></a>Insstancja kontenera firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | kontenerGrupy | Tak |
> | serwisAssociationLinks | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Rejestrów | Tak |
> | rejestry / agentPools | Tak |
> | rejestry / buduje | Nie |
> | rejestry / buduje / anulują | Nie |
> | rejestry / buduje / getLogLink | Nie |
> | rejestry / budowanieZadań | Tak |
> | rejestry / buildTasks / kroki | Nie |
> | rejestry / eventGridFilters | Nie |
> | rejestry / generateCredentials | Nie |
> | rejestry / getBuildSourceUploadUrl | Nie |
> | rejestry / GetCredentials | Nie |
> | rejestry / importImage | Nie |
> | rejestry / privateEndpointConnectionProxies | Nie |
> | rejestry / privateEndpointConnectionProxies / validate | Nie |
> | rejestry / privateEndpointConnections | Nie |
> | rejestry / privateLinkResources | Nie |
> | rejestry / queueBuild | Nie |
> | rejestry / regenerateCredential | Nie |
> | rejestry / regenerateCredentials | Nie |
> | rejestry / replikacje | Tak |
> | rejestry / biegi | Nie |
> | rejestry / biegi / anulować | Nie |
> | rejestry / harmonogramRun | Nie |
> | rejestry / zakresMapy | Nie |
> | rejestry / taskRuns | Tak |
> | rejestry / zadania | Tak |
> | rejestry / żetony | Nie |
> | rejestry / aktualizacjaPolityka | Nie |
> | rejestry / hook | Tak |
> | rejestry / webhooks / getCallbackConfig | Nie |
> | rejestry / webhooks / ping | Nie |

## <a name="microsoftcontainerservice"></a>Usługa kontenera firmy Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | kontenerSługi | Tak |
> | zarządzaneKlustery | Tak |
> | openShiftManagedClusters | Tak |

## <a name="microsoftcostmanagement"></a>Zarządzanie kosztami firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Alerty | Nie |
> | Rozliczeniakonta | Nie |
> | Budżety | Nie |
> | Połączenia w chmurze | Nie |
> | Łączniki | Tak |
> | Działy | Nie |
> | Wymiary | Nie |
> | Konta enrollmentaccounts | Nie |
> | Wywozu | Nie |
> | Zewnętrzne rachunki billingowe | Nie |
> | Zewnętrzne Rachunki / Alerty | Nie |
> | Zewnętrzne Rachunki / Wymiary | Nie |
> | Zewnętrzne Rachunki / Prognoza | Nie |
> | Zewnętrzne rachunki / kwerenda | Nie |
> | Zewnętrznesubskrypcje | Nie |
> | ZewnętrzneSubskrypcje / Alerty | Nie |
> | Zewnętrznesubskrypcje / wymiary | Nie |
> | Zewnętrznesubskrypcje / Prognoza | Nie |
> | Zewnętrznesubskrypcje / Zapytanie | Nie |
> | Prognoza | Nie |
> | Zapytanie | Nie |
> | zarejestruj | Nie |
> | Reportconfigs (Reportconfigs) | Nie |
> | Raporty | Nie |
> | Ustawienia | Nie |
> | showbackRules (reguły showback) | Nie |
> | Widoki | Nie |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox (Skrzynka z blokadą klienta firmy Microsoft)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Żądania | Nie |

## <a name="microsoftcustomproviders"></a>Usługi niestandardowe firmy Microsoft.Custom

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Stowarzyszenia | Nie |
> | resourceProviders (Dostawcy zasobów) | Tak |

## <a name="microsoftdatabox"></a>Skrzynka z danymi firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Zadania | Tak |

## <a name="microsoftdataboxedge"></a>Plik Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DataBoxEdgeUrządzenia | Tak |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | obszary robocze | Tak |
> | obszary robocze / dbPrzesienia robocze | Nie |
> | obszary robocze / magazynSzyfracja | Nie |
> | obszary robocze / virtualNetworkPeerings | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Katalogi | Tak |
> | datacatalogs (przetwarzanie danych) | Tak |
> | datacatalogs / datasources | Nie |
> | datacatalogs / datasources / skany | Nie |
> | datacatalogs / datasources / skany / zestawy danych | Nie |
> | datacatalogs / datasources / skany / wyzwalacze | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | daneFactory | Tak |
> | dataFactory / diagnostykaStawienie | Nie |
> | dataFactory / metryKadefinicje | Nie |
> | dataFactorySchema | Nie |
> | Fabryk | Tak |
> | fabryki / integracjaRuntimes | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak |
> | konta / dataLakeStoreKony | Nie |
> | konta / magazynKonta | Nie |
> | konta / przechowywanieKonta / kontenery | Nie |
> | konta / transferAnalyticsJedny | Nie |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak |
> | konta / eventGridFilters | Nie |
> | konta / firewallRules | Nie |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Tak |
> | usługi / projekty | Tak |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak |
> | rachunki / udziały | Nie |
> | rachunki / udziały / zbiory danych | Nie |
> | rachunki / akcje / zaproszenia | Nie |
> | rachunki / akcje / dostawcysharesubscriptions | Nie |
> | rachunki / udziały / synchronizacjaSwilki | Nie |
> | rachunki / sharesubscriptions | Nie |
> | konta / sharesubscriptions / consumerSourceDataSets | Nie |
> | rachunki / sharesubscriptions / mapy zestawów danych | Nie |
> | rachunki / sharesubscriptions / wyzwalacze | Nie |

## <a name="microsoftdbformariadb"></a>Baza danych Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Serwerów | Tak |
> | serwery / doradcy | Nie |
> | serwery / klucze | Nie |
> | serwery / privateEndpointConnectionProxies | Nie |
> | serwery / privateEndpointConnections | Nie |
> | serwery / privateLinkResources | Nie |
> | serwery / queryTekty | Nie |
> | serwery /odzyskiwalneSerwery | Nie |
> | serwery / topQueryStatystyka | Nie |
> | serwery / virtualNetworkRules | Nie |
> | serwery / waitStatistics | Nie |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Serwerów | Tak |
> | serwery / doradcy | Nie |
> | serwery / klucze | Nie |
> | serwery / privateEndpointConnectionProxies | Nie |
> | serwery / privateEndpointConnections | Nie |
> | serwery / privateLinkResources | Nie |
> | serwery / queryTekty | Nie |
> | serwery /odzyskiwalneSerwery | Nie |
> | serwery / topQueryStatystyka | Nie |
> | serwery / virtualNetworkRules | Nie |
> | serwery / waitStatistics | Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Servergroups | Tak |
> | Serwerów | Tak |
> | serwery / doradcy | Nie |
> | serwery / klucze | Nie |
> | serwery / privateEndpointConnectionProxies | Nie |
> | serwery / privateEndpointConnections | Nie |
> | serwery / privateLinkResources | Nie |
> | serwery / queryTekty | Nie |
> | serwery /odzyskiwalneSerwery | Nie |
> | serwery / topQueryStatystyka | Nie |
> | serwery / virtualNetworkRules | Nie |
> | serwery / waitStatistics | Nie |
> | serweryv2 | Tak |
> | singleServers | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | artifactSources | Tak |
> | Wdrożenia | Tak |
> | serwisTopologie | Tak |
> | serviceTopologie / usługi | Tak |
> | serviceTopologie / usługi / usługiJednostki | Tak |
> | kroki | Tak |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopWirtualizacja

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | grupy aplikacji | Tak |
> | applicationgroups / aplikacje | Nie |
> | applicationgroups / komputery stacjonarne | Nie |
> | applicationgroups / startmenuitems | Nie |
> | hostpoole | Tak |
> | hostpools / hosty sesji | Nie |
> | hostpools / sessionhosts / usersessions | Nie |
> | hostpools / usersessions | Nie |
> | obszary robocze | Tak |

## <a name="microsoftdevices"></a>Urządzenia firmy Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Baseny elastyczne | Tak |
> | Baseny elastyczne / IotHubTenants | Tak |
> | ElasticPools / IotHubTenants / securitySettings | Nie |
> | IotHubs ( IotHubs ) | Tak |
> | IotHubs / eventGridFilters | Nie |
> | IotHubs / securitySettings | Nie |
> | Usługi inicjowania obsługi administracyjnej | Tak |
> | Zwyczaje | Nie |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Rurociągów | Tak |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | kontrolery | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | centra laboratoryjne | Tak |
> | Labs | Tak |
> | laboratoria / środowiska | Tak |
> | laboratoria / serwisRunners | Tak |
> | laboratoria / wirtualneMatyny | Tak |
> | Harmonogramy | Tak |

## <a name="microsoftdocumentdb"></a>Baza danych microsoft.documentdb

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | nazwy konta danych | Nie |
> | konta danych | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Domen | Tak |
> | domeny / domainOwnershipIdentifiers | Nie |
> | generujeSsoRequest | Nie |
> | topLevelDomains | Nie |
> | sprawdzanie poprawnościInformacji DomainRegistrationInformation | Nie |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | lcsprojects | Nie |
> | lcsprojects / chmuryrozwają | Nie |
> | lcsprojects / złącza | Nie |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Tak |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Domen | Tak |
> | domeny / tematy | Nie |
> | eventSubscriptions | Nie |
> | extensionTopics | Nie |
> | przestrzenie partnername | Tak |
> | partnerNamespaces / eventKanały | Nie |
> | rejestracje partnerów | Tak |
> | partnerTopics | Tak |
> | partnerTopics / eventSubscriptions | Nie |
> | systemTopy | Tak |
> | systemTopics / eventSubscriptions | Nie |
> | Tematy | Tak |
> | topicTypy | Nie |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Klastrów | Tak |
> | Obszary nazw | Tak |
> | przestrzenie nazw / zasady autoryzacji | Nie |
> | przestrzenie nazw / disasterrecoveryconfigs | Nie |
> | przestrzenie nazw / eventhubs | Nie |
> | przestrzenie nazw / eventhubs / zasady autoryzacji | Nie |
> | przestrzenie nazw / eventhubs / grupy konsumentów | Nie |
> | przestrzenie nazw / zasady sieciowe | Nie |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary nazw | Tak |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | funkcjaProvidery | Nie |
> | funkcje | Nie |
> | dostawców | Nie |
> | subskrypcjaFeatureRegistrations | Nie |

## <a name="microsoftgallery"></a>Galeria Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Zarejestrować | Nie |
> | budynki galerii | Nie |
> | generateartifactaccessuri | Nie |
> | myareas | Nie |
> | myareas / obszary | Nie |
> | myareas / obszary / obszary | Nie |
> | myareas / obszary / obszary / galleryitems | Nie |
> | myareas / obszary / galleryitems | Nie |
> | myareas / galleryitems | Nie |
> | zarejestruj | Nie |
> | zasoby | Nie |
> | retrieveresourcesbyid | Nie |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak |

## <a name="microsoftguestconfiguration"></a>Konfiguracja witryny Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta automanagedAccounts | Tak |
> | autoManagedVmConfigurationProfiles | Tak |
> | konfiguracjaProfileAssignments | Nie |
> | guestConfigurationAssignments (Konfiguracje gości) | Nie |
> | Oprogramowania | Nie |
> | softwareUpdateProfile | Nie |
> | oprogramowanieUpdy | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hanaInstances | Tak |
> | sapMonitors (SapMonitors) | Tak |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | dedykowane moduły | Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Klastrów | Tak |
> | klastry / aplikacje | Nie |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Tak |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Maszyny | Tak |
> | maszyny / rozszerzenia | Tak |

## <a name="microsofthybriddata"></a>Microsoft.HybridDana

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | menedżery danych | Tak |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Składniki | Tak |
> | siećZakskopy | Tak |

## <a name="microsoftimportexport"></a>Microsoft.ImportEksport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Zadania | Tak |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnostykaStawienie | Nie |
> | diagnostykaKategorie | Nie |

## <a name="microsoftiotcentral"></a>Centrum IoT Firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appTemplates | Nie |
> | IoTApps | Tak |

## <a name="microsoftiotspaces"></a>Przestrzenie IoT firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Graph | Tak |

## <a name="microsoftkeyvault"></a>Usługa Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | deletedVaults | Nie |
> | Puli hsm | Tak |
> | Sklepienia | Tak |
> | skarbce / dostępPolicies | Nie |
> | przechowalnia / eventGridFilters | Nie |
> | skarbce / sekrety | Nie |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | connectedClusters (łączonykwierarze) | Tak |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Klastrów | Tak |
> | klastry / dołączone konfiguracje baz danych | Nie |
> | klastry / bazy danych | Nie |
> | klastry / bazy danych / połączenia danych | Nie |
> | klastry / bazy danych / eventhubconnections | Nie |
> | klastry / bazy danych / principalassignments | Nie |
> | klastry / połączenia danych | Nie |
> | klastry / główne przypisania | Nie |
> | klastry / współużytkowania | Nie |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta labaccounts | Tak |
> | użytkownicy | Nie |

## <a name="microsoftlogic"></a>Logika firmy Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hostingŚrodowiska | Tak |
> | integrationKontacje | Tak |
> | integracjaServiceEnvironments | Tak |
> | integrationServiceEnvironments / managedApis | Tak |
> | isolatedŚrodowiska | Tak |
> | Przepływy pracy | Tak |

## <a name="microsoftmachinelearning"></a>Uczenie się maszyn firmy Microsoft.Machine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zobowiązaniePlany | Tak |
> | Webservices | Tak |
> | Obszary robocze | Tak |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | obszary robocze | Tak |
> | obszary robocze / obliczenia | Nie |
> | obszary robocze / eventGridFilters | Nie |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applyUpdates (stosowaniewzszy i poucza | Nie |
> | konfiguracjaAssignments | Nie |
> | konfiguracje obsługi | Tak |
> | aktualizacje | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Tożsamości | Nie |
> | użytkownikAssignedIdentities | Tak |

## <a name="microsoftmanagedservices"></a>Usługi zarządzane przez firmę Microsoft.Managed

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions (RejestracjeDefinicji) | Nie |
> | rejestracjaAssignments | Nie |
> | rejestracjaDefinicje | Nie |

## <a name="microsoftmanagement"></a>Zarządzanie microsoftem

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | getEntities (władać) | Nie |
> | zarządzanieGrupy | Nie |
> | managementGrupy / ustawienia | Nie |
> | zasoby | Nie |
> | startTenantBackfill | Nie |
> | tenantBackfillStatus | Nie |

## <a name="microsoftmaps"></a>Microsoft.Maps (Mapy firmy Microsoft)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak |
> | konta / eventGridFilters | Nie |
> | konta / privateAtlases | Tak |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Oferuje | Nie |
> | offerTypes (Typy oferty) | Nie |
> | offerTypes / wydawcy | Nie |
> | offerTypes / wydawcy / oferty | Nie |
> | offerTypes / wydawcy / oferty / plany | Nie |
> | offerTypes / wydawcy / oferty / plany / umowy | Nie |
> | offerTypes / wydawcy / oferty / plany / configs | Nie |
> | offerTypes / wydawcy / oferty / plany / configs / importImage | Nie |
> | privategalleryitems | Nie |
> | privateStoreClient (Magazyn prywatnyClient) | Nie |
> | prywatneSklepy | Nie |
> | privateStores / oferty | Nie |
> | Produktów | Nie |
> | Wydawców | Nie |
> | wydawcy / oferty | Nie |
> | wydawcy / oferty / poprawki | Nie |
> | zarejestruj | Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | klasycznyDevSługa | Tak |
> | updateCommunicationPreference | Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Umów | Nie |
> | rodzaje ofert | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | usługi mediasce | Tak |
> | mediaservices / accountFilters | Nie |
> | usługi mediasce / aktywa | Nie |
> | mediaservices / assets / assetFiltry | Nie |
> | mediaservices / contentKeyPolicies | Nie |
> | mediaservices / eventGridFilters | Nie |
> | mediaservices / liveEventOperations | Nie |
> | mediaservices / liveEvents | Tak |
> | mediaservices / liveEvents / liveOutputs | Nie |
> | mediaservices / liveOutputOperations | Nie |
> | mediaservices / mediaGrafy | Nie |
> | mediaservices / streamingEndpointOperations | Nie |
> | mediaservices / streamingEndpoints | Tak |
> | mediaservices / streamingLocators | Nie |
> | mediaservices / streamingPolicies | Nie |
> | usługi medialne / przekształca | Nie |
> | mediaservices / przekształca / praca | Nie |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appClusters | Tak |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | ocenyProjekty | Tak |
> | migrowanieprojektów | Tak |
> | moveKollections (MoveCollections) | Tak |
> | Projektów | Tak |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality (Reality mieszane firmy Microsoft.MixedReality)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | holographicsBroadcastKontacje | Tak |
> | obiektUnderstandingKontacje | Tak |
> | konta zdalnegorenderingu | Tak |
> | przestrzenne Konta na kontach | Tak |

## <a name="microsoftnetapp"></a>Microsoft.NetApp (Microsoft.NetApp)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | netAppKony | Tak |
> | netAppKony / kontoBackups | Nie |
> | netAppKony / pojemnośćPuly | Tak |
> | netAppKony / pojemnośćPuly / woluminy | Tak |
> | netAppAccounts / pojemnośćPuly / woluminy / migawki | Tak |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationGateways | Tak |
> | aplikacjaGatewayWebApplicationFirewallPolicies | Tak |
> | applicationSecurityGroups | Tak |
> | azureFirewallFqdnTags | Nie |
> | azureFirewalls | Tak |
> | bastionHosts | Tak |
> | bgpSpołeczności usług | Nie |
> | Połączenia | Tak |
> | ddosCustomPolicies | Tak |
> | plany ochrony ddos | Tak |
> | dnsOperationStatuses | Nie |
> | strefy dns | Tak |
> | dnszones / A | Nie |
> | dnszones / AAAA | Nie |
> | dnszones / wszystkie | Nie |
> | dnszones / CAA | Nie |
> | dnszones / CNAME | Nie |
> | dnszones / MX | Nie |
> | dnszones / NS | Nie |
> | dnszones / PTR | Nie |
> | dnszones / recordsets | Nie |
> | dnszones / SOA | Nie |
> | dnszones / SRV | Nie |
> | dnszones / TXT | Nie |
> | expressRouteCircuits | Tak |
> | expressRouteCrossConnections (Połączenie ekspresowe) | Tak |
> | expressRouteGateways | Tak |
> | expressRoutePorts | Tak |
> | expressRouteServiceProviders expressRouteServiceProviders expressRouteServiceProviders expressRo | Nie |
> | firewallPolicies | Tak |
> | przednie drzwi | Tak |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nie |
> | frontdoorWebApplicationFirewallPolicies | Tak |
> | wniosek o źródła getDnsResourceReference | Nie |
> | internalNotify | Nie |
> | obciążeniaBalancery | Tak |
> | localNetworkGateways | Tak |
> | natGateways ( natGateways ) | Tak |
> | siećIntentPolicies | Tak |
> | siećPowierzchnie | Tak |
> | networkProfiles | Tak |
> | networkSecurityGroups | Tak |
> | networkWatchers (obserwatorzy sieci) | Tak |
> | networkWatchers / connectionMonitors | Tak |
> | networkWatchers / soczewki | Tak |
> | networkWatchers / pingMeshes | Tak |
> | p2sVpnGateways | Tak |
> | privateDnsOperationStatuss | Nie |
> | prywatneDnsZones | Tak |
> | privateDnsZones / A | Nie |
> | prywatneDnsZones / AAAA | Nie |
> | privateDnsZones / wszystkie | Nie |
> | privateDnsZones / CNAME | Nie |
> | privateDnsZones / MX | Nie |
> | privateDnsZones / PTR | Nie |
> | privateDnsZones / SOA | Nie |
> | privateDnsZones / SRV | Nie |
> | privateDnsZones / TXT | Nie |
> | privateDnsZones / virtualNetworkLinks | Tak |
> | privateEndpoints | Tak |
> | privateLinkServices (usługi prywatnelink) | Tak |
> | publicYSadresy | Tak |
> | publicIPPrefixes | Tak |
> | routeFiltry | Tak |
> | routeTables (routetables) | Tak |
> | serviceEndpointPolicies | Tak |
> | trafficManagerGeographicHierarchies | Nie |
> | profile trafficmanager | Tak |
> | profili / heatMaps | Nie |
> | klawisze trafficManagerUserMetricsKeys | Nie |
> | wirtualneshuby | Tak |
> | virtualNetworkGateways | Tak |
> | wirtualne sieci | Tak |
> | virtualNetworkTaps (Wirtualnetapy sieciowe) | Tak |
> | wirtualne wich | Tak |
> | vpnGateways | Tak |
> | vpnWitryny | Tak |
> | webApplicationFirewallPolicies | Tak |

## <a name="microsoftnotebooks"></a>Notesy firmy Microsoft.Notess

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | NotebookProxies (NotebookProxies) | Nie |

## <a name="microsoftnotificationhubs"></a>Witryna Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary nazw | Tak |
> | przestrzenie nazw / notificationHubs | Tak |

## <a name="microsoftobjectstore"></a>Sklep Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | osNamespaces | Tak |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | HyperVSites | Tak |
> | Miejsca importu | Tak |
> | Miejsca serwera | Tak |
> | Miejsca wirtualne | Tak |

## <a name="microsoftoperationalinsights"></a>Dane operacyjne firmy Microsoft.Operational

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Klastrów | Tak |
> | łącze celowe | Nie |
> | pamięć masowaInsightConfigs | Nie |
> | obszary robocze | Tak |
> | obszary robocze / daneRozprzemysł | Nie |
> | obszary robocze / daneŹródła | Nie |
> | obszary robocze / linkedServices | Nie |
> | obszary robocze / linkedStorageKony | Nie |
> | obszary robocze / kwerenda | Nie |
> | obszary robocze / scopedPrivateLinkProxies | Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zarządzaniami | Nie |
> | konfiguracje zarządzania | Tak |
> | rozwiązania | Tak |
> | widoki | Tak |

## <a name="microsoftpeering"></a>Komunikacja równorzędna firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | legacyPeerings | Nie |
> | peerAsns (równorzędny) | Nie |
> | komunikacja równorzędna | Tak |
> | peeringServiceCountries (nie można ego) | Nie |
> | usługi równorzędneProvidery | Nie |
> | usługi komunikacji równorzędnej | Tak |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | politykaWzdowyzdowy | Nie |
> | policyMetadata (Meada polityki) | Nie |
> | policyStates | Nie |
> | policyTrackedResources | Nie |
> | środki zaradcze | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konsole programu | Nie |
> | pulpity nawigacyjne | Tak |
> | userSettings | Nie |

## <a name="microsoftpowerbi"></a>Program Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | workspaceKolekcje | Tak |

## <a name="microsoftpowerbidedicated"></a>Program Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Pojemności | Tak |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Tak |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary robocze | Tak |

## <a name="microsoftrecoveryservices"></a>Usługi Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | backupProtectedItems | Nie |
> | Sklepienia | Tak |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary nazw | Tak |
> | przestrzenie nazw / zasady autoryzacji | Nie |
> | przestrzenie nazw / połączenia hybrydowe | Nie |
> | przestrzenie nazw / połączenia hybrydowe / zasady autoryzacji | Nie |
> | przestrzenie nazw / wcfrelays | Nie |
> | przestrzenie nazw / wcfrelays / autoryzacjarule | Nie |

## <a name="microsoftremoteapp"></a>Aplikacja Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accounts | Nie |
> | Kolekcje | Tak |
> | kolekcje / aplikacje | Nie |
> | kolekcje / securityprincipals | Nie |
> | templateImages | Nie |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Kwerendy | Tak |
> | resourceChangeDetails | Nie |
> | zmiany zasobów | Nie |
> | zasoby | Nie |
> | zasobyHistoria | Nie |
> | subskrypcjeStatus | Nie |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | dostępnośćStatusy | Nie |
> | childDostępnośćStatusy | Nie |
> | childResources | Nie |
> | nowe | Nie |
> | zdarzenia | Nie |
> | wpływResources | Nie |
> | metadane | Nie |
> | powiadomienia | Nie |

## <a name="microsoftresources"></a>Zasoby firmy Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Wdrożeń | Nie |
> | wdrożenia / operacje | Nie |
> | skrypty rozmieszczenia | Tak |
> | deploymentScripts / dzienniki | Nie |
> | Linki | Nie |
> | notifyResourceJobs | Nie |
> | dostawców | Nie |
> | zasobyGrupy | Nie |
> | Subskrypcji | Nie |
> | Najemców | Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Tak |
> | saasresources | Nie |

## <a name="microsoftsearch"></a>Wyszukiwanie microsoft.search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zasobyHealthMetadata | Nie |
> | wyszukiwanie Usługi | Tak |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Nie |
> | zaawansowaneThreatProtectionSettings | Nie |
> | alerts | Nie |
> | dozwolone Połączenia | Nie |
> | aplikacjaWhitelistings | Nie |
> | assessmentMetadata | Nie |
> | oceny | Nie |
> | autoDismissAlertsRules | Nie |
> | Automatyki | Tak |
> | Automatyczne ustawianie obsługi | Nie |
> | Zgodność z przepisami | Nie |
> | dataCollectionAgents | Nie |
> | deviceSecurityGroups (Grupy zabezpieczeń) | Nie |
> | discoveredSecuritySolutions | Nie |
> | zewnętrzneSecuritySolutions | Nie |
> | InformacjeProtectionPolicies | Nie |
> | rozwiązania iotSecuritySolutions | Tak |
> | iotSecuritySolutions / analyticsModels | Nie |
> | iotSecuritySolutions / analyticsModels / zagregowaneAlerts | Nie |
> | iotSecuritySolutions / analyticsModels / zagregowaneZamiany | Nie |
> | jitNetworkAccessPolicies | Nie |
> | networkData | Nie |
> | zasady | Nie |
> | ceny | Nie |
> | zgodność z przepisamiStandardy | Nie |
> | zgodność z przepisamiStandardy / regulatoryComplianceControls | Nie |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nie |
> | secureScoreControlDefinitions | Nie |
> | secureScoreControls | Nie |
> | secureScores (bezpieczneszechanie) | Nie |
> | secureScores / secureScoreControls | Nie |
> | securityContacts | Nie |
> | zabezpieczeniaRozwiązywanie | Nie |
> | zabezpieczeniaSolutionsReferenceData | Nie |
> | zabezpieczeniaStatusy | Nie |
> | zabezpieczeniaStatusesSummaries | Nie |
> | serverVulnerabilityAssessments serverVulnerabilityassessments serverVulnerabilityAssessments server | Nie |
> | settings | Nie |
> | podsypów | Nie |
> | zadania | Nie |
> | Topologii | Nie |
> | miejsca roboczeStawienia | Nie |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph (Microsoft.SecurityGraph)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnostykaStawienie | Nie |
> | diagnostykaKategorie | Nie |

## <a name="microsoftsecurityinsights"></a>Witryny zabezpieczeń firmy Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Agregacji | Nie |
> | alertRules (alertRules) | Nie |
> | alertRuleTemplates | Nie |
> | zakładki | Nie |
> | Przypadkach | Nie |
> | połączenia danych | Nie |
> | Wymagania kontroli połączeń danych | Nie |
> | Podmioty | Nie |
> | entityQueries (Wyeksle) | Nie |
> | Incydentów | Nie |
> | OfficeConsents | Nie |
> | settings | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary nazw | Tak |
> | przestrzenie nazw / zasady autoryzacji | Nie |
> | przestrzenie nazw / disasterrecoveryconfigs | Nie |
> | przestrzenie nazw / eventgridfilters | Nie |
> | przestrzenie nazw / zasady sieciowe | Nie |
> | przestrzenie nazw / kolejki | Nie |
> | przestrzenie nazw / kolejki / zasady autoryzacji | Nie |
> | przestrzenie nazw / tematy | Nie |
> | przestrzenie nazw / tematy / zasady autoryzacji | Nie |
> | przestrzenie nazw / tematy / subskrypcje | Nie |
> | przestrzenie nazw / tematy / subskrypcje / zasady | Nie |
> | premiumMessagingRegje | Nie |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Tak |
> | Klastrów | Tak |
> | klastry / aplikacje | Nie |
> | kontenerGrupy | Tak |
> | containerGroupSets (Zestawy grup kontenerów) | Tak |
> | edgeclusters | Tak |
> | edgeclusters / aplikacje | Nie |
> | zarządzane clusters | Tak |
> | zarządzaneklura / typy węzłów | Nie |
> | Sieci | Tak |
> | sklepy sekretowe | Tak |
> | secretstores / certyfikaty | Nie |
> | secretstores / tajemnice | Nie |
> | volumes | Tak |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Tak |
> | kontenerGrupy | Tak |
> | Bramy | Tak |
> | Sieci | Tak |
> | wpisy tajne | Tak |
> | volumes | Tak |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | rejestracje dostawców | Nie |
> | providerRegistrations / resourceTypeRegistrations | Nie |
> | Wdrożenia | Tak |

## <a name="microsoftsignalrservice"></a>Usługa Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SignalR | Tak |
> | SignalR / eventGridFilters | Nie |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SiteRecoveryVault | Tak |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hybridUseBenefits | Nie |

## <a name="microsoftsolutions"></a>Rozwiązania firmy Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationDefinitions (Zdefiniowanie aplikacji) | Tak |
> | aplikacje | Tak |
> | jitRequests | Tak |

## <a name="microsoftspoolservice"></a>Usługa Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zarejestrowaneSubskrypcje | Nie |
> | Szpule | Tak |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zarządzaneInstności | Tak |
> | zarządzaneInstności / bazy danych | Tak |
> | zarządzaneInstacja / bazy danych / backupShortTermRetentionPolicies | Nie |
> | zarządzaneInstności / bazy danych / schematy / tabele / kolumny / sensitivityLabels | Nie |
> | zarządzaneInstyty / bazy danych / podatnośćA na zagrożeniaSzewaga | Nie |
> | zarządzaneInstacji / bazy danych / podatnośćaosuty / zasady / linie bazowe | Nie |
> | zarządzaneInstekcje / szyfrowanieOtektor | Nie |
> | zarządzaneInstyki / klucze | Nie |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nie |
> | zarządzaneInstyty / podatnośćA na zagrożenia | Nie |
> | Serwerów | Tak |
> | serwery / administratorzy | Nie |
> | serwery / komunikacjaLinks | Nie |
> | serwery / bazy danych | Tak |
> | serwery / szyfrowanieProtector | Nie |
> | serwery / firewallRules | Nie |
> | serwery / klucze | Nie |
> | serwery / przywracalneDroppedDatabases | Nie |
> | serwery / usługiobiekty | Nie |
> | serwery / tdeCertificates | Nie |
> | virtualClusters (Wirtualneklustery) | Nie |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SqlVirtualMachineGrupy | Tak |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nie |
> | SqlVirtualMachines | Tak |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta przechowywania | Tak |
> | storageKonta / blobSługi | Nie |
> | storageKonta / fileServices | Nie |
> | storageKonta / kolejkiUsługi | Nie |
> | przechowywanieKonta / usługi | Nie |
> | storageKonta / usługi / metryKadefunkowania | Nie |
> | storageKonta / tabelaUsługi | Nie |
> | Zwyczaje | Nie |

## <a name="microsoftstoragecache"></a>Usługa Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Buforuje | Tak |
> | pamięci podręczne / storageTargets | Nie |
> | usageModels | Nie |

## <a name="microsoftstoragereplication"></a>Aplikacja Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | grupy replikacji | Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | usługi storageSyncServices | Tak |
> | storageSyncServices / registeredServers | Nie |
> | storageSyncServices / syncGroups | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie |
> | storageSyncServices / przepływy pracy | Nie |

## <a name="microsoftstoragesyncdev"></a>System Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | usługi storageSyncServices | Tak |
> | storageSyncServices / registeredServers | Nie |
> | storageSyncServices / syncGroups | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie |
> | storageSyncServices / przepływy pracy | Nie |

## <a name="microsoftstoragesyncint"></a>Program Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | usługi storageSyncServices | Tak |
> | storageSyncServices / registeredServers | Nie |
> | storageSyncServices / syncGroups | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie |
> | storageSyncServices / przepływy pracy | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Menedżerów | Tak |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalityka

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | streamingjobs | Tak |

## <a name="microsoftsubscription"></a>Subskrypcja firmy Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Anuluj | Nie |
> | Createsubscription | Nie |
> | Włącz | Nie |
> | zmień nazwę | Nie |
> | SubskrypcjeDefinicje | Nie |
> | Działanie subskrypcji | Nie |
> | Subskrypcji | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Środowiskach | Tak |
> | środowiska / accessPolicies | Nie |
> | środowiska / źródła zdarzeń | Tak |
> | środowiska / referenceDataSets | Tak |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | dedykowaneCloudNodes | Tak |
> | dedykowaneCloudServices | Tak |
> | wirtualnematyny | Tak |

## <a name="microsoftvnfmanager"></a>Menedżer microsoft.Vnf

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | urządzenia | Tak |
> | zarejestrowaneSubskrypcje | Nie |
> | Dostawców | Nie |
> | sprzedawcy / skus | Nie |
> | dostawców / vnfs | Nie |
> | virtualNetworkFunctionSkus | Nie |
> | vnfs ( vnfs ) | Tak |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta apiManagementAccounts | Nie |
> | apiManagementKonta / apiAcls | Nie |
> | apiManagementKonta / apis | Nie |
> | apiManagementKonta / apis / apiAcls | Nie |
> | apiManagementKonta / apis / connectionAcls | Nie |
> | apiManagementKonta / apis / połączenia | Nie |
> | apiManagementKonta / apis / połączenia / connectionAcls | Nie |
> | apiManagementKonta / apis / zlokalizowaneDedefitions | Nie |
> | apiManagementKonta / connectionAcls | Nie |
> | apiManagementKonta / połączenia | Nie |
> | stopy rozliczeniowe | Nie |
> | certyfikaty | Tak |
> | connectionGateways | Tak |
> | Połączenia | Tak |
> | zwyczajApis | Tak |
> | deletedSites | Nie |
> | hostingŚrodowiska | Tak |
> | hostingŚrodowiska / eventGridFilters | Nie |
> | hostingŚrodowiska / baseny wielozadaniowe | Nie |
> | hostingŚrodowiska / pracownicy Baseny | Nie |
> | kubeŚrodowiska | Tak |
> | publikowanieUżytnicy | Nie |
> | zalecenia | Nie |
> | zasobyHealthMetadata | Nie |
> | środowiska wykonawcze | Nie |
> | serwerFary | Tak |
> | serwerFary / eventGridFilters | Nie |
> | lokacje | Tak |
> | witryny/config  | Nie |
> | strony / eventGridFilters | Nie |
> | witryn / hostNameBindings | Nie |
> | strony / siećConfig | Nie |
> | strony / premieraddons | Tak |
> | witryny / szczeliny | Tak |
> | strony / sloty / eventGridFilters | Nie |
> | strony / sloty / hostNameBindings | Nie |
> | strony / sloty / siećConfig | Nie |
> | źródłoKontrole | Nie |
> | staicWitryny | Tak |
> | Sprawdzania poprawności | Nie |
> | verifyHostingEnvironmentVnet | Nie |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnostykaStawienie | Nie |
> | diagnostykaKategorie | Nie |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Usługi urządzenia | Tak |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Składniki | Nie |
> | komponentySummary | Nie |
> | MonitorowanieInstacji | Nie |
> | monitorInstancesSummary | Nie |
> | Monitory | Nie |
> | powiadomieniaUstawienia | Nie |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać te same dane co plik wartości oddzielonych przecinkami, pobierz [plik complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
