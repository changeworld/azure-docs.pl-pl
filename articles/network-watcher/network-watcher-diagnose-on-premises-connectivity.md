---
title: Diagnozowanie łączności lokalnej za pośrednictwem bramy sieci VPN
titleSuffix: Azure Network Watcher
description: W tym artykule opisano sposób diagnozowania łączności lokalnej za pośrednictwem bramy sieci VPN za pomocą rozwiązywania problemów z zasobami usługi Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 835b3a69e779b536961110b674ae67f4e8c13ce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845057"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnozowanie łączności lokalnej za pośrednictwem bram sieci VPN

Usługa Azure VPN Gateway umożliwia tworzenie rozwiązania hybrydowego, które zaspokaja potrzebę bezpiecznego połączenia między siecią lokalną a siecią wirtualną platformy Azure. Ponieważ twoje wymagania są unikatowe, podobnie jak wybór lokalnego urządzenia SIECI VPN. Platforma Azure obsługuje obecnie [kilka urządzeń sieci VPN,](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) które są stale sprawdzane we współpracy z dostawcami urządzeń. Przed skonfigurowaniem lokalnego urządzenia sieci VPN należy przejrzeć ustawienia konfiguracji specyficzne dla urządzenia. Podobnie usługa Azure VPN Gateway jest skonfigurowana z zestawem [obsługiwanych parametrów protokołu IPsec,](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) które są używane do ustanawiania połączeń. Obecnie nie ma możliwości określenia lub wybrania określonej kombinacji parametrów protokołu IPsec z bramy sieci VPN platformy Azure. Aby ustanowić pomyślne połączenie między lokalną a platformą Azure, lokalne ustawienia urządzenia sieci VPN muszą być zgodne z parametrami IPsec wymaganymi przez usługę Azure VPN Gateway. Jeśli ustawienia są nieprawidłowe, występuje utrata łączności i do tej pory rozwiązywanie tych problemów nie było trywialne i zwykle trwało wiele godzin, aby zidentyfikować i rozwiązać problem.

Dzięki funkcji rozwiązywania problemów z usługą Azure Network Watcher możesz zdiagnozować wszelkie problemy z bramą i połączeniami, a w ciągu kilku minut masz wystarczająco dużo informacji, aby podjąć świadomą decyzję o rozwiązaniu problemu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scenariusz

Chcesz skonfigurować połączenie lokacja-lokacja między platformą Azure a lokalną przy użyciu fortiGate jako lokalnej bramy sieci VPN. Aby osiągnąć ten scenariusz, należy wymagać następującej konfiguracji:

