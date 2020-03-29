---
title: Wzorce projektu ujawniania informacji
titleSuffix: Azure Cognitive Services
description: Wzorce projektowe i najlepsze praktyki dotyczące ujawniania informacji.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776626"
---
# <a name="disclosure-design-patterns"></a>Wzorce projektowania ujawniania
Teraz, gdy&#39;określisz odpowiedni poziom ujawnienia dla swojego [syntetycznego](concepts-disclosure-guidelines.md#disclosure-assessment) doświadczenia głosowego,&#39;to dobry moment na zbadanie potencjalnych wzorców projektowych.
## <a name="overview"></a>Omówienie
Istnieje spektrum wzorców projektowania ujawniania informacji, które można zastosować do swojego syntetycznego głosu. Jeśli wynikiem oceny ujawnienia było "Wysokie ujawnienie", zalecamy [**wyraźne ujawnienie,**](#explicit-disclosure)co oznacza bezpośrednie informowanie o pochodzeniu syntetycznego głosu. [**Niejawne ujawnienie**](#implicit-disclosure) obejmuje wskazówki i wzorce interakcji, które przynoszą korzyści doświadczeniom głosowym, niezależnie od tego, czy wymagane poziomy ujawniania są wysokie czy niskie.
![Spektrum wzorców ujawniania informacji](media/responsible-ai/disclosure-patterns/affordances.png)






| Jawne wzorce ujawniania informacji                                                                                                                                                                                    | Wzorce ujawniania niejawnego                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Przejrzyste wprowadzenie](#transparent-introduction)<br> [Werbalne przejrzyste wprowadzenie](#verbal-transparent-introduction)<br>  [Jawna linia](#explicit-byline)<br>  [Dostosowywanie i kalibracja](#customization-and-calibration)<br> [Ujawnienie rodzicielskie](#parental-disclosure)<br> [Zapewnienie możliwości dowiedzieć się więcej o tym, jak powstał głos](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Ujawnianie możliwości](#capability-disclosure)<br>[Niejawne wskazówki i opinie](#implicit-cues--feedback)<br> [Przejrzystość konwersacji](#conversational-transparency) |



Użyj poniższej tabeli, aby odnieść się bezpośrednio do wzorców, które mają zastosowanie do głosu syntetycznego. Niektóre z innych warunków na tym wykresie mogą również dotyczyć scenariusza:<br/>



| Jeśli twoje syntetyczne doświadczenie głosu... | Zalecenia | Wzorce projektowe |
| --- | --- | --- |
| Wymaga wysokiego ujawnienia  | Użyj co najmniej jeden jawny wzorzec i niejawne wskazówki z góry, aby pomóc użytkownikom tworzyć skojarzenia. |[Jawne ujawnianie informacji](#explicit-disclosure)<br>[Ukryte ujawnienie](#implicit-disclosure)  |
| Wymaga niskiego ujawnienia | Ujawnienie może być minimalne lub niepotrzebne, ale może korzystać z niektórych ukrytych wzorców. | [Ujawnianie możliwości](#capability-disclosure)<br>[Przejrzystość konwersacji](#conversational-transparency)  |
| Ma wysoki poziom zaangażowania | Twórz na dłuższą metę i oferuj wiele punktów wejścia do ujawnienia wzdłuż podróży użytkownika. Zdecydowanie zaleca się, aby mieć doświadczenie onboarding. | [Przejrzyste wprowadzenie](#transparent-introduction)<br>[Dostosowywanie i kalibracja](#customization-and-calibration)<br>[Ujawnianie możliwości](#capability-disclosure) |
| Obejmuje dzieci jako podstawowego zamierzonego odbiorcy | Kieruj do rodziców jako główną grupę odbiorców informacji i zapewnij, że mogą skutecznie informować dzieci o ujawnianiu informacji.  | [Ujawnienie rodzicielskie](#parental-disclosure)<br>[Werbalne przejrzyste wprowadzenie](#verbal-transparent-introduction)<br> [Ukryte ujawnienie](#implicit-disclosure)<br> [Przejrzystość konwersacji](#conversational-transparency)  |
| Obejmuje niewidomych użytkowników lub osoby niedowidzące jako główną odbiorcę  | Należy uwzględniać wszystkich użytkowników i zapewnić, że każda forma wizualnego ujawnienia ma związane z alternatywnym tekstem lub efektami dźwiękowymi. Stosować się do standardów dostępności dla współczynnika kontrastu i rozmiaru wyświetlacza. Użyj wskazówek słuchowych, aby przekazać ujawnienie.  | [Werbalne przejrzyste wprowadzenie](#verbal-transparent-introduction) <br>[Sygnały słuchowe](#implicit-cues--feedback)<br>[Haptyczne sygnały](#implicit-cues--feedback)<br>[Przejrzystość konwersacji](#conversational-transparency)<br>[Standardy dostępności](https://www.microsoft.com/accessibility) |
| Jest bez ekranu, bez urządzenia lub używa głosu jako podstawowy lub jedyny tryb interakcji | Użyj wskazówek słuchowych, aby przekazać ujawnienie. | [Werbalne przejrzyste wprowadzenie](#verbal-transparent-introduction) <br> [Sygnały słuchowe](#implicit-cues--feedback)  |
| Potencjalnie obejmuje wielu użytkowników / słuchaczy (np. osobistego asystenta w wielu gospodarstwach domowych)  | Należy pamiętać o różnych kontekstach użytkownika i poziom zrozumienia i oferują wiele możliwości ujawnienia w podróży użytkownika.  | [Wprowadzenie do przezroczystego (użytkownik zwrotny)](#transparent-introduction)<br> [Zapewnienie możliwości dowiedzieć się więcej o tym, jak powstał głos](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Przejrzystość konwersacji](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Jawne ujawnianie informacji
Jeśli twoje syntetyczne doświadczenie głosu wymaga wysokiego ujawnienia, najlepiej użyć co najmniej jednego z następujących jawnych wzorców, aby wyraźnie podać syntetyczny charakter.
### <a name="transparent-introduction"></a>Przejrzyste wprowadzenie

Zanim rozpocznie się doświadczenie głosowe, wprowadź cyfrowego asystenta, będąc w pełni przejrzystym co do pochodzenia jego głosu i jego możliwości. Optymalnym momentem, aby użyć tego wzorca jest podczas dołączania nowego użytkownika lub podczas wprowadzania nowych funkcji do powracającego użytkownika. Implementowanie niejawnych wskazówek podczas wprowadzania pomaga użytkownikom tworzyć model psychiczny o syntetycznym charakterze agenta cyfrowego.

#### <a name="first-time-user-experience"></a>Pierwsze środowisko użytkownika

![Przejrzyste wprowadzenie podczas pierwszego uruchomienia](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*Syntetyczny głos jest wprowadzany podczas dołączania nowego użytkownika.*

Zalecenia
- Opisz, że głos jest sztuczny (np. &quot;cyfrowy)&quot;
- Opisz, co agent jest w stanie zrobić
- Wyraźnie podać&#39;pochodzenie głosu
- Zaoferuj punkt wejścia, aby dowiedzieć się więcej o syntetycznym głosie

#### <a name="returning-user-experience"></a>Powrót środowiska użytkownika

Jeśli użytkownik pomija środowisko dołączania, kontynuuj oferowanie punktów wejścia do środowiska Wprowadzenie przezroczyste, dopóki użytkownik nie uruchomi głosu po raz pierwszy.
<br/>

![Przejrzyste wprowadzenie podczas powrotu użytkownika](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Zapewnij spójny punkt wejścia do syntetycznego doświadczenia głosowego. Pozwól użytkownikowi powrócić do środowiska dołączania, gdy po raz pierwszy wyzwoli głos w dowolnym momencie podróży użytkownika.*


### <a name="verbal-transparent-introduction"></a>Werbalne przejrzyste wprowadzenie

Głos mówionego, który podaje pochodzenie cyfrowego asystenta&#39;głosem, jest wystarczająco wyraźny, aby osiągnąć ujawnienie. Ten wzorzec jest najlepszy w przypadku scenariuszy wysokiego ujawniania informacji, w których głos jest jedynym dostępnym trybem interakcji.
<br/>

![Werbalnie wypowiedziane przejrzyste wprowadzenie](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Użyj przejrzystego wprowadzenia, gdy w środowiskach użytkownika występują momenty, w których możesz już wprowadzić lub przypisać osobę&#39;głosem s.*


![Werbalnie wypowiedziane przejrzyste wprowadzenie w pierwszej osobie](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Aby uzyskać dodatkową przejrzystość, aktor głosowy może ujawnić pochodzenie syntetycznego głosu w pierwszej osobie.*

### <a name="explicit-byline"></a>Jawna linia

Użyj tego wzorca, jeśli użytkownik będzie wchodził w interakcje z odtwarzaczem audio lub komponentem interaktywnym, aby wyzwolić głos.


![Jawna linia w scenariuszu mediów informacyjnych](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Jawny byline jest przypisanie, gdzie głos pochodzi.*

Zalecenia

- Zaoferuj punkt wejścia, aby dowiedzieć się więcej o syntetyzowanym głosie

### <a name="customization-and-calibration"></a>Dostosowywanie i kalibracja

Zapewnij użytkownikom kontrolę nad tym, jak asystent cyfrowy reaguje na nie (tj. jak brzmi głos).  Kiedy użytkownik wchodzi w interakcję z systemem na własnych warunkach i z myślą o konkretnych celach, to z definicji już zrozumieli, że nie&#39;to prawdziwa osoba.

#### <a name="user-control"></a>Kontrola użytkownika

Zaoferuj opcje, które mają znaczący i zauważalny wpływ na syntetyczne doświadczenie głosowe.

![Preferencje użytkownika](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Preferencje użytkownika umożliwiają użytkownikom dostosowywanie i ulepszanie ich środowiska.*

Zalecenia

- Zezwalaj użytkownikom na dostosowywanie głosu (np. wybierz język i typ głosu)
- Zapewnij użytkownikom sposób na nauczenie systemu reagowania na jego unikalny głos (np. kalibracja głosu, niestandardowe polecenia)
- Optymalizacja pod kątem interakcji generowanych przez użytkowników lub kontekstowych (np. przypomnień)

#### <a name="persona-customization"></a>Personalizacja persony

Zaoferuj sposoby dostosowywania cyfrowego asystenta&#39;głosem. Jeśli głos jest oparty na celebrycie lub szeroko rozpoznawalnej osobie, rozważ użycie zarówno wizualnych, jak i mówionych wprowadzeń, gdy użytkownicy wyświetlą podgląd głosu.

![Dostosowywanie głosu](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Oferowanie możliwości wyboru z zestawu głosów pomaga przekazać sztuczną naturę.*

Zalecenia
- Zezwalaj użytkownikom na wyświetlanie podglądu dźwięku każdego głosu
- Użyj autentycznego wprowadzenia dla każdego głosu
- Zaoferuj punkty wejścia, aby dowiedzieć się więcej o syntetyzowanym głosie

### <a name="parental-disclosure"></a>Ujawnienie rodzicielskie

Oprócz przestrzegania przepisów COPPA, należy ujawniać rodzicom informacje, jeśli głównym odbiorcą są małe dzieci, a poziom ekspozycji jest wysoki. W przypadku zastosowań wrażliwych należy rozważyć przeżycie, dopóki dorosły nie uzna użycia syntetycznego głosu. Zachęć rodziców, aby przekazywali przesłanie swoim dzieciom.

![Ujawnienie informacji dla rodziców](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Przejrzyste wprowadzenie zoptymalizowane pod kątem rodziców zapewnia, że osoba dorosła została poinformowana o syntetycznym charakterze głosu, zanim dziecko wejdzie w interakcję z nim.*

Zalecenia

- Kierować reklamy na rodziców jako główną grupę odbiorców do ujawnienia
- Zachęcanie rodziców do informowania swoich dzieci o ujawnianiu informacji
- Zaoferuj punkty wejścia, aby dowiedzieć się więcej o syntetyzowanym głosie
- Prześlij doświadczenie, &quot;zadając rodzicom proste pytanie ochronne,&quot; aby pokazać, że przeczytali ujawnienie

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Zapewnienie możliwości dowiedzieć się więcej o tym, jak powstał głos

Zaoferuj kontekstowe punkty wejścia do strony, okna podręcznego lub witryny zewnętrznej, która zawiera więcej informacji na temat technologii syntetycznego głosu. Na przykład można wyświetlić łącze, aby dowiedzieć się więcej podczas dołączania lub gdy użytkownik monituje o więcej informacji podczas konwersacji.

![Punkt wejścia, aby dowiedzieć się więcej](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Przykład punktu wejścia, aby zaoferować możliwość dowiedzieć się więcej o syntetyzowanym głosie.*

Gdy użytkownik zażąda więcej informacji na temat syntetycznego głosu, głównym celem jest edukowanie ich na temat pochodzenia syntetycznego głosu i przejrzystość technologii.

![Zapewnij użytkownikom więcej informacji na temat syntetycznego głosu](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Więcej informacji można uzyskać w zewnętrznej witrynie pomocy.*

Zalecenia

- Uprość złożone koncepcje i unikaj używania żargonu prawnego i technicznego
- Nie zakopuj tych treści w oświadczeniach dotyczących prywatności i warunków użytkowania
- Zachowaj zwięzłe treści i używaj obrazów, jeśli są dostępne

## <a name="implicit-disclosure"></a>Niejawne ujawnienie

Spójność jest kluczem do osiągnięcia ujawnienia w sposób niejawny w trakcie podróży użytkownika. Spójne stosowanie wskazówek wizualnych i słuchowych na różnych urządzeniach i trybach interakcji może pomóc w tworzeniu skojarzeń między niejawnymi wzorcami a jawnym ujawnieniem.

![Spójność niejawnych wskazówek](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Niejawne wskazówki & opinii

Antropomorfizm może objawiać się na różne sposoby, od rzeczywistej wizualnej reprezentacji agenta, do głosu, dźwięków, wzorów światła, odbijających się kształtów, a nawet wibracji urządzenia. Podczas definiowania swojej persony, wykorzystaj ukryte sygnały i wzorce sprzężenia zwrotnego, zamiast dążyć do bardzo ludzkiego awatara. Jest to jeden ze sposobów zminimalizowania potrzeby bardziej wyraźnego ujawnienia.

![Podpowiedzi wizualne i opinie](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Te wskazówki pomagają antropomorfizować środek, nie będąc zbyt ludzkim. Mogą one również stać się skutecznymi mechanizmami ujawniania informacji na własną rękę, gdy są stosowane konsekwentnie w czasie.*

Biorąc pod uwagę różne tryby interakcji doświadczenia podczas włączania następujących typów wskazówek:

| Wizualnych                                                                                                                                                               | Sygnały słuchowe                                                      | Haptyczne sygnały |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Awatar <br>Responsywne sygnały w czasie rzeczywistym (np. animacje)<br> Sygnały nieekranowe (np. światła i wzory na urządzeniu)<br>  | Sonicon (np. krótki charakterystyczny dźwięk, seria nut) | Wibracji   |

### <a name="capability-disclosure"></a>Ujawnianie możliwości

Ujawnienie można osiągnąć w sposób dorozumiany poprzez ustalenie dokładnych oczekiwań co do tego, do czego zdolny jest cyfrowy asystent. Podaj przykładowe polecenia, aby użytkownicy mogli dowiedzieć się, jak wchodzić w interakcje z asystentem cyfrowym i oferować pomoc kontekstową, aby dowiedzieć się więcej o syntetycznym głosie we wczesnych stadiach środowiska.

![Podpowiedzi wizualne i opinie](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Przejrzystość konwersacji

Gdy konwersacje mieszczą się w nieoczekiwanych ścieżkach, rozważ tworzenie domyślnych odpowiedzi, które mogą pomóc zresetować oczekiwania, wzmocnić przejrzystość i skierować użytkowników w kierunku udanych ścieżek. Istnieją możliwości wykorzystania jawnego ujawnienia w rozmowie, jak również.

![Obsługa nieoczekiwanych ścieżek](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Off-zadanie &quot;lub&quot; osobiste pytania skierowane do agenta to dobry moment, aby przypomnieć użytkownikom o syntetycznym charakterze agenta i kierować ich do zaangażowania się z nim odpowiednio lub przekierować je do prawdziwej osoby.

![Obsługa pytań poza zadaniami](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Kiedy ujawnić

Istnieje wiele możliwości ujawnienia w całej podróży użytkownika. Projektowanie do pierwszego użycia, drugiego użycia, n-tego użycia &quot;&quot; ... ale także obejmować momenty braku podświetlenia przezroczystości, jak wtedy, gdy system popełnia błąd lub gdy użytkownik odkryje ograniczenie możliwości agenta&#39;s.

![Możliwości ujawniania informacji w trakcie podróży użytkownika](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Przykład standardowej podróży użytkownika cyfrowego asystenta, który podkreśla różne możliwości ujawniania informacji.

### <a name="up-front"></a>Góry

Optymalnym momentem do ujawnienia jest pierwszy raz, kiedy dana osoba wchodzi w interakcję z syntetycznym głosem.W scenariuszu osobistego asystenta głosowego byłoby to podczas dołączania lub przy pierwszym uruchomieniu użytkownika praktycznie rozpakuje środowisko. W innych scenariuszach może to być pierwszy odczyt syntetycznego głosu w witrynie sieci Web lub za pierwszym razem, gdy użytkownik wchodzi w interakcję z postacią wirtualną.

- [Przejrzyste wprowadzenie](#transparent-introduction)
- [Ujawnianie możliwości](#capability-disclosure)
- [Dostosowywanie i kalibracja](#customization-and-calibration)
- [Niejawne wskazówki](#implicit-cues--feedback)

### <a name="upon-request"></a>Życzenie

Użytkownicy powinni mieć możliwość łatwego dostępu do dodatkowych informacji, preferencji kontrolnych i otrzymywania przejrzystej komunikacji w dowolnym momencie podróży użytkownika, gdy jest to wymagane.

- [Zapewnienie możliwości dowiedzieć się więcej o tym, jak powstał głos](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Dostosowywanie i kalibracja](#customization-and-calibration)
- [Przejrzystość konwersacji](#conversational-transparency)

### <a name="continuously"></a>Stale

Użyj niejawnych wzorców projektowych, które stale zwiększają komfort użytkownika.

- [Ujawnianie możliwości](#capability-disclosure)
- [Niejawne wskazówki](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Gdy system ulegnie awarii

Użyj ujawnienia jako okazji do niepowodzenia z gracją.

- [Przejrzystość konwersacji](#conversational-transparency)
- [Zapewnienie możliwości dowiedzieć się więcej o tym, jak powstał głos](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Przekazanie ludziom](#conversational-transparency)



## <a name="additional-resources"></a>Zasoby dodatkowe
- [Wskazówki dotyczące botów firmy Microsoft](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Wskazówki dotyczące projektowania Cortany](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Wskazówki dotyczące projektowania mowy systemu Microsoft Windows UWP](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Wskazówki dotyczące poleceń głosowych w systemie Microsoft Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Dokumenty referencyjne

* [Ujawnienie talentu głosów](https://aka.ms/disclosure-voice-talent)
* [Wytyczne dotyczące odpowiedzialnego wdrażania technologii syntetycznego głosu](concepts-guidelines-responsible-deployment-synthetic.md)
* [Przegląd Gating](concepts-gating-overview.md)
* [Jak ujawnić](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Następne kroki

* [Ujawnienie talentu głosów](https://aka.ms/disclosure-voice-talent)
