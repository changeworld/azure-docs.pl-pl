---
title: Sieć platformy Azure środowiska produkcyjnego
description: Ten artykuł zawiera ogólny opis sieci platformy Azure środowiska produkcyjnego.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: afae7cc6390ea4cd8c18c687e9d99400c8da9da4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611357"
---
# <a name="the-azure-production-network"></a>Sieć platformy Azure środowiska produkcyjnego
Użytkownicy sieci platformy Azure środowiska produkcyjnego obejmują zarówno zewnętrznych klientów, którzy dostęp do własnych aplikacji platformy Azure i personelu wewnętrznej pomocy technicznej platformy Azure, którzy zarządzają produkcyjnego środowiska sieciowego. W tym artykule omówiono metody dostępu zabezpieczeń i mechanizmy ochrony w celu nawiązania połączenia z siecią platformy Azure środowiska produkcyjnego.

## <a name="internet-routing-and-fault-tolerance"></a>Internetowego routingu i odporności na uszkodzenia
Globalnie nadmiarowy wewnętrzne i zewnętrzne usługi nazw domen (DNS) Azure infrastruktury, w połączeniu z wielu podstawowych i pomocniczych DNS serwera klastrów, zapewnia odporność na uszkodzenia. W tym samym czasie zabezpieczenia dodatkowe sieci platformy Azure, takie jak NetScaler, są używane do zapobiegania rozproszonych atakom typu odmowa usługi (DDoS) i chronią integralność usługi DNS platformy Azure.

Serwery usługi Azure DNS znajdują się w wielu lokalizacjach centrum danych. Wdrożenia usługi Azure DNS zawiera hierarchię dodatkowej i podstawowego serwera DNS, aby publicznie rozpoznawanie nazw domen klientów platformy Azure. Nazwy domen zwykle są rozwiązywane do adresu CloudApp.net, który otacza wirtualnego adresu IP (VIP) dla usługi klienta. Unikatowa na platformie Azure, adres VIP, umożliwiająca wewnętrznego dedykowany adres IP (DIP) tłumaczenia dzierżawy odbywa się przez usługi równoważenia obciążenia firmy Microsoft, które są odpowiedzialne za ten adres VIP.

Azure znajduje się w rozproszonych geograficznie centrach danych platformy Azure na terenie Stanów Zjednoczonych i jest on oparty na platformach routingu stanu grafiki, które implementują niezawodnych, skalowalnych ze standardami. Wśród zauważalne cechy są:

- Inżynieria wieloprotokołowych ruchu opartego na protokole MPLS przełączanie etykieta zawiera łącze efektywne wykorzystanie i łagodne pogorszenia się usług, jeśli nastąpi awaria.
- Sieci są implementowane za pomocą "potrzebę plus 1" (N + 1) architektury nadmiarowości lub lepszej.
- Zewnętrznie centra danych są obsługiwane przez dedykowany sieci o wysokiej przepustowości obwody nadmiarowo skupionej właściwości powyżej 1200 usług dwóch usługodawców internetowych globalnie w wielu punktach komunikacji równorzędnej. To połączenie zawiera ponad 2000 GB na sekundę (GB/s) pojemności krawędzi.

Ponieważ firma Microsoft posiada własny obwodów sieci między centrami danych, te atrybuty pomagają osiągnąć + 99,9 procent dostępności sieci bez potrzeby tradycyjnych usługodawców internetowych innych firm oferty dla platformy Azure.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Połączenie z sieci produkcyjnej i skojarzone zapory
Zasady przepływu ruchu internetowej sieci platformy Azure kieruje ruch do sieci platformy Azure środowiska produkcyjnego, który znajduje się w najbliższej regionalnym centrum danych na terenie Stanów Zjednoczonych. Ponieważ centrów danych platformy Azure środowiska produkcyjnego Obsługa architektury spójne sieci i sprzętu, opis przepływu ruchu, który następuje po jest stosowana do wszystkich centrów danych.

