---
title: Przenoszenie obsługi operacji przez typ zasobu platformy Azure
description: Wyświetla listę typów zasobów platformy Azure, które mogą zostać przeniesione do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: d44b1bf778c7ec9551e2fd30f67083f8dded22d1
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438472"
---
# <a name="move-operation-support-for-resources"></a>Przenoszenie obsługi operacji dla zasobów
W tym artykule wymieniono, czy typ zasobu platformy Azure obsługuje operacji przenoszenia. Mimo że typ zasobu obsługuje operację przenoszenia, może to być warunki, które uniemożliwiają przenoszonego zasobu. Aby uzyskać szczegółowe informacje o warunkach, które wpływają na operacji przenoszenia, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).

Aby uzyskać te same dane w formacie wartości rozdzielanych przecinkami, Pobierz [move-pomocy technicznej — resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| domainservices | Nie | Nie |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Dzierżaw | Nie | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| serwerów | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| usługa | Yes | Yes |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| aplikacje interfejsów API | Nie | Nie |
| appidentities | Nie | Nie |
| Bramy | Nie | Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| policyassignments | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| automationaccounts | Yes | Yes |
| automationaccounts/configurations | Yes | Yes |
| automationaccounts/elementów runbook | Yes | Yes |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| b2cdirectories | Yes | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| rejestracje | Yes | Yes |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| backupvault | Nie | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| batchaccounts | Yes | Yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Klastry | Nie | Nie |
| fileservers | Nie | Nie |
| zadania | Nie | Nie |
| obszary robocze | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| mapapis | Nie | Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| biztalk | Yes | Yes |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| blockchainmembers | Nie | Nie |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| blueprintassignments | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| botservices | Yes | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Redis Cache | Yes | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Profile | Yes | Yes |
| Profile/punktów końcowych | Yes | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| zamówień certyfikatów | Yes | Yes |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| domainnames | Yes | Nie |
| maszyn wirtualnych | Yes | Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Nie | Nie |
| reservedips | Nie | Nie |
| virtualnetworks | Nie | Nie |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| storageaccounts | Yes | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| availabilitysets | Yes | Yes |
| dyski | Yes | Yes |
| Galerie | Nie | Nie |
| galerie/obrazów | Nie | Nie |
| Galerie/obrazy/wersji | Nie | Nie |
| images | Yes | Yes |
| proximityplacementgroups | Nie | Nie |
| restorepointcollections | Nie | Nie |
| sharedvmimages | Nie | Nie |
| sharedvmimages/wersji | Nie | Nie |
| migawki | Yes | Yes |
| maszyn wirtualnych | Yes | Yes |
| maszyn wirtualnych/rozszerzenia | Yes | Yes |
| virtualmachinescalesets | Yes | Yes |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| containergroups | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| containergroups | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| rejestry | Yes | Yes |
| rejestry/buildtasks | Yes | Yes |
| rejestry/replikacji | Nie | Nie |
| rejestry/zadań | Yes | Yes |
| rejestry/elementów webhook | Yes | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| containerservices | Nie | Nie |
| managedclusters | Nie | Nie |
| openshiftmanagedclusters | Nie | Nie |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| aplikacje | Yes | Yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| przedsiębiorstw | Yes | Yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Koncentratory | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| zadania | Nie | Nie |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Nie | Nie |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| obszary robocze | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| katalogi | Yes | Yes |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| connectionmanagers | Nie | Nie |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| pakiety | Nie | Nie |
| Plany | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| datafactories | Yes | Yes |
| fabryki | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| services | Nie | Nie |
| usługi/projekty | Nie | Nie |
| gniazda | Nie | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| serwerów | Yes | Yes |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| serwerów | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| servergroups | Nie | Nie |
| serwerów | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| artifactsources | Nie | Nie |
| Wprowadzanie | Nie | Nie |
| servicetopologies | Nie | Nie |
| servicetopologies/usługi | Nie | Nie |
| servicetopologies/services/serviceunits | Nie | Nie |
| kroki | Nie | Nie |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| elasticpools | Nie | Nie |
| elasticpools/iothubtenants | Nie | Nie |
| iothubs | Yes | Yes |
| provisioningservices | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Kontrolery | Nie | Nie |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| labcenters | Nie | Nie |
| Warsztaty | Yes | Nie |
| laboratoria/servicerunners | Yes | Yes |
| laboratoria/maszyn wirtualnych | Yes | Nie |
| Harmonogramy | Nie | Nie |

## <a name="microsoftdns"></a>microsoft.dns
| Typ zasobu | Grupa zasobów | Subskrypcja |
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
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| databaseaccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| domeny | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| domeny | Yes | Yes |
| Tematy | Yes | Yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Klastry | Yes | Yes |
| przestrzenie nazw | Yes | Yes |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| hanainstances | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Klastry | Yes | Yes |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| datamanagers | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| zadania | Yes | Yes |

## <a name="microsoftinsights"></a>microsoft.insights
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |
| actiongroups | Yes | Yes |
| alerty dziennika aktywności | Nie | Nie |
| alertrules | Yes | Yes |
| wartość autoscalesettings | Yes | Yes |
| Składniki | Yes | Yes |
| guestdiagnosticsettings | Nie | Nie |
| metricalerts | Nie | Nie |
| notificationgroups | Nie | Nie |
| notificationrules | Nie | Nie |
| scheduledqueryrules | Nie | Nie |
| testy internetowe | Yes | Yes |
| skoroszyty | Yes | Yes |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| iotapps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| checknameavailability | Yes | Yes |
| wykres | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| hsmpools | Nie | Nie |
| Magazyny | Yes | Yes |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Klastry | Yes | Yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| labaccounts | Nie | Nie |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| hostingenvironments | Nie | Nie |
| integrationaccounts | Yes | Yes |
| integrationserviceenvironments | Nie | Nie |
| isolatedenvironments | Nie | Nie |
| Przepływy pracy | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| commitmentplans | Yes | Yes |
| usługi sieci Web | Yes | Nie |
| obszary robocze | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Yes | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |
| konta/obszarów roboczych | Yes | Yes |
| obszary robocze/kont/projektów | Yes | Yes |
| teamaccounts | Yes | Yes |
| teamaccounts/obszarów roboczych | Yes | Yes |
| obszary robocze/teamaccounts/projektów | Yes | Yes |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| hostingaccounts | Nie | Nie |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| obszary robocze | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| userassignedidentities | Yes | Yes |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| classicdevservices | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| mediaservices | Yes | Yes |
| mediaservices/liveevents | Yes | Yes |
| mediaservices/punkty | Yes | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| assessmentprojects | Nie | Nie |
| migrateprojects | Nie | Nie |
| Projekty | Nie | Nie |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| netappaccounts | Nie | Nie |
| netappaccounts/capacitypools | Nie | Nie |
| netappaccounts/capacitypools/volumes | Nie | Nie |
| netappaccounts/capacitypools/volumes/mounttargets | Nie | Nie |
| netappaccounts/capacitypools/volumes/snapshots | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| applicationgateways | Nie | Nie |
| applicationsecuritygroups | Yes | Yes |
| azurefirewalls | Yes | Yes |
| bastionhosts | Nie | Nie |
| połączenia | Yes | Yes |
| ddoscustompolicies | Yes | Yes |
| ddosprotectionplans | Nie | Nie |
| dnszones | Yes | Yes |
| expressroutecircuits | Nie | Nie |
| expressroutecrossconnections | Nie | Nie |
| expressroutegateways | Nie | Nie |
| expressrouteports | Nie | Nie |
| usługi Frontdoor | Yes | Yes |
| frontdoorwebapplicationfirewallpolicies | Yes | Yes |
| interfaceendpoints | Nie | Nie |
| loadbalancers | Yes | Yes |
| localnetworkgateways | Yes | Yes |
| natgateways | Yes | Yes |
| networkintentpolicies | Yes | Yes |
| interfejsy | Yes | Yes |
| networkprofiles | Nie | Nie |
| networksecuritygroups | Yes | Yes |
| networkwatchers | Yes | Yes |
| networkwatchers/connectionmonitors | Yes | Yes |
| networkwatchers/obiektywów | Yes | Yes |
| networkwatchers/pingmeshes | Yes | Yes |
| p2svpngateways | Nie | Nie |
| privatelinkservices | Nie | Nie |
| publicipaddresses | Yes | Yes |
| publicipprefixes | Yes | Yes |
| routefilters | Nie | Nie |
| routetables | Yes | Yes |
| securegateways | Nie | Nie |
| serviceendpointpolicies | Yes | Yes |
| trafficmanagerprofiles | Yes | Yes |
| virtualhubs | Nie | Nie |
| virtualnetworkgateways | Yes | Yes |
| virtualnetworks | Yes | Yes |
| virtualnetworktaps | Nie | Nie |
| virtualwans | Nie | Nie |
| vpngateways | Nie | Nie |
| vpnsites | Yes | Yes |
| webapplicationfirewallpolicies | Yes | Yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| przestrzenie nazw | Yes | Yes |
| przestrzenie nazw/notificationhubs | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| obszary robocze | Yes | Yes |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| managementconfigurations | Yes | Yes |
| rozwiązania | Yes | Yes |
| wyświetlenia | Yes | Yes |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Pulpity nawigacyjne | Yes | Yes |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| rootresources | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Pojemności | Yes | Yes |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| accounts | Nie | Nie |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Magazyny | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| przestrzenie nazw | Yes | Yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| aplikacje | Yes | Nie |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| przepływy | Yes | Yes |
| kolekcjach | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| searchservices | Yes | Yes |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Bramy | Nie | Nie |
| węzły | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| przestrzenie nazw | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| aplikacje | Nie | Nie |
| Klastry | Yes | Yes |
| containergroups | Nie | Nie |
| containergroupsets | Nie | Nie |
| edgeclusters | Nie | Nie |
| Sieci | Nie | Nie |
| secretstores | Nie | Nie |
| volumes | Nie | Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| aplikacje | Yes | Yes |
| containergroups | Nie | Nie |
| Bramy | Yes | Yes |
| Sieci | Yes | Yes |
| wpisy tajne | Yes | Yes |
| volumes | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| signalr | Yes | Yes |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Nie | Nie |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Nie | Nie |
| urządzenia | Nie | Nie |
| applicationdefinitions | Nie | Nie |
| aplikacje | Nie | Nie |
| jitrequests | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| managedinstances | Yes | Yes |
| managedinstances/baz danych | Yes | Yes |
| serwerów | Yes | Yes |
| serwery/baz danych | Yes | Yes |
| servers/elasticpools | Yes | Yes |
| servers/jobaccounts | Nie | Nie |
| servers/jobagents | Nie | Nie |
| virtualclusters | Yes | Yes |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Yes | Yes |
| sqlvirtualmachines | Yes | Yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| dwvm | Nie | Nie |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| storageaccounts | Yes | Yes |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| storagesyncservices | Yes | Yes |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Menedżerowie | Nie | Nie |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| streamingjobs | Yes | Yes |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Środowiska | Nie | Nie |
| środowiska/eventsources | Nie | Nie |
| wystąpienia | Nie | Nie |
| wystąpienia/środowisk | Nie | Nie |
| środowiska/wystąpienia/eventsources | Nie | Nie |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| providerregistrations | Nie | Nie |
| zasoby | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Środowiska | Yes | Yes |
| środowiska/eventsources | Yes | Yes |
| środowiska/referencedatasets | Yes | Yes |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| imagetemplates | Nie | Nie |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| account | Yes | Yes |
| konta i rozszerzeń | Yes | Yes |
| konto lub projekt docelowy | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| Certyfikaty | Nie | Yes |
| connectiongateways | Yes | Yes |
| połączenia | Yes | Yes |
| customapis | Yes | Yes |
| hostingenvironments | Nie | Nie |
| farm serwerów | Yes | Yes |
| lokacje | Yes | Yes |
| sites/premieraddons | Yes | Yes |
| Lokacje/miejsca | Yes | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | ----------- | ---------- |
| deviceservices | Yes | Yes |

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm nie obsługują obecnie operacji przenoszenia.

## <a name="next-steps"></a>Kolejne kroki
Poleceń przenieść zasoby można znaleźć [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).
