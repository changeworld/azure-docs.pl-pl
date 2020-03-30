---
title: Alerty zabezpieczeń w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: W tym temacie wyjaśniono, jakie są alerty zabezpieczeń i różne typy dostępne w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 697c038a2fefdde8e488dad23a4e38e0b2b7b288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415852"
---
# <a name="security-alerts-in-azure-security-center"></a>Alerty zabezpieczeń w Centrum zabezpieczeń Azure

W usłudze Azure Security Center istnieje wiele alertów dla wielu różnych typów zasobów. Usługa Security Center generuje alerty dotyczące zasobów wdrożonych na platformie Azure, a także dla zasobów wdrożonych w środowiskach chmury lokalnej i hybrydowej.

Alerty zabezpieczeń są wyzwalane przez zaawansowane wykrywania i są dostępne tylko w warstwie standardowa usługi Azure Security Center. Dostępna jest bezpłatna wersja próbna. Uaktualnienie usługi jest możliwe po przejściu do wyboru warstwy cenowej na stronie [Zasady zabezpieczeń](security-center-pricing.md). Odwiedź [stronę usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/), aby dowiedzieć się więcej na temat cen.

## <a name="responding-to-todays-threats"></a>Reagowanie na dzisiejsze <a name="respond-threats"> </a> zagrożenia

W ciągu ostatnich 20 lat miały miejsce istotne zmiany dotyczące charakteru występujących zagrożeń. W przeszłości firmy zazwyczaj musiały tylko martwić się o defacement witryny internetowej przez poszczególnych napastników, którzy byli głównie zainteresowani widząc "co mogą zrobić". Dzisiejsi napastnicy są znacznie bardziej wyrafinowani i zorganizowani. Często mają określone cele finansowe i strategiczne. Ponadto dysponują większą ilością zasobów, ponieważ mogą być finansowane przez państwa lub zorganizowane grupy przestępcze.

Te zmieniające się realia doprowadziły do bezprecedensowego poziomu profesjonalizmu w szeregach atakujących. Nie wystarcza im już wpływ na zawartość witryn internetowych. Obecnie są zainteresowani kradzieżą informacji, rachunków finansowych i prywatnych danych , które mogą wykorzystać do generowania gotówki na otwartym rynku lub do wykorzystania określonej pozycji biznesowej, politycznej lub wojskowej. Bardziej niepokojący od osób o motywach finansowych są atakujący, którzy naruszają bezpieczeństwo sieci, aby uszkodzić infrastrukturę lub zaszkodzić ludziom.

W odpowiedzi na takie działania organizacje często wdrażają różne rozwiązania punktowe, które poprzez wyszukiwanie znanych sygnatur ataku koncentrują się na obronie obwodów lub punktów końcowych przedsiębiorstwa. Tego typu rozwiązania zazwyczaj generują duże ilości alertów niskiej jakości, które wymagają dokonania klasyfikacji i przeprowadzenia badań przez analityka zabezpieczeń. Większość organizacji nie dysponuje czasem i niezbędną wiedzą, które umożliwiłyby odpowiednie zareagowanie na te ostrzeżenia — wiele z nich nie jest rozwiązywanych.  

Ponadto atakujący wyewoluowali swoje metody, aby obalić wiele mechanizmów obronnych opartych na sygnaturach i [dostosować się do środowisk chmury.](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/) Wymagane jest nowe podejście do problemu zabezpieczeń, które pozwoliłoby na szybszą identyfikację potencjalnych zagrożeń, a także przyspieszone wykrywanie ich i reagowanie na nie.

## <a name="what-are-security-alerts-and-security-incidents"></a>Co to są alerty zabezpieczeń i zdarzenia dotyczące zabezpieczeń? 

**Alerty** to powiadomienia generowane przez centrum zabezpieczeń po wykryciu zagrożeń w zasobach. Usługa Security Center nadaje priorytet i wyświetla listę alertów wraz z informacjami potrzebnymi do szybkiego zbadania problemu. Usługa Security Center udostępnia również zalecenia dotyczące sposobu korygowania ataku.

**Zdarzenie zabezpieczeń** to zbiór powiązanych alertów, zamiast wystawiać każdy alert osobno. Usługa Security Center używa [korelacji alertów inteligentnych w chmurze](security-center-alerts-cloud-smart.md) do skorelowania różnych alertów i sygnałów o niskiej wierności z zdarzeniami związanymi z bezpieczeństwem.

