---
title: Diagnozuj lokalnych połączeń za pomocą usługi VPN gateway za pomocą usługi Azure Network Watcher | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób diagnozowanie połączeń lokalnych za pośrednictwem bramy sieci VPN przy użyciu zasobów usługi Azure Network Watcher Rozwiązywanie problemów z.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 323e5d63b5f8566d570dfd47323fcf12f7c6b28b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051584"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnozowanie połączeń lokalnych za pośrednictwem bram sieci VPN

Usługa Azure VPN Gateway umożliwia tworzenie rozwiązań hybrydowych, które można spełnić potrzeby bezpiecznego połączenia między siecią lokalną a siecią wirtualną platformy Azure. Wymagania są unikatowe, dlatego jest wybór lokalnego urządzenia sieci VPN. Platforma Azure jest obecnie obsługuje [kilka urządzeń sieci VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) , stale są prawidłowe we współpracy z dostawcami urządzeń. Przejrzyj ustawienia konfiguracji specyficzne dla urządzenia, przed skonfigurowaniem urządzenia sieci VPN w środowisku lokalnym. Podobnie, bramy Azure VPN Gateway jest skonfigurowany przy użyciu zestawu [obsługiwane parametry protokołu IPsec](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) służące do nawiązywania połączenia. Obecnie nie ma żadnych sposób na określenie lub wybranie określonej kombinacji parametrów protokołu IPsec z obszaru bramy sieci VPN Azure. Ustanawianie udane połączenie między lokalną i platformą Azure, ustawienia urządzenia lokalnej sieci VPN musi być zgodnie z parametrów protokołu IPsec, ustalonym przez bramę sieci VPN platformy Azure. Jeśli ustawienia są poprawne, tam w przypadku utraty łączności i do tej pory rozwiązywania tych problemów nie jest proste i zazwyczaj trwało godzin, aby zidentyfikować i rozwiązać problem.

Za pomocą usługi Azure Network Watcher Rozwiązywanie problemów z funkcji, można zdiagnozować problemy z bramy oraz połączeń i w ciągu kilku minut ma wystarczających informacji, aby podjąć świadomą decyzję, aby rozwiązać ten problem.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scenariusz

Aby skonfigurować połączenie lokacja lokacja między platformą Azure i w środowisku lokalnym przy użyciu FortiGate lokalnej bramy sieci VPN. Aby osiągnąć ten scenariusz, wymagałoby następujące ustawienia:

