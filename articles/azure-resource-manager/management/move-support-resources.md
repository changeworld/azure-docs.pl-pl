---
title: Przenoszenie obsługi operacji według typu zasobu
description: Wyświetla listę typów zasobów platformy Azure, które można przenieść do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 90fbec4dc076feb1fee8c38cf9757d3c5ddbafaf
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804796"
---
# <a name="move-operation-support-for-resources"></a>Obsługa operacji przenoszenia dla zasobów
W tym artykule wymieniono, czy typ zasobu platformy Azure obsługuje operację przenoszenia. Zawiera również informacje o specjalnych warunkach, które należy wziąć pod uwagę podczas przenoszenia zasobu.

Przechodzenie do obszaru nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft.aAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Doradca firmy Microsoft](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Konfiguracja aplikacji Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Platforma Microsoft.AppPlatform](#microsoftappplatform)
> - [Usługa Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Automatyzacja firmy Microsoft.Automation](#microsoftautomation)
> - [Program Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Witryna Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Rozliczenia firmy Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Usługi Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Łańcuch bloków firmy Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Plan firmy Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Pamięć podręczna firmy Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Rejestracja certyfikatów firmy Microsoft.Certificate](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Program Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.Cognition](#microsoftcognition)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Zużycie przez firmę Microsoft](#microsoftconsumption)
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
> - [Microsoft.DataProtection](#microsoftdataprotection)
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
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Baza danych microsoft.documentdb](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Eksperymenty firmy Microsoft](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Konfiguracja witryny Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridDana](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportEksport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Centrum IoT Firmy Microsoft](#microsoftiotcentral)
> - [Przestrzenie IoT firmy Microsoft](#microsoftiotspaces)
> - [Usługa Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
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
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Usługi zarządzane przez firmę Microsoft.Managed](#microsoftmanagedservices)
> - [Microsoft.Maps (Mapy firmy Microsoft)](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp (Microsoft.NetApp)](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Witryna Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Sklep Microsoft.ObjectStore](#microsoftobjectstore)
> - [Dane operacyjne firmy Microsoft.Operational](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Komunikacja równorzędna firmy Microsoft](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Program Microsoft.PowerBI](#microsoftpowerbi)
> - [Program Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Usługi Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Zmiana redhatopensau w systemie Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Zasoby firmy Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Wyszukiwanie microsoft.search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Witryny zabezpieczeń firmy Microsoft.Security](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Usługa Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
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
> - [Subskrypcja firmy Microsoft.Subscription](#microsoftsubscription)
> - [pomoc techniczna firmy microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Witryna Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Token firmy Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Menedżer microsoft.Vnf](#microsoftvnfmanager)
> - [Usługa Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.aAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługi domeny | Nie | Nie |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Najemców | Nie | Nie |

## <a name="microsoftadvisor"></a>Doradca firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konfiguracji | Nie | Nie |
> | zalecenia | Nie | Nie |
> | Suppressions | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zasady działania | Tak | Tak |
> | alerts | Nie | Nie |
> | alertssummary | Nie | Nie |
> | smartdetectoralertrules | Tak | Tak |

## <a name="microsoftanalysisservices"></a>Microsoft.analysisservices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Serwerów | Tak | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługa | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść usługi zarządzania interfejsami API ustawionej na jednostkę SKU zużycia.

## <a name="microsoftappconfiguration"></a>Konfiguracja aplikacji Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | magazyny konfiguracyjne | Tak | Tak |

## <a name="microsoftappplatform"></a>Platforma Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Wiosna | Tak | Tak |

## <a name="microsoftappservice"></a>Usługa Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | apiapps | Nie | Nie |
> | appidentities | Nie | Nie |
> | Bramy | Nie | Nie |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia usługi app service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Checkaccess | Nie | Nie |
> | denyassignments (odm. | Nie | Nie |
> | findorphanroleassignments (znajdźorfanrolessignments) | Nie | Nie |
> | Blokad | Nie | Nie |
> | Uprawnienia | Nie | Nie |
> | zmiany w polityce | Nie | Nie |
> | Policydefinitions | Nie | Nie |
> | zdefiniowanie zasad | Nie | Nie |
> | roleassignments | Nie | Nie |
> | roleassignmentsusagemetrics | Nie | Nie |
> | definicje ról | Nie | Nie |

## <a name="microsoftautomation"></a>Automatyzacja firmy Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konta automatyzacji | Tak | Tak |
> | konta /konfiguracje automatyzacji | Tak | Tak |
> | automationaccounts / runbooks | Tak | Tak |

> [!IMPORTANT]
> Elementy runbook muszą istnieć w tej samej grupie zasobów co konto automatyzacji.

## <a name="microsoftazureactivedirectory"></a>Program Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | b2cdirectory | Tak | Tak |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Nie | Nie |
> | postgresinstances | Nie | Nie |
> | sqlinstances | Nie | Nie |
> | Sqlserverregistrations | Tak | Tak |

## <a name="microsoftazurestack"></a>Witryna Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Rejestracji | Tak | Tak |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konta partii | Tak | Tak |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Klastrów | Nie | Nie |
> | liczba plików | Nie | Nie |
> | Zadania | Nie | Nie |
> | obszary robocze | Nie | Nie |

## <a name="microsoftbilling"></a>Rozliczenia firmy Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | okresy rozliczeniowe | Nie | Nie |
> | rozliczeniawykonań | Nie | Nie |
> | rozliczeniaroleassignments | Nie | Nie |
> | rozliczeniaroledefinitions | Nie | Nie |
> | tworzeniezastawyroleassignment | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | mapapis | Nie | Nie |

## <a name="microsoftbiztalkservices"></a>Usługi Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | biztalk | Nie | Nie |

## <a name="microsoftblockchain"></a>Łańcuch bloków firmy Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | członkowie łańcucha bloków | Nie | Nie |
> | cordamembers | Nie | Nie |
> | Obserwatorów | Nie | Nie |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługi tokenów | Nie | Nie |

## <a name="microsoftblueprint"></a>Plan firmy Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | blueprintassignments (przydziały planów) | Nie | Nie |
> | Plany | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługi botowe | Tak | Tak |

## <a name="microsoftcache"></a>Pamięć podręczna firmy Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Redis | Tak | Tak |

> [!IMPORTANT]
> Jeśli usługa Azure Cache for Redis wystąpienie jest skonfigurowany z siecią wirtualną, wystąpienie nie można przenieść do innej subskrypcji. Zobacz [Ograniczenia przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Tak | Tak |
> | Profile | Tak | Tak |
> | profile / punkty końcowe | Tak | Tak |

## <a name="microsoftcertificateregistration"></a>Rejestracja certyfikatów firmy Microsoft.Certificate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certificateorders | Tak | Tak |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia usługi app service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | nazwy domen | Tak | Nie |
> | maszyny wirtualne | Tak | Nie |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia wdrażania klasycznego](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami za pomocą operacji specyficznej dla tego scenariusza.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nie | Nie |
> | zastrzeżone | Nie | Nie |
> | sieci wirtualnych | Nie | Nie |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia wdrażania klasycznego](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami za pomocą operacji specyficznej dla tego scenariusza.

## <a name="microsoftclassicstorage"></a>Program Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konta magazynowe | Tak | Nie |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia wdrażania klasycznego](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami za pomocą operacji specyficznej dla tego scenariusza.

## <a name="microsoftcognition"></a>Microsoft.Cognition

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konta syntetyków | Nie | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Tak | Tak |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zestawy dostępności | Tak | Tak |
> | zestawy dysków | Nie | Nie |
> | Dysków | Tak | Tak |
> | Galerie | Nie | Nie |
> | galerie / zdjęcia | Nie | Nie |
> | galerie / obrazy / wersje | Nie | Nie |
> | grupy hostów | Nie | Nie |
> | grupy hostów / hosty | Nie | Nie |
> | images | Tak | Tak |
> | grupy bliskozami | Tak | Tak |
> | przystawki punktów przywracania | Nie | Nie |
> | sharedvmextensions | Nie | Nie |
> | sharedvmimages | Nie | Nie |
> | sharedvmimages / wersje | Nie | Nie |
> | Migawki | Tak | Tak |
> | sshpublickeys | Nie | Nie |
> | maszyny wirtualne | Tak | Tak |
> | virtualmachines / rozszerzenia | Tak | Tak |
> | zestawy virtualmachinescalesets | Tak | Tak |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia maszyn wirtualnych](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Zużycie przez firmę Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zagregowanykoszt | Nie | Nie |
> | Salda | Nie | Nie |
> | Budżetów | Nie | Nie |
> | Opłaty | Nie | Nie |
> | znaczniki kosztów | Nie | Nie |
> | Kredyty | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | Prognozy | Nie | Nie |
> | Wiele | Nie | Nie |
> | Rynkach | Nie | Nie |
> | wyniki operacyjne | Nie | Nie |
> | program operacyjny | Nie | Nie |
> | arkusze cen | Nie | Nie |
> | Produktów | Nie | Nie |
> | szczegóły rezerwacji | Nie | Nie |
> | rezerwacje | Nie | Nie |
> | rezerwacje | Nie | Nie |
> | przetransakcje rezerwacji | Nie | Nie |
> | tags | Nie | Nie |
> | Najemców | Nie | Nie |
> | Warunki | Nie | Nie |
> | szczegóły użycia | Nie | Nie |

## <a name="microsoftcontainer"></a>Kontener firmy Microsoft.Container

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | grupy kontenerowe | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Insstancja kontenera firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | grupy kontenerowe | Nie | Nie |
> | linki do obsługi | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Rejestrów | Tak | Tak |
> | rejestry / biura agentów | Nie | Nie |
> | rejestry / buduj zadania | Tak | Tak |
> | rejestry / replikacje | Tak | Tak |
> | rejestry / zadania | Tak | Tak |
> | rejestry / zadania | Tak | Tak |
> | rejestry / hook | Tak | Tak |

## <a name="microsoftcontainerservice"></a>Usługa kontenera firmy Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługi kontenerowe | Nie | Nie |
> | zarządzane clusters | Nie | Nie |
> | otwarte łącze gołągowe | Nie | Nie |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Nie | Nie |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalityka

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftcostmanagement"></a>Zarządzanie kosztami firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | alerts | Nie | Nie |
> | Budżetów | Nie | Nie |
> | konta integracji | Tak | Tak |
> | Wymiary | Nie | Nie |
> | Wywozu | Nie | Nie |
> | zewnętrzne subskrypcje | Nie | Nie |
> | forecast | Nie | Nie |
> | query | Nie | Nie |
> | reportconfigs | Nie | Nie |
> | reports | Nie | Nie |
> | zasady showback | Nie | Nie |
> | widoki | Nie | Nie |

## <a name="microsoftcustomerinsights"></a>Informacje o klientach firmy Microsoft.Customer

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | koncentratory, | Nie | Nie |

## <a name="microsoftcustomproviders"></a>Usługi niestandardowe firmy Microsoft.Custom

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Stowarzyszenia | Nie | Nie |
> | dostawcy zasobów | Tak | Tak |

## <a name="microsoftdatabox"></a>Skrzynka z danymi firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Zadania | Nie | Nie |

## <a name="microsoftdataboxedge"></a>Plik Microsoft.DataBoxEdge

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
> | Katalogi | Tak | Tak |
> | datacatalogs (przetwarzanie danych) | Nie | Nie |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | menedżerów połączeń | Nie | Nie |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Pakiety | Nie | Nie |
> | Plany | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | datafactories (fabryki danych) | Tak | Tak |
> | Fabryk | Tak | Tak |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konta datalakeaccounts | Nie | Nie |

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

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Nie | Nie |
> | usługi / projekty | Nie | Nie |
> | Slotów | Nie | Nie |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nie | Nie |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Tak | Tak |

## <a name="microsoftdbformariadb"></a>Baza danych Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Serwerów | Tak | Tak |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Serwerów | Tak | Tak |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Servergroups | Nie | Nie |
> | Serwerów | Tak | Tak |
> | serweryv2 | Tak | Tak |
> | singleservers | Tak | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zasoby artefaktów | Tak | Tak |
> | Wdrożenia | Tak | Tak |
> | topologie usług | Tak | Tak |
> | topologie serwisowe / usługi | Tak | Tak |
> | servicetopologies / usługi / jednostki serwisowe | Tak | Tak |
> | kroki | Tak | Tak |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopWirtualizacja

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | grupy aplikacji | Nie | Nie |
> | hostpoole | Nie | Nie |
> | obszary robocze | Nie | Nie |

## <a name="microsoftdevices"></a>Urządzenia firmy Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | baseny elastyczne | Nie | Nie |
> | baseny elastyczne / iothubtenants | Nie | Nie |
> | iothubs ( iothubs ) | Tak | Tak |
> | usługi inicjowania obsługi administracyjnej | Tak | Tak |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Rurociągów | Tak | Tak |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | kontrolery | Tak | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | centra laboratoryjne | Nie | Nie |
> | Labs | Tak | Nie |
> | laboratoria / środowiska | Tak | Tak |
> | laboratoria / serwisanty | Tak | Tak |
> | laboratoria / maszyny wirtualne | Tak | Nie |
> | Harmonogramy | Tak | Tak |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nie | Nie |

## <a name="microsoftdocumentdb"></a>Baza danych microsoft.documentdb

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konta baz danych | Tak | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Domen | Tak | Tak |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Domen | Tak | Tak |
> | eventSubscriptions | Nie - nie można przenosić niezależnie, ale automatycznie przenosić za pomocą subskrybowanego zasobu. | Nie - nie można przenosić niezależnie, ale automatycznie przenosić za pomocą subskrybowanego zasobu. |
> | eventubscriptions | Nie - nie można przenosić niezależnie, ale automatycznie przenosić za pomocą subskrybowanego zasobu. | Nie - nie można przenosić niezależnie, ale automatycznie przenosić za pomocą subskrybowanego zasobu. |
> | extensiontopics (przedłużacze) | Nie | Nie |
> | przestrzenie partnerów | Tak | Tak |
> | rejestracje partnerów | Nie | Nie |
> | partnertopics | Tak | Tak |
> | systemtopics | Tak | Tak |
> | Tematy | Tak | Tak |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Klastrów | Tak | Tak |
> | Obszary nazw | Tak | Tak |

## <a name="microsoftexperimentation"></a>Eksperymenty firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzenie ekspperacyjnie | Nie | Nie |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Obszary nazw | Tak | Tak |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftguestconfiguration"></a>Konfiguracja witryny Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konta automanagedackonu | Nie | Nie |
> | automanagedvmkonfiguracje profile | Nie | Nie |
> | znaki konfiguracyjne gości | Nie | Nie |
> | Oprogramowania | Nie | Nie |
> | profil softwareupdate | Nie | Nie |
> | aktualizacje oprogramowania | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nie | Nie |
> | sapmonitory | Tak | Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Klastrów | Tak | Tak |

> [!IMPORTANT]
> Klastry USŁUGI HDInsight można przenieść do nowej subskrypcji lub grupy zasobów. Nie można jednak przenosić między subskrypcjami zasobów sieciowych połączonych z klastrem HDInsight (takich jak sieć wirtualna, karta sieciowa lub moduł równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów karty sieciowej dołączonej do maszyny wirtualnej dla klastra.
>
> Podczas przenoszenia klastra HDInsight do nowej subskrypcji należy najpierw przenieść inne zasoby (takie jak konto magazynu). Następnie przesuń klaster HDInsight samodzielnie.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Tak | Tak |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Maszyny | Tak | Tak |
> | maszyny / rozszerzenia | Nie | Nie |

## <a name="microsofthybriddata"></a>Microsoft.HybridDana

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | menedżerów danych | Tak | Tak |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Składniki | Nie | Nie |
> | szesnaskopy sieciowe | Nie | Nie |

## <a name="microsoftimportexport"></a>Microsoft.ImportEksport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Zadania | Tak | Tak |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | grupy działania | Tak | Tak |
> | activitylogalerts | Nie | Nie |
> | zasady alertów | Tak | Tak |
> | automatyczne skalowanie | Tak | Tak |
> | Linii bazowej | Nie | Nie |
> | obliczanie linii bazowej | Nie | Nie |
> | Składniki | Tak | Tak |
> | zasady zbiorów danych | Nie | Nie |
> | diagnostyki | Nie | Nie |
> | diagnostykikategorii | Nie | Nie |
> | typy zdarzeń | Nie | Nie |
> | rozszerzone diagnozy | Nie | Nie |
> | guestdiagnosticsettings | Nie | Nie |
> | logdefinitions | Nie | Nie |
> | dzienniki | Nie | Nie |
> | metryczne certyfikaty | Nie | Nie |
> | metryki linii bazowych | Nie | Nie |
> | metrycznedefinicje | Nie | Nie |
> | metricnamespaces | Nie | Nie |
> | metrics | Nie | Nie |
> | myworkbooks | Nie | Nie |
> | grupy powiadomień | Nie | Nie |
> | privatelinkscopes | Tak | Tak |
> | zasady zaplanowane | Tak | Tak |
> | topology | Nie | Nie |
> | transakcje | Nie | Nie |
> | vminsightsonboardingstatuss vminsightsonboardingstatuss vminsightsonboardingstatuss vmin | Nie | Nie |
> | testy internetowe | Tak | Tak |
> | skoroszyty | Tak | Tak |
> | zeszyty | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [limitów subskrypcji.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Centrum IoT Firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | iotapps ( iotapps ) | Tak | Tak |

## <a name="microsoftiotspaces"></a>Przestrzenie IoT firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dostępność sprawdzana | Tak | Tak |
> | Wykres | Tak | Tak |

## <a name="microsoftkeyvault"></a>Usługa Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | baseny hsmpools | Nie | Nie |
> | Sklepienia | Tak | Tak |

> [!IMPORTANT]
> Magazyny kluczy używane do szyfrowania dysku nie mogą być przenoszone do grupy zasobów w tej samej subskrypcji lub w ramach subskrypcji.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | połączone zgromadą | Nie | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Klastrów | Tak | Tak |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konta labaccounts | Nie | Nie |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftlocationservices"></a>Microsoft.LocationSługi

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftlogic"></a>Logika firmy Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hostingśrodowiska | Nie | Nie |
> | konta integracyjne | Tak | Tak |
> | integracja | Tak | Nie |
> | integracja / zarządzanie | Tak | Nie |
> | izolacja środowiska | Nie | Nie |
> | Przepływy pracy | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Uczenie się maszyn firmy Microsoft.Machine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | plany zobowiązań | Nie | Nie |
> | Webservices | Tak | Nie |
> | obszary robocze | Tak | Tak |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nie | Nie |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |
> | konta / obszary robocze | Nie | Nie |
> | konta / obszary robocze / projekty | Nie | Nie |
> | konta teamaccounts | Nie | Nie |
> | teamaccounts / obszary robocze | Nie | Nie |
> | teamaccounts / obszary robocze / projekty | Nie | Nie |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelModelManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hostingkonta | Nie | Nie |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |
> | obszary robocze / obliczenia | Nie | Nie |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | 10. | Nie | Nie |
> | konfiguracjassignments | Nie | Nie |
> | konfiguracje konserwacji | Tak | Tak |
> | aktualizacje | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Tożsamości | Nie | Nie |
> | identyfikatory przypisane przez użytkownika | Nie | Nie |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | sieci zarządzanych | Nie | Nie |
> | managednetworks / managednetworkgroups | Nie | Nie |
> | zarządzane sieci / zarządzanenetworkpeeringpolicies | Nie | Nie |
> | powiadomienie | Nie | Nie |

## <a name="microsoftmanagedservices"></a>Usługi zarządzane przez firmę Microsoft.Managed

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | rejestracje | Nie | Nie |
> | rejestracjedefinicje | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft.Maps (Mapy firmy Microsoft)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Tak | Tak |
> | konta / privateatlases | Tak | Tak |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługi mediasce | Tak | Tak |
> | mediaservices / liveevents | Tak | Tak |
> | mediaservices / streamingendpoints | Tak | Tak |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | appclusters | Nie | Nie |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | ocenyprojektów | Tak | Tak |
> | migrowanieprojektów | Tak | Tak |
> | transfery | Nie | Nie |
> | Projektów | Nie | Nie |

## <a name="microsoftnetapp"></a>Microsoft.NetApp (Microsoft.NetApp)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konta netto | Nie | Nie |
> | netappaccounts / backuppolicies | Nie | Nie |
> | netappaccounts / pojemnośćpuly | Nie | Nie |
> | netappaccounts / pojemność / woluminy | Nie | Nie |
> | netappaccounts / pojemność / woluminy / mounttargets | Nie | Nie |
> | netappaccounts / pojemność / woluminy / migawki | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nie | Nie |
> | applicationgatewaywebapplicationfirewallpolicies | Nie | Nie |
> | grupy zabezpieczeń aplikacji | Tak | Tak |
> | azurefirewalls | Tak | Tak |
> | bastionhosts | Nie | Nie |
> | Połączenia | Tak | Tak |
> | ddoscustompolicies | Tak | Tak |
> | plany ochrony ddosprotection | Nie | Nie |
> | strefy dns | Tak | Tak |
> | ekspresywy | Nie | Nie |
> | drogi ekspresowe | Nie | Nie |
> | firewallpolicies | Tak | Tak |
> | przednie drzwi | Nie | Nie |
> | frontdoorwebapplicationfirewallpolicies | Nie | Nie |
> | grupy ip | Tak | Tak |
> | równoważenia ładunków | Tak — podstawowa jednostka SKU<br>Nie - Standardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Nie - Standardowa jednostka SKU |
> | localnetworkgates | Tak | Tak |
> | natgateways | Tak | Tak |
> | profile w sieciach | Tak | Tak |
> | polityka sieciowa | Tak | Tak |
> | wsiew sieci | Tak | Tak |
> | profile sieciowe | Nie | Nie |
> | networksecuritygroups | Tak | Tak |
> | networkwatchers | Tak | Nie |
> | networkwatchers / connectionmonitors | Tak | Nie |
> | networkwatchers / flowlogs | Tak | Nie |
> | networkwatchers / pingmeshes | Tak | Nie |
> | p2svpngates | Nie | Nie |
> | strefa prywatna | Tak | Tak |
> | privatednszones / virtualnetworklinks | Tak | Tak |
> | privateendpointredirectmaps | Nie | Nie |
> | punkty prywatne | Tak | Tak |
> | usługi privatelink | Nie | Nie |
> | reklamowe sukienki | Tak — podstawowa jednostka SKU<br>Nie - Standardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Nie - Standardowa jednostka SKU |
> | poprawki do publicznej wiadomości | Tak | Tak |
> | filtry tras | Nie | Nie |
> | routetables (stoły trasowe) | Tak | Tak |
> | serviceendpointpolicies | Tak | Tak |
> | profile trafficmanager | Tak | Tak |
> | wirtualnehuby | Nie | Nie |
> | virtualnetworkgates | Tak | Tak |
> | sieci wirtualnych | Tak | Tak |
> | virtualnetworktaps | Nie | Nie |
> | wirtualiści | Tak | Tak |
> | wirtualnewans | Nie | Nie |
> | vpngateways (Wirtualna sieć WAN) | Nie | Nie |
> | konfiguracje vpnserver | Nie | Nie |
> | vpnsites (Virtual WAN) | Nie | Nie |
> | webapplicationfirepolicies | Tak | Tak |

> [!IMPORTANT]
> Zobacz [Wskazówki dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Witryna Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Obszary nazw | Tak | Tak |
> | przestrzenie nazw / notificationhubs | Tak | Tak |

## <a name="microsoftobjectstore"></a>Sklep Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Tak | Tak |

## <a name="microsoftoperationalinsights"></a>Dane operacyjne firmy Microsoft.Operational

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Klastrów | Nie | Nie |
> | magazynowaniewskości | Nie | Nie |
> | obszary robocze | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [limitów subskrypcji.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zarządzaniami | Nie | Nie |
> | konfiguracje zarządzania | Tak | Tak |
> | rozwiązania | Tak | Tak |
> | widoki | Tak | Tak |

## <a name="microsoftpeering"></a>Komunikacja równorzędna firmy Microsoft

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komunikacja równorzędna | Tak | Tak |
> | usługi komunikacji równorzędnej | Nie | Nie |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | polityczne | Nie | Nie |
> | państwa polityki | Nie | Nie |
> | zasoby polityczne | Nie | Nie |
> | środki zaradcze | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | pulpity nawigacyjne | Tak | Tak |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | źródła korzeni | Nie | Nie |

## <a name="microsoftpowerbi"></a>Program Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | workspacecollections (workspacecollections) | Tak | Tak |

## <a name="microsoftpowerbidedicated"></a>Program Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Pojemności | Tak | Tak |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Nie | Nie |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Wdrożenia | Nie | Nie |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |

## <a name="microsoftrecoveryservices"></a>Usługi Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nie | Nie |
> | wyniki replikacji | Nie | Nie |
> | Sklepienia | Tak | Tak |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia usług odzyskiwania](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Zmiana redhatopensau w systemie Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | otwarte łączki przesuwowe | Nie | Nie |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Obszary nazw | Tak | Tak |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Kwerendy | Tak | Tak |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dostępnośćstatusy | Nie | Nie |
> | statusy dostępności dzieci | Nie | Nie |
> | źródła podrzędne | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | powiadomienia | Nie | Nie |

## <a name="microsoftresources"></a>Zasoby firmy Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | skrypty wdrażania | Nie | Nie |
> | Linki | Nie | Nie |
> | tags | Nie | Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Tak | Nie |

## <a name="microsoftsearch"></a>Wyszukiwanie microsoft.search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługi wyszukiwania | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść kilka zasobów wyszukiwania w różnych regionach w jednej operacji. Zamiast tego przenieś je w oddzielnych operacjach.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nie | Nie |
> | zaawansowane ustawienia ochrony przed zagnieżdżeniami | Nie | Nie |
> | metadane oceny | Nie | Nie |
> | oceny | Nie | Nie |
> | Automatyki | Tak | Tak |
> | wyniki zgodności | Nie | Nie |
> | zgodności | Nie | Nie |
> | datacollectionagents | Nie | Nie |
> | devicesecuritygroups | Nie | Nie |
> | polityka ochrony informacji | Nie | Nie |
> | rozwiązania iotsecuritysolutions | Tak | Tak |
> | oceny serwerów | Nie | Nie |

## <a name="microsoftsecurityinsights"></a>Witryny zabezpieczeń firmy Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Agregacji | Nie | Nie |
> | zasady alertów | Nie | Nie |
> | tablice alarmowe | Nie | Nie |
> | zakładki | Nie | Nie |
> | Przypadkach | Nie | Nie |
> | połączenia danych | Nie | Nie |
> | wymagania dotyczące kontroli połączeń danych | Nie | Nie |
> | Podmioty | Nie | Nie |
> | entityqueries (100- 1 | Nie | Nie |
> | Incydentów | Nie | Nie |
> | przyzdjęcie biurowe | Nie | Nie |
> | settings | Nie | Nie |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Bramy | Nie | Nie |
> | Węzłów | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Obszary nazw | Tak | Tak |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Nie | Nie |
> | Klastrów | Tak | Tak |
> | klastry / aplikacje | Nie | Nie |
> | grupy kontenerowe | Nie | Nie |
> | zestawy grup kontenerowych | Nie | Nie |
> | edgeclusters | Nie | Nie |
> | zarządzane clusters | Nie | Nie |
> | Sieci | Nie | Nie |
> | sklepy sekretowe | Nie | Nie |
> | volumes | Nie | Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Tak | Tak |
> | grupy kontenerowe | Nie | Nie |
> | Bramy | Tak | Tak |
> | Sieci | Tak | Tak |
> | wpisy tajne | Tak | Tak |
> | volumes | Tak | Tak |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Wdrożenia | Nie | Nie |

## <a name="microsoftsignalrservice"></a>Usługa Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | signalr | Tak | Tak |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hybrydydojudy | Nie | Nie |

## <a name="microsoftsolutions"></a>Rozwiązania firmy Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions (zdefiniowanie aplikacji) | Nie | Nie |
> | aplikacje | Nie | Nie |
> | jitrequests | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | baseny wystąpień | Nie | Nie |
> | zarządzane zachowania | Nie | Nie |
> | zarządzanie / bazy danych | Nie | Nie |
> | Serwerów | Tak | Tak |
> | serwery / bazy danych | Tak | Tak |
> | serwery / baseny elastyczne | Tak | Tak |
> | serwery / konta pracy | Tak | Tak |
> | serwery / jobagents | Tak | Tak |
> | virtualclusters | Tak | Tak |

> [!IMPORTANT]
> Baza danych i serwer muszą znajdować się w tej samej grupie zasobów. Podczas przenoszenia serwera SQL, wszystkie jego bazy danych są również przenoszone. To zachowanie dotyczy bazy danych usługi Azure SQL Database i usługi Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegrupy | Tak | Tak |
> | sqlvirtualmachines | Tak | Tak |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dwvm ( dwvm ) | Nie | Nie |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | konta magazynowe | Tak | Tak |

## <a name="microsoftstoragecache"></a>Usługa Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Buforuje | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługi magazynowania | Tak | Tak |

## <a name="microsoftstoragesyncdev"></a>System Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługi magazynowania | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Program Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługi magazynowania | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Menedżerów | Nie | Nie |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalityka

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Tak | Tak |

> [!IMPORTANT]
> Zadań usługi Stream Analytics nie można przenosić w stanie uruchomionym.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Środowiskach | Nie | Nie |
> | środowiska / źródła zdarzeń | Nie | Nie |
> | Wystąpień | Nie | Nie |
> | instancje / środowiska | Nie | Nie |
> | instancje / środowiska / źródła zdarzeń | Nie | Nie |

## <a name="microsoftsubscription"></a>Subskrypcja firmy Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Createsubscription | Nie | Nie |

## <a name="microsoftsupport"></a>pomoc techniczna firmy microsoft.support

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | bilety pomocnicze | Nie | Nie |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |
> | obszary robocze / bigdatapools | Nie | Nie |
> | obszary robocze / sqlpools | Nie | Nie |

## <a name="microsoftterraformoss"></a>Witryna Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | rejestracje dostawców | Nie | Nie |
> | zasoby | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Środowiskach | Tak | Tak |
> | środowiska / źródła zdarzeń | Tak | Tak |
> | środowiska / referencedatasets | Tak | Tak |

## <a name="microsofttoken"></a>Token firmy Microsoft.Token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Sklepy | Tak | Tak |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | płyty obrazów | Nie | Nie |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | account | Nie | Nie |
> | konto / rozszerzenie | Tak | Tak |
> | konto / projekt | Tak | Tak |

> [!IMPORTANT]
> Aby zmienić subskrypcję usługi Azure DevOps, zobacz [zmienianie subskrypcji platformy Azure używanej do rozliczeń.](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dedykowane chmury | Nie | Nie |
> | usługi dedykowanecloud | Nie | Nie |
> | maszyny wirtualne | Nie | Nie |

## <a name="microsoftvnfmanager"></a>Menedżer microsoft.Vnf

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | urządzenia | Nie | Nie |
> | vnfs ( vnfs ) | Nie | Nie |

## <a name="microsoftvsonline"></a>Usługa Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | accounts | Tak | Tak |
> | Plany | Tak | Tak |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certyfikaty | Nie | Tak |
> | przyłącza | Tak | Tak |
> | Połączenia | Tak | Tak |
> | customapis | Tak | Tak |
> | hostingśrodowiska | Nie | Nie |
> | kubeenvironments | Tak | Tak |
> | farmy serwerowe | Tak | Tak |
> | lokacje | Tak | Tak |
> | strony / premieraddons | Tak | Tak |
> | witryny / szczeliny | Tak | Tak |
> | staicycje | Nie | Nie |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia usługi app service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | klucze multipleaktywacji | Nie | Nie |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługi urządzeń | Nie | Nie |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Składniki | Nie | Nie |
> | monitorowanieinstacji | Nie | Nie |
> | Monitory | Nie | Nie |
> | ustawienia powiadomień | Nie | Nie |

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm obecnie nie obsługują operacji przenoszenia.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać polecenia przenoszenia zasobów, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).

Aby uzyskać te same dane co plik wartości oddzielonych przecinkami, pobierz [plik move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
