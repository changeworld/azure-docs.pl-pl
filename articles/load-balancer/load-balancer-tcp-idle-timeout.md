---
title: Konfigurowanie limitu czasu bezczynnego modułu równoważenia obciążenia TCP na platformie Azure
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak skonfigurować limit czasu bezczynny usługi Azure Load Balance BalanceR TCP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456832"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurowanie ustawień limitu czasu bezczynnego TCP dla modułu równoważenia obciążenia platformy Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="tcp-idle-timeout"></a>Limit czasu bezczynnego TCP
Moduł równoważenia obciążenia platformy Azure ma ustawienie limitu czasu bezczynności od 4 minut do 30 minut. Domyślnie jest ustawiona na 4 minuty. Jeśli okres braku aktywności jest dłuższy niż wartość limitu czasu, nie ma żadnej gwarancji, że sesja TCP lub HTTP jest utrzymywana między klientem a usługą w chmurze.

Po zamknięciu połączenia aplikacja kliencka może otrzymać następujący komunikat o błędzie: "Połączenie podstawowe zostało zamknięte: Połączenie, które miało zostać zachowane przy życiu, zostało zamknięte przez serwer".

Powszechną praktyką jest użycie TCP keep-alive. Praktyka ta utrzymuje połączenie aktywne przez dłuższy czas. Aby uzyskać więcej informacji, zobacz te [przykłady .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Z włączoną funkcją keep-alive pakiety są wysyłane w okresach braku aktywności w połączeniu. Pakiety keep-alive upewnij się, że wartość limitu czasu bezczynność nie jest osiągnięty i połączenie jest utrzymywane przez długi okres.

Ustawienie działa tylko dla połączeń przychodzących. Aby uniknąć utraty połączenia, skonfiguruj tcp keep-alive z interwałem mniejszym niż ustawienie limitu czasu bezczynnego lub zwiększyć wartość limitu czasu bezczynnego. Aby obsługiwać te scenariusze, dodano obsługę konfigurowalny limit czasu bezczynności.

TCP keep-alive działa w scenariuszach, w których żywotność baterii nie jest ograniczeniem. Nie jest zalecany w przypadku aplikacji mobilnych. Korzystanie z TCP keep-alive w aplikacji mobilnej może szybciej rozładować baterię urządzenia.

![Limit czasu TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

W poniższych sekcjach opisano sposób zmiany ustawień limitu czasu bezczynnego dla publicznych zasobów ip i modułu równoważenia obciążenia.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurowanie limitu czasu TCP dla publicznego adresu IP na poziomie wystąpienia do 15 minut

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

Parametr `IdleTimeoutInMinutes` jest opcjonalny. Jeśli nie jest ustawiona, domyślny limit czasu wynosi 4 minuty. Dopuszczalny zakres limitu czasu wynosi od 4 do 30 minut.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Ustawianie limitu czasu TCP dla reguły równoważenia obciążenia na 15 minut

Aby ustawić limit czasu bezczynnego dla modułu równoważenia obciążenia, "IdleTimeoutInMinutes" jest ustawiona na regule równoważenia obciążenia. Przykład:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Następne kroki

[Omówienie wewnętrznego modułu równoważenia obciążenia](load-balancer-internal-overview.md)

[Rozpoczęcie konfigurowania modułu równoważenia obciążenia skierowanego do Internetu](quickstart-create-standard-load-balancer-powershell.md)

[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)