Po ruch internetowy dla platformy Azure jest kierowany do najbliższego centrum danych, połączenie jest nawiązywane z routerami dostępu. Te routery dostępu służą do izolacji ruchu między węzły na platformie Azure i tworzone przez klientów maszyn wirtualnych. Urządzenia infrastruktury sieci w lokalizacjach dostępu i urządzeniami brzegowymi są punkty granic, w których są stosowane filtry ruchu przychodzącego i wychodzącego. Te routery skonfigurowano za pomocą listy warstwowego kontroli dostępu (ACL) do filtrowania ruchu sieciowego niechciane i zastosować limity szybkości ruch, jeśli to konieczne. Ruch jest dozwolony przez listy kontroli dostępu jest kierowany do usługi równoważenia obciążenia. Routery dystrybucji są przeznaczone do Zezwalaj tylko adresy IP zatwierdzoną przez firmę Microsoft, zapewnienia ochrony przed fałszowaniem i nawiązywania połączeń TCP, korzystających z listy ACL.

Zewnętrzne Równoważenie obciążenia sieciowego urządzenia znajdują się za routerów dostępu do wykonywania translatora adresów sieciowych (NAT) z obsługą routingu internetowego adresów IP do systemu Azure wewnętrznych adresów IP. Urządzenia także kierowania pakietów do środowiska produkcyjnego prawidłowe, których wewnętrznych adresów IP i portów i mogą działać jako mechanizm ochrony, aby ograniczyć udostępnianie przestrzeń adresową sieci wewnętrznej w środowisku produkcyjnym.

Domyślnie firma Microsoft wymusza Hypertext Transfer Protocol Secure (HTTPS) dla całego ruchu, który jest wysyłany do przeglądarki sieci web klientów, w tym logowania i cały ruch po tej dacie. Korzystanie z protokołu TLS 1.2 umożliwia bezpieczny tunel dla ruchu przepływ. Listy ACL na routerach dostępu oraz podstawowe upewnij się, że źródło ruchu zgodne z niż oczekiwana.

To ważna różnica w ramach tej architektury porównaniu z architektury tradycyjne zabezpieczenia, zakłada, że zapory nie dedykowanego sprzętu, wykrywanie włamań specjalne lub zapobiegania urządzeń lub inne urządzenia zabezpieczeń, które zazwyczaj są Oczekiwano, zanim połączenia zostały wprowadzone do środowiska platformy Azure środowiska produkcyjnego. Klienci oczekują zazwyczaj tych urządzeń zapory w sieci platformy Azure; jednak żadne zatrudnionych w obrębie platformy Azure. Niemal wyłącznie tych zabezpieczeń wbudowanych funkcji oprogramowania uruchomionego środowiska platformy Azure, aby zapewnić mechanizmów zabezpieczeń niezawodne, wielowarstwowych, w tym możliwości zapory. Ponadto zakres granicy i skojarzone Rozrost krytycznych urządzeń jest łatwiejsze do zarządzania i spisu, jak pokazano na poprzedniej ilustracji, ponieważ jest ona zarządzana przez oprogramowanie, które jest uruchomionych na platformie Azure.

## <a name="core-security-and-firewall-features"></a>Podstawowe funkcje zabezpieczeń i zapory
Azure implementuje niezawodne oprogramowania zabezpieczeniami i funkcjami zapory na różnych poziomach w celu wymuszania funkcji zabezpieczeń, które zwykle powinny w tradycyjnym środowisku ochrona granic zabezpieczeń autoryzacji podstawowej.

### <a name="azure-security-features"></a>Funkcje zabezpieczeń platformy Azure
Azure implementuje zapory oprogramowania opartych na hoście w sieci produkcyjnej. Kilka podstawowych zabezpieczeń i funkcjami zapory znajdują się w podstawowej środowiska platformy Azure. Te funkcje zabezpieczeń odzwierciedlają strategii ochronę w głębi w środowisku platformy Azure. Dane klientów na platformie Azure są chronione przez następujące zapory:

**Zapory funkcji hypervisor (filtr pakietów)**: Ta zapora została zaimplementowana w funkcji hypervisor i skonfigurowana przez agenta kontrolera (FC) sieci szkieletowej. Ta Zapora chroni dzierżawy, który działa wewnątrz maszyny Wirtualnej przed nieautoryzowanym dostępem. Domyślnie po utworzeniu maszyny Wirtualnej cały ruch jest blokowany, a następnie FC agent dodaje regułami i wyjątkami w filtrze zezwalające na ruch autoryzowany.

Dwie kategorie reguł są w tym miejscu programowane:

