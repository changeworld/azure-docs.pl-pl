---
title: Etyka i odpowiedzialne użytkowanie - Personalizer
titleSuffix: Azure Cognitive Services
description: Te wytyczne mają na celu pomoc w implementacji personalizacji w sposób, który pomaga budować zaufanie do firmy i usług. Pamiętaj, aby zatrzymać się na badania, uczyć się i rozmyślać na temat wpływu personalizacji na życie ludzi. W razie wątpliwości, zasięgnąć wskazówek.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: e422284b871214dbeca31b5dd17b9177a18ad3c8
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478096"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Wytyczne dotyczące odpowiedzialnej implementacji Personalizer

Aby ludzie i społeczeństwo mogli w pełni wykorzystać potencjał SI, implementacje muszą być zaprojektowane w taki sposób, aby zdobyły zaufanie tych, którzy dodają AI do swoich aplikacji i użytkowników aplikacji zbudowanych za pomocą AI. Te wytyczne mają na celu pomoc w implementacji Personalizer w sposób, który pomaga budować zaufanie do firmy i usług. Pamiętaj, aby zatrzymać się na badania, uczyć się i rozmyślać na temat wpływu personalizacji na życie ludzi. W razie wątpliwości, zasięgnąć wskazówek.

Wytyczne te nie mają na celu doradztwa prawnego i należy oddzielnie upewnić się, że aplikacja jest zgodna z szybkimi zmianami w prawie w tej dziedzinie i w twoim sektorze.

