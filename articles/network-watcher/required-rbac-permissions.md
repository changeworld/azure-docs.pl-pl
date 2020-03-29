---
title: Uprawnienia RBAC wymagane do korzystania z możliwości
titleSuffix: Azure Network Watcher
description: Dowiedz się, które uprawnienia kontroli dostępu oparte na rolach platformy Azure są wymagane do pracy z funkcjami obserwatora sieci.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: damendo
ms.openlocfilehash: f8743f19d6cd262ad140659be55a4fc57e842564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840557"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Uprawnienia kontroli dostępu oparte na rolach wymagane do korzystania z funkcji Obserwatora sieci

Kontrola dostępu oparta na rolach platformy Azure (RBAC) umożliwia przypisanie tylko określonych akcji do członków organizacji, których potrzebują do wypełnienia przypisanych im obowiązków. Aby korzystać z funkcji Obserwatora sieciowego, konto, za pomocą którego logujesz się na platformie Azure, musi być przypisane do wbudowanych ról [Właściciela,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner) [Współautora](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)lub [Współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) lub przypisane do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) która jest przypisana do akcji wymienionych dla każdej funkcji obserwatora sieci w kolejnych sekcjach. Aby dowiedzieć się więcej o możliwościach funkcji Obserwatora sieci, zobacz [Co to jest Obserwator sieci?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Uzyskaj obserwatora sieciowego                                          |
| Microsoft.Network/networkWatchers/write                             | Tworzenie lub aktualizowanie obserwatora sieci                             |
| Microsoft.Network/networkWatchers/delete                            | Usuwanie obserwatora sieciowego                                       |

## <a name="nsg-flow-logs"></a>Dzienniki przepływów sieciowych grup zabezpieczeń

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Konfigurowanie dziennika przepływu                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Stan kwerendy dla dziennika przepływu                                    |

## <a name="connection-troubleshoot"></a>Rozwiązywanie problemów z połączeniami

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/akcja          | Inicjowanie testu rozwiązywania problemów z połączeniem
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Wyniki kwerendy testu rozwiązywania problemów z połączeniem                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Uruchamianie testu rozwiązywania problemów z połączeniem                             |

## <a name="connection-monitor"></a>Monitor połączenia

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Uruchamianie monitora połączenia                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Zatrzymywać monitor połączenia                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Wykonywanie zapytań na monitor połączenia                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Pobierz monitor połączeń                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Tworzenie monitora połączeń                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Usuwanie monitora połączeń                                    |

## <a name="packet-capture"></a>Przechwytywanie pakietów

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Zapytanie o stan przechwytywania pakietów                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Zatrzymywanie przechwytywania pakietów                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Uzyskaj przechwytywanie pakietów                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Tworzenie przechwytywania pakietów                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Usuwanie przechwytywania pakietów                                        |

## <a name="ip-flow-verify"></a>Weryfikowanie przepływu adresów IP

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Weryfikowanie przepływu adresów IP                                              |

## <a name="next-hop"></a>Następny przeskok

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/akcja                    | Pobierz następny przeskok z maszyny Wirtualnej                                     |

## <a name="network-security-group-view"></a>Widok sieciowych grup zabezpieczeń

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Wyświetlanie grup zabezpieczeń                                           |

## <a name="topology"></a>Topologia

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topologia/akcja                   | Uzyskaj topologię                                                   |

## <a name="reachability-report"></a>Raport o osiągalności

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Uzyskaj raport o osiągalności platformy Azure                               |


## <a name="additional-actions"></a>Dodatkowe działania

Funkcje obserwatora sieci wymagają również następujących działań:

| Akcje                                                           | Opis                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Authorization/\*/Odczyt                                     | Służy do pobierania przypisań ról RBAC i definicji zasad          |
| Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt               | Służy do wyliczania wszystkich grup zasobów w ramach subskrypcji    |
| Magazyn/magazyn microsoftKonsowania/odczytu                              | Służy do uzyskania właściwości dla określonego konta magazynu   |
| Magazyn/magazyn microsoftKonserwuje/listServiceSas/Action, </br> Magazyn/magazyn microsoftKonta/listaKonskusja/Akcja, <br> Magazyn/magazyn microsoftKonserwuje/listKeys/Akcja| Służy do pobierania wspólnych podpisów dostępu (SAS) umożliwiających [bezpieczny dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) i zapisu na koncie magazynu |
| Microsoft.Compute/virtualMachines/Odczyt, </br> Microsoft.Compute/virtualMachines/Write| Służy do logowania się do maszyny Wirtualnej, przechwytywania pakietów i przekazywania jej na konto magazynu|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Służy do sprawdzania, czy rozszerzenie Network Watcher jest obecny, i zainstalować w razie potrzeby |
| Microsoft.Compute/virtualMachineScaleSets/Odczyt, </br> Microsoft.Compute/virtualMachineScaleSets/Write| Służy do uzyskiwania dostępu do zestawów skalowania maszyny wirtualnej, przechwytywania pakietów i przekazywania ich na konto magazynu|
| Microsoft.Compute/virtualMachineScaleSets/extensions/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Służy do sprawdzania, czy rozszerzenie Network Watcher jest obecny, i zainstalować w razie potrzeby |
| Microsoft.Insights/alertRules/*                                     | Służy do konfigurowania alertów metryk                                     |
| Pomoc techniczna firmy Microsoft.Support/*                                                 | Służy do tworzenia i aktualizowania biletów pomocy technicznej od Network Watcher |