1. Brama sieci wirtualnej — brama sieci VPN na platformie Azure
1. Brama sieci lokalnej — [reprezentacja bramy sieci VPN w chmurze](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) platformy Azure
1. Połączenie lokacja-lokacja (oparte na trasie) — [połączenie między bramą sieci VPN a routerem lokalnym](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#CreateConnection)
1. [Konfigurowanie FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Szczegółowe wskazówki krok po kroku dotyczące konfigurowania konfiguracji lokacji można znaleźć, odwiedzając stronę: Tworzenie sieci [wirtualnej z połączeniem lokacja lokacja za pomocą portalu Azure.](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Jednym z kluczowych kroków konfiguracji jest skonfigurowanie parametrów komunikacji IPsec, wszelkie błędy konfiguracji prowadzi do utraty łączności między siecią lokalną i platformy Azure. Obecnie bramy sieci VPN platformy Azure są skonfigurowane do obsługi następujących parametrów protokołu IPsec dla fazy 1. Jak widać w poniższej tabeli, algorytmy szyfrowania obsługiwane przez usługę Azure VPN Gateway to AES256, AES128 i 3DES.

### <a name="ike-phase-1-setup"></a>Konfiguracja IKE fazy 1

| **Właściwość** | **PolicyBased** | **Brama sieci VPN oparta na trasach i standardowa lub wysokowydajna** |
| --- | --- | --- |
| Wersja IKE |IKEv1 |IKEv2 |
| Grupa Diffie’ego-Hellmana |Grupa 2 (1024 bity) |Grupa 2 (1024 bity) |
| Metoda uwierzytelniania |Klucz wstępny |Klucz wstępny |
| Algorytmy szyfrowania |AES256 AES128 3DES |AES256 3DES |
| Algorytm skrótu |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Okres istnienia (czas) skojarzenia zabezpieczeń (Security Association — SA) fazy 1 |28 800 sekund |28 800 sekund |

Jako użytkownik, trzeba będzie skonfigurować FortiGate, przykładową konfigurację można znaleźć na [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Nieświadomie skonfigurowano fortiGate do używania SHA-512 jako algorytmu mieszania. Ponieważ ten algorytm nie jest obsługiwanym algorytmem dla połączeń opartych na zasadach, połączenie sieci VPN działa.

Te problemy są trudne do rozwiązania, a przyczyny są często nie intuicyjne. W takim przypadku można otworzyć bilet pomocy technicznej, aby uzyskać pomoc w rozwiązaniu problemu. Jednak za pomocą interfejsu API azure network watcher można samodzielnie zidentyfikować te problemy.

## <a name="troubleshooting-using-azure-network-watcher"></a>Rozwiązywanie problemów przy użyciu usługi Azure Network Watcher

Aby zdiagnozować połączenie, połącz się z `Start-AzNetworkWatcherResourceTroubleshooting` programem Azure PowerShell i zainicjuj polecenie cmdlet. Szczegółowe informacje na temat korzystania z tego polecenia cmdlet można znaleźć w [aplikacji Rozwiązywanie problemów z bramą sieci wirtualnej i połączeniami — PowerShell](network-watcher-troubleshoot-manage-powershell.md). To polecenie cmdlet może potrwać do kilku minut.

Po zakończeniu polecenia cmdlet można przejść do lokalizacji magazynu określonej w podaniu cmdlet, aby uzyskać szczegółowe informacje na temat problemu i dzienników. Usługa Azure Network Watcher tworzy folder zip zawierający następujące pliki dziennika:

![1][1]

Otwórz plik o nazwie IKEErrors.txt i wyświetla następujący błąd, wskazujący problem z lokalną konfiguracją ustawienia IKE.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Szczegółowe informacje na temat błędu można uzyskać ze strony Scrubbed-wfpdiag.txt, ponieważ `ERROR_IPSEC_IKE_POLICY_MATCH` w tym przypadku wspomina się, że nie było to, że połączenie nie działa poprawnie.

Innym częstym błędem konfiguracji jest określanie niepoprawnych kluczy udostępnionych. Jeśli w poprzednim przykładzie określono różne klucze udostępnione, plik IKEErrors.txt wyświetla następujący błąd: `Error: Authentication failed. Check shared key`.

Funkcja rozwiązywania problemów z usługą Azure Network Watcher umożliwia diagnozowanie i rozwiązywanie problemów z bramą sieci VPN i połączeniem za pomocą prostego polecenia cmdlet programu PowerShell. Obecnie wspieramy diagnozowanie następujących warunków i pracujemy nad zwiększeniem stanu.

### <a name="gateway"></a>Brama

| Typ błędu | Przyczyna | Log|
|---|---|---|
| NoFault (brak błędów) | Nie wykryto żadnego błędu. |Tak|
| GatewayNotFound (nie znaleziono bramy) | Nie można odnaleźć bramy lub bramy nie jest aprowizowana. |Nie|
| PlannedMaintenance (planowana konserwacja) |  Wystąpienie bramy jest w trakcie konserwacji.  |Nie|
| UserDrivenUpdate (aktualizacja sterowana przez użytkownika) | Gdy trwa aktualizacja użytkownika. Może to być operacja ponownego rozmiaru. | Nie |
| VipUnResponsive (wirtualny adres IP nie odpowiada) | Nie można dotrzeć do podstawowego wystąpienia bramy. Dzieje się tak, gdy sonda kondycji nie powiedzie się. | Nie |
| PlatformInActive (nieaktywna platforma) | Wystąpił problem z platformą. | Nie|
| UsługaNotRunning | Podstawowa usługa nie jest uruchomiona. | Nie|
| NoConnectionsFoundForGateway | W bramie nie ma żadnych połączeń. To tylko ostrzeżenie.| Nie|
| PołączeniaNiełączone | Żadne z połączeń nie jest podłączone. To tylko ostrzeżenie.| Tak|
| Portal GATEWAYCPUUsageExceeded | Bieżące użycie procesora CPU użycia bramy wynosi > 95%. | Tak |

### <a name="connection"></a>Połączenie

| Typ błędu | Przyczyna | Log|
|---|---|---|
| NoFault (brak błędów) | Nie wykryto żadnego błędu. |Tak|
| GatewayNotFound (nie znaleziono bramy) | Nie można odnaleźć bramy lub bramy nie jest aprowizowana. |Nie|
| PlannedMaintenance (planowana konserwacja) | Wystąpienie bramy jest w trakcie konserwacji.  |Nie|
| UserDrivenUpdate (aktualizacja sterowana przez użytkownika) | Gdy trwa aktualizacja użytkownika. Może to być operacja ponownego rozmiaru.  | Nie |
| VipUnResponsive (wirtualny adres IP nie odpowiada) | Nie można dotrzeć do podstawowego wystąpienia bramy. Dzieje się tak, gdy sonda kondycji nie powiedzie się. | Nie |
| ConnectionEntityNotfound | Brak konfiguracji połączenia. | Nie |
| ConnectionIsMarkedDisconnected | Połączenie jest oznaczone jako "rozłączone". |Nie|
| ConnectionNotConfiguredOnGateway | Podstawowa usługa nie ma skonfigurowane połączenie. | Tak |
| ConnectionMarkedStandby (MarkedStandby) | Podstawowa usługa jest oznaczona jako wstrzymanie.| Tak|
| Uwierzytelnianie | Niezgodność klucza wstępnego. | Tak|
| Równość | Brama równorzędna jest niedoścignięci. | Tak|
| IkePolicyMismatch | Brama równorzędna ma zasady IKE, które nie są obsługiwane przez platformę Azure. | Tak|
| Błąd WfpParse | Wystąpił błąd podczas analizowania dziennika WFP. |Tak|

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak sprawdzić łączność bramy sieci VPN za pomocą programów PowerShell i usługi Azure Automation, odwiedzając [monitor bramy sieci VPN za pomocą rozwiązywania problemów z usługą Azure Network Watcher](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
