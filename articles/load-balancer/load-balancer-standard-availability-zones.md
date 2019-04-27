---
title: Usługa Azure standardowego modułu równoważenia obciążenia i strefy dostępności
titlesuffix: Azure Load Balancer
description: Usługa Load Balancer w warstwie Standardowa i strefy dostępności
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: kumud
ms.openlocfilehash: 6f33be6e418366f57d243f578035b5c87079c99e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734460"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Usługa Load Balancer w warstwie Standardowa i strefy dostępności

Obsługuje platformy Azure równoważenia obciążenia standardowej jednostki SKU [strefy dostępności](../availability-zones/az-overview.md) scenariuszy. Kilku nowych pojęć dostępnych przy użyciu standardowego modułu równoważenia obciążenia umożliwiają Optymalizowanie dostępności w tym scenariuszu end-to-end wyrównywanie zasobów przy użyciu stref i rozmieszczanie ich w strefach.  Przegląd [strefy dostępności](../availability-zones/az-overview.md) wskazówki dotyczące co to są strefy dostępności, które regiony świadczenia obecnie obsługują strefy dostępności i inne związane z pojęć i produktów. Strefy dostępności w połączeniu z Balancer w warstwie standardowa to zestaw funkcji rozszerzania i elastyczne, który można utworzyć wiele różnych scenariuszy.  Przejrzyj tego dokumentu, aby zrozumieć [pojęcia](#concepts) i podstawowych scenariuszy [wskazówki projektowania](#design).

>[!IMPORTANT]
>Przegląd [strefy dostępności](../availability-zones/az-overview.md) uzyskać pokrewne, w tym wszelkie informacje określonego regionu.

## <a name="concepts"></a> Strefy dostępności pojęcia stosowane do modułu równoważenia obciążenia

Nie ma bezpośredniej relacji między zasobami usługi równoważenia obciążenia i rzeczywistej infrastruktury. Tworzenie modułu równoważenia obciążenia nie tworzy wystąpienia. Zasoby są obiekty, w których można wyrazić jak Azure powinien program jej wbudowanych infrastruktury wielu dzierżawców do osiągnięcia scenariusza, który chcesz utworzyć moduł równoważenia obciążenia.  Jest to istotne w kontekście strefy dostępności, ponieważ pojedynczy zasób modułu równoważenia obciążenia można kontrolować programowania infrastruktury w wielu strefach dostępności, podczas gdy usługa strefowo nadmiarowy jest wyświetlany jako jeden zasób z punktu widzenia klienta.

Funkcje zasobu modułu równoważenia obciążenia są wyrażane jako frontonu, reguły, sondy kondycji i definicji puli zaplecza.

W kontekście strefy dostępności zachowanie i właściwości zasobu modułu równoważenia obciążenia są określane jako strefowo nadmiarowego lub strefowych.  Strefowo nadmiarowy i strefowych opisują zonality właściwości.  W kontekście usługi równoważenia obciążenia, strefowo nadmiarowe zawsze oznacza *wiele stref* i strefowych oznacza, że usługi do izolowania *jednej strefie*.

Zarówno public i internal Load Balancer, która obsługuje scenariusze strefowo nadmiarowy i strefowych i zarówno kierowania ruchem między strefami, zgodnie z potrzebami (*równoważenia obciążenia międzystrefowego*).

Samego zasobu modułu równoważenia obciążenia jest regionalnych i strefowych nigdy nie.  I sieci wirtualnej i podsieci są zawsze regionalnych i strefowych nigdy nie.

### <a name="frontend"></a>Fronton

Fronton modułu równoważenia obciążenia jest konfiguracja adresu IP frontonu, odwoływanie się do zasobu publicznego adresu IP lub prywatnego adresu IP w obrębie podsieci zasobu sieci wirtualnej.  Wchodzi w skład punkt końcowy o zrównoważonym obciążeniu gdzie usługa jest widoczna.

Zasób modułu równoważenia obciążenia może zawierać jednocześnie frontonów strefowo nadmiarowy i strefowych. 

Ma zostały gwarancję publicznego zasobu adresu IP w strefie, zonality (lub ich brak) jest modyfikowalna.  Jeśli chcesz zmienić lub Pomiń zonality z publicznego adresu IP frontonu, należy ponownie utworzyć publiczny adres IP, które są dostępne w odpowiedniej strefy.  

Usunięcie i ponowne utworzenie frontonu, zmienianie lub pominięcie zonality, można zmienić zonality z frontonu wewnętrznego modułu równoważenia obciążenia.

Korzystając z wielu frontonów, przejrzyj [wiele frontonów dla modułu równoważenia obciążenia](load-balancer-multivip-overview.md) ważne zagadnienia.

#### <a name="zone-redundant-by-default"></a>Domyślnie strefowo nadmiarowe

>[!IMPORTANT]
>Przegląd [strefy dostępności](../availability-zones/az-overview.md) uzyskać pokrewne, w tym wszelkie informacje określonego regionu.

W regionie Dzięki strefom dostępności frontonu Balancer w warstwie standardowa jest strefowo nadmiarowe domyślnie.  Adres IP frontonu pojedynczego mogą przetrwać awarie strefy i może służyć do dotrzeć do wszystkich składowych puli zaplecza, niezależnie od strefy. Nie oznacza to ścieżki hitless danych, ale ponownych prób ani reestablishment zakończy się powodzeniem. Schematy nadmiarowości DNS nie są wymagane. Pojedynczy adres IP frontonu modułu jest jednocześnie obsługiwany przez wiele wdrożeń infrastruktury niezależnych w wielu strefach dostępności.  Strefowo nadmiarowe oznacza, że wszystkie przepływy ruchu przychodzącego lub wychodzącego są obsługiwane przez wielu strefach dostępności w regionie jednocześnie za pomocą pojedynczego adresu IP.

Jeden lub więcej stref dostępności może zakończyć się niepowodzeniem, a ścieżka danych tak długo, jak jedną strefę w pozostaje region przeżyje dobrej kondycji. Strefowo nadmiarowy jest domyślnie i wymaga żadnych dodatkowych akcji.  

Użyj następującego skryptu, aby utworzyć strefowo nadmiarowy publiczny adres IP wewnętrznego modułu równoważenia obciążenia standardowego. Jeśli korzystasz z istniejących szablonów usługi Resource Manager w konfiguracji, należy dodać **jednostki sku** sekcji, aby te szablony.

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

Poniższy skrypt umożliwia utworzenie adresu IP frontonu strefowo nadmiarowe wewnętrznego modułu równoważenia obciążenia standardowego. Jeśli korzystasz z istniejących szablonów usługi Resource Manager w konfiguracji, należy dodać **jednostki sku** sekcji, aby te szablony.

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

#### <a name="optional-zone-isolation"></a>Izolacja strefy opcjonalne

Istnieje możliwość mają frontonu gwarantowane jedną strefę, co jest znane jako *strefowych frontonu*.  Oznacza to, że dowolny przepływ ruchu przychodzącego lub wychodzącego jest obsługiwany przez jedną strefę, w regionie.  Usługi frontonu udostępni LOS kondycję strefy.  Ścieżka danych jest niezależny od błędów w strefach niż gdzie zostało gwarantowane. Strefowych frontonów można użyć do udostępnienia adresu IP dla stref dostępności.  Ponadto można bezpośrednio korzystać strefowych frontonów lub, jeśli frontonu, który składa się z publicznych adresów IP, zintegrowanie ich z produktem równoważenia obciążenia DNS, takich jak [usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md) i używanie jednej nazwy DNS, która zostanie rozpoznana klienta wiele adresów IP strefowych.  Również służy to do udostępnienia poszczególnych punktów końcowych ze zrównoważonym obciążeniem strefy oddzielnie monitorować każdej strefy.  Jeśli chcesz dopasować te pojęcia (strefowo nadmiarowy i strefowych dla tego samego zaplecza), przejrzyj [wiele frontonów dla usługi Azure Load Balancer](load-balancer-multivip-overview.md).

Dla publicznej frontonu modułu równoważenia obciążenia, możesz dodać *stref* parametr do publicznego adresu IP przywoływany przez konfigurację IP frontonu.  

W przypadku wewnętrznych frontonu modułu równoważenia obciążenia, dodać *stref* parametr wewnętrzny konfiguracji adresu IP frontonu modułu równoważenia obciążenia. Strefowy frontonu powoduje, że moduł równoważenia obciążenia w celu zagwarantowania adresu IP w podsieci do określonej strefy.

Poniższy skrypt umożliwia utworzenie strefowych standardowego publicznego adresu IP w 1 strefy dostępności. Jeśli korzystasz z istniejących szablonów usługi Resource Manager w konfiguracji, należy dodać **jednostki sku** sekcji, aby te szablony.

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

Użyj następującego skryptu do tworzenia wewnętrznego modułu równoważenia obciążenia standardowego frontonu w 1 strefy dostępności.

Jeśli korzystasz z istniejących szablonów usługi Resource Manager w konfiguracji, należy dodać **jednostki sku** sekcji, aby te szablony. Ponadto zdefiniować **stref** właściwości w konfiguracji adresu IP frontonu dla zasobów podrzędnych.

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

### <a name="cross-zone-load-balancing"></a>Równoważenie obciążenia między strefami

Równoważenie obciążenia między strefami jest możliwość osiągnięcia punktu końcowego wewnętrznej bazy danych w każdej strefie modułu równoważenia obciążenia i jest niezależna od frontonu i jego zonality.

Jeśli chcesz się i gwarantuje wdrożenia w jednej strefie Wyrównaj strefowych frontonu i zaplecza strefowych zasobów do tej samej strefie. Nie są wymagane żadne dalsze działania.

### <a name="backend"></a>Zaplecze

Moduł równoważenia obciążenia działa z maszynami wirtualnymi.  Dowolnej maszyny Wirtualnej w pojedynczej sieci wirtualnej mogą być częścią puli zaplecza, niezależnie od tego, czy został gwarantowane do strefy lub która strefa została gwarantowane.

Jeśli chcesz się i gwarantuje usługi frontonu i zaplecza za pomocą jednej strefie tylko umieść maszyny wirtualne w ramach tej samej strefie w puli zaplecza odpowiednich.

Jeśli chcesz adres maszyny wirtualne w wielu strefach po prostu umieść maszyny wirtualne z wieloma strefami w tej samej puli zaplecza.  W przypadku zestawów danych za pomocą skalowania maszyn wirtualnych, można umieścić jednego lub więcej zestawów skalowania maszyn wirtualnych w tej samej puli zaplecza.  A każdy z tych zestawów skalowania maszyn wirtualnych może być w jednym lub wieloma strefami.

### <a name="outbound-connections"></a>Połączenia wychodzące

[Połączenia wychodzące](load-balancer-outbound-connections.md) są obsługiwane przez wszystkie strefy i są automatycznie strefowo nadmiarowy w regionie ze strefami dostępności występują, gdy maszyna wirtualna jest skojarzona z publicznej usługi Load Balancer i strefowo nadmiarowe frontonu.  Alokacje portu SNAT połączeń wychodzących po awarii strefy.  

Z kolei Jeśli maszyna wirtualna jest skojarzona z publicznej usługi Load Balancer i strefowych frontonu, połączenia wychodzące gwarancję ma być obsługiwana przez jedną strefę.  Połączenia wychodzące udostępniać LOS kondycji odpowiednimi strefy.

Wstępne SNAT portu przydzielanie i algorytm jest taki sam, z lub bez stref.

### <a name="health-probes"></a>Sondy kondycji

Są one bez stref dostępności istniejącej definicji sondy kondycji jest zachowywana.  Ale rozszerzono modelu kondycji na poziomie infrastruktury. 

Korzystając z strefowo nadmiarowe frontonów, moduł równoważenia obciążenia rozszerza swój model kondycji wewnętrznego niezależnie sondowania wysyłające maszyny Wirtualnej z każda strefa dostępności i zamknąć ścieżek w strefach, których nie powiodła się bez interwencji klienta.  Jeśli w podanej ścieżce nie jest dostępne z infrastruktury modułu równoważenia obciążenia w jednej strefie do maszyny Wirtualnej w innej strefie, moduł równoważenia obciążenia można wykryć i uniknąć tego błędu. Innych strefach, którzy mogą dotrzeć do tej maszyny Wirtualnej, mogą nadal obsługiwać maszynę Wirtualną z ich odpowiednimi frontonów.  W rezultacie jest możliwe, że podczas zdarzenia błędów każdej ze stref może mieć nieco inny przepływ dystrybucje przy jednoczesnej ochronie ogólnej kondycji usługi end-to-end.

## <a name="design"></a> Zagadnienia dotyczące projektowania

Moduł równoważenia obciążenia jest celowo elastyczne w kontekście stref dostępności. Istnieje możliwość wyrównane do strefy lub użytkownik może być strefowo nadmiarowe.  Zwiększona dostępność mogą pochodzić po cenie wzrostu złożoności i należy projektować dostępności w celu uzyskania optymalnej wydajności.  Spójrzmy na niektóre istotne zagadnienia.

### <a name="automatic-zone-redundancy"></a>Automatycznej nadmiarowości strefy

Moduł równoważenia obciążenia pozwala w prosty sposób mieć pojedynczy adres IP jako frontend strefowo nadmiarowe. Strefowo nadmiarowe adres IP można bezpiecznie obsługiwać zasobem strefowym w każdej strefie i mogą przetrwać awarię strefy co najmniej jednego, tak długo, jak tylko jedną strefę pozostanie w dobrej kondycji w obrębie regionu. Z drugiej strony strefowych frontonu jest zmniejszenie usługi do pojedynczej strefy i udziałów LOS z odpowiedniej strefy.

Nadmiarowości strefy nie sugeruje hitless ścieżki danych płaszczyzna kontroli;  jest wyraźnie płaszczyzny danych. Strefowo nadmiarowe przepływy mogą używać żadnych stref i przepływy klient użyje wszystkie strefy w dobrej kondycji w regionie. W przypadku awarii strefy nie są zagrożone widok przepływów ruchu sieciowego przy użyciu stref dobrej kondycji w danym momencie.  Widok przepływów ruchu sieciowego przy użyciu strefy w czasie awarii strefa może mieć wpływ, ale można odzyskać aplikacje i przepływy mogą nadal w pozostałych dobrej kondycji strefach w regionie od retransmisji lub reestablishment po Azure została połączona wokół błąd stref.

### <a name="xzonedesign"></a> Krzyżowe granice stref

Jest ważne dowiedzieć się, że ilekroć end-to-end usługi przecina strefy, możesz udostępniać LOS nie jedną strefę, ale potencjalnie wiele stref.  W wyniku end-to-end usługi mogą nie weszły wszelkie dostępności za pośrednictwem wdrożenia strefowych.

Należy unikać wprowadzenie niezamierzonych zależności między strefami, które zniweczyć zyski dostępności, korzystając z stref dostępności.  Gdy Twoja aplikacja składa się z wielu składników, i chcesz była odporna na awarie strefy, musi zajmie się, aby zapewnić pozostawanie w mocy wystarczające krytycznych składników w przypadku niepowodzenia strefy.  Na przykład pojedynczy kluczowy składnik aplikacji może wpłynąć na całej aplikacji, jeśli istnieje tylko w strefie niż sprawny strefy.  Ponadto należy również rozważyć przywracania strefy i jak zbierze aplikacji. Teraz przejrzeć niektóre punkty klucza i używać ich jako inspiracji pytania myśląc za pośrednictwem określonego scenariusza.

- Jeśli aplikacja ma dwa składniki, takie jak adres IP i maszyny Wirtualnej w przypadku dysków zarządzanych i są one gwarantowana w strefie 1 oraz w strefie 2, gdy strefa 1 nie powiodło się z usługą end-to-end, nie będą nadal obowiązywać jeśli strefa 1 nie powiodło się.  Nie przekracza strefy, chyba że w pełni rozumiesz tworzysz trybie potencjalnie niebezpiecznych awarii.

- Jeśli aplikacja ma dwa składniki, takie jak adres IP i maszyny Wirtualnej z dysku, zarządzanego i mają gwarancję, że strefowo nadmiarowy i odpowiednio strefa 1, end-to-end usługi przeżyje katastrofę awarii strefy w strefie 2, strefa 3 i / lub jeśli strefa 1 nie powiodło się.  Jednak utracisz możliwość przeglądanie informacji o kondycji usługi wszystkie występujący w przypadku wysyłające frontonu.  Należy rozważyć utworzenie bardziej rozległe modelu kondycji i wydajności.  Strefowo nadmiarowy i strefowych pojęcia możecie używać ze sobą, aby rozwinąć wglądu i możliwości zarządzania.

- Jeśli aplikacja ma dwa składniki, takie jak strefowo nadmiarowe frontonu modułu równoważenia obciążenia i zestaw skalowania maszyn wirtualnych między strefami w trzech strefach, zasobami w strefach nie będą miały wpływu awarii będzie dostępna, ale może się pogorszyć wydajność usługi end-to-end podczas awarii strefy. Z perspektywy infrastruktury wdrożenia mogą przetrwać awarię strefy co najmniej jednego, i zgłasza to następujące pytania:
  - Czy wiesz, jak aplikacja przyczyny o takich błędów i pogorszenie wydajności?
  - Czy muszą mieć zabezpieczenia w usłudze, aby wymusić przejścia w tryb failover w parze regionów, w razie potrzeby?
  - Jak będzie możesz monitorować, wykrywanie i eliminowanie takiej sytuacji? Dzięki temu można skorzystać z diagnostyki Balancer w warstwie standardowa można rozszerzyć monitorowanie wydajności usługi end-to-end. Należy wziąć pod uwagę, jakie opcje są dostępne i które mogą wymagać powiększenia do pełnego obrazu.

- Strefy można wprowadzić błędy łatwiej zrozumieć i zawarte.  Jednak błąd stref jest nie różni się od innych awarii, jeśli chodzi o koncepcje, takie jak limity czasu, ponownych prób i algorytmów wycofywania. Mimo że usługa Azure Load Balancer zapewnia strefowo nadmiarowe ścieżki i próbuje odzyskać szybkiego na poziomie pakietów w czasie rzeczywistym, liczba retransmisji lub reestablishments mogą wystąpić podczas wystąpienia awarii i jest ważne zrozumieć, jak aplikacja copes z błędy. Schemat równoważenia obciążenia, będą nadal obowiązywać, ale należy zaplanować do wykonania poniższych czynności:
  - Gdy strefa nie powiedzie się, czy usługa end-to-end rozumie to i jeśli stan zostanie utracony, jak będzie można odzyskać?
  - Po powrocie z strefy aplikacja zrozumieć, jak bezpiecznie zbieżne?

### <a name="zonalityguidance"></a> Strefowo nadmiarowy i strefowej

>[!IMPORTANT]
>Przegląd [strefy dostępności](../availability-zones/az-overview.md) uzyskać pokrewne, w tym wszelkie informacje określonego regionu.

Strefowo nadmiarowe można podać strefy niezależny od i w tej samej opcji odporne na błędy czasu za pomocą pojedynczego adresu IP adresów dla usługi.  Z kolei ona zredukowana złożoność.  Strefowo nadmiarowe również ma mobilności w różnych strefach i mogą być bezpiecznie stosowane na zasoby w każdej strefie.  Jest także przyszłe dowód w regionach bez stref dostępności, które mogą ograniczać zmiany są wymagane, gdy region uzyskiwanie strefy dostępności.  Składnia konfiguracji strefowo nadmiarowe adres IP lub fronton powiedzie się w dowolnym regionie, w tym użytkownicy bez stref dostępności.

Strefowy może zapewnić gwarancjami ze strefą udostępnianie LOS kondycję strefy. Kojarzenie strefowych IP adres lub strefowych frontonu modułu równoważenia obciążenia może być pożądane lub uzasadnione atrybut zwłaszcza, jeśli dołączone zasób jest strefowych maszyny Wirtualnej w tej samej strefie.  Być może aplikacja wymaga jawnego wiedzy, o której strefie zasób znajduje się w lub chcesz poprawić dostępność w oddzielnych strefach jawnie.  Możesz udostępnić wielu frontonów strefowych usługi end-to-end, rozproszonych w różnych strefach (czyli na strefy frontonów strefowych dla wielu skalowania w strefowych maszyny wirtualnej ustawia).  I Twoje strefowych frontonów są publicznymi adresami IP, możesz używać tych wiele frontonów strefowych do udostępniania usługi za pomocą [usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Możesz też wiele frontonów strefowych uzyskania na strefy kondycję i wydajność wnioski z innych firm, rozwiązania do monitorowania i udostępnienia ogólnego usługi za pomocą frontonu strefowo nadmiarowe. Należy tylko udostępniania zasobów strefowych z frontonów strefowych wyrównane do tej samej strefie i uniknąć potencjalnie szkodliwe scenariuszy międzystrefowego dla zasobów strefowych.  Zasobach strefowych istnieją tylko w regionach, w której istnieje stref dostępności.

Nie ma żadnych ogólne wskazówki, jeden z nich jest lepszym rozwiązaniem niż ten drugi, nie wiedząc o tym architektury usługi.

## <a name="limitations"></a>Ograniczenia

- Podczas gdy danych płaszczyzna jest w pełni strefowo nadmiarowy (o ile nie określono strefowych gwarancji), operacje warstwy kontroli nie są w pełni strefowo nadmiarowe.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [strefy dostępności](../availability-zones/az-overview.md)
- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)
- Dowiedz się, jak [Równoważenie obciążenia maszyn wirtualnych w strefie standardowego modułu równoważenia obciążenia przy użyciu strefowych frontonu](load-balancer-standard-public-zonal-cli.md)
- Dowiedz się, jak [Równoważenie obciążenia maszyn wirtualnych w strefach przy użyciu standardowego modułu równoważenia obciążenia za pomocą frontonu strefowo nadmiarowy](load-balancer-standard-public-zone-redundant-cli.md)
