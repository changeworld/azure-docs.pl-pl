---
title: Uprawnienia wymagane do korzystać z możliwości usługi Azure Network Watcher | Dokumentacja firmy Microsoft
description: Dowiedz się, które uprawnienia kontroli dostępu opartej na rolach na platformie Azure są wymagane do pracy z funkcjami usługi Network Watcher.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: kumud
ms.openlocfilehash: 8c8fe6125d9c638fedadc3d299ff0ac0d601fd61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685696"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Uprawnienia kontroli dostępu opartej na rolach, trzeba używać możliwościach usługi Network Watcher

Kontrola dostępu oparta na rolach na platformie Azure (RBAC) umożliwia przypisanie konkretne akcje do członków organizacji, którzy potrzebują do wykonania ich obowiązków przypisane. Aby korzystać z możliwości usługi Network Watcher, konta, zaloguj się do platformy Azure, muszą być przypisane do [właściciela](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [Współautor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), lub [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) wbudowanych ról, lub przypisane do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) przypisany akcji wymienionych dla każdej funkcji usługi Network Watcher w kolejnych sekcjach. Aby dowiedzieć się więcej o możliwościach usługi Network Watcher, zobacz [co to jest Usługa Network Watcher?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Uzyskać usługi network watcher                                          |
| Microsoft.Network/networkWatchers/write                             | Utwórz lub zaktualizuj usługi network watcher                             |
| Microsoft.Network/networkWatchers/delete                            | Usunąć obserwatora sieciowego                                       |

## <a name="nsg-flow-logs"></a>Dzienniki przepływu sieciowych grup zabezpieczeń

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Konfiguruj przepływ dziennika                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Kwerenda o stan dziennika przepływu                                    |

## <a name="connection-troubleshoot"></a>Rozwiązywanie problemów z połączeniami

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Zainicjuj połączenie Rozwiązywanie problemów z testów
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Wyniki zapytania połączenia Rozwiązywanie problemów z testów                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Uruchom połączenie Rozwiązywanie problemów z testów                             |

## <a name="connection-monitor"></a>Monitor połączeń

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Uruchom monitor połączeń                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Zatrzymywanie monitora połączeń                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Zapytania monitora połączeń                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Uzyskiwanie monitora połączeń                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Tworzenie monitora połączeń                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Usuwanie monitora połączeń                                    |

## <a name="packet-capture"></a>Przechwytywanie pakietów

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Kwerenda o stan przechwytywania pakietów                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Zatrzymać przechwytywania pakietu                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Rozpoczynanie przechwytywania pakietu                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Utwórz przechwytywania pakietów                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Usuwanie przechwycenia pakietu                                        |

## <a name="ip-flow-verify"></a>Weryfikowanie przepływu adresów IP

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Zweryfikuj przepływ IP                                              |

## <a name="next-hop"></a>Następny przeskok

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Pobieranie następnego przeskoku z maszyny Wirtualnej                                     |

## <a name="network-security-group-view"></a>Widok sieciowych grup zabezpieczeń

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Wyświetlanie grup zabezpieczeń                                           |

## <a name="topology"></a>Topologia

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Pobieranie topologii                                                   |

## <a name="reachability-report"></a>Dostępność raportów

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Pobierz raport osiągalności platformy Azure                               |

## <a name="additional-actions"></a>Dodatkowe akcje

Możliwości obserwatora sieci również wymagają następujących czynności:

- Microsoft.Authorization/ \* /Odczyt
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachines/extensions/Read
- Microsoft.Compute/virtualMachines/extensions/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Compute/virtualMachineScaleSets/extensions/Read
- Microsoft.Compute/virtualMachineScaleSets/extensions/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*