Korzystając z incydentów, Usługa Security Center udostępnia jeden widok kampanii ataku i wszystkich powiązanych alertów. Ten widok umożliwia szybkie zrozumienie, jakie akcje osoba atakująca podjęła i jakie zasoby zostały naruszone. Aby uzyskać więcej informacji, zobacz [Korelacja inteligentnych alertów w chmurze](security-center-alerts-cloud-smart.md).



## <a name="how-does-security-center-detect-threats"></a>W jaki sposób usługa Security Center wykrywa zagrożenia? <a name="detect-threats"> </a>

Pracownicy naukowo-badawczy firmy Microsoft stale poszukują nowych zagrożeń. Ze względu na globalną obecność firmy Microsoft w chmurze i lokalnie mają oni dostęp do rozległego zestawu danych telemetrycznych. Szeroko zakrojony i zróżnicowany zbiór zestawów danych umożliwia odkrywanie nowych wzorców ataków i trendów w lokalnych produktach konsumenckich i korporacyjnych, a także w usługach online. W związku z tym usługa Security Center może szybko zaktualizować swoje algorytmy wykrywania, w miarę jak atakujący wprowadzają nowe i coraz bardziej zaawansowane metody. Takie podejście pomaga sprostać wymaganiom szybko zmieniającego się środowiska zagrożenia.

Aby wykrywać rzeczywiste zagrożenia i zmniejszać liczbę fałszywych alarmów, usługa Security Center zbiera, analizuje i integruje dane dziennika z zasobów platformy Azure i sieci. Współpracuje również z połączonymi rozwiązaniami partnerskimi, takimi jak rozwiązania ochrony zapory i punktów końcowych. Usługa Security Center analizuje te informacje, często korelując informacje z wielu źródeł, aby zidentyfikować zagrożenia.

![Prezentacja i zbieranie danych przez usługę Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Przełomowe rozwiązania dotyczące danych big data i technologii [uczenia maszynowego](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) są używane do oceny zdarzeń zachodzących w całej sieci szkieletowej chmury. Wykrywane są zagrożenia, które byłyby niemożliwe do wykrycia przy użyciu ręcznych metod, i przewidywany jest kierunek ewolucji ataków. Do narzędzi analizy zabezpieczeń należą:

* **Zintegrowana analiza zagrożeń:** Firma Microsoft dysponuje ogromną ilością globalnej analizy zagrożeń. Dane telemetryczne są przepływane z wielu źródeł, takich jak Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) i Microsoft Security Response Center (MSRC). Naukowcy otrzymują również informacje dotyczące analizy zagrożeń, które są udostępniane głównym dostawcom usług w chmurze i kanały od innych stron trzecich. Usługa Azure Security Center może użyć tych informacji, aby ostrzec użytkownika przed zagrożeniami ze strony znanych złośliwych podmiotów.

* **Analiza behawioralna:** Analiza behawioralna to technika, która analizuje i porównuje dane z kolekcją znanych wzorców. Wzorce te nie są jednak prostymi sygnaturami. Określa się je za pośrednictwem złożonych algorytmów uczenia maszynowego, które są stosowane w przypadku wielkich zestawów danych. Są one również określane przez specjalistów od analizy, którzy dokonują dokładnej analizy złośliwych zachowań. Usługa Azure Security Center może używać analizy behawioralnej do identyfikowania zasobów, których bezpieczeństwo zostało naruszone, na podstawie analizy dzienników maszyn wirtualnych, dzienników urządzeń sieci wirtualnej, dzienników sieci szkieletowej, zrzutów awaryjnych i innych źródeł.

* **Wykrywanie anomalii:** Usługa Azure Security Center używa również wykrywania anomalii do identyfikowania zagrożeń. W przeciwieństwie do analizy behawioralnej (która zależy od znanych wzorców pochodzących z dużych zestawów danych), wykrywanie anomalii jest bardziej "spersonalizowane" i koncentruje się na liniach bazowych, które są specyficzne dla wdrożeń. Uczenie maszynowe jest stosowane do określania normalnego działania wdrożeń, a następnie generowania reguł definiujących odstające warunki, które mogą reprezentować zdarzenie związane z zabezpieczeniami.

## <a name="how-are-alerts-classified"></a>Jak są klasyfikowane alerty?

Usługa Security Center przypisuje ważność alertów, aby ułatwić ustalanie priorytetów kolejności, w jakiej dotyczysz każdego alertu, dzięki czemu gdy zasób zostanie naruszony, możesz uzyskać do niego od razu. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje.

> [!NOTE]
> Ważność alertu jest wyświetlany inaczej w portalu i wersjach interfejsu API REST, które poprzedzają 01-01-2019. Jeśli używasz starszej wersji interfejsu API, uaktualnij, aby uzyskać spójne środowisko opisane poniżej.

