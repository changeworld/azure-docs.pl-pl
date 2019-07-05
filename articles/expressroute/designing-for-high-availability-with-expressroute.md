---
title: Projektowanie pod kątem wysokiej dostępności przy użyciu usługi Azure ExpressRoute | Dokumentacja firmy Microsoft
description: Ta strona zawiera architektury zalecenia dotyczące wysokiej dostępności, podczas korzystania z usługi Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4984b30daf6170873cad9472bfed2d879af57efe
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466645"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Projektowanie pod kątem wysokiej dostępności przy użyciu usługi ExpressRoute

Usługa ExpressRoute jest przeznaczona dla wysokiej dostępności zapewnić operatora połączenie sieci prywatnej klasy korporacyjnej z zasobami firmy Microsoft. Innymi słowy nie ma żadnego pojedynczego punktu awarii w ścieżce usługi ExpressRoute w sieci firmy Microsoft. Aby zmaksymalizować dostępność, klienta i segment dostawcy usługi obwód usługi ExpressRoute powinien również być zaprojektowana z myślą o wysokiej dostępności. W tym artykule, najpierw Przyjrzyjmy zapozna sieci architektury informacje dotyczące tworzenia niezawodnej łączności przy użyciu usługi ExpressRoute, a następnie Przyjrzyjmy się dostosowawczych funkcje, które ułatwiają poprawić wysoką dostępność obwód usługi ExpressRoute.


## <a name="architecture-considerations"></a>Zagadnienia dotyczące architektury

Na poniższym rysunku przedstawiono zalecany sposób połączyć się przy użyciu obwodu usługi ExpressRoute do maksymalizacji dostępności obwodu usługi ExpressRoute.

 [![1]][1]

Wysoką dostępność jest niezbędne do obsługi nadmiarowości obwodu usługi ExpressRoute w całej sieci end-to-end. Innymi słowy trzeba utrzymywać nadmiarowości w sieci lokalnej, a nie może naruszyć bezpieczeństwo nadmiarowość w ramach sieci dostawcy usług. Utrzymywanie nadmiarowości co najmniej oznacza uniknięcia pojedynczego punktu awarii sieci. Istnienie nadmiarowe zasilanie i chłodzenie sieci, z którego urządzenia będzie dalsze poprawić wysoką dostępność.

### <a name="first-mile-physical-layer-design-considerations"></a>Pierwszy zagadnienia dotyczące projektowania w warstwie fizycznej ogniw w łańcuchu

 Jeżeli klient zakończy się zarówno połączeń podstawowych i pomocniczych obwodów usługi ExpressRoute na tych samych urządzeń lokalnych klienta (CPE), masz naruszenie wysokiej dostępności w ramach sieci lokalnej. Ponadto jeśli konfigurujesz zarówno podstawowego i pomocniczego połączeń za pomocą tego samego portu CPE (albo zapobieżenia dwóch połączeń w ramach różnych interfejsy podrzędne scalania dwóch połączeń w ramach sieci partnerów), wymuszono partnera do naruszenia bezpieczeństwa, wysokiej dostępności w ich segmencie sieci. To naruszenie zilustrowano na poniższym rysunku.

[![2]][2]

Z drugiej strony Jeżeli klient zakończy podstawowej i dodatkowej połączeń obwodów usługi ExpressRoute w różnych lokalizacjach geograficznych, następnie użytkownik może być utraty wydajności sieci połączenia. Jeśli ruch jest aktywnie równoważone między podstawowym i dodatkowych połączeń, które kończą się w różnych lokalizacjach geograficznych, potencjalnych istotne różnice w opóźnienie sieci między tych dwóch ścieżek mogłoby spowodować panującymi w sieci wydajność. 

Zagadnienia dotyczące projektowania magazynu geograficznie nadmiarowego, można zobaczyć [projektowanie pod kątem odzyskiwania po awarii dzięki usłudze ExpressRoute][DR].

