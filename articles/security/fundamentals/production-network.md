---
title: Sieć produkcyjna platformy Azure
description: Ten artykuł zawiera ogólny opis sieci produkcyjnej platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7c0748e4ff1531649274834cb1e602c228f102e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726690"
---
# <a name="the-azure-production-network"></a>Sieć produkcyjna platformy Azure
Użytkownicy sieci produkcyjnej platformy Azure obejmują zarówno klientów zewnętrznych, którzy uzyskują dostęp do własnych aplikacji platformy Azure, jak i wewnętrznych pracowników pomocy technicznej platformy Azure, którzy zarządzają siecią produkcyjną. W tym artykule omówiono metody dostępu zabezpieczeń i mechanizmy ochrony do ustanawiania połączeń z siecią produkcyjną platformy Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Routing internetowy i odporność na uszkodzenia
Globalnie nadmiarowa wewnętrzna i zewnętrzna infrastruktura usługi DNS (Azure Domain Name Service), w połączeniu z wieloma podstawowymi i pomocniczymi klastrami serwerów DNS, zapewnia odporność na uszkodzenia. W tym samym czasie dodatkowe zabezpieczenia sieci platformy Azure, takie jak NetScaler, są używane do zapobiegania atakom rozproszonej usługi typu "odmowa usługi" (DDoS) i ochrony integralności usług AZURE DNS.

Serwery DNS platformy Azure znajdują się w wielu obiektach centrum danych. Implementacja usługi Azure DNS zawiera hierarchię pomocniczych i podstawowych serwerów DNS do publicznego rozpoznawania nazw domen klientów platformy Azure. Nazwy domen zwykle są rozpoznawane na adres CloudApp.net, który zawija adres wirtualnego adresu IP (VIP) dla usługi klienta. Unikatowy dla platformy Azure, adres VIP odpowiadający wewnętrznemu dedykowanemu adresowi IP (DIP) tłumaczenia dzierżawy jest wykonywany przez moduły równoważenia obciążenia firmy Microsoft odpowiedzialne za ten adres VIP.

Platforma Azure jest hostowana w geograficznie rozproszonych centrach danych platformy Azure w Stanach Zjednoczonych i jest oparta na najnowocześniejszych platformach routingu, które implementują niezawodne, skalowalne standardy architektoniczne. Wśród godnych uwagi cech są:

- Wieloprotokołowe przełączanie etykiet (MPLS) oparte na inżynierii ruchu, która zapewnia efektywne wykorzystanie łącza i wdzięku degradacji usługi, jeśli występuje awaria.
- Sieci są implementowane z "need plus one" (N + 1) architektury nadmiarowości lub lepiej.
- Zewnętrznie centra danych są obsługiwane przez dedykowane obwody sieciowe o dużej przepustowości, które nadmiarowo łączą właściwości z ponad 1200 dostawcami usług internetowych na całym świecie w wielu punktach komunikacji równorzędnej. To połączenie zapewnia ponad 2000 gigabajtów na sekundę (GBps) pojemności krawędzi.

Ponieważ firma Microsoft jest właścicielem własnych obwodów sieciowych między centrami danych, te atrybuty pomagają platformie Azure osiągnąć 99,9+ procent dostępności sieci bez konieczności tradycyjnych dostawców usług internetowych innych firm.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Połączenie z siecią produkcyjną i skojarzonymi zaporami
Zasady przepływu ruchu internetowego platformy Azure kierują ruch do sieci produkcyjnej platformy Azure, która znajduje się w najbliższym regionalnym centrum danych w Stanach Zjednoczonych. Ponieważ produkcyjne centra danych platformy Azure zachowują spójną architekturę sieci i sprzęt, następujący opis przepływu ruchu ma spójne zastosowanie do wszystkich centrów danych.

Po przekierowaniu ruchu internetowego na platformie Azure do najbliższego centrum danych nawiązywać połączenie z routerami dostępu. Te routery dostępu służą do izolowania ruchu między węzłami platformy Azure i maszyn wirtualnych z wystąpieniami klienta. Urządzenia infrastruktury sieciowej w lokalizacjach dostępu i krawędzi są punktami granicznymi, w których stosowane są filtry przychodzące i wychodzące. Routery te są konfigurowane za pośrednictwem warstwowej listy kontroli dostępu (ACL) w celu filtrowania niechcianego ruchu sieciowego i stosowania limitów natężenia ruchu, jeśli to konieczne. Ruch dozwolony przez listy ACL jest kierowany do modułów równoważenia obciążenia. Routery dystrybucyjne są przeznaczone do zezwalania tylko na adresy IP zatwierdzone przez firmę Microsoft, zapewniania ochrony przed fałszowaniem i ustanawiania połączeń TCP korzystających z list ACL.

