---
title: Azure przewodnik AI rozwiązań konserwacji predykcyjnej | Dokumentacja firmy Microsoft
description: Kompleksowy opis analizy danych, obsługującego konserwacji predykcyjnej rozwiązań w wielu branżach pionowy.
services: machine-learning
author: fboylu
manager: cgronlun
editor: ''
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: fboylu
ms.openlocfilehash: 50338e05241be9ce573ff3dd3bb99711cbf15b28
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248541"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure przewodnik AI konserwacji predykcyjnej rozwiązań

## <a name="summary"></a>Podsumowanie

Konserwacji predykcyjnej (**PdM**) jest aplikacją popularnych analizy predykcyjnej, które mogą pomóc w firmach w branży kilka wykorzystania zasobów wysokiej i oszczędności kosztów operacyjnych. W tym przewodniku zgromadzono biznesowych i analitycznego wskazówki i najlepsze rozwiązania do pomyślnie opracowywania i wdrażania rozwiązania PdM przy użyciu [platformy Microsoft Azure AI](https://azure.microsoft.com/overview/ai-platform) technologii.

Po pierwsze w tym przewodniku wprowadza branżowe scenariusze i procesu dla PdM kwalifikujących się tych scenariuszy. Podawane są również wymagania dotyczące danych i techniki modelowania do tworzenia rozwiązań PdM. Główne zawartość przewodnika jest w procesie nauki danych — w tym kroki przygotowania danych, inżynieria funkcji tworzenia modelu i operationalization modelu. Uzupełnienie tych podstawowych pojęć, ten przewodnik zawiera zestaw szablonów rozwiązania ułatwiające przyspieszenie PdM opracowywania aplikacji. Przewodnik wskazuje także zasobów szkoleniowych przydatne dla praktyczny punkt dowiedzieć się więcej o AI za analizy danych. 

### <a name="data-science-guide-overview-and-target-audience"></a>Dane nauki przewodnik omówienie i docelowych odbiorców
Pierwszej połowy w tym przewodniku opisano problemy typowych godzin pracy, zalety stosowania PdM w celu rozwiązania tych problemów i przedstawiono niektóre typowe przypadki użycia. Podejmującym decyzje biznesowe (BDMs) będą korzystać z tej zawartości. Druga połowa opisano nauki danych za PdM oraz przedstawiono listę rozwiązań PdM utworzonych za pomocą zasad opisane w tym przewodniku. Umożliwia także ścieżki szkoleniowe dotyczące i wskaźniki do materiałów szkoleniowych. Techniczne podejmujące (TDMs) będą przydatne tej zawartości.

| Zaczynać... | Jeśli masz... |
|:---------------|:---------------|
| [Zagadnienia do konserwacji predykcyjnej](#Business-case-for-predictive-maintenance) |biznesowe podejmującą (BDM) chcą ograniczyć przestoje i kosztów operacyjnych i poprawić wykorzystanie sprzętu |
| [Nauki danych do konserwacji predykcyjnej](#Data-Science-for-predictive-maintenance) |techniczne podejmującą (TDM) oceny technologii PdM unikatowy przetwarzania danych i wymagania AI do konserwacji predykcyjnej |
| [Szablony rozwiązanie do konserwacji predykcyjnej](#Solution-templates-for-predictive-maintenance)|architekt oprogramowania lub AI deweloperów chcących szybko stać pokaz lub Weryfikacja koncepcji |
| [Zasoby szkoleniowe konserwacji predykcyjnej](#Training-resources-for-predictive-maintenance) | dowolne z powyższych i chcesz dowiedzieć się podstawowych pojęć dotyczących analizy danych, narzędzi i technik.

### <a name="prerequisite-knowledge"></a>Wymagana wiedza
Zawartość BDM nie oczekuje czytelnika znają nauki wszelkich wcześniejszych danych. TDM zawartości warto podstawową wiedzę na temat statystyki i analizy danych. Zaleca się wiedzę na temat danych Azure i usługi AI, Python, R, XML i JSON. Techniki AI są zaimplementowane w pakiety języka Python i R. Szablony rozwiązań są implementowane za pomocą usług Azure, narzędzia deweloperskie i zestawy SDK.

## <a name="business-case-for-predictive-maintenance"></a>Zagadnienia do konserwacji predykcyjnej

Firmy wymagają krytyczne urządzeń należy uruchomić na wydajność i użycie do zrealizowania ich zwrot z inwestycji kapitału. Te zasoby mogą należeć do zakresu od aparaty powietrznego, turbin, wind lub przemysłowych chłodniach — które koszt miliony - dół codzienne urządzeń, takich jak kopiarkach, kawy lub Chłodnice wodne.
- Domyślnie większość firm współpracuje z _naprawcze konserwacji_, której elementy są zastępowane jako, a w przypadku ich awarii. Zapewnia naprawcze konserwacji części służą całkowicie (życia składnika z tego powodu nie traci), ale koszty biznesowe przestojów, pracy i wymagania niezaplanowaną konserwację (poza godzin lub niewygodne lokalizacji).
- W następnej poziomu, rozwiązań firmy _przeprowadzenia profilaktycznej konserwacji_, którym ustalić przydatne informacje o czasie w części i obsługa lub zastąpienie go przed wystąpieniem awarii. Przeprowadzenia profilaktycznej konserwacji pozwala uniknąć niezaplanowane i poważnej awarii. Przy zachowaniu wysokich kosztów zaplanowany przestój, niepełnego wykorzystania przez składnik przed jego pełny okres istnienia użycia i nadal instalacyjne.
- Celem _konserwacji predykcyjnej_ ma na celu optymalizację równowagi między korygujące oraz program prewencyjnej konserwacji, włączając _tylko w czasie_ zastąpienie elementów. Takie podejście zastępuje tylko te składniki, gdy zbliża się awarii. Rozszerzając lifespans składnika (w porównaniu do przeprowadzenia profilaktycznej konserwacji) i zmniejszenie niezaplanowaną konserwację i kosztów pracy (za pośrednictwem naprawcze konserwacji), firmy mogą uzyskać kosztach i konkurencyjnych zalety.

## <a name="business-problems-in-pdm"></a>Problemy biznesowe w PdM
Duże ryzyko operacyjne z powodu nieoczekiwanych awarii stają przed firm i ograniczoną wgląd w głównej przyczyny problemów w złożonych systemów. Pytania biznesowe klucza, należą:

- Wykrywanie anomalii w funkcjonalność i wydajność sprzętu lub systemu.
- Prognozowania, czy zasób może zakończyć się niepowodzeniem w najbliższej przyszłości.
- Szacowanie pozostałych eksploatacji środka trwałego.
- Zidentyfikuj głównych przyczyn niepowodzenia zasób.
- Określ, co prac konserwacyjnych muszą być przeprowadzone przez, kiedy na zasób.

Instrukcje typowe celem z PdM są:

- Ograniczyć ryzyko operacyjne związane z misji krytyczne urządzeń.
- Zwiększyć częstotliwość zwrotu z zasobów przez zanim one wystąpią błędy.
- Kontrolowanie kosztów obsługi przez włączenie operacje konserwacji w czasie.
- Dolny ścierania klienta, poprawić obrazu marki i utracone sprzedaży.
- Niższe koszty spisu, zmniejszając poziomy spisu przez punkt zmiany kolejności.
- Wykryj wzorce podłączony do różnych problemów konserwacji.
- Podaj kluczowych wskaźników wydajności (kluczowe wskaźniki wydajności) takie jak oceny kondycji warunki aktywów.
- Szacowanie żywotność pozostałe zasoby.
- Zaleca się działania odpowiednim konserwacji.
- Włącz tylko w czasie spisu przez określenie kolejności terminy wymianę części.

Te instrukcje celem są punkty początkowe dla:

- _analityków danych_ do analizowania i rozwiązywanie określonych problemów predykcyjnej.
- _w chmurze dla architektów i deweloperów_ aby opracować kompleksowe rozwiązania.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kwalifikowanie problemy konserwacji predykcyjnej
Jest ważne podkreślić, że nie wszystkie przypadki użycia i problemów biznesowych, które może być skutecznie rozwiązany przez PdM. Istnieją trzy ważne kwalifikujących kryteria, które należy wziąć pod uwagę podczas wyboru problemu:

- Problem ma się charakteru; oznacza to powinny być obiektu docelowego lub wynik do prognozowania. Ten problem powinien również zawierać wyczyść ścieżka działań, aby zapobiec błędom podczas ich wykrycia.
- Problem powinno mieć też rekord historii operacyjne sprzętu, który zawiera _zarówno dobrego i nieprawidłowych wyników_. Zbiór działania podjęte w celu ograniczenia wyników zły również powinny być dostępne w ramach tych rekordów. Napraw raporty o błędach, dzienniki konserwacji spadek wydajności, a następnie zastąp dzienniki są równie ważne. Ponadto naprawy podejmowanych w celu zwiększenia ich i zastąpienie rekordy są także przydatne.
- Historia zarejestrowane powinno zostać odzwierciedlone w _odpowiednich_ dane o _wystarczające_ za mało jakości do obsługi przypadek użycia. Aby uzyskać więcej informacji na temat przydatność danych i musi się w temacie [danych wymagania dotyczące konserwacji predykcyjnej](#Data-requirements-for-predictive-maintenance).
- Na koniec firmy powinny mieć ekspertów domeny, którzy mają przejrzysty problemu. Należy zwrócić uwagę wewnętrznych procesów i praktyk, aby można było pomocy analityka zrozumieć i interpretować dane. Powinny być również możliwość wprowadź potrzebne zmiany w istniejących procesów biznesowych, aby ułatwić zbieranie odpowiednich danych dla problemów, jeśli to konieczne.

## <a name="sample-pdm-use-cases"></a>Przykładowe przypadki użycia PdM
W tej sekcji koncentruje się na kolekcję przypadki użycia PdM z branży kilka takich jak Aerospace, narzędzia i transportu. Każda sekcja rozpoczyna się od problem biznesowy i omawia zalety PdM, odpowiednich danych otaczającego problem biznesowy, a na koniec korzyści rozwiązania PdM.

| Problem biznesowy | Korzyści z PdM |
|:-----------------|-------------------|
|**Lotniczego**      |                   |
|_Lotu opóźnienia i anulowania_ z powodu problemów z mechaniczne. Błędy, których nie można naprawić w czasie mogą spowodować lotach do anulowania i zakłócić jego operacji i planowania. |Rozwiązania PdM można przewidzieć prawdopodobieństwo samolotu jest opóźnione lub anulowane z powodu błędów mechaniczne.|
|_Niepowodzenie części aparatu powietrznego_: zamiany części aparatu powietrznego należą najbardziej typowych zadań konserwacji w branży linii lotniczych. Obsługa rozwiązania wymagają staranne zarządzanie dostępność podstawowych składników, dostarczania i planowania|Możliwość zebrania analizy na niezawodność składnika prowadzi do znacznego obniżenia kosztów inwestycji.|
|**Finance** |                         |
|_Uszkodzenie_ jest to powszechny problem w branży bankowości. Problem, w tym miejscu jest prawdopodobieństwo, że ATM gotówkowych wycofanie transakcji zostanie przerwane z powodu błędu zakleszczenie lub części papieru rozdzielacz gotówkowych raportu. W oparciu o prognoz niepowodzeń transakcji, bankomatami może być obsługiwany aktywnego aby zapobiec wystąpieniu błędów.| A nie zezwala maszynie niepowodzenie połowie za pomocą transakcji, pożądane alternatywą jest program maszyna do odmowy usługi oparta na Prognozowanie.|
|**Energii** |                          |
|_Wiatru błędów turbiny_: turbin knie są źródłem energii głównego w krajach przyjazne i może dotyczyć wysokie koszty inwestycyjne. Kluczowym czynnikiem turbin knie jest silnika generatora. braku renderuje turbiny żadnego efektu. Jest również wysokiej kosztowne rozwiązać problem.|Przewidywanie kluczowych wskaźników wydajności, takich jak MTTF (Średni czas do awarii) może pomóc firmy z branży elektroenergetycznej zapobiec błędom turbiny i upewnij się, minimalnym czasem przestojów. Błąd prawdopodobieństwa informuje techników do monitorowania turbin, które mogą nie działać prawidłowo wkrótce i zaplanować systemów opartych na czas konserwacji. Modeli predykcyjnych zapewniają wgląd w różnych czynników, które przyczyniają się do awarii, co ułatwia techników lepiej poznać głównych przyczyn problemów.|
|_Błędy wyłącznika_: dystrybucja energii elektrycznej do domach i firm wymaga zasilania wierszy do użycia przez cały czas, aby zagwarantować dostarczania energii. Obwód podziałów pomóc ograniczyć lub uniknąć uszkodzenia zasilania warunki pogodowe wierszy podczas przeładowanie lub szkodliwe. Problem biznesowy w tym miejscu jest do prognozowania wyłącznika błędów.| PdM rozwiązania pomagają zmniejszyć naprawy koszty i zwiększyć czas działania urządzenia, takie jak podziałów obwodu. Pomagają poprawić jakość sieci zasilania dzięki zmniejszeniu nieoczekiwanych awarii i przerw w działaniu usługi.|
|**Transport i logistyki** |    |
|_Błędy drzwi krótka_: krótka dużych firm świadczenia usługi pełnego stosu dla miliony funkcjonalności windy na całym świecie. Krótka bezpieczeństwa, niezawodności i dostępności są główne zagadnienia klientom. Te firmy śledzić tych i różnych innych atrybutów za pośrednictwem czujników, aby pomóc naprawczych i przeprowadzenia profilaktycznej konserwacji. W krótka najbardziej widocznym problem klienta działa poprawnie drzwi krótka. Problem biznesowy w tym przypadku jest zapewnienie aplikacji predykcyjnej bazy wiedzy knowledge base, który prognozuje powoduje potencjalnych awarii drzwi.| Windy są inwestycji kapitału dla potencjalnie żywotność 20-30 roku. Dzięki każdej potencjalnej sprzedaży mogą wysoce konkurencyjnych; Dlatego oczekiwania dotyczące obsługi i pomocy technicznej są wysokie. Konserwacji predykcyjnej można zapewnić tym firmom więcej możliwości niż ich konkurencji w swoich produktach i ofert usług.|
|_Kółka błędów_: kółka konta błędów połowy wszystkich uczenia derailments i kosztów miliardów branży kolei globalnego. Błędy kółka powodować szyny do pogorszeniu, czasami powoduje kolei przedwcześnie przerwanie. Podziały kolei prowadzić do krytycznego zdarzenia, takie jak derailments. Aby uniknąć tych wystąpień, kolei monitorowania wydajności koła i zastąpić je w sposób przeprowadzenia profilaktycznej. Problem biznesowy w tym miejscu jest prognozowanie kółka błędów.| Konserwacji predykcyjnej koła zawierają informacje pomocne w czasie wymiany koła |
|_Błędy drzwi train całość_: błędy drzwi samochodów pociągu jest główną przyczyną opóźnienia w operacjach całość. Problem biznesowy w tym miejscu jest do prognozowania train drzwi błędów.|Wczesne pogłębianie wiedzy na temat niepowodzenia drzwi lub liczba dni do uszkodzenia drzwi, pomogą Optymalizacja biznesowa uczenia drzwi obsługi harmonogramów.|

Pobiera następną sekcję do szczegółów jak do osiągnięcia korzyści PdM opisanych wyżej.

## <a name="data-science-for-predictive-maintenance"></a>Nauki danych do konserwacji predykcyjnej

Ta sekcja zawiera ogólne wskazówki danych nauki zasady i praktyki dotyczące PdM. Jej celem jest TDM, architektów rozwiązania lub deweloperem zrozumieć wymagania wstępne i proces tworzenia aplikacji AI end-to-end dla PdM. Możesz przeczytać w tej sekcji oraz przegląd pokazy i weryfikacja koncepcji szablonów wymienionych w [szablonów rozwiązanie do konserwacji predykcyjnej](#Solution-templates-for-predictive-maintenance). Tych zasad i najlepsze rozwiązania można następnie użyć do wdrożenia rozwiązania PdM na platformie Azure.

> [!NOTE]
> Ten przewodnik nie jest przeznaczony do nauki czytnika danych nauki. Aby uzyskać więcej informacji w sekcji znajdują się kilka źródeł przydatne [zasobów szkoleniowych do konserwacji predykcyjnej](#Training-resources-for-predictive-maintenance). [Szablony rozwiązań](#Solution-templates-for-predictive-maintenance) w przewodniku przedstawiono niektóre techniki AI określonych problemów PdM.

## <a name="data-requirements-for-predictive-maintenance"></a>Wymagania dotyczące danych do konserwacji predykcyjnej

Powodzenie żadnych learning zależy od tego, czego dotyczy przykład (jakości i (b) zdolność uczeń. Modeli predykcyjnych Dowiedz się wzorce w danych historycznych i przewidywania przyszłych rezultatów z określonego na podstawie tych wzorców obserwowanych prawdopodobieństwa. Model predykcyjny dokładność zależy od tego, dokładność, jakie i jakości danych szkoleniowych i testów. Nowe dane, które są "oceniane" przy użyciu tego modelu powinien mieć te same funkcje i schematu, jak dane szkoleniowe i testowanie. Funkcja właściwości nowych danych (typ, gęstość dystrybucji i tak dalej) powinna być zgodna z zestawów danych szkoleniowych i testów. W tej sekcji koncentruje się na wymagania dotyczące tych danych.

### <a name="relevant-data"></a>Odpowiednie dane

Po pierwsze, danych musi być _dotyczy problem_. Należy wziąć pod uwagę _kółka awarii_ omówione przypadek użycia powyżej - danych szkoleniowych powinna zawierać funkcje związane z operacjami kółka. Jeśli problem się do prognozowania awarii _systemu trakcji_, ma obejmować wszystkich składników systemu trakcji danych szkoleniowych. Pierwszym przypadku dotyczy określonego składnika, natomiast w drugim przypadku celem awarii większych podsystemu. Ogólne zalecanie służy do projektowania systemów prognozowania o określonych składników, a nie większych podsystemów, ponieważ to drugie będą mieć więcej rozproszone danych. Ekspert domeny (zobacz [kwalifikowanie problemy konserwacji predykcyjnej](#Qualifying-problems-for-predictive-maintenance)) powinny pomóc w wyborze najbardziej odpowiednich podzbiorów danych do analizy. Źródła danych odpowiednie omówiono bardziej szczegółowo w [Przygotowanie danych do konserwacji predykcyjnej](#Data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Wystarczającą ilość danych
Często zadawane pytania dwóch względem danych historii błąd: (1) "ilu zdarzenia błędów są wymagane do uczenia modelu?" [2] "liczbę rekordów jest uznawany za"wystarczająco"?" Nie istnieją żadne ostateczne odpowiedzi, ale tylko reguły przyjąć. (1), aby uzyskać więcej liczba zdarzeń błędów lepsze modelu. (2) i dokładna liczba zdarzeń błędów zależy od danych oraz kontekst problem zostanie rozwiązany. Jednak po stronie Przerzucanie Jeśli maszynę zbyt często nie powiedzie się następnie firmy spowoduje zastąpienie, któremu zostanie obniżony wystąpienia awarii. W tym miejscu ponownie, wskazówek z domeny ekspertów jest ważne. Istnieją jednak metody radzenia sobie z problemem z _rzadkich zdarzenia_. Omówiono w sekcji [obsługi danych imbalanced](#Handling-imbalanced-data).

### <a name="quality-data"></a>Jakości danych
Jakość danych jest krytyczne — każda wartość atrybutu predykcyjne musi być _dokładne_ w połączeniu z wartością Zmienna docelowa. Jakości danych jest dobrze przebadanych obszar w przystawce Zarządzanie statystyk i danych i dlatego out zakres tego podręcznika.

> [!NOTE]
> Istnieje kilka zasobów i produkty dla przedsiębiorstw do dostarczania jakości danych. Poniżej znajduje się przykład odwołania:
> - Dasu, T, Johnson, wyszukiwania danych T., badawczych i czyszczenie danych, Wiley, 2003.
> - [Analiza danych poznawcze, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Danymi ilościowymi Hellerstein, J, czyszczenia dla dużych baz danych](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [Niemcy Jonge, E, van der odszukaj, M, wprowadzenie do czyszczenia danych z języka R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Przygotowanie danych do konserwacji predykcyjnej

### <a name="data-sources"></a>Źródła danych

Źródła danych odpowiednie dla konserwacji predykcyjnej obejmują, ale nie są ograniczone do:
- Błąd historii
- Historia konserwacji lub naprawy
- Warunki operacyjne maszyny
- Metadane urządzenia

#### <a name="failure-history"></a>Błąd historii
Zdarzenia błędów są rzadko w PdM aplikacji. Jednak podczas budowania modeli prognozowania, algorytm musi Dowiedz się więcej o normalnym wzorzec operacyjnej składnika, a także jej wzorce awarii. Dlatego dane szkoleniowe powinien zawierać wystarczającą liczbę przykłady z obu kategorii. Historia zastępczy rekordów i części obsługi są dobre źródła do znalezienia zdarzeń błędu. Za pomocą pewna znajomość domeny anomalii w danych szkoleniowych może także być zdefiniowany jako błędy.

#### <a name="maintenancerepair-history"></a>Historia konserwacji lub naprawy
Historia obsługi środka trwałego zawiera szczegółowe informacje dotyczące składników zastąpiony, napraw wykonywanych itp. Te zdarzenia rekord degradacji. Brak ważnych informacji w danych szkoleniowych może prowadzić do błędne wyniki modelu. Historia błędu można znaleźć w historii konserwacji jako kody błędów specjalnych lub dat zamówienia części. Dodatkowych źródeł danych mające wpływ awarii wzorce powinny zbadana i udostępniane przez ekspertów domeny.

#### <a name="machine-operating-conditions"></a>Warunki operacyjne maszyny
Dane przesyłane strumieniowo czujnik oparte na (lub innych) urządzeń w operacji jest źródłem ważnych danych. Kluczowych założeń w PdM jest, że stan kondycji na komputerze powoduje spadek wraz z upływem czasu podczas jego rutynowych operacji. Dane powinien zawierać funkcje różnym czasie przechwytywania tego wzorca przedawnienia i wszelkich anomaliach, które powoduje spadek. Zawierająca dane czasowe aspektu danych jest wymagany dla algorytmu dowiedzieć się błędu i wzorce z systemem innym niż błąd w czasie. W oparciu o te punkty danych, algorytm uczy się do prognozowania liczbę jednostek więcej czasu maszynę można kontynuować pracę przed jej nie powiedzie się.

#### <a name="static-feature-data"></a>Dane funkcji statycznych
Statyczne funkcje są metadane dotyczące urządzeń. Przykłady są Marka sprzętu, modelu, Data wyprodukowanych, uruchomienie Data service, lokalizacji systemu i inne dane techniczne.

Przykłady odpowiednich danych dla [przypadki użycia próbki PdM](#Sample-PdM-use-cases) są przedstawione w poniższej tabeli:

| Przypadek użycia | Przykłady odpowiednich danych |
|:---------|---------------------------|
|_Opóźnienie transmitowane i anulowania_ | Informacje o lotach trasy w formie etapy transmitowane i dzienniki strony. Transmitowane etap dane zawierają szczegóły routingu, takie jak wyjścia/przyjęcia dzień, godzina, lotnisku layovers itp. Strona dziennika zawiera szereg kody błędów i konserwacji, które są rejestrowane przez personel konserwacji podstaw.|
|_Błąd części aparatu powietrznego_ | Dane zbierane z czujników w powietrznego, które zawierają informacje na temat stanu różnych części. Rejestruje konserwacji identyfikowania podczas wystąpienia awarii składników oraz gdy są one zostały zastąpione.|
|_Błąd ATM_ | Odczyty czujników dla każdej transakcji (złożenie gotówkowych/wyboru) i wydawanie gotówkowych. Informacje o pomiaru odstęp między uwagi, należy zwrócić uwagę grubość, należy zwrócić uwagę odległość przyjęcia, zaznacz opcję atrybutów itp. Rekordy konserwacji, które zapewniają kody błędów, informacji naprawy, czas ostatniego rozdzielacz gotówkowych został uzupełniania.|
|_Błąd turbiny knie_ | Czujniki monitorować turbiny warunków, takich jak temperatury, kierunku knie, zasilania wygenerowane, generator prędkości itp. Dane są zbierane z wielu turbin knie z knie farmy serwerów znajdujących się w różnych regionach. Zazwyczaj każdego turbiny ma wiele odczyty czujników przekazywanie pomiarów w stałych interwałach czasu.|
|_Błędy wyłącznika_ | Dzienniki konserwacji, które obejmują akcje naprawcze, zapobiegawczych oraz systematyczne. Dane operacyjne zawiera automatycznej i ręcznej polecenia wysyłane do obwodu podziałów, takich jak dla akcji otwierających i zamykających. Urządzenie metadane, takie jak data produkcji, lokalizacji, modelu, itp. Specyfikacje wyłącznika takich jak poziomy napięcia, geolokalizacja, warunków otoczenia.|
|_Krótka drzwi błędów_| Krótka metadane, takie jak typ krótka, Data wyprodukowanych, częstotliwość konserwacji, typu budynku i tak dalej. Informacje operacyjne, takie jak liczby cykli drzwi drzwi Średni czas zamknięcia. Błąd historii kodu za pomocą przyczyny.|
|_Koło błędów_ | Dane czujników, że środki kółka przyspieszenie, hamowania wystąpień, pobudzenie odległość, prędkości itp. Informacje o statycznych na koła jak producenta, wyprodukowanych daty. Dane awarii wywnioskować na podstawie bazy danych zamówień części, śledzenia dat zamówienia i ilości.|
|_Całość train drzwi błędów_ | Drzwi otwarcia i zamknięcia godziny, innych danych operacyjnych, takich jak bieżący stan drzwi pociągu. Dane statyczne to identyfikator zasobu, czas i kolumny wartości warunku.|

### <a name="data-types"></a>Typy danych
Dwa typy danych głównych w domenie PdM podane powyżej źródeł danych, to:

- _Danych czasowych_: operacyjne telemetrii, warunki maszyny, typów kolejność pracy, kody priorytet, które będą miały sygnatury czasowe w czasie rejestrowania. Błąd, konserwacji lub naprawy i historię użycia będą także mieć skojarzone z każdym zdarzeniu sygnatur czasowych.
- _Dane statyczne_: funkcje maszyny i operatora ogólnie są statyczne ponieważ opisują techniczne maszyny lub operator atrybutów. Jeśli te funkcje można zmieniać w czasie, również powinny mieć sygnatur czasowych skojarzonych z nimi.

Prognoza i obiekt docelowy zmienne powinny zawierać przetwarzanych wstępnie/przekształcone w [dane liczbowe, podzielone na kategorie i innych typów](https://www.statsdirect.com/help/basics/measurement_scales.htm) w zależności od algorytm używany.

### <a name="data-preprocessing"></a>Przetwarzanie wstępne danych
Jako warunek wstępny _Inżynieria_, przygotować dane z różnych strumieni utworzenie schematu, z którego jest łatwy w celu tworzenia funkcji. Najpierw wizualizacji danych jako tabelę rekordów. Każdy wiersz w tabeli reprezentuje wystąpienie szkolenia i kolumny reprezentują _predykcyjne_ funkcji (nazywanych również atrybuty niezależne lub zmiennych). Organizowanie danych w taki sposób, że jest ostatniej kolumny _docelowej_ (zależnych od zmiennej). Dla każdego wystąpienia szkolenia przypisać _etykiety_ jako wartości tej kolumny.

W przypadku danych czasowych podział danych czujnika w czasie trwania jednostki czasu. Każdy rekord powinna należeć do jednostki czasu dla zasobu, _i powinno oferować różne informacje_. Jednostki czasu są definiowane w zależności od potrzeb biznesowych wielokrotności sekundy, minuty, godziny, dni, miesięcy, i tak dalej. Jednostka czasu _nie musi być taka sama jak częstotliwość zbierania danych_. Częstotliwość jest wysoka, dane mogą nie pokazywać znaczące różnice z jednej jednostki do drugiego. Na przykład załóżmy, że temperatura otoczenia został zebrany co 10 sekund. Tylko za pomocą takiego samego interwału dla danych szkoleniowych nadyma wiele przykładów bez konieczności podawania dodatkowych informacji. Dla tego przypadku lepszą strategię jest używany średnia danych ponad 10 minut lub godzinę oparte na biznesowego wyjaśnienia.

Dla danych statycznych
- _Rejestruje konserwacji_: dane pierwotne konserwacji ma identyfikator zasobu i sygnatura czasowa informacje dotyczące konserwacji działań, które mogły zostać wykonane w danym punkcie w czasie. Przekształć działania obsługi do _podzielone na kategorie_ kolumn, w którym jednoznacznie mapuje akcja konserwacji określonego deskryptora każdej kategorii. Identyfikator zasobu, czas i akcja konserwacji obejmuje schematu dla utrzymania rekordów.

- _Rejestruje błąd_: błędy lub przyczyny niepowodzenia mogą być rejestrowane jako określonych kodów błędów i zdarzeń błędów zdefiniowanych przez warunków firmy. W przypadkach, gdy urządzenie ma wiele kody błędów ekspertów domeny powinna pomagać w identyfikacji te, które są odpowiednie do zmiennej docelowej. Użyj pozostałe kody błędów lub warunków w celu utworzenia _predykcyjne_ funkcje, które są zgodne z tych błędów. Schemat dla rekordów awarii obejmuje identyfikator zasobu, czas, Niepowodzenie lub Przyczyna niepowodzenia — Jeśli jest dostępna.

- _Metadane maszyny i operatora_: scalić dane maszyny i operatora jeden schemat, aby skojarzyć zasób operatorem wraz z ich odpowiednich atrybutów. Identyfikator zasobu, funkcje zasobu, identyfikator operator i operator obejmuje schematu dla warunków maszyny.

Inne dane przetwarzania wstępnego kroki obejmują _obsługi brakujące wartości_ i _normalizacji_ wartości atrybutów. Szczegółowe omówienie wykracza poza zakres tego podręcznika — w następnej sekcji niektóre przydatne dane.

Z powyższych wstępnie przetworzony źródeł danych w miejscu, transformacja końcowego przed engineering funkcji sprzęgać tabel wymienionych powyżej, na podstawie identyfikatora zasobów i sygnatura czasowa. Tabeli wynikowej musi wartości null dla kolumny awarii, gdy komputer jest w normalnych operacji. Te wartości null można kalkulacyjne za pomocą wskaźnika do normalnego działania. Ta kolumna niepowodzenia służy do tworzenia _etykiety model predykcyjny_. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [modelowania techniki konserwacji predykcyjnej](#Modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Inżynieria funkcji
Funkcja engineering jest pierwszym krokiem przed modelowania danych. Swoją rolę w procesie nauki danych [opisany tutaj](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). A _funkcji_ jest atrybutem predykcyjnego modelu — takie jak temperatury, wykorzystania wibrację i tak dalej. Dla PdM Inżynieria funkcji obejmuje abstrakcyjność kondycji na komputerze za pośrednictwem historyczne dane zebrane przez dany okres może być zmieniany. W tym sensie różni się od jego elementów równorzędnych, takie jak zdalne monitorowanie, wykrywanie anomalii i wykrywania awarii. 

### <a name="time-windows"></a>Czas systemu windows
Zdalne monitorowanie powoduje zgłoszenie zdarzeń, które zachodzą w _punkty w czasie_. Modele wykrywania anomalii ocenić (wynik) przychodzących strumieni danych do anomalii flagi wersji punkty w czasie. Wykrywanie błędów klasyfikuje błędów, aby mieć określone typy występujące punkty w czasie. Z kolei PdM obejmuje Prognozowanie błędów w _przyszłego okresu_, oparte na funkcji, które reprezentują zachowanie maszyny za pośrednictwem _historycznych okresie_. W przypadku PdM dane funkcji z poszczególnych punktów czasu są zbyt dużo się. Dlatego dane dla każdej funkcji musi być _smoothened_ przez agregowanie punktów danych za pośrednictwem okna czasowe.

### <a name="lag-features"></a>Funkcja Lag funkcji
Wymagania określają, jak daleko modelu ma do prognozowania w przyszłości. Z kolei ten czas trwania pomaga określają "jak daleko wstecz modelu ma szukać" do tworzenia tych prognoz. To "wyszukiwania wstecz' okres jest nazywany _opóźnienie_, i funkcje odtwarzane w tym okresie opóźnienia są nazywane _lag funkcji_. W tej sekcji omówiono funkcje opóźnienia, które mogą zostać utworzone na podstawie źródeł danych za pomocą sygnatur czasowych oraz tworzenie funkcji ze źródeł danych statycznych. Opóźnienie funkcje są zwykle _numeryczny_ charakter.

> [!IMPORTANT]
> Rozmiar okna jest określany za pośrednictwem eksperymenty, a powinien sfinalizowany za pomocą domeny ekspertów. Tym samym zastrzeżenie: odnosi się do wyboru i definicji funkcji lag, ich agregacji i typ systemu windows.

#### <a name="rolling-aggregates"></a>Wprowadzanie wartości zagregowanych
Dla każdego rekordu zasobu stopniowego okno rozmiaru "W" zostanie wybrany jako liczba jednostek czasu można obliczyć agregacji. Opóźnienie funkcje są obliczane przy użyciu kropki W _przed datą_ tego rekordu. Na rysunku 1 niebieski wiersze zawierają wartości czujnik zarejestrowane dla zasobu dla każdej jednostki czasu. Nad okno, rozmiaru W = 3 one oznaczenia średnią kroczącą funkcji wartości. Średnią kroczącą to obliczona dla wszystkich rekordów z sygnatury czasowe w zakresie t<sub>1</sub> (w kolorze pomarańczowym) t<sub>2</sub> (na zielono). Wartość W jest zwykle w minutach lub godzin w zależności od charakteru danych. Ale dla niektórych problemów, pobrania W dużych (np. 12 miesięcy) może zapewnić całej historii zasobów do czasu rekordu.

![Rysunek 1. Funkcje agregujące stopniowych](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) rysunek 1. Wycofanie funkcje agregujące

Przykłady wycofania agreguje za pośrednictwem przedział czasu to liczba, średnia, środki CUMESUM (suma zbiorcza), wartości minimalnej i maksymalnej. Ponadto wariancję, odchylenie standardowe i liczba wartości odstających poza odchyleń standardowych N są często używane. Przykłady agregacji, które mogą być stosowane dla [przypadki użycia](#Sample-PdM-use-cases) w tym przewodniku są wymienione poniżej. 
- _Opóźnienie lotu_: liczba kodów błędów przez ostatni dzień/tydzień.
- _Część niepowodzenie aparatu powietrznego_: stopniowych oznacza odchylenie standardowe i sumy przez ostatnich dnia, tygodnia itp. Ta metryka należy określić wraz z domeny business ekspertów.
- _Błędy ATM_: stopniowych oznacza, mediana, zakres, odchyleń standardowych, liczby wartości odstających poza trzy odchyleń standardowych, górny i dolny CUMESUM.
- _Błędy drzwi train całość_: liczba zdarzeń w ciągu dnia, tygodnia, dwa tygodnie itp.
- _Błędy wyłącznika_: liczba awarii w zeszłym tygodniu, rok, trzech lat itd.

Innej techniki przydatne w PdM jest przechwytywania trend zmiany, nagłego i poziomu przy użyciu algorytmów wykrywania anomalii w danych.

#### <a name="tumbling-aggregates"></a>Agreguje wirowania
Dla każdego z etykietą rekordu zasobu, okno rozmiaru _W -<sub>k</sub>_  jest zdefiniowany, gdzie _k_ jest liczba windows rozmiar _W_. Agregacje są tworzone przez _k_ _windows wirowania_ _P-k, W -<sub>(k-1)</sub>,..., W -<sub>2</sub>, W -<sub>1</sub>_  okresów przed sygnaturę czasową rekordu. _k_ może być niewielka liczba przechwytywania efekty krótkoterminowej lub dużą liczbą przechwytywania degradacji długoterminowej. (patrz rysunek 2).

![Rysunek 2. Funkcje agregujące wirowania](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) na rysunku 2. Funkcje agregujące wirowania

Na przykład lag funkcje dla przypadek użycia turbin knie może zostać utworzony W = 1 i k = 3. Opóźnienie dla każdego z ostatnich trzech miesięcy za pomocą górnej i dolnej wartości odstających one oznaczać.

### <a name="static-features"></a>Funkcje statyczne

Specyfikacji technicznych sprzętu, takie jak data produkcji, numer modelu, lokalizacji, znajdują się przykłady statycznych funkcji. Są one traktowane jako _podzielone na kategorie_ zmienne modelowania. Przykłady dotyczące wyłącznika przypadek użycia są napięcia, bieżący moc, typ transformatora i zasilania. Niepowodzeń kółka typ opona koła (stopu stali vs) jest przykładem.

Działań przygotowania danych do tej pory omówione powinny prowadzić do danych są zorganizowane w sposób przedstawiony poniżej. Szkolenie, testów i weryfikacji danych powinny mieć ten schemat logiczny (ten przykład przedstawia czas w jednostkach dni).

| Identyfikator elementu zawartości | Time | <Feature Columns> | Etykieta |
| ---- | ---- | --- | --- |
| A123 |1 dzień | . . . | . |
| A123 |Dzień 2 | . . . | . |
| Przyciski ...  |Przyciski ...   | . . . | . |
| B234 |1 dzień | . . . | . |
| B234 |Dzień 2 | . . . | . |
| Przyciski ...  |Przyciski ...   | . . . | . |

Ostatnim krokiem w funkcji inżynierii jest **etykietowania** zmiennej docelowej. Ten proces jest zależna od techniki modelowania. Z kolei techniki modelowania zależy od problem biznesowy i rodzaj dostępnych danych. Etykietowanie została szczegółowo opisana w następnej sekcji.

> [!IMPORTANT]
> Przygotowanie danych i funkcji zespołu inżynieryjnego są równie ważne jako modelowania techniki znalezienie rozwiązania PdM powiodło się. Ekspert domeny i praktyczny punkt powinien inwestują długiego czasu w celu uzyskania właściwych funkcji i danych dla modelu. Poniżej wymieniono małej przykładowej z wielu książek na inżynierii funkcji:
> - Pyle, przygotowanie D. danych do wyszukiwania (Morgan Kaufmann serii danych systemów zarządzania), 1999 danych
> - Casari Zheng, A., inżynieria A. funkcji dla usługi Machine Learning: zasady i techniki dla analityków danych, O'Reilly, 2018.
> - Dong G. Liu H. (Edytory), funkcja inżynierii uczenie maszynowe i analizy danych (Chapman & Hall/CRC wyszukiwania danych i serii odnajdywania wiedzy), naciśnij CRC 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Techniki modelowania konserwacji predykcyjnej

W tej sekcji omówiono techniki modelowania głównych problemów PdM, wraz z ich metod konstrukcji określoną etykietą. Zwróć uwagę, że techniki modelowania pojedynczego mogą być używane w różnych branż. Techniki modelowania parowania problem analizy danych, a nie danych w kontekście.

> [!IMPORTANT]
> Wybór etykiety w przypadkach niepowodzenia i strategii etykietowania  
> należy określić w porozumieniu z domeny ekspertów.

### <a name="binary-classification"></a>klasyfikacji binarnej
Klasyfikacji binarnej służy do _prognozowania prawdopodobieństwo, że urządzenie nie powiedzie się w przedziale czasu przyszłych_ — wywołane _okres przyszłych typu horizon X_. X jest określana przez problem biznesowy i dane, po konsultacji z domeny ekspertów. Przykłady to:
- _Minimalny czas realizacji_ wymagane, aby zastąpić składników, wdrażać konserwacji zasobów, wykonywanie konserwacji, aby uniknąć problemu, który może nastąpić w danym okresie.
- _Minimalna liczba zdarzeń_ zdarzyć przed wystąpieniem problemu.

W tej techniki są identyfikowane dwa rodzaje przykładów szkoleniowych. Przykład dodatnią, _co wskazuje błąd_, z etykietą = 1. Przykład ujemną, wskazuje normalną pracę, z etykietą = 0. Zmienna docelowa, i dlatego wartości etykiety są _podzielone na kategorie_. Model musi wskazywać każdego nowego przykład jako może zakończyć się niepowodzeniem lub działały normalnie w ciągu następnych X jednostki czasu.

#### <a name="label-construction-for-binary-classification"></a>Konstrukcja etykiety klasyfikacji binarnej
To pytanie, w tym miejscu: "jakie jest prawdopodobieństwo, że zasób zakończy się niepowodzeniem w ciągu następnych X jednostki czasu?" Odpowiedzi na to pytanie, rekordów etykiety X przed awarią zasobu jako "o do odrzucaj" (etykiety = 1) i oznaczanie wszystkie rekordy jako "normal" (etykiety = 0). (patrz rysunek 3).

![Rysunek 3. Etykiety klasyfikacji binarnej](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) rysunek 3. Etykiety klasyfikacji binarnej

Poniżej przedstawiono przykłady etykietowania strategii dla niektórych przypadków użycia.
- _Lotu opóźnienia_: X może zostać wybrany jako 1 dzień do prognozowania opóźnienia w ciągu 24 godzin. Następnie wszystkie lotach znajdujących się w ciągu 24 godzin, zanim błędy są oznaczone jako 1.
- _Gotówkowych ATM zwolnienie błędów_: celem może być ustalenie prawdopodobieństwo błędu transakcji w ciągu jednej godziny. W takim przypadku wszystkie transakcje, które wystąpiły w ciągu ostatniej godziny błędu są oznaczone jako 1. Przewidywanie prawdopodobieństwo błędu za pośrednictwem dalej waluty N uwagi zbędne, wszystkie informacje o pominięte w uwagach N ostatniej awarii są oznaczone jako 1.
- _Błędy wyłącznika_: celem może być do prognozowania dalej błędu polecenia wyłącznika. W takim przypadku X zostanie wybrany jako jedno polecenie w przyszłości.
- _Szkolenie błędów drzwi_: X może zostać wybrany jako dwa dni.
- _Wiatru błędów turbiny_: X może zostać wybrany jako dwa miesiące.

### <a name="regression-for-predictive-maintenance"></a>Regresja do konserwacji predykcyjnej
Modele regresji są używane do _obliczeniowe pozostałych użytkowania (RUL) zasób_. RUL jest zdefiniowany jako ilość czasu zasób działa przed wystąpieniem awarii dalej. Każdy przykład szkolenia jest rekordem, który należy do jednostki czasu _nY_ dla zasobu, gdzie _n_ jest wielokrotność. Model należy obliczyć RUL każdego nowego przykładu jako _numer kolejny_. Liczba ta określa czas pozostały przed awarią.

#### <a name="label-construction-for-regression"></a>Tworzenie etykiety regresji
To pytanie, w tym miejscu: "Nowości pozostałych użytkowania (RUL) urządzenia" Dla każdego rekordu przed awarią obliczyć jest etykieta liczbę jednostek czasu pozostałego przed awarią dalej. W przypadku tej metody etykiety są ciągłe zmiennych. (Patrz rysunek 4)

![Rysunek 4. Etykietowania dla regresji](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) rysunek 4. Etykietowania dla regresji

Dla regresji etykietowania odbywa się w odniesieniu do punktu awarii. Obliczenia nie jest możliwe bez wiedzy o ile zasobu ma udało przetrwać przed wystąpieniem awarii. Dlatego w przeciwieństwie do klasyfikacji binarnej trwałe bez żadnych błędów w danych nie może służyć do modelowania. Ten problem dotyczy najlepiej innej techniki statystyczne o nazwie [analizy przetrwania](https://en.wikipedia.org/wiki/Survival_analysis). Jednak potencjalne komplikacji mogą wystąpić podczas stosowania tej techniki przypadki użycia PdM obejmujących różne czasu danych za pomocą częstych odstępach czasu. Aby uzyskać więcej informacji na analizie przetrwania, zobacz [tym jeden pagera](https://www.cscu.cornell.edu/news/statnews/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasa usługi klasyfikacji konserwacji predykcyjnej
Techniki wielu klas klasyfikacji można w rozwiązaniach PdM dwóch scenariuszy:
- Przewidywanie _dwóch przyszłych rezultatów_: pierwszy wynikiem jest _zakres czasu do awarii_ dla zasobu. Element zawartości jest przypisany do jednego z wielu możliwych okresach czasu. Drugi wynik jest prawdopodobieństwo wystąpienia błędu w przyszłości ze względu na _jeden z głównych wielu powoduje_. Ta prognozowania umożliwia zespół ds. konserwacji, które należy obserwować objawy i harmonogramy konserwacji planu.
- Przewidywanie _najbardziej prawdopodobne przyczyny_ danego awarii. Ten wynik zaleca prawidłowego zestawu działań konserwacyjnych, aby naprawić błąd. Listy uporządkowanej według rangi główne przyczyny i zalecane napraw może pomóc techników priorytety swoje działania naprawy po awarii.

#### <a name="label-construction-for-multi-class-classification"></a>Konstrukcja etykiety klasyfikacji wielu — klasa
Pytanie, w tym miejscu jest: "jakie jest prawdopodobieństwo, że zasób zakończy się niepowodzeniem w ciągu następnych _nZ_ jednostki czasu, gdy _n_ jest liczbę okresów?" Aby odpowiedzieć na to pytanie, etykiety rekordów nZ przed awarią zasobów przy użyciu przedziały czasu (3Z 2Z, Z). Etykieta wszystkie inne rejestruje "normal" (etykiety = 0). W przypadku tej metody zawiera zmienną docelową _podzielone na kategorie_ wartości. (Patrz rysunek 5).

![Rysunek 5. Etykietowanie wieloklasowej klasyfikacji dla niepowodzenia czasowego](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) rysunek 5. Etykiety klasyfikacji wielu klasy dla niepowodzenia czasowego

To pytanie, w tym miejscu: "jakie jest prawdopodobieństwo, że zasób zakończy się niepowodzeniem w ciągu następnych X jednostki czasu z powodu problem Przyczyna główny _P<sub>i</sub>_?" gdzie _i_ jest liczba możliwych przyczyn. Odpowiedzi na to pytanie, rekordów etykiety X przed awarią zasobu jako "o się niepowodzeniem z powodu przyczynę _P<sub>i</sub>_" (etykiety = _P<sub>i</sub>_). Etykieta wszystkie rekordy jako "normal" (etykiety = 0). W tej metodzie etykiety są również podzielone na kategorie (patrz rysunek 6).

![Rysunek 6. Etykietowanie wieloklasowej klasyfikacji dla głównej przyczyny prognozowania](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) rysunek 6. Etykiety klasyfikacji wielu klasy dla głównej przyczyny prognozowania

Model przypisuje prawdopodobieństwo Niepowodzenie z powodu każdego _P<sub>i</sub>_  oraz prawdopodobieństwo bez błędów. Te prawdopodobieństwa może zostać określona przez wielkości umożliwia prognozowanie problemów, które są najbardziej prawdopodobne w przyszłości.

To pytanie, w tym miejscu: "jakie akcje konserwacji polecisz po awarii?" Aby odpowiedzieć na to pytanie, etykietowania _nie wymaga przyszłych typu horizon do pobrania_, ponieważ model nie jest prognozowanie błędu w przyszłości. Go jest tylko przewidywanie najbardziej prawdopodobne przyczyny _po awarii została już wykonana_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Szkolenie, weryfikacji i testowania metody konserwacji predykcyjnej
[Proces nauki danych zespołu](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) zapewnia pełne pokrycie cyklu sprawdzanie poprawności testów train model. W tej sekcji omówiono kwestii charakterystycznych dla PdM.

### <a name="cross-validation"></a>Krzyżowe sprawdzanie poprawności
Celem [krzyżowe sprawdzanie poprawności](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) jest określenie zestawu danych na "test" modelu w fazie szkolenia. Ten zestaw danych jest nazywany _sprawdzania poprawności zestawu_. Ta technika pomaga limit problemów takich jak _overfitting_ i zapewnia wgląd w jak model zostanie generalize do niezależnego zestawu danych. Oznacza to nieznane dane zestaw, które może być z rzeczywistego problemu. Procedury szkolenie i testowania dla PdM należy wziąć pod uwagę czas różnych aspektów, które lepiej generalize w niewidocznym przyszłych danych.

Wiele algorytmów uczenia maszynowego zależy od wielu hyperparameters, który znacznie zmienić działania modelu. Optymalne wartości hyperparameters te nie są automatycznie obliczane, podczas uczenia modelu. Należy je określić przez naukowca danych. Istnieje kilka metod odnajdywania dobre wartości hyperparameters.

Najbardziej typowe jest _k składanie krzyżowego sprawdzania poprawności_ dzieli się przykłady losowo do _k_ złożeń. Dla każdego zestawu wartości hyperparameters Uruchom Algorytm uczenia _k_ razy. W każdej iteracji użycia przykłady w bieżącej złożenia jako zestaw sprawdzania poprawności i pozostałych przykładach zestawu szkoleniowego. Szkolenia w zakresie algorytmu za pośrednictwem przykłady szkolenia i obliczeniowego metryki wydajności za pośrednictwem przykłady sprawdzania poprawności. Na koniec pętlę obliczyć średnią _k_ metryki wydajności. Dla każdego zestawu wartości hyperparameter wybierz te, które średni najlepszą wydajność. Wybranie hyperparameters jest często eksperymentalne charakter.

Problemy z PdM dane są rejestrowane jako szeregu czasowego zdarzenia, które pochodzą z wielu źródeł danych. Te rekordy mogą uporządkowanych według czasu etykiet. W związku z tym jeśli zestaw danych jest podzielona _losowo_ w zestawie szkolenia i sprawdzania poprawności, _niektóre przykłady szkolenia mogą być później w czasie niż niektóre przykłady sprawdzania poprawności_. Wydajność hyperparameter wartości w przyszłości będzie można oszacować na podstawie niektórych danych, które dotarły _przed_ został uczenia modelu. Te uzyskać szacunkowe wartości może być zbyt optymistycznej, zwłaszcza, jeśli szeregów czasowych nie jest stacjonarnych i rozwoju wraz z upływem czasu. W związku z tym może być nieoptymalne hyperparameter wybranych wartości.

Zalecaną metodą jest podzielenie przykłady na zestaw szkoleniowy i sprawdzania poprawności w _zależnych od czasu_ sposób, w których później w czasie niż wszystkie przykłady szkolenia wszystkie przykłady sprawdzania poprawności. Dla każdego zestawu wartości hyperparameter szkolenia w zakresie algorytmu za pośrednictwem szkoleniowy zestaw danych. Mierzenie wydajności modelu przez ten sam zestaw sprawdzania poprawności. Wybierz wartości hyperparameter, które zawierają najlepszą wydajność. Wartości Hyperparameter wybierany przez wyniku podziału train/weryfikacji wydajności lepiej przyszłych modelu niż przy użyciu wartości z losowo wybranym przez krzyżowego sprawdzania poprawności.

Ostatecznego modelu mogą być generowane przez szkolenia Algorytm uczenia przez cały szkolenia danych przy użyciu najlepszych wartości hyperparameter.

### <a name="testing-for-model-performance"></a>Testowanie pod kątem wydajności modelu
Po utworzeniu modelu szacunkową jego przyszłych wydajności na nowych danych jest wymagana. Jest dobrą oszacować metryki wydajności wartości hyperparameter obliczona dla zestawu weryfikacji lub metryki wydajności średni obliczana na podstawie krzyżowego sprawdzania poprawności. Te uzyskać szacunkowe wartości są często zbyt optymistycznej. Działalność często może być dodatkowe wskazówki na jak chciałby do testowania modelu.

Zalecany sposób PdM jest podzielona w przykładach szkolenia, sprawdzanie poprawności, i ustawia dane testowe w _zależnych od czasu_ sposób. Wszystkie przykłady testu powinien przypadać później w czasie niż wszystkie przykłady szkolenia i sprawdzania poprawności. Po podziału Generowanie modelu i mierzenie jego wydajności, zgodnie z wcześniejszym opisem.

W przypadku stałych i łatwe do prognozowania szeregu czasowego losowe i zależnych od czasu metod Generowanie podobne ocen działalności w przyszłości. Ale w przypadku stacjonarnych z systemem innym niż i/lub trudna do przewidzenia szeregów czasowych podejście zależnych od czasu generuje bardziej realistyczne oszacowania wydajność w przyszłości.

### <a name="time-dependent-split"></a>Podziel zależnych od czasu
W tej sekcji opisano najlepsze rozwiązania w celu wykonania podziału zależnych od czasu. Poniżej opisano zależnych od czasu dwukierunkowej podziału między zestawy szkoleniowe i testowe.

Przykładowa strumienia zdarzeń oznaczony znacznikiem czasowym, takich jak pomiarów z różnych czujników. Zdefiniuj funkcje i etykiety szkolenia i przykłady testu za pośrednictwem przedziały czasu, które zawierają wiele zdarzeń. Na przykład dla klasyfikacji binarnej tworzenie na podstawie ostatnich zdarzeń funkcji i utworzyć etykiety na podstawie przyszłych zdarzeń w "jednostkach czasu w przyszłości X" (znajduje się w sekcjach na [Inżynieria](#Feature-engineering) i [modelowania techniki](#Modeling-techniques-applied-to-PdM-use-cases)). W związku z tym etykietowania przedział czasu przykład pochodzi później niż w czasie jego funkcje.

Podział zależnych od czasu, można wybrać _szkolenia odcięcia czasu T<sub>c</sub>_  jaką do nauczenia modelu, z hyperparameters dopasowane przy użyciu danych historycznych maksymalnie T<sub>c</sub>. Aby uniknąć wycieków przyszłych etykiety, które wykraczają poza T<sub>c</sub> do danych szkoleniowych wybierz czas najnowsze przykłady szkolenia etykiety jako X jednostki przed T<sub>c</sub>. W tym przykładzie pokazano na rysunku 7 każdy kwadrat reprezentuje rekord w zestawie danych, w której funkcje i etykiety są obliczane zgodnie z powyższym opisem. Na ilustracji przedstawiono rekordy, które powinny przejść do celów szkoleniowych i testów zestawów dla X = 2, a W = 3:

![Rysunek 7. Czas zależne podzielić klasyfikacji binarnej](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) rysunek 7. Podziel zależnych od czasu dla klasyfikacji binarnej

Zielony kwadratów reprezentują rekordów należących do jednostki czasu, w których można użyć do trenowania. Każdy przykład szkolenia jest generowany przy uwzględnieniu przeszłości trzy kropki generacji funkcji, a dwie kropki przyszłych na etykietach przed T<sub>c</sub>. Gdy jest dowolną część dwie kropki przyszłych poza T<sub>c</sub>, Wyklucz przykładu z szkoleniowy zestaw danych, ponieważ nie widoczność przyjęto, że poza T<sub>c</sub>.

Czarnych pól reprezentują rekordy ostatecznego etykietą zestawu danych nie powinna być używana w zestawie danych szkoleniowych podane powyżej ograniczenia. Te rekordy również nie będą używane podczas testowania danych, ponieważ są one przed T<sub>c</sub>. Ponadto ich etykietowania przedziały czasu częściowo zależą od szkolenia przedziału czasu, który nie jest idealnym rozwiązaniem. Szkolenie i badanie danych powinny mieć osobne etykietowania przedziały czasu, aby uniknąć wycieków informacji etykiety.

Techniki omówione w do tej pory umożliwia pokrywanie celów szkoleniowych i testów przykłady, które mają sygnatur czasowych w pobliżu T<sub>c</sub>. Osiągnąć większą separacji rozwiązaniem jest wyłączenie przykłady, które znajdują się w jednostkach czasu W t<sub>c</sub> z testu. Ale agresywne podziału zależy od dostępności wystarczającą ilość danych.

Modele regresji używane do prognozowania RUL więcej poważny wpływ na problem wycieku. Za pomocą metody split losowe prowadzi do extreme dopasowywania nadmierne. W przypadku problemów regresji podziału powinien być taki sposób, że rekordy należące do zasoby z błędami przed T<sub>c</sub> przejdź do zestawu szkoleniowego. Rekordy zasobów, które mają błędy po odcięcia przejdź do zestawu testów.

Inny najlepszym rozwiązaniem do dzielenia danych do celów szkoleniowych i testów jest użycie podziału, według identyfikatora zasobu. Podział powinna być w taki sposób, że żaden z zasobów używanych w zestawie szkolenia są używane do testowania wydajności modelu. W ten sposób model ma zwiększa prawdopodobieństwo zapewnia bardziej realistyczne wyników nowych zasobów.

### <a name="handling-imbalanced-data"></a>Obsługa danych imbalanced
Problemy z klasyfikacji, jeśli istnieje więcej przykładów dotyczących jednej klasy niż inne, zestaw danych jest określany jako _imbalanced_. W idealnym przypadku wystarczającej ilości przedstawicielom każda klasa w dane szkoleniowe są preferowane umożliwia rozróżnianie między różnych klas. Jeśli jedna klasa jest mniejsza niż 10% danych, dane są uważane za imbalanced. Klasa underrepresented jest nazywana _klasy mniejszości_.

Wiele problemów PdM stają przed takie imbalanced zestawów danych, gdy jedna klasa jest znacznie underrepresented w porównaniu do innych klas, lub klasy. W niektórych sytuacjach klasy mniejszości może stanowić tylko 0,001% punktów Łączna ilość danych. Nierównowaga klasy nie jest unikatowa dla PdM. Inne domeny, w których rzadko wystąpienia błędów i anomalie, stają przed podobny problem, przykłady, wykrywanie oszustw i sieci nieautoryzowanego dostępu. Te błędy tworzą mniejszości przykłady klas.

Z klasy nierównowaga danych wydajności większość standardowych algorytmów uczenia zostanie naruszony, ponieważ są one na celu zminimalizować szybkość ogólny błąd. Dla zestawu danych z 99% ujemna i przykłady dodatnią 1% model można można wyświetlić do 99% dokładność etykietowania wszystkie wystąpienia jako ujemne. Ale modelu zostaną nieprawidłowo klasyfikować wszystkie przykłady dodatnią; Dlatego nawet jeśli jego dokładność jest wysoka, algorytm nie jest użyteczne. W rezultacie metryki oceny z konwencjonalnej, takich jak _dokładności na wskaźnik błędów_ są niewystarczające do uczenia imbalanced. W przypadku zastosowania imbalanced zestawów danych, innych metryk służą do oceny modelu:
- dokładność
- Odwołaj
- Wyniki F1
- Koszt dostosowana ROC (odbiornika charakterystyki działania)

Aby uzyskać więcej informacji o tych metryk, zobacz [modelu oceny](#Model-evaluation).

Istnieją jednak niektórych metod, które pomagają rozwiązać klasy nierównowaga problem. Te dwie główne są _próbkowania techniki_ i _koszt poufnych learning_.

#### <a name="sampling-methods"></a>Metody pobierania próbek
Imbalanced learning polega na użyciu pobierania próbek, aby zmodyfikować szkoleniowy zestaw danych do zestawu o zrównoważonym danych. Metody pobierania próbek nie mają być stosowane do zestawu testów. Istnieje kilka technik, większość z nich bezpośrednio do przodu są _losowe oversampling_ i _w obszarze próbkowania_.

_Losowe oversampling_ obejmuje losowej próbki z klasy mniejszości, wybierając replikowanie te przykłady i dodaniem ich do zestawu danych szkoleniowych. W związku z tym liczba przykłady w klasie mniejszości zwiększa się, a ostatecznie równowagę między liczbą przykłady różnych klas. Wadą oversampling jest, że wiele wystąpień niektóre przykłady może spowodować klasyfikatora stanie zbyt określonych, co prowadziło do dopasowywania uwierzytelniając się. Modelu mogą być wyświetlane szkolenia wysokiej dokładności, ale jej wydajności na danych testowych niewidocznym może być nieoptymalne.

Z drugiej strony _losowe w obszarze pobierania próbek_ jest wybranie losowej próbki z klasy większość i usuwanie tych przykładów szkoleniowy zestaw danych. Jednak usunięcie przykłady z klasy większość może spowodować klasyfikatora ważnych pojęć odnoszących się do większości klasy przeoczenie. _Hybrydowe próbkowania_ gdzie klasa mniejszości jest nadmiernie próbki, a większość klasy w obszarze próbkowany w tym samym czasie jest innego podejścia działało.

Istnieje wiele zaawansowanych technik. Techniki wybrany zależy od właściwości danych i wyniki iteracji eksperymenty przez naukowca danych.

#### <a name="cost-sensitive-learning"></a>Koszt learning poufne
W PdM błędów, które tworzą klasę mniejszości są większe znaczenie niż normalne przykłady. Tak skupiono się głównie na wydajność algorytmu na błędy. Niepoprawnie przewidywania dodatnią klasy jako klasa ujemna można koszty były wyższe niż odwrotnie. Taka sytuacja jest często określana jako nierówne utracie lub asymetrycznego koszt źle klasyfikującego elementów do różnych klas. Nadaje się doskonale klasyfikatora powinien dostarczać dokładności prognozy wysokiej za pośrednictwem klasy mniejszości, bez obniżania dokładności dla klasy większość.

Istnieje wiele sposobów osiągnięcia tej równowagi. Aby uniknąć problemu nierówne utraty, przypisać wysokiego kosztu błędnej klasyfikacji klasy mniejszości, a dążyć do minimalizacji całkowity koszt. Algorytmy, takich jak _SVMs (Obsługa wektor maszyny)_ przyjąć tę metodę z założenia, umożliwiając wartości dodatnie i ujemne przykłady można podać podczas uczenia. Podobnie, takie jak zwiększanie wyniku metody _boosted drzew decyzyjnych_ zwykle Pokaż dobrą wydajność imbalanced danych.

## <a name="model-evaluation"></a>Ocena modelu
Błędna klasyfikacja jest poważny problem dla PdM zastosowaniach koszt fałszywe alarmy w firmie wysokie. Dla wystąpienia podjęciem decyzji o tła samolotu oparte na nieprawidłowe prognozowania awarii aparatu może zakłócać harmonogramy i plany podróży. Pobieranie maszyny w tryb offline w wierszu zestawu może prowadzić do utraty przychodu. Dlatego bardzo ważne jest ocena modelu z metryk wydajności prawo względem nowych danych testowych.

Metryki wydajności typowe używane do analizowania modele PdM omówiono poniżej:

- [Dokładność](https://en.wikipedia.org/wiki/Accuracy_and_precision) jest najbardziej popularnym Metryka opisujących wydajności klasyfikatora programu. Jednak dokładność jest wielkość liter podziału danych i jest nieefektywne miary w scenariuszach z imbalanced zestawami danych. Zamiast tego używane są inne metryki. Narzędzi, takich jak [macierzy pomyłek](https://en.wikipedia.org/wiki/Confusion_matrix) są używane na potrzeby obliczania i przyczyny o dokładności modelu.
- [Dokładność](https://en.wikipedia.org/wiki/Precision_and_recall) z PdM modeli odnoszą się do szybkości fałszywe alarmy. Niższe dokładności modelu zazwyczaj odpowiada wyższy stopień fałszywe alarmy.
- [Odwołaj](https://en.wikipedia.org/wiki/Precision_and_recall) szybkość oznacza liczbę niepowodzeń w zestawie testów zostały poprawnie zidentyfikowane przez model. Większe odwołania oznaczają, że modelu zakończy się pomyślnie do identyfikowania błędów wartość true.
- [Wynik F1](https://en.wikipedia.org/wiki/F1_score) jest średnią harmoniczne precision i odwołania z jego wartość z zakresu od 0 (najgorszy) do 1 (najlepiej).

Binarny klasyfikacji
- [Odbiornik operacyjnego krzywych (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) jest również popularnych metryki. W krzywych ROC jest interpretowany oparte na jednym stałe punktu działania na ROC wydajności modelu.
- Nawet w przypadku problemów PdM _tabel decile_ i _Podnieś wykresy_ są więcej informacji. Skupienie się na klasie dodatnią (błędy) i zapewnić bardziej złożonych obraz algorithm wydajności niż krzywych ROC.
  - _Tabele decile_ są tworzone przy użyciu testu przykłady malejącej prawdopodobieństwa awarii. Uporządkowanej przykłady są następnie grupowane w deciles (10% próbki o najwyższym prawdopodobieństwo, a następnie 20%, 30% i tak dalej). /(Random baseline) stosunek (częstotliwość dodatnią wartość true) dla każdego decile pomaga oszacowania wydajności algorytm w każdym decile. Losowe linii bazowej przejmuje wartości 0.1, 0.2 i tak dalej.
  - [Podnieś wykresy](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) wykreślenia decile true szybkość dodatnią i losowe szybkość dodatnią wartość true, dla wszystkich deciles. Pierwszy deciles zwykle koncentrują się na wyniki, ponieważ pokazywały największych korzyści. Pierwszy deciles znajdują się również jako reprezentatywny dla "zagrożony", gdy jest używany dla PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Operationalization modelu do konserwacji predykcyjnej

Korzyści wykonywania analizy danych jest realizowane tylko trenowanego modelu nawiązaniem operacyjną. Oznacza to należy wdrożyć model do systemów firm w celu tworzenia prognoz na podstawie danych nowego, wcześniej niewidoczne.  Nowe dane musi dokładnie odpowiadać _podpisu modelu_ trenowanego modelu na dwa sposoby:
- wszystkie funkcje muszą być obecne w każdym wystąpieniu logiczne (np. wiersz w tabeli) nowych danych.
- nowe dane muszą zostać przetworzone wcześniej, a każda z tych funkcji zaprojektowane w taki sam sposób, jak dane szkoleniowe.

Proces powyżej podano na wiele sposobów w uczelni oraz z branży materiały. Jednak poniższe instrukcje oznaczają to samo:
- _Oceniać nowe dane_ przy użyciu modelu
- _Zastosowanie tego modelu_ do nowych danych
- _Operacjonalizuj_ modelu
- _Wdrażanie_ modelu
- _Uruchom modelu_ odniesieniu do nowych danych

Jak wspomniano wcześniej, operationalization modelu dla PdM różni się od jego elementów równorzędnych. Implementowanie scenariuszy obejmujących wykrywania anomalii i wykrywanie awarii zazwyczaj _online oceniania_ (nazywane również _oceniania w czasie rzeczywistym_). Tutaj, model _wyniki_ każdego rekordu przychodzących i zwraca prognozowania. Do wykrywania anomalii Prognozowanie jest wskazanie, że wystąpił anomalii (przykład: jedna klasa SVM). Do wykrywania awarii jest typu lub klasy awarii.

Z kolei obejmuje PdM _wsadowego oceniania_. Odpowiada podpisu modelu funkcji w nowych danych muszą zostać uwzględnione w taki sam sposób jak danych szkoleniowych. Duże zestawy danych, które są typowe dla nowych danych funkcje są agregowane przez czas systemu windows i oceniane w partii. Ocenianie partii jest zazwyczaj wykonywane w systemach rozproszonych, takich jak [Spark](http://spark.apache.org/) lub [partii zadań Azure](https://docs.microsoft.com/azure/batch/batch-api-basics). Istnieje kilka rozwiązań alternatywnych — zarówno nieoptymalne:
- Przesyłania strumieniowego aparaty danych obsługuje agregacji za pośrednictwem systemu windows w pamięci. Dlatego można utrzymywał, czy obsługują one oceniania online. Jednak te systemy są odpowiednie dla danych zawierające gęsto wąskie systemu Windows na czas lub rozrzedzonych elementów za pośrednictwem szersze systemu windows. One może nie skalowania dla danych zawierające gęsto za pośrednictwem szersze czas systemu windows, jak pokazano w scenariuszach PdM.
- Jeśli oceniania partii nie jest dostępna, rozwiązanie jest dostosowanie oceniania online do obsługi nowych danych w małych partiach naraz.

## <a name="solution-templates-for-predictive-maintenance"></a>Szablony rozwiązanie do konserwacji predykcyjnej

Ostatnia część ten przewodnik zawiera listę szablonów rozwiązania PdM, samouczki i eksperymenty zaimplementowane na platformie Azure. Te aplikacje PdM można wdrożyć do subskrypcji platformy Azure w ciągu minut w niektórych przypadkach. One może służyć jako Weryfikacja koncepcji pokazów, piaskownic eksperymentować alternatyw czy akceleratorów implementacji rzeczywistej produkcji. Te szablony znajdują się w [galerii Azure AI](http://gallery.azure.ai) lub [Azure GitHub](https://github.com/Azure). Te przykłady różnych zostanie wycofana w czasie do ten szablon rozwiązania.

| # | Stanowisko | Opis |
|--:|:------|-------------|
| 1 | [Azure konserwacji predykcyjnej Machine Learning próbki](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance) |Przykład PdM do prognozowania awarii za pośrednictwem dalej jednostki czasu N. Ten przykład jest zapisywany jako projekt Workbench uczenie Maszynowe Azure i jest idealny dla początkujących użytkowników do PdM. [Dodatkową dokumentację](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance) związane z tego przykładu.|
| 2 | [Szablon rozwiązania Azure konserwacji predykcyjnej](https://github.com/Azure/AI-PredictiveMaintenance) | Kompleksowe framework do zaprezentowania scenariuszy z wieloma PdM. Ten szablon przedstawia dwa scenariusze: pierwsza to nowy przypadek użycia klasyfikacji warunku błędu w czasie rzeczywistym. Drugi scenariusz jest po prostu zintegrowania rozwiązania do [1] ten szablon rozwiązania. Go pokazano, jak użyć ponownie w tej samej infrastrukturze wdrożone, aby dodać inne scenariusze nowego lub istniejącego.|
| 3 | [Bezpośrednie uczenia dla konserwacji predykcyjnej](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure notesu z rozwiązaniem Pokaz używania sieci LSTM (długa krótkoterminowe pamięci) (klasa powtarzającego się sieci Neuronowej) do konserwacji predykcyjnej z [wpis w blogu w tym przykładzie](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Podręcznik modelowania konserwacji predykcyjnej w R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Podręcznik modelowania PdM za pomocą skryptów w języku R.|
| 5 | [Azure konserwacji predykcyjnej dla Aerospace](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Jeden z pierwszego szablonów rozwiązania PdM oparte na uczenie Maszynowe Azure w wersji 1.0 powietrznego konserwacji. Ten przewodnik pochodzi z tego projektu. |
| 6 | [Azure Toolkit AI IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI w programie edge IoT przy użyciu TensorFlow; pakiety Toolkit głębokie modeli w kontenerach Docker zgodnych z programem Azure IoT krawędzi uczenia i ujawniać te modele jako interfejsów API REST.
| 7 | [Konserwacji predykcyjnej Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Pakiet Azure IoT komputerów — wstępnie skonfigurowane rozwiązanie. Szablon PdM konserwacji powietrznego z pakiet IoT. [Innym dokumencie](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) i [wskazówki](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) związane z tym samym projekcie. |
| 8 | [Predykcyjnej szablonu konserwacji za pomocą usług SQL Server R](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Wersja demonstracyjna dla pozostałych scenariusza użytkowania na podstawie usług R. |
| 9 | [Podręcznik modelowania konserwacji predykcyjnej](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funkcja zestaw danych obsługi powietrznego odtwarzane przy użyciu R z [eksperymenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) i [zestawów danych](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) i [Azure notesu](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) i [eksperymenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)w uczenie maszynowe Azure w wersji 1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Zasoby szkoleniowe konserwacji predykcyjnej

[Ścieżka szkoleniowa Azure AI do konserwacji predykcyjnej](https://github.com/Azure/AI-PredictiveMaintenance/blob/master/docs/azure-ai-learning-path-for-predictive-maintenance.md) zapewnia materiałów szkoleniowych dla głębsze zrozumienie pojęcia i matematyczne za algorytmy i techniki stosowane PdM problemy. 

Microsoft Azure oferuje zawartość wolnego i szkolenia na ogólne koncepcje AI i praktyki.

| Szkolenie zasobów  | Dostępność |
|:-------------------|--------------|
| [AI dewelopera na platformie Azure](http://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Public |
| [Służbowe Microsoft AI](http://aischool.microsoft.com/learning-paths) | Public |
| [Learning Azure AI z usługi GitHub](http://azure.github.io/learnanalytics/public) | Public |
| [LinkedIn Learning](http://www.linkedin.com/learning) | Public |
| [Microsoft AI Youtube seminaria internetowe](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Public |
| [Pokaż AI firmy Microsoft](http://channel9.msdn.com/Shows/AI-Show) | Public |
| [LearnAI@MS](http://learnanalytics.microsoft.com) | Dla partnerów firmy Microsoft |
| [Sieci Microsoft Partner Network](http://learningportal.microsoft.com) | Dla partnerów firmy Microsoft |

Ponadto bezpłatne MOOCS (duże Otwórz szkoleń w trybie online) na AI są online oferowane przez academic instytucje, takich jak Stanford i MIT i innych firm edukacyjnych.