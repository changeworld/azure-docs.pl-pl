---
title: Usługa Azure drzwiami frontowymi — równoważenia obciążenia z pakietem dostarczania aplikacji platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci dowiedzieć się o jak usługa Azure zaleca równoważenia obciążenia za pomocą jego application delivery pakietu
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 3d5c0ac068a6644f3499da6c3b642a4a04408370
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879663"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Równoważenie obciążenia za pomocą pakietu dostarczania aplikacji platformy Azure

## <a name="introduction"></a>Wprowadzenie
System Microsoft Azure oferuje wiele globalnym i regionalnym usług zarządzania, sposób dystrybucji ruchu sieciowego i równoważenia obciążenia: Usługi Traffic Manager, Service drzwiami frontowymi, usługa Application Gateway i modułu równoważenia obciążenia.  Wraz z wielu regionów platformy Azure i strefowych architektury, jednocześnie korzystać z tych usług umożliwiają tworzenie niezawodnych, skalowalnych aplikacji o wysokiej wydajności.

![Application Delivery Suite ][1]
 
Te usługi są podzielone na dwie kategorie:
1. **Usług równoważenia obciążenia globalne** takich jak usługa Traffic Manager i drzwiami frontowymi rozdystrybuować ruch od użytkowników końcowych regionalnych zaplecza, chmurach lub nawet hybrydowe usług lokalnych. Równoważenia obciążenia globalne kieruje ruch do najbliższego wewnętrzną bazą danych usługi i reaguje na zmiany w niezawodności usług lub wydajności, aby zachować zawsze włączone, maksymalna wydajność dla użytkowników. 
2. **Regionalne usług równoważenia obciążenia** takie jak standardowego modułu równoważenia obciążenia lub usługi Application Gateway zapewniają możliwość dystrybuować ruch w sieci wirtualne (Vnet) w sieci maszyn wirtualnych (VM) lub punkty końcowe usługi strefowych w obrębie regionu.

Łączenie usług globalnym i regionalnym w aplikacji zapewnia end-to-end niezawodne, wydajny i bezpieczny sposób kierowania ruchu do i z użytkowników do usługi IaaS, PaaS lub usługami lokalnymi. W następnej sekcji opisano każdej z tych usług.

## <a name="global-load-balancing"></a>Globalne równoważenia obciążenia
**Usługa Traffic Manager** zapewnia równoważenie obciążenia globalnego DNS. Ona patrzy na przychodzące żądania DNS i odpowiada za pomocą zaplecza w dobrej kondycji, zgodnie z zasadami routingu, wybranej przez klienta. Dostępne są opcje dla metody routingu:
- Wydajność routingu, aby wysłać obiekt żądający do najbliższego wewnętrznej bazy danych pod kątem opóźnień.
- Priorytet routingu, aby kierować cały ruch do wewnętrznej bazy danych, innych zapleczy jako kopii zapasowej.
- Ważone działanie okrężne routingu, który rozprowadza ruchu w oparciu o wagi, która jest przypisana do poszczególnych pól zaplecza.
- Geograficznego routingu, aby upewnić się, że obiekty żądające znajduje się w określonych regionach geograficznych są kierowane do zaplecza mapowane do tych regionów (na przykład, wszystkie żądania z Hiszpania ma być przekierowywany w regionie centralnym platformy Azure (Francja))
- Podsieć routingu, która umożliwia mapowanie adresu IP zakresu do zaplecza, czemu żądania pochodzące z tych, które będą wysyłane do określonego wewnętrznej bazy danych (na przykład wszystkich użytkowników łączących się z zakresu adresów IP w Centrali firmy powinny pobierać zawartości internetowej innej niż ogólne użytkowników)

Klient łączy się bezpośrednio z tym w wewnętrznej bazie danych. Usługa Azure Traffic Manager wykrywa zaplecza jest w złej kondycji, a następnie przekierowuje klientów do innego wystąpienia dobrej kondycji. Zapoznaj się [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) dokumentacji, aby dowiedzieć się więcej o usłudze.