Zewnętrzne urządzenia równoważące obciążenie znajdują się za routerami dostępu, aby wykonać translację adresów sieciowych (NAT) z adresów IP z routingiem internetowym do wewnętrznych adresów IP platformy Azure. Urządzenia kierują również pakiety do prawidłowych wewnętrznych adresów IP i portów produkcyjnych i działają jako mechanizm ochrony, aby ograniczyć udostępnianie wewnętrznej przestrzeni adresowej sieci produkcyjnej.

Domyślnie firma Microsoft wymusza zabezpieczenie protokołu HTTPS (Hypertext Transfer Protocol Secure) dla całego ruchu przesyłany do przeglądarek internetowych klientów, w tym logowania i całego ruchu. Zastosowanie protokołu TLS v1.2 umożliwia bezpieczny tunel dla przepływu ruchu. Listy ACL na routerach dostępu i podstawowych upewnij się, że źródło ruchu jest zgodne z oczekiwaniami.

Ważnym rozróżnieniem w tej architekturze, gdy jest porównywana z tradycyjną architekturą zabezpieczeń, jest to, że nie ma dedykowanych zapór sprzętowych, wyspecjalizowanych urządzeń do wykrywania włamań lub zapobiegania lub innych urządzeń zabezpieczających, które są zwykle oczekuje się przed nawiązaniem połączeń do środowiska produkcyjnego platformy Azure. Klienci zwykle oczekują tych urządzeń zapory sprzętowej w sieci platformy Azure; jednak żaden z nich nie są zatrudnieni na platformie Azure. Prawie wyłącznie te funkcje zabezpieczeń są wbudowane w oprogramowanie, które uruchamia środowisko platformy Azure, aby zapewnić niezawodne, wielowarstwowe mechanizmy zabezpieczeń, w tym możliwości zapory. Ponadto zakres granic i skojarzone rozrost krytycznych urządzeń zabezpieczających jest łatwiejsze do zarządzania i spisu, jak pokazano na poprzedniej ilustracji, ponieważ jest zarządzany przez oprogramowanie, które jest uruchomione na platformie Azure.

## <a name="core-security-and-firewall-features"></a>Podstawowe funkcje zabezpieczeń i zapory
Platforma Azure implementuje niezawodne funkcje zabezpieczeń oprogramowania i zapory na różnych poziomach, aby wymusić funkcje zabezpieczeń, które są zwykle oczekiwane w tradycyjnym środowisku w celu ochrony podstawowych granic autoryzacji zabezpieczeń.

### <a name="azure-security-features"></a>Funkcje zabezpieczeń platformy Azure
Platforma Azure implementuje zapory programowe oparte na hostach wewnątrz sieci produkcyjnej. Kilka podstawowych zabezpieczeń i funkcji zapory znajdują się w podstawowym środowisku platformy Azure. Te funkcje zabezpieczeń odzwierciedlają strategię obrony w środowisku platformy Azure. Dane klientów na platformie Azure są chronione przez następujące zapory:

**Zapora hipernadzorcy (filtr pakietów):** Ta zapora jest zaimplementowana w funkcji hypervisor i konfigurowana przez agenta kontrolera sieci szkieletowej (FC). Ta zapora chroni dzierżawę, która działa wewnątrz maszyny Wirtualnej przed nieautoryzowanym dostępem. Domyślnie po utworzeniu maszyny Wirtualnej cały ruch jest zablokowany, a następnie agent FC dodaje reguły i wyjątki w filtrze, aby umożliwić autoryzowany ruch.

W tym miejscu zaprogramowane są dwie kategorie reguł:

