---
title: Sieci Azure środowiska produkcyjnego
description: Ten artykuł zawiera ogólny opis produkcyjnego środowiska sieciowego Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 5c0bfae35464e73278a1efd9c04a03123bb9018a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102488"
---
# <a name="azure-production-network"></a>Sieci Azure środowiska produkcyjnego
Użytkownicy sieci Azure środowiska produkcyjnego obejmują zewnętrznym klientom dostęp do własnych aplikacji firmy Microsoft Azure, a także wewnętrzny Microsoft Azure pomocy technicznej, które zarządzają produkcyjnego środowiska sieciowego. W tym artykule omówiono zabezpieczeń metody dostępu i ochrony mechanizmów nawiązywanie połączeń z siecią Azure środowiska produkcyjnego.

## <a name="internet-routing-and-fault-tolerance"></a>Internet Routing i odporności na uszkodzenia
Globalnie nadmiarowe wewnętrznych i zewnętrznych infrastruktury usługi nazwa domeny firmy Microsoft Azure (WADNS) wraz z wielu klastrów o serwera usługi nazw domen (DNS, Domain Name System) podstawowych i pomocniczych zapewnienia odporności na uszkodzenia podczas dodatkowe sieci Microsoft Azure zabezpieczeń formanty, takie jak NetScaler są używane do zapobiegać atakom Distributed Denial of Service (DDoS) i ochronę integralności usługi Microsoft Azure DNS.

Serwery WADNS znajdują się w wielu lokalizacjach centrum danych. Implementacja WADNS zawiera hierarchii pomocniczy/podstawowych serwerów DNS w celu rozpoznania publicznie nazwy domeny klienta usługi Azure. Nazwy domen zwykle rozpoznać adres CloudApp.net opakowuje adres wirtualnego adresu IP (VIP) dla usługi klienta. Unikatowa na platformie Azure, VIP odpowiadający wewnętrzny adres dedykowany adres IP (DIP) tłumaczenia dzierżawy odbywa się przez usługi równoważenia obciążenia firmy Microsoft, które są odpowiedzialne za tego adresu VIP.

Azure znajduje się w centrach danych platformy Azure geograficznie rozproszonych w Stanach Zjednoczonych i jest oparty na platformach routingu stanu grafiki standardy architektury niezawodne i skalowalne wdrażanie. Niektóre ważne funkcje to:

- Wieloprotokołowych przełączania etykiety (MPLS) na podstawie ruchu zespołu inżynieryjnego, zapewniając wykorzystania łącza wydajne i bezpieczne pogorszenia się usług w przypadku awarii
- Sieci są wykonywane z "potrzeby plus 1" (N + 1) nadmiarowość architektur lub lepszej.
- Zewnętrznie centrów danych są obsługiwane przez obwodów dedykowanym, wysokiej przepustowości sieci, które nadmiarowo Uzyskuj dostęp do właściwości ponad 1200 usługodawcy internetowi globalnie w wielu punktach komunikacji równorzędnej, zapewniając ponad 2000 GB na sekundę (GB/s) z pojemność krawędzi.

Jak Microsoft ma własną obwody sieci między centrami danych, te atrybuty pomocy oferty Azure, osiągnięcia 99.9 + % dostępności sieci bez konieczności ustanawiania tradycyjnego usługodawców internetowych innych firm.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Połączenie do środowiska produkcyjnego i skojarzone zapory
Sieć platformy Azure zasad przepływu ruchu internetowego kieruje ruch do sieci produkcyjnej Azure znajduje się w najbliższej regionalnych centrów danych w Stanach Zjednoczonych. Ponieważ centrach danych produkcyjnych Azure Obsługa architektury spójne sieci i sprzętu, poniżej przepływ ruchu opis jest stosowana do wszystkich centrów danych.

