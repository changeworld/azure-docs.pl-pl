---
title: Połączenie z Chinami przy użyciu wirtualnej sieci WAN platformy Azure i bezpiecznego centrum
description: Dowiedz się więcej o wirtualnej sieci WAN zautomatyzowanej skalowalnej łączności między gałęziami, dostępnych regionach i partnerach.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985630"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Połączenie z Chinami przy użyciu wirtualnej sieci WAN platformy Azure i usługi Secure Hub

Patrząc na wspólną motoryzację, przemysł motoryzacyjny, logistyczny lub inne instytuty, takie jak ambasady, często pojawia się pytanie, jak poprawić wzajemne połączenia z Chinami. Te ulepszenia są głównie istotne w przypadku korzystania z usług w chmurze, takich jak Office 365, Usługi globalne platformy Azure lub wzajemnych oddziałów wewnątrz Chin z szkieletem klienta.

W większości przypadków klienci mają problemy z wysokimi opóźnieniami, niską przepustowością, niestabilnym połączeniem i wysokimi kosztami łączenia się poza Chinami (na przykład w Europie lub Stanach Zjednoczonych).

Powodem tych zmagań jest "Wielka Zapora Chińska", która chroni chińską część Internetu i filtruje ruch do Chin. Prawie cały ruch biegnący z Chin kontynentalnych do poza Chinami, z wyjątkiem specjalnych stref administracyjnych, takich jak Hongkong i Makau, przechodzi przez Wielką Zaporę. Ruch biegnący przez Hongkong i Makau nie uderza w Wielką Zaporę w pełnej mocy, jest obsługiwany przez podzbiór Wielkiej Zapory Ogniowej.

![Połączenie między dostawcami](./media/interconnect-china/provider.png)

Korzystając z wirtualnej sieci WAN, klient może nawiązać bardziej wydajne i stabilne połączenie z usługami Microsoft Cloud Services oraz połączenie z siecią przedsiębiorstwa bez łamania chińskiego prawa cyberbezpieczeństwa.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Wymagania i przepływ pracy

Jeśli chcesz zachować zgodność z chińskim prawem dotyczącym cyberbezpieczeństwa, musisz spełnić określone warunki.

Najpierw musisz współpracować z siecią i usługodawcą internetowym, który jest właścicielem licencji ICP (Internet Content Provider) dla Chin. W większości przypadków będziesz w końcu z jednym z następujących dostawców:

* China Telecom Global Sp. z o.o.
* Chiny Mobile Sp. z o.o.
* Chiny Unicom Sp. z o.o.
* PCCW Global Sp. z o.o.
* Hong Kong Telecom Sp. z o.o.

W zależności od dostawcy i twoich potrzeb musisz teraz zakupić jedną z następujących usług łączności sieciowej, aby połączyć swoje oddziały w Chinach.

* Sieć MPLS/IPVPN 
* Program definiowany przez program WAN (SDWAN)
* Dedykowany dostęp do Internetu

Następnie musisz zgodzić się z tym dostawcą, aby dać breakout do Microsoft Global Network i jego Edge Network w Hong Kongu, a nie w Pekinie lub Szanghaju. W tym przypadku Hongkong jest bardzo ważny ze względu na jego fizyczne połączenie i lokalizację z Chinami.

Podczas gdy większość klientów uważa, że korzystanie z Singapuru dla połączeń jest najlepszym przypadkiem, ponieważ wygląda bliżej Chin, gdy patrzy na mapie, nie jest to prawdą. Gdy śledzisz mapy światłowodowe sieci, prawie wszystkie połączenia sieciowe przechodzą przez Pekin, Szanghaj i Hongkong. To sprawia, że Hongkong jest lepszym wyborem lokalizacji, aby połączyć się z Chinami.

W zależności od dostawcy możesz otrzymać różne oferty usług. W poniższej tabeli przedstawiono przykład dostawców i usługi, które oferują, na podstawie informacji w momencie pisania tego artykułu.

| Usługa | Przykłady dostawców |
| --- | --- |
| Sieć MPLS/IPVPN |PCCW, Chiny Telecom Global |
|SDWAN ( SDWAN )| PCCW, Chiny Telecom Global|
| Dedykowany dostęp do Internetu | PCCW, Hong Kong Telecom, Chiny Mobil|

Za pomocą dostawcy można uzgodnić, które z następujących dwóch rozwiązań do osiągnięcia globalnego szkieletu firmy Microsoft:

