---
title: Wzorce projektowania ujawniania
titleSuffix: Azure Cognitive Services
description: Wzorce projektowe i najlepsze rozwiązania związane z ujawnianiem.
services: cognitive-services
author: angle
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: angle
ms.openlocfilehash: dfa05ff114ea0976cb8d57f711eaacc016ea53cc
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836996"
---
# <a name="disclosure-design-patterns"></a>Wzorce projektowania ujawniania
Teraz,&#39;po ustaleniu właściwego [poziomu ujawniania](concepts-disclosure-guidelines.md#disclosure-assessment) danych na potrzeby obsługi głosu syntetycznego,&#39;warto zapoznać się z potencjalnymi wzorcami projektowymi.
## <a name="overview"></a>Omówienie
Istnieje szeroki zakres wzorców projektowych, które można zastosować do Twoich syntetycznych połączeń głosowych. Jeśli wynik oceny ujawnienia był "wysokim ujawnieniem", zalecamy [**jawne ujawnienie**](#explicit-disclosure), co oznacza, że komunikacja z pochodzeniem z syntetycznego głosu jest nieodpowiednia. [**Niejawne ujawnienie**](#implicit-disclosure) obejmuje wskaźniki i wzorce interakcji, które korzystają z głosu, niezależnie od tego, czy wymagane poziomy ujawniania są wysokie czy niskie.
![spektrum ujawniania](media/responsible-ai/disclosure-patterns/affordances.png)






| Jawne wzorce ujawniania                                                                                                                                                                                    | Niejawne wzorce ujawniania                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Przejrzyste wprowadzenie](#transparent-introduction)<br> [Werbalne jawne wprowadzenie](#verbal-transparent-introduction)<br>  [Jawne Byline](#explicit-byline)<br>  [Dostosowywanie i kalibracja](#customization-and-calibration)<br> [Ujawnienie rodzicielskie](#parental-disclosure)<br> [Zapewnianie możliwości Dowiedz się więcej o tym, jak nastąpiło głos](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Ujawnienie możliwości](#capability-disclosure)<br>[Niejawne wskaźniki i opinie](#implicit-cues--feedback)<br> [Przezroczystość konwersacji](#conversational-transparency) |



Skorzystaj z poniższej tabeli, aby odwołać się bezpośrednio do wzorców, które są stosowane do głosu syntetycznego. Niektóre z innych warunków tego wykresu mogą również dotyczyć Twojego scenariusza:<br/>



| Jeśli Twoje środowisko głosu syntetycznego... | Zalecenia | Wzorce projektowe |
| --- | --- | --- |
| Wymaga wysokiego ujawnienia  | Użyj co najmniej jednego jawnego wzorca i niejawnych wskazówek na początku, aby ułatwić użytkownikom tworzenie skojarzeń. |[Jawne ujawnienie](#explicit-disclosure)<br>[Niejawne ujawnienie](#implicit-disclosure)  |
| Wymaga niskiej ilości informacji | Ujawnienie może być minimalne lub niepotrzebne, ale może korzystać z niektórych niejawnych wzorców. | [Ujawnienie możliwości](#capability-disclosure)<br>[Przezroczystość konwersacji](#conversational-transparency)  |
| Ma wysoki poziom zaangażowania | Kompiluj przez długi czas i oferując wiele punktów wejścia do ujawnienia podczas podróży użytkownika. Zdecydowanie zaleca się korzystanie z funkcji dołączania. | [Przejrzyste wprowadzenie](#transparent-introduction)<br>[Dostosowywanie i kalibracja](#customization-and-calibration)<br>[Ujawnienie możliwości](#capability-disclosure) |
| Zawiera elementy podrzędne jako głównej zamierzonej grupy odbiorców | Kierowanie obiektów docelowych jako głównych odbiorców ujawniania i zapewnienie, że mogą one efektywnie komunikować się z odniesiemi do elementów podrzędnych.  | [Ujawnienie rodzicielskie](#parental-disclosure)<br>[Werbalne jawne wprowadzenie](#verbal-transparent-introduction)<br> [Niejawne ujawnienie](#implicit-disclosure)<br> [Przezroczystość konwersacji](#conversational-transparency)  |
| Obejmuje użytkowników niewidomych lub osób mających słabą wizję jako podstawową zamierzonymi odbiorcami  | Załącz się z wszystkimi użytkownikami i upewnij się, że jakakolwiek forma ujawniania wizualnego ma skojarzone alternatywny tekst lub efekty dYwiękowe. Przestrzegaj standardów dostępności dla współczynnika kontrastu i rozmiaru ekranu. Użyj wskaźników audytorów, aby komunikować się z ujawnianiem.  | [Werbalne jawne wprowadzenie](#verbal-transparent-introduction) <br>[Wskaźniki audytorów](#implicit-cues--feedback)<br>[Haptic podpowiedzi](#implicit-cues--feedback)<br>[Przezroczystość konwersacji](#conversational-transparency)<br>[Standardy dostępności](https://www.microsoft.com/accessibility) |
| Jest mniejsze niż ekran, urządzenia i mniejsze lub używa głosu jako podstawowego lub tylko trybu interakcji | Użyj wskaźników audytorów, aby komunikować się z ujawnianiem. | [Werbalne jawne wprowadzenie](#verbal-transparent-introduction) <br> [Wskaźniki audytorów](#implicit-cues--feedback)  |
| Potencjalnie obejmuje wielu użytkowników/odbiorników (np. Personal Assistant w wielu gospodarstwach domowych)  | Zapoznaj się z różnymi kontekstami użytkownika i poziomami interpretacji i oferując wiele możliwości ujawniania w podróży użytkownika.  | [Jawne wprowadzenie (użytkownik zwrotny)](#transparent-introduction)<br> [Zapewnianie możliwości Dowiedz się więcej o tym, jak nastąpiło głos](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Przezroczystość konwersacji](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Jawne ujawnienie
Jeśli środowisko głosu syntetycznego wymaga wysokiego ujawnienia, najlepszym rozwiązaniem jest użycie co najmniej jednego z następujących jawnych wzorców w celu jasnego podania charakteru syntetycznego.
### <a name="transparent-introduction"></a>Przejrzyste wprowadzenie

Przed rozpoczęciem pracy z głosem należy wprowadzić asystenta cyfrowego przez pełny wgląd w informacje o źródłach głosu i jego możliwości. Najlepszym momentem korzystania z tego wzorca jest dołączenie nowego użytkownika lub wprowadzenie nowych funkcji do zwracanego użytkownika. Zaimplementowanie niejawnych wskazówek podczas wprowadzania ułatwia użytkownikom formę modelu psychicznego na temat syntetycznego charakteru agenta cyfrowego.

#### <a name="first-time-user-experience"></a>Środowisko użytkownika pierwszego czasu

![przejrzyste wprowadzenie podczas pierwszego uruchomienia środowiska](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*Głos syntetyczny jest wprowadzany podczas dołączania nowego użytkownika.*

Zalecenia
- Opisz, że głos jest sztuczny (np. &quot;Digital&quot;)
- Opisz, do czego służy Agent
- Jawnie określaj źródła&#39;głosu
- Oferowanie punktu wejścia, aby dowiedzieć się więcej o głosowaniu syntetycznym

#### <a name="returning-user-experience"></a>Zwracanie środowiska użytkownika

Jeśli użytkownik pomija środowisko dołączania, kontynuuje oferowanie punktów wejścia do przezroczystego środowiska wprowadzania do momentu wyzwolenia głosu przez użytkownika po raz pierwszy.
<br/>

![przejrzyste wprowadzenie podczas korzystania ze środowiska użytkownika zwrotnego](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Zapewnienie spójnego punktu wejścia do obsługi głosu syntetycznego. Zezwalaj użytkownikowi na powrót do środowiska dołączania, gdy wyzwala głos po raz pierwszy w dowolnym momencie podróży użytkownika.*


### <a name="verbal-transparent-introduction"></a>Werbalne jawne wprowadzenie

Mówiony monit zawierający informacje o źródłach głosu asystenta&#39;cyfrowego jest wystarczająco wyraźny, aby uzyskać ujawnienie. Ten wzorzec jest najlepszy dla scenariuszy o wysokim poziomie ujawniania, w których głos jest jedynym dostępnym trybem interakcji.
<br/>

![j niezauważalne wprowadzenie](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*W przypadku momentu, w którym użytkownik może już wprowadzać lub atrybutować&#39;głos, można użyć przejrzystego wprowadzenia.*


![j czasownikowo niewidocznym wprowadzeniem w pierwszej osoby](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*W celu uzyskania dodatkowej przejrzystości aktora głosowego może ujawnić źródła głosu syntetycznego w pierwszej osoby.*

### <a name="explicit-byline"></a>Jawne Byline

Użyj tego wzorca, jeśli użytkownik będzie pracowali przy użyciu odtwarzacza audio lub składnika interaktywnego w celu wyzwolenia głosu.


![Explicit Byline w scenariuszu multimediów wiadomości](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Jawna Byline jest przyznanym miejscu, z którego pochodzi głos.*

Zalecenia

- Punkt wejścia oferty, aby dowiedzieć się więcej o wystawionym głosie

### <a name="customization-and-calibration"></a>Dostosowywanie i kalibracja

Zapewnij użytkownikom kontrolę nad tym, w jaki sposób asystent cyfrowy reaguje na nie (tj. jak dźwięki głosowe).  Gdy użytkownik współdziała z systemem zgodnie z własnym warunkiem i z konkretnymi celami, a następnie według definicji, rozumieli już, że&#39;nie jest to prawdziwa osoba.

#### <a name="user-control"></a>Kontrolka użytkownika

Oferuje opcje, które mają znaczący i zauważalny wpływ na środowisko głosu syntetycznego.

![preferencje użytkownika](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Preferencje użytkownika umożliwiają użytkownikom dostosowywanie i ulepszanie ich środowiska.*

Zalecenia

- Zezwól użytkownikom na dostosowywanie głosu (np. Wybierz język i typ głosu)
- Zapewnianie użytkownikom możliwości nauczenia systemu w celu reagowania na jego unikatowy głos (np. Kalibracja głosu, polecenia niestandardowe)
- Optymalizuj pod kątem interakcji generowanych przez użytkownika lub kontekstowych (np. przypomnień)

#### <a name="persona-customization"></a>Dostosowanie osoby

Oferuje sposoby dostosowywania głosu asystenta&#39;cyfrowego. Jeśli głos jest oparty na osobistości lub szeroko rozpoznawalnej osobie, rozważ użycie wizualizacji i wprowadzenie, gdy użytkownicy wyświetlają Podgląd głosu.

![dostosowania głosu](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Oferowanie możliwości wyboru z zestawu głosów ułatwia przekazanie sztucznego charakteru.*

Zalecenia
- Zezwalaj użytkownikom na podgląd dźwięku każdego głosu
- Użyj oryginalnego wprowadzenia dla każdego głosu
- Punkty wejścia oferty, aby dowiedzieć się więcej o wystawionym głosie

### <a name="parental-disclosure"></a>Ujawnienie rodzicielskie

Oprócz zgodności z przepisami COPPA należy zapewnić ujawnienie rodzicom, jeśli głównymi zamierzonymi odbiorcami są młode elementy podrzędne, a poziom ekspozycji jest wysoki. W celu uwzględnienia wrażliwych celów należy rozważyć kontroli środowiska, dopóki osoba dorosła nie potwierdzi użycia syntetycznego głosu. Zachęcaj rodziców do przekazywania wiadomości do ich elementów podrzędnych.

![ujawnienie dla elementów nadrzędnych](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Przejrzyste wprowadzenie zoptymalizowane pod kątem rodziców gwarantuje, że osoba dorosła świadomie poznała syntetycznego charakteru głosu przed interakcją z nim.*

Zalecenia

- Kierowanie obiektów docelowych jako głównych odbiorców do ujawnienia
- Zachęcanie rodziców do przekazywania informacji do ich elementów podrzędnych
- Punkty wejścia oferty, aby dowiedzieć się więcej o wystawionym głosie
- Poinformuj o tym, jak zadawać rodzicom proste &quot;ochronę&quot;, aby pokazać, że zapoznają się z ujawnieniem

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Zapewnianie możliwości Dowiedz się więcej o tym, jak nastąpiło głos

Udostępniaj kontekstowe punkty wejścia do strony, wyskakującej lub zewnętrznej witryny zawierającej więcej informacji na temat syntetycznej technologii głosowej. Można na przykład utworzyć link, aby dowiedzieć się więcej podczas dołączania, lub gdy użytkownik poprosi o dodatkowe informacje podczas rozmowy.

![punkt wejścia, aby dowiedzieć się więcej](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Przykład punktu wejścia, aby zaoferować szansę dowiedzieć się więcej o wykorzystanym głosie.*

Gdy użytkownik zażąda więcej informacji na temat głosu syntetycznego, główny cel polega na przeprowadzeniu szkolenia dotyczącego pochodzenia syntetycznego i przejrzystego o technologii.

![zapewnić użytkownikom więcej informacji na temat syntetycznego głosu](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Więcej informacji można uzyskać w witrynie pomocy zewnętrznej witryny.*

Zalecenia

- Uprość złożone koncepcje i Unikaj korzystania z Legalese i technicznych żargon
- Nie Bury tej zawartości w oświadczeniach o ochronie prywatności i warunkach użytkowania
- Przechowuj zawartość zwięzłą i Używaj obrazów, jeśli są dostępne

## <a name="implicit-disclosure"></a>Niejawne ujawnienie

Spójność to klucz umożliwiający ujawnienie niejawnie w całej podróży użytkownika. Spójne korzystanie z podpowiedzi wizualizacji i audytorów na urządzeniach i w różnych trybach interakcji może pomóc w tworzeniu skojarzeń między niejawnymi wzorcami i jawnym ujawnieniem.

![Spójność niejawnych wskazówek](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues-amp-feedback"></a>Niejawne wskaźniki &amp; Opinie

Anthropomorphism może zamanifestować różne sposoby, od rzeczywistej reprezentacji agenta do głosu, dYwięków, wzorców świateł, odbijających się kształtów, a nawet wibracji urządzenia. Podczas definiowania osoby należy wykorzystać niejawne wskaźniki i wzorce opinii zamiast dążyć do bardzo przypominającego awatara. Jest to jeden ze sposobów, aby zminimalizować potrzebę bardziej jawnego ujawniania.

![podpowiedzi i opinii wizualnych](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Te wskaźniki pomagają anthropomorphize agenta, nie są zbyt podobne do ludzi. Mogą oni także stać się skutecznymi mechanizmami ujawniania, gdy są używane spójnie z upływem czasu.*

Należy wziąć pod uwagę różne tryby interakcji środowiska podczas uwzględniania następujących typów wskaźników:

| Podpowiedzi wizualne                                                                                                                                                               | Wskaźniki audytorów                                                      | Haptic podpowiedzi |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Pojawia <br>Wskaźniki czasu rzeczywistego odpowiedzi (np. animacje)<br> Podpowiedzi, które nie są ekranem (np. światła i wzorce na urządzeniu)<br>  | Sonicon (np. krótki dźwięk odróżniający, Seria notatek muzycznych) | Wibracj   |

### <a name="capability-disclosure"></a>Ujawnienie możliwości

Ujawnienie można uzyskać niejawnie, ustawiając dokładne oczekiwania na to, co może być możliwe. Podaj przykładowe polecenia, aby użytkownicy mogli dowiedzieć się, jak korzystać z Asystenta cyfrowego i oferować pomoc kontekstową, aby dowiedzieć się więcej o głosowaniu syntetycznym na wczesnych etapach tego środowiska.

![Wizualne podpowiedzi i opinie](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Przezroczystość konwersacji

Gdy konwersacje znajdują się w nieoczekiwanych ścieżkach, warto wziąć pod uwagę odpowiedzi domyślne, które mogą pomóc w resetowaniu oczekiwań, wzmocnić przejrzystości i dowiązywać użytkowników do pomyślnych Istnieją również okazje do użycia jawnego ujawnienia w konwersacji.

![Obsługa nieoczekiwanych ścieżek](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Niezależne lub &quot;osobiste pytania&quot; kierowane do agenta to dobry moment, aby przypominać użytkowników o syntetycznym charakterze agenta i kierować je do odpowiednich osób lub przekierowywać je do rzeczywistej osoby.

![Obsługa z pytaniami dotyczącymi zadań](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Kiedy należy ujawnić

Istnieje wiele możliwości ujawniania informacji w trakcie podróży użytkownika. Zaprojektuj do pierwszego użycia, drugiego użycia, n-ty..., ale również przeznaczenie momentu &quot;awarii&quot; do wyróżnienia przejrzystości, jak w przypadku błędu systemu lub gdy użytkownik odkryje ograniczenie możliwości agenta&#39;.

![Możliwości ujawniania informacji w trakcie podróży użytkownika](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Przykład standardowej podróży użytkownika asystenta cyfrowego wyróżniający różne możliwości ujawniania.

### <a name="up-front"></a>Do przodu

Optymalny czas na ujawnienie jest podczas pierwszego działania osoby w przypadku głosu syntetycznego.  W scenariuszu osobistego asystenta głosowego będzie to możliwe podczas dołączania lub podczas pierwszego rozłączenia środowiska użytkownika. W innych scenariuszach może to być za pierwszym razem, gdy użytkownik odczytuje zawartość w witrynie sieci Web lub po raz pierwszy współdziała z wirtualnym znakiem.

- [Przejrzyste wprowadzenie](#transparent-introduction)
- [Ujawnienie możliwości](#capability-disclosure)
- [Dostosowywanie i kalibracja](#customization-and-calibration)
- [Niejawne podpowiedzi](#implicit-cues--feedback)

### <a name="upon-request"></a>Na żądanie

Użytkownicy powinni mieć możliwość łatwego dostępu do dodatkowych informacji, preferencji kontroli i otrzymywania przejrzystych informacji w dowolnym momencie podczas podróży użytkownika.

- [Zapewnianie możliwości Dowiedz się więcej o tym, jak nastąpiło głos](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Dostosowywanie i kalibracja](#customization-and-calibration)
- [Przezroczystość konwersacji](#conversational-transparency)

### <a name="continuously"></a>Stałą

Używaj niejawnych wzorców projektowych, które zwiększają możliwości środowiska użytkownika w sposób ciągły.

- [Ujawnienie możliwości](#capability-disclosure)
- [Niejawne podpowiedzi](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Gdy system ulegnie awarii

Użyj ujawnienia jako możliwości bezproblemowego niepowodzenia.

- [Przezroczystość konwersacji](#conversational-transparency)
- [Zapewnianie możliwości Dowiedz się więcej o tym, jak nastąpiło głos](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Oddanie ludzi](#conversational-transparency)



## <a name="additional-resources"></a>Dodatkowe zasoby
- [Wytyczne dotyczące bot firmy Microsoft](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Wskazówki dotyczące projektowania Cortany](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Wskazówki dotyczące projektowania mowy w systemie Microsoft Windows platformy UWP](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Wskazówki dotyczące poleceń głosowych programu Microsoft Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Dokumentacja

* [Ujawnienie dla talent głosu](https://aka.ms/disclosure-voice-talent)
* [Wytyczne dotyczące odpowiedzialnego wdrożenia syntetycznej technologii głosowej](concepts-guidelines-responsible-deployment-synthetic.md)
* [Kontroli — Omówienie](concepts-gating-overview.md)
* [Jak ujawnić](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Następne kroki

* [Ujawnienie dla talent głosu](https://aka.ms/disclosure-voice-talent)
