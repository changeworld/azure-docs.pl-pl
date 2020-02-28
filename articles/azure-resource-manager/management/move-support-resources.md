---
title: Obsługa operacji przenoszenia według typu zasobu
description: Wyświetla listę typów zasobów platformy Azure, które można przenieść do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 8ab194ad240e4f3e0994314ef9ade3bc7159cf81
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671850"
---
# <a name="move-operation-support-for-resources"></a>Obsługa operacji przenoszenia dla zasobów
W tym artykule przedstawiono, czy typ zasobu platformy Azure obsługuje operację przenoszenia. Zawiera również informacje dotyczące specjalnych warunków, które należy wziąć pod uwagę podczas przeniesienia zasobu.

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. usługi azureactivedirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Batch](#microsoftbatch)
> - [Microsoft. Batchai Job](#microsoftbatchai)
> - [Microsoft. rozliczenia](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. łańcucha bloków](#microsoftblockchain)
> - [Microsoft. plan](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. COMPUTE](#microsoftcompute)
> - [Microsoft. zużycie](#microsoftconsumption)
> - [Microsoft. Container](#microsoftcontainer)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. datacegły](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft. dataconnect](#microsoftdataconnect)
> - [Microsoft. dataexchange](#microsoftdataexchange)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. datalake](#microsoftdatalake)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. kontach datalakestore](#microsoftdatalakestore)
> - [Migracja Microsoft.](#microsoftdatamigration)
> - [Microsoft. dataprotection](#microsoftdataprotection)
> - [Microsoft. dataudział](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. Deploymentmanager](#microsoftdeploymentmanager)
> - [Microsoft. urządzenia](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. wspólny](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kluczy — magazyn](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrowanie](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. Komunikacja równorzędna](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. servicefabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft. Storage](#microsoftstorage)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. Support](#microsoftsupport)
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
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
> | actionrules | Yes | Yes |
> | alerts | Nie | Nie |
> | alertssummary | Nie | Nie |
> | smartdetectoralertrules | Yes | Yes |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwery | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługa | Yes | Yes |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | configurationstores | Yes | Yes |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Spring | Yes | Yes |

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
> | automationaccounts | Yes | Yes |
> | automationaccounts/konfiguracje | Yes | Yes |
> | automationaccounts/elementy Runbook | Yes | Yes |

> [!IMPORTANT]
> Elementy Runbook muszą istnieć w tej samej grupie zasobów co konto usługi Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Yes | Yes |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Nie | Nie |
> | postgresinstances | Nie | Nie |
> | sqlbigdataclusters | Nie | Nie |
> | wystąpienia sqlinstances | Nie | Nie |
> | sqlserverregistrations | Yes | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | rejestracje | Yes | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Yes | Yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Nie | Nie |
> | fileservers | Nie | Nie |
> | Zadania | Nie | Nie |
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
> | obserwatorów | Nie | Nie |

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
> | botservices | Yes | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Redis | Yes | Yes |

> [!IMPORTANT]
> Jeśli wystąpienie usługi Azure cache for Redis jest skonfigurowane przy użyciu sieci wirtualnej, nie można przenieść wystąpienia do innej subskrypcji. Zobacz [ograniczenia dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Yes | Yes |
> | profile | Yes | Yes |
> | Profile/punkty końcowe | Yes | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certificateorders | Yes | Yes |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | domainnames | Yes | Nie |
> | virtualmachines | Yes | Nie |

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
> | storageaccounts | Yes | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Yes | Yes |
> | diskencryptionsets | Nie | Nie |
> | dysku | Yes | Yes |
> | znajduj | Nie | Nie |
> | Galerie/obrazy | Nie | Nie |
> | Galerie/obrazy/wersje | Nie | Nie |
> | hostgroups | Nie | Nie |
> | hostgroups/hosty | Nie | Nie |
> | images | Yes | Yes |
> | proximityplacementgroups | Nie | Nie |
> | restorepointcollections | Nie | Nie |
> | sharedvmimages | Nie | Nie |
> | sharedvmimages/wersje | Nie | Nie |
> | Migawki | Yes | Yes |
> | virtualmachines | Yes | Yes |
> | virtualmachines/rozszerzenia | Yes | Yes |
> | virtualmachinescalesets | Yes | Yes |

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
> | wyrobów | Nie | Nie |
> | reservationdetails | Nie | Nie |
> | reservationrecommendations | Nie | Nie |
> | reservationsummaries | Nie | Nie |
> | reservationtransactions | Nie | Nie |
> | tagów | Nie | Nie |
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
> | wołuje | Yes | Yes |
> | rejestry/BuildTasks | Yes | Yes |
> | rejestry/replikacje | Yes | Yes |
> | rejestry/taskruns | Yes | Yes |
> | rejestry/zadania | Yes | Yes |
> | rejestry/elementy webhook | Yes | Yes |

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
> | aplikacje | Nie | Nie |

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
> | przedsiębiorstw | Yes | Yes |
> | wymiary | Nie | Nie |
> | eksporty | Nie | Nie |
> | externalsubscriptions | Nie | Nie |
> | prognozy | Nie | Nie |
> | query | Nie | Nie |
> | Reportconfigs | Nie | Nie |
> | reports | Nie | Nie |
> | showbackrules | Nie | Nie |
> | Widoki | Nie | Nie |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | węzłów | Nie | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | skojarzenia | Nie | Nie |
> | resourceproviders | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Zadania | Nie | Nie |

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
> | wykazy | Yes | Yes |
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
> | fabryki datafactors | Yes | Yes |
> | fabryki | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

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
> | accounts | Yes | Yes |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwery | Yes | Yes |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwery | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | servergroups | Nie | Nie |
> | serwery | Yes | Yes |
> | serversv2 | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | artifactsources | Yes | Yes |
> | wprowadzanie | Yes | Yes |
> | servicetopologie | Yes | Yes |
> | servicetopologie/usługi | Yes | Yes |
> | servicetopologie/usługi/serviceunits | Yes | Yes |
> | kroki | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nie | Nie |
> | elasticpools / iothubtenants | Nie | Nie |
> | iothubs | Yes | Yes |
> | provisioningservices | Yes | Yes |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | potoki | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Rejestrowanie | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | labcenters | Nie | Nie |
> | labs | Yes | Nie |
> | laboratoria/środowiska | Yes | Yes |
> | laboratoria/servicerunner | Yes | Yes |
> | Labs/virtualmachines | Yes | Nie |
> | Uruchamianie | Yes | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nie | Nie |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | domeny | Yes | Yes |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | domeny | Yes | Yes |
> | eventSubscriptions | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. |
> | eventsubscriptions | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. |
> | extensiontopics | Nie | Nie |
> | opisano | Yes | Yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Yes | Yes |
> | przestrzenie nazw | Yes | Yes |

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
> | sapmonitors | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Yes | Yes |

> [!IMPORTANT]
> Klastry HDInsight można przenieść do nowej subskrypcji lub grupy zasobów. Jednak nie można przenosić między subskrypcjami, zasoby sieciowe połączone z klastrem HDInsight (na przykład sieci wirtualnej, kart interfejsu Sieciowego lub modułu równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów interfejsu Sieciowego, który jest dołączony do maszyny wirtualnej do klastra.
>
> Podczas przenoszenia klastra usługi HDInsight do nowej subskrypcji, należy najpierw przenieść innych zasobów (np. konta magazynu). Następnie przenieś klaster HDInsight samodzielnie.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Yes | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Win64 | Yes | Yes |
> | Maszyny/rozszerzenia | Nie | Nie |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | datamanagers | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Zadania | Yes | Yes |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | actiongroups | Yes | Yes |
> | activitylogalerts | Nie | Nie |
> | alertrules | Yes | Yes |
> | autoscalesettings | Yes | Yes |
> | punkt odniesienia | Nie | Nie |
> | calculatebaseline | Nie | Nie |
> | składniki | Yes | Yes |
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
> | privatelinkscopes | Yes | Yes |
> | scheduledqueryrules | Yes | Yes |
> | topology | Nie | Nie |
> | transakcje | Nie | Nie |
> | vminsightsonboardingstatuses | Nie | Nie |
> | testów internetowych | Yes | Yes |
> | skoroszyty | Yes | Yes |
> | workbooktemplates | Yes | Yes |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | iotapps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Yes | Yes |
> | graph | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | magazynów | Yes | Yes |

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
> | oparty | Yes | Yes |

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
> | integrationaccounts | Yes | Yes |
> | integrationserviceenvironments | Yes | Nie |
> | integrationserviceenvironments/managedapis | Yes | Nie |
> | isolatedenvironments | Nie | Nie |
> | przepływy pracy | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Yes | Yes |
> | WebServices | Yes | Nie |
> | obszary robocze | Yes | Yes |

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
> | obszary robocze/obliczenia | Nie | Nie |

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
> | accounts | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | MediaServices | Yes | Yes |
> | MediaServices/liveevents | Yes | Yes |
> | MediaServices/streamingendpoints | Yes | Yes |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | appclusters | Nie | Nie |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Yes | Yes |
> | migrateprojects | Yes | Yes |
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
> | applicationsecuritygroups | Yes | Yes |
> | azurefirewalls | Yes | Yes |
> | bastionhosts | Nie | Nie |
> | połączenia | Yes | Yes |
> | ddoscustompolicies | Yes | Yes |
> | ddosprotectionplans | Nie | Nie |
> | dnszones | Yes | Yes |
> | expressroutecircuits | Nie | Nie |
> | expressroutegateways | Nie | Nie |
> | firewallpolicies | Yes | Yes |
> | usługi frontdoor | Nie | Nie |
> | frontdoorwebapplicationfirewallpolicies | Nie | Nie |
> | ipgroups | Yes | Yes |
> | loadbalancers | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | localnetworkgateways | Yes | Yes |
> | networkexperimentprofiles | Yes | Yes |
> | networkintentpolicies | Yes | Yes |
> | networkinterfaces | Yes | Yes |
> | networkprofiles | Nie | Nie |
> | networksecuritygroups | Yes | Yes |
> | networkwatchers | Yes | Nie |
> | networkwatchers / connectionmonitors | Yes | Nie |
> | networkwatchers / flowlogs | Yes | Nie |
> | networkwatchers/soczewki | Yes | Nie |
> | networkwatchers / pingmeshes | Yes | Nie |
> | p2svpngateways | Nie | Nie |
> | privatednszones | Yes | Yes |
> | privatednszones / virtualnetworklinks | Yes | Yes |
> | privateendpointredirectmaps | Nie | Nie |
> | privateendpoints | Nie | Nie |
> | privatelinkservices | Nie | Nie |
> | adresów publicipaddress | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | publicipprefixes | Yes | Yes |
> | routefilters | Nie | Nie |
> | routetables | Yes | Yes |
> | serviceendpointpolicies | Yes | Yes |
> | trafficmanagerprofiles | Yes | Yes |
> | virtualhubs | Nie | Nie |
> | virtualnetworkgateways | Yes | Yes |
> | virtualnetworks | Yes | Yes |
> | virtualnetworktaps | Nie | Nie |
> | virtualrouters | Yes | Yes |
> | virtualwans | Nie | Nie |
> | vpngateways (wirtualna sieć WAN) | Nie | Nie |
> | vpnserverconfigurations | Nie | Nie |
> | vpnsites (wirtualna sieć WAN) | Nie | Nie |
> | webapplicationfirewallpolicies | Yes | Yes |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzenie nazw | Yes | Yes |
> | przestrzenie nazw/notificationhubs | Yes | Yes |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | Nie | Nie |
> | obszary robocze | Yes | Yes |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nie | Nie |
> | managementconfigurations | Yes | Yes |
> | rozwiązania | Yes | Yes |
> | Widoki | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komunikacje równorzędne | Yes | Yes |
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
> | pulpity nawigacyjne | Yes | Yes |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | rootresources | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | możliwości | Yes | Yes |

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
> | magazynów | Yes | Yes |

> [!IMPORTANT]
> Zobacz [Recovery Services wskazówki dotyczące przenoszenia](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzenie nazw | Yes | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zapytania | Yes | Yes |

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
> | tagów | Nie | Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Yes | Nie |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | searchservices | Yes | Yes |

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
> | automatyzacji | Yes | Yes |
> | complianceresults | Nie | Nie |
> | Zachowania zgodności prawnymi | Nie | Nie |
> | datacollectionagents | Nie | Nie |
> | devicesecuritygroups | Nie | Nie |
> | informationprotectionpolicies | Nie | Nie |
> | iotsecuritysolutions | Yes | Yes |
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
> | dataconnectorscheckrequirements | Nie | Nie |
> | jednostki | Nie | Nie |
> | entityqueries | Nie | Nie |
> | Zdarzenia | Nie | Nie |
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
> | przestrzenie nazw | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Nie | Nie |
> | oparty | Yes | Yes |
> | klastry/aplikacje | Nie | Nie |
> | containergroups | Nie | Nie |
> | containergroupsets | Nie | Nie |
> | edgeclusters | Nie | Nie |
> | managedclusters | Nie | Nie |
> | sieci | Nie | Nie |
> | secretstores | Nie | Nie |
> | volumes | Nie | Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Yes | Yes |
> | bram | Yes | Yes |
> | sieci | Yes | Yes |
> | wpisy tajne | Yes | Yes |
> | volumes | Yes | Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wprowadzanie | Nie | Nie |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | SignalR | Yes | Yes |

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
> | aplikacje | Nie | Nie |
> | jitrequests | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | instancepools | Nie | Nie |
> | managedinstances | Nie | Nie |
> | ManagedInstances/bazy danych | Nie | Nie |
> | serwery | Yes | Yes |
> | Serwery/bazy danych | Yes | Yes |
> | serwery/elasticpools | Yes | Yes |
> | serwery/jobaccounts | Yes | Yes |
> | serwery/jobagents | Yes | Yes |
> | virtualclusters | Yes | Yes |

> [!IMPORTANT]
> Baza danych i serwer muszą znajdować się w tej samej grupie zasobów. Gdy przesuniesz programu SQL server, jego baz danych są również przenoszone. To zachowanie ma zastosowanie do baz danych Azure SQL Database i Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Yes | Yes |
> | sqlvirtualmachines | Yes | Yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dwvm | Nie | Nie |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Yes | Yes |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Yes | Yes |

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
> | streamingjobs | Yes | Yes |

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
> | środowiska | Yes | Yes |
> | środowiska/źródła zdarzeń | Yes | Yes |
> | środowiska/referencedatasets | Yes | Yes |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | magazyny | Yes | Yes |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | account | Nie | Nie |
> | konto/rozszerzenie | Yes | Yes |
> | konto/projekt | Yes | Yes |

> [!IMPORTANT]
> Aby zmienić subskrypcję usługi Azure DevOps, zobacz [zmiana subskrypcji platformy Azure używanej do rozliczania](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

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
> | accounts | Yes | Yes |
> | pakiety | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certyfikaty | Nie | Yes |
> | connectiongateways | Yes | Yes |
> | połączenia | Yes | Yes |
> | customapis | Yes | Yes |
> | hostingenvironments | Nie | Nie |
> | dopuszczalna | Yes | Yes |
> | teren | Yes | Yes |
> | Lokacje/premieraddons | Yes | Yes |
> | Lokacje/miejsca | Yes | Yes |
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