- **Reguły konfiguracji lub infrastruktury usługi Machine**: Domyślnie cała komunikacja jest blokowana. Istnieją wyjątki, które Zezwalaj na maszynę Wirtualną w celu wysyłania i odbierania wiadomości protokołu dynamicznej konfiguracji hosta (DHCP, Dynamic Host Configuration Protocol) i informacje dotyczące systemu DNS i wysyłania ruchu do "publicznej" sieci internet, ruch wychodzący do innych maszyn wirtualnych w klastrze FC oraz do serwera aktywacji systemu operacyjnego. Ponieważ maszyn wirtualnych dozwolony wychodzącego liście miejsc docelowych nie obejmuje podsieci routera platformy Azure i innych obszarów firmy Microsoft, reguły działają jako warstwę ochrony dla nich.
- **Reguły pliku konfiguracji roli**: Definiuje listy ACL ruchu przychodzącego oparciu o model usług dzierżawców. Na przykład jeśli dzierżawca ma fronton sieci web na porcie 80 określonej maszyny wirtualnej, port 80 jest otwarty dla wszystkich adresów IP. Jeśli maszyna wirtualna ma uruchomione roli proces roboczy, roli procesu roboczego zostanie otwarty tylko dla maszyn wirtualnych w ramach tej samej dzierżawy.

**Zapora hosta macierzystego**: Usługa Azure Service Fabric i usługi Azure Storage, uruchom w macierzystym OS ma nie funkcji hypervisor i w związku z tym, Zapora Windows jest skonfigurowana z poprzednich dwóch zestawów reguł.

**Zapora hosta**: Zapora hosta zapewnia ochronę z partycją hosta, w którym jest uruchomiona funkcja hypervisor. Aby zezwolić tylko FC i przejdź do pola, aby komunikować się z partycją hosta na określonym porcie programowane reguły. Pozostałe wyjątki znajdują, aby zezwolić na odpowiedzi serwerów DHCP i DNS. Platforma Azure używa pliku konfiguracji komputera, który zawiera szablon reguł zapory dla partycji hosta. Wyjątek zapory hostów istnieje, która umożliwia maszynom wirtualnym komunikowanie się hostowane będą składniki o komunikacji sieciowej serwera i serwera metadanych, przy użyciu określonych portów.

**Zapora gościa**: Zapora Windows fragment gościa systemu operacyjnego, który jest konfigurowalne przez klientów na maszynach wirtualnych klientów i magazynu.

Dodatkowe funkcje zabezpieczeń, które są wbudowane w funkcji platformy Azure obejmują:

- Składniki infrastruktury, które są przypisywane adresy IP, które pochodzą z spadku. Osoba atakująca w Internecie nie adresów ruchu do tych adresów, ponieważ nie będzie on uzyskać dostępu firmy Microsoft. Routery bramy Internet filtr pakietów, które zostały rozwiązane wyłącznie do wewnętrznych adresów, dzięki czemu użytkownik może nie podał produkcyjnego środowiska sieciowego. Tylko składniki, które akceptowania ruchu, który jest kierowany do adresów VIP są moduły równoważenia obciążenia.
- Zapór, które są implementowane w wszystkie węzły wewnętrznego ma trzy podstawowe architektury wymagania dotyczące zabezpieczeń dowolnego danego scenariusza:

   - Zapory są umieszczane za modułem równoważenia obciążenia i akceptuje pakietów z dowolnego miejsca. Te pakiety są przeznaczone na zewnątrz ujawnianie i otwieranie portów w zaporze obwodowej tradycyjnych będzie odpowiadać.
   - Zapory Akceptuj pakietów tylko z określonych adresów. To zagadnienie to część strategii szczegółowe obrony przed atakami DDoS. Takie połączenia są kryptograficznie uwierzytelniony.
   - Zapory są dostępne tylko z Wybierz węzły wewnętrznego. Akceptują pakietów tylko od wyliczany listę źródłowych adresów IP, które są spadku w ramach sieci platformy Azure. Na przykład ataków w sieci firmowej można kierować żądania do tych adresów, ale przed atakami zostałby zablokowany, chyba, że adres źródłowy pakiet był na liście wyliczany sieci platformy Azure.
     - Router dostępu w sieci obwodowej blokuje pakietów wychodzących, które są wysyłane na adres który znajduje się wewnątrz sieci platformy Azure z powodu jego skonfigurowane trasy statyczne.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Funkcje zabezpieczeń w usłudze Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](azure-protection-of-customer-data.md)