- **Wysoki:** Istnieje duże prawdopodobieństwo, że zasób zostanie naruszony. Powinieneś zajrzeć do niego od razu. Usługa Security Center ma duże zaufanie zarówno do złośliwego zamiaru, jak i do ustaleń użytych do wydania alertu. Na przykład alert, który wykrywa wykonanie znanego złośliwego narzędzia, takiego jak Mimikatz, wspólne narzędzie używane do kradzieży poświadczeń.
- **Średni:** Jest to prawdopodobnie podejrzane działanie może wskazywać, że zasób jest zagrożona.
Zaufanie centrum zabezpieczeń do analitycznego lub znalezienia jest średnie, a zaufanie do złośliwego zamiaru jest średnie i wysokie. Zazwyczaj są to wykrywanie oparte na uczeniu maszynowym lub anomalii. Na przykład próba logowania z nietypowej lokalizacji.
- **Niski:** Może to być łagodny pozytywny lub zablokowany atak.
   * Usługa Security Center nie jest na tyle pewna, że intencja jest złośliwa, a działanie może być niewinne. Na przykład wyczyść dziennik jest akcja, która może się zdarzyć, gdy osoba atakująca próbuje ukryć swoje ślady, ale w wielu przypadkach jest rutynowa operacja wykonywana przez administratorów.
   * Usługa Security Center zazwyczaj nie informuje, kiedy ataki zostały zablokowane, chyba że jest to interesujący przypadek, który sugerujemy, aby przyjrzeć się. 
- **Informacje:** Alerty informacyjne będą widoczne tylko podczas przechodzenia do szczegółów zdarzenia zabezpieczeń lub użycia interfejsu API REST z określonym identyfikatorem alertu. Incydent składa się zazwyczaj z wielu alertów, z których niektóre mogą być wyświetlane same w sobie jako tylko informacyjne, ale w kontekście innych alertów mogą być godne bliższego przyjrzenia się. 

## <a name="continuous-monitoring-and-assessments"></a>Ciągłe monitorowanie i ocena

Usługa Azure Security Center korzysta z posiadania zespołów zajmujących się badaniami nad zabezpieczeniami i analizą danych w firmie Microsoft, które stale monitorują zmiany w krajobrazie zagrożeń. Obejmuje to następujące inicjatywy:

* **Monitorowanie analizy zagrożeń:** Analiza zagrożeń obejmuje mechanizmy, wskaźniki, implikacje i porady dotyczące istniejących lub pojawiających się zagrożeń. Te informacje są udostępniane w branży zabezpieczeń, a firma Microsoft stale monitoruje zagrożenia płynące z wewnętrznych i zewnętrznych źródeł.
* **Udostępnianie sygnału:** Dostępne są szczegółowe informacje od zespołów zabezpieczeń w szerokim portfolio usług chmurowych i lokalnych firmy Microsoft, serwerów i urządzeń końcowych klienta.
* **Specjaliści ds. zabezpieczeń firmy Microsoft**: ciągła współpraca zespołów firmy Microsoft w zakresie wyspecjalizowanych zabezpieczeń, takich jak analiza śledcza i wykrywanie ataków w sieci Web.
* **Dostrajanie wykrywania zagrożeń**: algorytmy są uruchamiane na rzeczywistych zestawach danych klienta, a pracownicy naukowo-badawczy z zakresu zabezpieczeń pracują z klientami w celu weryfikacji otrzymanych wyników. Wyniki prawdziwie i fałszywie dodatnie są używane w celu udoskonalania algorytmów uczenia maszynowego.

Te połączone wysiłki kończą się nowymi i ulepszonymi wykrywaniami, z których możesz korzystać natychmiast – nie ma żadnych działań do podjęcia.


## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o różnych typach alertów dostępnych w Centrum zabezpieczeń. Aby uzyskać więcej informacji, zobacz:

* [Ochrona przed zagrożeniami w usłudze Azure Security Center](threat-protection.md) — krótki opis źródeł alertów zabezpieczeń wyświetlanych przez usługę Azure Security Center 
* **Alerty zabezpieczeń w dzienniku aktywności platformy Azure** — oprócz dostępności w witrynie Azure portal lub programowo, alerty zabezpieczeń i zdarzenia są kontrolowane jako zdarzenia w [dzienniku aktywności platformy Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) Aby uzyskać więcej informacji na temat schematu zdarzenia, zobacz [Alerty zabezpieczeń w dzienniku aktywności platformy Azure](https://go.microsoft.com/fwlink/?linkid=2114113)

