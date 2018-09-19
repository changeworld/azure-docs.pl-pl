---
title: Przenoszenie obsługi operacji przez typ zasobu platformy Azure | Dokumentacja firmy Microsoft
description: Wyświetla listę typów zasobów platformy Azure, które mogą zostać przeniesione do nowej grupy zasobów lub subskrypcji.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: tomfitz
ms.openlocfilehash: 4ec09f5ed57dca89934efa750eed05e1ebcb1216
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46298289"
---
# <a name="move-operation-support-for-resources"></a>Przenoszenie obsługi operacji dla zasobów

W tym artykule wymieniono, czy typ zasobu platformy Azure obsługuje operacji przenoszenia. Mimo że typ zasobu obsługuje operację przenoszenia, może to być warunki, które uniemożliwiają przenoszonego zasobu. Aby uzyskać szczegółowe informacje o warunkach, które wpływają na operacji przenoszenia, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).

## <a name="find-resource-provider-and-resource-type"></a>Znajdź dostawcę zasobów i typu zasobu

Aby ustalić, czy można przenieść zasobów, możesz znaleźć jego dostawcy zasobów i typu zasobu.

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzureRmResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, reourcetype:type}'
```

Typ zasobu jest zwracany w formacie `<resource-provider>/<resource-type-name>`. Dlatego wartość `Microsoft.OperationalInsights/workspaces` dostawca zasobów ma **Microsoft.OperationalInsights** i nazwa typu zasobu **obszary robocze**.

Po znalezieniu dostawcy zasobów i typu zasobu, użyj tabel w tym artykule, aby sprawdzić, czy typ zasobu obsługuje operacji przenoszenia.

## <a name="microsoftaad"></a>Microsoft.AAD

| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | --------------- | ----------- |
| domainservices | Nie | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| serwerów | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | --------------- | ----------- |
| usługa | Yes | Yes |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | --------------- | ----------- |
| policyassignments | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| automationaccounts | Yes | Yes |
| automationaccounts/konfiguracji | Yes | Yes |
| automationaccounts/elementów runbook | Yes | Yes |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| b2cdirectories | Yes | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| rejestracje | Yes | Yes |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| backupvault | Nie | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| batchaccounts | Yes | Yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Klastry | Nie | Nie |
| fileservers | Nie | Nie |
| zadania | Nie | Nie |
| obszary robocze | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| mapapis | Nie | Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| biztalk | Yes | Yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| blueprintassignments | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| botservices | Yes | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Redis Cache | Yes | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Profile | Yes | Yes |
| Profile/punktów końcowych | Yes | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| zamówień certyfikatów | Yes | Yes |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| domainnames | Yes | Nie |
| maszyn wirtualnych | Yes | Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| networksecuritygroups | Nie | Nie |
| zastrzeżonych adresów IP | Nie | Nie |
| virtualnetworks | Nie | Nie |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| storageaccounts | Yes | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| availabilitysets | Yes | Yes |
| Dyski | Yes | Yes |
| Galerie | Nie | Nie |
| galerie/obrazów | Nie | Nie |
| Galerie/obrazy/wersji | Nie | Nie |
| images | Yes | Yes |
| restorepointcollections | Nie | Nie |
| sharedvmimages | Nie | Nie |
| sharedvmimages/wersji | Nie | Nie |
| migawki | Yes | Yes |
| maszyn wirtualnych | Yes | Yes |
| maszyn wirtualnych/rozszerzenia | Yes | Yes |
| virtualmachinescalesets | Yes | Yes |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| containergroups | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| containergroups | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| rejestry | Yes | Yes |
| rejestry/buildtasks | Yes | Yes |
| rejestry/replikacji | Nie | Nie |
| rejestry/elementów webhook | Yes | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| containerservices | Nie | Nie |
| managedclusters | Nie | Nie |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| aplikacje | Yes | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| przedsiębiorstw | Yes | Yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Koncentratory | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| zadania | Nie | Nie |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| databoxedgedevices | Nie | Nie |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| obszary robocze | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| katalogi | Yes | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| datafactories | Yes | Yes |
| fabryki | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| datalakeaccounts | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| services | Nie | Nie |
| usługi/projekty | Nie | Nie |
| gniazda | Nie | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| serwerów | Nie | Nie |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| serwerów | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| servergroups | Nie | Nie |
| serwerów | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| iothubs | Yes | Yes |
| provisioningservices | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| labcenters | Nie | Nie |
| Warsztaty | Yes | Nie |
| laboratoria/servicerunners | Yes | Yes |
| laboratoria/maszyn wirtualnych | Yes | Nie |
| Harmonogramy | Nie | Nie |

## <a name="microsoftdns"></a>Microsoft.DNS
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
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
| ------------- | -------------- | ------------ |
| databaseaccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| domeny | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Tematy | Yes | Yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Klastry | Yes | Yes |
| Przestrzenie nazw | Yes | Yes |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| hanainstances | Yes | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| dedicatedhsms | Nie | Nie |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Klastry | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| zadania | Yes | Yes |

## <a name="microsoftinsights"></a>Microsoft.insights
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| actiongroups | Yes | Yes |
| alerty dziennika aktywności | Nie | Nie |
| alertrules | Yes | Yes |
| wartość autoscalesettings | Yes | Yes |
| Składniki | Yes | Yes |
| metricalerts | Nie | Nie |
| scheduledqueryrules | Yes | Yes |
| testy internetowe | Yes | Yes |
| Skoroszyty | Yes | Yes |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| iotapps | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Magazyny | Yes | Yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| labaccounts | Yes | Yes |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| integrationaccounts | Yes | Yes |
| Przepływy pracy | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| commitmentplans | Yes | Yes |
| usługi sieci Web | Yes | Nie |
| obszary robocze | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| operationalizationclusters | Yes | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |
| konta/obszarów roboczych | Yes | Yes |
| obszary robocze/kont/projektów | Yes | Yes |
| teamaccounts | Yes | Yes |
| teamaccounts/obszarów roboczych | Yes | Yes |
| obszary robocze/teamaccounts/projektów | Yes | Yes |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| userassignedidentities | Yes | Yes |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| accounts | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| classicdevservices | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| mediaservices | Yes | Yes |
| mediaservices/liveevents | Yes | Yes |
| mediaservices/punkty | Yes | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Projekty | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| applicationgateways | Nie | Nie |
| applicationsecuritygroup | Yes | Yes |
| połączenia | Yes | Yes |
| ddosprotectionplans | Nie | Nie |
| dnszones | Yes | Yes |
| expressroutecircuits | Nie | Nie |
| expressrouteports | Nie | Nie |
| modułami modułami | Yes | Yes |
| localnetworkgateways | Yes | Yes |
| networkintentpolicies | Yes | Yes |
| interfejsy | Yes | Yes |
| networksecuritygroups | Yes | Yes |
| networkwatchers | Yes | Yes |
| networkwatchers/connectionmonitors | Yes | Yes |
| networkwatchers/obiektywów | Yes | Yes |
| networkwatchers/pingmeshes | Yes | Yes |
| publicipaddresses | Yes | Yes |
| publicipprefixes | Yes | Yes |
| routefilters | Nie | Nie |
| routetables | Yes | Yes |
| trafficmanagerprofiles | Yes | Yes |
| elementów virtualnetworkgateway | Yes | Yes |
| virtualnetworks | Yes | Yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Przestrzenie nazw | Yes | Yes |
| przestrzenie nazw/notificationhubs | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| obszary robocze | Yes | Yes |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| managementconfigurations | Yes | Yes |
| rozwiązania | Yes | Yes |
| wyświetlenia | Yes | Yes |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Pulpity nawigacyjne | Yes | Yes |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Pojemności | Yes | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Magazyny | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Przestrzenie nazw | Yes | Yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| aplikacje | Yes | Nie |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| przepływy | Yes | Yes |
| kolekcjach | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| searchservices | Yes | Yes |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Przestrzenie nazw | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Klastry | Yes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| aplikacje | Yes | Yes |
| Sieci | Yes | Yes |
| volumes | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| signalr | Yes | Yes |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| siterecoveryvault | Nie | Nie |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| appliancedefinitions | Nie | Nie |
| urządzenia | Nie | Nie |
| applicationdefinitions | Nie | Nie |
| aplikacje | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| serwerów | Yes | Yes |
| serwery/baz danych | Yes | Yes |
| serwery/elasticpools | Yes | Yes |
| virtualclusters | Yes | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| storageaccounts | Yes | Yes |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| storagesyncservices | Yes | Yes |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Menedżerowie | Nie | Nie |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| streamingjobs | Yes | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Środowiska | Yes | Yes |
| środowiska/eventsources | Yes | Yes |
| środowiska/referencedatasets | Yes | Yes |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| account | Yes | Yes |
| konta i rozszerzeń | Yes | Yes |
| konto lub projekt docelowy | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| Certyfikaty | Nie | Yes |
| classicmobileservices | Nie | Nie |
| connectiongateways | Yes | Yes |
| połączenia | Yes | Yes |
| customapis | Yes | Yes |
| hostingenvironments | Nie | Nie |
| farm serwerów | Yes | Yes |
| Lokacje | Yes | Yes |
| Lokacje/premieraddons | Yes | Yes |
| Lokacje/miejsca | Yes | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ zasobu | Grupa zasobów | Subskrypcja |
| ------------- | -------------- | ------------ |
| deviceservices | Yes | Yes |


## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm nie obsługują obecnie operacji przenoszenia. Te dostawcy zasobów to:

* 84codes. Usługa CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>Kolejne kroki

* Poleceń przenieść zasoby można znaleźć [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).
