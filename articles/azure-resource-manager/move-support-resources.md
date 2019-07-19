---
title: Obsługa operacji przenoszenia według typu zasobów platformy Azure
description: Wyświetla listę typów zasobów platformy Azure, które można przenieść do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 22493ad7998e9014c88c79e6ac5eee3bf1216119
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226816"
---
# <a name="move-operation-support-for-resources"></a>Obsługa operacji przenoszenia dla zasobów
W tym artykule przedstawiono, czy typ zasobu platformy Azure obsługuje operację przenoszenia. Zawiera również informacje dotyczące specjalnych warunków, które należy wziąć pod uwagę podczas przeniesienia zasobu.

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft. plan](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. datacegły](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Migracja Microsoft.](#microsoftdatamigration)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Migrowanie](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. Komunikacja równorzędna](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Nie | Nie |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dzierżaw | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| actionrules | Tak | Tak |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Serwerem | Tak | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| usługa | Tak | Tak |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| configurationstores | Tak | Yes |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| apiapps | Nie | Nie |
| appidentities | Nie | Nie |
| bram | Nie | Nie |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | Tak | Yes |
| automationaccounts/konfiguracje | Tak | Yes |
| automationaccounts/elementy Runbook | Yes | Tak |

> [!IMPORTANT]
> Elementy Runbook muszą istnieć w tej samej grupie zasobów co konto usługi Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| b2cdirectories | Tak | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rejestracje | Tak | Yes |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| backupvault | Nie | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| batchaccounts | Tak | Yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| oparty | Nie | Nie |
| fileservers | Nie | Nie |
| zadania | Nie | Nie |
| Obszarów roboczych | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mapapis | Nie | Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztalk | Tak | Tak |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blockchainmembers | Tak | Tak |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | Yes | Tak |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Redis | Tak | Yes |

> [!IMPORTANT]
> Jeśli wystąpienie usługi Azure cache for Redis jest skonfigurowane przy użyciu sieci wirtualnej, nie można przenieść wystąpienia do innej subskrypcji. Zobacz [ograniczenia przenoszenia sieci wirtualnych](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| profiles | Tak | Tak |
| Profile/punkty końcowe | Tak | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificateorders | Yes | Yes |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainnames | Tak | Nie |
| virtualmachines | Tak | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Nie | Nie |
| reservedips | Nie | Nie |
| virtualnetworks | Nie | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Tak | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Tak | Tak |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | Tak | Tak |
| Dysku | Tak | Tak |
| znajduj | Nie | Nie |
| Galerie/obrazy | Nie | Nie |
| Galerie/obrazy/wersje | Nie | Nie |
| hostgroups | Nie | Nie |
| hostgroups/hosty | Nie | Nie |
| images | Yes | Tak |
| proximityplacementgroups | Nie | Nie |
| restorepointcollections | Nie | Nie |
| sharedvmimages | Nie | Nie |
| sharedvmimages/wersje | Nie | Nie |
| Migawki | Tak | Yes |
| virtualmachines | Yes | Yes |
| virtualmachines/rozszerzenia | Yes | Tak |
| virtualmachinescalesets | Tak | Tak |

> [!IMPORTANT]
> Zobacz [Virtual Machines wskazówki dotyczące przenoszenia](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| wołuje | Yes | Tak |
| rejestry/BuildTasks | Tak | Tak |
| rejestry/replikacje | Yes | Tak |
| rejestry/zadania | Tak | Tak |
| rejestry/elementy webhook | Tak | Tak |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containerservices | Nie | Nie |
| managedclusters | Nie | Nie |
| openshiftmanagedclusters | Nie | Nie |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplikacje | Tak | Tak |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| przedsiębiorstw | Yes | Tak |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| węzłów | Tak | Tak |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| zadania | Nie | Nie |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Nie | Nie |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Obszarów roboczych | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| wykazy | Tak | Yes |
| wykazy | Nie | Nie |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectionmanagers | Nie | Nie |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Pakiety | Nie | Nie |
| pakiety | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fabryki datafactors | Tak | Tak |
| fabryki | Tak | Tak |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Tak | Tak |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Tak | Yes |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Nie | Nie |
| usługi/projekty | Nie | Nie |
| czasów | Nie | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Serwerem | Tak | Yes |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Serwerem | Yes | Tak |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Nie | Nie |
| Serwerem | Tak | Tak |
| serversv2 | Tak | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | Tak | Tak |
| wprowadzanie | Tak | Yes |
| servicetopologie | Yes | Tak |
| servicetopologie/usługi | Yes | Yes |
| servicetopologie/usługi/serviceunits | Yes | Tak |
| kroki | Yes | Tak |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| elasticpools | Nie | Nie |
| elasticpools/iothubtenants | Nie | Nie |
| iothubs | Tak | Yes |
| provisioningservices | Tak | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Rejestrowanie | Nie | Nie |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Nie | Nie |
| Labs | Tak | Nie |
| laboratoria/środowiska | Tak | Yes |
| laboratoria/servicerunner | Tak | Tak |
| Labs/virtualmachines | Tak | Nie |
| Uruchamianie | Tak | Tak |

## <a name="microsoftdns"></a>microsoft.dns
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Nie | Nie |
| dnszones/a | Nie | Nie |
| dnszones/AAAA | Nie | Nie |
| dnszones/CNAME | Nie | Nie |
| dnszones/MX | Nie | Nie |
| dnszones/PTR | Nie | Nie |
| dnszones/SRV | Nie | Nie |
| dnszones/txt | Nie | Nie |
| trafficmanagerprofiles | Nie | Nie |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databaseaccounts | Tak | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domeny | Tak | Tak |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domeny | Tak | Tak |
| opisano | Tak | Tak |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| oparty | Yes | Tak |
| Przestrzeni | Yes | Yes |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hanainstances | Yes | Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| oparty | Yes | Yes |

> [!IMPORTANT]
> Klastry HDInsight można przenieść do nowej subskrypcji lub grupy zasobów. Jednak nie można przenosić między subskrypcjami, zasoby sieciowe połączone z klastrem HDInsight (na przykład sieci wirtualnej, kart interfejsu Sieciowego lub modułu równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów interfejsu Sieciowego, który jest dołączony do maszyny wirtualnej do klastra.
>
> Podczas przenoszenia klastra usługi HDInsight do nowej subskrypcji, należy najpierw przenieść innych zasobów (np. konta magazynu). Następnie przenieś klaster HDInsight samodzielnie.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Tak | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Win64 | Nie | Nie |

## <a name="microsofthybriddata"></a>Microsoft. HybridData
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagers | Tak | Tak |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| zadania | Yes | Tak |

## <a name="microsoftinsights"></a>Microsoft. Insights
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |
| actiongroups | Tak | Tak |
| activitylogalerts | Nie | Nie |
| alertrules | Tak | Yes |
| autoscalesettings | Yes | Tak |
| Komponentów | Tak | Tak |
| guestdiagnosticsettings | Nie | Nie |
| metricalerts | Nie | Nie |
| notificationgroups | Nie | Nie |
| notificationrules | Nie | Nie |
| scheduledqueryrules | Tak | Yes |
| testów internetowych | Tak | Yes |
| Otwiera | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza przydziałów [subskrypcji](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | Yes | Tak |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | Tak | Tak |
| graph | Tak | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Nie | Nie |
| magazynów | Yes | Yes |

> [!IMPORTANT]
> Magazyny kluczy używane do szyfrowania dysków nie mogą zostać przeniesione do grupy zasobów w tej samej subskrypcji lub w różnych subskrypcjach.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| oparty | Tak | Tak |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labaccounts | Nie | Nie |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Tak | Tak |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingenvironments | Nie | Nie |
| integrationaccounts | Tak | Tak |
| integrationserviceenvironments | Nie | Nie |
| isolatedenvironments | Nie | Nie |
| przebieg | Yes | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | Yes | Tak |
| WebServices | Tak | Nie |
| Obszarów roboczych | Tak | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Yes | Tak |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |
| konta/obszary robocze | Nie | Nie |
| konta/obszary robocze/projekty | Nie | Nie |
| teamaccounts | Nie | Nie |
| teamaccounts/obszary robocze | Nie | Nie |
| teamaccounts/obszary robocze/projekty | Nie | Nie |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Tak | Yes |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingaccounts | Nie | Nie |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Obszarów roboczych | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| resourceidentity | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft. Maps
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Yes | Tak |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| classicdevservices | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| MediaServices | Yes | Tak |
| MediaServices/liveevents | Tak | Yes |
| MediaServices/streamingendpoints | Tak | Tak |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| assessmentprojects | Nie | Nie |
| migrateprojects | Nie | Nie |
| projektami | Nie | Nie |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| netappaccounts | Nie | Nie |
| netappaccounts/capacitypools | Nie | Nie |
| netappaccounts/capacitypools/volumes | Nie | Nie |
| netappaccounts/capacitypools/woluminy/mounttargets | Nie | Nie |
| netappaccounts/capacitypools/volumes/snapshots | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgateways | Nie | Nie |
| applicationgatewaywebapplicationfirewallpolicies | Nie | Nie |
| applicationsecuritygroups | Tak | Tak |
| azurefirewalls | Tak | Yes |
| bastionhosts | Nie | Nie |
| Licznik | Tak | Tak |
| ddoscustompolicies | Tak | Tak |
| ddosprotectionplans | Nie | Nie |
| dnszones | Yes | Yes |
| expressroutecircuits | Nie | Nie |
| expressroutecrossconnections | Nie | Nie |
| expressroutegateways | Nie | Nie |
| expressrouteports | Nie | Nie |
| usługi frontdoor | Nie | Nie |
| frontdoorwebapplicationfirewallpolicies | Nie | Nie |
| loadbalancers | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
| localnetworkgateways | Yes | Tak |
| natgateways | Yes | Tak |
| networkintentpolicies | Tak | Yes |
| networkinterfaces | Tak | Yes |
| networkprofiles | Nie | Nie |
| networksecuritygroups | Yes | Tak |
| networkwatchers | Yes | Tak |
| networkwatchers/connectionmonitors | Tak | Tak |
| networkwatchers/soczewki | Tak | Tak |
| networkwatchers/pingmeshes | Yes | Yes |
| p2svpngateways | Nie | Nie |
| privatednszones | Yes | Tak |
| privatednszones/virtualnetworklinks | Tak | Tak |
| privateendpoints | Nie | Nie |
| privatelinkservices | Nie | Nie |
| adresów publicipaddress | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
| publicipprefixes | Tak | Yes |
| routefilters | Nie | Nie |
| routetables | Tak | Tak |
| securegateways | Yes | Tak |
| serviceendpointpolicies | Yes | Tak |
| trafficmanagerprofiles | Yes | Tak |
| virtualhubs | Nie | Nie |
| virtualnetworkgateways | Tak | Tak |
| virtualnetworks | Tak | Tak |
| virtualnetworktaps | Nie | Nie |
| virtualwans | Nie | Nie |
| vpngateways | Nie | Nie |
| vpnsites | Nie | Nie |
| webapplicationfirewallpolicies | Tak | Yes |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia sieci wirtualnych](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Przestrzeni | Tak | Tak |
| przestrzenie nazw/notificationhubs | Tak | Tak |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Obszarów roboczych | Yes | Yes |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza przydziałów [subskrypcji](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | Yes | Yes |
| rozwiązania | Tak | Tak |
| Widoki | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| komunikacje równorzędne | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Pulpitów nawigacyjnych | Yes | Yes |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rootresources | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| produkcyjnych | Yes | Tak |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| magazynów | Tak | Tak |

> [!IMPORTANT]
> Zobacz [Recovery Services wskazówki dotyczące przenoszenia](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Przestrzeni | Yes | Tak |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplikacje | Yes | Nie |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| przekazywania | Tak | Tak |
| jobcollections | Tak | Tak |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść kilku zasobów wyszukiwania w różnych regionach w jednej operacji. Zamiast tego należy przenieść je w osobnych operacjach.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Yes | Tak |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| bram | Nie | Nie |
| węzły | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Przestrzeni | Tak | Tak |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplikacje | Nie | Nie |
| oparty | Tak | Tak |
| containergroups | Nie | Nie |
| containergroupsets | Nie | Nie |
| edgeclusters | Nie | Nie |
| sieci | Nie | Nie |
| secretstores | Nie | Nie |
| volumes | Nie | Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplikacje | Yes | Tak |
| containergroups | Nie | Nie |
| bram | Yes | Tak |
| sieci | Tak | Yes |
| wpisy tajne | Yes | Tak |
| volumes | Tak | Tak |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| SignalR | Tak | Yes |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Nie | Nie |

> [!IMPORTANT]
> Zobacz [Recovery Services wskazówki dotyczące przenoszenia](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Nie | Nie |
| gotowania | Nie | Nie |
| applicationdefinitions | Nie | Nie |
| aplikacje | Nie | Nie |
| jitrequests | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| instancepools | Nie | Nie |
| managedinstances | Nie | Nie |
| ManagedInstances/bazy danych | Nie | Nie |
| Serwerem | Tak | Yes |
| Serwery/bazy danych | Tak | Tak |
| serwery/elasticpools | Tak | Tak |
| virtualclusters | Tak | Tak |

> [!IMPORTANT]
> Baza danych i serwer muszą znajdować się w tej samej grupie zasobów. Gdy przesuniesz programu SQL server, jego baz danych są również przenoszone. To zachowanie ma zastosowanie do baz danych Azure SQL Database i Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Yes | Yes |
| sqlvirtualmachines | Tak | Tak |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dwvm | Nie | Nie |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Yes | Tak |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| pamięci podręcznych | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Tak | Tak |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| skonfigurowany | Nie | Nie |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | Yes | Tak |

> [!IMPORTANT]
> Nie można przenieść zadań Stream Analytics, gdy stan jest uruchomiony.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| wiejski | Nie | Nie |
| środowiska/źródła zdarzeń | Nie | Nie |
| wystąpienia | Nie | Nie |
| wystąpienia/środowiska | Nie | Nie |
| wystąpienia/środowiska/obiekty EventSource | Nie | Nie |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Nie | Nie |
| Produkcyjnych | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| wiejski | Tak | Tak |
| środowiska/źródła zdarzeń | Tak | Yes |
| środowiska/referencedatasets | Tak | Yes |

## <a name="microsofttoken"></a>Microsoft. token
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| przechowywać | Nie | Nie |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Nie | Nie |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| account | Yes | Tak |
| konto/rozszerzenie | Tak | Yes |
| konto/projekt | Tak | Yes |

> [!IMPORTANT]
> Aby zmienić subskrypcję usługi Azure DevOps, zobacz [zmiana subskrypcji platformy Azure używanej do](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)rozliczania.

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Tak | Yes |
| dedicatedcloudservices | Yes | Tak |
| virtualmachines | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Przystawki | Nie | Tak |
| connectiongateways | Tak | Tak |
| Licznik | Tak | Tak |
| customapis | Tak | Tak |
| hostingenvironments | Nie | Nie |
| dopuszczalna | Tak | Yes |
| teren | Yes | Tak |
| sites/premieraddons | Yes | Tak |
| Lokacje/miejsca | Yes | Tak |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Nie | Nie |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgroups | Nie | Nie |
| hostpools | Nie | Nie |
| Obszarów roboczych | Nie | Nie |

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm obecnie nie obsługują operacji przenoszenia.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).

Aby uzyskać te same dane jak plik z wartościami rozdzielanymi przecinkami, Pobierz [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
