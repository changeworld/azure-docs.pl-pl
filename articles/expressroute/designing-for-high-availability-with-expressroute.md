---
title: 'Azure ExpressRoute: Projektowanie dla wysokiej dostępności'
description: Ta strona zawiera zalecenia architektury dotyczące wysokiej dostępności podczas korzystania z usługi Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076679"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Projektowanie dla wysokiej dostępności za pomocą usługi ExpressRoute

Usługa ExpressRoute została zaprojektowana z myślą o wysokiej dostępności w celu zapewnienia łączności sieci prywatnej klasy operatora z zasobami firmy Microsoft. Innymi słowy nie ma jednego punktu awarii w ścieżce usługi ExpressRoute w sieci Microsoft. Aby zmaksymalizować dostępność, należy również zachwycić segment klienta i dostawcy usług obwodu usługi ExpressRoute w celu zapewnienia wysokiej dostępności. W tym artykule najpierw przyjrzyjmy się zagadnieniom architektury sieci do tworzenia niezawodnej łączności sieciowej przy użyciu usługi ExpressRoute, a następnie przyjrzyjmy się funkcjom dostrajania, które pomagają poprawić wysoką dostępność obwodu usługi ExpressRoute.


## <a name="architecture-considerations"></a>Zagadnienia dotyczące architektury

Na poniższym rysunku przedstawiono zalecany sposób łączenia się przy użyciu obwodu usługi ExpressRoute w celu maksymalizacji dostępności obwodu usługi ExpressRoute.

 [![1]][1]

Aby zapewnić wysoką dostępność, konieczne jest utrzymanie nadmiarowości obwodu usługi ExpressRoute w całej sieci end-to-end. Innymi słowy należy zachować nadmiarowość w sieci lokalnej i nie powinno naruszyć nadmiarowości w sieci dostawcy usług. Utrzymywanie nadmiarowości na minimalnym poziomie oznacza unikanie pojedynczych awarii sieci. Nadmiarowe zasilanie i chłodzenie urządzeń sieciowych dodatkowo poprawi wysoką dostępność.

### <a name="first-mile-physical-layer-design-considerations"></a>Zagadnienia dotyczące projektowania warstwy fizycznej na pierwszym kilometrze

 Jeśli połączenie podstawowe i pomocnicze obwodów usługi ExpressRoute na tym samym sprzęcie lokalnym klienta (CPE) zostanie zakończone, narażasz na szwank wysoką dostępność w sieci lokalnej. Ponadto w przypadku skonfigurowania połączeń podstawowych i pomocniczych za pośrednictwem tego samego portu CPE (przez zakończenie dwóch połączeń pod różnymi podprzekami lub przez połączenie dwóch połączeń w sieci partnerskiej), wymuszasz na partnerze wysokiej dostępności w segmencie sieci. Kompromis ten przedstawiono na poniższym rysunku.

[![2]][2]

Z drugiej strony, jeśli zakończysz połączenia podstawowe i pomocnicze obwodów usługi ExpressRoute w różnych lokalizacjach geograficznych, może to nawrót wydajności sieci łączności. Jeśli ruch jest aktywnie równoważony obciążeniem między połączeniami podstawowymi i pomocniczymi, które są zakończone w różnych lokalizacjach geograficznych, potencjalna istotna różnica w opóźnieniu sieci między tymi dwiema ścieżkami spowoduje nieoptymalną sieć Wydajności. 

Aby zapoznać się z zagadnieniami związanymi z projektowaniem geograficznie, zobacz [Projektowanie odzyskiwania po awarii za pomocą usługi ExpressRoute][DR].

### <a name="active-active-connections"></a>Aktywne i aktywne połączenia

Sieć firmy Microsoft jest skonfigurowana do obsługi połączeń podstawowych i pomocniczych obwodów usługi ExpressRoute w trybie aktywnym i aktywnym. Jednak za pośrednictwem anonsów trasy można wymusić nadmiarowe połączenia obwodu usługi ExpressRoute do pracy w trybie aktywnym i pasywnym. Reklamy bardziej szczegółowe trasy i BGP AS path prepending są wspólne techniki używane do jednej ścieżki preferowane przez drugą.

Aby zwiększyć wysoką dostępność, zaleca się obsługę obu połączeń obwodu usługi ExpressRoute w trybie aktywnym i aktywnym. Jeśli połączenia zostaną włączone do trybu aktywny-aktywny, sieć firmy Microsoft zrównoważy ruch między połączeniami na podstawie przepływu.

Uruchomienie połączeń podstawowych i pomocniczych obwodu usługi ExpressRoute w trybie aktywnym i pasywnym napotyka ryzyko niepowodzenia obu połączeń po awarii aktywnej ścieżki. Najczęstszymi przyczynami awarii przy przełączaniu są brak aktywnego zarządzania połączeniem pasywnym i pasywne połączenie reklamujące przestarzałe trasy.

Alternatywnie uruchomienie połączeń podstawowych i pomocniczych obwodu usługi ExpressRoute w trybie aktywnym i aktywnym powoduje tylko około połowę przepływów w przypadku niepowodzenia i przekierowania, po awarii połączenia usługi ExpressRoute. W ten sposób tryb aktywny i aktywny znacznie pomoże poprawić średni czas odzyskiwania (MTTR).

### <a name="nat-for-microsoft-peering"></a>Translator z translatora i rozpowszechnianie informacji na potrzeby komunikacji równorzędnej firmy Microsoft 

