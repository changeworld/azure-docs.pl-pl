---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/10/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c3f727c6154a0364f151d22000d2684c361676a
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037214"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Jaka jest różnica między bramą sieci wirtualnej platformy Azure (VPN Gateway) i Azure bramą VPN Gateway wirtualnej sieci WAN platformy Azure?

Wirtualna sieć WAN zapewnia łączność typu lokacja-lokacja w dużej skali oraz oferuje przepływność, skalowalność i łatwość użycia. Urządzenia z oddziału urządzeń lokalnych klienta są automatycznie aprowizowane i łączone z wirtualną siecią WAN platformy Azure. Te urządzenia są dostępne w rozwijającym się ekosystemie partnerów sieci SD-WAN i VPN.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Którzy dostawcy urządzeń (partnerzy wirtualnej sieci WAN) są obsługiwani w czasie wprowadzenia oferty? 

Obecnie firmy Citrix i Riverbed obsługują w pełni zautomatyzowane środowisko wirtualnej sieci WAN. Dodatkowi partnerzy będą dołączać w najbliższych miesiącach, w tym: Nokia Nuage, Palo Alto i Checkpoint. Aby uzyskać więcej informacji, zobacz [Virtual WAN partners (Partnerzy wirtualnej sieci WAN)](https://aka.ms/virtualwan).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Czy muszę korzystać z urządzenia preferowanego przez partnera?

Nie. Możesz używać dowolnego urządzenia obsługującego sieć VPN, które spełnia wymagania wersji zapoznawczej pod kątem obsługi protokołu IKEv2 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partnerzy wirtualnej sieci WAN automatyzują połączenie z wirtualną siecią WAN platformy Azure?

Zdefiniowane programowo rozwiązania w zakresie łączności zwykle umożliwiają zarządzanie urządzeniami oddziału przy użyciu kontrolera lub centrum aprowizacji urządzeń. Kontroler może zautomatyzować połączenie z wirtualną siecią WAN platformy Azure za pomocą interfejsów API platformy Azure. Aby uzyskać więcej informacji o tym, jak to działa, zobacz [Virtual WAN partner automation (Automatyzacja partnerów wirtualnej sieci WAN)](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Czy wirtualna sieć WAN zmienia istniejące funkcje łączności?   

Nie ma żadnych zmian istniejących funkcji łączności platformy Azure.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Czy są dostępne nowe zasoby usługi Resource Manager dla wirtualnej sieci WAN?
  
Tak. Wraz z wirtualną siecią WAN wprowadzono nowe zasoby usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [Omówienie](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Czy są specjalne wymagania dotyczące dołączania do wersji zapoznawczej? 

Aby móc skonfigurować wirtualną sieć WAN platformy Azure, musisz zarejestrować subskrypcję w wersji zapoznawczej. Aby przeprowadzić rejestrację, wyślij wiadomość e-mail na adres <azurevirtualwan@microsoft.com>, podając identyfikator subskrypcji. Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail. Do czasu otrzymania potwierdzenia rejestracji subskrypcji nie można korzystać z wirtualnej sieci WAN w portalu.

Zagadnienia do rozważenia:

* Wersja zapoznawcza jest ograniczona do publicznych regionów platformy Azure.
* Obsługiwanych jest do 100 połączeń na koncentrator wirtualny. Każde połączenie obejmuje 2 tunele w konfiguracji aktywne-aktywne. Tunele kończą się w bramie VPN Gateway koncentratora wirtualnego platformy Azure.
* Rozważ użycie tej wersji zapoznawczej w następujących sytuacjach:
  * Chcesz wdrożyć zagregowaną przepustowość mniejszą niż 1 Gb/s na koncentrator wirtualny.
  * Masz urządzenie sieci VPN, które obsługuje konfigurację opartą na trasach i połączenia IKEv2 IPsec.
  * Chcesz wypróbować technologię SD-WAN i urządzenia oddziału operacyjnego od partnerów we wprowadzaniu produktu na rynek (Riverbed i Citrix).<br>lub<br>Chcesz skonfigurować łączność między poszczególnymi oddziałami oraz między oddziałem a platformą Azure, która obejmuje zarządzanie konfiguracją urządzenia lokalnego. (Konfiguracja samodzielna)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Czy wirtualna sieć WAN platformy Azure obsługuje globalne wirtualne sieci równorzędne? 

 Nie.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Czy sieci wirtualne będące szprychami połączone z koncentratorem wirtualnym mogą komunikować się ze sobą?

Tak. Możesz bezpośrednio nawiązywać komunikację równorzędną sieci wirtualnej między szprychami, które są połączone z koncentratorem wirtualnym. Aby uzyskać więcej informacji, zobacz [Wirtualne sieci równorzędne](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Czy mogę wdrożyć moje ulubione wirtualne urządzenie sieciowe (w sieci wirtualnej wirtualnego urządzenia sieciowego) i używać go za pomocą wirtualnej sieci WAN platformy Azure?

Tak. Możesz połączyć sieć wirtualną ulubionego wirtualnego urządzenia sieciowego z wirtualną siecią WAN platformy Azure, ale wymaga to możliwości routingu w koncentratorze, które będą ogólnie dostępne. Wszystkie szprychy połączone z siecią wirtualną wirtualnego urządzenia sieciowego muszą też być połączone z koncentratorem wirtualnym. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Czy sieć wirtualna wirtualnego urządzenia sieciowego może zawierać bramę sieci wirtualnej?

Nie. Sieć wirtualna wirtualnego urządzenia sieciowego nie może mieć bramy sieci wirtualnej, jeśli jest połączona z koncentratorem wirtualnym. 

### <a name="is-there-support-for-bgp"></a>Czy jest obsługiwany protokół BGP?

Tak. Protokół BGP jest obsługiwany. Aby upewnić się, że trasy z sieci wirtualnej wirtualnego urządzenia sieciowego są odpowiednio anonsowane, szprychy muszą wyłączyć protokół BGP, jeśli są połączone z siecią wirtualną wirtualnego urządzenia sieciowego, które jest połączone z koncentratorem wirtualnym. Ponadto można łączyć sieci wirtualne będące szprychami z koncentratorem wirtualnym.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Czy można kierować ruchem przy użyciu trasy zdefiniowanej przez użytkownika w koncentratorze wirtualnym?

Trasa zdefiniowana przez użytkownika i funkcja routingu będą dostępne po ogólnym udostępnieniu.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Czy są dostępne informacje na temat licencjonowania i cen wirtualnej sieci WAN?
 
Podczas udostępniania wersji zapoznawczej nie są naliczane dodatkowe opłaty. Bieżący [cennik sieci VPN platformy Azure i ruchu wychodzącego](https://azure.microsoft.com/pricing/details/vpn-gateway/) będzie obowiązywać w okresie wersji zapoznawczej.