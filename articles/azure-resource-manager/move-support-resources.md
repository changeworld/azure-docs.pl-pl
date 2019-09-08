---
title: Obsługa operacji przenoszenia według typu zasobów platformy Azure
description: Wyświetla listę typów zasobów platformy Azure, które można przenieść do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 09/06/2019
ms.author: tomfitz
ms.openlocfilehash: 9dc86fe7876a050fe67ec73a665be6141a4b98da
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801351"
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
> - [Microsoft. AzureData](#microsoftazuredata)
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
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
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
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
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

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Nie | Nie |
> | domainservices / replicasets | Nie | Nie |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dzierżaw | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | Tak | Tak |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | serwerów | Tak | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | usługa | Tak | Tak |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | Tak | Tak |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Nie | Nie |
> | appidentities | Nie | Nie |
> | bram | Nie | Nie |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyassignments | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Tak | Tak |
> | automationaccounts/konfiguracje | Tak | Tak |
> | automationaccounts/elementy Runbook | Tak | Tak |

> [!IMPORTANT]
> Elementy Runbook muszą istnieć w tej samej grupie zasobów co konto usługi Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Tak | Tak |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlserverregistrations | Nie | Nie |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rejestracje | Tak | Tak |

## <a name="microsoftbackup"></a>Microsoft.Backup

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvault | Nie | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Tak | Tak |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | oparty | Nie | Nie |
> | fileservers | Nie | Nie |
> | zadania | Nie | Nie |
> | obszary robocze | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Nie | Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | Tak | Tak |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Tak | Tak |
> | obserwatorzy | Nie | Nie |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Tak | Tak |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Redis | Tak | Tak |

> [!IMPORTANT]
> Jeśli wystąpienie usługi Azure cache for Redis jest skonfigurowane przy użyciu sieci wirtualnej, nie można przenieść wystąpienia do innej subskrypcji. Zobacz [ograniczenia dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Nie | Nie |
> | profiles | Tak | Tak |
> | Profile/punkty końcowe | Tak | Tak |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Tak | Tak |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | Tak | Nie |
> | virtualmachines | Tak | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nie | Nie |
> | reservedips | Nie | Nie |
> | virtualnetworks | Nie | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Tak | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Tak | Tak |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Tak | Tak |
> | diskencryptionsets | Nie | Nie |
> | dyski | Tak | Tak |
> | znajduj | Nie | Nie |
> | Galerie/obrazy | Nie | Nie |
> | Galerie/obrazy/wersje | Nie | Nie |
> | hostgroups | Nie | Nie |
> | hostgroups/hosty | Nie | Nie |
> | obrazy | Tak | Tak |
> | proximityplacementgroups | Nie | Nie |
> | restorepointcollections | Nie | Nie |
> | sharedvmimages | Nie | Nie |
> | sharedvmimages/wersje | Nie | Nie |
> | migawki | Tak | Tak |
> | virtualmachines | Tak | Tak |
> | virtualmachines/rozszerzenia | Tak | Tak |
> | virtualmachinescalesets | Tak | Tak |

> [!IMPORTANT]
> Zobacz [Virtual Machines wskazówki dotyczące przenoszenia](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | wołuje | Tak | Tak |
> | rejestry/BuildTasks | Tak | Tak |
> | rejestry/replikacje | Tak | Tak |
> | rejestry/zadania | Tak | Tak |
> | rejestry/elementy webhook | Tak | Tak |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Nie | Nie |
> | managedclusters | Nie | Nie |
> | openshiftmanagedclusters | Nie | Nie |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aplikacje | Tak | Tak |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Nie | Nie |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | łączniki | Tak | Tak |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | węzłów | Tak | Tak |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | zadania | Nie | Nie |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Nie | Nie |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | wykazy | Tak | Tak |
> | wykazy | Nie | Nie |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nie | Nie |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pakiety | Nie | Nie |
> | pakiety | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fabryki datafactors | Tak | Tak |
> | fabryki | Tak | Tak |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Tak | Tak |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Tak | Tak |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Nie | Nie |
> | usługi/projekty | Nie | Nie |
> | czasów | Nie | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | serwerów | Tak | Tak |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | serwerów | Tak | Tak |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | Nie | Nie |
> | serwerów | Tak | Tak |
> | serversv2 | Tak | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Tak | Tak |
> | wprowadzanie | Tak | Tak |
> | servicetopologie | Tak | Tak |
> | servicetopologie/usługi | Tak | Tak |
> | servicetopologie/usługi/serviceunits | Tak | Tak |
> | kroki | Tak | Tak |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nie | Nie |
> | elasticpools / iothubtenants | Nie | Nie |
> | iothubs | Tak | Tak |
> | provisioningservices | Tak | Tak |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Rejestrowanie | Tak | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Nie | Nie |
> | Labs | Tak | Nie |
> | laboratoria/środowiska | Tak | Tak |
> | laboratoria/servicerunner | Tak | Tak |
> | Labs/virtualmachines | Tak | Nie |
> | Uruchamianie | Tak | Tak |

## <a name="microsoftdns"></a>microsoft.dns

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dnszones | Nie | Nie |
> | dnszones/a | Nie | Nie |
> | dnszones/AAAA | Nie | Nie |
> | dnszones/CNAME | Nie | Nie |
> | dnszones/MX | Nie | Nie |
> | dnszones/PTR | Nie | Nie |
> | dnszones/SRV | Nie | Nie |
> | dnszones/txt | Nie | Nie |
> | trafficmanagerprofiles | Nie | Nie |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Tak | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domeny | Tak | Tak |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domeny | Tak | Tak |
> | opisano | Tak | Tak |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |
> | przestrzenie nazw | Tak | Tak |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nie | Nie |
> | sapmonitors | Tak | Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |

> [!IMPORTANT]
> Klastry HDInsight można przenieść do nowej subskrypcji lub grupy zasobów. Jednak nie można przenosić między subskrypcjami, zasoby sieciowe połączone z klastrem HDInsight (na przykład sieci wirtualnej, kart interfejsu Sieciowego lub modułu równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów interfejsu Sieciowego, który jest dołączony do maszyny wirtualnej do klastra.
>
> Podczas przenoszenia klastra usługi HDInsight do nowej subskrypcji, należy najpierw przenieść innych zasobów (np. konta magazynu). Następnie przenieś klaster HDInsight samodzielnie.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Tak | Tak |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | maszyny | Nie | Nie |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Tak | Tak |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | zadania | Tak | Tak |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Nie | Nie |
> | actiongroups | Tak | Tak |
> | activitylogalerts | Nie | Nie |
> | alertrules | Tak | Tak |
> | autoscalesettings | Tak | Tak |
> | komponentów | Tak | Tak |
> | guestdiagnosticsettings | Nie | Nie |
> | metricalerts | Nie | Nie |
> | notificationgroups | Nie | Nie |
> | notificationrules | Nie | Nie |
> | scheduledqueryrules | Tak | Tak |
> | testów internetowych | Tak | Tak |
> | skoroszyty | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | Tak | Tak |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Tak | Tak |
> | graph | Tak | Tak |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hsmpools | Nie | Nie |
> | magazynów | Tak | Tak |

> [!IMPORTANT]
> Magazyny kluczy używane do szyfrowania dysków nie mogą zostać przeniesione do grupy zasobów w tej samej subskrypcji lub w różnych subskrypcjach.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nie | Nie |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Nie | Nie |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Nie | Nie |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nie | Nie |
> | integrationaccounts | Tak | Tak |
> | integrationserviceenvironments | Nie | Nie |
> | isolatedenvironments | Nie | Nie |
> | przebieg | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Tak | Tak |
> | WebServices | Tak | Nie |
> | obszary robocze | Tak | Tak |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Tak | Tak |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Nie | Nie |
> | konta/obszary robocze | Nie | Nie |
> | konta/obszary robocze/projekty | Nie | Nie |
> | teamaccounts | Nie | Nie |
> | teamaccounts/obszary robocze | Nie | Nie |
> | teamaccounts/obszary robocze/projekty | Nie | Nie |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Nie | Nie |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Nie | Nie |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | resourceidentity | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Tak | Tak |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | MediaServices | Tak | Tak |
> | MediaServices/liveevents | Tak | Tak |
> | MediaServices/streamingendpoints | Tak | Tak |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | Nie | Nie |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Nie | Nie |
> | migrateprojects | Nie | Nie |
> | projektami | Nie | Nie |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nie | Nie |
> | netappaccounts / capacitypools | Nie | Nie |
> | netappaccounts/capacitypools/woluminy | Nie | Nie |
> | netappaccounts/capacitypools/woluminy/mounttargets | Nie | Nie |
> | netappaccounts/capacitypools/woluminy/migawki | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nie | Nie |
> | applicationgatewaywebapplicationfirewallpolicies | Nie | Nie |
> | applicationsecuritygroups | Tak | Tak |
> | azurefirewalls | Tak | Tak |
> | bastionhosts | Nie | Nie |
> | połączenia | Tak | Tak |
> | ddoscustompolicies | Tak | Tak |
> | ddosprotectionplans | Nie | Nie |
> | dnszones | Tak | Tak |
> | expressroutecircuits | Nie | Nie |
> | expressroutecrossconnections | Nie | Nie |
> | expressroutegateways | Nie | Nie |
> | expressrouteports | Nie | Nie |
> | usługi Frontdoor | Nie | Nie |
> | frontdoorwebapplicationfirewallpolicies | Nie | Nie |
> | loadbalancers | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | localnetworkgateways | Tak | Tak |
> | natgateways | Tak | Tak |
> | networkintentpolicies | Tak | Tak |
> | networkinterfaces | Tak | Tak |
> | networkprofiles | Nie | Nie |
> | networksecuritygroups | Tak | Tak |
> | networkwatchers | Tak | Tak |
> | networkwatchers / connectionmonitors | Tak | Tak |
> | networkwatchers/soczewki | Tak | Tak |
> | networkwatchers / pingmeshes | Tak | Tak |
> | p2svpngateways | Nie | Nie |
> | privatednszones | Tak | Tak |
> | privatednszones / virtualnetworklinks | Tak | Tak |
> | privateendpoints | Nie | Nie |
> | privatelinkservices | Nie | Nie |
> | adresów publicipaddress | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | publicipprefixes | Tak | Tak |
> | routefilters | Nie | Nie |
> | routetables | Tak | Tak |
> | securegateways | Tak | Tak |
> | serviceendpointpolicies | Tak | Tak |
> | trafficmanagerprofiles | Tak | Tak |
> | virtualhubs | Nie | Nie |
> | virtualnetworkgateways | Tak | Tak |
> | virtualnetworks | Tak | Tak |
> | virtualnetworktaps | Nie | Nie |
> | virtualwans | Nie | Nie |
> | vpngateways (wirtualna sieć WAN) | Nie | Nie |
> | vpnsites (wirtualna sieć WAN) | Nie | Nie |
> | webapplicationfirewallpolicies | Tak | Tak |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | przestrzenie nazw | Tak | Tak |
> | przestrzenie nazw/notificationhubs | Tak | Tak |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Tak | Tak |
> | rozwiązania | Tak | Tak |
> | wyświetlenia | Tak | Tak |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | komunikacje równorzędne | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pulpitów nawigacyjnych | Tak | Tak |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Tak | Tak |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | produkcyjnych | Tak | Tak |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konta | Nie | Nie |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | magazynów | Tak | Tak |

> [!IMPORTANT]
> Zobacz [Recovery Services wskazówki dotyczące przenoszenia](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | przestrzenie nazw | Tak | Tak |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | — zapytania | Tak | Tak |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aplikacje | Tak | Nie |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | przepływy | Tak | Tak |
> | jobcollections | Tak | Tak |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść kilku zasobów wyszukiwania w różnych regionach w jednej operacji. Zamiast tego należy przenieść je w osobnych operacjach.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | Tak | Tak |
> | playbookconfigurations | Nie | Nie |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | bram | Nie | Nie |
> | węzły | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | przestrzenie nazw | Tak | Tak |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aplikacje | Nie | Nie |
> | oparty | Tak | Tak |
> | klastry/aplikacje | Nie | Nie |
> | containergroups | Nie | Nie |
> | containergroupsets | Nie | Nie |
> | edgeclusters | Nie | Nie |
> | sieci | Nie | Nie |
> | secretstores | Nie | Nie |
> | woluminy | Nie | Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aplikacje | Tak | Tak |
> | containergroups | Nie | Nie |
> | bram | Tak | Tak |
> | sieci | Tak | Tak |
> | wpisy tajne | Tak | Tak |
> | woluminy | Tak | Tak |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | SignalR | Tak | Tak |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | siterecoveryvault | Nie | Nie |

> [!IMPORTANT]
> Zobacz [Recovery Services wskazówki dotyczące przenoszenia](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Nie | Nie |
> | gotowania | Nie | Nie |
> | applicationdefinitions | Nie | Nie |
> | aplikacje | Nie | Nie |
> | jitrequests | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | Nie | Nie |
> | managedinstances | Nie | Nie |
> | ManagedInstances/bazy danych | Nie | Nie |
> | serwerów | Tak | Tak |
> | Serwery/bazy danych | Tak | Tak |
> | serwery/elasticpools | Tak | Tak |
> | virtualclusters | Tak | Tak |

> [!IMPORTANT]
> Baza danych i serwer muszą znajdować się w tej samej grupie zasobów. Gdy przesuniesz programu SQL server, jego baz danych są również przenoszone. To zachowanie ma zastosowanie do baz danych Azure SQL Database i Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Tak | Tak |
> | sqlvirtualmachines | Tak | Tak |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | Nie | Nie |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Tak | Tak |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pamięci podręcznych | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Tak | Tak |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | skonfigurowany | Nie | Nie |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść zadań Stream Analytics, gdy stan jest uruchomiony.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | wiejski | Nie | Nie |
> | środowiska/źródła zdarzeń | Nie | Nie |
> | wystąpienia | Nie | Nie |
> | wystąpienia/środowiska | Nie | Nie |
> | wystąpienia/środowiska/obiekty EventSource | Nie | Nie |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nie | Nie |
> | zasoby | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | wiejski | Tak | Tak |
> | środowiska/źródła zdarzeń | Tak | Tak |
> | środowiska/referencedatasets | Tak | Tak |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | przechowywać | Nie | Nie |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nie | Nie |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | konto | Tak | Tak |
> | konto/rozszerzenie | Tak | Tak |
> | konto/projekt | Tak | Tak |

> [!IMPORTANT]
> Aby zmienić subskrypcję usługi Azure DevOps, zobacz [zmiana subskrypcji platformy Azure używanej do rozliczania](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Tak | Tak |
> | dedicatedcloudservices | Tak | Tak |
> | virtualmachines | Tak | Tak |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Certyfikaty | Nie | Tak |
> | connectiongateways | Tak | Tak |
> | połączenia | Tak | Tak |
> | customapis | Tak | Tak |
> | hostingenvironments | Nie | Nie |
> | dopuszczalna | Tak | Tak |
> | lokacje | Tak | Tak |
> | Lokacje/premieraddons | Tak | Tak |
> | Lokacje/miejsca | Tak | Tak |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nie | Nie |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Typ zasobu | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Nie | Nie |
> | hostpools | Nie | Nie |
> | obszary robocze | Nie | Nie |

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm obecnie nie obsługują operacji przenoszenia.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).

Aby uzyskać te same dane jak plik z wartościami rozdzielanymi przecinkami, Pobierz [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