Ponadto, projektując aplikację przy użyciu Personalizer, należy wziąć pod uwagę szeroki zestaw obowiązków, które masz przy opracowywaniu dowolnego systemu AI zorientowanego na dane, w tym etyki, prywatności, bezpieczeństwa, bezpieczeństwa, włączenia, przejrzystości i odpowiedzialności. Możesz przeczytać więcej na ten temat w sekcji [Zalecane czytanie.](#recommended-reading)

Możesz użyć następującej zawartości jako początkowej listy kontrolnej i dostosować ją i uściślić do scenariusza. Ten dokument zawiera dwie główne sekcje: Pierwsza poświęcona jest wyróżnianiu kwestii odpowiedzialnego wykorzystania przy wyborze scenariuszy, funkcji i nagród dla Personalizer. Drugie podejście do zestawu wartości, które firma Microsoft uważa za należy wziąć pod uwagę przy tworzeniu systemów AI, i zapewnia zasłyniętą sugestie i zagrożenia dotyczące wpływu personalizatora na korzystanie z personalizatora.


## <a name="your-responsibility"></a>Twoja odpowiedzialność

Wszystkie wytyczne dotyczące odpowiedzialnego wdrażania opierają się na fundamencie, że deweloperzy i firmy korzystające z Personalizer są odpowiedzialne i odpowiedzialne za skutki korzystania z tych algorytmów w społeczeństwie. Jeśli tworzysz aplikację, która zostanie wdrożona przez twoją organizację, należy rozpoznać swoją rolę i odpowiedzialność za jej działanie i wpływ na ludzi. Jeśli projektujesz aplikację, która ma zostać wdrożona przez stronę trzecią, dojdź do wspólnego zrozumienia z nimi, kto jest ostatecznie odpowiedzialny za zachowanie aplikacji i udokumentować to zrozumienie.

Zaufanie opiera się na pojęciu spełnionych zobowiązań — należy wziąć pod uwagę użytkowników, społeczeństwo i ramy prawne, w których działają aplikacje, aby zidentyfikować jawne i dorozumiane zobowiązania, które mogą mieć.

Firma Microsoft nieustannie wkładamy wysiłek w swoje narzędzia i dokumenty, aby pomóc w realizacji tych obowiązków. [Prześlij opinię do firmy Microsoft,](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) jeśli uważasz, że dodatkowe narzędzia, funkcje produktu i dokumenty pomogą Ci wdrożyć te wskazówki dotyczące korzystania z personalizatora.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Czynniki odpowiedzialnego wdrażania Personalizer

Implementacja Personalizer może mieć wielką wartość dla użytkowników i firmy. Aby wdrożyć Personalizer odpowiedzialnie, zacznij od rozważenia następujących wytycznych, gdy:

* Wybieranie przypadków użycia w celu zastosowania personalizacji.
* Budowanie [funkcji nagradzania](concept-rewards.md).
* Wybór [funkcji](concepts-features.md) dotyczących kontekstu i możliwych działań, które będą używane do personalizacji.


## <a name="choosing-use-cases-for-personalizer"></a>Wybór przypadków użycia personalizatora

Korzystanie z usługi, która uczy się personalizować zawartość i interfejsy użytkownika jest przydatne. Może być również błędnie stosowany, jeśli sposób personalizacji tworzy negatywne skutki uboczne w świecie rzeczywistym, w tym jeśli użytkownicy nie są świadomi personalizacji zawartości.

Przykłady zastosowań Personalizer z podwyższonym potencjałem negatywnych skutków ubocznych lub brak przejrzystości obejmują scenariusze, w których "nagroda" zależy od wielu długoterminowych złożonych czynników, które, gdy nadmiernie uproszczone do natychmiastowej nagrody może mieć niekorzystne wyniki dla osób fizycznych. Są one uważane za "wtórne" wybory lub wybory, które wiążą się z ryzykiem szkody. Przykład:


* **Finanse**: Personalizacja ofert produktów pożyczkowych, finansowych i ubezpieczeniowych, w których czynniki ryzyka są oparte na danych, o których osoby fizyczne nie wiedzą, nie mogą uzyskać lub nie mogą się spierać.
* **Edukacja**: Personalizacja szeregów dla kursów szkolnych i instytucji edukacyjnych, w których zalecenia mogą propagować uprzedzenia i zmniejszać świadomość użytkowników na temat innych opcji.
* **Demokracja i uczestnictwo obywatelskie**: Personalizacja treści dla użytkowników w celu wpływania na opinie jest konsekwencją i manipulacją.
* **Ocena nagrody innych firm:** Personalizacja przedmiotów, w których nagroda jest oparta na ocenie użytkownika przez osoby trzecie, zamiast nagrody generowanej przez własne zachowanie użytkownika.
* **Nietolerancja na eksplorację**: Każda sytuacja, w której zachowanie poszukiwawcze Personalizer może spowodować szkody.

Przy wyborze przypadków użycia personalizatora:

* Rozpocznij proces projektowania, biorąc pod uwagę, w jaki sposób personalizacja pomaga użytkownikom.
* Należy wziąć pod uwagę negatywne konsekwencje w świecie rzeczywistym, jeśli niektóre elementy nie są klasyfikowane dla użytkowników ze względu na wzorce personalizacji lub eksploracji.
* Zastanów się, czy przypadek użycia stanowi zautomatyzowane przetwarzanie, które znacząco wpływa na osoby, których dane dotyczą, które jest regulowane na mocy artykułu 22 [RODO](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) lub innych przepisów.
* Rozważ samospełniające się pętle proroctwa. Może się tak zdarzyć, jeśli nagroda za personalizację trenuje model, więc może następnie dodatkowo wykluczyć grupę demograficzną z dostępu do odpowiednich treści. Na przykład większość ludzi w dzielnicy o niskich dochodach nie otrzymuje oferty ubezpieczenia premium i powoli nikt w okolicy nie ma tendencji do zobaczenia oferty, jeśli nie ma wystarczającej liczby poszukiwań.
* Zapisz kopie modeli i zasad uczenia się w przypadku, gdy jest to konieczne do odtworzenia Personalizer w przyszłości. Można to zrobić okresowo lub w każdym okresie odświeżania modelu.
* Należy wziąć pod uwagę poziom eksploracji odpowiedni dla przestrzeni i jak go używać jako narzędzia do łagodzenia efektów "komory echa".


## <a name="selecting-features-for-personalizer"></a>Wybieranie funkcji personalizatora

Personalizacja treści zależy od posiadania przydatnych informacji o zawartości i użytkowniku. Należy pamiętać, że w przypadku niektórych aplikacji i branż niektóre funkcje użytkownika mogą być bezpośrednio lub pośrednio uważane za dyskryminujące i potencjalnie nielegalne.

Należy wziąć pod uwagę wpływ tych funkcji:

* **Dane demograficzne użytkowników**: Funkcje dotyczące płci, płci, wieku, rasy, religii: Te cechy mogą nie być dozwolone w niektórych aplikacjach ze względów regulacyjnych i nie może być etyczne personalizować wokół nich, ponieważ personalizacja propaguje uogólnienia i stronniczość. Przykładem tej propagacji stronniczości jest ogłoszenie o pracę dla inżynierii nie są wyświetlane osobom starszym lub grupom odbiorców ze względu na płeć.
* **Informacje o lokalizacjach:** W wielu miejscach na świecie informacje o lokalizacji (takie jak kod pocztowy, kod pocztowy lub nazwa sąsiedztwa) mogą być wysoce skorelowane z dochodami, rasą i religią.
* **Postrzeganie przez użytkownika uczciwości**: Nawet w przypadkach, gdy aplikacja podejmuje rozsądne decyzje, należy wziąć pod uwagę wpływ użytkowników dostrzegających, że zawartość wyświetlana w aplikacji zmienia się w sposób, który wydaje się być skorelowany z funkcjami, które byłyby dyskryminujące.
* **Niezamierzone bias w funkcje:** Istnieją typy uprzedzeń, które mogą być wprowadzone przy użyciu funkcji, które wpływają tylko na podzbiór populacji. Wymaga to dodatkowej uwagi, jeśli funkcje są generowane algorytmicznie, na przykład podczas korzystania z analizy obrazu do wyodrębniania elementów w analizie obrazu lub tekstu w celu odnajdywanie jednostek w tekście. Upewnij się, że znasz cechy usług, których używasz do tworzenia tych funkcji.

Zastosuj następujące praktyki przy wyborze funkcji do wysyłania w kontekstach i działaniach do Personalizer:

* Należy wziąć pod uwagę legalność i etykę korzystania z niektórych funkcji dla niektórych aplikacji, i czy niewinnie wyglądające funkcje mogą być proxy dla innych, które chcesz lub należy unikać,
* Bądź przejrzysty dla użytkowników, że algorytmy i analiza danych są używane do personalizacji opcji, które widzą.
* Zadaj sobie pytanie: Czy moi użytkownicy dbają i są szczęśliwi, gdybym użył tych informacji do personalizacji treści dla nich? Czy czuję się komfortowo pokazując im, jak podjęto decyzję o podkreśleniu lub ukryciu niektórych przedmiotów?
* Użyj danych behawioralnych, a nie klasyfikacji lub segmentacji na podstawie innych cech. Informacje demograficzne były tradycyjnie wykorzystywane przez sprzedawców detalicznych ze względów historycznych - atrybuty demograficzne wydawały się proste do zebrania i działania przed erą cyfrową , ale pytanie, jak istotne są informacje demograficzne, gdy masz rzeczywistą interakcję, dane kontekstowe i historyczne, które bardziej odnoszą się do preferencji i tożsamości użytkowników.
* Zastanów się, jak zapobiec "fałszowaniu" funkcji przez złośliwych użytkowników, co w przypadku wykorzystania w dużych ilościach może prowadzić do szkolenia Personalizer w wprowadzający w błąd sposób, aby celowo zakłócać, zawstydzać i nękać niektóre klasy użytkowników.
* Jeśli jest to właściwe i wykonalne, zaprojektuj aplikację, aby umożliwić użytkownikom zgodę lub zrezygnować z posiadania niektórych funkcji osobistych. Można je zgrupować, na przykład "Informacje o lokalizacji", "Informacje o urządzeniu", "Historia wcześniejszych zakupów" itp.


## <a name="computing-rewards-for-personalizer"></a>Nagrody komputerowe dla Personalizer

Personalizer stara się poprawić wybór, które działania do nagrody na podstawie wyniku nagrody dostarczone przez logikę biznesową aplikacji.

Dobrze zbudowany wynik nagrody będzie działać jako krótkoterminowy proxy do celu biznesowego, który jest związany z misją organizacji.

Na przykład nagradzanie kliknięć sprawi, że Usługa Personalizer będzie szukać kliknięć kosztem wszystkiego innego, nawet jeśli to, co zostanie kliknięty, rozprasza lub nie jest związane z wynikiem biznesowym.

Jako kontrastujący przykład, witryna z wiadomościami może chcieć ustawić nagrody powiązane z czymś bardziej znaczącym niż kliknięcia, na przykład "Czy użytkownik spędzał wystarczająco dużo czasu na przeczytaniu treści?" "Czy kliknęli na odpowiednie artykuły lub referencje?". Z Personalizer łatwo jest ściśle powiązać dane z nagrodami. Ale uważaj, aby nie zmylić krótkoterminowego zaangażowania użytkowników z dobrymi wynikami.

### <a name="unintended-consequences-from-reward-scores"></a>Niezamierzone konsekwencje z wyników nagród
Wyniki nagród mogą być tworzone z najlepszymi intencjami, ale nadal mogą powodować nieoczekiwane konsekwencje lub niezamierzone wyniki dotyczące sposobu, w jaki Personalizator plasuje zawartość.

Rozważmy następujące przykłady:

* Nagradzanie personalizacji treści wideo na procentie oglądanej długości filmu prawdopodobnie będzie miało tendencję do klasyfikowania krótszych filmów.
* Nagradzanie akcji w mediach społecznościowych, bez analizy nastrojów, w jaki sposób są udostępniane lub sama treść, może prowadzić do rankingu obraźliwe, niemoderowane lub zapalnych treści, które mają tendencję do podżegania do wielu "zaangażowania", ale dodaje niewiele wartości.
* Nagradzanie akcji na elementy interfejsu użytkownika, że użytkownicy nie spodziewają się zmienić może zakłócać użyteczność i przewidywalność interfejsu użytkownika, gdzie przyciski są zaskakująco zmiana lokalizacji lub celu bez ostrzeżenia, co utrudnia niektórych grup użytkowników do utrzymania produktywnej.

Zaimplementuj następujące najlepsze rozwiązania:

* Eksperymenty offline z systemem przy użyciu różnych metod nagradzania, aby zrozumieć wpływ i skutki uboczne.
* Oceń swoje funkcje nagrody i zadaj sobie pytanie, w jaki sposób skrajnie naiwna osoba naginałaby swoją interpretację i osiągała z nią niepożądane skutki.


## <a name="responsible-design-considerations"></a>Odpowiedzialne zagadnienia projektowe

Poniżej przedstawiono obszary projektowania odpowiedzialnych wdrożeń SI. Dowiedz się więcej abut tej struktury w [Przyszłości Computed](https://news.microsoft.com/futurecomputed/).

![Wartości AI z przyszłych obliczeń](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Odpowiedzialność
*Osoby, które projektują i wdrażają systemy AI, muszą być odpowiedzialne za działanie ich systemów.*

* Tworzenie wewnętrznych wytycznych dotyczących wdrażania personalizatora, dokumentowania i przekazywania ich zespołowi, kadrze kierowniczej i dostawcom.
* Wykonywanie okresowych przeglądów sposobu obliczania wyników nagród, wykonywanie ocen offline, aby zobaczyć, jakie funkcje wpływają na personalizator, i używanie wyników w celu wyeliminowania niepotrzebnych i niepotrzebnych funkcji.
* Komunikuj wyraźnie użytkownikom, w jaki sposób personalizator jest używany, w jakim celu i z jakimi danymi.
* Archiwizuj informacje i zasoby , takie jak modele, zasady uczenia się i inne dane , które Personalizer używa do działania, aby móc powielać wyniki.

### <a name="transparency"></a>Przezroczystość
*Systemy AI powinny być zrozumiałe*. Z Personalizer:

* *Podaj użytkownikom informacje o tym, jak zawartość została spersonalizowana.* Na przykład można wyświetlić użytkownikom przycisk `Why These Suggestions?` z etykietą pokazujący, które najważniejsze funkcje użytkownika i akcje odegrały rolę w wynikach personalizatora.
* Upewnij się, że warunki użytkowania sprawiają, że będziesz używać informacji o użytkownikach i ich zachowaniu, aby spersonalizować środowisko.

### <a name="fairness"></a>Sprawiedliwość
*AI Systems powinien traktować wszystkich ludzi sprawiedliwie*.

* Nie używaj Personalizer do przypadków użycia, gdy wyniki są długoterminowe, wtórne, lub pociągają za sobą rzeczywistą szkodę.
* Nie używaj funkcji, które nie są odpowiednie do personalizowania zawartości lub które mogą pomóc w propagowaniu niepożądanych uprzedzeń. Na przykład każda osoba o podobnej sytuacji finansowej powinna zobaczyć te same spersonalizowane rekomendacje dla produktów finansowych.
* Zrozumienie uprzedzeń, które mogą istnieć w funkcjach, które pochodzą z edytorów, narzędzi algorytmicznych lub samych użytkowników.

### <a name="reliability-and-safety"></a>Niezawodność i bezpieczeństwo
*Systemy AI powinny działać niezawodnie i bezpiecznie.* Dla Personalizer:

* *Nie należy udostępniać personalizatora działań, których nie należy wybierać.* Na przykład niewłaściwie filmy powinny być odfiltrowane z akcji, aby spersonalizować, jeśli sformułowanie rekomendacji dla użytkownika anonimowego lub niepełnoletniego.
* *Zarządzaj swoim modelem Personalizer jako zasobem biznesowym.*  Zastanów się, jak często zapisywać i odtwarzać kopii zapasowych modelu i zasady uczenia się za pętli Personalizer i w inny sposób traktować go jako ważny zasób biznesowy. Odtwarzanie wyników z przeszłości jest ważne dla samokontroli i poprawy pomiaru.
* *Udostępnij kanały, aby uzyskać bezpośrednią informację zwrotną od użytkowników.* Oprócz kontroli bezpieczeństwa kodowania, aby upewnić się, że tylko właściwi odbiorcy widzą odpowiednie treści, udostępnij użytkownikom mechanizm przekazywania informacji zwrotnych, które mogą być zaskakujące lub niepokojące. Zwłaszcza jeśli zawartość pochodzi od użytkowników lub stron trzecich, rozważ użycie moderatora zawartości firmy Microsoft lub dodatkowych narzędzi do przeglądania i sprawdzania poprawności zawartości.
* *Wykonywanie częstych ocen offline*. Pomoże to monitorować trendy i upewnić się, że skuteczność jest znana.
* *Ustanowienie procesu wykrywania i działania na złośliwą manipulację*. Są podmioty, które skorzystają z uczenia się w zakresie uczenia się przez systemy uczenia się z uczenia się w swoim środowisku, aby przenieść wynik w kierunku swoich celów. Jeśli korzystanie z Personalizer jest w stanie wpłynąć na ważne wybory, upewnij się, że odpowiednie środki do wykrywania i łagodzenia tych klas ataków, w tym przegląd człowieka w odpowiednich okolicznościach.

### <a name="security-and-privacy"></a>Zabezpieczenia i ochrona prywatności
*Systemy AI powinny być bezpieczne i szanować prywatność.* Podczas korzystania z Personalizer:

* *Z góry poinformuj użytkowników o gromadzonych danych oraz o tym, w jaki sposób są wykorzystywane, oraz uzyskaj ich wcześniej zgodę,* przestrzegając lokalnych i branżowych przepisów.
* *Zapewnij kontrolę użytkownika chroniącą prywatność.* W przypadku aplikacji przechowujących dane osobowe należy rozważyć udostępnienie łatwego do znalezienia przycisku dla funkcji, takich jak:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

W niektórych przypadkach mogą one być wymagane z prawnego punktu widzenia. Należy wziąć pod uwagę kompromisy w modelach przekwalifikowania okresowo, aby nie zawierają śladów usuniętych danych.

### <a name="inclusiveness"></a>Brak wykluczeń
*Zaspokajaj szeroki zakres ludzkich potrzeb i doświadczeń.*
* *Zapewnij spersonalizowane środowisko dla interfejsów z obsługą ułatwień dostępu.* Wydajność, która wynika z dobrej personalizacji - stosowanej w celu zmniejszenia wysiłku, ruchu i niepotrzebnego powtarzania interakcji - może być szczególnie korzystna dla osób niepełnosprawnych.
* *Dostosuj zachowanie aplikacji do kontekstu*. Personalizer można użyć do rozróżniania między intencjami w czacie bot, na przykład, jak właściwa interpretacja może być kontekstowe i jeden rozmiar może nie pasować do wszystkich.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktywna gotowość do zwiększenia ochrony danych i zarządzania nimi

Trudno jest przewidzieć konkretne zmiany w kontekście regulacyjnym, ale ogólnie rzecz biorąc, dobrze byłoby wyjść poza minimalne ramy prawne w zapewnianiu pełnego szacunku wykorzystania danych osobowych oraz zapewnianiu przejrzystości i wyboru związanego z algorytmicznym podejmowaniem decyzji.


* Rozważ planowanie z wyprzedzeniem sytuacji, w której mogą istnieć nowe ograniczenia dotyczące danych gromadzonych od osób fizycznych i istnieje potrzeba pokazania, w jaki sposób zostały wykorzystane do podejmowania decyzji.
* Należy wziąć pod uwagę dodatkową gotowość, w której użytkownicy mogą obejmować marginalizowane populacje narażone na zagrożenia, dzieci, użytkowników znajdujących się w trudnej sytuacji ekonomicznej lub użytkowników podatnych na wpływ manipulacji algorytmicznej.
* Należy wziąć pod uwagę powszechne niezadowolenie z tego, w jaki sposób programy i algorytmy do kierowania na odbiorców i wpływy na odbiorców oraz jak uniknąć udowodnionych błędów strategicznych.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktywne oceny w cyklu życia projektu

Rozważ utworzenie metod zgłaszania przez członków zespołu, użytkowników i właścicieli firm rozwiązań dotyczących odpowiedzialnego użytkowania oraz tworzenie procesu, który nadaje priorytet ich rozwiązaniu i zapobiega odwetowi.

Każda osoba myśląca o skutkach ubocznych korzystania z jakiejkolwiek technologii jest ograniczona przez ich perspektywy i doświadczenia życiowego. Poszerz zakres dostępnych opinii, wprowadzając bardziej zróżnicowane głosy do swoich zespołów, użytkowników lub rad doradczych; jest to możliwe i zachęcane do wypowiadania się. Rozważ materiały szkoleniowe i szkoleniowe, aby jeszcze bardziej poszerzyć wiedzę zespołu w tej dziedzinie i dodać możliwość omówienia złożonych i wrażliwych tematów.

Należy rozważyć traktowanie zadań dotyczących odpowiedzialnego użycia, podobnie jak inne zadania przecięcia poprzecznego w cyklu życia aplikacji, takie jak zadania związane z środowiskiem użytkownika, zabezpieczeniami lub funkcjami DevOps. Te zadania i ich wymagania nie mogą być refleksją. Należy omówić i zweryfikować odpowiedzialne stosowanie w całym cyklu życia aplikacji.

## <a name="questions-and-feedback"></a>Pytania i opinie

Firma Microsoft nieustannie wkładamy wysiłek w narzędzia i dokumenty, które pomagają w działaniach na tych obowiązkach. Nasz zespół zachęca do [przekazywania opinii firmie Microsoft,](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) jeśli uważasz, że dodatkowe narzędzia, funkcje produktu i dokumenty pomogą Ci wdrożyć te wytyczne dotyczące korzystania z personalizatora.

## <a name="recommended-reading"></a>Zalecana lektura

* Zobacz sześć zasad odpowiedzialnego rozwoju SI opublikowanych w książce ze stycznia 2018 [r., The Future Computed](https://news.microsoft.com/futurecomputed/)
* [Kto jest właścicielem przyszłości?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) przez Jaron Lanier.
* [Broń zniszczenia matematyki](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) przez - Cathy O'Neil
* [Etyki i nauki o danych](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) przez DJ Patil, Hilary Mason, Mike Loukides.
* [Kodeks Etyki ACM](https://www.acm.org/code-of-ethics)
* [Ustawa o niedyskryminacji informacji genetycznej - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Zasady FATML dla algorytmów odpowiedzialnych](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Następne kroki

[Cechy: działanie i kontekst](concepts-features.md).
