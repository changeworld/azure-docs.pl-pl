---
title: Moduł równoważenia obciążenia standardowego Azure i dostępności stref | Dokumentacja firmy Microsoft
description: Moduł równoważenia obciążenia standardowego i dostępności stref
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: kumud
ms.openlocfilehash: f5d46fda6bdb32c1a5000883c6aedb2da15e796a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30322798"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Moduł równoważenia obciążenia standardowego i dostępności stref

Standardowy SKU Azure równoważenia obciążenia obsługuje [stref dostępności](../availability-zones/az-overview.md) scenariuszy. Kilku nowych pojęć są dostępne standardowe usługi równoważenia obciążenia, które pozwalają zoptymalizować dostępność w danym scenariuszu end-to-end ustawiając zasobów przy użyciu stref, a także rozpowszechniają strefy.  Przegląd [stref dostępności](../availability-zones/az-overview.md) wskazówki dotyczące dostępności strefy są, regiony obsługuje obecnie stref dostępności i inne powiązane pojęcia i produktów. Dostępność stref w połączeniu z modułem równoważenia obciążenia standardowego to zestaw funkcji rozszerzania i elastyczne, który może tworzyć wiele różnych scenariuszy.  Przejrzyj tego dokumentu, aby zrozumieć [pojęcia](#concepts) oraz scenariusz podstawowych [wskazówki dotyczące projektowania](#design).

>[!NOTE]
>Przegląd [stref dostępności](https://aka.ms/availabilityzones) dla innych powiązanych tematów. 

## <a name="concepts"></a> Pojęcia dotyczące dostępności stref stosowane do modułu równoważenia obciążenia

Nie ma żadnej bezpośredniej zależności między zasobami usługi równoważenia obciążenia i rzeczywistego infrastruktury. Tworzenie modułu równoważenia obciążenia nie tworzy wystąpienie. Zasoby usługi równoważenia obciążenia to obiekty w ramach których mogą express, jak program jego wbudowane wielodostępnej infrastrukturze do osiągnięcia scenariusza, który chcesz utworzyć w usłudze Azure.  Jest to istotne w kontekście stref dostępności, ponieważ pojedynczego zasobu usługi równoważenia obciążenia można kontrolować programowania infrastruktury w wielu strefach dostępności podczas strefowo nadmiarowy usługi pojawia się jako jeden zasób z punktu widzenia klienta.

Funkcje zasób usługi równoważenia obciążenia są wyrażane jako frontonu, regułę, sondy kondycji i definicji puli wewnętrznej bazy danych.

W kontekście dostępności stref jako strefowo nadmiarowy lub zonal opisane zachowanie i właściwości zasobów usługi równoważenia obciążenia.  Strefowo nadmiarowy i zonal opisują zonality właściwości.  W kontekście usługi równoważenia obciążenia, strefowo nadmiarowy zawsze oznacza *wszystkich stref* i zonal oznacza usługę w celu zagwarantowania *jednej strefie*.

Publiczny i wewnętrzny moduł równoważenia obciążenia obsługuje scenariusze strefowo nadmiarowy i zonal i jednocześnie może kierować ruch różnych strefach zgodnie z potrzebami (*Równoważenie obciążenia sieciowego stref między*).

Sam zasobu usługi równoważenia obciążenia jest regionalnego i nigdy nie zonal.  I sieci wirtualnej i podsieci są zawsze regionalnych i nigdy nie zonal.

### <a name="frontend"></a>Fronton

Konfiguracja IP frontonu odwołującego się do zasobu publiczny adres IP lub prywatnego adresu IP w podsieci sieci wirtualnej zasobu jest frontonu modułu równoważenia obciążenia.  Wchodzi w skład punkt końcowy ze zrównoważonym obciążeniem gdzie ma połączenie z usługą.

Zasób usługi równoważenia obciążenia może zawierać zarówno zonal i strefowo nadmiarowy frontends jednocześnie. 

Gdy ma zostały gwarancję zasób publicznego adresu IP do strefy, zonality (lub ich brak) nie jest modyfikowalna.  Jeśli chcesz zmienić lub Pomiń zonality z publicznego adresu IP frontonu, należy utworzyć je ponownie z publicznym adresem IP w odpowiedniej strefy.  

Usunięcie i ponowne utworzenie frontonu, zmiana lub pominięcie zonality, można zmienić zonality z frontonu wewnętrzny moduł równoważenia obciążenia.

Korzystając z wielu frontends, przejrzyj [wiele frontends dla usługi równoważenia obciążenia](load-balancer-multivip-overview.md) przypadku istotnych kwestii.

#### <a name="zone-redundant-by-default"></a>Obszar strefowo nadmiarowy domyślnie

W regionie ze strefami dostępności frontonu standardowy moduł równoważenia obciążenia jest strefowo nadmiarowy domyślnie.  Adres IP frontonu pojedynczego przełączniki strefy awarii i może służyć do wszystkich elementów członkowskich puli wewnętrznej bazy danych niezależnie od strefy. Nie oznacza to ścieżka hitless danych, ale ponownych prób ani reestablishment się pomyślnie. Schematy nadmiarowość DNS nie są wymagane. Pojedynczy adres IP frontonu jednocześnie obsługiwanej przez niezależnych infrastruktury wdrożenia w każdej strefie dostępności.  Strefowo nadmiarowy oznacza, że wszystkie przepływy ruchu przychodzącego lub wychodzącego są obsługiwane przez wszystkie strefy dostępności w regionie jednocześnie przy użyciu jednego adresu IP.

Co najmniej jedna strefa dostępności może zakończyć się niepowodzeniem rekompensaty dla użytkownika i ścieżki danych tak długo, jak jedną strefę w regionie pozostaje dobrej kondycji. Konfiguracja strefowo nadmiarowy jest domyślnie i wymaga żadnych dodatkowych czynności.  Region uzyska możliwość obsługi dostępności stref, istniejące frontonu staje się strefowo nadmiarowy automatycznie.

Użyj następującego skryptu do utworzenia strefowo nadmiarowy adresu publicznego adresu IP dla sieci wewnętrznej standardowe usługi równoważenia obciążenia. Jeśli używasz istniejących szablonów usługi Resource Manager w konfiguracji, należy dodać **sku** sekcji, aby te szablony.

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

Poniższy skrypt umożliwia utworzenie adresu IP frontonu strefowo nadmiarowy dla wewnętrznego standardowy moduł równoważenia obciążenia. Jeśli używasz istniejących szablonów usługi Resource Manager w konfiguracji, należy dodać **sku** sekcji, aby te szablony.

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

#### <a name="optional-zone-guarantee"></a>Wymagana jest gwarancja opcjonalne strefy

Możesz wybrać opcję frontonu gwarancji jednej strefie, znany jako *zonal frontonu*.  Oznacza to, że wszystkie przepływu ruchu przychodzącego lub wychodzącego jest obsługiwana przez jednej strefie w regionie.  Z serwera sieci Web udostępnia obecność kondycji strefy.  Ścieżka danych nie mają wpływu błędów w strefach niż gdzie został gwarancji. Zonal frontends można użyć do udostępnienia adresu IP dla stref dostępności.  Ponadto można korzystać bezpośrednio zonal frontends lub, jeśli frontonu składa się z publicznych adresów IP, integracja je z produktu, takich jak z równoważeniem obciążenia DNS [Traffic Manager](../traffic-manager/traffic-manager-overview.md) i używanie jednej nazwy DNS, który klient będzie prowadzić do wiele zonal adresów IP.  Umożliwia także to do udostępnienia poszczególnych punktów końcowych z równoważeniem obciążenia strefy oddzielnie monitorować każdej strefy.  Jeśli chcesz dopasować tych pojęć (strefowo nadmiarowy i zonal dla tego samego wewnętrznej bazy danych), przejrzyj [wiele frontends dla usługi równoważenia obciążenia Azure](/load-balancer-multivip-overview.md).

Dla publicznych frontonu modułu równoważenia obciążenia, możesz dodać *stref* parametr do publicznego adresu IP, przywoływany przez konfigurację IP frontonu.  

Wewnętrzny moduł równoważenia obciążenia serwera sieci Web, można dodać *stref* parametr wewnętrzny konfiguracji IP frontonu modułu równoważenia obciążenia. Zonal frontonu powoduje, że moduł równoważenia obciążenia zagwarantować adresu IP w podsieci do określonej strefy.

Poniższy skrypt umożliwia utworzenie zonal standardowe publiczny adres IP 1 strefy dostępności. Jeśli używasz istniejących szablonów usługi Resource Manager w konfiguracji, należy dodać **sku** sekcji, aby te szablony.

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

Użyj następującego skryptu można utworzyć wewnętrznego modułu równoważenia obciążenia standardowego frontonu w dostępności strefy 1.

Jeśli używasz istniejących szablonów usługi Resource Manager w konfiguracji, należy dodać **sku** sekcji, aby te szablony. Ponadto zdefiniować **stref** właściwości w konfiguracji adresu IP frontonu dla zasobu podrzędnego.

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

### <a name="cross-zone-load-balancing"></a>Równoważenie obciążenia między strefy

Równoważenie obciążenia między strefy jest możliwość osiągnięcia punktu końcowego wewnętrznej bazy danych w każdej strefie moduł równoważenia obciążenia i jest niezależna od frontonu i jego zonality.

Jeśli chcesz wyrównać i zagwarantować wdrożenia w ramach jednej strefie wyrównuje zonal frontonu i zaplecza zonal zasoby do tej samej strefy. Są wymagane żadne dalsze akcje.

### <a name="backend"></a>Zaplecze

Moduł równoważenia obciążenia działa z maszynami wirtualnymi.  Wszystkie maszyny Wirtualnej w ramach jednej sieci wirtualnej może być częścią puli wewnętrznej bazy danych niezależnie od tego, czy został gwarantowane do strefy lub stref, które zostało gwarancji.

Jeśli chcesz wyrównać i zagwarantować sieci frontonu i wewnętrznej bazy danych z jednej strefie tylko umieszczenie maszyn wirtualnych w tej samej strefie w puli zaplecza odpowiednich.

Jeśli chcesz adres maszyn wirtualnych w wielu strefach po prostu umieść maszyny wirtualne z wielu strefach w tej samej puli wewnętrznej bazy danych.  Po użyciu skali maszyny wirtualnej, co najmniej jeden zestaw skali maszyny wirtualnej można umieścić w tej samej puli wewnętrznej bazy danych.  A każdy z tych zestawów skali maszyny wirtualnej może być w jednym lub wielu strefach.

### <a name="outbound-connections"></a>Połączenia wychodzące

[Połączenia wychodzące](load-balancer-outbound-connections.md) są obsługiwane przez wszystkie strefy i są automatycznie strefowo nadmiarowy w regionie ze strefami dostępności maszyny wirtualnej jest skojarzony z publicznej usługi równoważenia obciążenia i strefowo nadmiarowy frontonu.  Wychodzące połączenie SNAT portu alokacji po awarii strefy.  

Z kolei Jeśli maszyna wirtualna jest skojarzona z publicznej usługi równoważenia obciążenia i zonal frontonu, połączeń wychodzących gwarancję ma być obsługiwana przez jednej strefie.  Połączenia wychodzące udostępniać kondycja strefy odpowiednich przeznaczenie.

Wstępne przydzielanie portu SNAT i algorytm jest taki sam, z lub bez stref.

### <a name="health-probes"></a>Sondy kondycji

Do istniejącej definicji sondy kondycji pozostają bez stref dostępności.  Ale zostały rozszerzone, model kondycji na poziomie infrastruktury. 

Korzystając z strefowo nadmiarowy frontends, usługi równoważenia obciążenia rozszerza jego model kondycji wewnętrzny niezależnie sondowania wysyłające maszyny Wirtualnej z każdej strefy dostępności i zamknąć ścieżek w różnych strefach, których nie powiodła się bez interwencji klienta.  Jeśli podanej ścieżki nie jest dostępny od infrastruktury usługi równoważenia obciążenia jedną strefę z maszyną wirtualną w innej strefie, usługi równoważenia obciążenia mogą wykrywać i uniknąć tego błędu. Inne stref, które można osiągnąć tej maszyny Wirtualnej mogą nadal służyć maszyny Wirtualnej z ich odpowiednich frontends.  W związku z tym jest możliwe, że podczas zdarzenia błędów każdej strefy może mieć dystrybucje nieco inny przepływ ochrony ogólnej kondycji usługi end-to-end.

## <a name="design"></a> Zagadnienia dotyczące projektowania

Moduł równoważenia obciążenia jest celowo elastyczna w kontekście dostępności strefy. Użytkownik może być wyrównane do strefy lub użytkownik może być strefowo nadmiarowy.  Większej dostępności może występować w cenie zwiększenia złożoności i należy projektować dostępności, aby zapewnić optymalną wydajność.  Spójrzmy na niektóre istotne zagadnienia.

### <a name="automatic-zone-redundancy"></a>Automatic zone-redundancy

Moduł równoważenia obciążenia upraszcza mieć pojedynczy adres IP, jako strefowo nadmiarowy frontonu. Strefowo nadmiarowy adres IP może bezpiecznie obsługiwać zonal zasobów w każdej strefie, może po awarii strefy co najmniej jednego, tak długo, jak jedną strefę pozostaje w dobrej kondycji w regionie. Z drugiej strony zonal frontonu jest zmniejszenie usługi do pojedynczego obecność strefy i udziałów dla odpowiednich strefy.

Nadmiarowość stref oznacza hitless ścieżki danych ani płaszczyzny kontroli;  należy wyraźnie płaszczyzna danych. Strefowo nadmiarowy przepływów można używać żadnych stref i przepływów klienta będzie używać wszystkich stref dobrej kondycji w regionie. W przypadku niepowodzenia strefy nie dotyczy przepływów ruchu sieciowego przy użyciu stref dobrej kondycji w danym momencie.  Przy użyciu strefy w chwili wystąpienia błędu strefy przepływów ruchu sieciowego może mieć wpływ na, ale aplikacje można odzyskać, i kontynuować przepływy w pozostałych strefach dobrej kondycji w obrębie regionu retransmisji lub reestablishment po Azure ma zbieżność wokół błąd stref.

### <a name="xzonedesign"></a> Krzyżowe granice stref

Należy zrozumieć, że kiedykolwiek end-to-end usługi przecina strefy, możesz udostępniać obecność nie jedną strefę, ale potencjalnie wiele stref.  W związku z tym usługa end-to-end może nie uzyskały żadnych dostępności za pośrednictwem-zonal wdrożeń.

Unikaj wprowadzenia niepożądanych zależności między strefy, które zniesienia zyski dostępności, używając stref dostępności.  Gdy aplikacja składa się z wielu składników, a chcesz zapewnienie odporności na awarie strefy, musi zajmie się do zapewnienia przetrwania wystarczające krytycznych składników w przypadku niepowodzeniu strefy.  Na przykład pojedynczy składnikiem krytycznym dla aplikacji może wpływać na całej aplikacji, jeśli istnieje tylko w strefie innego niż jego strefy.  Ponadto należy również rozważyć przywracania strefy i jak zbierze aplikacji. Umożliwia przeglądanie niektóre najważniejsze i używać ich jako inspirującymi odpowiedzi na pytania jako przemyślenie konkretnego scenariusza.

- Jeśli aplikacja ma dwa składniki, takie jak adres IP i Maszynę wirtualną z dyskiem zarządzanym, są one zagwarantować w strefie 1 i nie będzie przetrwać strefy 2, gdy strefa 1 nie powiodło się z usługą end-to-end Jeśli strefa 1 nie powiodło się.  Nie wielu stref, chyba że lepiej zrozumieć tworzonej tryb potencjalnie niebezpiecznych awarii.

- Jeśli aplikacja ma dwa składniki, takie jak adres IP i maszyny Wirtualnej z dysku, zarządzane i gwarancję strefowo nadmiarowy i odpowiednio strefa 1, usługi end-to-end będzie przetrwać błąd stref strefy 2, strefy 3 i/lub jeśli strefa 1 nie powiodło się.  Jednak utracisz możliwość przeglądanie informacji o kondycji usługi, jeśli są obserwowania będzie wysyłające frontonu.  Należy rozważyć utworzenie szerszej modelu kondycji i wydajności.  Można jednocześnie używać strefowo nadmiarowy i zonal pojęcia rozszerzenia szczegółowe dane i możliwości zarządzania.

- Jeśli aplikacja ma dwa składniki, takie jak strefowo nadmiarowy frontonu modułu równoważenia obciążenia i zestaw skali maszyny wirtualnej między strefy w trzech stref, zasobami w strefach nie wpływ awarii będzie dostępne, ale z usługą end-to-end może ulec pogorszeniu w postaci liczby pojemność podczas awarii strefy. Z perspektywy infrastruktury wdrożenie może po awarii strefy co najmniej jeden i zgłasza to wymienione niżej pytania:
  - Czy wiesz, jak aplikacja powodów, dla których o rodzaju błędów i obniżeniem wydajności?
  - Czy musisz mieć zabezpieczeń w usłudze, aby wymusić funkcjonować w trybie awaryjnym pary regionu, w razie potrzeby?
  - Jak będzie można monitorować, wykrywania i ograniczyć takiej sytuacji? Można skorzystać z diagnostyki standardowy moduł równoważenia obciążenia można rozszerzyć monitorowanie wydajności usługi end-to-end. Należy rozważyć, co jest dostępne i jakie mogą być potrzebne rozszerzeniu pełnego obrazu.

- Strefy można wprowadzać błędy, co ułatwia zrozumienie i zawarte.  Jednak błąd stref nie różni się od innych błędów po przejściu do koncepcji, takie jak limity czasu, ponownych prób i algorytmy wycofywania. Mimo że usługa równoważenia obciążenia Azure zapewnia strefy nadmiarowe ścieżki i próbuje odzyskać szybkie, na poziomie pakietów w czasie rzeczywistym, liczba retransmisji lub reestablishments mogą wystąpić podczas wystąpienia awarii i ważne jest, aby zrozumieć, jak copes aplikacji z błędy. Schemat równoważenia obciążenia będzie przetrwać, ale należy zaplanować dla następujących elementów:
  - W przypadku awarii strefy usługą end-to-end to zrozumieć i jeśli stan jest utracone, jak będzie można odzyskać?
  - Po powrocie z strefy aplikacja zrozumienie, jak bezpiecznie zbieżności?

### <a name="zonalityguidance"></a> Strefowo nadmiarowy i zonal

Strefowo nadmiarowy może udostępniać strefy niezależny od i o tej samej opcji odporność czasu z jednym adresem IP adresie usługi.  Z kolei ona zredukowana złożoność.  Strefowo nadmiarowy również ma mobilności w różnych strefach i można bezpiecznie zasobów w każdej strefie.  Jest także przyszłe dowód w regionach bez dostępności stref, które mogą ograniczać zmian wymaganych po region uzyskania dostępność strefy.  Składnia konfiguracji strefowo nadmiarowy adres IP lub fronton powiedzie się w dowolnym regionie, w tym użytkownicy bez stref dostępności.

Zonal można podać jawne gwarancji do strefy, udostępnianie obecność kondycji strefy. Kojarzenie zonal IP adres lub zonal frontonu modułu równoważenia obciążenia może być pożądane lub uzasadnione atrybut szczególnie w przypadku dołączonych zasobu zonal maszyny Wirtualnej w ramach tej samej strefie.  Lub być może aplikacja wymaga jawnego wiedzy, o której strefie zasób znajduje się w i chcesz jawnie przyczyny o dostępności w osobnym stref.  Użytkownik może ujawnić wielu frontends zonal usługi end-to-end dystrybuowana do strefy (to znaczy na strefy frontends zonal dla wielu skalowania zonal maszyny wirtualnej ustawia).  Jeśli Twoje zonal frontends są publiczne adresy IP, można użyć tych wielu frontends zonal publikowania usługi z [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Lub wielu frontends zonal służy do uzyskania na strefy insights kondycji i wydajności za pośrednictwem innej monitorowanie rozwiązań i udostępnienia ogólnej usługi z strefowo nadmiarowy frontonu. Tylko powinien obsługiwać zasoby zonal z zonal frontends wyrównane do tej samej strefy i uniknąć potencjalnie szkodliwe scenariusze strefy między zonal zasobów.  Zonal zasobów istnieje tylko w regionach, gdzie istnieje stref dostępności.

Nie ma żadnych ogólne wskazówki, że jeden jest lepszym rozwiązaniem niż drugi bez uprzedniego uzyskania informacji o architekturze usługi.

## <a name="limitations"></a>Ograniczenia

- Podczas danych płaszczyzna jest w pełni strefowo nadmiarowy (chyba że określono zonal gwarancji), operacje płaszczyzny kontroli nie są w pełni strefowo nadmiarowy.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [stref dostępności](../availability-zones/az-overview.md)
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md)
- Dowiedz się, jak [Równoważenie obciążenia maszyn wirtualnych w ramach strefy z zonal frontonu przy użyciu standardowych usługi równoważenia obciążenia.](load-balancer-standard-public-zonal-cli.md)
- Dowiedz się, jak [Równoważenie obciążenia maszyn wirtualnych różnych strefach przy użyciu standardowych moduł równoważenia obciążenia z frontonu strefowo nadmiarowy](load-balancer-standard-public-zone-redundant-cli.md)
