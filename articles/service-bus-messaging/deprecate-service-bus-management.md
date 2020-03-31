---
title: Usługi obsługi wiadomości platformy Azure — Menedżer usług do Menedżera zasobów
description: Ten artykuł zawiera mapowanie przestarzałych interfejsów PONUŚ Usłudze Azure & polecenia cmdlet programu PowerShell do interfejsu API REST menedżera zasobów & polecenia cmdlet programu PowerShell.
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589911"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Wycofanie obsługi usługi Azure Service Manager dla usługi Azure Service Bus, Relay i Event Hubs

Resource Manager, nasz stos infrastruktury chmury nowej generacji, w pełni zastępuje "klasyczny" model usługi Azure Service Management (klasyczny model wdrażania). W rezultacie klasyczne interfejsy API REST modelu wdrażania i obsługa usługi Service Bus, Relay i Event Hubs zostaną wycofane 1 listopada 2021 r. To wycofanie zostało po raz pierwszy ogłoszone w [ogłoszeniu Microsoft Tech Community,](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)ale niedawno zdecydowaliśmy się przedłużyć okres deprecation o kolejne dwa lata od momentu pierwotnego ogłoszenia. Aby ułatwić identyfikację, te `management.core.windows.net` interfejsy API mają w ich identyfikatorze URI. Zapoznaj się z poniższą tabelą, aby uzyskać listę przestarzałych interfejsów API i ich wersji interfejsu API usługi Azure Resource Manager, której należy teraz używać.

Aby kontynuować korzystanie z usługi Service Bus, Relay i Event Hubs, przejdź do Menedżera zasobów do 31 października 2021 r. Zachęcamy wszystkich klientów, którzy nadal korzystają ze starych interfejsów API, aby wkrótce przeszli do skorzystania z dodatkowych zalet Menedżera zasobów, które obejmują grupowanie zasobów, tagi, usprawniony proces wdrażania i zarządzania oraz szczegółowy dostęp kontroli dostępu opartej na rolach (RBAC).

Aby uzyskać więcej informacji na temat usługi Azure Resource Manager vs Azure Service Manager, zobacz [blog TechNet](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/).

Aby uzyskać więcej informacji na temat interfejsów API programu Service Manager i Resource Manager dla usługi Azure Service Bus, Relay i Event Hubs, zobacz naszą dokumentację interfejsu API REST:

- [Azure Service Bus](/rest/api/servicebus/)
- [Azure Event Hubs](/rest/api/eventhub/)
- [Azure Relay](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Interfejs API REST programu Rest programu Service Manager — interfejs API REST Menedżera zasobów

| Interfejsy API menedżera usług (przestarzałe) | Menedżer zasobów — interfejs API usługi Service Bus | Menedżer zasobów — interfejs API centrum zdarzeń | Menedżer zasobów — interfejs API przekazywania |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Przestrzenie nazw-GetNamespaceSync** <br/>[Usługa Service Bus Pobierz obszar nazw](/rest/api/servicebus/get-namespace)<br/>[Usługa Event Hub Pobierz obszar nazw](/rest/api/eventhub/get-event-hub)<br/>[Przekaźnik Pobierz obszar nazw](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>Usługa Magistrala usług/Centrum zdarzeń/Przekazywanie GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [klawisze listkey](/rest/api/servicebus/namespaces/listkeys) | [klawisze listkey](/rest/api/eventhub/namespaces/listkeys) | [klawisze listkey](/rest/api/relay/namespaces/listkeys) |
| **Tematy-GetTopicsAsync**<br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Kolejki-GetQueueAsync** <br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **Przekaźniki-GetRelaysAsync**<br/>[Pobierz przekaźniki](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>Usługa Magistrala/Centrum zdarzeń/Przekaźnik GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [rządy w getautoryzacyjne](/rest/api/servicebus/namespaces/getauthorizationrule) |[rządy w getautoryzacyjne](/rest/api/eventhub/namespaces/getauthorizationrule) | [rządy w getautoryzacyjne](/rest/api/relay/namespaces/getauthorizationrule) |
| **Przestrzenie nazw-DeleteNamespaceSync**<br/>[Obszar nazw usługi Service Bus Delete](/rest/api/servicebus/delete-namespace)<br/>[Centra zdarzeń usuwaj obszar nazw](/rest/api/eventhub/delete-event-hub)<br/>[Przekaźniki Usuń obszar nazw](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [Usunąć](/rest/api/servicebus/namespaces/delete) | [Usunąć](/rest/api/eventhub/namespaces/delete) | [Usunąć](/rest/api/relay/namespaces/delete) | 
| **WiadomościSKUPlan-GetPlanAsync**<br/>Usługa Magistrala usług/Centrum zdarzeń/przekazywanie pobierz obszar nazw<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **WiadomościSKUPlan-UpdatePlanAsync**<br/>Usługa Magistrala usług/Centrum zdarzeń/przekazywanie pobierz obszar nazw<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNamespaceAuthorizationRuleAsync**<br/>Usługa Magistrala usług/Centrum zdarzeń/przekazywanie pobierz obszar nazw<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule createorupdate](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule createorupdate](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
Magistrala usług/Centrum zdarzeń/Przekaźnik<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule createorupdate](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule createorupdate](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[Usługa Service Bus Pobierz obszar nazw](/rest/api/servicebus/get-namespace)<br/>[Centra zdarzeń Pobierz obszar nazw](/rest/api/eventhub/get-event-hub)<br/>[Przekaźnik Pobierz obszar nazw](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>Usługa Service Bus/EventHub/Relay Pobierz obszar nazw<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | [listbysku](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>Magistrala usług/EventHub/przekaźnik<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[Lista centrów zdarzeń](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Pobierz centra zdarzeń](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>Magistrala usług/Centrum zdarzeń/Przekaźnik<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesasync**<br/>Magistrala usług/EventHub/przekaźnik<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [zasady autoryzacji listu](/rest/api/servicebus/namespaces/listauthorizationrules) | [zasady autoryzacji listu](/rest/api/eventhub/namespaces/listauthorizationrules) | [zasady autoryzacji listu](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceDostępność-IsNamespaceDostępne**<br/>[Dostępność obszaru nazw magistrali usług](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [dostępność sprawdzana](/rest/api/servicebus/namespaces/checknameavailability) | [dostępność sprawdzana](/rest/api/eventhub/namespaces/checknameavailability) | [dostępność sprawdzana](/rest/api/relay/namespaces/checknameavailability) |
| **Przestrzenie nazw-CreateOrUpdateNamespaceAsync**<br/>Magistrala usług/Centrum zdarzeń/Przekaźnik<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Tematy-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Menedżer usług PowerShell — Menedżer zasobów Programu PowerShell
| Polecenie programu PowerShell programu Service Manager (przestarzałe) | Nowe polecenia Menedżera zasobów | Nowsze polecenie Menedżera zasobów |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Konfiguracja Get-AzureRmServiceBusGeoDRConfiguration](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Konfiguracja Get-AzServiceBusGeoDRConfiguracja](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [Obszar Typu Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Nowa reguła AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [Nowy obszar AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [Usuń-AzureRmRelayAuthorizationRule](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [Usuń-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [Usuń-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>Następne kroki
Zobacz następującą dokumentację: 

- Najnowsza dokumentacja interfejsu API REST
    - [Azure Service Bus](/rest/api/servicebus/)
    - [Azure Event Hubs](/rest/api/eventhub/)
    - [Azure Relay](/rest/api/relay/)
- Najnowsza dokumentacja programu PowerShell
    - [Azure Service Bus](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Azure Event Hubs](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Azure Event Grid](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