1. Brama sieci wirtualnej — bramy sieci VPN na platformie Azure
1. Brama sieci lokalnej — [lokalnej bramy sieci VPN (FortiGate)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) reprezentacji w chmurze platformy Azure
1. Połączenia lokacja lokacja (trasy na podstawie) - [połączenia między bramą sieci VPN i router lokalny](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Konfigurowanie FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Szczegółowe wskazówki krok po kroku dotyczące konfigurowania konfiguracji lokacja-lokacja można znaleźć, odwiedzając: [Tworzenie sieci wirtualnej z połączeniem typu lokacja-lokacja przy użyciu witryny Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Jedną z czynności konfiguracyjnych krytyczne służy do konfigurowania parametrów komunikacji protokołu IPsec, wszelkie błędnej konfiguracji prowadzi do utraty łączności między siecią lokalną a platformą Azure. Obecnie bram Azure VPN Gateway są skonfigurowane do obsługi następujących parametrów protokołu IPsec dla fazy 1. Należy zauważyć, jak wspomniano wcześniej, że nie można modyfikować tych ustawień.  Jak pokazano w poniższej tabeli, algorytmów szyfrowania obsługiwane przez bramę sieci VPN platformy Azure są AES256, AES128 i 3DES.

### <a name="ike-phase-1-setup"></a>Ustawienia fazy 1 IKE

| **Właściwość** | **PolicyBased** | **Bramy typu RouteBased i standardowa lub sieci VPN o wysokiej wydajności** |
| --- | --- | --- |
| Wersja IKE |IKEv1 |IKEv2 |
| Grupa Diffie’ego-Hellmana |Grupa 2 (1024 bity) |Grupa 2 (1024 bity) |
| Metoda uwierzytelniania |Klucz wstępny |Klucz wstępny |
| Algorytmy szyfrowania |AES256 AES128 3DES |AES256 3DES |
| Algorytm skrótu |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Okres istnienia (czas) skojarzenia zabezpieczeń (Security Association — SA) fazy 1 |28 800 sekund |10 800 sekund |

Jako użytkownik, należy skonfigurować usługi FortiGate, przykładowa konfiguracja znajduje się na [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Nieświadomie skonfigurowano usługi FortiGate używać algorytmu SHA-512 algorytmu wyznaczania wartości skrótu. Ten algorytm nie jest obsługiwany algorytm dla połączeń opartych na zasadach, połączenie sieci VPN działa.

Te problemy są trudne do rozwiązywania i które są często nieintuicyjnych głównych przyczyn. W takim przypadku możesz otworzyć bilet pomocy technicznej, aby uzyskać pomoc w rozwiązaniu problemu. Jednak przy użyciu usługi Azure Network Watcher Rozwiązywanie problemów z interfejsu API, można zidentyfikować te problemy na własną rękę.

## <a name="troubleshooting-using-azure-network-watcher"></a>Rozwiązywanie problemów przy użyciu usługi Azure Network Watcher

Aby zdiagnozować połączenie, nawiązać połączenie z programu Azure PowerShell i zainicjować `Start-AzNetworkWatcherResourceTroubleshooting` polecenia cmdlet. Można znaleźć szczegółowe informacje na temat korzystania z tego polecenia cmdlet w [połączenia — program PowerShell i rozwiązywanie problemów z bramy sieci wirtualnej](network-watcher-troubleshoot-manage-powershell.md). To polecenie cmdlet może potrwać kilka minut, aby zakończyć.

Po zakończeniu działania polecenia cmdlet, możesz przejść do lokalizacji magazynu określone w poleceniu cmdlet, aby uzyskać szczegółowe informacje temat problemu i dzienniki. Usługa Azure Network Watcher tworzy folder zip, który zawiera następujące pliki dziennika:

![1][1]

Otwórz plik o nazwie IKEErrors.txt i wyświetla następujący błąd, wskazujący wystąpił problem z lokalnych błędnej konfiguracji ustawienia protokołu IKE.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Szczegółowe informacje można uzyskać z Scrubbed wfpdiag.txt do dotyczących tego błędu, ponieważ w tym przypadku przewodnim, że wystąpił `ERROR_IPSEC_IKE_POLICY_MATCH` potencjalny połączenie nie działa prawidłowo.

Inny powszechnym jest określanie nieprawidłowe klucze wspólne. Jeśli w poprzednim przykładzie możesz miały różne klucze współużytkowane, IKEErrors.txt wyświetla następujący błąd: `Error: Authentication failed. Check shared key`.

Usługa Azure Network Watcher Rozwiązywanie problemów z funkcją umożliwia diagnozowanie i rozwiązywanie problemów z połączeniami i bramy sieci VPN z łatwością prostego polecenia cmdlet programu PowerShell. Obecnie obsługujemy diagnozowanie następujące warunki i działają na dodanie więcej warunku.

### <a name="gateway"></a>Brama

| Typ błędu | Przyczyna | Log|
|---|---|---|
| NoFault | Po wykryciu braku błędów. |Yes|
| GatewayNotFound | Nie można odnaleźć bramy lub bramy nie jest obsługiwana administracyjnie. |Nie|
| PlannedMaintenance |  Wystąpienie bramy jest w trakcie konserwacji.  |Nie|
| UserDrivenUpdate | Gdy aktualizacja użytkownika jest w toku. Może to być operacji zmiany rozmiaru. | Nie |
| VipUnResponsive | Nie można nawiązać połączenia podstawowego wystąpienia bramy. Dzieje się tak, gdy sonda kondycji nie powiodło się. | Nie |
| PlatformInActive | Występuje problem z platformą. | Nie|
| ServiceNotRunning | Usługa podstawowy nie jest uruchomiona. | Nie|
| NoConnectionsFoundForGateway | Nawiązywanie połączeń nie istnieje w bramie. Jest to tylko ostrzeżenie.| Nie|
| ConnectionsNotConnected | Brak połączenia są połączone. Jest to tylko ostrzeżenie.| Yes|
| GatewayCPUUsageExceeded | Bieżące użycie bramy użycie procesora CPU jest > 95%. | Yes |

### <a name="connection"></a>Połączenie

| Typ błędu | Przyczyna | Log|
|---|---|---|
| NoFault | Po wykryciu braku błędów. |Yes|
| GatewayNotFound | Nie można odnaleźć bramy lub bramy nie jest obsługiwana administracyjnie. |Nie|
| PlannedMaintenance | Wystąpienie bramy jest w trakcie konserwacji.  |Nie|
| UserDrivenUpdate | Gdy aktualizacja użytkownika jest w toku. Może to być operacji zmiany rozmiaru.  | Nie |
| VipUnResponsive | Nie można nawiązać połączenia podstawowego wystąpienia bramy. Zdarza się, gdy sonda kondycji nie powiodło się. | Nie |
| ConnectionEntityNotFound | Brak konfiguracji połączenia. | Nie |
| ConnectionIsMarkedDisconnected | Połączenie jest oznaczone jako "odłączony." |Nie|
| ConnectionNotConfiguredOnGateway | Usłudze podstawowej nie ma skonfigurowanego połączenia. | Yes |
| ConnectionMarkedStandby | Usłudze podstawowej jest oznaczana w stanie wstrzymania.| Yes|
| Authentication | Wstępny niezgodność klucza. | Yes|
| PeerReachability | Bramy równorzędnej jest nieosiągalny. | Yes|
| IkePolicyMismatch | Brama elementów równorzędnych ma zasady IKE, które nie są obsługiwane przez platformę Azure. | Yes|
| WfpParse Error | Wystąpił błąd podczas analizowania dziennika wywołanie. |Yes|

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się sprawdzić połączenie bramy sieci VPN przy użyciu programu PowerShell i usługi Azure Automation, odwiedzając [bram VPN monitorowanie za pomocą usługi Azure Network Watcher rozwiązywania problemów](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
