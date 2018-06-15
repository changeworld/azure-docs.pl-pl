---
title: Łączność Diagnozuj lokalnej za pośrednictwem bramy sieci VPN z obserwatora sieciowego Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób diagnozowania łączność z lokalnymi za pośrednictwem bramy sieci VPN z rozwiązywaniem problemów zasobów Azure obserwatora sieciowego.
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
ms.openlocfilehash: e51d31035a8b05238ef0f8d13dd6b6c3f9ad02e8
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2017
ms.locfileid: "26374206"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnozowanie łączność z lokalnymi za pośrednictwem bramy sieci VPN

Brama sieci VPN platformy Azure umożliwia tworzenie hybrydowych rozwiązań, które potrzebą bezpiecznego połączenia między siecią lokalną a sieci wirtualnej platformy Azure. Zgodnie z wymaganiami są unikatowe, dlatego jest wybór lokalnego urządzenia sieci VPN. Azure obecnie obsługuje [kilka urządzeń sieci VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) stale zweryfikowaniu współpracują z dostawcami urządzenia. Sprawdź ustawienia konfiguracji specyficznych dla urządzenia przed rozpoczęciem konfigurowania lokalnego urządzenia sieci VPN. Podobnie, Brama sieci VPN platformy Azure jest skonfigurowany z zestawem [obsługiwanymi parametrami IPsec](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) służące do nawiązywania połączenia. Obecnie nie istnieje sposób można określić lub wybrać określona kombinacja parametrów IPsec z bramy sieci VPN platformy Azure. Do utworzenia udane połączenie między lokalną i platformą Azure, ustawienia urządzenia sieci VPN między lokalnymi musi być zgodnie z parametrów protokołu IPsec, określonej przez bramy sieci VPN platformy Azure. Jeśli ustawienia są poprawne, że jest utratę łączności i do tej pory rozwiązywania tych problemów nie prosta i zwykle trwało godziny, aby zidentyfikować i rozwiązać problem.

Z obserwatora sieciowego Azure Rozwiązywanie problemów z funkcji, można zdiagnozować wszelkie problemy z połączeniami i bramy i w ciągu minut ma za mało informacji, aby podjąć świadomej decyzji, aby rozwiązać problem.

## <a name="scenario"></a>Scenariusz

Aby skonfigurować połączenie lokacja lokacja platformy Azure i lokalnymi przy użyciu FortiGate jako brama sieci VPN między lokalnymi. Aby zrealizować ten scenariusz, wymagałyby następujące ustawienia:

