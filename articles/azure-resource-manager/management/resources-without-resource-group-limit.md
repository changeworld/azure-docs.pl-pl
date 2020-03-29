---
title: Zasoby bez limitu 800
description: Wyświetla listę typów zasobów platformy Azure, które mogą mieć więcej niż 800 wystąpień w grupie zasobów.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 735cad0bfa936c41f603e42bdb9be77a1562cc1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937952"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Zasoby nie ograniczone do 800 wystąpień na grupę zasobów

Domyślnie można wdrożyć maksymalnie 800 wystąpień typu zasobu w każdej grupie zasobów. Jednak niektóre typy zasobów są zwolnione z limitu wystąpienia 800. W tym artykule wymieniono typy zasobów platformy Azure, które mogą mieć więcej niż 800 wystąpień w grupie zasobów. Wszystkie inne typy zasobów są ograniczone do 800 wystąpień.

W przypadku niektórych typów zasobów należy skontaktować się z pomocą techniczną, aby usunąć limit wystąpienia 800. Te typy zasobów są wymienione w tym artykule.


## <a name="microsoftautomation"></a>Automatyzacja firmy Microsoft.Automation

* automationKonta

## <a name="microsoftazurestack"></a>Witryna Microsoft.AzureStack

* Rejestracji
* rejestracje/customerSubscriptions
* rejestracje/produkty
* verificationKeys (Klawisze)

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices — domyślnie ograniczone do 800 wystąpień. Limit ten można zwiększyć, kontaktując się ze wsparciem.

## <a name="microsoftcompute"></a>Microsoft.Compute

* Dysków
* images
* Migawki
* wirtualnematyny

## <a name="microsoftcontainerinstance"></a>Insstancja kontenera firmy Microsoft

* kontenerGrupy

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* rejestry/budowanieZadań
* registries/buildTasks/listSourceRepositoryProperties
* rejestry/budowanieZadowienia/kroki
* rejestry/buildTasks/steps/listBuildArguments
* rejestry/eventGridFilters
* rejestry/replikacje
* rejestry/zadania
* rejestry/łącze internetowe

## <a name="microsoftdbformariadb"></a>Baza danych Microsoft.DBforMariaDB

* Serwerów

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* Serwerów

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* Servergroups
* Serwerów
* serweryv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* Klastrów
* Obszary nazw

## <a name="microsoftexperimentation"></a>Eksperymenty firmy Microsoft

* eksperymentWorks

## <a name="microsoftguestconfiguration"></a>Konfiguracja witryny Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* konfiguracjaProfileAssignments
* guestConfigurationAssignments (Konfiguracje gości)
* Oprogramowania
* softwareUpdateProfile
* oprogramowanieUpdy

## <a name="microsoftinsights"></a>Microsoft.Insights

* metryczne certyfikaty

## <a name="microsoftlogic"></a>Logika firmy Microsoft.Logic

* integrationKontacje
* Przepływy pracy

## <a name="microsoftnetapp"></a>Microsoft.NetApp (Microsoft.NetApp)

* netAppKony
* netAppKony/pojemność Pools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets netAppAccounts/capacityPools/volumes/mountTargets netAppAccounts/capacityPools/volumes/mountTargets netApp
* netAppAccounts/capacityPools/volumes/snapshots

## <a name="microsoftnetwork"></a>Microsoft.Network

* aplikacjaGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* plany ochrony ddos
* strefy dns
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* siećIntentPolicies
* siećPowierzchnie
* prywatneDnsZones
* privateDnsZones/A
* prywatneDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* prywatneDnsZones/SRV
* prywatneDnsZones/TXT
* privateDnsZones/wszystkie
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices (usługi prywatnelink)
* publicIPAddresses — domyślnie ograniczone do 800 wystąpień. Limit ten można zwiększyć, kontaktując się ze wsparciem.
* serviceEndpointPolicies
* profile trafficmanager
* virtualNetworkTaps (Wirtualnetapy sieciowe)

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* zasoby główne

## <a name="microsoftpowerbi"></a>Program Microsoft.PowerBI

* workspaceCollections — domyślnie ograniczone do 800 wystąpień. Limit ten można zwiększyć, kontaktując się ze wsparciem.

## <a name="microsoftrelay"></a>Microsoft.Relay

* Obszary nazw

## <a name="microsoftscheduler"></a>Harmonogram firmy Microsoft

* liczba miejsc pracy

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* Obszary nazw

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* aplikacje
* kontenerGrupy
* Bramy
* Sieci
* wpisy tajne
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* konta przechowywania

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementKonta/apis
* lokacje

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełną listę przydziałów i limitów, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](azure-subscription-service-limits.md).