Komunikacja równorzędna firmy Microsoft jest przeznaczona do komunikacji między publicznymi punktami końcowymi. Dlatego często lokalne prywatne punkty końcowe są przetłumaczonym adresem sieciowym (NATed) z publicznym adresem IP w sieci klienta lub partnera, zanim komunikują się za pośrednictwem komunikacji równorzędnej firmy Microsoft. Zakładając, że używasz zarówno połączeń podstawowych, jak i pomocniczych w trybie aktywnym i aktywnym, gdzie i jak nat ma wpływ na szybkość odzyskiwania po awarii w jednym z połączeń usługi ExpressRoute. Dwie różne opcje NAT są zilustrowane na poniższym rysunku:

[![3]][3]

W opcji 1 NAT jest stosowany po podzieleniu ruchu między połączenia podstawowe i pomocnicze usługi ExpressRoute. Aby spełnić stanowe wymagania translatora adresów sieciowych, niezależne pule TRANSLATORA są używane między urządzeniami podstawowymi i pomocniczymi, dzięki czemu ruch zwrotny dociera do tego samego urządzenia brzegowego, przez które przepływ był przesyłany wychodzący.

W opcji 2 przed podzieleniem ruchu między połączenia podstawowe i pomocnicze usługi ExpressRoute jest używana wspólna pula translatora nat. Ważne jest, aby dokonać rozróżnienia, że wspólna pula NAT przed podziałem ruchu nie oznacza wprowadzenie pojedynczego punktu awarii, co naraża na szwank wysoką dostępność.

W przypadku opcji 1 po awarii połączenia usługi ExpressRoute zdolność dotarcia do odpowiedniej puli NAT jest przerywana. W związku z tym wszystkie przerwane przepływy muszą zostać ponownie ustanowione przez TCP lub warstwę aplikacji po odpowiednim przesunie czasu okna. Jeśli którakolwiek z pul NAT są używane do frontend dowolnego z serwerów lokalnych i jeśli odpowiednia łączność nie powiodło się, serwerów lokalnych nie można uzyskać z platformy Azure, dopóki łączność zostanie naprawiona.

Natomiast z opcją 2 NAT jest osiągalny nawet po awarii połączenia podstawowego lub pomocniczego. W związku z tym sama warstwa sieciowa może przekierować pakiety i przyspieszyć odzyskiwanie po awarii. 

> [!NOTE]
> Jeśli używasz opcji NAT 1 (niezależne pule NAT dla podstawowych i pomocniczych połączeń usługi ExpressRoute) i mapujesz port adresu IP z jednej puli NAT na serwer lokalny, serwer nie będzie dostępny za pośrednictwem obwodu usługi ExpressRoute, gdy połączenie nie powiedzie się.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Funkcje dostrajania dla prywatnej komunikacji równorzędnej

W tej sekcji pozwól nam przejrzeć opcjonalne (w zależności od wdrożenia platformy Azure i jak wrażliwe są funkcje MTTR), które pomagają poprawić wysoką dostępność obwodu usługi ExpressRoute. W szczególności przejrzyjmy wdrożenie bramy sieci wirtualnej usługi ExpressRoute i dwukierunkowe wykrywanie przekazywania dalej (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Strefy dostępności obsługujące bramy sieci wirtualnej usługi ExpressRoute

Strefa dostępności w regionie świadczenia usługi Azure jest kombinacją domeny błędów i domeny aktualizacji. Jeśli zdecydujesz się na wdrożenie usługi Azure IaaS w nadmiarze stref, możesz również skonfigurować bramy sieci wirtualnej nadmiarowe strefowe, które kończą prywatną komunikację równorzędną usługi ExpressRoute. Aby dowiedzieć się więcej, zobacz [Bramy sieci wirtualnej nadmiarowe stref w strefach dostępności platformy Azure][zone redundant vgw]— informacje . Aby skonfigurować bramę sieci wirtualnej nadmiarową [strefą, zobacz Tworzenie bramy sieci wirtualnej nadmiarowej strefowej w strefach dostępności platformy Azure][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Poprawa czasu wykrywania awarii

Usługa ExpressRoute obsługuje bfd za prywatną komunikację równorzędnych. BFD skraca czas wykrywania awarii w sieci warstwy 2 między usługą Microsoft Enterprise Edge (MSEE) a ich sąsiadami BGP po stronie lokalnej z około 3 minut (domyślnie) do mniej niż jednej sekundy. Szybki czas wykrywania awarii pomaga przyspieszyć odzyskiwanie błędów. Aby dowiedzieć się więcej, zobacz [Konfigurowanie BFD zawersają expressroute][BFD].

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiliśmy sposób projektowania dla wysokiej dostępności łączności obwodu usługi ExpressRoute. Punkt komunikacji równorzędnej obwodu usługi ExpressRoute jest przypięty do lokalizacji geograficznej i dlatego może mieć wpływ katastrofalny błąd, który wpływa na całą lokalizację. 

W przypadku zagadnień projektowych dotyczących tworzenia geograficznie nadmiarowej łączności sieciowej z szkieletem firmy Microsoft, która może wytrzymać katastrofalne awarie, które mają wpływ na cały region, zobacz [Projektowanie odzyskiwania po awarii za pomocą prywatnej komunikacji równorzędnej usługi ExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "Zalecany sposób łączenia się za pomocą usługi ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Nieoptymalne połączenie ostatniej mili"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "Opcje NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




