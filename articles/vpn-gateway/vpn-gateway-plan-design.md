---
title: 'Planowanie i projektowanie pod kątem połączeń między lokalizacjami: Brama sieci VPN platformy Azure | Dokumentacja firmy Microsoft'
description: Więcej informacji na temat bramy sieci VPN planowania i projektowania dla między różnymi lokalizacjami, hybrydowej i połączeń do wirtualnymi
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: d5aaab83-4e74-4484-8bf0-cc465811e757
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2017
ms.author: cherylmc
ms.openlocfilehash: 0ebc3ef4a64432e993dd6ed69766bb64544fe433
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884384"
---
# <a name="planning-and-design-for-vpn-gateway"></a>Planowanie i projektowanie dla usługi VPN Gateway

Planowanie i projektowanie sieci między lokalizacjami i konfiguracje sieci wirtualnej do sieci wirtualnej może być proste i złożone, w zależności od potrzeb sieci. W tym artykule przedstawiono podstawowe zagadnienia dotyczące planowania i projektowania.

## <a name="planning"></a>Planowanie

### <a name="compare"></a>Opcje łączności między lokalizacjami

Jeśli chcesz bezpieczne łączenie z lokacjami lokalnymi do sieci wirtualnej ma trzy różne sposoby, aby to zrobić: lokacja-lokacja, punkt-lokacja i ExpressRoute. Porównaj połączenia różnych między różnymi lokalizacjami, które są dostępne. Wybrana opcja może zależeć od różnych zagadnienia, takie jak:

* Jakiego rodzaju przepływności wymaga rozwiązanie?
* Czy chcesz komunikować się za pośrednictwem publicznej sieci Internet z użyciem bezpiecznego połączenia sieci VPN, czy też połączenia prywatnego?
* Czy dysponujesz publicznym adresem IP, którego możesz użyć?
* Czy planujesz użycie urządzenia sieci VPN? Jeśli tak, to czy jest ono zgodne?
* Czy zamierzasz połączyć tylko kilka komputerów, czy też chcesz utworzyć trwałe połączenie dla witryny?
* Jakiego rodzaju brama sieci VPN jest wymagana dla rozwiązania, które chcesz utworzyć?
* Które jednostka SKU bramy należy używać?

### <a name="planningtable"></a>Tabela planowania

Poniższej tabeli ułatwią podjęcie decyzji najlepszej opcji łączności dla rozwiązania.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>Jednostki SKU bramy

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Przepływ pracy

Poniższa lista przedstawia wspólnego przepływu pracy dla łączności chmury:

1. Projektowanie i planowanie topologii łączności Sporządź listę przestrzeni adresowych dla wszystkich sieci, którą chcesz się połączyć.
2. Tworzenie sieci wirtualnej platformy Azure. 
3. Tworzenie bramy sieci VPN dla sieci wirtualnej.
4. Tworzenie i konfigurowanie połączenia z sieciami lokalnymi lub innych sieci wirtualnych (w razie potrzeby).
5. Utwórz i skonfiguruj połączenie punkt-lokacja dla bramy sieci VPN platformy Azure (w razie potrzeby).

## <a name="design"></a>Projekt
### <a name="topologies"></a>Topologie połączenia

Przyjrzeć diagramów w [o bramy sieci VPN](vpn-gateway-about-vpngateways.md) artykułu. Ten artykuł zawiera podstawowe diagramy, modele wdrażania dla każdej topologii oraz narzędzia dostępne wdrożenia, których można użyć do wdrożenia programu configuration.

### <a name="designbasics"></a>Podstawowe informacje o projekcie

W poniższych sekcjach omówiono podstawowe bramy sieci VPN. 

#### <a name="servicelimits"></a>Limity usług sieci