- **Reguły konfiguracji komputera lub infrastruktury**: Domyślnie cała komunikacja jest zablokowana. Istnieją wyjątki, które umożliwiają maszynie wirtualnej wysyłanie i odbieranie informacji o protokole DHCP (Dynamic Host Configuration Protocol) i dns oraz wysyłanie ruchu do "publicznego" internetu wychodzącego do innych maszyn wirtualnych w klastrze FC i serwerze aktywacji systemu operacyjnego. Ponieważ lista dozwolonych maszyn wirtualnych wychodzących miejsc docelowych nie zawiera podsieci routera platformy Azure i innych właściwości firmy Microsoft, reguły działają jako warstwa ochrony dla nich.
- **Reguły plików konfiguracji roli:** Definiuje przychodzące listy ACL na podstawie modelu usługi dzierżawców. Na przykład jeśli dzierżawca ma frontomatu sieci web na porcie 80 na określonej maszynie wirtualnej, port 80 jest otwarty dla wszystkich adresów IP. Jeśli maszyna wirtualna ma rolę procesu roboczego uruchomionej, rola procesu roboczego jest otwierana tylko dla maszyny Wirtualnej w ramach tej samej dzierżawy.

**Natywna zapora hosta:** Usługa Azure Service Fabric i usługa Azure Storage są uruchamiane na natywnym systemie operacyjnym, który nie ma funkcji hypervisor i dlatego Zapora systemu Windows jest skonfigurowana z dwoma poprzednimi zestawami reguł.

**Zapora hosta:** Zapora hosta chroni partycję hosta, która uruchamia hipernadzorcę. Reguły są zaprogramowane tak, aby zezwalać tylko fc i jump pola rozmawiać z partycji hosta na określonym porcie. Inne wyjątki to zezwalanie na odpowiedzi DHCP i odpowiedzi DNS. Platforma Azure używa pliku konfiguracji komputera, który zawiera szablon reguł zapory dla partycji hosta. Istnieje również wyjątek zapory hosta, który umożliwia maszynom wirtualnym komunikowanie się ze składnikami hosta, serwerem przewodów i serwerem metadanych za pośrednictwem określonych protokołów/portów.

**Zapora gościa:** Element Zapory systemu Windows systemu operacyjnego gościa, który jest konfigurowany przez klientów na maszynach wirtualnych i magazynie klienta.

Dodatkowe funkcje zabezpieczeń, które są wbudowane w możliwości platformy Azure obejmują:

- Składniki infrastruktury, które są przypisane adresy IP, które są z DIPs. Osoba atakująca w Internecie nie może skierować ruchu na te adresy, ponieważ nie dotrze do firmy Microsoft. Routery bramy internetowej filtrują pakiety, które są adresowane wyłącznie do adresów wewnętrznych, więc nie wchodzą do sieci produkcyjnej. Jedynymi składnikami, które akceptują ruch kierowany do adresów VIP, są moduły równoważenia obciążenia.
- Zapory, które są implementowane we wszystkich węzłach wewnętrznych mają trzy podstawowe zagadnienia architektury zabezpieczeń dla danego scenariusza:

   - Zapory są umieszczane za modułem równoważenia obciążenia i akceptują pakiety z dowolnego miejsca. Pakiety te mają być narażone zewnętrznie i odpowiadają otwartym portom w tradycyjnej zaporze obwodowej.
   - Zapory akceptują pakiety tylko z ograniczonego zestawu adresów. Ta uwaga jest częścią defensywnej strategii pogłębionej przeciwko atakom DDoS. Takie połączenia są uwierzytelnione kryptograficznie.
   - Zapory są dostępne tylko z wybranych węzłów wewnętrznych. Akceptują pakiety tylko z wyliczonej listy źródłowych adresów IP, z których wszystkie są DIP w sieci platformy Azure. Na przykład atak na sieć firmową może kierować żądania do tych adresów, ale ataki zostaną zablokowane, chyba że adres źródłowy pakietu był jeden z listy wyliczane w sieci platformy Azure.
     - Router dostępu na obwodzie blokuje pakiety wychodzące, które są adresowane do adresu znajdującego się wewnątrz sieci Azure ze względu na skonfigurowane trasy statyczne.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure, zobacz:

- [Obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacyjnego platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Funkcje zabezpieczeń usługi Azure SQL Database](infrastructure-sql.md)
- [Operacje produkcyjne i zarządzanie platformą Azure](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](protection-customer-data.md)