1. Brama sieci wirtualnej - bramy sieci VPN w systemie Azure
1. Bramy sieci lokalnej - [lokalnej bramy sieci VPN (FortiGate)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) reprezentacja w chmurze Azure
1. Połączenie lokacja lokacja (na podstawie trasy) - [połączenie między bramą sieci VPN i router lokalny](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Konfigurowanie FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Szczegółowe wskazówki krok po kroku dotyczące konfigurowania konfiguracji lokacja-lokacja można znaleźć, odwiedzając: [tworzenie sieci wirtualnej za pomocą połączenia lokacja-lokacja przy użyciu portalu Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Jeden z kroków konfiguracji krytyczne jest skonfigurowanie parametrów komunikacji protokołu IPsec, wszelkie błędnej konfiguracji prowadzi do utraty połączenia między siecią lokalną a platformą Azure. Obecnie bramy sieci VPN platformy Azure są skonfigurowane do obsługi następujących parametrów IPsec dotyczących fazy 1. Należy zwrócić uwagę, jak wspomniano wcześniej, nie można modyfikować tych ustawień.  Jak widać w poniższej tabeli, algorytmów szyfrowania obsługiwane przez bramę sieci VPN platformy Azure są AES256 AES128 i algorytmu 3DES.

### <a name="ike-phase-1-setup"></a>Instalator faza 1 IKE

| **Właściwość** | **PolicyBased** | **Brama RouteBased i Standard lub VPN wysokiej wydajności** |
| --- | --- | --- |
| Wersja IKE |IKEv1 |IKEv2 |
| Grupa Diffie’ego-Hellmana |Grupa 2 (1024 bity) |Grupa 2 (1024 bity) |
| Metoda uwierzytelniania |Klucz wstępny |Klucz wstępny |
| Algorytmy szyfrowania |AES256 AES128 3DES |AES256 3DES |
| Algorytm skrótu |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Okres istnienia (czas) skojarzenia zabezpieczeń (Security Association — SA) fazy 1 |28 800 sekund |10 800 sekund |

Jako użytkownik, użytkownik będzie wymagana do konfigurowania programu FortiGate, przykładowa konfiguracja można znaleźć w [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Nieświadomie skonfigurowanych z FortiGate do użycia jako algorytmu wyznaczania wartości skrótu SHA-512. Ten algorytm nie jest obsługiwany algorytm dla połączeń opartych na zasadach, pracy połączenia sieci VPN.

Te problemy są trudne do rozwiązywania i które są często nieintuicyjnych głównych przyczyn. W takim przypadku można otworzyć bilet pomocy technicznej, aby uzyskać pomoc w rozwiązaniu problemu. Ale z obserwatora sieciowego Azure Rozwiązywanie problemów z interfejsu API, można zidentyfikować te problemy samodzielnie.

## <a name="troubleshooting-using-azure-network-watcher"></a>Rozwiązywanie problemów przy użyciu usługi Azure obserwatora sieciowego

Aby zdiagnozować połączenie, połącz się programu Azure PowerShell i zainicjować `Start-AzureRmNetworkWatcherResourceTroubleshooting` polecenia cmdlet. Można znaleźć szczegółowe informacje na temat używania tego polecenia cmdlet w [połączenia - środowiska PowerShell i rozwiązywanie problemów z Brama sieci wirtualnej](network-watcher-troubleshoot-manage-powershell.md). To polecenie cmdlet może potrwać kilka minut na zakończenie.

Po zakończeniu działania polecenia cmdlet, można przejść do lokalizacji magazynu określony w poleceniu cmdlet, aby uzyskać szczegółowe informacje temat problemu i dzienniki. Azure obserwatora sieciowego tworzy folderu zip, który zawiera następujące pliki dziennika:

![1][1]

Otwórz plik o nazwie IKEErrors.txt i wyświetla następujący błąd wskazujący problemu z lokalnymi IKE ustawienia konfiguracji.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Można uzyskać szczegółowe informacje z Scrubbed-wfpdiag.txt o tym błędzie, ponieważ w takim przypadku informacje o że `ERROR_IPSEC_IKE_POLICY_MATCH` prowadzących do połączenia nie działa prawidłowo.

Inny powszechnym błędem konfiguracji jest niepoprawna określanie kluczy współużytkowanych. Jeśli w poprzednim przykładzie podał różnych kluczy współużytkowanych, IKEErrors.txt zawiera następujący błąd: `Error: Authentication failed. Check shared key`.

Rozwiązywanie problemów z Azure obserwatora sieciowego umożliwia diagnozowanie i rozwiązywanie problemów z bramy sieci VPN i połączeń z łatwość prostego polecenia cmdlet programu PowerShell. Obecnie firma Microsoft obsługuje diagnozowania następujące warunki i działają na dodanie więcej warunku.

### <a name="gateway"></a>Brama

| Typ błędu | Przyczyna | Log|
|---|---|---|
| NoFault | Gdy błąd nie została wykryta. |Tak|
| GatewayNotFound | Nie można odnaleźć bramy lub bramy nie jest obsługiwana administracyjnie. |Nie|
| PlannedMaintenance |  Wystąpienie bramy jest w trakcie konserwacji.  |Nie|
| UserDrivenUpdate | Gdy aktualizacja użytkownika jest w toku. Może to być operacji zmiany rozmiaru. | Nie |
| VipUnResponsive | Nie można osiągnąć podstawowego wystąpienia bramy. Dzieje się tak, gdy badania kondycji nie powiodło się. | Nie |
| PlatformInActive | Istnieje problem z platformą. | Nie|
| ServiceNotRunning | Usługa podstawowy nie jest uruchomiona. | Nie|
| NoConnectionsFoundForGateway | Połączenia nie istnieje w bramie. Jest to tylko ostrzeżenie.| Nie|
| ConnectionsNotConnected | Brak połączenia są połączone. Jest to tylko ostrzeżenie.| Tak|
| GatewayCPUUsageExceeded | Bieżące użycie bramy użycia procesora CPU jest > 95%. | Tak |

### <a name="connection"></a>Połączenie

| Typ błędu | Przyczyna | Log|
|---|---|---|
| NoFault | Gdy błąd nie została wykryta. |Tak|
| GatewayNotFound | Nie można odnaleźć bramy lub bramy nie jest obsługiwana administracyjnie. |Nie|
| PlannedMaintenance | Wystąpienie bramy jest w trakcie konserwacji.  |Nie|
| UserDrivenUpdate | Gdy aktualizacja użytkownika jest w toku. Może to być operacji zmiany rozmiaru.  | Nie |
| VipUnResponsive | Nie można osiągnąć podstawowego wystąpienia bramy. Zdarza się, gdy badania kondycji nie powiodło się. | Nie |
| ConnectionEntityNotFound | Brak konfiguracji połączenia. | Nie |
| ConnectionIsMarkedDisconnected | Połączenie zostało oznaczone jako "odłączony". |Nie|
| ConnectionNotConfiguredOnGateway | Podległej usłudze nie ma skonfigurowanego połączenia. | Tak |
| ConnectionMarkedStandy | Podstawowe usługi jest oznaczony jako stan wstrzymania.| Tak|
| Authentication | Niezgodność klucz wstępny. | Tak|
| PeerReachability | Brama elementu równorzędnego nie jest dostępny. | Tak|
| IkePolicyMismatch | Brama równorzędnej ma zasady IKE, które nie są obsługiwane przez platformę Azure. | Tak|
| Błąd WfpParse | Wystąpił błąd podczas analizowania dziennika platformy filtrowania systemu Windows. |Tak|

## <a name="next-steps"></a>Następne kroki

Dowiedz się sprawdzić połączenie bramy sieci VPN z programu PowerShell i automatyzacja Azure po przejściu na stronę [bramy sieci VPN monitora obserwatora sieciowego Azure rozwiązywania problemów](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