Gdy ruch internetowy dla platformy Azure jest kierowany do najbliższego centrum danych, routery dostępu jest nawiązywane połączenie. Te routery dostępu służą do izolacji ruchu między węzłami Azure a wystąpienia klienta maszyn wirtualnych. Urządzenia infrastruktury sieci w lokalizacjach dostępu i krawędzi są punkty granic, gdy transfer danych przychodzących i/lub wyjście filtry są stosowane. Te routery skonfigurowano za pomocą warstwowych listy kontroli dostępu do filtrowania ruchu sieciowego niechciane i zastosowanie limitów szybkości ruchu, jeśli to konieczne. Ruch, który jest dozwolony przez listy kontroli dostępu jest kierowany do usługi równoważenia obciążenia. Routery dystrybucji mają Zezwalaj tylko adresy IP zatwierdzone firmy Microsoft, podaj ochrony przed fałszowaniem i ustanowić połączenia TCP za pomocą listy kontroli dostępu.

Równoważenie obciążenia sieciowego urządzeń zewnętrznych znajdują się za routery dostępu, aby wykonać translacji adresów sieciowych (NAT) z obsługą routingu internetowego adresów IP do platformy Azure wewnętrznych adresów IP. One również kierowania pakietów do prawidłowe adresy IP wewnętrznego produkcyjnych i portów i działają jako mechanizm ochrony, aby ograniczyć udostępnianie przestrzeni adresowej używanej sieci wewnętrznej.

Domyślnie program Microsoft wymusza Hypertext Transfer Protocol Secure (HTTPS) dla całego ruchu przesyłane do przeglądarki, w tym Zaloguj się w sieci web klienta i cały ruch później. Korzystanie z protokołu TLS 1.2 Włącza bezpieczny tunel dla ruchu przepływ. Listy kontroli dostępu na routerach dostępu i podstawowe upewnij się, że źródło ruch jest zgodny z oczekiwano.

Jest to ważna różnica w ramach tej architektury w porównaniu do architektury tradycyjne zabezpieczenia czy zapory nie dedykowanego sprzętu, urządzeń wykrywania/zapobiegania specjalne nieautoryzowanego dostępu ani innych urządzeń zabezpieczeń oczekuje przed połączenia są nawiązywane do środowiska produkcyjnego Azure. Klienci oczekują zwykle te urządzenia zapory w sieci platformy Azure; jednak żaden nie jest stosowane w obrębie platformy Azure. Prawie wyłącznie te funkcje zabezpieczeń są wbudowane w oprogramowanie uruchomione w środowisku Azure zapewniają mechanizmy skuteczną ochronę wielowarstwowy, włącznie z funkcjami zapory. Ponadto zakres granicy i skojarzone przetwarzania krytycznych urządzeń jest łatwiejsze do zarządzania i spisu, jak pokazano na ilustracji powyżej, ponieważ nie jest zarządzany przez oprogramowanie systemem Azure.

## <a name="core-security-and-firewall-features"></a>Podstawowe funkcje zabezpieczeń i zapory
Azure implementuje oprogramowania niezawodne bezpieczeństwo i ulepszone funkcje zapory na różnych poziomach, aby wymusić zwykle oczekiwać w środowisku tradycyjnych ochrony granic zabezpieczeń autoryzacji podstawowe funkcje zabezpieczeń.

### <a name="azure-security-features"></a>Funkcje zabezpieczeń platformy Azure
Azure implementuje zapór programowych oparta na hoście w sieci produkcyjnej. Kilka podstawowych zabezpieczeń i funkcjami zapory znajdują się wewnątrz rdzenia środowiska platformy Azure. Te funkcje zabezpieczeń odzwierciedlają strategii obrony zabezpieczeń w środowisku Azure. Dane klienta na platformie Microsoft Azure jest chroniony przez następujące zapory:

**Funkcja hypervisor zapory (filtr pakietów)**: Zapora jest zaimplementowana w funkcji Hypervisor i skonfigurowany przez agenta FC. Zapora chroni dzierżawy uruchomiony w ramach maszyny Wirtualnej przed nieautoryzowanym dostępem. Domyślnie po utworzeniu maszyny Wirtualnej cały ruch jest zablokowany, a następnie FC agent dodaje reguły/wyjątki w filtrze zezwalająca na ruch autoryzowanych.

Istnieją dwie kategorie reguł, które są w tym miejscu programowane:

- Maszyny Config lub reguły infrastruktury: Domyślnie cała komunikacja jest zablokowany. Istnieją wyjątki, aby umożliwić Maszynę wirtualną do wysyłania i otrzymywania wiadomości protokołu dynamicznej konfiguracji hosta (DHCP), informacje dotyczące systemu DNS, Wyślij ruch do Internetu "public", ruch wychodzący do innych maszyn wirtualnych w ramach klastra FC i aktywacji systemu operacyjnego serwera. Ponieważ maszyn wirtualnych dozwolona lista wychodzące miejsc docelowych nie ma podsieci router Microsoft Azure i inne właściwości firmy Microsoft, reguły pełnienie warstwę ochrony dla nich.
- Plik konfiguracji roli: Definiuje przychodzących listy ACL oparte na modelu usługi dzierżawców. Na przykład jeśli dzierżawca ma frontonu sieci web na porcie 80 w przypadku niektórych maszyny Wirtualnej, a następnie port 80 jest otwarty dla wszystkich adresów IP. Jeśli maszyna wirtualna ma uruchamiania roli proces roboczy, roli proces roboczy jest otwarty tylko do maszyny Wirtualnej w ramach tej samej dzierżawy.

**Natywny zaporę hosta**: Microsoft Azure w sieci szkieletowej i magazynu uruchomiony na natywnej systemu operacyjnego, którego nie funkcji Hypervisor i dlatego Zapora systemu Windows jest skonfigurowany z powyższych dwóch zestawów reguł.

**Host zapory**: Zapora hosta chroni z partycją hosta, w którym jest uruchomiona funkcja Hypervisor. Reguły są zaprogramowane w taki sposób, aby zezwolić tylko FC i przejść pola, aby komunikował się z partycją hosta na określonym porcie. Inne wyjątki zezwalają na odpowiedzi serwerów DHCP i DNS. Platforma Azure korzysta w pliku konfiguracji komputera, który zawiera szablon reguły zapory dla hostów partition. Istnieje również wyjątek zapory hostów umożliwiający maszyny wirtualne do komunikowania się do hosta składniki serwera podczas transmisji & serwera metadanych za pomocą określonych portów.

**Gość zapory**: Zapora systemu Windows część systemu operacyjnego gościa, które można skonfigurować przez klienta na klienta maszyn wirtualnych i magazynu.

Dodatkowe funkcje zabezpieczeń wbudowane możliwości platformy Azure:

- Składniki infrastruktury są przypisywane adresy IP, które są z dedykowanych adresów IP (DIP). Osoba atakująca w Internecie nie adresów ruch do tych adresów, ponieważ nie docierają firmy Microsoft. Routery bramą internetową filtrować pakiety zaadresowane wyłącznie wewnętrzne adresy, więc nie będą oni wprowadzać produkcyjnego środowiska sieciowego. Tylko składniki, które akceptować ruch kierowany do adresów VIP są usługi równoważenia obciążenia.
- Zapory wdrożone na wszystkich węzłach wewnętrzny ma trzy podstawowej architektury wymagania dotyczące zabezpieczeń żadnych danego scenariusza:

   - One są umieszczone za usługą równoważenia obciążenia (LB) i Zaakceptuj pakiety z dowolnego miejsca. Te powinny być widoczne zewnętrznie i odpowiada otwartych portów w zaporze obwodowej tradycyjnych.
   - Akceptować tylko pakiety z określonych adresów. Jest to część strategii szczegółowe obrony przed "odmowa usługi". Takie połączenia kryptograficznie są uwierzytelniane.
   - Zapory można uzyskać tylko z węzłów wewnętrznej wybierz, w tym przypadku akceptacji pakietów tylko od wyliczany listę źródłowych adresów IP, które są DIP w ramach sieci platformy Azure. Na przykład w sieci firmowej atak może kierować żądań na te adresy, ale ich zostałby zablokowany, chyba że adres źródłowy pakiet był na liście wyliczenia sieć platformy Azure.
   - Router dostępu w obwodzie blokuje pakietów wychodzących skierowane do adresu, który znajduje się wewnątrz sieci platformy Azure z powodu jego skonfigurowanych tras statycznych.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Składniki systemu Azure informacji i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft](azure-infrastructure-sql.md)
- [Operacje Azure środowiska produkcyjnego i zarządzania](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klienta na platformie Azure](azure-protection-of-customer-data.md)