### <a name="active-active-connections"></a>Połączeń typu aktywne aktywne

Sieć firmy Microsoft jest skonfigurowany do obsługi połączeń podstawowych i pomocniczych obwodów usługi ExpressRoute w trybie aktywny aktywny. Jednak za pośrednictwem usługi anonsy tras, możesz wymusić nadmiarowe połączenia obwodu usługi ExpressRoute do pracy w trybie aktywny / pasywny. Anonsuje bardziej specyficzne tras i protokołu BGP, dołączania ścieżki AS są typowe metody używane do tworzenia ścieżek preferowany nad innymi.

Aby poprawić wysoką dostępność, zaleca się działać połączenia obwodu usługi ExpressRoute w trybie aktywny aktywny. Jeśli wybierzesz opcję połączenia działają w trybie aktywny / aktywny, sieci firmy Microsoft zostaną załadowane saldo ruch przez połączenia na podstawie przepływów.

Uruchamianie połączeń podstawowych i pomocniczych obwodu usługi ExpressRoute w trybie aktywny / pasywny twarzy ryzyko połączenia kończy się niepowodzeniem po awarii w ścieżce aktywnej. Typowe przyczyny niepowodzenia przełączania za pośrednictwem są braku zarządzania aktywnego połączenia pasywne i pasywnych połączeń anonsowanie tras starych.

Alternatywnie wynikiem działania połączenia podstawowego i pomocniczego obwodu usługi ExpressRoute w trybie aktywny aktywny tylko około połowie przepływów, kończy się niepowodzeniem i wprowadzenie przekierowane, zgodnie z błędem połączenia usługi ExpressRoute. W związku z tym tryb aktywny aktywny będzie znacznie zwiększyć czas do odzyskania (MTTR).

### <a name="nat-for-microsoft-peering"></a>Translator adresów Sieciowych dla komunikacji równorzędnej firmy Microsoft 

Komunikacja równorzędna firmy Microsoft jest przeznaczona do komunikacji między publicznych punktów końcowych. Tak często lokalne punkty końcowe prywatne są sieci translacji adresów (NATed) z publicznym adresem IP na klientów lub partner network przed komunikują się za pośrednictwem komunikacji równorzędnej firmy Microsoft. Przy założeniu, że używasz połączeń podstawowych i pomocniczych w trybie aktywny / aktywny, gdzie i jak możesz translatora adresów Sieciowych ma wpływ na sposób szybkiego odzyskiwania po awarii w jednym z połączeń usługi ExpressRoute. Dwie różne opcje translatora adresów Sieciowych są przedstawione na poniższym rysunku:

[![3]][3]

W przypadku opcji 1 translatora adresów Sieciowych są stosowane po rozdzielenie ruchu między głównych i dodatkowych połączeń usługi ExpressRoute. Do spełnienia wymagań stanowych translatora adresów Sieciowych, niezależnie od pule translatora adresów Sieciowych są używane między podstawowego i pomocniczego urządzenia tak, aby ruch powrotny może pojawić się na tym samym urządzeniu usługi edge za pośrednictwem którego egressed przepływ.

W przypadku opcji 2 wspólnej puli translatora adresów Sieciowych jest używana przed rozdzielenie ruchu między głównych i dodatkowych połączeń usługi ExpressRoute. Należy wprowadzić rozróżnienia, wspólnej puli translatora adresów Sieciowych przed dzielenia ruch oznacza wprowadzenie pojedynczego punktu awarii, w tym samym naruszania wysokiej dostępności.

Za pomocą opcji 1, po awarii połączenia usługi ExpressRoute uzyskanie dostępu do odpowiedniej puli translatora adresów Sieciowych jest uszkodzona. W związku z tym, wszystkie przepływy przerwany, konieczne będzie nawiązane ponownie przez protokół TCP lub warstwie aplikacji po odpowiedni limit czasu okna. Jeśli albo pule translatora adresów Sieciowych są używane do serwera sieci Web dowolnego z serwerów lokalnych i były odpowiednie połączenie nie powiedzie się, serwerów lokalnych nie można nawiązać połączenia z platformy Azure do czasu naprawienia połączenia.

