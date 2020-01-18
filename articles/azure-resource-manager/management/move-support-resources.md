---
title: Obsługa operacji przenoszenia według typu zasobu
description: Wyświetla listę typów zasobów platformy Azure, które można przenieść do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: da08775ed6c694b95ecec452507f94638091db0c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261062"
---
# <a name="move-operation-support-for-resources"></a>Obsługa operacji przenoszenia dla zasobów
W tym artykule przedstawiono, czy typ zasobu platformy Azure obsługuje operację przenoszenia. Zawiera również informacje dotyczące specjalnych warunków, które należy wziąć pod uwagę podczas przeniesienia zasobu.

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft. rozliczenia](#microsoftbilling)
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
> - [Microsoft. zużycie](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
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
> - [Microsoft. dataprotection](#microsoftdataprotection)
> - [Microsoft. dataudział](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
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
> - [Microsoft. Kubernetes](#microsoftkubernetes)
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
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrowanie](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. Komunikacja równorzędna](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. Support](#microsoftsupport)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. Monitor obciążenia został](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | domainservices | Nie | Nie |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dzierżaw | Nie | Nie |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konfiguracje | Nie | Nie |
> | Zalecenia | Nie | Nie |
> | pominięć | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | actionrules | Tak | Tak |
> | alerts | Nie | Nie |
> | alertssummary | Nie | Nie |
> | smartdetectoralertrules | Tak | Tak |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwery | Tak | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługa | Tak | Tak |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | configurationstores | Tak | Tak |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Spring | Tak | Tak |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | apiapps | Nie | Nie |
> | appidentities | Nie | Nie |
> | bram | Nie | Nie |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | CheckAccess | Nie | Nie |
> | denyassignments | Nie | Nie |
> | findorphanroleassignments | Nie | Nie |
> | blokady | Nie | Nie |
> | uprawnienia | Nie | Nie |
> | policyassignments | Nie | Nie |
> | policydefinitions | Nie | Nie |
> | policysetdefinitions | Nie | Nie |
> | RoleAssignments | Nie | Nie |
> | roleassignmentsusagemetrics | Nie | Nie |
> | roledefinitions | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Tak | Tak |
> | automationaccounts/konfiguracje | Tak | Tak |
> | automationaccounts/elementy Runbook | Tak | Tak |

> [!IMPORTANT]
> Elementy Runbook muszą istnieć w tej samej grupie zasobów co konto usługi Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Tak | Tak |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Nie | Nie |
> | postgresinstances | Nie | Nie |
> | sqlbigdataclusters | Nie | Nie |
> | wystąpienia sqlinstances | Nie | Nie |
> | sqlserverregistrations | Tak | Tak |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | rejestracje | Tak | Tak |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Tak | Tak |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Nie | Nie |
> | fileservers | Nie | Nie |
> | zadania | Nie | Nie |
> | obszary robocze | Nie | Nie |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | billingperiods | Nie | Nie |
> | billingpermissions | Nie | Nie |
> | billingroleassignments | Nie | Nie |
> | billingroledefinitions | Nie | Nie |
> | createbillingroleassignment | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | mapapis | Nie | Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | biztalk | Nie | Nie |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nie | Nie |
> | obserwatorzy | Nie | Nie |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nie | Nie |
> | plany | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | botservices | Tak | Tak |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Redis | Tak | Tak |

> [!IMPORTANT]
> Jeśli wystąpienie usługi Azure cache for Redis jest skonfigurowane przy użyciu sieci wirtualnej, nie można przenieść wystąpienia do innej subskrypcji. Zobacz [ograniczenia dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Tak | Tak |
> | profiles | Tak | Tak |
> | Profile/punkty końcowe | Tak | Tak |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certificateorders | Tak | Tak |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | domainnames | Tak | Nie |
> | virtualmachines | Tak | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nie | Nie |
> | reservedips | Nie | Nie |
> | virtualnetworks | Nie | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Tak | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Tak | Tak |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Tak | Tak |
> | diskencryptionsets | Nie | Nie |
> | dysku | Tak | Tak |
> | znajduj | Nie | Nie |
> | Galerie/obrazy | Nie | Nie |
> | Galerie/obrazy/wersje | Nie | Nie |
> | hostgroups | Nie | Nie |
> | hostgroups/hosty | Nie | Nie |
> | images | Tak | Tak |
> | proximityplacementgroups | Nie | Nie |
> | restorepointcollections | Nie | Nie |
> | sharedvmimages | Nie | Nie |
> | sharedvmimages/wersje | Nie | Nie |
> | Migawki | Tak | Tak |
> | virtualmachines | Tak | Tak |
> | virtualmachines/rozszerzenia | Tak | Tak |
> | virtualmachinescalesets | Tak | Tak |

> [!IMPORTANT]
> Zobacz [Virtual Machines wskazówki dotyczące przenoszenia](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Nie | Nie |
> | równoważy | Nie | Nie |
> | budżetów | Nie | Nie |
> | odsetk | Nie | Nie |
> | costtags | Nie | Nie |
> | środki | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | prognozy | Nie | Nie |
> | ilości | Nie | Nie |
> | Platform handlowych | Nie | Nie |
> | operationresults | Nie | Nie |
> | operationstatus | Nie | Nie |
> | Pricesheets | Nie | Nie |
> | produkty | Nie | Nie |
> | reservationdetails | Nie | Nie |
> | reservationrecommendations | Nie | Nie |
> | reservationsummaries | Nie | Nie |
> | reservationtransactions | Nie | Nie |
> | tags | Nie | Nie |
> | dzierżaw | Nie | Nie |
> | odsetk | Nie | Nie |
> | usagedetails | Nie | Nie |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | containergroups | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | containergroups | Nie | Nie |
> | serviceassociationlinks | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wołuje | Tak | Tak |
> | rejestry/BuildTasks | Tak | Tak |
> | rejestry/replikacje | Tak | Tak |
> | rejestry/taskruns | Tak | Tak |
> | rejestry/zadania | Tak | Tak |
> | rejestry/elementy webhook | Tak | Tak |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | containerservices | Nie | Nie |
> | managedclusters | Nie | Nie |
> | openshiftmanagedclusters | Nie | Nie |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacji | Nie | Nie |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | alerts | Nie | Nie |
> | budżetów | Nie | Nie |
> | konta integracji | Tak | Tak |
> | wymiary | Nie | Nie |
> | eksporty | Nie | Nie |
> | externalsubscriptions | Nie | Nie |
> | forecast | Nie | Nie |
> | query | Nie | Nie |
> | Reportconfigs | Nie | Nie |
> | reports | Nie | Nie |
> | showbackrules | Nie | Nie |
> | views | Nie | Nie |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | koncentratory, | Nie | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | skojarzenia | Nie | Nie |
> | resourceproviders | Tak | Tak |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zadania | Nie | Nie |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Nie | Nie |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wykazy | Tak | Tak |
> | wykazy | Nie | Nie |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nie | Nie |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | pakiety | Nie | Nie |
> | pakiety | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | fabryki datafactors | Tak | Tak |
> | fabryki | Tak | Tak |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Tak | Tak |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Tak | Tak |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Nie | Nie |
> | usługi/projekty | Nie | Nie |
> | czasów | Nie | Nie |

## <a name="microsoftdataprotection"></a>Microsoft. dataprotection

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nie | Nie |

## <a name="microsoftdatashare"></a>Microsoft. dataudział

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Tak | Tak |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwery | Tak | Tak |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwery | Tak | Tak |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | servergroups | Nie | Nie |
> | serwery | Tak | Tak |
> | serversv2 | Tak | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | artifactsources | Tak | Tak |
> | wprowadzanie | Tak | Tak |
> | servicetopologie | Tak | Tak |
> | servicetopologie/usługi | Tak | Tak |
> | servicetopologie/usługi/serviceunits | Tak | Tak |
> | kroki | Tak | Tak |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nie | Nie |
> | elasticpools / iothubtenants | Nie | Nie |
> | iothubs | Tak | Tak |
> | provisioningservices | Tak | Tak |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | potoki | Tak | Tak |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | kontrolery | Tak | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | labcenters | Nie | Nie |
> | labs | Tak | Nie |
> | laboratoria/środowiska | Tak | Tak |
> | laboratoria/servicerunner | Tak | Tak |
> | Labs/virtualmachines | Tak | Nie |
> | Uruchamianie | Tak | Tak |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Tak | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | domeny | Tak | Tak |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | domeny | Tak | Tak |
> | eventSubscriptions | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. |
> | eventsubscriptions | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. |
> | extensiontopics | Nie | Nie |
> | — tematy | Tak | Tak |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |
> | przestrzenie nazw | Tak | Tak |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | guestconfigurationassignments | Nie | Nie |
> | oprogramowanie | Nie | Nie |
> | softwareupdateprofile | Nie | Nie |
> | softwareupdates | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nie | Nie |
> | sapmonitors | Tak | Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |

> [!IMPORTANT]
> Klastry HDInsight można przenieść do nowej subskrypcji lub grupy zasobów. Jednak nie można przenosić między subskrypcjami, zasoby sieciowe połączone z klastrem HDInsight (na przykład sieci wirtualnej, kart interfejsu Sieciowego lub modułu równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów interfejsu Sieciowego, który jest dołączony do maszyny wirtualnej do klastra.
>
> Podczas przenoszenia klastra usługi HDInsight do nowej subskrypcji, należy najpierw przenieść innych zasobów (np. konta magazynu). Następnie przenieś klaster HDInsight samodzielnie.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Tak | Tak |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Win64 | Tak | Tak |
> | Maszyny/rozszerzenia | Nie | Nie |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | datamanagers | Tak | Tak |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zadania | Tak | Tak |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | actiongroups | Tak | Tak |
> | activitylogalerts | Nie | Nie |
> | alertrules | Tak | Tak |
> | automatedexportsettings | Nie | Nie |
> | autoscalesettings | Tak | Tak |
> | punkt odniesienia | Nie | Nie |
> | calculatebaseline | Nie | Nie |
> | składniki | Tak | Tak |
> | diagnosticsettings | Nie | Nie |
> | diagnosticsettingscategories | Nie | Nie |
> | Typ zdarzenia | Nie | Nie |
> | extendeddiagnosticsettings | Nie | Nie |
> | logdefinitions | Nie | Nie |
> | dzienniki | Nie | Nie |
> | metricalerts | Nie | Nie |
> | metricbaselines | Nie | Nie |
> | metricdefinitions | Nie | Nie |
> | metricnamespaces | Nie | Nie |
> | metrics | Nie | Nie |
> | Moje skoroszyty | Nie | Nie |
> | scheduledqueryrules | Tak | Tak |
> | topology | Nie | Nie |
> | transakcji | Nie | Nie |
> | vminsightsonboardingstatuses | Nie | Nie |
> | testów internetowych | Tak | Tak |
> | skoroszyty | Tak | Tak |
> | workbooktemplates | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | iotapps | Tak | Tak |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Tak | Tak |
> | graph | Tak | Tak |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | magazynów | Tak | Tak |

> [!IMPORTANT]
> Magazyny kluczy używane do szyfrowania dysków nie mogą zostać przeniesione do grupy zasobów w tej samej subskrypcji lub w różnych subskrypcjach.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Nie | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nie | Nie |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nie | Nie |
> | integrationaccounts | Tak | Tak |
> | integrationserviceenvironments | Tak | Nie |
> | integrationserviceenvironments/managedapis | Tak | Nie |
> | isolatedenvironments | Nie | Nie |
> | przepływy pracy | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Tak | Tak |
> | WebServices | Tak | Nie |
> | obszary robocze | Tak | Tak |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nie | Nie |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |
> | konta/obszary robocze | Nie | Nie |
> | konta/obszary robocze/projekty | Nie | Nie |
> | teamaccounts | Nie | Nie |
> | teamaccounts/obszary robocze | Nie | Nie |
> | teamaccounts/obszary robocze/projekty | Nie | Nie |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Nie | Nie |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | osob | Nie | Nie |
> | resourceidentity | Nie | Nie |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | registrationassignments | Nie | Nie |
> | registrationdefinitions | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Tak | Tak |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | MediaServices | Tak | Tak |
> | MediaServices/liveevents | Tak | Tak |
> | MediaServices/streamingendpoints | Tak | Tak |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | appclusters | Nie | Nie |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Tak | Tak |
> | migrateprojects | Tak | Tak |
> | projekty | Nie | Nie |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nie | Nie |
> | netappaccounts / backuppolicies | Nie | Nie |
> | netappaccounts / capacitypools | Nie | Nie |
> | netappaccounts/capacitypools/woluminy | Nie | Nie |
> | netappaccounts/capacitypools/woluminy/mounttargets | Nie | Nie |
> | netappaccounts/capacitypools/woluminy/migawki | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
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
> | expressroutegateways | Nie | Nie |
> | usługi frontdoor | Nie | Nie |
> | frontdoorwebapplicationfirewallpolicies | Nie | Nie |
> | loadbalancers | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | localnetworkgateways | Tak | Tak |
> | networkexperimentprofiles | Tak | Tak |
> | networkintentpolicies | Tak | Tak |
> | networkinterfaces | Tak | Tak |
> | networkprofiles | Nie | Nie |
> | networksecuritygroups | Tak | Tak |
> | networkwatchers | Tak | Tak |
> | networkwatchers / connectionmonitors | Tak | Tak |
> | networkwatchers / flowlogs | Tak | Tak |
> | networkwatchers/soczewki | Tak | Tak |
> | networkwatchers / pingmeshes | Tak | Tak |
> | p2svpngateways | Nie | Nie |
> | privatednszones | Tak | Tak |
> | privatednszones / virtualnetworklinks | Tak | Tak |
> | privateendpointredirectmaps | Nie | Nie |
> | privateendpoints | Nie | Nie |
> | privatelinkservices | Nie | Nie |
> | adresów publicipaddress | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | publicipprefixes | Tak | Tak |
> | routefilters | Nie | Nie |
> | routetables | Tak | Tak |
> | serviceendpointpolicies | Tak | Tak |
> | trafficmanagerprofiles | Tak | Tak |
> | virtualhubs | Nie | Nie |
> | virtualnetworkgateways | Tak | Tak |
> | virtualnetworks | Tak | Tak |
> | virtualnetworktaps | Nie | Nie |
> | virtualrouters | Tak | Tak |
> | virtualwans | Nie | Nie |
> | vpngateways (wirtualna sieć WAN) | Nie | Nie |
> | vpnserverconfigurations | Nie | Nie |
> | vpnsites (wirtualna sieć WAN) | Nie | Nie |
> | webapplicationfirewallpolicies | Tak | Tak |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzenie nazw | Tak | Tak |
> | przestrzenie nazw/notificationhubs | Tak | Tak |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Tak | Tak |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | Nie | Nie |
> | obszary robocze | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nie | Nie |
> | managementconfigurations | Tak | Tak |
> | rozwiązania | Tak | Tak |
> | views | Tak | Tak |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komunikacje równorzędne | Tak | Tak |
> | peeringservices | Nie | Nie |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | policyevents | Nie | Nie |
> | policystates | Nie | Nie |
> | policytrackedresources | Nie | Nie |
> | korygowania | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | pulpity nawigacyjne | Tak | Tak |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | rootresources | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Tak | Tak |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | możliwości | Tak | Tak |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wprowadzanie | Nie | Nie |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nie | Nie |
> | replicationeligibilityresults | Nie | Nie |
> | magazynów | Tak | Tak |

> [!IMPORTANT]
> Zobacz [Recovery Services wskazówki dotyczące przenoszenia](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzenie nazw | Tak | Tak |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | — zapytania | Tak | Tak |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Nie | Nie |
> | childavailabilitystatuses | Nie | Nie |
> | childresources | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | powiadomienia | Nie | Nie |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Nie | Nie |
> | łącza | Nie | Nie |
> | tags | Nie | Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacji | Tak | Nie |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | jobcollections | Tak | Tak |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | searchservices | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść kilku zasobów wyszukiwania w różnych regionach w jednej operacji. Zamiast tego należy przenieść je w osobnych operacjach.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nie | Nie |
> | advancedthreatprotectionsettings | Nie | Nie |
> | assessmentmetadata | Nie | Nie |
> | oceny | Nie | Nie |
> | automatyzacji | Tak | Tak |
> | complianceresults | Nie | Nie |
> | Zachowania zgodności prawnymi | Nie | Nie |
> | datacollectionagents | Nie | Nie |
> | datacollectionresults | Nie | Nie |
> | devicesecuritygroups | Nie | Nie |
> | informationprotectionpolicies | Nie | Nie |
> | iotsecuritysolutions | Tak | Tak |
> | servervulnerabilityassessments | Nie | Nie |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | agregacji | Nie | Nie |
> | alertrules | Nie | Nie |
> | alertruletemplates | Nie | Nie |
> | zakładki | Nie | Nie |
> | padkach | Nie | Nie |
> | połączenia dataconnecters | Nie | Nie |
> | jednostki | Nie | Nie |
> | entityqueries | Nie | Nie |
> | officeconsents | Nie | Nie |
> | settings | Nie | Nie |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | bram | Nie | Nie |
> | węzły | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzenie nazw | Tak | Tak |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacji | Nie | Nie |
> | oparty | Tak | Tak |
> | klastry/aplikacje | Nie | Nie |
> | containergroups | Nie | Nie |
> | containergroupsets | Nie | Nie |
> | edgeclusters | Nie | Nie |
> | sieci | Nie | Nie |
> | secretstores | Nie | Nie |
> | volumes | Nie | Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacji | Tak | Tak |
> | bram | Tak | Tak |
> | sieci | Tak | Tak |
> | wpisy tajne | Tak | Tak |
> | volumes | Tak | Tak |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wprowadzanie | Nie | Nie |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | SignalR | Tak | Tak |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nie | Nie |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nie | Nie |
> | aplikacji | Nie | Nie |
> | jitrequests | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | instancepools | Nie | Nie |
> | managedinstances | Nie | Nie |
> | ManagedInstances/bazy danych | Nie | Nie |
> | serwery | Tak | Tak |
> | Serwery/bazy danych | Tak | Tak |
> | serwery/elasticpools | Tak | Tak |
> | virtualclusters | Tak | Tak |

> [!IMPORTANT]
> Baza danych i serwer muszą znajdować się w tej samej grupie zasobów. Gdy przesuniesz programu SQL server, jego baz danych są również przenoszone. To zachowanie ma zastosowanie do baz danych Azure SQL Database i Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Tak | Tak |
> | sqlvirtualmachines | Tak | Tak |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dwvm | Nie | Nie |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Tak | Tak |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Tak | Tak |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | skonfigurowany | Nie | Nie |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść zadań Stream Analytics, gdy stan jest uruchomiony.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | środowiska | Nie | Nie |
> | środowiska/źródła zdarzeń | Nie | Nie |
> | wystąpienia | Nie | Nie |
> | wystąpienia/środowiska | Nie | Nie |
> | wystąpienia/środowiska/obiekty EventSource | Nie | Nie |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Anulowanie subskrypcji | Nie | Nie |

## <a name="microsoftsupport"></a>Microsoft. Support

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | createsupportticket | Nie | Nie |
> | supporttickets | Nie | Nie |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nie | Nie |
> | zasoby | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | środowiska | Tak | Tak |
> | środowiska/źródła zdarzeń | Tak | Tak |
> | środowiska/referencedatasets | Tak | Tak |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | magazyny | Tak | Tak |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nie | Nie |
> | dedicatedcloudservices | Nie | Nie |
> | virtualmachines | Nie | Nie |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Tak | Tak |
> | pakiety | Tak | Tak |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certyfikaty | Nie | Tak |
> | connectiongateways | Tak | Tak |
> | połączenia | Tak | Tak |
> | customapis | Tak | Tak |
> | hostingenvironments | Nie | Nie |
> | dopuszczalna | Tak | Tak |
> | lokacje | Tak | Tak |
> | Lokacje/premieraddons | Tak | Tak |
> | Lokacje/miejsca | Tak | Tak |
> | staticsites | Nie | Nie |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nie | Nie |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | składniki | Nie | Nie |
> | monitorinstances | Nie | Nie |
> | monitory | Nie | Nie |
> | notificationsettings | Nie | Nie |

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm obecnie nie obsługują operacji przenoszenia.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).

Aby uzyskać te same dane jak plik z wartościami rozdzielanymi przecinkami, Pobierz [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