**Usługa Azure drzwiami frontowymi** zapewnia dynamiczną witrynę sieci Web przyspieszanie (witryn dynamicznych DSA) w tym globalne równoważenia obciążenia HTTP.  Wygląda trasy do najbliższego wewnętrznej bazy danych usługi w przychodzących żądań HTTP / region dla określonej nazwy hosta, ścieżka adresu URL i skonfigurowanych reguł.  
Drzwiami frontowymi kończy żądań HTTP na granicy sieci firmy Microsoft i aktywnie sondy do wykrywania aplikacji lub infrastruktury zmiany kondycji lub opóźnienia.  Drzwiami frontowymi następnie zawsze kieruje ruch do zaplecza (dobrej kondycji) to najszybszy i dostępne. Zobacz drzwiami frontowymi [architekturę routingu](front-door-routing-architecture.md) szczegóły i [metody routingu ruchu](front-door-routing-methods.md) Aby dowiedzieć się więcej o usłudze.

## <a name="regional-load-balancing"></a>Regionalne równoważenia obciążenia
Application Gateway oferuje kontrolera dostarczania aplikacji (ADC) jako usługę, oferuje różne możliwości warstwy 7 równoważenia obciążenia dla danej aplikacji. Umożliwia to klientom optymalizowanie wydajności farmy sieci web dzięki przeniesieniu kończenia żądań SSL mocy procesora CPU do usługi application gateway. Inne możliwości routingu warstwy 7 obejmują-okrężna Dystrybucja ruchu przychodzącego, koligacja sesji na podstawie plików cookie, routing oparty na ścieżkach URL i możliwość hostowania wielu witryn sieci Web za bramą pojedynczej aplikacji. Usługa Application Gateway można skonfigurować jako bramę dostępnego z Internetu, bramę tylko wewnętrzne lub jako kombinację obu tych. Usługa Application Gateway jest w pełni Azure zarządzane, skalowalne i o wysokiej dostępności. Zapewnia ona bogaty zestaw funkcji diagnostyki i rejestrowania, aby uprościć zarządzanie.
Moduł równoważenia obciążenia jest integralną częścią stosu Azure SDN świadczący o wysokiej wydajności i niskich opóźnieniach warstwy 4 równoważenia obciążenia usługi dla wszystkich protokołów UDP i TCP. Zarządza połączeń przychodzących i wychodzących. Można skonfigurować public i internal końcowe ze zrównoważonym obciążeniem i zdefiniuj reguły mapowania połączeń przychodzących do miejsc docelowych w puli zaplecza, zarządzanie za pomocą protokołu TCP i HTTP opcje usługi badania kondycji dostępności usługi.


## <a name="choosing-a-global-load-balancer"></a>Wybranie usługi równoważenia obciążenia globalne
Podczas wybierania globalne modułem równoważenia obciążenia między Traffic Manager i Azure drzwiami frontowymi globalnego routingu, należy rozważyć, co to jest podobne i czym się różni od dwóch usług.   Obie te usługi zapewniają
- **Nadmiarowość wielu regionów geograficznych:** Jeśli jeden region ulegnie awarii, ruch bezproblemowo kierowane do najbliższego regionu bez żadnej interwencji od właściciela aplikacji.
- **Najbliższy region routingu:** Ruch odbywa się automatycznie do najbliższego regionu

</br>W poniższej tabeli opisano różnice między usługi Traffic Manager i Azure drzwiami frontowymi Service:</br>

