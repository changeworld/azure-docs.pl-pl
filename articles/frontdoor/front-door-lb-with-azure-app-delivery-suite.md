---
title: Drzwi frontowe platformy Azure — równoważenie obciążenia za pomocą pakietu dostarczania aplikacji platformy Azure | Dokumenty firmy Microsoft
description: Ten artykuł ułatwia zapoznanie się z zaleceniami platformy Azure dotyczącymi równoważenia obciążenia za pomocą pakietu dostarczania aplikacji
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
ms.openlocfilehash: 44af14a01e7b045b7abb6a84db89a67f3dd22445
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875286"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Równoważenie obciążenia za pomocą pakietu dostarczania aplikacji platformy Azure

## <a name="introduction"></a>Wprowadzenie
Platforma Microsoft Azure oferuje wiele usług globalnych i regionalnych do zarządzania sposób dystrybucji ruchu sieciowego i równoważenia obciążenia: Menedżer ruchu, drzwiami frontowymi, bramą aplikacji i modułem równoważenia obciążenia.  Wraz z wieloma regionami i architekturą strefową platformy Azure, korzystanie z tych usług razem umożliwia tworzenie niezawodnych, skalowalnych aplikacji o wysokiej wydajności.

![Pakiet dostarczania aplikacji ][1]
 
Usługi te są podzielone na dwie kategorie:
1. **Globalne usługi równoważenia obciążenia,** takie jak Traffic Manager i Front Door, rozdzielają ruch użytkowników końcowych między regionalnymi zapleczem, chmurami, a nawet hybrydowymi usługami lokalnymi. Globalne równoważenie obciążenia kieruje ruch do najbliższej wewnętrznej bazy danych i reaguje na zmiany w niezawodności i wydajności usług, aby utrzymać stale włączającą, maksymalną wydajność dla użytkowników. 
2. **Regionalne usługi równoważenia obciążenia,** takie jak standardowy moduł równoważenia obciążenia lub brama aplikacji, umożliwiają dystrybucję ruchu w sieciach wirtualnych (VNETs) na maszynach wirtualnych (VM) lub punktach końcowych usługi strefowej w regionie.

Połączenie usług globalnych i regionalnych w aplikacji zapewnia kompleksowy niezawodny, wydajny i bezpieczny sposób kierowania ruchu do i z użytkowników do usług IaaS, PaaS lub usług lokalnych. W następnej sekcji opisujemy każdą z tych usług.

## <a name="global-load-balancing"></a>Globalne równoważenie obciążenia
**Traffic Manager** zapewnia globalne równoważenie obciążenia DNS. Analizuje przychodzące żądania DNS i odpowiada za pomocą dobrej kondycji wewnętrznej bazy danych, zgodnie z zasadami routingu wybranymi przez klienta. Opcje dla metod routingu to:
- Routing wydajności, aby wysłać żądającego do najbliższej wewnętrznej bazy danych pod względem opóźnienia.
- Routing priorytetu, aby skierować cały ruch do wewnętrznej bazy danych, z innymi zapleczem jako kopią zapasową.
- Ważona routing okrężny, który rozdziela ruch na podstawie wagi przypisanej do każdej wewnętrznej bazy danych.
- Routing geograficzny w celu zapewnienia, że osoby żądające znajdujące się w określonych regionach geograficznych są kierowane do zaplecza mapowanych do tych regionów (na przykład wszystkie żądania z Hiszpanii powinny być kierowane do regionu France Central Azure)
- Routing podsieci, który umożliwia mapowanie zakresów adresów IP do zaplecza, dzięki czemu żądania pochodzące z nich będą wysyłane do określonej wewnętrznej bazy danych (na przykład wszyscy użytkownicy łączący się z zakresu adresów IP głównej firmy powinni uzyskać inną zawartość sieci Web niż użytkownicy generalni)

