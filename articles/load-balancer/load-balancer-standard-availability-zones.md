---
title: Azure usługa Load Balancer w warstwie Standardowa i Strefy dostępności
titleSuffix: Azure Load Balancer
description: Korzystając z tej ścieżki szkoleniowej, Rozpocznij pracę z usługą Azure usługa Load Balancer w warstwie Standardowa i Strefy dostępności.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 63a7db6e7078df978f47a6d53ea82df83c22c800
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076982"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Usługa Load Balancer w warstwie Standardowa i strefy dostępności

Usługa Azure usługa Load Balancer w warstwie Standardowa obsługuje scenariusze [stref dostępności](../availability-zones/az-overview.md) . Aby zoptymalizować dostępność w kompleksowym scenariuszu, można użyć usługa Load Balancer w warstwie Standardowa, przez wyrównanie zasobów do stref i dystrybuowanie ich między strefami.  Przejrzyj [strefy dostępności](../availability-zones/az-overview.md) , aby uzyskać wskazówki dotyczące stref dostępności, które obecnie obsługują strefy dostępności, oraz inne powiązane koncepcje i produkty. strefy dostępności w połączeniu z usługa Load Balancer w warstwie Standardowa są rozległych i elastycznym zestawem funkcji, który może tworzyć wiele różnych scenariuszy.  Zapoznaj się z tym dokumentem, aby poznać te [koncepcje](#concepts) i podstawowe [wskazówki dotyczące projektowania](#design).

>[!IMPORTANT]
>Przejrzyj [strefy dostępności](../availability-zones/az-overview.md) Tematy pokrewne, w tym informacje specyficzne dla regionu.

## <a name="concepts"></a>Koncepcje Strefy dostępności zastosowane do Load Balancer

Nie istnieje bezpośredni związek między zasobami Load Balancer i rzeczywistą infrastrukturą; utworzenie Load Balancer nie powoduje utworzenia wystąpienia. Zasoby Load Balancer są obiektami, w których można wypróbować, w jaki sposób platforma Azure powinna programować wbudowaną infrastrukturę z wieloma dzierżawcami w celu osiągnięcia scenariusza, który ma zostać utworzony.  Jest to istotne w kontekście stref dostępności, ponieważ pojedynczy zasób Load Balancer może kontrolować programowanie infrastruktury w wielu strefach dostępności, podczas gdy usługa nadmiarowa strefy jest wyświetlana jako jeden zasób z punktu widzenia klienta.  

Sam zasób Load Balancer jest regionalny i nigdy nie jest strefą.  A sieć wirtualna i podsieci zawsze są regionalne i nigdy nie są zona. Stopień szczegółowości tego, co można skonfigurować, jest ograniczony przez każdą konfigurację frontonu, reguły i definicji puli zaplecza.

W kontekście stref dostępności zachowanie i właściwości reguły Load Balancer są opisane jako strefa nadmiarowa lub strefa.  Strefowo nadmiarowe i strefowo opisują zonality właściwości.  W kontekście Load Balancer strefa nadmiarowa zawsze oznacza, że *wiele stref* i stref oznacza izolację usługi do *pojedynczej strefy*.

Zarówno publiczne, jak i wewnętrzne Load Balancer obsługują scenariusze nadmiarowe i strefowe, a oba mogą kierować ruchem między strefami w razie potrzeby (*równoważenie obciążenia między strefami*). 

### <a name="frontend"></a>Frontonu

Fronton Load Balancer jest konfiguracją adresów IP frontonu odwołującą się do zasobu publicznego adresu IP lub prywatnego adresu IP w podsieci zasobu sieci wirtualnej.  Tworzy punkt końcowy ze zrównoważonym obciążeniem, w którym jest udostępniana usługa.

Zasób Load Balancer może zawierać reguły ze strefą i nadmiarowe strefy frontonu jednocześnie. 

Gdy zasób publicznego adresu IP lub prywatny adres IP jest gwarantowany dla strefy, zonality (lub ich brak) nie jest modyfikowalny.  Jeśli chcesz zmienić lub pominąć zonality publicznej lub prywatnego adresu IP frontonu, należy ponownie utworzyć publiczny adres IP w odpowiedniej strefie.  Strefy dostępności nie zmieniają ograniczeń dla wielu frontonów, przejrzyj [wiele frontonów dla Load Balancer](load-balancer-multivip-overview.md) , aby uzyskać szczegółowe informacje na temat tej możliwości.

#### <a name="zone-redundant-by-default"></a>Strefy nadmiarowe domyślnie

W regionie ze strefami dostępności usługa Load Balancer w warstwie Standardowa frontonu jest domyślnie strefowo nadmiarowy.  Strefa nadmiarowa oznacza, że wszystkie przepływy ruchu przychodzącego lub wychodzącego są obsługiwane przez wiele stref dostępności w regionie jednocześnie przy użyciu jednego adresu IP. Schematy nadmiarowości DNS nie są wymagane. Pojedynczy adres IP frontonu może przetrwać awarię strefy i może być używany do uzyskiwania dostępu do wszystkich członków puli zaplecza (bez wpływu), niezależnie od strefy. Co najmniej jedna strefa dostępności może zakończyć się niepowodzeniem, a ścieżka danych przeżyje, dopóki jedna strefa w regionie pozostanie w dobrej kondycji. Pojedynczy adres IP frontonu jest obsługiwany jednocześnie przez wiele niezależnych wdrożeń infrastruktury w wielu strefach dostępności.  Nie oznacza to, że hitless ścieżka danych, ale wszystkie ponowne próby lub ponowna próba zostaną wykonane pomyślnie w innych strefach, które nie wpłyną na awarię strefy.   

Poniższy fragment przedstawia sposób definiowania publicznego adresu IP w strefie nadmiarowy publiczny adres IP do użycia z publiczną usługa Load Balancer w warstwie Standardowa. Jeśli używasz istniejących szablonów Menedżer zasobów w konfiguracji, Dodaj sekcję **SKU** do tych szablonów.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Poniższy fragment przedstawia sposób definiowania Strefowo nadmiarowego adresu IP frontonu dla wewnętrznej usługa Load Balancer w warstwie Standardowa. Jeśli używasz istniejących szablonów Menedżer zasobów w konfiguracji, Dodaj sekcję **SKU** do tych szablonów.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Poprzednie fragmenty nie są kompletnymi szablonami, ale mają na celu pokazanie sposobu prezentowania właściwości stref dostępności.  Musisz dołączyć te instrukcje do szablonów.

#### <a name="optional-zone-isolation"></a>Opcjonalna izolacja strefy

Można zdecydować, aby fronton miał zagwarantowaną pojedynczą strefę, zwaną *strefą frontonu*.  Oznacza to, że każdy przepływ przychodzący lub wychodzący jest obsługiwany przez jedną strefę w regionie.  Fronton udostępnia losy z kondycją strefy.  Błędy w ścieżce danych nie mają wpływ na awarie w strefach innych niż to, w którym zostało zagwarantowane. Za pomocą stref frontonów można uwidocznić adres IP dla strefy dostępności.  

Ponadto można korzystać z stref frontonów bezpośrednio dla punktów końcowych ze zrównoważonym obciążeniem w ramach każdej strefy. Można go również użyć do udostępnienia punktów końcowych ze zrównoważonym obciążeniem strefy, aby osobno monitorować każdą strefę.  Lub dla publicznych punktów końcowych można zintegrować je z produktem do równoważenia obciążenia DNS, takiego jak [Traffic Manager](../traffic-manager/traffic-manager-overview.md) , i użyć pojedynczej nazwy DNS. Następnie klient rozwiąże tę nazwę DNS z wieloma adresami IP stref.  

Jeśli chcesz mieszać te koncepcje (strefowo nadmiarowe i zona dla tego samego zaplecza), przejrzyj [wiele frontonów dla Azure Load Balancer](load-balancer-multivip-overview.md).

W przypadku publicznego Load Balancer frontonu należy dodać parametr *strefy* do zasobu publicznego adresu IP, do którego odwołuje się Konfiguracja adresu IP frontonu używana przez odpowiednią regułę.

W przypadku wewnętrznego Load Balancer frontonu Dodaj parametr *strefy* do konfiguracji wewnętrznego adresu IP frontonu Load Balancer. Strefa frontonu powoduje, że Load Balancer zagwarantowania adresu IP w podsieci do określonej strefy.

Poniższy fragment przedstawia sposób definiowania standardowego publicznego adresu IP strefowego w Strefa 1 dostępności. Jeśli używasz istniejących szablonów Menedżer zasobów w konfiguracji, Dodaj sekcję **SKU** do tych szablonów.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Poniższy fragment przedstawia sposób definiowania wewnętrznego usługa Load Balancer w warstwie Standardowa frontonu w Strefa 1 dostępności. Jeśli używasz istniejących szablonów Menedżer zasobów w konfiguracji, Dodaj sekcję **SKU** do tych szablonów. Ponadto Zdefiniuj Właściwość **Zones** w konfiguracji adresu IP frontonu dla zasobu podrzędnego.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Poprzedzające fragmenty nie są kompletnymi szablonami, ale mają na celu pokazanie sposobu prezentowania właściwości stref dostępności.  Musisz dołączyć te instrukcje do szablonów.

### <a name="cross-zone-load-balancing"></a>Równoważenie obciążenia między strefami

Równoważenie obciążenia między strefami umożliwia Load Balancer dostępu do punktu końcowego zaplecza w dowolnej strefie i jest niezależne od frontonu i jego zonality.  Każda reguła równoważenia obciążenia może kierować wystąpieniem zaplecza w dowolnej strefie dostępności lub w wystąpieniach regionalnych.

Należy zachować ostrożność konstruowania scenariusza w sposób, który wyraża koncepcję stref dostępności. Na przykład należy zagwarantowaniu wdrożenia maszyny wirtualnej w ramach jednej strefy lub wielu stref, a także wyrównania zasobów frontonu i stref wewnętrznej bazy danych do tej samej strefy.  W przypadku stref obejmujących wiele dostępności zawierających tylko zasoby strefowe scenariusz będzie działał, ale może nie mieć trybu niejawnego uszkodzenia w odniesieniu do stref dostępności. 

### <a name="backend"></a>Danych

Load Balancer współpracuje z wystąpieniami maszyn wirtualnych.  Mogą to być autonomiczne, zestawy dostępności lub zestawy skalowania maszyn wirtualnych.  Każde wystąpienie maszyny wirtualnej w pojedynczej sieci wirtualnej może być częścią puli zaplecza, niezależnie od tego, czy została ona zagwarantowana do strefy, czy do której zagwarantowana jest strefa.

Jeśli chcesz wyrównać i zagwarantowania frontonu i zaplecza przy użyciu jednej strefy, umieść maszyny wirtualne w tej samej strefie w odpowiedniej puli zaplecza.

Jeśli chcesz zająć się maszynami wirtualnymi w wielu strefach, po prostu umieść maszyny wirtualne z wielu stref w tej samej puli zaplecza.  W przypadku korzystania z zestawów skalowania maszyn wirtualnych można umieścić jeden lub więcej zestawów skalowania maszyn wirtualnych w tej samej puli zaplecza.  Każdy z tych zestawów skalowania maszyn wirtualnych może znajdować się w jednej lub wielu strefach.

### <a name="outbound-connections"></a>Połączenia wychodzące

Te same właściwości strefowo nadmiarowe i zona mają zastosowanie do [połączeń wychodzących](load-balancer-outbound-connections.md).  Strefa nadmiarowy publiczny adres IP używany do połączeń wychodzących jest obsługiwany przez wszystkie strefy. Publiczny adres IP strefy jest obsługiwany tylko przez strefę, w której jest gwarantowana.  Alokacje portów dla ruchu wychodzącego nie są obsługiwane w przypadku awarii strefy, a Twój scenariusz będzie nadal zapewniać łączność wychodzącego połączenia z odruchem w przypadku niepowodzenia strefy.  Może to wymagać transmisja lub dla połączeń, które mają być ponownie nawiązane w scenariuszach nadmiarowych strefy, jeśli przepływ został obsłużony przez strefę, której to dotyczy.  Nie ma to wpływu na przepływy w strefach innych niż wpływające strefy.

Algorytm wstępnego przydzielenia portu dla translatora adresów sieciowych jest taki sam ze strefami dostępności lub bez.

### <a name="health-probes"></a>Sondy kondycji

Istniejące definicje sondy kondycji pozostają w stanie, w jakim się znajdują, bez stref dostępności.  Jednak model kondycji został rozszerzony na poziomie infrastruktury. 

W przypadku korzystania z frontonów strefowo nadmiarowych Load Balancer rozszerza swój wewnętrzny model kondycji, aby niezależnie przesondować możliwości maszyny wirtualnej z każdej strefy dostępności i zamknąć ścieżki między strefami, które mogły zakończyć się niepowodzeniem bez interwencji klienta.  Jeśli dana ścieżka nie jest dostępna z infrastruktury Load Balancer jednej strefy do maszyny wirtualnej w innej strefie, Load Balancer może wykryć i uniknąć tego błędu. Inne strefy, które mogą nawiązać połączenie z tą maszyną wirtualną, mogą nadal obsłużyć maszynę wirtualną z odpowiednich frontonów.  W związku z tym możliwe jest, że podczas zdarzeń błędów każda strefa może mieć nieco różne dystrybucje nowych przepływów przy jednoczesnej ochronie ogólnej kondycji usługi kompleksowej.

## <a name="design"></a>Zagadnienia dotyczące projektowania

Load Balancer jest elastycznie elastyczny w kontekście stref dostępności. Można wybrać opcję Wyrównaj do stref lub wybrać opcję nadmiarowa strefy dla każdej reguły.  Zwiększona dostępność może być naliczana w cenie zwiększonej złożoności i należy projektować pod kątem optymalnej wydajności.  Przyjrzyjmy się pewnym ważnym zagadnieniom dotyczącym projektowania.

### <a name="automatic-zone-redundancy"></a>Automatyczna nadmiarowość stref

Load Balancer ułatwia używanie pojedynczego adresu IP jako frontonu Strefowo nadmiarowego. Adres IP nadmiarowy strefy może bezpiecznie obsłużyć zasoby strefowe w każdej strefie i może przetrwać awarie jednej strefy lub więcej, o ile jedna strefa pozostaje w dobrej kondycji w regionie. Z kolei strefa frontonu jest redukcją usługi do pojedynczej strefy i udostępnia losy z odpowiednią strefą.

Nadmiarowość stref nie oznacza hitless datapath lub płaszczyzny kontroli;  jest to wyraźnie płaszczyzna danych. Przepływ nadmiarowy strefy może korzystać ze wszystkich stref, a przepływy klientów będą używać wszystkich stref w dobrej kondycji w regionie. W przypadku awarii strefy nie ma to wpływu na przepływy ruchu korzystające z prawidłowych stref w tym momencie.  Może to mieć wpływ na przepływy ruchu korzystające z strefy w czasie awarii strefy, ale aplikacje mogą je odzyskać. Te przepływy mogą być kontynuowane w pozostałych strefach o dobrej kondycji w regionie po ponownym nawiązaniu lub ponownym ustanowieniu, gdy platforma Azure zostanie zbieżna wokół błędu strefy.

### <a name="xzonedesign"></a>Granice między strefami

Ważne jest, aby zrozumieć, że w dowolnym momencie usługa kompleksowej przekroczy strefy, możesz udostępniać losy nie tylko jednej strefie, ale potencjalnie w wielu strefach.  W związku z tym kompleksowa usługa może nie uzyskać dostępności w przypadku wdrożeń nienależących do stref.

Unikaj wprowadzania niezamierzonych zależności między strefami, co spowoduje zniesienia korzyści z dostępności podczas korzystania ze stref dostępności.  Gdy aplikacja składa się z wielu składników i chcesz odporna na awarie strefy, należy zadbać o to, aby upewnić się, że w przypadku awarii strefy wystąpią wystarczające krytyczne składniki.  Na przykład pojedynczy składnik krytyczny dla aplikacji może mieć wpływ na całą aplikację, jeśli istnieje tylko w strefie innej niż w przypadku pozostałych stref.  Ponadto należy również rozważyć przywrócenie strefy i sposób, w jaki aplikacja będzie zbieżna. Musisz zrozumieć, jak przyczyny Twojej aplikacji odnoszą się do błędów części. Zapoznajmy się z przykładami najważniejszych punktów i korzystaj z nich jako inspiracji w przypadku pytań w konkretnym scenariuszu.

- Jeśli aplikacja ma dwa składniki, takie jak adres IP i maszyna wirtualna z dyskiem zarządzanym, i są one gwarantowane w strefie 1 i 2, gdy strefa 1 zakończy się niepowodzeniem, usługa nie będzie przeżyje, gdy strefa 1 ulegnie awarii.  Nie należy przechodzić między strefami ze scenariuszami stref, chyba że w pełni rozumiesz, że tworzysz bezpieczny tryb awarii.  Ten scenariusz może zapewnić elastyczność.

- Jeśli aplikacja ma dwa składniki, takie jak adres IP i maszyna wirtualna z dyskiem zarządzanym i że mają być odpowiednio nadmiarowe strefy i strefy 1, usługa end-to-end nie ulegnie awarii strefy 2, strefy 3 lub obu, chyba że strefa 1 zakończyła się niepowodzeniem.  Jednak utracisz pewne możliwości, aby przyczynić się do kondycji usługi, jeśli wszystko, co jest zauważalne, jest osiągalne dla frontonu.  Zapoznaj się z bardziej rozbudowanym modelem kondycji i pojemności.  W celu zwiększenia wglądu w dane i łatwość zarządzania mogą być używane koncepcje nadmiarowe stref i strefowo.

- Jeśli aplikacja ma dwa składniki, takie jak strefowo nadmiarowe Load Balancer frontonu i wielostrefowy zestaw skalowania maszyn wirtualnych w trzech strefach, zasoby w strefach, których nie dotyczyły awarie, będą dostępne, ale wydajność usługi end-to-end może być obniżona podczas awarii strefy. Z punktu widzenia infrastruktury wdrożenie może obsłużyć co najmniej jedną awarię strefy i w ten sposób zgłasza następujące pytania:
  - Czy rozumiesz, jak przyczyny Twojej aplikacji dotyczą takich niepowodzeń i wydajności.
  - Czy musisz mieć zabezpieczenia w usłudze, aby wymusić przejście w tryb failover do pary regionów w razie potrzeby?
  - Jak będzie można monitorować, wykrywać i ograniczać ten scenariusz? Może być możliwe użycie diagnostyki usługa Load Balancer w warstwie Standardowa, aby rozszerzyć monitorowanie kompleksowej wydajności usługi. Zastanów się, co jest dostępne i co może wymagać rozszerzenia dla kompletnego obrazu.

- Strefy mogą sprawiać, że błędy są łatwiejsze do zrozumienia i zawarte.  Jednak awaria strefy nie różni się od innych błędów, gdy wiąże się to z pojęciami, takimi jak limity czasu, ponawianie prób i algorytmy wycofywania. Mimo że Azure Load Balancer zapewnia ścieżki nadmiarowe strefy i próbuje szybko wykonać odzyskiwanie, na poziomie pakietu w czasie rzeczywistym, retransmisje lub Reporty mogą wystąpić podczas wystąpienia awarii i ważne jest, aby zrozumieć, w jaki sposób aplikacja działa powodzenia. Schemat równoważenia obciążenia będzie przeżyje, ale należy zaplanować następujące czynności:
  - W przypadku niepowodzenia strefy usługa zapewnia kompleksową usługę, a jeśli stan zostanie utracony, w jaki sposób zostanie odzyskany?
  - Czy w przypadku powracania strefy aplikacja rozumie, jak bezpiecznie przeprowadzić zbieżność?

Przejrzyj [wzorce projektowania w chmurze platformy Azure](https://docs.microsoft.com/azure/architecture/patterns/) , aby zwiększyć odporność aplikacji na błędy.

### <a name="zonalityguidance"></a>Strefowo nadmiarowe w stosunku do stref

Strefa — nadmiarowe może zapewnić prostotę z opcją niezależny od strefy i w tym samym czasie odporny na jeden adres IP dla usługi.  Może ona zmniejszyć stopień złożoności.  Strefa nadmiarowa również ma mobilność między strefami i może być bezpiecznie używana do zasobów w dowolnej strefie.  Ponadto jest to przyszłe potwierdzenie w regionach bez stref dostępności, co może ograniczyć zmiany wymagane, gdy region uzyska strefy dostępności.  Składnia konfiguracji nadmiarowego adresu IP lub frontonu w dowolnym regionie, w tym w przypadku braku stref dostępności: strefa nie została określona w obszarze strefy: właściwość zasobu.

Strefa może zapewnić jawną gwarancję dla strefy, jawnie udostępniającą losy z kondycją strefy. Tworzenie reguły Load Balancer przy użyciu strefy frontonu IP lub strefy wewnętrznej Load Balancer wewnętrzna może być pożądane, szczególnie jeśli dołączony zasób jest maszyną wirtualną w tej samej strefie.  Lub być może Twoja aplikacja wymaga jawnej wiedzy na temat strefy, w której zasób znajduje się przed chwilą, i chcemy jawnie określić dostępność w oddzielnych strefach.  Możesz uwidocznić wiele frontonów na potrzeby kompleksowych usług rozproszonych w różnych strefach (to jest na frontony strefy stref dla wielu zestawów skalowania maszyn wirtualnych).  A jeśli strefą frontony są publiczne adresy IP, można użyć tych wielu frontonów, aby uwidocznić swoją usługę za pomocą [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Można też użyć wielu stref frontonów, aby uzyskać informacje o kondycji strefy i wydajności za pomocą rozwiązań do monitorowania innych firm i uwidocznić ogólną usługę za pomocą nadmiarowej strefy. Zasoby strefowe z frontonami stref są wyrównane do tej samej strefy, co pozwala uniknąć potencjalnie szkodliwych scenariuszy między strefami dla zasobów strefowych.  Zasoby strefowe istnieją tylko w regionach, w których istnieją strefy dostępności.

Nie ma ogólnych wskazówek, które są lepszym wyborem niż pozostałe, bez znajomości architektury usług.  Przejrzyj [wzorce projektowania w chmurze platformy Azure](https://docs.microsoft.com/azure/architecture/patterns/) , aby zwiększyć odporność aplikacji na błędy.

## <a name="limitations"></a>Ograniczenia

- Gdy płaszczyzna danych jest w pełni strefowo nadmiarowa (chyba że została określona gwarancja Zona), operacje płaszczyzny kontroli nie są w pełni nadmiarowe.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [strefy dostępności](../availability-zones/az-overview.md)
- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)
- Dowiedz się, jak [równoważyć obciążenie maszyn wirtualnych w strefie przy użyciu usługa Load Balancer w warstwie Standardowa z strefą frontonu](load-balancer-standard-public-zonal-cli.md)
- Dowiedz się, jak [równoważyć obciążenie maszyn wirtualnych między strefami przy użyciu usługa Load Balancer w warstwie Standardowa ze strefowo nadmiarowy fronton](load-balancer-standard-public-zone-redundant-cli.md)
- Dowiedz się więcej o [wzorcach projektowych chmury platformy Azure](https://docs.microsoft.com/azure/architecture/patterns/) w celu zwiększenia odporności aplikacji na scenariusze błędów.