* Zakończenie usługi Microsoft Azure ExpressRoute w Hongkongu. Tak byłoby w przypadku korzystania z MPLS/IPVPN. Obecnie jedynym dostawcą licencji ICP z usługą ExpressRoute do Hong Kongu jest China Telecom Global. Mogą jednak również rozmawiać z innymi dostawcami, jeśli wykorzystują dostawców wymiany w chmurze, takich jak Megaport lub InterCloud. Aby uzyskać więcej informacji, zobacz [Dostawcy łączności usługi ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Korzystanie z dedykowanego dostępu do Internetu bezpośrednio w jednym z następujących punktów programu Internet Exchange Points lub przy użyciu sieci prywatnej.

Poniższa lista pokazuje, że wymiana internetowa jest możliwa w Hongkongu:

* AMS-IX Hongkong
* BBIX Hongkong
* Equinix Hongkong
* HKIX ( HKIX )

Podczas korzystania z tego połączenia następny przeskok BGP dla usług firmy Microsoft musi być numerem systemu autonomicznego firmy Microsoft (AS#) 8075. Jeśli używasz jednej lokalizacji lub rozwiązania SDWAN, to będzie wybór połączenia.

Tak czy inaczej, nadal zalecamy, aby mieć drugi i regularny Internet Breakout do Chin kontynentalnych. Ma to na celu podzielenie ruchu przedsiębiorstwa na usługi w chmurze, takie jak Microsoft 365 i Azure, oraz regulowany przez prawo ruch internetowy.

Zgodna architektura sieci w Chinach może wyglądać następująco:

![Wiele gałęzi](./media/interconnect-china/multi-branch.png)

W tym przykładzie połączenie z siecią Microsoft Global Network w Hongkongu można teraz rozpocząć korzystanie z [architektury azure virtual WAN Global Transit Architecture](virtual-wan-global-transit-network-architecture.md) i dodatkowych usług, takich jak centrum wirtualnej sieci WAN platformy Azure, aby korzystać z usług i łączyć się z oddziałami i centrum danych poza Chinami.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Komunikacja między węzłami

W tej sekcji używamy wirtualnej komunikacji centrum sieci WAN do połączenia. W tym scenariuszu utworzysz nowy zasób centrum wirtualnej sieci WAN, aby połączyć się z centrum wirtualnej sieci WAN w Hongkongu, innych regionach, w których masz już zasoby platformy Azure lub gdzie chcesz się połączyć.

Przykładowa architektura może wyglądać następująco:

![Przykładowa wan](./media/interconnect-china/sample.png)

W tym przykładzie chińskie gałęzie łączą się z usługą Azure Cloud China i sobą nawzajem przy użyciu połączeń SIECI VPN lub MPLS. Gałęzie, które muszą być połączone z usługami globalnymi, używają protokołu MPLS lub usług internetowych połączonych bezpośrednio z Hongkongiem. Jeśli chcesz korzystać z usługi ExpressRoute w Hongkongu, a także w innym regionie, musisz skonfigurować [globalny zasięg usługi ExpressRoute,](../expressroute/expressroute-global-reach.md) aby połączyć oba obwody usługi ExpressRoute.

Globalny zasięg usługi ExpressRoute nie jest dostępny w niektórych regionach. Jeśli na przykład musisz połączyć się z Brazylią lub Indiami, musisz korzystać z [usług dostawców usług wymiany w chmurze](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) w celu świadczenia usług routingu.

Poniższy rysunek przedstawia oba przykłady dla tego scenariusza.

![Globalny zasięg](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Bezpieczne wyrwanie internetu dla usługi Office 365

Inną kwestią jest bezpieczeństwo sieci, a także rejestrowanie punktu wejścia między Chinami a składnikiem szkieletowym wirtualnej sieci WAN i szkieletem klienta. W większości przypadków istnieje potrzeba wyrwania się z Internetu w Hong Kongu, aby bezpośrednio dotrzeć do sieci Microsoft Edge Network, a tym samym serwerów azure front door używanych dla usług Microsoft 365.

W obu scenariuszach z wirtualną siecią WAN można korzystać z [centrum zabezpieczonego wirtualnej sieci WAN platformy Azure.](../firewall-manager/secured-virtual-hub.md) Korzystając z usługi Azure Firewall Manager, można zmienić zwykłe centrum wirtualnej sieci WAN na zabezpieczoną koncentrator, a następnie wdrożyć zaporę platformy Azure i zarządzać nią w tym centrum.

Na poniższej ilustracji przedstawiono przykład tego scenariusza:

![Wyrwanie internetu dla ruchu w sieci Web i usługach firmy Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architektura i przepływy ruchu

W zależności od wyboru dotyczącego połączenia z Hongkongiem ogólna architektura może się nieznacznie zmienić. W tej sekcji przedstawiono trzy dostępne architektury w różnych kombinacjach z sieci VPN lub SDWAN i/lub Usługi ExpressRoute.

Wszystkie te opcje korzystają z usługi Azure Virtual WAN zabezpieczone centrum dla bezpośredniej łączności M365 w Hong Kongu. Te architektury obsługują również wymagania dotyczące zgodności dla [usługi Office 365 Multi-Geo](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) i utrzymują ten ruch w pobliżu następnej lokalizacji drzwi wejściowych usługi Office 365. W rezultacie jest to również poprawa wykorzystania microsoftu 365 z Chin.

Podczas korzystania z wirtualnej sieci WAN platformy Azure wraz z połączeniami internetowymi każde połączenie może korzystać z dodatkowych usług, takich jak [usługi komunikacji równorzędnej platformy Microsoft Azure (MAPS).](https://docs.microsoft.com/azure/peering-service/about) Usługa MAPS została stworzona w celu optymalizacji ruchu przychodzącego do sieci Microsoft Global Network od dostawców usług internetowych innych firm.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opcja 1: SDWAN lub VPN

W tej sekcji omówiono projekt, który używa funkcji SDWAN lub VPN w Hongkongu i innych oddziałach. Ta opcja pokazuje użycie i przepływ ruchu podczas korzystania z czystego połączenia internetowego w obu witrynach szkieletu wirtualnej sieci WAN. W takim przypadku połączenie jest doprowadzone do Hongkongu przy użyciu dedykowanego dostępu do Internetu lub rozwiązania SDWAN dostawcy ICP. Inne gałęzie korzystają z czystego Internetu lub rozwiązań SDWAN.

![Ruch w Chinach do Hongkongu](./media/interconnect-china/china-traffic.png)

W tej architekturze każda lokacja jest połączona z siecią Microsoft Global Network przy użyciu sieci VPN i wirtualnej sieci WAN platformy Azure. Ruch między witrynami a Hongkongiem jest przesyłany przez sieć Microsoft Network i korzysta tylko ze zwykłego połączenia internetowego na ostatniej mili.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opcja 2: Usługa ExpressRoute i SDWAN lub VPN

W tej sekcji omówiono projekt, który używa usługi ExpressRoute w Hong Kongu i innych oddziałach z oddziałami SIECI VPN/SDWAN. Ta opcja pokazuje korzystanie z usługi ExpressRoute i usługi ExpressRoute zakończone w Hong Kongu i innych oddziałach połączonych za pośrednictwem funkcji SDWAN lub VPN. Usługa ExpressRoute w Hong Kongu jest obecnie ograniczona do krótkiej listy dostawców, którą można znaleźć na liście [Partnerów trasy ekspresowej](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![Ruch w Chinach do Hongkongu Usługi ExpressRoute](./media/interconnect-china/expressroute.png)

Istnieją również opcje zakończenia usługi ExpressRoute z Chin, na przykład w Korei Południowej lub Japonii. Ale biorąc pod uwagę zgodność, regulacje i opóźnienia, Hongkong jest obecnie najlepszym wyborem.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opcja 3: Tylko usługa ExpressRoute

W tej sekcji omówiono projekt, który jest używany w przypadku usługi ExpressRoute dla Hongkongu i innych gałęzi. Ta opcja pokazuje połączenie za pomocą usługi ExpressRoute na obu końcach. Tutaj masz inny przepływ ruchu niż inne. Ruch usługi Microsoft 365 będzie przepływał do wirtualnego centrum sieci WAN platformy Azure, a stamtąd do sieci Microsoft Edge Network i Internetu.

Ruch, który idzie do połączonych oddziałów lub z nich do lokalizacji w Chinach będzie po innym podejściu w tej architekturze. Obecnie wirtualna sieć WAN nie obsługuje usługi ExpressRoute do przesyłania usługi ExpressRoute. Ruch będzie korzystać z usługi ExpressRoute Global Reach lub połączenia 3rd Party bez przechodzenia wirtualnego Centrum WAN. Będzie przepływać bezpośrednio z jednego programu Microsoft Enterprise Edge (MSEE) do drugiego.

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

Obecnie usługa ExpressRoute Global Reach nie jest dostępna w każdym kraju, ale można skonfigurować rozwiązanie przy użyciu wirtualnej sieci WAN platformy Azure.

Można na przykład skonfigurować usługę ExpressRoute za pomocą komunikacji równorzędnej firmy Microsoft i połączyć tunel sieci VPN za pośrednictwem tej komunikacji równorzędnej z wirtualną siecią WAN platformy Azure. Teraz ponownie włączysz tranzyt między siecią VPN i usługą ExpressRoute bez globalnego zasięgu oraz dostawcą i usługą innej firmy, taką jak Megaport Cloud.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Architektura sieci Usługi Global Transit z wirtualną siecią WAN platformy Azure](virtual-wan-global-transit-network-architecture.md)

* [Tworzenie wirtualnego centrum sieci WAN](virtual-wan-site-to-site-portal.md)

* [Konfigurowanie centrum zabezpieczonego wirtualnej sieci WAN](../firewall-manager/secure-cloud-network.md)

* [Omówienie usługi komunikacji równorzędnej platformy Azure](https://docs.microsoft.com/azure/peering-service/about)
