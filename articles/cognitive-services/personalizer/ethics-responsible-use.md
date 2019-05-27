---
title: Etyki i użycie odpowiada - Personalizer
titleSuffix: Azure Cognitive Services
description: Te wytyczne są mających na celu pomoc można zaimplementować personalizacji w sposób, który pomaga w tworzeniu relacji zaufania w firmie i usługi. Pamiętaj wstrzymać badań, ucz się i zamierzone wpływu personalizacji na życie osób. W razie wątpliwości należy starać się wskazówek.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 437dc1fba2502602109483aa9d6f25b4265af26f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239893"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Wytyczne dotyczące implementacji odpowiada Personalizer

Osoby i społeczeństwa wykorzystywanie pełnego potencjału sztucznej Inteligencji implementacje muszą być zaprojektowane w taki sposób, że zyskują zaufanie Dodawanie sztucznej Inteligencji do aplikacji i użytkowników aplikacji skompilowanej za pomocą sztucznej Inteligencji. Te wytyczne są mających na celu pomoc można zaimplementować Personalizer w sposób, który pomaga w tworzeniu relacji zaufania w firmie i usługi. Pamiętaj wstrzymać badań, ucz się i zamierzone wpływu personalizacji na życie osób. W razie wątpliwości należy starać się wskazówek.

Te wytyczne nie służą jako porady prawne, a następnie oddzielnie upewnij się, że aplikacja spełnia dynamicznym rozwoju prawa, w tym obszarze, jak i w sektorze usługi.

