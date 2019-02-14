---
title: 'Planowanie i projektowanie pod kątem połączeń obejmujących wiele lokalizacji: Azure VPN Gateway| Microsoft Docs'
description: Dowiedz się więcej o planowanie i projektowanie między środowiskami lokalnymi, hybrydowych i połączeń między sieciami do bramy sieci VPN
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/27/2017
ms.author: yushwang
ms.openlocfilehash: 7802061ba09a30ca34ed3804ace846118c5edb9b
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235374"
---
# <a name="planning-and-design-for-vpn-gateway"></a>Planowanie i projektowanie dla usługi VPN Gateway

Planowanie i projektowanie sieci między środowiskami lokalnymi i konfiguracje sieć wirtualna-sieć wirtualna może być proste lub złożone, w zależności od potrzeb sieci. W tym artykule przedstawiono podstawowe zagadnienia dotyczące planowania i projektowania.

## <a name="planning"></a>Planowanie

### <a name="compare"></a>Opcje łączności między środowiskami lokalnymi

Jeśli chcesz nawiązać bezpiecznego połączenia z lokacjami lokalnymi z siecią wirtualną, masz trzy różne sposoby, aby to zrobić: Lokacja lokacja, punkt lokacja i ExpressRoute. Porównaj połączenia różnych między środowiskami lokalnymi, które są dostępne. Wybranej opcji może zależeć od różnych zagadnień, takich jak:

* Jakiego rodzaju przepływności wymaga rozwiązanie?
* Czy chcesz komunikować się za pośrednictwem publicznej sieci Internet z użyciem bezpiecznego połączenia sieci VPN, czy też połączenia prywatnego?
* Czy dysponujesz publicznym adresem IP, którego możesz użyć?
* Czy planujesz użycie urządzenia sieci VPN? Jeśli tak, to czy jest ono zgodne?
* Czy zamierzasz połączyć tylko kilka komputerów, czy też chcesz utworzyć trwałe połączenie dla witryny?
* Jakiego rodzaju brama sieci VPN jest wymagana dla rozwiązania, które chcesz utworzyć?
* Które jednostki SKU bramy należy użyć?

### <a name="planningtable"></a>Tabela planowania

W poniższej tabeli znajdują się informacje pomocne podczas podejmowania decyzji co do najlepszej opcji łączności dla rozwiązania.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>Jednostki SKU bramy

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Przepływ pracy

Poniższa lista przedstawia Typowy przepływ pracy dla łączność z chmurą:

1. Projektowanie i planowanie topologii połączenia oraz listę przestrzeni adresowych dla wszystkich sieci, z którymi chcesz się połączyć.
2. Tworzenie sieci wirtualnej platformy Azure. 
3. Tworzenie bramy sieci VPN dla sieci wirtualnej.
4. Tworzenie i konfigurowanie połączeń z sieciami lokalnymi lub innymi sieciami wirtualnymi (zgodnie z potrzebami).
5. Tworzenie i konfigurowanie połączenia typu punkt-lokacja dla bramy sieci VPN platformy Azure (zgodnie z potrzebami).

## <a name="design"></a>Projekt
### <a name="topologies"></a>Topologie połączeń

Zacznij od przejrzenia diagramy w [VPN Gateway — informacje](vpn-gateway-about-vpngateways.md) artykułu. Ten artykuł zawiera diagramy podstawowych, modele wdrażania dla każdej topologii oraz dostępne narzędzia wdrażania służących do wdrażania danej konfiguracji.

### <a name="designbasics"></a>Podstawowe informacje o projekcie

W poniższych sekcjach omówiono podstawowe informacje dotyczące bramy sieci VPN. 

#### <a name="servicelimits"></a>Limity usługi dotyczące sieci