Przewijanie tabele, aby wyświetlić [sieci usług limity](../azure-subscription-service-limits.md#networking-limits). Limity na liście mogą mieć wpływ na projekt.

#### <a name="subnets"></a>Temat podsieci

Podczas tworzenia połączenia, należy wziąć pod uwagę zakresy podsieci. Nie można umieścić nakładające się zakresy adresów w podsieci. Nakładające się podsieci jest jednej wirtualnej sieci lub lokalizacji lokalnej zawiera tę samą przestrzeń adresową, zawierający w innej lokalizacji. Oznacza to konieczność inżynierom Twojej sieci sieci lokalnej lokalnymi do dotycząca szczególnych zakresu można użyć adresu IP sieci Azure adresowania miejsca/podsieci. Należy przestrzeń adresów, która nie jest używany w sieci lokalnej lokalnymi.

Unikanie nakładające się podsieci ważne jest również podczas pracy z połączeniami sieci wirtualnej do sieci wirtualnej. Jeśli nakłada się na podsieci i adres IP istnieje zarówno na wysyłanie i docelowej sieci wirtualnych, połączeń do wirtualnymi zakończyć się niepowodzeniem. Azure nie można przekierować danych do innych sieci wirtualnej, ponieważ adres docelowy jest częścią wysyłania sieci wirtualnej.

Bramy sieci VPN wymaga określonej podsieci o nazwie podsieci bramy. Aby podsieć bramy działała prawidłowo, musi nosić nazwę GatewaySubnet. Pamiętaj, aby nie podsieci bramy nazwę innej nazwy i nie zostanie wdrożona maszyn wirtualnych lub innych elementów do podsieci bramy. Zobacz [podsieci bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Temat bram sieci lokalnej

Brama sieci lokalnej zazwyczaj odwołuje się do lokalizacji lokalnej. W klasycznym modelu wdrażania Brama sieci lokalnej jest określana jako lokację sieci lokalnej. Podczas konfigurowania bramy sieci lokalnej, należy nadać mu nazwę, określ publiczny adres IP lokalnego urządzenia sieci VPN i określić prefiksy adresów, które znajdują się w lokalizacji lokalnej. Azure analizuje prefiksy adresów docelowy dla ruchu sieciowego, sprawdza konfigurację, którą określono dla bramy sieci lokalnej i odpowiednio kieruje pakiety. W razie potrzeby można zmodyfikować prefiksy adresów. Aby uzyskać więcej informacji, zobacz [bramy sieci lokalnej](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>Typy bramy — informacje

Bardzo ważne jest wybranie typu bramy poprawne dla danej topologii. W przypadku wybrania niewłaściwego typu bramy nie będzie działać prawidłowo. Typ bramy określa sposób nawiązywania przez nią połączenia i jest wymaganym ustawieniem konfiguracji w przypadku modelu wdrażania przy użyciu usługi Resource Manager.

Dostępne są następujące typy bramy:

* Vpn
* ExpressRoute

#### <a name="connectiontype"></a>Typy połączenia — informacje

Każda konfiguracja wymaga określonego typu połączenia. Typy połączeń są:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Typy sieci VPN — informacje

Każdej konfiguracji wymaga określonego typu sieci VPN. W przypadku łączenia dwóch konfiguracji, na przykład tworzenia połączenia lokacja-lokacja i połączenia punkt-lokacja w tej samej sieci wirtualnej, należy użyć typu sieci VPN, który spełnia wymagania obu połączeń.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

W poniższych tabelach przedstawiono typ sieci VPN, ponieważ mapuje on konfigurację każdego połączenia. Upewnij się, że konfigurację, którą chcesz utworzyć zgodny typ sieci VPN dla bramy. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Urządzenia sieci VPN w przypadku połączeń lokacja-lokacja

Aby skonfigurować połączenie lokacja-lokacja, niezależnie od tego modelu wdrażania są potrzebne następujące elementy:

* Urządzenia sieci VPN, który jest zgodny z bram sieci VPN platformy Azure
* Adres IPv4 publicznych, który nie znajduje się za translatorem adresów Sieciowych

Musisz mieć doświadczenie w konfigurowaniu urządzenie sieci VPN lub innej osobie, które można skonfigurować urządzenie dla Ciebie.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Należy wziąć pod uwagę routingu tunelowania wymuszonego

W przypadku większości konfiguracji można skonfigurować wymuszanie tunelowania. Wymuszanie tunelowania umożliwia przekierowanie lub "force" cały ruch do Internetu z powrotem do lokalizacji lokalnej za pośrednictwem tunelu VPN typu lokacja-lokacja dla inspekcji i inspekcji. Jest to wymagane krytycznych dla większości organizacji IT zasad. 

Bez tunelowania wymuszonego, ruch do Internetu z maszyn wirtualnych na platformie Azure będzie zawsze przechodzenie od infrastruktury sieci platformy Azure bezpośrednio się z Internetem, bez opcji pozwala sprawdzać lub kontrolować ruch. Nieautoryzowany dostęp do Internetu potencjalnie może spowodować ujawnienie informacji lub inne rodzaje naruszeń zabezpieczeń.

W obu modeli wdrażania i za pomocą różnych narzędzi można skonfigurować połączenie tunelowania wymuszonego. Aby uzyskać więcej informacji, zobacz [Konfiguracja wymuszonego tunelowania](vpn-gateway-forced-tunneling-rm.md).

**Diagram tunelowania wymuszonego**

![Diagram tunelowania wymuszonego Brama sieci VPN](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Następne kroki

Zobacz [bramy sieci VPN — często zadawane pytania](vpn-gateway-vpn-faq.md) i [o bramy sieci VPN](vpn-gateway-about-vpngateways.md) artykuły, aby uzyskać więcej informacji, które zapewniają pomoc podczas projektowania.

Aby uzyskać więcej informacji o ustawieniach bramy, zobacz [o ustawienia bramy sieci VPN](vpn-gateway-about-vpn-gateway-settings.md).