Ponadto w projektowaniu aplikacji za pomocą Personalizer, należy rozważyć szeroką gamę obowiązków, które mają podczas opracowywania wszelkich przetwarzających dane systemu sztucznej Inteligencji, w tym etyki, ochrony prywatności, zabezpieczeń, bezpieczeństwa, dołączania, przejrzystość i odpowiedzialność. Możesz dowiedzieć się więcej o tych [zalecana lektura](#recommended-reading) sekcji.

Można użyć następującej zawartości jako listę kontrolną starter i dostosować i dostosuj je do danego scenariusza. Ten dokument ma dwa główne sekcje: Pierwszy jest dedykowany do wyróżniania uwagi dotyczące użycia odpowiada podczas wybierania scenariuszy, funkcje i korzyści dla Personalizer. Drugi take zbiór wartości, które firma Microsoft uważa móc należy uwzględnić podczas tworzenia systemy sztucznej Inteligencji i zapewnia wiarygodne sugestie i ryzyka na użytkowanie Personalizer wpływ je. 


## <a name="your-responsibility"></a>Odpowiedzialny za

Wszystkie wytyczne dotyczące implementacji odpowiada kompilacji na fundamencie czy deweloperom i firmom przy użyciu Personalizer są odpowiedzialne i odpowiedzialni za pomocą tych algorytmów w społeczeństwie skutki. Jeśli tworzysz aplikację, która zostanie wdrożona w organizacji, możesz rozpoznać rolę i odpowiedzialność za jego działania i jak wpływa na ludzi. W przypadku projektowania aplikacji do wdrożenia przez stronę trzecią dostarczane do udostępnionej wiedzy z nimi, kto jest ponoszą ostateczną odpowiedzialność za zachowanie aplikacji i zrozumienie tego dokumentu.

Zaufanie jest oparta na pojęcie spełnione zobowiązań — należy wziąć pod uwagę użytkowników, społeczeństwa i ramy prawne aplikacji działa, aby zidentyfikować jawne i niejawne zobowiązania, które mogą mieć.

Microsoft stale jest wprowadzenie nakład pracy do jej narzędzi i dokumenty, aby pomóc Ci zajmującym się obowiązków. [Przesyłanie opinii do firmy Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) Jeśli uważasz, że dodatkowe narzędzia, funkcje produktu i dokumentów umożliwia implementuje te zalecenia dotyczące używania Personalizer.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Czynniki odpowiedzialnego implementowania Personalizer

Implementowanie Personalizer mogą być zaoferowanie większych korzyści użytkownikom i firmie. Aby zaimplementować Personalizer odpowiedzialnego, start, biorąc pod uwagę następujące wskazówki podczas:

* Wybieranie przypadki użycia, aby zastosować personalizacji.
* Tworzenie [nagradzaj funkcji](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-rewards.md).
* Wybieranie, które [funkcji](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-features.md) o kontekście i możliwe akcje będą używane na potrzeby personalizacji.


## <a name="choosing-use-cases-for-personalizer"></a>Wybieranie przypadki użycia dla Personalizer

Korzystanie z usługi, która uczy się personalizowania zawartości oraz interfejsów użytkownika jest przydatne. Można również misapplied, jeśli sposób personalizacji tworzy negatywne skutki uboczne w świecie rzeczywistym, w tym przypadku świadomości Personalizacja zawartości możliwej do użytkowników. 

Przykłady użycia Personalizer zwiększyć możliwości dla negatywne skutki uboczne lub brak przejrzystości obejmują scenariusze, gdzie "nagradzania" zależy od wielu długoterminowych złożone czynniki, podczas nadmiernie uproszczona do natychmiastowego nagradzania może mieć niekorzystne wyniki dla użytkowników indywidualnych. Zwykle są uważane za "za" wyborów lub opcji, które obejmują ryzyko uszkodzenia. Na przykład: 


* **Finanse**: Personalizowanie oferuje pożyczki finansowe i ubezpieczeniowe produktów, w którym czynniki ryzyka są oparte na danych osób nie wiedzieć o, nie można uzyskać lub nie sporów. 
* **Edukacja**: Personalizowanie rangę kursów szkoły i instytucji edukacyjnych, gdzie zalecenia mogą Propagacja odchyleń i ograniczyć użytkowników świadomość inne opcje.
* **Demokracji strumieniowo rozgrywki i uczestniczenia Civic**: Personalizowanie zawartości dla użytkowników z celem wywieranie opinii jest wtórne i manipulacyjne.
* **Oceny innych firm za wynagrodzeniem**: Personalizowanie elementów, których nagrody opiera się na ostatnim 3 oceny innych firm użytkownika, zamiast nagrody, generowane przez działanie przez użytkownika.
* **Nietolerancji eksplorację**: Każdej sytuacji, w których zachowanie eksploracji Personalizer może powodować uszkodzenie.

Podczas wybierania przypadki użycia dla Personalizer:

* Uruchom proces projektowania, biorąc pod uwagę, jak personalizacji pomaga użytkownikom.
* Jeśli niektóre elementy nie są randze spośród wszystkich dokumentów dla użytkowników z powodu wzorców personalizacji lub eksploracji, należy wziąć pod uwagę negatywne konsekwencje w świecie rzeczywistym.
* Należy wziąć pod uwagę własnym spełnienia prophecy pętli. To może być możliwe jeśli nagradzania personalizacji szkolenie modeli model, dzięki czemu może następnie dalsze wykluczyć demograficznych grupy dostęp do odpowiedniej zawartości. Na przykład większość osób w otoczeniu o niskim dochodzie nie uzyskać ofertę ubezpieczeniowe premium i powoli nikt w klubie zwykle zobaczysz tę ofertę w ogóle.
* Zapisywanie kopii zasad uczenia i modeli, w przypadku, gdy jest konieczne odtworzenie Personalizer w przyszłości. Można to zrobić, okresowo lub każdego okresu odświeżania modelu.
* Należy wziąć pod uwagę stopień eksploracji odpowiednia dla miejsca i sposobu używania go jako narzędzie do złagodzenia skutków "echo komory".


## <a name="selecting-features-for-personalizer"></a>Wybieranie funkcji Personalizer

Personalizacji zawartości zależy od tego, o przydatnych informacji o zawartości i użytkownika. Należy pamiętać, w przypadku niektórych aplikacji i branż, niektóre użytkownika, do którego funkcje mogą zostać bezpośrednio lub pośrednio uwzględnione porównawczego i potencjalnie niedozwolone.

Należy wziąć pod uwagę wpływ tych funkcji:

* **Dane demograficzne użytkownika**: Funkcje dotyczące płeć, płeć, wiek, wyścigu, religii: Te funkcje mogą nie mogą w niektórych aplikacjach ze względów prawnych i nie może być etyczny spersonalizować wokół nich, ponieważ personalizacji będzie propagowane generalizacji i odchylenie. Przykładem tego odchylenia propagacji jest zadaniem publikowanie do zespołu inżynieryjnego nie jest wyświetlany starszych lub na podstawie płeć odbiorców.
* **Informacje o ustawieniach regionalnych**: W wielu miejscach świata informacje o lokalizacji (np. kod pocztowy, kod pocztowy lub Nazwa klubu) może ściśle powiązane z przychodów, wyścigu i religii.
* **Użytkownik postrzegania sprawiedliwe**: Nawet w przypadkach, gdy aplikacja jest podejmowania trafnych decyzji należy wziąć pod uwagę wpływ odczuwaniu tę zawartość wyświetlana w zmiany aplikacji w taki sposób, że wydaje się zostać skorelowane z funkcji, które byłyby porównawczego użytkowników.
* **Niezamierzone odchylenie w funkcjach**:  Istnieją typy odchyleń, które mogą być wprowadzane przy użyciu funkcji, które mają wpływ tylko na podzestaw populacji. Wymaga to szczególną uwagę, gdy funkcje są generowane algorithmically, takie jak kiedy obrazów przy użyciu analizy, aby wyodrębnić elementy w obrazu lub tekstu analizy, aby odnaleźć jednostki w tekście. Uświadomić sobie właściwości usług, których używasz do tworzenia tych funkcji.

Wybieranie funkcji do wysyłania w kontekstach i akcje do Personalizer obowiązują następujące rozwiązania:

* Należy wziąć pod uwagę legalności i etyki używania pewnych funkcji w niektórych aplikacjach i czy funkcje nieszkodliwie wyszukiwania mogą być serwerów proxy przez inne osoby mają być, czy należy unikać
* Można w sposób niewidoczny dla użytkowników czy algorytmów i analiza danych są używane do personalizowania opcje zobaczą.
* Zadaj sobie: Czy Moi użytkownicy care i pewnością ucieszy wiadomość, jeśli te informacje używane do personalizowania zawartości dla nich? Czy mogę bez obaw pokazujący, jak decyzja została podjęta wyróżnić lub ukrywanie niektórych elementów?
* Za pomocą zachowań zamiast klasyfikację lub segmentacji danych oparte na podstawie innych cech. Informacje demograficzne tradycyjnie był używany przez sprzedawców detalicznych ze względów historycznych — atrybuty demograficznych sprawiał proste do zbierania i operowania przed era cyfrowych, - pytanie, ale w jaki sposób jest istotne informacje demograficzne, gdy rzeczywista interakcji kontekstowe i danymi historycznymi dokładniej związanego z preferencji i tożsamości użytkowników.
* Pomyśl, jak zapobiec funkcji "fałszowania" przez złośliwych użytkowników, których wykorzystana w dużych liczb może prowadzić do szkolenia Personalizer w mylące sposoby celowo zakłócać embarrass i prześladowania niektórych klas użytkowników. 
* Podczas stosowne i możliwe, należy zaprojektować aplikację, aby umożliwić użytkownikom zgadzaj lub zrezygnować z konieczności niektórych funkcji osobistych używanych. Można je pogrupować, takie jak "Informacje o lokalizacji", "Informacje o urządzeniu", "Ostatnich historię zakupów" itp.


## <a name="computing-rewards-for-personalizer"></a>Korzyści dla Personalizer obliczeń

Personalizer dokłada wszelkich starań poprawić wybór akcję do osób trzecich, na podstawie wyniku nagrody, dostarczone przez logikę biznesową aplikacji.

Wynik dobrze utworzonych za wynagrodzeniem będzie pełnił rolę krótkoterminowej serwera proxy do celu biznesowego, który jest powiązany z misji organizacji.

Na przykład nagradzanie na kliknięcia spowoduje, że kliknięć seek usługi Personalizer kosztem wszystko inne, nawet jeśli kliknięciu na rozprasza uwagę lub nie wiązanej, do wyników biznesowych.

Przykład kontrastujące witrynę wiadomości warto ustawić rewards powiązane coś bardziej opisowe niż kliknie, takie jak "Czy użytkownika możesz wydać wystarczająco dużo czasu do odczytu zawartości?" "Czy ich kliknij odpowiednie artykuły lub odwołania?". Za pomocą Personalizer jest można łatwo powiązać metryk do nagrody. Jednak należy uważać, aby nie confound krótkoterminowej stopnia zaangażowania użytkowników dzięki dobre wyniki.

### <a name="unintended-consequences-from-reward-scores"></a>Niezamierzone konsekwencje z Silicon wyniki
Wyniki nagradzania mogły zostać utworzone za pomocą najlepszych zamiarach, ale nadal można też utworzyć w sposób Personalizer klasyfikuje zawartość nieoczekiwanych konsekwencji lub niepożądanych wyników. 

Należy wziąć pod uwagę następujące przykłady:

* Nagradzanie wideo Personalizacja zawartości na wartość procentowa długości klipu wideo, którzy oglądali transmisje stacji będzie prawdopodobnie mają tendencję do rangi krótszą filmy wideo.
* Nagradzanie udziałów mediów społecznościowych, bez analiza tonacji w sposób udostępniania lub zawartości, może prowadzić do klasyfikacji obraźliwe, Niemoderowana lub obraźliwych zawartość, która zwykle tam wiele "zaangażowania", ale dodaje korzyści.
* Nagradzanie działania w elementach interfejsu użytkownika, które użytkownicy oczekują, aby zmienić może zakłócać użyteczność i przewidywalność interfejsu użytkownika, w którym przyciski zaskakująco zmieniania lokalizacji lub przeznaczenia bez ostrzeżenia, co utrudnia dla niektórych grupy użytkowników, aby utrzymywać wydajność pracy.

Implementuje te najlepsze rozwiązania:

* Uruchamianie eksperymentów w trybie offline za pomocą systemu za pomocą nagradzania różnych metod, aby zrozumieć wpływ i efekty uboczne.
* Oceń swoje funkcje za wynagrodzeniem i zadać sobie pytanie, jak będzie bardzo prostego osoby zakrzywia interpretacji i dotrzeć do niepożądanych wyników z nim.


## <a name="responsible-design-considerations"></a>Zagadnienia dotyczące projektowania odpowiada

Dostępne są następujące obszary projektu odpowiada implementacji sztucznej Inteligencji. Dowiedz się więcej dotyczący ta struktura w [obliczane przyszłość](https://news.microsoft.com/futurecomputed/).

![Wartości sztucznej Inteligencji z przyszłość obliczonych](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Odpowiedzialność
*Osoby, które projektowanie i wdrażanie systemów sztucznej Inteligencji musi być odpowiedzialni sposób działania systemów*. 

* Utwórz wewnętrzne wytyczne dotyczące implementacji Personalizer, dokumentów i przekazywania ich do zespołu, dyrektorów i dostawców.
* Okresowe przeglądy jak są obliczane nagradzania wyniki wykonania, przeprowadzania ocen w trybie offline, aby zobaczyć, jakie funkcje mają wpływ na Personalizer i użyj wyników, aby wyeliminować funkcje niepotrzebne i niepotrzebne.
* Komunikują się wyraźnie użytkownikom jak używany Personalizer, do jakich celów i z jakich danych.
* Archiwizowanie informacji i zasobów — takich jak modeli, zasady nauki i inne dane — Personalizer korzysta z funkcji, aby można było odtworzyć wyników.

### <a name="transparency"></a>Przezroczystości
*Sztuczna Inteligencja systemów należy Understandable*. Za pomocą Personalizer:

* *Zapewnij użytkownikom dowiedzieć się, jak został spersonalizowany zawartości.* Na przykład, możesz wyświetlić użytkowników przycisku `Why These Suggestions?` przedstawiający, które najważniejsze funkcje użytkownika i akcje odtwarzania roli w wynikach Personalizer.
* Upewnij się, warunków użytkowania upewnij Użyj wspomnieć, aby spersonalizować środowisko użyjesz informacje o użytkownikach i ich zachowania.

### <a name="fairness"></a>Sprawiedliwe
*Systemy sztucznej Inteligencji powinien traktować wszystkie osoby dość*.

* Nie używaj Personalizer do zastosowań związanych wyniki długoterminowe, wtórne lub obejmują rzeczywistych uszkodzeń.
* Nie korzystać z funkcji, które nie są odpowiednie do personalizacji zawartości przy użyciu lub, pomoc propagowane odchyleń niepożądane. Na przykład każda osoba mająca podobne sytuacji finansowej powinien zostać wyświetlony ten sam spersonalizowane zalecenia dotyczące produktów finansowych.
* Dowiedz się, czym kierujemy podczas, które mogą występować w funkcjach, które pochodzą z edytory, narzędzia konsolidatorze lub samych użytkowników.

### <a name="reliability-and-safety"></a>Niezawodność i bezpieczeństwo
*Sztuczna Inteligencja systemów należy wykonywać, niezawodnie i bezpiecznie*. Aby uzyskać Personalizer:

* *Nie zapewniają operacje do Personalizer, który nie powinien zostać wybrany*. Na przykład niewłaściwie filmy ma być filtrowany poza akcje spersonalizować, jeśli zalecenia dotyczące anonimowym lub niepełną wieku użytkownika.
* *Zarządzanie modelu Personalizer jako zasobem biznesowym*.  Zastanów się, jak często zapisać i utworzyć kopię zapasową modelu i nauka zasad za pętlę metodyki Personalizer, a w przeciwnym razie traktować jako zasobem biznesowym ważne. Odtwarzanie ostatnie wyniki ważne jest, doskonalenia własnym inspekcji i pomiarowych.
* *Podaj kanały, aby uzyskać informacji zwrotnych od użytkowników*. Oprócz kodowania kontrole bezpieczeństwa, aby upewnić się, odpowiednich odbiorców zobacz odpowiednią treść, zapewniają mechanizm opinie użytkowników do zawartości raportów, które mogą być Zaskakujące lub zakłóceń. Zwłaszcza w przypadku, gdy zawartość pochodzi z użytkowników lub 3 strony, należy wziąć pod uwagę przy użyciu pakiet Microsoft Content Moderator lub dodatkowe narzędzia, aby przejrzeć i sprawdzanie poprawności zawartości.
* *Wykonywania częstych ocen w trybie offline*. Pomoże to monitorowanie trendów i upewnij się, że effectiveness jest znane.
* *Ustanowić proces wykrywania oraz umożliwia korzystanie z manipulacji złośliwego*. Brak aktorów, które będzie korzystać z uczenia maszynowego i sztucznej Inteligencji systemów możliwość ich środowiska do przesunięcia wynik kierunku swoje cele. W przypadku używania Personalizer mogła mieć wpływ na ważne opcji, upewnij się, że masz odpowiednie środki do wykrywania i łagodzenia tych klas ataków, w tym przeglądu przez ludzi w odpowiednich przypadkach.

### <a name="security-and-privacy"></a>Zabezpieczenia i ochrona prywatności
*Systemy sztucznej Inteligencji powinny być bezpieczne i Szanujemy prywatność*. W przypadku używania Personalizer:

* *Informuje użytkowników na początku na temat zbieranych danych i sposobu ich wykorzystywania oraz wcześniej uzyskać zgodę*, zgodnie z przepisami lokalnymi i branżowymi.
* *Zapewniają ochronę prywatności kontrolki użytkownika.* Dla aplikacji, które przechowują informacje osobiste należy wziąć pod uwagę zapewnienie przycisk łatwe do znalezienia dla funkcji, takich jak: 
   * `Show me all you know about me`    
   * `Forget my last interaction` 
   * `Delete all you know about me`

W niektórych przypadkach może to być wymagane przez prawo. Należy wziąć pod uwagę kompromisów w okresowe ponowne trenowanie modeli, dzięki czemu nie zawierają one dane śledzenia usuniętych danych.

### <a name="inclusiveness"></a>Inclusiveness
*Adres szerokiej gamy potrzeb człowieka i środowisk*.
* *Podaj spersonalizowanych środowisk interfejsy ułatwień dostępu.* Wydajność pochodzący z dobrą personalizacji - stosowane w celu zmniejszenia ilości wysiłku, przenoszenia i powtórzenia niepotrzebnego w interakcje mogą być szczególnie przydatne dla osób niepełnosprawnych.
* *Dostosować zachowanie aplikacji w kontekście*. Personalizer służy do rozróżniania między opcjami w czatbot, na przykład podczas interpretacji prawa mogą być kontekstowych i jedno rozwiązanie może nie być dopasowane wszystkie. 


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktywne gotowość do ochrony danych i zarządzanie

Jest trudna do przewidzenia określonej zmiany w kontekstach prawnych, ale na ogół będzie poddanie czegoś więcej niż minimalna ramy prawne w zapewnienie respectful wykorzystanie danych osobowych i zapewnieniu przejrzystości i wybór związane z podejmowania decyzji w konsolidatorze.


* Należy wziąć pod uwagę wcześniejszemu planowaniu do sytuacji, w którym mogą istnieć ograniczenia nowych danych zebranych od osób i konieczne jest pokazanie, jak został on użyty do podejmowania decyzji.
* Należy wziąć pod uwagę dodatkowe gotowości, w której użytkownicy mogą obejmować marginalizowane populacji narażony, elementy podrzędne, ekonomicznych luki w zabezpieczeniach albo w przeciwnym razie podatne na wpływ w konsolidatorze manipulacji.
* Należy wziąć pod uwagę szerokie niezadowolenie z kolekcją danych jak określanie odbiorców i wpływają na dostęp odbiorców algorytmów i programy, odgrywają oraz sposoby unikania sprawdzone błędy strategiczne.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Ocen proaktywnych podczas cyklu życia projektu

Należy rozważyć utworzenie metody dla członków zespołu, użytkowników i właściciele firm raportu obawy dotyczące użycia odpowiada i tworzenia procesu, który uniemożliwia odwet i priorytet ich rozwiązania.

Każda osoba, myśl o efekty uboczne korzystanie z dowolnej technologii jest ograniczona przez ich doświadczeń życia i perspektywy. Rozwiń zakres dostępnych opinii, przenosząc w bardziej zróżnicowane głosów do zespołów, użytkowników lub rad ds; taki sposób, że jest to możliwe i zachęca się ich wypowiedzieć się. Należy wziąć pod uwagę, szkolenia i nauki materiałów, aby rozwinąć wiedzy zespołu w tej domenie i można dodać możliwości do omówienia złożonych i poufnych tematów.

Należy wziąć pod uwagę traktowanie zadań dotyczących używania odpowiada, podobnie jak inne zadania crosscutting w cyklu życia aplikacji, takich jak zadania związane z użytkownika środowisko zabezpieczeń lub metodyki devops. Te zadania i ich wymagań nie może być ekskluzywnym dodatkiem. Użyj odpowiada powinien być omówione i zweryfikowane przez cały cykl życia aplikacji.
 
## <a name="questions-and-feedback"></a>Pytania i opinie

Microsoft stale jest umieszczenie nakład pracy w narzędzia i dokumenty, aby pomóc Ci zajmującym się obowiązków. Nasz zespół umożliwiające [opinii do firmy Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) Jeśli uważasz, że dodatkowe narzędzia i funkcje produktu i dokumenty może pomóc w zaimplementowaniu te wskazówki dotyczące używania Personalizer.

## <a name="recommended-reading"></a>Zalecane materiały do przeczytania

* Zobacz zasady sześć firmy Microsoft odpowiedzialnego rozwoju sztucznej Inteligencji, opublikowane w książce stycznia 2018 r. [obliczane w przyszłości](https://news.microsoft.com/futurecomputed/)
* [Kto jest właścicielem przyszłości? ](https://www.goodreads.com/book/show/15802693-who-owns-the-future) przez Jaron Lanier.
* [Broni rażenia matematyczne](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) przez - Cathy O'Neil
* [Etyki i analiza danych](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) DJ Patil, Hilary Mason, Mike Loukides.
* [Kod ACM etyki](https://www.acm.org/code-of-ethics)
* [Information genetycznych Nondiscrimination Act - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Zasady FATML Accountable algorytmów](http://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Kolejne kroki

[Funkcje: akcji i kontekstu](concepts-features.md).