Przewijaj tabele, aby wyświetlić [sieci usług limity](../azure-subscription-service-limits.md#networking-limits). Podanych limitów może mieć wpływ na projekt.

#### <a name="subnets"></a>Temat podsieci

Podczas tworzenia połączenia należy wziąć pod uwagę zakres podsieci. Nie możesz mieć nakładających się zakresów adresów podsieci. Nakładającą się podsiecią jest co sieć wirtualna lub lokalizacji lokalnej zawiera tą samą przestrzenią adresów, zawierający w innej lokalizacji. Oznacza to, że wymagane usługi inżynierów sieciowych dla sieci lokalnej się wydzielenie przez zakres do użycia dla adresu IP usługi Azure adresów podsieci/miejsca. Należy przestrzeń adresów, która nie jest używany w sieci lokalnej.

Unikanie nakładających się podsieci ważne jest również podczas pracy przy użyciu połączenia sieć wirtualna-sieć wirtualna. Jeśli adres IP istnieje w odniesieniu do wysyłania i docelowej sieci wirtualnych nakładają się w podsieci, połączenia sieć wirtualna-sieć wirtualna zakończyć się niepowodzeniem. Azure nie mogą kierować dane do innej sieci wirtualnej, ponieważ adres docelowy jest częścią wysyłania sieci wirtualnej.

Bramy sieci VPN wymagają określonej podsieci o nazwie podsieć bramy. Aby podsieć bramy działała prawidłowo, musi nosić nazwę GatewaySubnet. Pamiętaj, aby nie Nazwa podsieci bramy innej nazwy, a nie wdrażać maszyny wirtualne lub czymkolwiek podsieci bramy. Zobacz [podsieci bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Temat bram sieci lokalnej

Brama sieci lokalnej zazwyczaj odwołuje się do lokalizacji lokalnej. W klasycznym modelu wdrażania Brama sieci lokalnej jest określane jako lokacja sieci lokalnej. Podczas konfigurowania bramy sieci lokalnej, nadaj jej nazwę, określ publiczny adres IP lokalnego urządzenia sieci VPN i określić prefiksy adresów, które znajdują się w lokalizacji lokalnej. Azure porównaniem z prefiksy adresów docelowych dla ruchu sieciowego, konsultacje dotyczące konfiguracji, który ma określony dla bramy sieci lokalnej i w związku z tym kieruje pakiety. Można zmodyfikować prefiksy adresów, zgodnie z potrzebami. Aby uzyskać więcej informacji, zobacz [bramy sieci lokalnej](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>O typach bram

Ważne jest wybranie poprawnego typu bramy dla danej topologii. Jeśli wybierzesz niewłaściwego typu, brama nie będzie działać prawidłowo. Typ bramy określa sposób nawiązywania przez nią połączenia i jest wymaganym ustawieniem konfiguracji w przypadku modelu wdrażania przy użyciu usługi Resource Manager.

Dostępne są następujące typy bram:

* Vpn
* ExpressRoute

#### <a name="connectiontype"></a>O typach połączeń

Każda konfiguracja wymaga określonego typu połączenia. Typy połączeń są:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Typy sieci VPN — informacje

Każda konfiguracja wymaga określonego typu sieci VPN. W przypadku łączenia dwóch konfiguracji, na przykład tworzenia połączenia lokacja-lokacja i połączenia punkt-lokacja w tej samej sieci wirtualnej, należy użyć typu sieci VPN, który spełnia wymagania obu połączeń.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

W poniższych tabelach przedstawiono typ sieci VPN, ponieważ jest on mapowany dla każdej konfiguracji połączenia. Upewnij się, że typ sieci VPN dla bramy odpowiada konfiguracji, który chcesz utworzyć. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Urządzenia sieci VPN dla połączeń lokacja-lokacja

Aby skonfigurować połączenie lokacja-lokacja, niezależnie od modelu wdrażania przy użyciu potrzebne są następujące elementy:

* Urządzenie sieci VPN, który jest zgodny z bramami sieci VPN platformy Azure
* Publicznego adresu IP protokołu IPv4, który nie znajduje się poza NAT

Musisz środowisko konfigurowania urządzenia sieci VPN lub Poproś kogoś, które można skonfigurować urządzenie dla Ciebie.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Należy wziąć pod uwagę routingu wymuszonego tunelowania

W przypadku większości konfiguracji można skonfigurować wymuszone tunelowanie. Wymuszone tunelowanie umożliwia przekierowywanie lub "force" całego ruchu skierowanego do Internetu z powrotem do Twojej lokalizacji lokalnej za pośrednictwem tunelu sieci VPN typu lokacja-lokacja do celów przeprowadzania inspekcji. Jest to wymagane krytycznych dla większości organizacji IT zasad. 

Bez wymuszonego tunelowania ruchu skierowanego do Internetu z maszynami wirtualnymi na platformie Azure zawsze będzie przechodzić od infrastruktury sieci platformy Azure bezpośrednio się z Internetem, bez opcji, aby możliwe było sprawdzać lub kontrolować ruch. Nieautoryzowany dostęp do Internetu potencjalnie może prowadzić do ujawnienia informacji lub inne rodzaje naruszeń zabezpieczeń.

Połączenie tunelowania wymuszonego można skonfigurować w obu modelach wdrażania i przy użyciu różnych narzędzi. Aby uzyskać więcej informacji, zobacz [Konfigurowanie wymuszonego tunelowania](vpn-gateway-forced-tunneling-rm.md).

**Diagram tunelowania wymuszonego**

![Usługa Azure VPN Gateway wymuszonego tunelowania diagramu](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Kolejne kroki

Zobacz [— często zadawane pytania dla bramy sieci VPN](vpn-gateway-vpn-faq.md) i [VPN Gateway — informacje](vpn-gateway-about-vpngateways.md) artykuły, aby uzyskać więcej informacji, które pomagają w projekcie.

Aby uzyskać więcej informacji o ustawieniach daną bramę, zobacz [informacje o ustawieniach bramy VPN](vpn-gateway-about-vpn-gateway-settings.md).
