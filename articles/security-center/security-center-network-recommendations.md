---
title: Ochrona zasobów sieciowych w Azure Security Center | Microsoft Docs
description: Ten dokument zawiera zalecenia dotyczące Azure Security Center, które pomagają chronić zasoby sieci platformy Azure i zachować zgodność z zasadami zabezpieczeń.
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
ms.date: 04/05/2019
ms.author: v-mohabe
ms.openlocfilehash: 3683550045699a24c27a3226fba2e90a671f80e1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101026"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Ochrona zasobów sieciowych w Azure Security Center
Azure Security Center stale analizuje stan zabezpieczeń zasobów platformy Azure pod kątem najlepszych rozwiązań z zakresu zabezpieczeń sieci. Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek do zabezpieczania i zabezpieczania zasobów.

Ten artykuł dotyczy zaleceń, które dotyczą zasobów platformy Azure z punktu widzenia zabezpieczeń sieci. Centrum zaleceń dotyczących sieci wokół zapór nowej generacji, sieciowych grup zabezpieczeń, JIT dostęp do maszyny wirtualnej za pośrednictwem reguł ruchu przychodzącego i innych. Aby zapoznać się z listą zaleceń dotyczących sieci i akcji korygowania, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> Strona **sieci** umożliwia głębokie szczegółowe usługi Azure Resource Health z perspektywy sieci. Mapy sieci i adaptacyjne kontrolki sieci są dostępne tylko dla Azure Security Center warstwy Standardowa. W [przypadku korzystania z warstwy Bezpłatna możesz kliknąć ten przycisk, aby **wyświetlić starszą sieć** i otrzymać zalecenia dotyczące zasobów sieciowych](#legacy-networking).
>

Strona **Sieć** zawiera przegląd sekcji, w których można szczegółowe, aby uzyskać więcej informacji na temat kondycji zasobów sieciowych:

- Mapa sieci (tylko Azure Security Center warstwy Standardowa)
- Adaptacyjne wzmacnianie zabezpieczeń sieci
- Zalecenia dotyczące zabezpieczeń sieci.
- Blok starszej **sieci** (poprzedni blok sieci) 
 
![Okienko sieć](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Mapa sieci
Interaktywna mapa sieci zapewnia widok graficzny z nakładkami zabezpieczeń, które zapewniają zalecenia i szczegółowe informacje na potrzeby zabezpieczania zasobów sieciowych. Za pomocą mapy można zobaczyć topologię sieci obciążeń platformy Azure, połączenia między maszynami wirtualnymi i podsieciami oraz możliwość przechodzenia do szczegółów z mapy do określonych zasobów i zaleceń dotyczących tych zasobów.

Aby otworzyć mapę sieci:

1. W Security Center w obszarze higiena zabezpieczeń zasobów wybierz pozycję **Sieć**.
2. W obszarze **mapa sieci** kliknij pozycję **Zobacz topologia**.
 
Zostanie wyświetlony domyślny widok mapy topologii:

- Subskrypcje wybrane na platformie Azure. Mapa obsługuje wiele subskrypcji.
- Maszyny wirtualne, podsieci i sieci wirtualnych typu zasobów Menedżer zasobów (klasyczne zasoby platformy Azure nie są obsługiwane)
- Sieci wirtualnych komunikacji równorzędnej
- Tylko zasoby mające [zalecenia dotyczące sieci](security-center-recommendations.md) o wysokiej lub średniej ważności  
- Zasoby połączone z Internetem
- Mapa jest zoptymalizowana pod kątem subskrypcji wybranych na platformie Azure. W przypadku zmodyfikowania zaznaczenia mapa zostanie ponownie obliczona i zoptymalizowana na podstawie nowych ustawień.  

![Mapa topologii sieci](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Omówienie mapy sieci

Mapa sieci może przedstawiać zasoby platformy Azure w widoku **topologii** i widoku **ruchu** .

### <a name="the-topology-view"></a>Widok topologii

W widoku **topologii** mapy sieci można wyświetlić następujące informacje o zasobach sieciowych:

- W okręgu wewnętrznym można zobaczyć wszystkie sieci wirtualnych w wybranych subskrypcjach, następnym okręgiem są wszystkie podsieci, a zewnętrzny okrąg to wszystkie maszyny wirtualne.
- Linie łączące zasoby w mapie informują o tym, które zasoby są ze sobą skojarzone, oraz o strukturze sieci platformy Azure. 
- Użyj wskaźników ważności, aby szybko zapoznać się z tym, które zasoby mają otwarte zalecenia z Security Center.
- Możesz kliknąć dowolny z zasobów, aby przejść do szczegółów i wyświetlić szczegóły tego zasobu oraz jego zalecenia bezpośrednio, a także w kontekście mapy sieci.  
- Jeśli na mapie są wyświetlane zbyt wiele zasobów, Azure Security Center używa swojego algorytmu własnościowego do inteligentnego klastrowania zasobów, wyróżnienia zasobów, które znajdują się w najbardziej krytycznym stanie, i mają najwięcej zaleceń dotyczących ważności. 

Ponieważ mapa jest interaktywna i dynamiczna, każdy węzeł jest klikany, a widok może ulec zmianie w zależności od filtrów:

1. Można modyfikować elementy widoczne na mapie sieci przy użyciu filtrów znajdujących się na górze. Możesz skupić się na mapie na podstawie:

   -  **Kondycja zabezpieczeń**: Mapę można filtrować na podstawie ważności (wysoka, średnia, niska) zasobów platformy Azure.
   - **Zalecenia**: Możesz wybrać, które zasoby są wyświetlane na podstawie zaleceń, które są aktywne dla tych zasobów. Można na przykład wyświetlić tylko zasoby, dla których Security Center zaleca się włączenie sieciowych grup zabezpieczeń.
   - **Strefy sieciowe**: Domyślnie mapa wyświetla tylko zasoby połączone z Internetem, można również wybrać wewnętrzne maszyny wirtualne.
 
2. Możesz kliknąć pozycję **Zresetuj** w lewym górnym rogu w dowolnym momencie, aby przywrócić mapę do stanu domyślnego.

Aby przejść do szczegółów zasobu:

1. Po wybraniu określonego zasobu na mapie zostanie otwarte okienko po prawej stronie, w którym znajdują się ogólne informacje o zasobie, połączone rozwiązania zabezpieczeń, jeśli istnieją, a także zalecenia dotyczące zasobu. Jest to ten sam typ zachowania dla każdego typu zasobu, który został wybrany. 
2. Po umieszczeniu wskaźnika myszy na węźle na mapie można wyświetlić ogólne informacje o zasobie, w tym o subskrypcji, typie zasobu i grupie zasobów.
3. Użyj linku, aby powiększyć do etykietki narzędzia i ponownie umieścić mapę na tym konkretnym węźle. 
4. Aby skoncentrować się na mapie od określonego węzła, Pomniejsz.

### <a name="the-traffic-view"></a>Widok ruchu

Widok **ruch** udostępnia mapę całego możliwego ruchu między zasobami. Zapewnia to wizualną mapę wszystkich skonfigurowanych reguł, które definiują, które zasoby mogą komunikować się z tym, z którymi się komunikują. Dzięki temu można zobaczyć istniejącą konfigurację sieciowych grup zabezpieczeń, a także szybko identyfikować możliwe konfiguracje ryzykowne w ramach obciążeń.

### <a name="uncover-unwanted-connections"></a>Odkrywanie niechcianych połączeń

Siła tego widoku pozwala na wyświetlanie tych dozwolonych połączeń wraz z lukami w zabezpieczeniach, dzięki czemu można użyć tej części danych w celu przeprowadzenia niezbędnej ochrony zasobów. 

Można na przykład wykryć dwie nieświadome komputery, które mogły się komunikować, co pozwala lepiej izolować obciążenia i podsieci.

### <a name="investigate-resources"></a>Zbadaj zasoby

Aby przejść do szczegółów zasobu:

1. Po wybraniu określonego zasobu na mapie zostanie otwarte okienko po prawej stronie, w którym znajdują się ogólne informacje o zasobie, połączone rozwiązania zabezpieczeń, jeśli istnieją, a także zalecenia dotyczące zasobu. Jest to ten sam typ zachowania dla każdego typu zasobu, który został wybrany. 
2. Kliknij pozycję **ruch** , aby wyświetlić listę możliwego ruchu wychodzącego i przychodzącego dla zasobu — jest to kompleksowa lista osób, które mogą komunikować się z zasobem i kto może się z nim komunikować oraz za pomocą których protokołów i portów. Na przykład po wybraniu maszyny wirtualnej są wyświetlane wszystkie maszyny wirtualne, z którymi mogą się komunikować, a po wybraniu podsieci są wyświetlane wszystkie podsieci, z którymi może się komunikować.

**Dane te są oparte na analizie sieciowych grup zabezpieczeń, a także zaawansowanych algorytmów uczenia maszynowego, które analizują wiele reguł, aby zrozumieć ich skrzyżowania i interakcje.** 

![Mapa ruchu sieciowego](./media/security-center-network-recommendations/network-map-traffic.png)

## Starsze sieci<a name ="legacy-networking"></a>

Jeśli nie masz Security Center warstwy Standardowa, w tej sekcji wyjaśniono, jak wyświetlić bezpłatne zalecenia dotyczące sieci.

Aby uzyskać dostęp do tych informacji, w bloku sieć kliknij pozycję **Wyświetl starszą sieć**. 

![Starsze sieci](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Sekcja Internet facing endpoints (Punkty końcowe umożliwiające dostęp do Internetu)
W sekcji **punkty końcowe** połączone z Internetem można zobaczyć maszyny wirtualne, które są aktualnie skonfigurowane z punktem końcowym mającym dostęp do Internetu i jego stan.

Ta tabela zawiera nazwę punktu końcowego, adres IP połączony z Internetem oraz bieżący stan ważności sieciowej grupy zabezpieczeń i rekomendacji zapory następnej generacji. Tabela jest posortowana według ważności.

### <a name="networking-topology-section"></a>Sekcja Topologia sieci
Sekcja **topologia sieci** zawiera hierarchiczny widok zasobów.

Ta tabela jest posortowana (maszyny wirtualne i podsieci) według ważności.

W tym widoku topologii pierwszy poziom wyświetla sieci wirtualnych. Drugie wyświetlenie zawiera podsieci, a trzeci poziom wyświetla maszyny wirtualne należące do tych podsieci. W prawej kolumnie jest wyświetlany bieżący stan zaleceń sieciowych grup zabezpieczeń dla tych zasobów.

Trzeci poziom przedstawia maszyny wirtualne, które są podobne do opisanych wcześniej. Możesz kliknąć dowolny zasób, aby dowiedzieć się więcej lub zastosować wymaganą kontrolę zabezpieczeń lub konfigurację.

## <a name="network-recommendations"></a>Zalecenia dotyczące sieci

|Nazwa zalecenia|Opis|severity|Wskaźnik bezpieczeństwa|Typ zasobu|
|----|----|----|----|----|----|
|Sieciowe grupy zabezpieczeń na poziomie podsieci powinny być włączone|Włącz sieciowe grupy zabezpieczeń, aby kontrolować dostęp sieciowy do zasobów wdrożonych w podsieciach.|Wysoki/średni|30|Subnet|
|Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń|Włącz sieciowe grupy zabezpieczeń, aby kontrolować dostęp sieciowy do maszyn wirtualnych.|Wysoki/średni|30|Maszyna wirtualna|
|Dostęp powinien być ograniczony do ograniczeń sieciowych grup zabezpieczeń z maszynami wirtualnymi z Internetu|Podwyższenie poziomu bezpieczeństwa sieci maszyn wirtualnych połączonych z Internetem przez ograniczenie dostępu do istniejących reguł zezwalania.|Wysoka|20|Maszyna wirtualna|
|Reguły dla aplikacji sieci Web na IaaS sieciowych grup zabezpieczeń powinny być zaostrzone|Zawzmacniaj grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń) dla maszyn wirtualnych, na których działają aplikacje sieci Web, z regułami sieciowej grupy zabezpieczeń, które są nadmiernie ograniczające w odniesieniu do portów aplikacji sieci Web.|Wysoka|20|Maszyna wirtualna|
|Dostęp do App Services powinien być ograniczony|Ogranicz dostęp do App Services, zmieniając konfigurację sieci, aby odmówić ruchu przychodzącego z zakresów, które są zbyt szerokie.|Wysoka|10|App Service|
|Porty zarządzania powinny być zamknięte na maszynach wirtualnych|Ogranicz grupę zabezpieczeń sieci maszyn wirtualnych, aby ograniczyć dostęp do portów zarządzania.|Wysoka|10|Maszyna wirtualna|
Należy włączyć Standard DDoS Protection|Ochrona sieci wirtualnych zawierających aplikacje z publicznymi adresami IP przez włączenie usługi DDoS Protection Standard. Ochrona DDoS umożliwia łagodzenie ataków między pomiarami i protokołami sieciowymi.|Wysoka|10|Sieć wirtualna|
|Przekazywanie adresów IP na maszynie wirtualnej powinno być wyłączone|Wyłącz przekazywanie adresów IP. Po włączeniu przekazywania adresów IP na karcie sieciowej maszyny wirtualnej komputer może odbierać ruch skierowany do innych miejsc docelowych. Przekazywanie adresów IP jest rzadko wymagane (np. w przypadku używania maszyny wirtualnej jako sieciowego urządzenia wirtualnego), dlatego należy je sprawdzić przez zespół ds. zabezpieczeń sieci.|Średni|10|Maszyna wirtualna|
|Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Włącz dostęp tylko do protokołu HTTPS dla aplikacji sieci Web. Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej.|Średni|20|Aplikacja internetowa|
|Kontrola dostępu do sieci just in Time powinna być stosowana na maszynach wirtualnych|Zastosuj kontrolę dostępu maszyny wirtualnej just-in-Time (JIT), aby trwale zablokować dostęp do wybranych portów i umożliwić autoryzowanym użytkownikom otwieranie ich za pośrednictwem JIT tylko przez ograniczoną ilość czasu.|Wysoka|20|Maszyna wirtualna|
|Aplikacje funkcji powinny być dostępne tylko za pośrednictwem protokołu HTTPS|Włącz dostęp tylko do protokołu HTTPS dla aplikacji funkcji. Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej.|Średni|20|Aplikacja funkcji|
|Należy włączyć bezpieczny transfer do kont magazynu|Włącz bezpieczny transfer do kont magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak Man-in-the-Middle, podsłuchiwanie i przejmowanie sesji.|Wysoka|20|Konto magazynu|

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrona usługi Azure SQL w usłudze Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
