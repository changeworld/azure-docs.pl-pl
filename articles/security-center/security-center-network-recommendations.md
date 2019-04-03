---
title: Ochrona zasobów sieciowych w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Adresy tego dokumentu, zalecenia w usłudze Azure Security Center, które ułatwiają ochronę zasobów sieci platformy Azure i pozostają zgodne z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: monhaber
ms.openlocfilehash: cca1962e5146300cc376fab4bcb1bf0876acec6c
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863155"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Ochrona zasobów sieciowych w usłudze Azure Security Center
Usługa Azure Security Center w sposób ciągły analizuje stan zabezpieczeń zasobów platformy Azure najlepsze rozwiązania dotyczące zabezpieczeń sieci. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek harden i chronić zasoby.

W tym artykule opisano zaleceń, które są stosowane do zasobów platformy Azure z punktu widzenia zabezpieczeń sieci. Sieć Centrum zalecenia dotyczące, wokół następnej generacji zapory, sieciowe grupy zabezpieczeń, reguły nadmiernie ograniczająca ruch przychodzący dostępu do maszyn wirtualnych JIT i. Aby uzyskać listę zaleceń dotyczących sieci i akcji korygowania, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> **Sieć** strona pozwala głębszej analizy usługi kondycji zasobów platformy Azure z punktu widzenia sieci. Mapa sieci oraz adaptacyjne kontrolki sieci są dostępne w usłudze Azure Security Center w warstwie standardowa tylko. [Jeśli używasz warstwy bezpłatnej, możesz kliknąć przycisk, aby **Wyświetl sieć starszych** otrzymasz zaleceń dotyczących zasobów sieci](#legacy-networking).
>

**Sieć** bloku zawiera przegląd sekcje głębokiego możesz od razu, aby uzyskać więcej informacji na temat kondycji zasobów sieciowych:

- Mapa sieci (tylko w warstwie standardowej usługi Azure Security Center)
- Zalecenia dotyczące zabezpieczeń sieci.
- Starsza wersja **sieć** bloku (poprzedniego bloku sieci) 
 
![Okienko sieci](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Mapa sieci
Mapa interaktywne sieci zawiera graficzne przedstawienie z nakładki zabezpieczeń, zapewniając zalecenia i szczegółowych informacji dotyczących wzmacniania ochrony zasobów sieciowych. Za pomocą mapy widać topologii sieci obciążeń platformy Azure, połączenia między maszynami wirtualnymi i podsieciami i możliwości, aby przejść do szczegółów z mapy do określonych zasobów i zalecenia dotyczące tych zasobów.

Aby otworzyć program Tablica sieci:

1. W usłudze Security Center w obszarze higieny zabezpieczeń zasobu, wybierz **sieć**.
2. W obszarze **mapy sieci** kliknij **Zobacz topologii**.
 
Wyświetla domyślny widok mapy topologii:
- Subskrypcje, które wybrano na platformie Azure. Mapa obsługuje wiele subskrypcji.
- Maszyny wirtualne, podsieci i sieci wirtualne typu zasobu usługi Resource Manager (klasycznym Azure zasoby nie są obsługiwane)
- Wirtualne sieci równorzędne
- Tylko zasoby, które mają [sieci zalecenia](security-center-recommendations.md) o wysokiej i średniej ważności  
- Zasoby dostępnego z Internetu
- Mapa jest zoptymalizowany dla subskrypcji, które wybrano na platformie Azure. W przypadku zmodyfikowania wybór mapy jest działaniem i ponownie zoptymalizowane pod kątem na podstawie nowych ustawień.  

![Mapy topologii sieci](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Informacje o mapie sieci

Mapa sieci można wyświetlić zasobów platformy Azure w **topologii** widoku i **ruchu** widoku.

### <a name="the-topology-view"></a>Widok topologii

W **topologii** widoku mapy sieci, można wyświetlić poniższe szczegółowe informacje dotyczące zasobów sieciowych:
- W wewnętrznego okręgu możesz zobaczyć wszystkie sieci wirtualne w ramach wybranej subskrypcji, okrąg dalej jest wszystkie podsieci się zewnętrznego okręgu wszystkich maszyn wirtualnych.
- Wiersze, łączenie z zasobami w mapie pozwalają wiedzieć, jakie zasoby są skojarzone ze sobą i struktury sieci platformy Azure. 
- Wskaźniki ważności umożliwiają szybko zapoznaj się z omówieniem, którego zasoby mają Otwórz zalecenia z usługi Security Center.
- Możesz kliknąć dowolny z zasobów, aby przejść do nich i wyświetlić szczegóły tego zasobu i zalecenia bezpośrednio, a w kontekście sieci mapy.  
- Jeśli jest ich zbyt wiele zasobów, które są wyświetlane na mapie, usługa Azure Security Center korzysta z jego algorytmu do inteligentnego klaster zasobów, wyróżnianie zasoby, które znajdują się w najbardziej krytyczny stan i mieć najbardziej wysokiej ważności zaleceń. 

Ponieważ mapy jest interakcyjny i dynamiczne, każdy węzeł jest aktywne i widoku można zmienić w oparciu filtrów:

1. Możesz zmodyfikować wyświetlanych na mapie sieci przy użyciu filtrów u góry. Możesz skupić się mapy na podstawie:
   -  **Kondycja zabezpieczeń**: Można filtrować mapy, w oparciu o ważności (wysoki, Średni, niski) zasobów platformy Azure.
   - **Zalecenia dotyczące**: Można wybrać, które zasoby są wyświetlane, oparte na zalecenia, które są aktywne w odniesieniu do tych zasobów. Na przykład można wyświetlić tylko tych zasobów, dla których usługa Security Center zaleca Włączanie sieciowych grup zabezpieczeń.
   - **Sieci stref**: Domyślnie mapa zawiera tylko zasoby dostępnego z Internetu, można wybrać także wewnętrznych maszyn wirtualnych.
 
2. Możesz kliknąć pozycję **resetowania** w lewym górnym rogu w dowolnym momencie wrócić mapy do stanu domyślnego.

Aby przejść do zasobu:
1. Po wybraniu określonego zasobu na mapie, w okienku po prawej stronie zostanie otwarty oraz udostępnia ogólne informacje o zasobie, rozwiązania zabezpieczeń połączone, jeśli istnieją i zalecenia, które są odpowiednie do zasobu. Jest to ten sam typ zachowanie dla każdego typu zasobu, którą wybierzesz. 
2. Po najechaniu kursorem na węzeł na mapie, możesz wyświetlić ogólne informacje o zasobie, łącznie z subskrypcji, typ zasobu i grupy zasobów.
3. Użyj linku, aby powiększenie etykietki narzędzia i ponowne skupienie mapy, w tym określonym węźle. 
4. Aby ponowne skupienie Mapa od określonego węzła, pomniejszyć.

### <a name="the-traffic-view"></a>Widok ruchu

**Ruchu** widok zawiera mapę wszystkich możliwych ruch między zasobami. Zapewnia to, że za pomocą wizualnej mapy wszystkich reguł, że został skonfigurowany, który zdefiniujesz zasoby mogą komunikować się za pomocą którego. Dzięki temu można zobaczyć istniejącą konfigurację grup zabezpieczeń sieci, a także szybko zidentyfikować możliwe konfiguracje ryzykowne w ramach Twoich obciążeń.

### <a name="uncover-unwanted-connections"></a>Odkryj niechciane połączenia

Siła tego widoku jest możliwość Pokaż te dozwolone połączenia wraz z luk w zabezpieczeniach, które istnieją, aby można było używać ten szereg danymi, aby wykonać niezbędne zaostrzanie poziomu zabezpieczeń na zasoby. 

Na przykład użytkownik może wykryć komunikację dwóch maszyn, możesz nie wiedzieć, umożliwiając lepsze odizolować obciążenia i podsieci.

### <a name="investigate-resources"></a>Badanie zasobów

Aby przejść do zasobu:
1. Po wybraniu określonego zasobu na mapie, w okienku po prawej stronie zostanie otwarty oraz udostępnia ogólne informacje o zasobie, rozwiązania zabezpieczeń połączone, jeśli istnieją i zalecenia, które są odpowiednie do zasobu. Jest to ten sam typ zachowanie dla każdego typu zasobu, którą wybierzesz. 
2. Kliknij przycisk **ruchu** Aby wyświetlić listę możliwych ruchu wychodzącego i przychodzącego zasobu — jest to kompleksowe, który może komunikować się z zasobem i który może komunikować się z, a także za pośrednictwem protokołów i portów. Na przykład po wybraniu maszyny Wirtualnej, wszystkie maszyny wirtualne mogą komunikować są wyświetlane, a po wybraniu podsieci, są wyświetlane wszystkie podsieci, które może komunikować się z.

**Te dane są na podstawie analizy sieciowych grup zabezpieczeń, a także zaawansowane algorytmy, które analizują wiele reguł, aby zrozumieć ich rozjazdów i interakcje uczenia maszynowego.** 

![Mapa ruchu w sieci](./media/security-center-network-recommendations/network-map-traffic.png)

## Starsze sieci <a name ="legacy-networking"></a>

Jeśli nie masz Security Center w warstwie standardowa w tej sekcji opisano sposób wyświetlania bezpłatne zalecenia dotyczące sieci.

Dostęp do informacji, w bloku sieć, kliknij przycisk **Wyświetl sieć starszych**. 

![Starsze sieci](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Sekcja Internet facing endpoints (Punkty końcowe umożliwiające dostęp do Internetu)
W **punktów końcowych z Internetem** sekcji, możesz zobaczyć maszyn wirtualnych, które są aktualnie skonfigurowane z Internetem, punkt końcowy i jego stan.

Ta tabela zawiera nazwę punktu końcowego, dostępny adres IP i bieżący stan ważności dla sieciowej grupy zabezpieczeń i zalecenia dotyczące zapory nowej generacji. W tabeli są posortowane według ważności.

### <a name="networking-topology-section"></a>Sekcja Topologia sieci
**Topologia sieci** sekcja zawiera hierarchiczny widok zasobów.

Ta tabela jest sortowana według ważności (maszyny wirtualne i podsieci).

W tym widoku topologii pierwszy poziom Wyświetla sieci wirtualnych. Drugi Wyświetla obejmuje podsieci, natomiast trzeci poziom przedstawia maszyny wirtualne, które należą do tych podsieci. Prawa kolumna przedstawia bieżący stan zalecenia dotyczące grupy zabezpieczeń sieci dla tych zasobów.

Trzeci poziom przedstawia maszyny wirtualne, co jest podobne do powyższego opisu. Możesz kliknąć dowolny zasób, aby dowiedzieć się więcej lub zastosować wymagane kontroli lub konfigurację zabezpieczeń.

## <a name="network-recommendations"></a>Zalecenia dotyczące sieci

|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|Maszyna|40|Włącz sieciowe grupy zabezpieczeń na maszynach wirtualnych|Włącz sieciowe grupy zabezpieczeń do kontrolowania dostępu do sieci maszyn wirtualnych.|
|Podsieć|35|Włącz sieciowe grupy zabezpieczeń w podsieciach |Włącz sieciowe grupy zabezpieczeń kontrolować dostęp do zasobów wdrożonych w podsieci sieci.|
|Maszyna|30|Stosowanie kontroli dostępu just in time do sieci|Stosuje się tylko w kontroli dostępu maszyn wirtualnych czas trwale zablokować dostęp do wybranych portów i włączyć autoryzowanych użytkowników otworzyć je w ten sam mechanizm, jak i przez ograniczony czas.|
|Maszyna|20|Ogranicz dostęp za pośrednictwem punktu końcowego mającego połączenie z Internetem|Utrwalanie zezwolić grupy zabezpieczeń sieci z Internetem maszyn wirtualnych przez ograniczenie dostępu do istniejącej reguły.|
|Maszyna|10|Dodawanie zapory nowej generacji|Dodaj rozwiązanie nowej następnej generacji zapory (NGFW) w celu zapewnienia lepszej ochrony maszyn wirtualnych z Internetem.|
|Maszyna|5|Kierowanie ruchu przez sieci bramy tylko zapory|Aby można było ukończyć wdrożenie rozwiązania zapory następnej generacji, ruch do chronionego kontakt z Internetem maszyn wirtualnych należy używały tylko kolejnego rozwiązania zapory generacji.|
|VNet|5|Włączanie standardu ochrony przed atakami DDoS|Aplikacje z publicznych adresów IP w tych sieciach wirtualnych nie są chronione przy użyciu standardowych usługi DDOS protection. Zaleca się, aby umożliwić włączanie łagodzenia pojemności sieci i ataki protokołu.|
|Maszyna|10|Dodawanie zapory nowej generacji|Dodaj rozwiązanie nowej następnej generacji zapory (NGFW) w celu zapewnienia lepszej ochrony maszyn wirtualnych z Internetem.|
|Maszyna|5|Kierowanie ruchu przez sieci bramy tylko zapory|Aby można było ukończyć wdrożenie rozwiązania zapory następnej generacji, ruch do chronionego kontakt z Internetem maszyn wirtualnych należy używały tylko kolejnego rozwiązania zapory generacji.|
Sieć wirtualna|5|Włączanie standardu ochrony przed atakami DDoS|Aplikacje z publicznych adresów IP w tych sieciach wirtualnych nie są chronione przy użyciu standardowych usługi DDOS protection. Zaleca się, aby umożliwić włączanie łagodzenia pojemności sieci i ataki protokołu.|
## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn wirtualnych w usłudze Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrona aplikacji w usłudze Azure Security Center](security-center-application-recommendations.md)
* [Ochrona usługi Azure SQL w usłudze Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
