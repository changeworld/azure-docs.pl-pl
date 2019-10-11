---
title: Zasoby platformy Azure bez limitu 800
description: Wyświetla listę typów zasobów platformy Azure, które mogą mieć więcej niż 800 wystąpień w grupie zasobów.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: tomfitz
ms.openlocfilehash: 0f9db618b69ff692350f333b8a4a4354f4980169
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249137"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Zasoby nieograniczone do 800 wystąpień na grupę zasobów

Domyślnie można wdrożyć do 800 wystąpień typu zasobu w każdej grupie zasobów. Niektóre typy zasobów są jednak wykluczone z limitu wystąpienia 800. W tym artykule wymieniono typy zasobów platformy Azure, które mogą mieć więcej niż 800 wystąpień w grupie zasobów. Wszystkie inne typy zasobów są ograniczone do 800 wystąpień.

W przypadku niektórych typów zasobów należy skontaktować się z pomocą techniczną, aby ograniczyć liczbę wystąpień 800. Te typy zasobów zostały wymienione w tym artykule.


## <a name="microsoftautomation"></a>Microsoft. Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

* rejestracje
* rejestracje/customerSubscriptions
* rejestracje/produkty

## <a name="microsoftbotservice"></a>Microsoft. BotService

* botServices — domyślnie ograniczone do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.

## <a name="microsoftcompute"></a>Microsoft.Compute

* dysku
* images
* Migawki
* VirtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

* rejestry/buildTasks
* rejestry/buildTasks/listSourceRepositoryProperties
* rejestry/buildTasks/kroki
* rejestry/buildTasks/etapy/listBuildArguments
* rejestry/eventGridFilters
* rejestry/replikacje
* rejestry/zadania
* rejestry/elementy webhook

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

* serwery

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

* serwery

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

* serverGroups
* serwery
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

* services

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

* guestConfigurationAssignments
* programy
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft. Logic

* integrationAccounts
* przebieg

## <a name="microsoftnetapp"></a>Microsoft. NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/woluminy
* netAppAccounts/capacityPools/woluminy/mountTargets
* netAppAccounts/capacityPools/woluminy/migawki

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
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
* dnszones/wszystkie
* dnszones/zestawy rekordów
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/wszystkie
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* Adresów publicipaddress — domyślnie ograniczone do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

* workspaceCollections — domyślnie ograniczone do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

* jobcollections

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

* aplikacji
* containerGroups
* bram
* sieci
* wpisy tajne
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft. Web

* apiManagementAccounts/interfejsy API
* teren

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełną listę przydziałów i limitów, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-subscription-service-limits.md).