Klient łączy się bezpośrednio z tym zapleczem. Usługa Azure Traffic Manager wykrywa, gdy wewnętrznej bazy danych jest w złej kondycji, a następnie przekierowuje klientów do innego wystąpienia w dobrej kondycji. Więcej informacji na temat usługi można znaleźć w dokumentacji usługi [Azure Traffic Manager.](../traffic-manager/traffic-manager-overview.md)

**Usługa Azure Front Door** zapewnia dynamiczne przyspieszanie witryny sieci Web (DSA), w tym globalne równoważenie obciążenia HTTP.  Analizuje przychodzące trasy żądań HTTP do najbliższej wewnętrznej bazy danych /regionu usługi dla określonej nazwy hosta, ścieżki adresu URL i skonfigurowanych reguł.  
Drzwi frontowe kończą żądania HTTP na krawędzi sieci firmy Microsoft i aktywnie sondują w celu wykrycia zmian kondycji lub opóźnienia aplikacji lub infrastruktury.  Drzwi frontowe następnie zawsze kieruje ruch do najszybszego i dostępnego (zdrowego) zaplecza. Aby dowiedzieć się więcej o usłudze, zapoznaj się ze szczegółami [architektury routingu](front-door-routing-architecture.md) drzwiami frontowymi i [metodami routingu ruchu.](front-door-routing-methods.md)

## <a name="regional-load-balancing"></a>Regionalne równoważenie obciążenia
Brama aplikacji udostępnia kontroler dostarczania aplikacji (ADC) jako usługę, oferując różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Umożliwia klientom optymalizację wydajności farmy internetowej przez odciążanie obciążającego procesora TLS zakończenia do bramy aplikacji. Inne funkcje routingu warstwy 7 obejmują dystrybucję ruchu przychodzącego okrężną, koligacji sesji opartej na plikach cookie, routing oparty na ścieżce adresów URL oraz możliwość hostowania wielu witryn sieci Web za jedną bramą aplikacji. Brama aplikacji może być skonfigurowana jako brama internetowa, brama wewnętrzna lub kombinacja obu tych bram. Brama aplikacji jest w pełni zarządzana, skalowalna i wysoce dostępna na platformie Azure. Zapewnia ona bogaty zestaw funkcji diagnostyki i rejestrowania, aby uprościć zarządzanie.
Moduł równoważenia obciążenia jest integralną częścią stosu SDN platformy Azure, zapewniając wysokiej wydajności, niskie opóźnienia warstwy 4 równoważenia obciążenia usług dla wszystkich protokołów UDP i TCP. Zarządza połączeniami przychodzącymi i wychodzącymi. Możesz skonfigurować publiczne i wewnętrzne punkty końcowe ze zrównoważonym obciążeniem i zdefiniować reguły mapowania połączeń przychodzących do miejsc docelowych w puli zaplecza, wykorzystując opcje badania kondycji protokołu TCP i HTTP do zarządzania dostępnością usługi.


## <a name="choosing-a-global-load-balancer"></a>Wybór globalnego modułu równoważenia obciążenia
Wybierając globalny moduł równoważenia obciążenia między usługą Traffic Manager a usługą Azure Front Door dla routingu globalnego, należy wziąć pod uwagę, co jest podobne i co różni się od tych dwóch usług.   Obie usługi zapewniają
- **Nadmiarowość wielu geodezjów:** Jeśli jeden region ustępuje, ruch płynnie trasy do najbliższego regionu bez interwencji właściciela aplikacji.
- **Routing najbliższego regionu:** Ruch jest automatycznie kierowany do najbliższego regionu

