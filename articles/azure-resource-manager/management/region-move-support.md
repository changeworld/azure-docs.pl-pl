---
title: Obsługa przenoszenia zasobów platformy Azure między regionami
description: Wyświetla listę typów zasobów platformy Azure, które można przenosić w regionach platformy Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760712"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Obsługa przenoszenia zasobów platformy Azure między regionami

Ten artykuł potwierdza, czy typ zasobu platformy Azure jest obsługiwany w celu przeniesienia do innego regionu platformy Azure. 

Przechodzenie do obszaru nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft.aAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Konfiguracja aplikacji Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Usługa Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Automatyzacja firmy Microsoft.Automation](#microsoftautomation)
> - [Program Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Witryna Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Usługi Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Łańcuch bloków firmy Microsoft.Blockchain](#microsoftblockchain)
> - [Plan firmy Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Pamięć podręczna firmy Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Rejestracja certyfikatów firmy Microsoft.Certificate](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Program Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Kontener firmy Microsoft.Container](#microsoftcontainer)
> - [Insstancja kontenera firmy Microsoft](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Usługa kontenera firmy Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalityka](#microsoftcortanaanalytics)
> - [Zarządzanie kosztami firmy Microsoft](#microsoftcostmanagement)
> - [Informacje o klientach firmy Microsoft.Customer](#microsoftcustomerinsights)
> - [Usługi niestandardowe firmy Microsoft.Custom](#microsoftcustomproviders)
> - [Skrzynka z danymi firmy Microsoft](#microsoftdatabox)
> - [Plik Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Baza danych Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Urządzenia firmy Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Baza danych microsoft.documentdb](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridDana](#microsofthybriddata)
> - [Microsoft.ImportEksport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Centrum IoT Firmy Microsoft](#microsoftiotcentral)
> - [Przestrzenie IoT firmy Microsoft](#microsoftiotspaces)
> - [Usługa Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationSługi](#microsoftlocationservices)
> - [Logika firmy Microsoft.Logic](#microsoftlogic)
> - [Uczenie się maszyn firmy Microsoft.Machine](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps (Mapy firmy Microsoft)](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp (Microsoft.NetApp)](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Witryna Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Dane operacyjne firmy Microsoft.Operational](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Komunikacja równorzędna firmy Microsoft](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Program Microsoft.PowerBI](#microsoftpowerbi)
> - [Program Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Usługi Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Harmonogram firmy Microsoft](#microsoftscheduler)
> - [Wyszukiwanie microsoft.search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Usługa Microsoft.SignalRService](#microsoftsignalrservice)
> - [Rozwiązania firmy Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Usługa Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [System Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Program Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalityka](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Witryna Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Token firmy Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.aAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- | 
> | usługi domeny | Nie | 
> | domainservices / replicasets domainservices /replicasets domainservices/replicasets domainservice | Nie | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Najemców | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | zasady działania | Nie | 

## <a name="microsoftanalysisservices"></a>Microsoft.analysisservices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Serwerów | Nie |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | usługa |  Tak | 

## <a name="microsoftappconfiguration"></a>Konfiguracja aplikacji Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | magazyny konfiguracyjne | Nie | 

## <a name="microsoftappservice"></a>Usługa Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | apiapps | Nie | 
> | appidentities | Nie | 
> | Bramy | Nie | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | zmiany w polityce | Nie |

## <a name="microsoftautomation"></a>Automatyzacja firmy Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | konta automatyzacji | Nie | 
> | konta /konfiguracje automatyzacji | Nie | 
> | automationaccounts / runbooks | Nie | 



## <a name="microsoftazureactivedirectory"></a>Program Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | b2cdirectory | Nie | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Sqlserverregistrations | Nie |

## <a name="microsoftazurestack"></a>Witryna Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Rejestracji | Nie | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | konta partii | Nie |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Klastrów | Nie | 
> | liczba plików | Nie | 
> | Zadania | Nie | 
> | obszary robocze | Nie | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | mapapis | Nie | 

## <a name="microsoftbiztalkservices"></a>Usługi Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | biztalk | Nie | 

## <a name="microsoftblockchain"></a>Łańcuch bloków firmy Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | członkowie łańcucha bloków | Nie |
> | Obserwatorów | Nie | 

## <a name="microsoftblueprint"></a>Plan firmy Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | blueprintassignments (przydziały planów) | Nie | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | usługi botowe | Nie | 

## <a name="microsoftcache"></a>Pamięć podręczna firmy Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Redis | Nie | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nie |
> | Profile | Nie | 
> | profile / punkty końcowe | Nie | 

## <a name="microsoftcertificateregistration"></a>Rejestracja certyfikatów firmy Microsoft.Certificate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | certificateorders | Nie | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | nazwy domen | Nie |  
> | maszyny wirtualne | Nie | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | networksecuritygroups | Nie |
> | zastrzeżone | Nie | 
> | sieci wirtualnych | Nie | 

## <a name="microsoftclassicstorage"></a>Program Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | konta magazynowe | Tak |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | zestawy dostępności | Nie | 
> | zestawy dysków | Nie | 
> | Dysków | Nie | 
> | Galerie | Nie | 
> | galerie / zdjęcia | Nie | 
> | galerie / obrazy / wersje | Nie | 
> | grupy hostów | Nie | 
> | grupy hostów / hosty | Nie | 
> | images | Nie | 
> | grupy bliskozami | Nie | 
> | przystawki punktów przywracania | Nie | 
> | sharedvmimages | Nie | 
> | sharedvmimages / wersje | Nie | 
> | Migawki | Nie | 
> | maszyny wirtualne | Tak | 
> | virtualmachines / rozszerzenia | Nie | 
> | zestawy virtualmachinescalesets | Nie | 

## <a name="microsoftcontainer"></a>Kontener firmy Microsoft.Container

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | grupy kontenerowe | Nie | 

## <a name="microsoftcontainerinstance"></a>Insstancja kontenera firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | grupy kontenerowe | Nie | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Rejestrów | Nie |  
> | rejestry / buduj zadania | Nie |  
> | rejestry / replikacje | Nie | 
> | rejestry / zadania | Nie |  
> | rejestry / hook | Nie | 

## <a name="microsoftcontainerservice"></a>Usługa kontenera firmy Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | usługi kontenerowe | Nie | 
> | zarządzane clusters | Nie | 
> | otwarte łącze gołągowe | Nie | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | aplikacje | Nie | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalityka

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 

## <a name="microsoftcostmanagement"></a>Zarządzanie kosztami firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | konta integracji | Nie |  

## <a name="microsoftcustomerinsights"></a>Informacje o klientach firmy Microsoft.Customer

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | koncentratory, | Nie |  

## <a name="microsoftcustomproviders"></a>Usługi niestandardowe firmy Microsoft.Custom

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | dostawcy zasobów | Nie | 

## <a name="microsoftdatabox"></a>Skrzynka z danymi firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Zadania | Nie | 

## <a name="microsoftdataboxedge"></a>Plik Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | databoxedgedevices | Nie | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | obszary robocze | Nie | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Katalogi | Nie | 
> | datacatalogs (przetwarzanie danych) | Nie | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | menedżerów połączeń | Nie | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Pakiety | Nie | 
> | Plany | Nie | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | datafactories (fabryki danych) | Nie | 
> | Fabryk | Nie |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | konta datalakeaccounts | Nie | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | services | Nie | 
> | usługi / projekty | Nie | 
> | Slotów | Nie | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 

## <a name="microsoftdbformariadb"></a>Baza danych Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Serwerów | Nie |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Serwerów | Nie |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Servergroups | Nie | 
> | Serwerów | Nie |  
> | serweryv2 | Nie | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | zasoby artefaktów | Nie | 
> | Wdrożenia | Nie |  
> | topologie usług | Nie | 
> | topologie serwisowe / usługi | Nie |  
> | servicetopologies / usługi / jednostki serwisowe | Nie | 
> | kroki | Nie | 

## <a name="microsoftdevices"></a>Urządzenia firmy Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | baseny elastyczne | Nie | 
> | baseny elastyczne / iothubtenants | Nie | 
> | iothubs ( iothubs ) | Tak | 
> | usługi inicjowania obsługi administracyjnej | Nie | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | kontrolery | Nie | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | centra laboratoryjne | Nie | 
> | Labs | Nie | 
> | laboratoria / środowiska | Nie |  
> | laboratoria / serwisanty | Nie | 
> | laboratoria / maszyny wirtualne | Nie |  
> | Harmonogramy | Nie |  

## <a name="microsoftdocumentdb"></a>Baza danych microsoft.documentdb

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | konta baz danych | Nie | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Domen | Nie | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | services | Nie |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Domen | Nie |  
> | Tematy | Nie | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Klastrów | Nie |  
> | Obszary nazw | Nie | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | hanainstances | Nie | 
> | sapmonitory | Nie |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Klastrów | Nie | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | services | Nie |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Maszyny | Nie | 

## <a name="microsofthybriddata"></a>Microsoft.HybridDana

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | menedżerów danych |  Nie | 

## <a name="microsoftimportexport"></a>Microsoft.ImportEksport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Zadania |  Nie | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 
> | grupy działania |  Nie | 
> | activitylogalerts | Nie | 
> | zasady alertów |  Nie | 
> | automatyczne skalowanie |  Nie | 
> | Składniki |  Nie |  
> | guestdiagnosticsettings | Nie | 
> | metryczne certyfikaty | Nie | 
> | grupy powiadomień | Nie | 
> | zasady powiadomień | Nie | 
> | zasady zaplanowane |  Nie | 
> | testy internetowe |  Nie | 
> | skoroszyty |  Nie |  


## <a name="microsoftiotcentral"></a>Centrum IoT Firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | iotapps ( iotapps ) |  Nie |  

## <a name="microsoftiotspaces"></a>Przestrzenie IoT firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | dostępność sprawdzana |  Nie |  
> | Wykres |  Nie | 

## <a name="microsoftkeyvault"></a>Usługa Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | baseny hsmpools | Nie | 
> | Sklepienia |  Nie | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Klastrów |  Nie |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | konta labaccounts | Nie | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationSługi

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 

## <a name="microsoftlogic"></a>Logika firmy Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | hostingśrodowiska | Nie | 
> | konta integracyjne |  Nie |  
> | integracja | Nie | 
> | izolacja środowiska | Nie | 
> | Przepływy pracy |  Nie |  

## <a name="microsoftmachinelearning"></a>Uczenie się maszyn firmy Microsoft.Machine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | plany zobowiązań |  Nie | 
> | Webservices |  Nie | 
> | obszary robocze |  Nie | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | operationalizationclusters |  Nie | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 
> | konta / obszary robocze | Nie | 
> | konta / obszary robocze / projekty | Nie | 
> | konta teamaccounts | Nie | 
> | teamaccounts / obszary robocze | Nie | 
> | teamaccounts / obszary robocze / projekty | Nie | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelModelManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | hostingkonta | Nie | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | obszary robocze | Nie | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | identyfikatory przypisane przez użytkownika | Nie | 

## <a name="microsoftmaps"></a>Microsoft.Maps (Mapy firmy Microsoft)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts |  Nie |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | classicdevservices | Nie | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | usługi mediasce |  Nie | 
> | mediaservices / liveevents |  Nie | 
> | mediaservices / streamingendpoints |  Nie | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | appclusters | Nie | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | ocenyprojektów | Nie | 
> | migrowanieprojektów | Nie | 
> | Projektów | Nie | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp (Microsoft.NetApp)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | konta netto | Nie | 
> | netappaccounts / pojemnośćpuly | Nie | 
> | netappaccounts / pojemność / woluminy | Nie | 
> | netappaccounts / pojemność / woluminy / mounttargets | Nie | 
> | netappaccounts / pojemność / woluminy / migawki | Nie | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | applicationgateways | Nie | 
> | applicationgatewaywebapplicationfirewallpolicies | Nie | 
> | grupy zabezpieczeń aplikacji |  Nie |  
> | azurefirewalls |  Nie |  
> | bastionhosts | Nie | 
> | Połączenia |  Nie | 
> | ddoscustompolicies |  Nie | 
> | plany ochrony ddosprotection | Nie | 
> | strefy dns |  Nie | 
> | ekspresywy | Nie | 
> | ekspresowe połączenia krzyżowe | Nie | 
> | drogi ekspresowe | Nie | 
> | ekspresowe porty | Nie | 
> | przednie drzwi | Nie | 
> | frontdoorwebapplicationfirewallpolicies | Nie | 
> | równoważenia ładunków | Tak — podstawowa jednostka SKU<br>Nie - Standardowa jednostka SKU | Tak — podstawowa jednostka SKU<br> -Tak Standardowa jednostka SKU |
> | localnetworkgates |  Nie | 
> | natgateways |  Nie | 
> | polityka sieciowa |  Nie | 
> | wsiew sieci | Tak | 
> | profile sieciowe | Nie | 
> | networksecuritygroups | Tak | 
> | networkwatchers |  Nie |  
> | networkwatchers / connectionmonitors |  Nie | 
> | networkwatchers / soczewki |  Nie | 
> | networkwatchers / pingmeshes |  Nie | 
> | p2svpngates | Nie | 
> | strefa prywatna |  Nie |  
> | privatednszones / virtualnetworklinks |  Nie |  
> | punkty prywatne | Nie | 
> | usługi privatelink | Nie | 
> | reklamowe sukienki | Tak — podstawowa jednostka SKU<br>Nie - Standardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Nie - Standardowa jednostka SKU |
> | poprawki do publicznej wiadomości | Nie | 
> | filtry tras | Nie | 
> | routetables (stoły trasowe) |  Nie | 
> | serviceendpointpolicies |  Nie | 
> | profile trafficmanager |  Nie | 
> | wirtualnehuby | Nie | 
> | virtualnetworkgates |  Nie |  
> | sieci wirtualnych |  Nie | 
> | virtualnetworktaps | Nie | 
> | wirtualnewans | Nie | 
> | vpngateways (Wirtualna sieć WAN) | Nie | 
> | vpnsites (Virtual WAN) | Nie | 
> | webapplicationfirepolicies |  Nie | 


## <a name="microsoftnotificationhubs"></a>Witryna Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Obszary nazw |  Nie | 
> | przestrzenie nazw / notificationhubs |  Nie |  

## <a name="microsoftoperationalinsights"></a>Dane operacyjne firmy Microsoft.Operational

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | obszary robocze |  Nie | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | konfiguracje zarządzania |  Nie | 
> | widoki |  Nie | 

## <a name="microsoftpeering"></a>Komunikacja równorzędna firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | komunikacja równorzędna | Nie | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | pulpity nawigacyjne | Nie | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | źródła korzeni | Nie | 

## <a name="microsoftpowerbi"></a>Program Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | workspacecollections (workspacecollections) |  Nie | 

## <a name="microsoftpowerbidedicated"></a>Program Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Pojemności |  Nie | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | accounts | Nie | 

## <a name="microsoftrecoveryservices"></a>Usługi Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Sklepienia | Nie. [Wyłączanie przechowalni i odtworzenie](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) w celu odtworzenia witryny  | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Obszary nazw |  Nie | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Kwerendy |  Nie |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | aplikacje |  Nie | 

## <a name="microsoftscheduler"></a>Harmonogram firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | flows (przepływy) |  Nie |  
> | liczba miejsc pracy |  Nie | 

## <a name="microsoftsearch"></a>Wyszukiwanie microsoft.search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | usługi wyszukiwania |  Nie | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | rozwiązania iotsecuritysolutions |  Nie | 
> | konfiguracje podręczników | Nie | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Bramy | Nie | 
> | Węzłów | Nie | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Obszary nazw |  Nie | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | aplikacje | Nie | 
> | Klastrów |  Nie | 
> | klastry / aplikacje | Nie | 
> | grupy kontenerowe | Nie | 
> | zestawy grup kontenerowych | Nie | 
> | edgeclusters | Nie | 
> | Sieci | Nie | 
> | sklepy sekretowe | Nie | 
> | volumes | Nie | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | aplikacje |  Nie | 
> | grupy kontenerowe | Nie | 
> | Bramy |  Nie | 
> | Sieci |  Nie | 
> | wpisy tajne |  Nie | 
> | volumes |  Nie |  

## <a name="microsoftsignalrservice"></a>Usługa Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | signalr |  Nie |  

## <a name="microsoftsolutions"></a>Rozwiązania firmy Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | zdefiniowanie urządzeń | Nie | 
> | Urządzeń | Nie | 
> | applicationdefinitions (zdefiniowanie aplikacji) | Nie | 
> | aplikacje | Nie | 
> | jitrequests | Nie | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | baseny wystąpień | Nie | 
> | zarządzane zachowania | Tak | 
> | zarządzanie / bazy danych | Tak | 
> | Serwerów | Tak | 
> | serwery / bazy danych | Tak | 
> | serwery / baseny elastyczne | Tak | 
> | virtualclusters | Tak | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | sqlvirtualmachinegrupy |  Nie |  
> | sqlvirtualmachines |  Nie |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | dwvm ( dwvm ) | Nie | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | konta magazynowe | Tak | 

## <a name="microsoftstoragecache"></a>Usługa Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Buforuje | Nie | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | usługi magazynowania |  Nie | 

## <a name="microsoftstoragesyncdev"></a>System Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | usługi magazynowania | Nie | 

## <a name="microsoftstoragesyncint"></a>Program Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | usługi magazynowania | Nie | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Menedżerów | Nie | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalityka

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | streamingjobs |  Nie |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Środowiskach | Nie | 
> | środowiska / źródła zdarzeń | Nie | 
> | Wystąpień | Nie | 
> | instancje / środowiska | Nie | 
> | instancje / środowiska / źródła zdarzeń | Nie | 

## <a name="microsoftterraformoss"></a>Witryna Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | rejestracje dostawców | Nie | 
> | zasoby | Nie | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Środowiskach |  Nie | 
> | środowiska / źródła zdarzeń |  Nie |  
> | środowiska / referencedatasets |  Nie | 

## <a name="microsofttoken"></a>Token firmy Microsoft.Token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | Sklepy | Nie | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | płyty obrazów | Nie | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | account |  Nie | 
> | konto / rozszerzenie |  Nie | 
> | konto / projekt |  Nie | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | dedykowane chmury | Nie | 
> | usługi dedykowanecloud | Nie | 
> | maszyny wirtualne | Nie | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | certyfikaty | Nie | 
> | przyłącza |  Nie |  
> | Połączenia |  Nie |  
> | customapis |  Nie | 
> | hostingśrodowiska | Nie | 
> | farmy serwerowe |  Nie |  
> | lokacje |  Nie | 
> | strony / premieraddons |  Nie |  
> | witryny / szczeliny |  Nie |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | usługi urządzeń | Nie | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Typ zasobu | Ruch regionu | 
> | ------------- | ----------- |
> | grupy aplikacji | Nie | 
> | hostpoole | Nie | 
> | obszary robocze | Nie | 

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm obecnie nie obsługują operacji przenoszenia.
