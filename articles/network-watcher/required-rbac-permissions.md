---
title: Uprawnienia wymagane do używania funkcji obserwatora sieciowego Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, które uprawnienia kontroli dostępu opartej na rolach na platformie Azure są wymagane do pracy z funkcjami obserwatora sieciowego.
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jdial
ms.openlocfilehash: 09f3a1e1d9c6796cb55ae8f0ab18bf8e1b3fa198
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Uprawnienia kontroli dostępu opartej na rolach, wymagane, aby skorzystać z możliwości obserwatora sieciowego

Kontroli dostępu opartej na rolach na platformie Azure (RBAC) można przypisać tylko określone akcje do członków organizacji, które są wymagane do ukończenia obowiązków przypisane. Aby korzystać z możliwości obserwatora sieciowego, konta, zaloguj się do platformy Azure, musi być przypisany do [właściciela](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [współautora](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), lub [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) wbudowane role lub przypisane do [niestandardowej roli zabezpieczeń](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) przypisany akcji wymienionych dla każdej funkcji obserwatora sieciowego w kolejnych sekcjach. Aby dowiedzieć się więcej o możliwościach obserwatora sieciowego, zobacz [co to jest obserwatora sieciowego?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Pobierz obserwatora sieciowego                                          |
| Microsoft.Network/networkWatchers/write                             | Utwórz lub zaktualizuj obserwatora sieciowego                             |
| Microsoft.Network/networkWatchers/delete                            | Usunąć obserwatora sieciowego                                       |

## <a name="nsg-flow-logs"></a>Dzienniki przepływu NSG

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Konfiguruj przepływ dziennika                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Kwerenda o stan dziennika przepływu                                    |

## <a name="connection-troubleshoot"></a>Rozwiązywanie problemów z połączenia

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Wyniki zapytania połączenia Rozwiązywanie problemów z testu                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Uruchom połączenie Rozwiązywanie problemów z testu                             |

## <a name="connection-monitor"></a>Monitor połączenia

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Uruchom monitor połączenia                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Zatrzymaj monitor połączenia                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Zapytanie monitor połączenia                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Pobierz monitor połączenia                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Utwórz monitor połączenia                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Usuń monitor połączenia                                    |

## <a name="packet-capture"></a>Przechwytywanie pakietów

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Zapytać o stan przechwytywania pakietów                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Zatrzymaj przechwytywania pakietów                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Pobierz przechwytywania pakietów                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Utwórz przechwytywania pakietów                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Usuń przechwytywania pakietów                                        |

## <a name="ip-flow-verify"></a>Sprawdź przepływ IP

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Sprawdź przepływ IP                                              |

## <a name="next-hop"></a>Następny przeskok

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Pobierz następnego przeskoku z maszyny Wirtualnej                                     |

## <a name="network-security-group-view"></a>Widok sieciowych grup zabezpieczeń

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Wyświetlanie grup zabezpieczeń                                           |

## <a name="topology"></a>Topologia

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Pobierz topologii                                                   |

## <a name="reachability-report"></a>Uzyskiwanie raportów

| Akcja                                                              | Name (Nazwa)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Raport uzyskiwanie Azure                               |

## <a name="additional-actions"></a>Dodatkowe akcje

Możliwości obserwatora sieciowego wymaga również następujące akcje:

- Microsoft.Storage/Read
- Microsoft.Authorization/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*