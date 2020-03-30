---
title: Ochrona zasobów sieciowych w usłudze Azure Security Center
description: Ten dokument zawiera zalecenia zawarte w usłudze Azure Security Center, które pomagają chronić zasoby sieci platformy Azure i zachować zgodność z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 792b95b120f67afcd360730acbd783a3071388b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77431489"
---
# <a name="protect-your-network-resources"></a>Ochrona zasobów sieciowych
Usługa Azure Security Center stale analizuje stan zabezpieczeń zasobów platformy Azure pod kątem najlepszych rozwiązań dotyczących zabezpieczeń sieci. Gdy usługa Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które prowadzą użytkownika przez proces konfigurowania potrzebnych formantów w celu wzmocnienia i ochrony zasobów.

W tym artykule opisano stronę **Sieć** w sekcji zabezpieczeń zasobów w Centrum zabezpieczeń.

Aby uzyskać pełną listę zaleceń dotyczących sieci, zobacz [Zalecenia dotyczące sieci](recommendations-reference.md#recs-network).

Ten artykuł dotyczy zaleceń dotyczących zasobów platformy Azure z punktu widzenia zabezpieczeń sieci. Zalecenia dotyczące sieci koncentrują się wokół zapór nowej generacji, grup zabezpieczeń sieciowych, dostępu do maszyny Wirtualnej JIT, zbyt permisywnych reguł ruchu przychodzącego i innych. Aby uzyskać listę zaleceń dotyczących sieci i akcji korygujących, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> Strona Sieć umożliwia głębokie **zagłębienie** się w kondycję zasobów platformy Azure z punktu widzenia sieci. Mapa sieci i kontrolki sieci adaptacyjne są dostępne tylko dla warstwy standardowej usługi Azure Security Center. [Jeśli korzystasz z warstwy bezpłatnej, możesz kliknąć przycisk, aby **wyświetlić starszą sieć** i otrzymać rekomendacje zasobów sieciowych](#legacy-networking).
>

Strona Sieć zawiera **omówienie** sekcji, które można dogłębnie zagłębić, aby uzyskać więcej informacji na temat kondycji zasobów sieciowych:

- Mapa sieci (tylko warstwa Standard usługi Azure Security Center)
- Adaptacyjne wzmocnienie zabezpieczeń sieci
- Zalecenia dotyczące zabezpieczeń sieci.
- Starsza **karta sieciowa** (poprzednia przywiązka sieci) 
 
[![Okienko sieci](./media/security-center-network-recommendations/networking-pane.png)](./media/security-center-network-recommendations/networking-pane.png#lightbox)

## <a name="network-map"></a>Mapa sieci
Interaktywna mapa sieci zapewnia graficzny widok z nakładkami zabezpieczeń, zawierający zalecenia i szczegółowe informacje dotyczące wzmacniania zasobów sieciowych. Za pomocą mapy można zobaczyć topologii sieci obciążeń platformy Azure, połączenia między maszynami wirtualnymi i podsieci i możliwość przechodzenia do szczegółów z mapy do określonych zasobów i zalecenia dla tych zasobów.

Aby otworzyć mapę sieci:

1. W obszarze Centrum zabezpieczeń w obszarze Higiena zabezpieczeń zasobów wybierz pozycję **Sieć**.
2. W obszarze **Mapa sieci** kliknij pozycję **Zobacz topologię**.
 
Wyświetlany jest domyślny widok mapy topologii:

- Subskrypcje wybrane na platformie Azure. Mapa obsługuje wiele subskrypcji.
- Maszyny wirtualne, podsieci i sieci wirtualne typu zasobu Usługi Resource Manager (klasyczne zasoby platformy Azure nie są obsługiwane)
- Sieci wirtualne w sieciach wirtualnych typu peered
- Tylko zasoby, które mają [zalecenia sieciowe](security-center-recommendations.md) o wysokim lub średnim nasileniu  
- Zasoby internetowe
- Mapa jest zoptymalizowana pod kątem subskrypcji wybranych na platformie Azure. Jeśli zmodyfikujesz wybór, mapa zostanie ponownie obliczona i ponownie zoptymalizowana na podstawie nowych ustawień.  

[![Mapa topologii sieci](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Opis mapy sieci

Mapa sieci może wyświetlać zasoby platformy Azure w widoku **topologii** i widoku **Ruchu.** 

### <a name="the-topology-view"></a>Widok topologii

W widoku **Topologia** mapy sieci można wyświetlić następujące szczegółowe informacje na temat zasobów sieciowych:

- W kręgu wewnętrznym można zobaczyć wszystkie sieci wirtualne w wybranych subskrypcjach, następny okrąg to wszystkie podsieci, okrąg zewnętrzny to wszystkie maszyny wirtualne.
- Wiersze łączące zasoby na mapie informujące o tym, które zasoby są ze sobą skojarzone i jak jest zorganizowana sieć platformy Azure. 
- Użyj wskaźników ważności, aby szybko uzyskać przegląd zasobów, które mają otwarte zalecenia z Centrum zabezpieczeń.
- Można kliknąć dowolny z zasobów, aby przejść do nich i wyświetlić szczegóły tego zasobu i jego zalecenia bezpośrednio oraz w kontekście mapy sieci.  
- Jeśli na mapie jest wyświetlanych zbyt wiele zasobów, usługa Azure Security Center używa własnego algorytmu do inteligentnego klastrowania zasobów, wyróżniając zasoby, które są w najbardziej krytycznym stanie i mają najbardziej zalecenia dotyczące ważności. 

Ponieważ mapa jest interaktywna i dynamiczna, każdy węzeł można kliknąć, a widok może się zmieniać na podstawie filtrów:

1. To, co widzisz na mapie sieci, można zmodyfikować za pomocą filtrów u góry. Mapę można skupić na:

   -  **Kondycja zabezpieczeń:** można filtrować mapę na podstawie ważności (wysoki, średni, niski) zasobów platformy Azure.
   - **Zalecenia:** Można wybrać, które zasoby są wyświetlane na podstawie których zalecenia są aktywne dla tych zasobów. Można na przykład wyświetlić tylko zasoby, dla których Usługa Security Center zaleca włączenie sieciowych grup zabezpieczeń.
   - **Strefy sieciowe:** Domyślnie na mapie wyświetlane są tylko zasoby skierowane do Internetu, można również wybrać wewnętrzne maszyny wirtualne.
 
2. W dowolnym momencie możesz kliknąć **przycisk Resetuj** w lewym górnym rogu, aby przywrócić mapę do stanu domyślnego.

Aby przejść do szczegółów zasobu:

1. Po wybraniu określonego zasobu na mapie zostanie otwarte prawe okienko i znajdziesz ogólne informacje o zasobie, połączonych rozwiązaniach zabezpieczeń, jeśli istnieją, oraz zalecenia dotyczące zasobu. Jest to ten sam typ zachowania dla każdego typu zasobu, który wybierzesz. 
2. Po umieszczeniu wskaźnika myszy na węźle na mapie można wyświetlić ogólne informacje o zasobie, w tym subskrypcję, typ zasobu i grupę zasobów.
3. Użyj łącza, aby powiększyć etykietkę narzędzia i ponownie skupić mapę na tym konkretnym węźle. 
4. Aby ponownie skupić mapę z dala od określonego węzła, pomniejsz.

### <a name="the-traffic-view"></a>Widok Ruch drogowy

**Widok Ruch** zapewnia mapę wszystkich możliwych ruchów między zasobami. Zapewnia to wizualną mapę wszystkich skonfigurowanych reguł, które określają, które zasoby mogą komunikować się z kim. Dzięki temu można zobaczyć istniejącą konfigurację grup zabezpieczeń sieci, a także szybko zidentyfikować możliwe ryzykowne konfiguracje w ramach obciążeń.

### <a name="uncover-unwanted-connections"></a>Odkryj niechciane połączenia

Siła tego widoku polega na jego zdolności do pokazywania tych dozwolonych połączeń wraz z istniejącymi lukami, dzięki czemu można użyć tego przekroju danych do wykonania niezbędnego zaostrzenia zasobów. 

Na przykład można wykryć dwie maszyny, które nie były świadome może komunikować się, co pozwala lepiej izolować obciążeń i podsieci.

### <a name="investigate-resources"></a>Badanie zasobów

Aby przejść do szczegółów zasobu:

1. Po wybraniu określonego zasobu na mapie zostanie otwarte prawe okienko i znajdziesz ogólne informacje o zasobie, połączonych rozwiązaniach zabezpieczeń, jeśli istnieją, oraz zalecenia dotyczące zasobu. Jest to ten sam typ zachowania dla każdego typu zasobu, który wybierzesz. 
2. Kliknij **ruch,** aby wyświetlić listę możliwych ruchu wychodzącego i przychodzącego w zasobie — jest to pełna lista osób, które mogą komunikować się z zasobem i z kim może się komunikować oraz za pośrednictwem których protokoły i porty. Na przykład po wybraniu maszyny Wirtualnej wyświetlane są wszystkie maszyny wirtualne, z którymi może się komunikować, a po wybraniu podsieci zostaną wyświetlone wszystkie podsieci, z którymi może się komunikować.

**Dane te są oparte na analizie grup zabezpieczeń sieciowych, a także zaawansowanych algorytmów uczenia maszynowego, które analizują wiele reguł, aby zrozumieć ich zwrotnice i interakcje.** 

[![Mapa ruchu sieciowego](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="legacy-networking"></a>Starsza sieć<a name ="legacy-networking"></a>

Jeśli nie masz warstwy Standard usługi Security Center, w tej sekcji wyjaśniono, jak wyświetlić bezpłatne zalecenia dotyczące sieci.

Aby uzyskać dostęp do tych informacji, w bloku Sieć kliknij pozycję **Wyświetl starszą sieć**. 

[![Starsza sieć](./media/security-center-network-recommendations/legacy-networking.png)](./media/security-center-network-recommendations/legacy-networking.png#lightbox)

### <a name="internet-facing-endpoints-section"></a>Sekcja Internet facing endpoints (Punkty końcowe umożliwiające dostęp do Internetu)
W **sekcji Punkty końcowe skierowane do Internetu** można zobaczyć maszyny wirtualne, które są obecnie skonfigurowane z punktem końcowym skierowanym do Internetu i jego stanem.

Ta tabela ma nazwę punktu końcowego, adres IP skierowany do Internetu oraz bieżący stan ważności sieciowej grupy zabezpieczeń i zalecenia NGFW. Tabela jest sortowana według ważności.

### <a name="networking-topology-section"></a>Sekcja Topologia sieci
Sekcja **Topologia sieci** ma hierarchiczny widok zasobów.

Ta tabela jest sortowana (maszyny wirtualne i podsieci) według ważności.

W tym widoku topologii pierwszy poziom wyświetla sieci wirtualne. Drugi wyświetla podsieci, a trzeci poziom wyświetla maszyny wirtualne, które należą do tych podsieci. W prawej kolumnie przedstawiono bieżący stan rekomendacji sieciowej grupy zabezpieczeń dla tych zasobów.

Trzeci poziom wyświetla maszyny wirtualne, który jest podobny do tego, co jest opisane wcześniej. Można kliknąć dowolny zasób, aby dowiedzieć się więcej lub zastosować wymaganą kontrolę zabezpieczeń lub konfigurację.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące elementy:

* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrona usługi SQL platformy Azure w usłudze Azure Security Center](security-center-sql-service-recommendations.md)