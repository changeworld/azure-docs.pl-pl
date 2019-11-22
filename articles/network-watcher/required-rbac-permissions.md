---
title: Uprawnienia RBAC wymagane do korzystania z możliwości
titleSuffix: Azure Network Watcher
description: Dowiedz się, które uprawnienia kontroli dostępu opartej na rolach platformy Azure są wymagane do pracy z możliwościami Network Watcher.
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
ms.openlocfilehash: 9d56865a558f027a044e990a2da697dc53e7a311
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277715"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Uprawnienia kontroli dostępu opartej na rolach wymagane do korzystania z funkcji Network Watcher

Kontrola dostępu oparta na rolach (RBAC) na platformie Azure umożliwia przypisywanie tylko określonych czynności do członków organizacji, których potrzebują do wykonania przypisanych im obowiązków. Aby można było korzystać z funkcji Network Watcher, konto, które logujesz się do platformy Azure za [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner)pomocą programu, musi być przypisane do wbudowanych ról współautor, [współautor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)lub [sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) lub przypisane do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , do której przypisano akcje wymienione dla każdej Network Watcher możliwości w poniższych sekcjach. Aby dowiedzieć się więcej o możliwościach Network Watcher, zobacz [co to jest Network Watcher?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Pobierz obserwatora sieciowego                                          |
| Microsoft.Network/networkWatchers/write                             | Tworzenie lub aktualizowanie obserwatora sieciowego                             |
| Microsoft.Network/networkWatchers/delete                            | Usuwanie obserwatora sieciowego                                       |

## <a name="nsg-flow-logs"></a>Dzienniki przepływu sieciowej grupy zabezpieczeń

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Konfigurowanie dziennika przepływu                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Stan zapytania dla dziennika przepływu                                    |

## <a name="connection-troubleshoot"></a>Rozwiązywanie problemów z połączeniami

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Zainicjuj test rozwiązywania problemów z połączeniem
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Wyniki zapytania dotyczące test rozwiązywania problemów z połączeniem                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Uruchom test rozwiązywania problemów z połączeniem                             |

## <a name="connection-monitor"></a>Monitor połączeń

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Uruchom Monitor połączeń                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Zatrzymywanie monitora połączeń                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Wysyłanie zapytań do monitora połączeń                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Pobierz monitor połączeń                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Tworzenie monitora połączeń                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Usuwanie monitora połączeń                                    |

## <a name="packet-capture"></a>Przechwytywanie pakietów

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Zbadaj stan przechwycenia pakietu                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Zatrzymywanie przechwytywania pakietu                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Pobierz przechwycenie pakietu                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Tworzenie przechwycenia pakietu                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Usuwanie przechwytywania pakietu                                        |

## <a name="ip-flow-verify"></a>Weryfikowanie przepływu adresów IP

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/ipFlowVerify/akcja               | Weryfikowanie przepływu IP                                              |

## <a name="next-hop"></a>Następny przeskok

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Pobierz następny przeskok z maszyny wirtualnej                                     |

## <a name="network-security-group-view"></a>Widok sieciowych grup zabezpieczeń

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Wyświetlanie grup zabezpieczeń                                           |

## <a name="topology"></a>Topologia

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Uzyskiwanie topologii                                                   |

## <a name="reachability-report"></a>Raport osiągalności

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Pobierz raport o osiągalności platformy Azure                               |


## <a name="additional-actions"></a>Dodatkowe akcje

Funkcje Network Watcher wymagają również następujących działań:

| Akcje:                                                           | Opis                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft. Authorization/\*/Read                                     | Służy do pobierania przypisań ról RBAC i definicji zasad          |
| Microsoft.Resources/subscriptions/resourceGroups/Read               | Służy do wyliczania wszystkich grup zasobów w ramach subskrypcji    |
| Microsoft. Storage/storageAccounts/Read                              | Służy do pobierania właściwości dla określonego konta magazynu   |
| Microsoft. Storage/storageAccounts/listServiceSas/Action, </br> Microsoft. Storage/storageAccounts/listAccountSas/Action, <br> Microsoft. Storage/storageAccounts/listKeys/akcja| Służy do pobierania sygnatur dostępu współdzielonego (SAS) umożliwiających [bezpieczny dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) i zapisywanie na koncie magazynu |
| Microsoft. COMPUTE/virtualMachines/odczyt, </br> Microsoft. COMPUTE/virtualMachines/Write| Służy do logowania się do maszyny wirtualnej, przechwycenia pakietu i przekazania jej do konta magazynu|
| Microsoft. COMPUTE/virtualMachines/Extensions/Read </br> Microsoft. COMPUTE/virtualMachines/Extensions/Write| Służy do sprawdzania, czy Network Watcher rozszerzenie jest obecne, i instalowanie w razie potrzeby |
| Microsoft. COMPUTE/virtualMachineScaleSets/odczyt, </br> Microsoft. COMPUTE/virtualMachineScaleSets/Write| Służy do uzyskiwania dostępu do zestawów skalowania maszyn wirtualnych, do przechwytywania pakietów i przekazywania ich do konta magazynu|
| Microsoft. COMPUTE/virtualMachineScaleSets/rozszerzenia/odczyt, </br> Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write| Służy do sprawdzania, czy Network Watcher rozszerzenie jest obecne, i instalowanie w razie potrzeby |
| Microsoft.Insights/alertRules/*                                     | Służy do konfigurowania alertów metryk                                     |
| Microsoft.Support/*                                                 | Służy do tworzenia i aktualizowania biletów pomocy technicznej z Network Watcher |