</br>W poniższej tabeli opisano różnice między usługą Traffic Manager a drzwiami frontowymi platformy Azure:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Dowolny protokół:** Ponieważ usługa Traffic Manager działa w warstwie DNS, można kierować dowolny typ ruchu sieciowego; HTTP, TCP, UDP itp. | **Akceleracja HTTP:** Z front door ruchu jest proxied na skraju sieci Firmy Microsoft.  W związku z tym żądania HTTP(S) zobacz opóźnienia i przepustowość ulepszenia zmniejszenie opóźnienia dla negocjacji TLS i przy użyciu gorących połączeń z afd do aplikacji.|
|**Routing lokalny:** W warstwie DNS ruch odbywa się z punktu do momentu.  Routing z oddziału do lokalnego centrum danych może przebiegać bezpośrednio; nawet we własnej sieci za pomocą Usługi Traffic Manager. | **Niezależna skalowalność:** Ponieważ drzwiami frontowymi współpracuje z żądaniem HTTP, żądania do różnych ścieżek adresów URL mogą być kierowane do różnych pul wewnętrznej bazy danych / regionalnych usług (mikrousług) na podstawie reguł i kondycji każdej mikrousług aplikacji.|
|**Format rozliczeń:** Płatności oparte na systemie DNS są skalowane z użytkownikami i usługami z większą liczeną licze, aby zmniejszyć koszty przy wyższym użyciu. |**Zabezpieczenia wbudowane:** Drzwi frontowe umożliwia reguły, takie jak ograniczanie szybkości i IP ACL-ing, aby umożliwić ochronę zaplecza, zanim ruch dotrze do aplikacji. 

</br>Ze względu na wydajność, funkcjonalność i korzyści związane z zabezpieczeniami obciążeń HTTP za pomocą drzwi frontowych zalecamy klientom używanie drzwi y frontowej dla swoich obciążeń HTTP.    Usługa Traffic Manager i drzwi frontowe mogą być używane równolegle do obsługi całego ruchu dla aplikacji. 

## <a name="building-with-azures-application-delivery-suite"></a>Tworzenie za pomocą pakietu dostarczania aplikacji platformy Azure 
Firma Microsoft zaleca, aby wszystkie witryny sieci Web, interfejsy API, usługi były geograficznie nadmiarowe i dostarczać do nich ruch z najbliższej lokalizacji (najniższe opóźnienie) do nich, gdy tylko jest to możliwe.  Połączenie usług z usługi z usługi Menedżera ruchu, drzwi frontowych, bramy aplikacji i moduł równoważenia obciążenia umożliwia tworzenie geograficznie i zochętnie zbędne, aby zmaksymalizować niezawodność, skalę i wydajność.

Na poniższym diagramie opisano przykładową usługę, która używa kombinacji wszystkich tych usług do tworzenia globalnej usługi sieci web.   W takim przypadku architekt użył usługi Traffic Manager do kierowania do globalnych zaplecza dla dostarczania plików i obiektów, podczas gdy za pomocą drzwi frontowych do globalnie trasy ścieżek ADRESOWYCH, które pasują do wzorca /store/* do usługi, którą zostały zmigrowane do usługi App Service podczas routingu wszystkich innych żądań do regionalnych bram aplikacji.

W regionie dla ich usługi IaaS deweloper aplikacji zdecydował, że wszystkie adresy URL pasujące do wzorca /images/* są obsługiwane z dedykowanej puli maszyn wirtualnych, które różnią się od reszty farmy sieci web.

Ponadto domyślna pula maszyn wirtualnych obsługująca zawartość dynamiczną musi rozmawiać z bazą danych zaplecza, która jest hostowana w klastrze o wysokiej dostępności. Całe wdrożenie jest skonfigurowane za pośrednictwem usługi Azure Resource Manager.

Na poniższym diagramie przedstawiono architekturę tego scenariusza:

![Szczegółowa architektura pakietu dostarczania aplikacji][2] 

> [!NOTE]
> W tym przykładzie jest tylko jedną z wielu możliwych konfiguracji usług równoważenia obciążenia, które oferuje platforma Azure. Menedżer ruchu, drzwi frontowe, brama aplikacji i moduł równoważenia obciążenia można mieszać i dopasowywać do potrzeb związanych z równoważeniem obciążenia. Na przykład jeśli odciążanie protokołu TLS/SSL lub przetwarzanie warstwy 7 nie jest konieczne, moduł równoważenia obciążenia może być używany zamiast bramy aplikacji.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
