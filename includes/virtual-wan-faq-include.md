---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/18/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d059cab5668eef8d4dafc1442ca9749a7dcf8c9d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162518"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Jaka jest różnica między bramą sieci wirtualnej platformy Azure (VPN Gateway) i Azure bramą VPN Gateway wirtualnej sieci WAN platformy Azure?

Wirtualna sieć WAN zapewnia łączność typu lokacja-lokacja w dużej skali oraz oferuje przepływność, skalowalność i łatwość użycia. Urządzenia z oddziału urządzeń lokalnych klienta są automatycznie aprowizowane i łączone z wirtualną siecią WAN platformy Azure. Te urządzenia są dostępne w rozwijającym się ekosystemie partnerów sieci SD-WAN i VPN.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Którzy dostawcy urządzeń (partnerzy wirtualnej sieci WAN) są obsługiwani w czasie wprowadzenia oferty? 

Obecnie firmy Citrix i Riverbed obsługują w pełni zautomatyzowane środowisko wirtualnej sieci WAN. Aby uzyskać więcej informacji, zobacz [Virtual WAN partners (Partnerzy wirtualnej sieci WAN)](https://aka.ms/virtualwan).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Czy muszę korzystać z urządzenia preferowanego przez partnera?

Nie. Możesz używać dowolnego urządzenia obsługującego sieć VPN, które spełnia wymagania wersji zapoznawczej pod kątem obsługi protokołu IKEv2 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partnerzy wirtualnej sieci WAN automatyzują połączenie z wirtualną siecią WAN platformy Azure?

Zdefiniowane programowo rozwiązania w zakresie łączności zwykle umożliwiają zarządzanie urządzeniami oddziału przy użyciu kontrolera lub centrum aprowizacji urządzeń. Kontroler może zautomatyzować połączenie z wirtualną siecią WAN platformy Azure za pomocą interfejsów API platformy Azure. Aby uzyskać więcej informacji, zobacz [Virtual WAN partner automation (Automatyzacja dla partnerów usługi Virtual WAN)](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Czy wirtualna sieć WAN zmienia istniejące funkcje łączności?   

Nie ma żadnych zmian istniejących funkcji łączności platformy Azure.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Czy są dostępne nowe zasoby usługi Resource Manager dla wirtualnej sieci WAN?
  
Tak. Wraz z wirtualną siecią WAN wprowadzono nowe zasoby usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [Omówienie](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Czy są specjalne wymagania dotyczące dołączania do wersji zapoznawczej? 

Aby móc skonfigurować wirtualną sieć WAN platformy Azure, musisz zarejestrować subskrypcję w wersji zapoznawczej. Aby przeprowadzić rejestrację, wyślij wiadomość e-mail na adres <azurevirtualwan@microsoft.com>, podając identyfikator subskrypcji. Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail. Do czasu otrzymania potwierdzenia rejestracji subskrypcji nie można korzystać z wirtualnej sieci WAN w portalu.

Zagadnienia do rozważenia:

* Wersja zapoznawcza jest ograniczona do publicznych regionów platformy Azure.
* Obsługiwanych jest do 100 połączeń na koncentrator wirtualny. Każde połączenie obejmuje dwa tunele w konfiguracji aktywne-aktywne. Tunele kończą się w bramie VPN Gateway koncentratora wirtualnego platformy Azure.
* Rozważ użycie tej wersji zapoznawczej w następujących sytuacjach:
  * Chcesz wdrożyć zagregowaną przepustowość mniejszą niż 1 Gb/s na koncentrator wirtualny.
  * Masz urządzenie sieci VPN, które obsługuje konfigurację opartą na trasach i połączenia IKEv2 IPsec.
  * Chcesz wypróbować technologię SD-WAN i urządzenia oddziału operacyjnego od partnerów we wprowadzaniu produktu na rynek (Riverbed i Citrix).<br>lub<br>Chcesz skonfigurować łączność między poszczególnymi oddziałami oraz między oddziałem a platformą Azure, która obejmuje zarządzanie konfiguracją urządzenia lokalnego. (Konfiguracja samodzielna)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Czy wirtualna sieć WAN platformy Azure obsługuje globalne wirtualne sieci równorzędne? 

 Nie.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Czy sieci wirtualne będące szprychami połączone z koncentratorem wirtualnym mogą komunikować się ze sobą?

Tak. Możesz bezpośrednio nawiązywać komunikację równorzędną sieci wirtualnej między szprychami, które są połączone z koncentratorem wirtualnym. Aby uzyskać więcej informacji, zobacz [Wirtualne sieci równorzędne](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Czy mogę wdrożyć moje ulubione wirtualne urządzenie sieciowe (w sieci wirtualnej wirtualnego urządzenia sieciowego) i używać go za pomocą wirtualnej sieci WAN platformy Azure?

Tak. Możesz połączyć sieć wirtualną ulubionego wirtualnego urządzenia sieciowego z usługą Azure Virtual WAN, ale wymaga to możliwości routingu w koncentratorze. Obsługa tej funkcji znajduje się w naszym harmonogramie działania. Wszystkie szprychy połączone z siecią wirtualną wirtualnego urządzenia sieciowego muszą też być połączone z koncentratorem wirtualnym. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Czy sieć wirtualna wirtualnego urządzenia sieciowego może zawierać bramę sieci wirtualnej?

Nie. Sieć wirtualna wirtualnego urządzenia sieciowego nie może mieć bramy sieci wirtualnej, jeśli jest połączona z koncentratorem wirtualnym. 

### <a name="is-there-support-for-bgp"></a>Czy jest obsługiwany protokół BGP?

Tak. Protokół BGP jest obsługiwany. Aby upewnić się, że trasy z sieci wirtualnej wirtualnego urządzenia sieciowego są odpowiednio anonsowane, szprychy muszą wyłączyć protokół BGP, jeśli są połączone z siecią wirtualną wirtualnego urządzenia sieciowego, które jest połączone z koncentratorem wirtualnym. Ponadto można łączyć sieci wirtualne będące szprychami z koncentratorem wirtualnym.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Czy można kierować ruchem przy użyciu trasy zdefiniowanej przez użytkownika w koncentratorze wirtualnym?

Jest to w naszym harmonogramie działania. Śledź aktualności!

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Czy są dostępne informacje na temat licencjonowania i cen wirtualnej sieci WAN?
 
Podczas udostępniania wersji zapoznawczej nie są naliczane dodatkowe opłaty. Bieżący [cennik sieci VPN platformy Azure i ruchu wychodzącego](https://azure.microsoft.com/pricing/details/vpn-gateway/) będzie obowiązywać w okresie wersji zapoznawczej.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>W jaki sposób partnerzy, którzy nie są wymienieni na liście partnerów uruchomienia, mogą dołączyć do programu?

Wyślij wiadomość e-mail na adres azurevirtualwan@microsoft.com. Idealny partner powinien dysponować urządzeniem, które można aprowizować na potrzeby połączeń IKEv2 IPSec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Czy istnieje możliwość skonstruowania sieci usługi Azure Virtual WAN przy użyciu szablonu usługi Resource Manager?

Pracujemy nad tym. W tej chwili usługa jest oparta na interfejsie REST i na portalu.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Czy łączność między oddziałami jest dozwolona w usłudze Virtual WAN?

Tak. Łączność między oddziałami jest dostępna w usłudze Virtual WAN.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Czy ruch zachodzący między oddziałami przechodzi przez usługę Azure Virtual WAN?

Tak.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Czym różni się usługa Virtual WAN od istniejącej bramy sieci wirtualnej platformy Azure?

Sieć VPN bramy sieci wirtualnej jest ograniczona do 30 tuneli. W przypadku połączeń należy używać usługi Virtual WAN dla sieci VPN na dużą skalę. W publicznej wersji zapoznawczej ta funkcjonalność jest ograniczona do 100 połączeń oddziałów o szybkości 1 GB/s w piaście.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Czy usługa Virtual WAN wymaga usługi ExpressRoute w każdej lokacji?

Nie. Usługa Virtual WAN nie wymaga usługi ExpressRoute w każdej lokacji. Używa ona standardowej łączności IPsec typu lokacja-lokacja z użyciem linków internetowych z urządzenia do piasty usługi Azure Virtual WAN.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Czy istnieje limit przepływności sieci podczas korzystania z usługi Azure Virtual WAN?

W publicznej wersji zapoznawczej liczba oddziałów jest ograniczona do 100 połączeń na centrum/region, a łączna szybkość do 1 GB/s w piaście.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Czy usługa Virtual WAN umożliwia urządzeniu lokalnemu równoległe korzystanie z wielu usługodawców internetowych, czy jest to zawsze pojedynczy tunel sieci VPN?

Z pojedynczego oddziału może wychodzić wiele tuneli typu aktywne/aktywne (2 tunele = 1 połączenie usługi Azure Virtual WAN) w zależności od urządzenia-gałęzi.

### <a name="how-is-traffic-is-routed-on-the-azure-backbone"></a>W jaki sposób ruch jest kierowany w sieci szkieletowej platformy Azure?

Ruch zachodzi zgodnie ze wzorcem: urządzenie-gałąź->usługodawca Internetowy->Microsoft Edge->Microsoft DC->Microsoft Edge->usługodawca Internetowy->urządzenie-gałąź.

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Które elementy tego modelu muszą znajdować się w każdej lokacji? Czy wystarczy tylko połączenie internetowe?

Tak. Połączenie internetowe oraz urządzenie fizyczne, najlepiej od naszych powiązanych partnerów. Chyba że chcesz ręcznie zarządzać konfiguracją i łącznością z platformą Azure z poziomu preferowanego urządzenia.