Przy użyciu opcji 2 translatora adresów Sieciowych jest dostępny nawet po awarii połączenia podstawowy lub pomocniczy. W związku z tym warstwy sieciowej, sama można przekierować pakietów i pomocy szybsze odzyskiwanie po awarii. 

> [!NOTE]
> Jeśli korzystasz z translatora adresów Sieciowych, opcja 1 (niezależnie od pule translatora adresów Sieciowych dla połączeń usługi ExpressRoute podstawowych i pomocniczych) i mapują port adresu IP z jednej puli translatora adresów Sieciowych na serwerze lokalnym, serwer nie będzie dostępny za pośrednictwem usługi ExpressRoute circuit, gdy odpowiedni połączenie nie powiodło się.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Dostrajanie funkcje dla prywatnej komunikacji równorzędnej

W tej sekcji Powiadom nas przeglądu opcjonalne (w zależności od wdrożenie systemu Azure i ile poufna jest do MTTR) funkcje Pomóż nam w usprawnianiu wysokiej dostępności obwód usługi ExpressRoute. Ściślej mówiąc Omówmy uwzględniających strefy wdrażanie bramy sieci wirtualnej usługi ExpressRoute i dwukierunkowe przesyłanie dalej wykrywania (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Strefa dostępności pamiętać bramy sieci wirtualnej usługi ExpressRoute

Strefa dostępności w regionie świadczenia usługi Azure jest kombinacją domeny błędów i domeny aktualizacji. Jeśli postanowisz strefowo nadmiarowe wdrożenia infrastruktury IaaS platformy Azure można również skonfigurować bramy sieci wirtualnej strefowo nadmiarowe, kończące prywatnej komunikacji równorzędnej usługi ExpressRoute. Aby dowiedzieć się więcej, zobacz [o bramach sieci wirtualnej strefowo nadmiarowy w strefach dostępności platformy Azure][zone redundant vgw]. To configure zone-redundant virtual network gateway, see [Create a zone-redundant virtual network gateway in Azure Availability Zones][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Zwiększanie czasu wykrycia awarii

Usługa ExpressRoute obsługuje BFD za pośrednictwem prywatnej komunikacji równorzędnej. BFD redukuje czas wykrycia awarii za pośrednictwem sieci warstwy 2 między przeglądarki Microsoft Edge Enterprise (Msee) i jego sąsiadami protokołu BGP stronie lokalnych z około 3 minutach (ustawienie domyślne) do niecałej sekundy. Czas wykrycia awarii szybkie pomaga hastening odzyskiwanie po awarii. Aby dowiedzieć się więcej, zobacz [skonfigurować BFD za pośrednictwem usługi ExpressRoute][BFD].

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono sposób projektowania w celu zapewnienia wysokiej dostępności połączenia obwodu usługi ExpressRoute. Punkt komunikacji równorzędnej obwodu usługi ExpressRoute jest przypięta do lokalizacji geograficznej i dlatego może mieć wpływ poważnej awarii, która ma wpływ na cały lokalizacji. 

Zagadnienia dotyczące tworzenia połączenia sieciowego magazynu geograficznie nadmiarowego sieci szkieletowej firmy Microsoft, która może wytrzymać katastrofalnych awarii, które mają wpływ na cały region, można zobaczyć [projektowanie pod kątem odzyskiwania po awarii przy użyciu prywatnegokomunikacjirównorzędnejusługiExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "zalecany sposób na połączenie przy użyciu usługi ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal ostatnich ogniw w łańcuchu łączności"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "opcji translatora adresów Sieciowych"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