| Traffic Manager | Azure Front Door Service |
| --------------- | ------------------------ |
|**Każdy protokół:** Ponieważ usługa Traffic Manager działa na poziomie warstwy DNS, może kierować dowolnego typu ruchu sieciowego; HTTP TCP, UDP, itp. | **Przyspieszenie protokołu HTTP:** Za pomocą drzwiami frontowymi jest serwerem proxy w sieci krawędź Microsoft.  W związku z tym żądania HTTP (S), zobacz opóźnienia i przepływności ulepszenia zmniejszenie opóźnienia i negocjacji w protokole SSL i używania gorącej połączeń z AFD do aplikacji.|
|**Lokalne routingu:** Za pomocą routingu na poziomie warstwy DNS, ruch jest przesyłany zawsze punkt punkt.  Routing w biurze oddziału firmy do Twojego lokalnego centrum danych może zająć bezpośrednią ścieżkę; nawet w przypadku Twojej własnej sieci przy użyciu usługi Traffic Manager. | **Niezależną skalowalność:** Ponieważ drzwiami frontowymi współpracuje z żądania HTTP, żądania do różnych ścieżek URL może być przekierowane do różnych wewnętrznych baz danych / regionalnej usługi pul (mikrousług) na podstawie kondycji poszczególnych aplikacji mikrousług i reguł.|
|**Format rozliczeń:** Rozliczanie oparte na systemie DNS umożliwia skalowanie z użytkownikami i usług z większej liczby użytkowników, płaskowyżach w celu zmniejszenia kosztów na stronie większego użycia. |**Wbudowane zabezpieczenia:** Drzwiami frontowymi włącza reguły, takie jak ograniczanie szybkości i umieszczanie listy ACL adresów IP pozwala chronić zaplecza, zanim ruch trafia aplikacji. 

</br>Ze względu na wydajność, sprawność działania i korzyści w zakresie zabezpieczeń do obciążeń HTTP przy wejściu zalecamy klientom drzwiami frontowymi potrzeby ich obciążeń HTTP.    Usługa Traffic Manager i drzwiami frontowymi można równolegle do obsługi całego ruchu dla aplikacji. 

## <a name="building-with-azures-application-delivery-suite"></a>Kompilowanie z pakietem dostarczania aplikacji platformy Azure 
Zaleca się, że wszystkie witryny sieci Web, interfejsów API usługi jako geograficznie nadmiarowe i dostarczania ruchu na swoim użytkownikom z najbliższego (najmniejszego opóźnienia) lokalizacji, aby je zawsze, gdy jest to możliwe.  Łączenie usługi Traffic Manager, Service drzwiami frontowymi, usługa Application Gateway i modułu równoważenia obciążenia umożliwia tworzenie nadmiarowy geograficznie i zonally Aby zmaksymalizować niezawodność, skalowalność i wydajność.

Na poniższym diagramie opisujemy usługą przykład, która używa kombinacji tych usług do tworzenia usługi sieci web globalnego.   W tym przypadku architekta został użyty usługi Traffic Manager kierować do zaplecza globalne dostarczanie plików i obiektów, podczas korzystania z drzwiami frontowymi globalnie kierować ścieżki adresu URL, zgodne ze wzorcem/store / * w usłudze one po migracji do usługi App Service podczas routingu wszystkich innych żądania do regionalnych bram Application Gateway.

W regionie, dotyczące ich usługi IaaS Deweloper aplikacji zdecydował, że wszystkie adresy URL zgodne ze wzorcem/obrazy / * są obsługiwane z dedykowanej puli maszyn wirtualnych, które różnią się od reszty kolektywu serwerów sieci web.

Ponadto domyślnej puli maszyn wirtualnych obsługujących zawartość dynamiczną musi komunikować się z wewnętrznej bazy danych, który znajduje się w klastrze o wysokiej dostępności. Całe wdrożenie jest skonfigurowane za pomocą usługi Azure Resource Manager.

Na poniższym diagramie przedstawiono architekturę tego scenariusza:

![Pakiet dostarczania aplikacji Szczegółowa architektura][2] 

> [!NOTE]
> W tym przykładzie jest tylko jeden z wielu możliwych konfiguracji usługi równoważenia obciążenia, oferowanych przez platformę Azure. Usługi Traffic Manager, drzwiami frontowymi, usługa Application Gateway i modułu równoważenia obciążenia mogą być mieszane i dopasowywane zależności potrzeb równoważenia obciążenia. Na przykład jeśli odciążanie protokołu SSL lub nie jest konieczne przetwarzanie warstwy 7, moduł równoważenia obciążenia można zamiast Application Gateway.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
