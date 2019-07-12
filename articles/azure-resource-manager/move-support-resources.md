---
title: Przenoszenie obsługi operacji przez typ zasobu platformy Azure
description: Wyświetla listę typów zasobów platformy Azure, które mogą zostać przeniesione do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 093c20407cb6210125106189f36566f539de0dcc
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721118"
---
# <a name="move-operation-support-for-resources"></a>Przenoszenie obsługi operacji dla zasobów
W tym artykule wymieniono, czy typ zasobu platformy Azure obsługuje operacji przenoszenia. Umożliwia także informacji na temat specjalne warunki, które należy wziąć pod uwagę podczas przenoszenia zasobu.

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation w sposób](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
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
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
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
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
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
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Nie | Nie |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Dzierżaw | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| actionrules | Yes | Tak |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Serwery | Yes | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| usługa | Tak | Yes |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| configurationstores | Tak | Yes |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplikacje interfejsów API | Nie | Nie |
| appidentities | Nie | Nie |
| Bramy | Nie | Nie |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia usługi App](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | Yes | Yes |
| automationaccounts/configurations | Yes | Tak |
| automationaccounts/elementów runbook | Tak | Tak |

> [!IMPORTANT]
> Elementy Runbook muszą istnieć w tej samej grupie zasobów co konto usługi Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| b2cdirectories | Tak | Tak |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rejestracje | Tak | Tak |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| backupvault | Nie | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| batchaccounts | Tak | Tak |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Klastry | Nie | Nie |
| fileservers | Nie | Nie |
| Zadania | Nie | Nie |
| Obszary robocze | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mapapis | Nie | Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztalk | Tak | Yes |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blockchainmembers | Yes | Tak |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | Tak | Tak |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Redis Cache | Yes | Tak |

> [!IMPORTANT]
> Skonfigurowanie usługi Azure Cache dla wystąpienia pamięci podręcznej Redis przy użyciu sieci wirtualnej wystąpienia nie można przenieść do innej subskrypcji. Zobacz [sieci wirtualnych, Przenieś ograniczenia](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| profiles | Tak | Yes |
| Profile/punktów końcowych | Tak | Tak |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| zamówień certyfikatów | Tak | Tak |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia usługi App](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainnames | Tak | Nie |
| maszyn wirtualnych | Yes | Nie |

> [!IMPORTANT]
> Zobacz [wdrożeń klasycznych przenieść wskazówki](./move-limitations/classic-model-move-limitations.md). Zasobów klasycznego wdrożenia można przenosić między subskrypcjami przy użyciu określonych operacji w przypadku tego scenariusza.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Nie | Nie |
| reservedips | Nie | Nie |
| virtualnetworks | Nie | Nie |

> [!IMPORTANT]
> Zobacz [wdrożeń klasycznych przenieść wskazówki](./move-limitations/classic-model-move-limitations.md). Zasobów klasycznego wdrożenia można przenosić między subskrypcjami przy użyciu określonych operacji w przypadku tego scenariusza.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Tak | Nie |

> [!IMPORTANT]
> Zobacz [wdrożeń klasycznych przenieść wskazówki](./move-limitations/classic-model-move-limitations.md). Zasobów klasycznego wdrożenia można przenosić między subskrypcjami przy użyciu określonych operacji w przypadku tego scenariusza.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Tak | Tak |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | Tak | Tak |
| Dyski | Tak | Tak |
| Galerie | Nie | Nie |
| galerie/obrazów | Nie | Nie |
| Galerie/obrazy/wersji | Nie | Nie |
| hostgroups | Nie | Nie |
| hostgroups/hosts | Nie | Nie |
| images | Tak | Yes |
| proximityplacementgroups | Nie | Nie |
| restorepointcollections | Nie | Nie |
| sharedvmimages | Nie | Nie |
| sharedvmimages/wersji | Nie | Nie |
| Migawki | Tak | Yes |
| maszyn wirtualnych | Tak | Yes |
| maszyn wirtualnych/rozszerzenia | Yes | Yes |
| virtualmachinescalesets | Tak | Tak |

> [!IMPORTANT]
> Zobacz [maszyn wirtualnych, Przenieś wskazówki](./move-limitations/virtual-machines-move-limitations.md).

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
| rejestry | Tak | Yes |
| rejestry/buildtasks | Tak | Yes |
| rejestry/replikacji | Tak | Tak |
| rejestry/zadań | Tak | Tak |
| rejestry/elementów webhook | Tak | Tak |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containerservices | Nie | Nie |
| managedclusters | Nie | Nie |
| openshiftmanagedclusters | Nie | Nie |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplikacje | Yes | Tak |

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
| Koncentratory | Tak | Tak |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Zadania | Nie | Nie |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Nie | Nie |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Obszary robocze | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| katalogi | Tak | Tak |
| datacatalogs | Nie | Nie |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectionmanagers | Nie | Nie |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Pakiety | Nie | Nie |
| Plany | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datafactories | Tak | Tak |
| fabryki | Tak | Yes |

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
| accounts | Tak | Tak |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Nie | Nie |
| usługi/projekty | Nie | Nie |
| gniazda | Nie | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Serwery | Yes | Tak |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Serwery | Tak | Tak |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Nie | Nie |
| Serwery | Tak | Tak |
| serversv2 | Tak | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | Tak | Tak |
| Wprowadzanie | Yes | Tak |
| servicetopologies | Tak | Yes |
| servicetopologies/usługi | Tak | Yes |
| servicetopologies/services/serviceunits | Tak | Tak |
| kroki | Tak | Tak |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| elasticpools | Nie | Nie |
| elasticpools/iothubtenants | Nie | Nie |
| iothubs | Tak | Tak |
| provisioningservices | Yes | Tak |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Kontrolery | Nie | Nie |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Nie | Nie |
| Warsztaty | Yes | Nie |
| laboratoria/środowisk | Tak | Tak |
| laboratoria/servicerunners | Yes | Tak |
| laboratoria/maszyn wirtualnych | Yes | Nie |
| Harmonogramy | Yes | Tak |

## <a name="microsoftdns"></a>microsoft.dns
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Nie | Nie |
| dnszones/a | Nie | Nie |
| dnszones/aaaa | Nie | Nie |
| dnszones/cname | Nie | Nie |
| dnszones/mx | Nie | Nie |
| dnszones/ptr | Nie | Nie |
| dnszones/srv | Nie | Nie |
| dnszones/txt | Nie | Nie |
| trafficmanagerprofiles | Nie | Nie |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databaseaccounts | Tak | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domeny | Tak | Yes |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domeny | Tak | Yes |
| Tematy | Tak | Tak |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Klastry | Tak | Tak |
| Przestrzenie nazw | Tak | Yes |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hanainstances | Tak | Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Klastry | Tak | Tak |

> [!IMPORTANT]
> Klastry HDInsight można przenieść do nowej subskrypcji lub grupy zasobów. Jednak nie można przenosić między subskrypcjami, zasoby sieciowe połączone z klastrem HDInsight (na przykład sieci wirtualnej, kart interfejsu Sieciowego lub modułu równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów interfejsu Sieciowego, który jest dołączony do maszyny wirtualnej do klastra.
>
> Podczas przenoszenia klastra usługi HDInsight do nowej subskrypcji, należy najpierw przenieść innych zasobów (np. konta magazynu). Następnie przenieś klaster HDInsight samodzielnie.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Tak | Tak |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| maszyny | Nie | Nie |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagers | Tak | Tak |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Zadania | Tak | Tak |

## <a name="microsoftinsights"></a>microsoft.insights
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |
| actiongroups | Yes | Yes |
| alerty dziennika aktywności | Nie | Nie |
| alertrules | Tak | Tak |
| wartość autoscalesettings | Tak | Tak |
| Składniki | Yes | Tak |
| guestdiagnosticsettings | Nie | Nie |
| metricalerts | Nie | Nie |
| notificationgroups | Nie | Nie |
| notificationrules | Nie | Nie |
| scheduledqueryrules | Yes | Tak |
| testy internetowe | Tak | Tak |
| Skoroszyty | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie może przekraczać [limity przydziału subskrypcji](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | Tak | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | Yes | Tak |
| graph | Yes | Tak |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Nie | Nie |
| Magazyny | Yes | Yes |

> [!IMPORTANT]
> Magazyny klucz używany do szyfrowania dysku nie można przenieść do grupy zasobów w tej samej subskrypcji lub w różnych subskrypcjach.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Klastry | Tak | Yes |

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
| integrationaccounts | Yes | Tak |
| integrationserviceenvironments | Nie | Nie |
| isolatedenvironments | Nie | Nie |
| Przepływy pracy | Tak | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | Tak | Tak |
| usługi sieci Web | Tak | Nie |
| Obszary robocze | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Yes | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |
| konta/obszarów roboczych | Nie | Nie |
| obszary robocze/kont/projektów | Nie | Nie |
| teamaccounts | Nie | Nie |
| teamaccounts/obszarów roboczych | Nie | Nie |
| obszary robocze/teamaccounts/projektów | Nie | Nie |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingaccounts | Nie | Nie |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Obszary robocze | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| userassignedidentities | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Tak | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| classicdevservices | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mediaservices | Tak | Tak |
| mediaservices/liveevents | Tak | Yes |
| mediaservices/punkty | Yes | Tak |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| assessmentprojects | Nie | Nie |
| migrateprojects | Nie | Nie |
| Projekty | Nie | Nie |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| netappaccounts | Nie | Nie |
| netappaccounts/capacitypools | Nie | Nie |
| netappaccounts/capacitypools/volumes | Nie | Nie |
| netappaccounts/capacitypools/volumes/mounttargets | Nie | Nie |
| netappaccounts/capacitypools/volumes/snapshots | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgateways | Nie | Nie |
| applicationgatewaywebapplicationfirewallpolicies | Nie | Nie |
| applicationsecuritygroups | Tak | Yes |
| azurefirewalls | Yes | Tak |
| bastionhosts | Nie | Nie |
| Połączenia | Tak | Tak |
| ddoscustompolicies | Tak | Tak |
| ddosprotectionplans | Nie | Nie |
| dnszones | Tak | Yes |
| expressroutecircuits | Nie | Nie |
| expressroutecrossconnections | Nie | Nie |
| expressroutegateways | Nie | Nie |
| expressrouteports | Nie | Nie |
| frontdoors | Nie | Nie |
| frontdoorwebapplicationfirewallpolicies | Nie | Nie |
| loadbalancers | Tak — podstawowej jednostki SKU<br>Nie — standardowa jednostka SKU | Tak — podstawowej jednostki SKU<br>Nie — standardowa jednostka SKU |
| localnetworkgateways | Tak | Tak |
| natgateways | Yes | Tak |
| networkintentpolicies | Tak | Tak |
| interfejsy | Yes | Tak |
| networkprofiles | Nie | Nie |
| networksecuritygroups | Tak | Tak |
| networkwatchers | Tak | Tak |
| networkwatchers/connectionmonitors | Yes | Tak |
| networkwatchers/obiektywów | Tak | Tak |
| networkwatchers/pingmeshes | Tak | Tak |
| p2svpngateways | Nie | Nie |
| privatednszones | Yes | Tak |
| privatednszones/virtualnetworklinks | Yes | Tak |
| privateendpoints | Nie | Nie |
| privatelinkservices | Nie | Nie |
| publicipaddresses | Tak — podstawowej jednostki SKU<br>Nie — standardowa jednostka SKU | Tak — podstawowej jednostki SKU<br>Nie — standardowa jednostka SKU |
| publicipprefixes | Tak | Tak |
| routefilters | Nie | Nie |
| routetables | Tak | Tak |
| securegateways | Tak | Tak |
| serviceendpointpolicies | Yes | Tak |
| trafficmanagerprofiles | Tak | Tak |
| virtualhubs | Nie | Nie |
| virtualnetworkgateways | Yes | Tak |
| virtualnetworks | Tak | Tak |
| virtualnetworktaps | Nie | Nie |
| virtualwans | Nie | Nie |
| vpngateways | Nie | Nie |
| vpnsites | Nie | Nie |
| webapplicationfirewallpolicies | Tak | Tak |

> [!IMPORTANT]
> Zobacz [sieci wirtualnych, Przenieś wskazówki](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Przestrzenie nazw | Yes | Tak |
| przestrzenie nazw/notificationhubs | Tak | Tak |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Obszary robocze | Yes | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie może przekraczać [limity przydziału subskrypcji](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | Yes | Tak |
| rozwiązania | Yes | Tak |
| Widoki | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Komunikacja równorzędna | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Pulpity nawigacyjne | Tak | Tak |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rootresources | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspacecollections | Tak | Tak |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Pojemności | Tak | Yes |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Magazyny | Tak | Yes |

> [!IMPORTANT]
> Zobacz [usługi Recovery Services przenieść wskazówki](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Przestrzenie nazw | Yes | Yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplikacje | Tak | Nie |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| przepływy | Yes | Tak |
| kolekcjach | Tak | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | Tak | Yes |

> [!IMPORTANT]
> Nie można przenieść kilka wyszukiwania zasobów w różnych regionach w ramach jednej operacji. Zamiast tego należy przenieść je w oddzielne operacje.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Tak | Tak |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Bramy | Nie | Nie |
| węzły | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Przestrzenie nazw | Yes | Tak |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplikacje | Nie | Nie |
| Klastry | Tak | Yes |
| containergroups | Nie | Nie |
| containergroupsets | Nie | Nie |
| edgeclusters | Nie | Nie |
| Sieci | Nie | Nie |
| secretstores | Nie | Nie |
| volumes | Nie | Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplikacje | Yes | Yes |
| containergroups | Nie | Nie |
| Bramy | Tak | Yes |
| Sieci | Tak | Tak |
| wpisy tajne | Tak | Yes |
| volumes | Tak | Tak |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| signalr | Yes | Yes |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Nie | Nie |

> [!IMPORTANT]
> Zobacz [usługi Recovery Services przenieść wskazówki](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Nie | Nie |
| urządzenia | Nie | Nie |
| applicationdefinitions | Nie | Nie |
| aplikacje | Nie | Nie |
| jitrequests | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| instancepools | Tak | Tak |
| managedinstances | Tak | Yes |
| managedinstances/baz danych | Yes | Tak |
| Serwery | Yes | Tak |
| serwery/baz danych | Yes | Tak |
| servers/elasticpools | Tak | Tak |
| virtualclusters | Tak | Tak |

> [!IMPORTANT]
> Bazy danych i serwera musi być w tej samej grupie zasobów. Gdy przesuniesz programu SQL server, jego baz danych są również przenoszone. To zachowanie ma zastosowanie do baz danych Azure SQL Database i Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Tak | Tak |
| sqlvirtualmachines | Yes | Yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dwvm | Nie | Nie |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Tak | Tak |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Pamięci podręczne | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Tak | Yes |

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
| Menedżerowie | Nie | Nie |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | Yes | Yes |

> [!IMPORTANT]
> Stream Analytics, które zadania nie można przenieść uruchamianego w stanie.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Środowiska | Nie | Nie |
| środowiska/eventsources | Nie | Nie |
| wystąpienia | Nie | Nie |
| wystąpienia/środowisk | Nie | Nie |
| środowiska/wystąpienia/eventsources | Nie | Nie |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Nie | Nie |
| Zasoby | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Środowiska | Tak | Tak |
| środowiska/eventsources | Tak | Yes |
| środowiska/referencedatasets | Yes | Tak |

## <a name="microsofttoken"></a>Microsoft.Token
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| przechowuje | Nie | Nie |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Nie | Nie |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| account | Tak | Tak |
| konta i rozszerzeń | Tak | Tak |
| konto lub projekt docelowy | Yes | Yes |

> [!IMPORTANT]
> Aby zmienić subskrypcję DevOps platformy Azure, zobacz [zmienić subskrypcję platformy Azure używane na potrzeby rozliczeń](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Tak | Yes |
| dedicatedcloudservices | Yes | Tak |
| maszyn wirtualnych | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Certyfikaty | Nie | Tak |
| connectiongateways | Tak | Yes |
| Połączenia | Tak | Tak |
| customapis | Tak | Tak |
| hostingenvironments | Nie | Nie |
| farm serwerów | Yes | Tak |
| Lokacje | Yes | Yes |
| sites/premieraddons | Tak | Tak |
| Lokacje/miejsca | Yes | Yes |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia usługi App](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Nie | Nie |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Typ zasobu | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgroups | Nie | Nie |
| hostpools | Nie | Nie |
| Obszary robocze | Nie | Nie |

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm nie obsługują obecnie operacji przenoszenia.

## <a name="next-steps"></a>Następne kroki
Poleceń przenieść zasoby można znaleźć [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).

Aby uzyskać te same dane w formacie wartości rozdzielanych przecinkami, Pobierz [move-pomocy technicznej — resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
