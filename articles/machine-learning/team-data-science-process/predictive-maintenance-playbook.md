---
title: Przewodnik sztucznej Inteligencji platformy Azure dla rozwiązania do konserwacji zapobiegawczej - zespołu danych dla celów naukowych
description: Kompleksowy opis do nauki o danych, zapewniająca rozwiązania do konserwacji zapobiegawczej w wielu branżach pionowy.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 5cf3f02284777a54a9d26cad8a7f3b5b4fa6b335
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087778"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Przewodnik sztucznej Inteligencji platformy Azure dla rozwiązania do konserwacji zapobiegawczej

## <a name="summary"></a>Podsumowanie

Konserwacja predykcyjna (**PDM**) to popularna aplikacja analizy predykcyjnej, która może pomóc firmom w kilku branżach osiągnąć duże wykorzystanie zasobów i oszczędności w kosztach operacyjnych. Ten przewodnik zawiera szczegółowe wytyczne biznesowe i analityczne oraz najlepsze rozwiązania w celu pomyślnego opracowania i wdrożenia rozwiązań PdM przy użyciu technologii [platformy AI Microsoft Azure](https://azure.microsoft.com/overview/ai-platform) .

Po pierwsze ten przewodnik wprowadzenie scenariusze biznesowe specyficzne dla branży i proces kwalifikujących się te scenariusze dla menedżerów PdM. Wymagania dotyczące danych i technik modelowania, do tworzenia rozwiązań menedżerów PdM również są dostarczone. Główną zawartością przewodnik znajduje się na proces analizy danych — w tym kroki przygotowywania danych, technicznego opracowywania funkcji, tworzenia modelu i operacjonalizacji modeli. Jako uzupełnienie tych kluczowych pojęć, ten przewodnik zawiera zestaw szablonów rozwiązań ułatwia przyspieszenie procesu opracowywania aplikacji menedżerów PdM. Przewodnik wskazuje również zasoby szkoleniowe przydatne do lekarza dowiedzieć się więcej o sztucznej Inteligencji za do nauki o danych. 

### <a name="data-science-guide-overview-and-target-audience"></a>Danych analizy przewodnik Przegląd i docelowej grupy odbiorców
Pierwsza połowa tego przewodnika opisano problemy, typowych godzin pracy, korzyści płynące z zainstalowania program PdM w celu rozwiązania tych problemów i zawiera listę niektórych typowych przypadków użycia. Osobom podejmującym decyzje biznesowe (BDMs) będą mogli korzystać z tej zawartości. Drugą połowę wyjaśnia do nauki o danych za menedżerów PdM i zawiera listę rozwiązań menedżerów PdM utworzonych za pomocą zasad przedstawionych w tym przewodniku. Zapewnia również ścieżki szkoleniowe i wskaźników do materiałów szkoleniowych. Osób podejmujących decyzje techniczne (TDMs) będą przydatne tej zawartości.

| Zaczynać... | Jeśli jesteś... |
|:---------------|:---------------|
| [Przypadek biznesowy dla konserwacji predykcyjnej](#business-case-for-predictive-maintenance) |Osoba podejmująca decyzje biznesowe (BDM) chcących zmniejszenia przestojów i kosztów operacyjnych i poprawić wykorzystanie sprzętu |
| [Analiza danych w celu przeprowadzenia konserwacji predykcyjnej](#data-science-for-predictive-maintenance) |techniczne osobą podejmującą decyzje (TDM) ocenia technologie menedżerów PdM, aby zrozumieć unikatowy przetwarzania danych i sztucznej Inteligencji wymagania dotyczące konserwacji predykcyjnej |
| [Szablony rozwiązań do konserwacji predykcyjnej](#solution-templates-for-predictive-maintenance)|architekt oprogramowania lub deweloper sztucznej Inteligencji, które chcą szybko paru pokaz lub Weryfikacja koncepcji |
| [Szkoleniowe zasoby na potrzeby konserwacji predykcyjnej](#training-resources-for-predictive-maintenance) | niektóre lub wszystkie z powyższych i chcesz dowiedzieć się podstawowe pojęcia dotyczące do nauki o danych, narzędzia i techniki.

### <a name="prerequisite-knowledge"></a>Wymagana wiedza
Zawartość BDM nie oczekuje czytnika mieć żadnej wiedzy do nauki o danych z poprzedniego. Dla zawartości TDM podstawową wiedzę na temat statystyk i analizy danych jest pomocne. Zaleca się wiedzę na temat danych na platformę Azure i usług sztucznej Inteligencji, Python, R, XML i JSON. Techniki sztucznej Inteligencji są implementowane w pakiety języka Python i R. Szablony rozwiązań są implementowane za pomocą usług platformy Azure, narzędzia deweloperskie i zestawy SDK.

## <a name="business-case-for-predictive-maintenance"></a>Przypadek biznesowy pod kątem konserwacji predykcyjnej

Firmy wymagają krytycznych urządzeń, należy uruchomić na wydajność i użycie do zrealizowania ich zwrot z inwestycji kapitałowych. Te zasoby mogą należeć do zakresu od silników samolotów, turbiny, windy lub przemysłowych chłodniach — które koszt milionów — do codziennego urządzeń, takich jak kopiarkach, kawy lub Chłodnice wodne.
- Domyślnie większość firm korzysta z _konserwacji naprawczej_, w której części są zastępowane jako i po ich niepowodzeniu. Naprawcze konserwacji zapewnia części używane są całkowicie (w związku z tym nie marnowania składnika life), ale kosztuje firmy przestojów, pracy i wymagania niezaplanowaną konserwację (funkcja wyłączona godzin lub nie można użyć lokalizacji).
- Na następnym poziomie firmy przeprowadzają _konserwację zapobiegawczą_, gdzie określają przydatną cykl życia częściową i utrzymują lub zastępują przed awarią. Konserwacji prewencyjnej unika się niezaplanowane i poważnej awarii. Jednak wysokie koszty zaplanowanego przestoju, pod względem użycia składnika w trakcie jego okresu istnienia i robocizny nadal pozostają.
- Celem _konserwacji predykcyjnej_ jest zoptymalizowanie równowagi między konserwacją naprawczą i zapobiegawczą przez włączenie _just in Time_ składników. To podejście zastępuje tylko tych składników, znajdujących się blisko awarii. Rozszerzając lifespans składnika (w porównaniu do konserwacji zapobiegawczej) i zmniejszając niezaplanowaną konserwację i koszty pracy (za pośrednictwem naprawcze konserwacji), firmy mogą uzyskać oszczędności kosztów i konkurencyjność korzyści.

## <a name="business-problems-in-pdm"></a>Problemy biznesowe podczas menedżerów PdM
Firmom twarzy wysokiego ryzyka operacyjnego z powodu nieoczekiwanych awarii i ma ograniczoną wgląd w głównej przyczyny problemów w złożonych systemów. Na kluczowe pytania biznesowe, należą:

- Wykrywaj anomalie wydajność sprzętu lub systemu i funkcje.
- Przewidywania, czy element zawartości może zakończyć się niepowodzeniem w niedalekiej przyszłości.
- Oszacuj pozostałego okresu eksploatacji środka trwałego.
- Identyfikuj główne przyczyny awarii zasobu.
- Zidentyfikuj prac konserwacyjnych należy wykonać, gdy dla zasobu.

Typowym celem instrukcje z menedżerów PdM są:

- Zmniejszenie ryzyka operacyjnego krytycznych urządzeń o krytycznym.
- Zwiększ stopa zwrotu z zasobami, przewidywanie awarii, przed ich wystąpieniem.
- Po włączeniu operacji konserwacji just-in-time decydują o koszcie konserwacji.
- Obniż ścieralność klienta, poprawić obraz marką i sprzedaż.
- Niższe koszty magazynu dzięki zmniejszeniu poziomy zapasów dzięki możliwości przewidywania której kolejność chcesz zmienić.
- Znajdź wzorce podłączone do różnych problemów konserwacji.
- Obejmij kluczowych wskaźników wydajności (kluczowych wskaźników wydajności) takich jak oceny kondycji zasobów warunków.
- Szacowany pozostały okres eksploatacji zasobów.
- Zalecane czynności konserwacyjnych w odpowiednim czasie.
- Włącz dokładnie na czas spisu przez określenie kolejności daty wymiany części.

Te instrukcje celem są punktów początkowych dla:

- analityków _danych_ do analizowania i rozwiązywania określonych problemów predykcyjnych.
- Dzięki _architektom i deweloperom chmury_ można kompleksowo wdrożyć rozwiązanie.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kwalifikowanie problemów pod kątem konserwacji predykcyjnej
Należy podkreślić, że nie wszystkie przypadki użycia lub problemy biznesowe, które może być skutecznie rozwiązany przez menedżerów PdM. Istnieją trzy ważne kwalifikującym się kryteria, które należy wziąć pod uwagę podczas wyboru problem:

- Ten problem ma się charakter; oznacza to powinien istnieć obiekt docelowy lub wynik do prognozowania. Problem powinien mieć również przejrzystą ścieżkę działania w celu uniknięcia błędów podczas ich wykrycia.
- Problem powinien zawierać rekord historii operacyjnej sprzętu, który zawiera _zarówno dobre, jak i złe wyniki_. Zbiór działania podjęte w celu złagodzenia nieprawidłowych wyników również powinny być dostępne w ramach tych rekordów. Napraw raportów o błędach, dzienniki konserwacji spadek wydajności, i Zastąp dzienniki są równie ważne. Ponadto naprawy podejmowanych w celu ich zastąpienie rekordy i są także przydatne.
- Zarejestrowana historia powinna być odzwierciedlona w _odpowiednich_ danych, które mają _wystarczającą_ wystarczającą jakość do obsługi przypadku użycia. Aby uzyskać więcej informacji na temat istotności i adekwatności danych, zobacz [wymagania dotyczące danych w przypadku konserwacji predykcyjnej](#data-requirements-for-predictive-maintenance).
- Na koniec firmy powinny mieć eksperci domeny poczuć problemu. Powinny one być świadome wewnętrznych procesów i praktyk pomocy przez analityka, zrozumieć i zinterpretować danych. Należy je również mogą wprowadzać niezbędne zmiany istniejące procesy biznesowe, aby ułatwić zbieranie odpowiednich danych, w razie problemów, jeśli to konieczne.

## <a name="sample-pdm-use-cases"></a>Przykładowe przypadki użycia menedżerów PdM
Ta sekcja koncentruje się na zbiór przypadki użycia program PdM w branżach, takich jak lotnictwa i Kosmonautyki, narzędzia i transportu. Każda sekcja rozpoczyna się od problemu biznesowego, a w tym artykule omówiono korzyści wynikające z menedżerów PdM, odpowiednie dane otaczającego problemu biznesowego, a na koniec korzyści rozwiązania menedżerów PdM.

| Problem biznesowy | Korzyści związane z menedżerów PdM |
|:-----------------|-------------------|
|**Pokład**      |                   |
|_Opóźnienie lotu i anulowanie_ z powodu problemów mechanicznych. Błędy, które nie można naprawić w czasie mogą spowodować lotów zostaną anulowane i zakłócić planowania i operacji. |Rozwiązania menedżerów PdM przewidzieć prawdopodobieństwo samolotu są opóźnione lub zostało anulowane z powodu awarii mechanicznych.|
|_Awaria części silnika samolotu_: przemieszczenie części aparatu samolotu to najbardziej typowe zadania konserwacyjne w branży lotniczej. Konserwacja rozwiązania wymagają staranne zarządzanie dostępność podstawowych składników, dostarczania i planowania|Możliwość zebrania analizy na niezawodność składnika prowadzi do znacznej redukcji kosztów inwestycyjnych.|
|**Odsetek** |                         |
|_Awaria ATM_ to typowy problem w branży bankowej. Problem polega na tym zgłoszenia prawdopodobieństwo, że ATM środków pieniężnych wycofania transakcji zostanie przerwane z powodu błędu zakleszczenie lub części dokument rozdzielacz środków pieniężnych. Oparte na prognozy błędów transakcji, bankomatami może być obsługiwany aktywnie aby zapobiec występowaniu błędów.| A nie zezwolić na maszynę aby zakończyć się niepowodzeniem przy użyciu transakcji w połowie drogi, pożądane alternatywą jest program maszyny do odmowy usługi oparte na prognozowania.|
|**Efektywności** |                          |
|_Awarie programu wiatru turbin_: turbiny wiatrowe są głównym źródłem energii w krajach/regionach odpowiedzialnych za środowisko i obejmują wysokie koszty. Kluczowy składnik w turbinach wiatru jest silnikiem generatora, którego awaria nieskuteczna renderuje turbinę. Jest również bardzo drogie do naprawienia.|Prognozowanie kluczowe wskaźniki wydajności, takich jak MTTF (średniego czasu awarii) może pomóc firmom energetycznym zapobiec błędom turbiny i upewnij się, minimalnym czasem przestojów. Prawdopodobieństwa niepowodzeń informuje pracowników technicznych, aby monitorować turbiny, które mogą wkrótce się nie powieść i planowanie systemów opartych na czas konserwacji. Modele predykcyjne zapewniają wgląd w różne czynniki wpływające na błędy, co pomaga techników lepiej zrozumieć głównych przyczyn problemów.|
|_Awarie_wyłącznika: dystrybucja energii elektrycznej do domów i firm wymaga, aby linie zasilania działały przez cały czas w celu zagwarantowania dostarczania energii. Wyłączniki pomóc ograniczyć lub uniknąć uszkodzenia power warunki pogodowe wierszy podczas przeciążenie lub niepożądane. W tym miejscu problem biznesowy jest przewiduje awarie wyłącznika.| Program PdM rozwiązania pomagają obniżenie kosztów napraw i zwiększenie cyklem życia urządzeń, takich jak wyłączniki. Pomagają poprawić jakość sieci zasilania, zmniejszając nieoczekiwanych awarii i przerw w działaniu usług.|
|**Transport i logistyka** |    |
|_Awarie drzwi Wind_: duże firmy Wind oferują pełną usługę stosu dla milionów Wind funkcjonalnych na całym świecie. Windy bezpieczeństwa, niezawodności i czas pracy to główne kwestie dla swoich klientów. Te firmy śledzić te i różnych innych atrybutów za pośrednictwem czujników, aby pomóc im dzięki naprawcze i prewencyjnej konserwacji. W elevator najczęściej występujący problem klienta działa poprawnie drzwi elevator. Problem biznesowy w tym przypadku jest zapewnienie bazy wiedzy knowledge base predykcyjna aplikacja która przewiduje się, że powoduje, że ryzyko błędów drzwi biblioteki.| Windy są inwestycji kapitałowych dla potencjalnie czas 20 – 30 roku. Dlatego może być wysoce konkurencyjnych; każdej potencjalnej sprzedaży Dlatego do obsługi i pomocy technicznej oczekują. Konserwacji predykcyjnej można zapewnić tym firmom z przewagę nad ich konkurentów w swoich produktach i oferty usług.|
|_Awarie kół_: w przypadku awarii kółka dla połowy wszystkich poręczeń i kosztów w branży kolejek globalnych. Błędy kółka również powodować rails się pogarszać, czasami powodując szyny włamanie się przedwcześnie. Podziały szyny prowadzić do katastrofami, takich jak derailments. Aby uniknąć tych wystąpień, kolei monitorować wydajność koła i zastąpić je w sposób prewencyjnej. Problem biznesowy w tym miejscu jest przewidywanie awarii koła.| Konserwacja predykcyjna kół pomoże just-in-time zastąpienie plików w formacie Wheel |
|_Awarie drzwi Subway pociąg_: główną przyczyną opóźnień w operacjach Subway są awarie drzwi samochodów. Problem biznesowy w tym miejscu jest przewiduje awarie drzwi pociągu.|Wczesne świadomości awarii drzwi lub liczba dni do awarii drzwi może pomóc w optymalizacji biznesowej szkolenie drzwi obsługi harmonogramy.|

Następna sekcja pobiera Szczegóły sposobu osiągnięcia korzyści menedżerów PdM omówione powyżej.

## <a name="data-science-for-predictive-maintenance"></a>Do nauki o danych pod kątem konserwacji predykcyjnej

Ta sekcja zawiera ogólne wytyczne dotyczące zasad do nauki o danych i praktyki dla menedżerów PdM. Jest ona przeznaczona do pomagają TDM, architekt rozwiązań lub deweloper zrozumienie wymagań wstępnych i proces tworzenia aplikacji sztucznej Inteligencji end-to-end dla menedżerów PdM. W tej sekcji można zapoznać się z przeglądem pokazów i szablonów weryfikacji koncepcji wymienionych w [szablonach rozwiązań do konserwacji predykcyjnej](#solution-templates-for-predictive-maintenance). Można następnie użyć tych zasad i najlepsze rozwiązania do implementowania rozwiązania menedżerów PdM na platformie Azure.

> [!NOTE]
> Ten przewodnik nie jest przeznaczona do uczyć czytnik do nauki o danych. Kilka przydatnych źródeł jest dostępnych do dalszej części sekcji dotyczącej [szkoleń dotyczących zasobów związanych z konserwacją predykcyjną](#training-resources-for-predictive-maintenance). [Szablony rozwiązań](#solution-templates-for-predictive-maintenance) wymienione w przewodniku przedstawiają niektóre z tych technik AI dla określonych problemów PDM.

## <a name="data-requirements-for-predictive-maintenance"></a>Wymagania dotyczące danych pod kątem konserwacji predykcyjnej

Powodzenie wszelkie learning zależy od tego, czego dotyczy przykład, (jakość i (b) zdolność uczeń. Modele predykcyjne Dowiedz się wzorców na podstawie danych historycznych i prognozowania przyszłych wyników przy użyciu określonego prawdopodobieństwa na podstawie tych wzorców obserwowanych. Model predykcyjny dokładność zależy od tego, dokładność, dostateczną ilość i jakość danych szkoleniowych i testów. Nowe dane, które są "oceniane" przy użyciu tego modelu powinny mieć takie same funkcje i schematu jako dane szkoleniowe i testowania. Funkcja właściwości nowych danych (typ, gęstość, dystrybucji i tak dalej) powinien odpowiadać okresowi istnienia zestawów danych szkoleniowych i testów. Ta sekcja koncentruje się na wymagania dotyczące tych danych.

### <a name="relevant-data"></a>Odpowiednie dane

Najpierw dane powinny być _istotne dla danego problemu_. Rozważ przypadek użycia _koła_ z opisem powyżej — dane szkoleniowe powinny zawierać funkcje związane z operacjami pokrętła. Jeśli problem był przewidywany w przypadku awarii _systemu trakcyjnego_, dane szkoleniowe mają obejmować wszystkie różne składniki systemu trakcyjnego. Pierwszy przypadek jest przeznaczony dla określonego składnika, natomiast w drugim przypadku jest przeznaczony dla awarii większych podsystemu. Ogólnie zalecane jest projektowanie systemów prognozy dotyczące określonych składników, a nie z większych podsystemów, od momentu jego będzie mieć więcej rozproszonych danych. Ekspert domeny (zobacz [problemy uprawniające do konserwacji predykcyjnej](#qualifying-problems-for-predictive-maintenance)) powinien pomóc w wyborze najbardziej odpowiednich podzbiorów danych do analizy. Odpowiednie źródła danych zostały omówione bardziej szczegółowo w temacie [przygotowanie danych do konserwacji predykcyjnej](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Ilością danych
Często zadawane pytania dwa w odniesieniu do danych historii błąd: (1) "ile zdarzenia błędów są wymagane do nauczenia modelu?" (2) "ile rekordów jest traktowanych jako" wystarczające "? Brak ostatecznych odpowiedzi, ale tylko reguły elementu kciuk. (1), aby uzyskać bardziej liczba zdarzeń błędów lepiej modelu. (2) i dokładna liczba zdarzeń błędów jest zależny od danych i kontekście problem jest rozwiązany. Ale na drugiej strony, jeśli maszyna zbyt często nie powiedzie się następnie firmy spowoduje zastąpienie, któremu zostanie obniżony wystąpienia awarii. W tym miejscu ponownie wskazówki od ekspertów domeny jest ważne. Istnieją jednak metody pozwalające sprostać problemom _rzadkich zdarzeń_. Są one omówione w sekcji [Obsługa niezrównoważonych danych](#handling-imbalanced-data).

### <a name="quality-data"></a>Dane dotyczące jakości
Jakość danych jest krytyczna — każda wartość atrybutu predykcyjnego musi być _dokładna_ w połączeniu z wartością zmiennej docelowej. Dobrą jakość danych jest dobrze przebadanych obszar w statystyk i zarządzanie danymi i tym samym poziomie zakresu, w tym przewodniku.

> [!NOTE]
> Istnieje kilka zasobów i produktami w przedsiębiorstwie dostarczać dane dotyczące jakości. Poniżej znajduje się przykład odwołania:
> - Dasu, T, Johnson, wyszukiwanie danych T., poznawczego i czyszczenie danych, Wiley, 2003.
> - [Analiza danych badawczych, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, ilościowe czyszczenie danych dla dużych baz danych](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, Van der wy, M, wprowadzenie do czyszczenia danych przy użyciu języka R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Przygotowywanie danych pod kątem konserwacji predykcyjnej

### <a name="data-sources"></a>Źródła danych

Źródła danych odpowiednie dla konserwacji predykcyjnej obejmują, ale nie są ograniczone do:
- Historia niepowodzeń
- Historia konserwacji lub naprawy.
- Warunki operacyjne maszyny
- Metadane urządzenia

#### <a name="failure-history"></a>Historia niepowodzeń
Zdarzenia błędów są rzadkie w aplikacjach menedżerów PdM. Jednak podczas kompilowania modele predykcyjne, algorytm musi Dowiedz się więcej o normalnym wzorzec operacyjnej składnika, a także jej wzorców błędów. Dlatego dane szkoleniowe powinna zawierać wystarczającą liczbę przykłady z obu kategorii. Historia obsługi rekordów i części zastąpienia są dobre źródła można znaleźć zdarzenia błędów. Za pomocą pewną wiedzę na temat domeny anomalii w danych szkoleniowych można też zdefiniować jako błędy.

#### <a name="maintenancerepair-history"></a>Historia konserwacji lub naprawy.
Historia konserwacji elementu zawartości zawiera szczegółowe informacje o składnikach, wykonanych czynnościach naprawczych itp. Te zdarzenia mają wzorce obniżenia wydajności. Braku tych ważnych informacji w danych szkoleniowych może spowodować wyświetlenie nieprawdziwych wyników modelu. Historia niepowodzeń można także znaleźć w historii konserwacji jako kody błędów specjalne lub dat zamówień dla części. Dodatkowe źródła danych, które wpływają na wzorce błędu należy zbadać i udostępniane przez ekspertów z konkretnych dziedzin.

#### <a name="machine-operating-conditions"></a>Warunki operacyjne maszyny
Dane przesyłane strumieniowo czujnika na podstawie (lub innych) urządzeń w ramach operacji są źródłem ważne dane. Kluczem założeń w PdM jest to, że stan kondycji maszyny jest zmniejszany w miarę upływu czasu podczas jego rutynowej operacji. Dane powinien zawierać w różnym czasie funkcje, które zbierają ten wzorzec przedawnienia i wszelkie anomalie, które prowadzi do spadku. Danych czasowych aspektu danych jest wymagana dla algorytmu dowiedzieć się awarii i wzorce bez błędów, wraz z upływem czasu. Oparte na następujących punktów danych, algorytm dowiaduje się przewidzieć liczbę jednostek więcej czasu maszynę można kontynuować pracę, zanim zakończy się niepowodzeniem.

#### <a name="static-feature-data"></a>Dane funkcji statycznych
Statyczne funkcje są metadane dotyczące urządzenia. Należą do nich Marka sprzętu, modelu, jest to data, start Data service, lokalizacji, systemu i inne specyfikacje techniczne.

Przykłady odpowiednich danych dla [przykładowych przypadków użycia PDM](#sample-pdm-use-cases) są podane poniżej:

| Przypadek użycia | Przykłady odpowiednie dane |
|:---------|---------------------------|
|_Opóźnienie i anulowanie lotu_ | Informacje o lotach trasy w formie nogi lotu i dzienniki strony. Dane dotyczące etapu lotu obejmują szczegóły routingu, takie jak Data wyjazdu/przybycia, czas, Port lotniczy, layovers itp. Dziennik stron zawiera serię kodów błędów i obsługi zarejestrowanych przez personel konserwacyjny.|
|_Awaria części silnika samolotu_ | Dane zbierane z czujników w samolotu, który zawiera informacje na temat stanu różnych części. Rekordy konserwacji pomagać w identyfikacji podczas wystąpienia awarii składników sprzętowych, i po nich zostały zastąpione.|
|_Awaria ATM_ | Odczyty czujników, dla każdej transakcji (złożenie środków pieniężnych/wyboru) i wydawanie środków pieniężnych. Informacje o pomiarze przerwy między notatkami, grubością notatki, odległością przybycia uwagi, sprawdzaniem atrybutów itp. Rekordy konserwacji, które zawierają kody błędów, informacje o naprawie, podczas ostatniego zapełniania rozdzielacza środków pieniężnych.|
|_Awaria wiatru_ | Czujniki czujników, takie jak temperatura, kierunek wiatru, wygenerowana energia, szybkość generatora itp. Dane są zbierane z wielu turbin wiatrów z Farm wiatrów, które znajdują się w różnych regionach. Zazwyczaj każdego turbiny mają wiele odczyty czujników, przekazywanie pomiarów w stałych przedziałach czasu.|
|_Awarie wyłącznika_ | Dzienniki konserwacji, które obejmują akcje naprawcze, prewencyjnej i systematyczny. Dane operacyjne obejmuje automatyczne i ręczne polecenia wysyłane do wyłączniki takich jak otwierających i zamykających akcji. Metadane urządzenia, takie jak data produkcji, lokalizacja, model itp. Specyfikacje wyłącznika, takie jak poziomy napięcia, geolokalizacja, warunki otoczenia.|
|_Awarie drzwi Wind_| Windy metadane, takie jak typ wind, jest to data, częstotliwość konserwacji, typ tworzenia i tak dalej. Informacje operacyjne, takie jak liczba cykli drzwi, drzwi Średni czas Zamknij. Historia niepowodzeń z przyczyn.|
|_Awarie kółka_ | Dane czujnika, które mierzą przyspieszenie kół, wystąpienia hamowania, odległość, prędkość itp. Informacje statyczne na kołach, takie jak producent, Data wyprodukowania. Dane awarii wywnioskować na podstawie bazy danych zamówień części, śledzenia dat zamówień i ilości.|
|_Subway awarie drzwi_ | Drzwi biblioteki otwarcia i zamknięcia godzinach innych danych operacyjnych, takich jak bieżący stan drzwi train. Dane statyczne obejmuje identyfikator zasobu, czasu i warunek wartość kolumny.|

### <a name="data-types"></a>Typy danych
Dwa typy danych głównego obserwowane w przypadku menedżerów PdM domeny, biorąc pod uwagę powyższe źródeł danych, są:

- _Dane_czasowe: Telemetria operacyjna, warunki maszynowe, typy zamówień pracy, kody priorytetów, które będą miały sygnatury czasowe podczas rejestrowania. Błąd, konserwacji lub naprawy i historyczne dane użycia będą również mają sygnatury czasowe, skojarzone z każdym zdarzeniem.
- _Dane statyczne_: funkcje maszyny i funkcje operatora ogólnie są statyczne, ponieważ opisują techniczne specyfikacje maszyn lub atrybutów operatora. Jeśli te funkcje można zmienić z czasem, powinni mieć także sygnatury czasowe skojarzonych z nimi.

Zmienne predykcyjne i docelowe powinny być wstępnie przetworzone/przekształcone na wartości [liczbowe, kategorii i inne typy danych](https://www.statsdirect.com/help/basics/measurement_scales.htm) w zależności od używanego algorytmu.

### <a name="data-preprocessing"></a>Przetwarzanie wstępne danych
Jako warunek wstępny dla _inżynierii funkcjonalnej_Przygotuj dane z różnych strumieni, aby utworzyć schemat, z którego można łatwo tworzyć funkcje. Najpierw wizualizować te dane jako tabelę rekordów. Każdy wiersz w tabeli reprezentuje wystąpienie szkoleniowe, a kolumny reprezentują funkcje _predykcyjne_ (nazywane również niezależnymi atrybutami lub zmiennymi). Organizuj dane w taki sposób, aby ostatnie kolumny były _elementem docelowym_ (zmienna zależna). Dla każdego wystąpienia szkoleniowego Przypisz _etykietę_ jako wartość tej kolumny.

W przypadku danych czasowych podzielić czas trwania danych z czujników na jednostki czasu. Każdy rekord powinien należeć do jednostki czasu dla elementu zawartości _i powinien oferować odrębne informacje_. Jednostki czasu są definiowane w zależności od potrzeb biznesowych wielokrotności sekundy, minuty, godziny, dni, miesięcy, i tak dalej. Jednostka czasu nie _musi być taka sama jak częstotliwość zbierania danych_. W przypadku wysokiej częstotliwości dane nie mogą być wyświetlane istotną różnicą z jednej jednostki do drugiego. Na przykład załóżmy, że temperatury otoczenia został zebrany co 10 sekund. Tylko przy użyciu tego samego interwału danych szkoleniowych zwiększa liczbę przykłady nie oferuje żadnych dodatkowych informacji. Dla tego przypadku lepszą strategię byłoby użyć średniej ponad 10 minut lub godzinę dane oparte na uzasadnienie biznesowe.

W przypadku danych statycznych
- _Rekordy konserwacji_: surowe dane obsługi mają identyfikator zasobu i sygnaturę czasową z informacjami o działaniach konserwacyjnych wykonanych w danym punkcie w czasie. Przekształć działania konserwacyjne w kolumny _kategorii_ , gdzie każdy deskryptor kategorii jednoznacznie mapuje na określoną akcję konserwacji. Schemat dla rekordów konserwacji obejmuje identyfikator zasobu, czasu i akcja konserwacji.

- _Rekordy błędów_: przyczyny niepowodzenia lub niepowodzenia mogą być rejestrowane jako określone kody błędów lub zdarzenia błędów zdefiniowane w określonych warunkach firmy. W przypadkach, gdy urządzenie ma wiele kodów błędu eksperta domeny powinna pomagać w identyfikacji te, które są odpowiednie do zawiera zmienną docelową. Użyj pozostałych kodów błędów lub warunków do skonstruowania funkcji _predykcyjnych_ , które są skorelowane z tymi awariami. Schemat dla rekordów błędów obejmuje identyfikator zasobu, czasu, Niepowodzenie lub Przyczyna niepowodzenia — Jeśli jest dostępny.

- _Metadane maszyn i operatorów_: scalanie maszyn i danych operatora w jeden schemat w celu skojarzenia elementu zawartości z jego operatorem wraz z odpowiednimi atrybutami. Identyfikator zasobu, funkcje zasobów, identyfikator — operator i operator funkcji obejmuje schematu dla warunków maszyny.

Inne kroki przetwarzania przed przetwarzaniem danych obejmują _obsługę brakujących wartości_ i _normalizację_ wartości atrybutów. Szczegółowe omówienie wykracza poza zakres tego podręcznika — w następnej sekcji niektóre przydatne dane.

Za pomocą powyższych wstępnie przetworzony źródeł danych w miejscu, przekształcenie końcowego przed technicznego opracowywania funkcji do dołączenia do powyższych tabel na podstawie identyfikatora zasobu i sygnatura czasowa. Tabeli wynikowej musi wartości null dla kolumny błąd, gdy komputer jest w normalnych warunkach. Te wartości null można kalkulacyjne za pomocą wskaźnika do normalnego działania. Ta kolumna niepowodzeń służy do tworzenia _etykiet dla modelu predykcyjnego_. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [technik modelowania do konserwacji predykcyjnej](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Inżynieria funkcji
Inżynieria funkcji jest pierwszym krokiem przed modelowania danych. Jej rola w procesie nauki o danych [została opisana tutaj](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). _Funkcja_ jest atrybutem predykcyjnym dla modelu — takim jak temperatura, ciśnienie, wibracje i tak dalej. W przypadku usługi PdM funkcja inżynierii obejmuje abstrakcyjną kondycję maszyny przez dane historyczne zebrane w czasie trwania o zmiennym rozmiarze. W tym sensie jest inny niż jego elementów równorzędnych, takich jak zdalne monitorowanie, wykrywanie anomalii i wykrywanie awarii. 

### <a name="time-windows"></a>Czas systemu windows
Zdalne monitorowanie polega na raportowaniu zdarzeń, które _wystąpiły_od momentu w czasie. Modele wykrywania anomalii oceny (wynik) przychodzących strumieni danych na anomalie flagi od punktów w czasie. Wykrywanie błędów klasyfikuje błędów będą określone typy występujące punkty w czasie. Z kolei PdM obejmuje przewidywania błędów w _przyszłych okresach_, na podstawie funkcji, które reprezentują zachowanie maszyny w _okresie historycznym_. Dla menedżerów PdM, funkcja dane z punktami czasu są zbyt hałaśliwa się. Dlatego dane dla każdej funkcji muszą być _smoothened_ przez agregację punktów danych w oknach czasu.

### <a name="lag-features"></a>Funkcja Lag funkcji
Wymagania biznesowe definiują, jak daleko ma modelu do prognozowania w przyszłości. Z kolei ten czas trwania pomaga określają "jak daleko tyłu modelu ma szukać" do tych przewidywania przyszłych zdarzeń. Ten okres "wyszukiwania wsteczny" jest nazywany _opóźnieniem_, a funkcje w tym okresie zwłoki są nazywane _funkcjami opóźnienia_. W tej sekcji omówiono funkcje opóźnienie, które mogą zostać utworzone na podstawie źródła danych za pomocą sygnatur czasowych i tworzenia funkcji ze źródeł danych statycznych. Funkcje opóźnienia są zwykle _numeryczne_ .

> [!IMPORTANT]
> Rozmiar okna jest określany za pośrednictwem eksperymentów, a powinien finalizowane przy pomocy eksperta domeny. Tym samym zastrzeżenie: przechowuje zaznaczenie i definicji funkcji lag, ich agregacji i typ systemu windows.

#### <a name="rolling-aggregates"></a>Stopniowe agregacji
Dla każdego rekordu zasobu okno stopniowe rozmiaru "W" jest wybierany jako liczba jednostek czasu do obliczenia wartości zagregowanych. Funkcje opóźnienia są następnie obliczane przy użyciu wartości W okresach _przed datą_ tego rekordu. Na rysunku 1 niebieskie linie pokazują wartości z czujników zarejestrowanych dla zasobu dla każdej jednostki czasu. Średnia krocząca wartości funkcji one oznaczają przedziale rozmiar W = 3. Średnia krocząca jest obliczana na wszystkich rekordach z sygnaturami czasowymi z zakresu t<sub>1</sub> (w kolorze pomarańczowym) do t<sub>2</sub> (zielony). Wartość dla parametru W jest zwykle w ciągu kilku minut lub godzin w zależności od charakteru danych. Ale w przypadku niektórych problemów i pobierania dużych W (np. 12 miesięcy) może zapewnić całej historii elementu zawartości, aż do rekordu.

![Rysunek 1. Stopniowe funkcje agregujące](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Rysunek 1. Stopniowe funkcje agregujące

Przykłady stopniowe agregacje w przedziale czasu to liczba, średnia, środki CUMESUM (suma zbiorczej), wartości minimalnej/maksymalnej. Ponadto odchylenie, odchylenie standardowe i liczba wartości odstających poza odchyleń standardowych N są często używane. Przykłady wartości zagregowanych, które mogą być stosowane do [przypadków użycia](#sample-pdm-use-cases) w tym przewodniku, są wymienione poniżej. 
- _Opóźnienie lotu_: liczba kodów błędów w ciągu ostatniego dnia/tygodnia.
- _Awaria części silnika samolotu_: Metoda krocząca, odchylenie standardowe i suma w ciągu ostatniego dnia, tydzień itd. Ta Metryka powinna być określona wraz z ekspertem domeny biznesowej.
- _Awarie sieci ATM_: Metoda krocząca, mediana, zakres, odchylenia standardowe, liczba elementów wykraczających poza trzy odchylenia standardowe, górny i dolny CUMESUM.
- _Awarie drzwi Subway pociąg_: liczba zdarzeń w ciągu ostatnich dni, tydzień, dwa tygodnie itd.
- _Awarie wyłącznika_: liczba błędów w ubiegłym tygodniu, rok, trzy lata itd.

Inną przydatną techniką w menedżerów PdM jest Przechwytywanie zmian trendów, wzrostów i zmiany na poziomie przy użyciu algorytmów, wykrywanie anomalii w danych.

#### <a name="tumbling-aggregates"></a>Agregacje wirowania
Dla każdego oznaczonego rekordem elementu zawartości jest zdefiniowane okno rozmiaru z _-<sub>k</sub>_  , gdzie _k_ jest liczbą okien rozmiaru _w_. Agregacje są następnie tworzone za _pomocą_ _wirowania systemu Windows_ _w-k, w-<sub>(k-1)</sub>,..., z-<sub>2</sub>, z-<sub>1</sub>_  dla okresów przed sygnaturą czasową rekordu. _k_ może być małą liczbą, aby przechwytywać skutki krótkoterminowe lub dużą liczbę do przechwytywania długoterminowych wzorców obniżenia poziomu. (patrz rysunek 2).

![Rysunek 2. Wirowania funkcje agregujące](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Rysunek 2. Wirowania funkcje agregujące

Na przykład funkcja lag funkcje dla przypadku użycia turbiny wiatru mogą zostać utworzone za pomocą W = 1 i k = 3. Oznaczają opóźnienia dla każdego z ostatnich trzech miesięcy, przy użyciu górnej i dolnej wartości odstających.

### <a name="static-features"></a>Funkcje statyczne

Przedstawiono dane techniczne urządzeń, takie jak data produkcji, numer modelu, lokalizacji, przedstawiono kilka przykładów statycznych funkcji. Są one traktowane jako zmienne _kategorii_ do modelowania. Przykłady dla przypadku użycia wyłącznik są napięcia, bieżące, pojemności usługi power, typu transformer i źródła zasilania. W przypadku błędów kółka typ opona koła (stopu vs stali) znajduje się przykład.

Wysiłek przygotowywania danych omówionych do tej pory powinny prowadzić do dane są organizowane, jak pokazano poniżej. Szkolenia, testowanie i Walidacja danych powinny mieć ten schemat logicznych (w tym przykładzie przedstawiono czas w jednostkach dni).

| Identyfikator elementu zawartości | Time | \<kolumny funkcji > | Label |
| ---- | ---- | --- | --- |
| A123 |1 dzień | . . . | . |
| A123 |2 dni | . . . | . |
| Przyciski ...  |Przyciski ...   | . . . | . |
| B234 |1 dzień | . . . | . |
| B234 |2 dni | . . . | . |
| Przyciski ...  |Przyciski ...   | . . . | . |

Ostatnim krokiem w inżynierii funkcji jest **etykietowanie** zmiennej docelowej. Ten proces jest zależna od technik modelowania. Z kolei techniki modelowania zależy od problemu biznesowego i charakter dostępnych danych. Etykietowanie jest omówiona w następnej sekcji.

> [!IMPORTANT]
> Przygotowywanie danych i technicznego opracowywania funkcji są tak ważna jak modelowanie technik znalezienie pomyślne rozwiązania menedżerów PdM. Eksperta domeny i praktyki inwestycji znaczną ilość czasu w celu uzyskania odpowiednie funkcje i dane dla modelu. Poniżej wymieniono niewielką próbkę z wielu książki na temat technicznego opracowywania funkcji:
> - Pyle, przygotowania danych D. dla danych wyszukiwania (Morgan Kaufmann serie danych systemów zarządzania), 1999 r.
> - Casari Zheng, A., technicznego opracowywania funkcji A. dla usługi Machine Learning: zasad i technik dla analityków danych, O'Reilly, 2018 r.
> - Dong, G. Liu, H. (redaktorzy), Inżynieria funkcji dla Machine Learning i analizy danych (Chapman & wyszukiwać danych i serii informacji o produkcie), prasy CRC, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Technik modelowania pod kątem konserwacji predykcyjnej

W tej sekcji omówiono technik modelowania głównego menedżerów PdM występują problemy, wraz z ich metod konstrukcji określonej etykiety. Należy zauważyć, że techniki modelowania pojedynczego mogą być używane w różnych branż. Techniki modelowania jest powiązany problem do nauki o danych, a nie w kontekście je pod ręką.

> [!IMPORTANT]
> Wybór etykiety przypadki awarii oraz strategii etykietowania  
> należy określić w porozumieniu z domeną ekspertów.

### <a name="binary-classification"></a>Klasyfikacja binarna
Klasyfikacja binarna jest używana do _przewidywania prawdopodobieństwa, że część sprzętu w przyszłości zakończy się niepowodzeniem_ — o nazwie "okres czasu w _przyszłości" X_. X jest określany przez problem biznesowy i dane, które są dostępne w konsultacji z ekspertem domeny. Oto przykłady:
- _minimalny czas realizacji_ wymagany do zastąpienia składników, wdrożenia zasobów konserwacji, przeprowadzenia konserwacji, aby uniknąć problemu, który prawdopodobnie wystąpi w tym okresie.
- _minimalna liczba zdarzeń_ , które mogą wystąpić przed wystąpieniem problemu.

W tej metodzie są identyfikowane dwa typy przykładów szkoleniowych. Pozytywny przykład, _który wskazuje błąd_, z etykietą = 1. Ujemna przykładu, który oznacza normalną pracę, przy użyciu etykiety = 0. Zmienna docelowa, dlatego wartości etykiet są _kategorii_. Model, należy zidentyfikować każdy nowy przykład jako może zakończyć się niepowodzeniem lub normalną pracę w ciągu kolejnych X jednostki czasu.

#### <a name="label-construction-for-binary-classification"></a>Konstrukcja etykiety klasyfikacji binarne
Pytanie, w tym miejscu jest: "co to jest prawdopodobieństwo, że zasób zakończy się niepowodzeniem w ciągu następnych X jednostki czasu?" Odpowiedzi na to pytanie, rekordy Etykieta X przed awarią zasobu jako "o do odrzucaj" (Etykieta = 1) i oznaczać wszystkie rekordy jako "normal" (Etykieta = 0). (patrz rysunek 3).

![Rysunek 3. Klasyfikacja binarna etykietowania](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Rysunek 3. Klasyfikacja binarna etykietowania

Poniżej przedstawiono przykłady etykietowania strategii dla niektórych przypadków użycia.
- _Opóźnienia lotu_: wartość X można wybrać jako jeden dzień, aby przewidzieć opóźnienia w ciągu następnych 24 godzin. Następnie wszystkie loty znajdujących się w ciągu 24 godzin zanim błędy są oznaczone jako 1.
- _Niepowodzenia środków pieniężnych ATM_: celem może być określenie prawdopodobieństwa niepowodzenia transakcji w ciągu następnej godziny. W takim przypadku wszystkie transakcje, które wystąpiły w ciągu ostatniej godziny błędu są oznaczone jako 1. Aby przewidzieć prawdopodobieństwo awarii za pośrednictwem dalej waluty N uwagi zbędne, wszystkie informacje o pominięte w ciągu ostatnich uwagi N awarii są oznaczone jako 1.
- _Awarie_wyłącznika: celem może być przewidywanie awarii następnego wyłącznika. W takim przypadku X jest wybierany jako jednego polecenia w przyszłości.
- _Awarie drzwi szkolenia_: X można wybrać jako dwa dni.
- _Awarie z turbiny wiatru_: X może być wybrana jako dwa miesiące.

### <a name="regression-for-predictive-maintenance"></a>Regresja pod kątem konserwacji predykcyjnej
Modele regresji są używane do _obliczania pozostałego okresu użytkowania (pozostałego czasu eksploatacji) zasobu_. Wartość pozostałego czasu eksploatacji jest zdefiniowany jako czas, który zasobu jest operacyjnej zanim nastąpi następny błąd. Każdy przykład szkolenia jest rekordem, który należy _do jednostki czasu_ dla elementu zawartości, gdzie _n_ to wielokrotność. Model powinien obliczyć pozostałego czasu eksploatacji każdego nowego przykładu jako _ciągłej liczby_. Liczba ta wskazuje, że czas pozostały przed awarią.

#### <a name="label-construction-for-regression"></a>Konstrukcja etykiety dla regresji
Pytanie, w tym miejscu jest: "Co to jest pozostałego okresu eksploatacji (RUL) urządzeń?" Dla każdego rekordu przed awarią obliczyć etykiety jako liczba jednostek czasu pozostały następny błąd. W przypadku tej metody etykiety są ciągłe zmiennych. (Zobacz rysunek 4)

![Rysunek 4. Etykietowania regresji](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Rysunek 4. Etykietowania regresji

Aby uzyskać regresji etykietowania odbywa się z odniesieniem do punktu awarii. Nie wiedząc o tym, jak długo element zawartości został przetrwały przed wystąpieniem awarii nie jest możliwe jego obliczeń. Dlatego w przeciwieństwie do klasyfikacji binarnej zasoby bez żadnych przeszkód w danych nie można używać do modelowania. Ten problem jest najlepiej rozwiązany przez inną technikę statystyczną o nazwie [Analiza przeżycia](https://en.wikipedia.org/wiki/Survival_analysis). Ale potencjalnych kompilacji mogą wystąpić w przypadku stosowania tej techniki do menedżerów PdM przypadki użycia obejmują danych w różnym czasie z częstotliwością. Aby uzyskać więcej informacji na temat analizy przeżycia, zobacz ten, który ma ten sam moduł [stronicowania](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasyfikacji wieloklasowej pod kątem konserwacji predykcyjnej
Technik klasyfikacji wieloklasowej może służyć w rozwiązaniach program PdM w przypadku dwóch scenariuszy:
- Przewidywanie _dwóch przyszłych wyników_: pierwszy wynik jest _zakresem czasu na niepowodzenie_ dla elementu zawartości. Element zawartości jest przypisany do jednego z wielu możliwych okresach czasu. Drugim wynikiem jest prawdopodobieństwo wystąpienia błędu w przyszłym okresie z powodu _jednego z głównych przyczyn_. Ta prognozowania umożliwia załogi konserwacji do obserwacji objawy i harmonogramy konserwacji planu.
- Przewidywanie _najbardziej najprawdopodobniej głównej przyczyny_ danego błędu. Ten wynik zaleca odpowiedniego zestawu działań konserwacyjnych, aby naprawić błąd. Uporządkowaną listę głównych przyczyn i zalecane naprawy może pomóc techników priorytety swoich akcji naprawy po awarii.

#### <a name="label-construction-for-multi-class-classification"></a>Konstrukcja etykiety klasyfikacji wieloklasowej
Poniżej znajduje się pytanie: "co to jest prawdopodobieństwo, że element zawartości zakończy się niepowodzeniem w następnych jednostkach _nZ_ czasu, gdzie _n_ to liczba okresów?". Aby odpowiedzieć na to pytanie, etykiety rekordów nZ przed awarią zasobów przy użyciu przedziały czasu (3Z 2Z-Z). Wszystkie inne etykiety rejestruje "normal" (Etykieta = 0). W tej metodzie zmienna docelowa zawiera wartości _kategorii_ . (Zobacz rysunek 5).

![Rysunek 5. Etykiety prognoz czasu niepowodzeń dla klasyfikacji wieloklasowej](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Rysunek 5. Etykiety klasyfikacji wieloklasowej dla błędu czasu prognoz

Poniżej znajduje się pytanie: "co to jest prawdopodobieństwo, że element zawartości zakończy się niepowodzeniem w ciągu następnych X jednostek czasu z powodu głównej przyczyny/problemu _P<sub>i</sub>_ ?". gdzie _jest_ liczbą możliwych przyczyn głównych. Aby odpowiedzieć na to pytanie, należy oznaczyć rekordy X przed awarią elementu zawartości jako "informacje o niepowodzeniach z powodu wystąpienia głównego: _p<sub>i</sub>_ " (etykieta = _p<sub>i</sub>_ ). Etykieta wszystkie rekordy jako "normal" (Etykieta = 0). W przypadku tej metody etykiety są również podzielone na kategorie (zobacz rysunek 6).

![Rysunek 6. Główne przyczyny dla klasyfikacji wieloklasowej](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Rysunek 6. Etykiety klasyfikacji wieloklasowej dla głównej przyczyny prognoz

Model przypisuje prawdopodobieństwo błędu z powodu każdego _P<sub>i</sub>_  , a także prawdopodobieństwo braku błędu. Tych prawdopodobieństw może zostać określona przez wielkość umożliwia prognozowanie problemy, które z największym prawdopodobieństwem mogą wystąpić w przyszłości.

Pytanie, w tym miejscu jest: "jakie akcje konserwacji polecisz po awarii?" Aby odpowiedzieć na to pytanie, etykieta _nie wymaga, aby można było wybrać przyszły zakres_, ponieważ model nie przewiduje niepowodzenia w przyszłości. Po prostu przewidywalnuje najprawdopodobniej główną przyczynę, gdy wystąpił _błąd_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Szkolenia, weryfikacji i testowania metod pod kątem konserwacji predykcyjnej
[Proces nauka danych zespołu](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) zapewnia pełen zakres cyklu uczenia w zakresie testów i testowania. W tej sekcji opisano aspekty unikatowe dla menedżerów PdM.

### <a name="cross-validation"></a>Krzyżowe sprawdzanie poprawności
Celem [krzyżowego sprawdzania poprawności](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) jest zdefiniowanie zestawu danych do "testowania" modelu w fazie uczenia. Ten zestaw danych jest nazywany _zestawem walidacji_. Ta technika pomaga ograniczyć problemy, takie jak _nadinstalowanie_ , i zapewnia wgląd w sposób, w jaki model będzie uogólniać do niezależnego zestawu danych. Oznacza to nieznany zestawu danych, które może być z prawdziwego problemu. Procedura szkolenia i testowania dla menedżerów PdM należy wziąć pod uwagę czas różnych aspektów, które lepiej generalize niewidzianych przyszłych danych.

Wiele algorytmów uczenia maszynowego zależą od liczby hiperparametrów, która może znacznie zmieniać wydajności modelu. Optymalne wartości hiperparametrów te nie są automatycznie obliczane, podczas uczenia modelu. Powinny one być określone, badacz danych. Istnieje kilka metod znajdowania dobre wartości hiperparametrów.

Najbardziej powszechną funkcją jest _złożenie krzyżowego sprawdzania poprawności_ , które dzieli przykłady losowo na zgięcia w _k_ . Dla każdego zestawu wartości parametrów, należy uruchomić algorytm uczenia _k_ . W każdej iteracji Użyj przykłady w bieżącej zwijania jako zestaw sprawdzania poprawności i pozostałych przykładów jako zestaw szkoleniowy. Szkolenia w zakresie algorytmu za pośrednictwem przykłady szkolenia i obliczenia metryk wydajności za pośrednictwem weryfikacji przykłady. Na końcu tej pętli Oblicz średnią metryk wydajności _k_ . Wybierz mają najlepszych średniej wydajności dla każdego zestawu wartości hiperparametrycznego. Wybranie hiperparametrów jest często eksperymentalne charakter.

Problemy z menedżerów PdM dane są zapisywane jako szereg zdarzeń, które pochodzą z wielu źródeł danych w czasie. Te rekordy mogą być uporządkowane według ich etykietowania. W związku z tym, jeśli zestaw danych zostanie _losowo_ podzielony na szkolenie i zestaw walidacji, _niektóre przykłady szkoleniowe mogą być późniejsze w czasie niż niektóre przykłady weryfikacji_. Przyszła wydajność wartości parametrów będzie Szacowana na podstawie danych, które dotarły _przed_ przeszkoleniem modelu. Te szacunki mogą być nadmiernie optymistycznej, zwłaszcza, jeśli szeregu czasowego jest nieruchomy i ewoluuje wraz z upływem czasu. Co w efekcie może być suboptymalny hiperparametrycznego wybranej wartości.

Zalecane jest, aby podzielić przykłady na szkolenia i weryfikacje w sposób _zależny od czasu_ , gdzie wszystkie przykłady sprawdzania poprawności są późniejsze niż wszystkie przykłady szkoleniowe. Dla każdego zestawu wartości hiperparametrycznego uczenie algorytmu za pośrednictwem szkoleniowy zestaw danych. Zmierz wydajność modelu nad tym samym zestawem walidacji. Wybierz wartości hiperparametrycznego, które pokazują najlepszą wydajność. Wybrany przez wynik dzielenia train/weryfikacji lepiej przyszłych modelu wydajności niż przy użyciu wartości wybierane losowo przez krzyżowego sprawdzania poprawności wartości Hiperparametrycznego.

Końcowego modelu mogą być generowane przez uczenie algorytmu uczenia szkolenia cały danych przy użyciu najlepszych wartości hiperparametrycznego.

### <a name="testing-for-model-performance"></a>Testowanie pod kątem wydajności modelu
Po skompilowaniu modelu oszacowanie realizowaniem przyszłych na nowych danych jest wymagana. Jest dobrą oszacować Metryka wydajności wartości hiperparametrycznego obliczona dla zestawu weryfikacji lub metrykę średnią wydajność obliczane na podstawie krzyżowego sprawdzania poprawności. Te szacunki są często nadmiernie optymistyczne. Firma może często mają pewne dodatkowe wytyczne dotyczące sposobu chciałby testowania modelu.

Zalecany sposób PdM polega na podzieleniu przykładów na szkolenia, walidację i testy zestawów danych w sposób _zależny od czasu_ . Wszystkie przykłady testu powinien być później w czasie niż wszystkie przykłady szkolenia i sprawdzania poprawności. Po podziału Generowanie modelu i zmierzyć jej wydajność, zgodnie z wcześniejszym opisem.

W przypadku stałych i łatwy do przewidzenia szeregów czasowych losowych i zależne od czasu metod Generowanie podobne szacunki działalności w przyszłości. Ale w przypadku innych stacjonarnych i/lub trudna do przewidzenia szeregów czasowych podejście zależne od czasu generuje szacunków bardziej realistycznego wydajność w przyszłości.

### <a name="time-dependent-split"></a>Podziel zależne od czasu
W tej sekcji opisano najlepsze rozwiązania w celu zaimplementowania podziału zależne od czasu. Poniżej opisano podziału dwukierunkowe zależne od czasu między zestawy szkoleniowe i testowe.

Przyjęto założenie, strumień oznaczony sygnaturą czasową zdarzenia, takie jak miar z różnymi czujników. Definiowanie funkcji i etykiety, szkolenia oraz przykłady testu za pośrednictwem ramy czasowe, które zawierają wiele zdarzeń. Na przykład w przypadku klasyfikacji binarnej Utwórz funkcje oparte na przeszłych zdarzeniach, a następnie Utwórz etykiety na podstawie przyszłych zdarzeń w jednostkach "X" czasu w przyszłości (zobacz sekcję dotyczącą [inżynierów funkcji](#feature-engineering) i technik modelowania). W związku z tym etykietowania przedział czasu przykładem jest dostarczany później niż przedział czasu jego funkcji.

W przypadku podziału zależnego od czasu należy wybrać _czas odcięcia szkolenia T<sub>c</sub>_  , przy którym ma być uczenie modelu, z parametrami dostrojonymi przy użyciu danych historycznych do T<sub>c</sub>. Aby zapobiec wyciekom przyszłych etykiet, które przekraczają T<sub>c</sub> do danych szkoleniowych, wybierz najnowszą godzinę, aby oznaczyć przykłady szkolenia jako X jednostek przed T<sub>c</sub>. W tym przykładzie pokazano na rysunku 7 każdy kwadrat reprezentuje rekordu w zestawie danych, których funkcji i etykiety są obliczane zgodnie z powyższym opisem. Na ilustracji przedstawiono rekordy, które powinny przejść do szkolenia i testowania zestawów dla X = 2, a W = 3:

![Rysunek 7. Zależne od czasu podział dla klasyfikacji binarnej](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Rysunek 7. Zależne od czasu podział dla klasyfikacji binarnej

Zielony kwadratów reprezentują rekordy należące do jednostki czasu, których można użyć do trenowania. Każdy przykład szkolenia jest generowany przez uwzględnienie ostatnich trzech okresów dla generacji funkcji oraz dwóch przyszłych okresów etykiet przed T<sub>c</sub>. Jeśli jakakolwiek część dwóch przyszłych okresów przekracza T<sub>c</sub>, należy wykluczyć ten przykład z zestawu danych szkoleniowych, ponieważ nie jest założono, że nie jest on widoczny poza t<sub>c</sub>.

Czarne kwadraty reprezentują rekordy końcowy etykietami zestaw danych, które nie mają być używane w zestawie danych szkoleniowych, biorąc pod uwagę powyższe ograniczenia. Te rekordy również nie będą używane w testowaniu danych, ponieważ znajdują się przed T<sub>c</sub>. Ponadto ich etykietowania horyzontach czasowych częściowo zależą od szkolenia przedziału czasu, który nie jest idealnym rozwiązaniem. Dane szkoleniowe i testowe powinny mieć osobne etykietowania przedziały czasu, aby zapobiec wyciekom informacji etykiety.

Opisana tutaj technika umożliwia zachodzenie między przykładami szkoleń i testowania, które mają sygnatury czasowe blisko T<sub>c</sub>. Rozwiązanie do osiągnięcia większej separacji polega na wykluczeniu przykładów, które znajdują się w jednostkach w czasie T<sub>c</sub> z zestawu testowego. Ale agresywne podziału jest zależna od dostępności wystarczającą ilość danych.

Problem wycieku bardziej poważnie dotyczy modele regresji, używany do przewidywania pozostałego czasu eksploatacji. Za pomocą metody split losowe prowadzi do extreme nadmiernego dopasowywania. W przypadku problemów z regresją podział powinien być taki, aby rekordy należące do zasobów z błędami przed T<sub>c</sub> przechodzą do zestawu szkoleniowego. Rekordy zasobów, które mają błędy po odcięcia przejdź do zestawu testów.

Inny najlepszym rozwiązaniem jest do dzielenia danych celów szkoleniowych i testów jest użycie podział według identyfikatora zasobu. Taki sposób, że żaden z zasobów używanych w zestaw treningowy są używane do testowania wydajności modelu, powinien być podziału. W ten sposób modelu ma szanse na dostarczanie bardziej realistycznego wyniki nowe zasoby.

### <a name="handling-imbalanced-data"></a>Obsługa danych imbalanced
W przypadku problemów z klasyfikacją, jeśli istnieje więcej przykładów jednej klasy niż pozostałe, zestaw danych jest uznawany za _niezrównoważony_. W idealnym przypadku wystarczająco dużo przedstawicieli każda klasa w dane szkoleniowe są preferowane umożliwia rozróżnianie między różnymi klasami. Jeśli jedna klasa jest mniej niż 10% danych, dane są uważane za imbalanced. Poddana Klasa jest nazywana _klasą mniejszości_.

Wiele problemów z menedżerów PdM stoją w obliczu takich imbalanced zestawów danych, gdzie jest poważnie underrepresented jednej klasy w porównaniu do innych klasy lub klas. W niektórych sytuacjach klasy mniejszości może stanowić tylko 0,001% punktów Łączna ilość danych. Brak równowagi klasy nie jest unikatowy dla menedżerów PdM. Innych domen, których rzadko błędów i anomalii stoją w obliczu podobny problem, aby uzyskać przykłady, wykrywanie oszustw i włamania do sieci. Przykłady klas mniejszości składają się tych błędów.

Za pomocą klasy nierównowagi danych wydajność większości standardowych algorytmów uczenia zostanie naruszony, ponieważ ich dążyć do zminimalizowania współczynnik błędów ogólną. Dla zestawu danych za pomocą 99% ujemny i przykładów prawdziwych 1% modelu mogą być wyświetlane do etykietowania wszystkich wystąpień negatywne mają dokładność 99%. Jednak model będzie źle klasyfikowanie wszystkich przykładów prawdziwych; Dlatego nawet jeśli jego dokładności jest wysoka, algorytm nie jest przydatne. W związku z tym konwencjonalne metryki oceny, takie jak _ogólna dokładność dla współczynnika błędów_ , są niewystarczające do uczenia się niezrównoważonym. W obliczu imbalanced zestawów danych, innych metryk służą do oceny modelu:
- Precyzja
- Odwołaj
- Wyniki F1
- Koszt korygowany ROC (receiver charakterystyk operacyjnych)

Aby uzyskać więcej informacji na temat tych metryk, zobacz [Obliczanie modelu](#model-evaluation).

Istnieją jednak niektóre metody, które pomagają rozwiązać klasy nierównowagi problem. Dwa główne są _technikami próbkowania_ i _uczeniem z uwzględnieniem kosztów_.

#### <a name="sampling-methods"></a>Metody pobierania próbek
Imbalanced learning polega na użyciu metody służące do modyfikowania szkoleniowy zestaw danych do zestawu danych o zrównoważonym obciążeniu do pobierania próbek. Metody pobierania próbek nie mają być stosowane do zestawu testów. Mimo że istnieje kilka technik próbkowania, większość prostych _przepróbkuje losowo_ i _Pobiera próbkowanie_.

_Losowe próbkowanie_ obejmuje wybranie losowej próbki z klasy mniejszości, replikowanie tych przykładów i dodawanie ich do zestawu danych szkoleniowych. W związku z tym liczba przykłady w klasie mniejszości jest zwiększana, a ostatecznie równomierny podział liczby przykłady różnych klas. Wadą minority to, że wiele wystąpień niektóre przykłady może spowodować klasyfikatory stają się zbyt określonych, co prowadzi do nadmiernego dopasowywania. Model może zawierać szkolenia wysokiej dokładności, ale jej wydajności na danych testowych niewidzianych może być nieoptymalne.

Na odwrót, _losowo w obszarze próbkowania_ wybiera losowo losową próbkę z klasy większości i usuwa te przykłady z zestawu danych szkoleniowych. Jednak usunięcie przykłady z większością klasy, może spowodować klasyfikatora do pominięcia ważnych pojęć odnoszących się do klasy głosów. _Próbkowanie hybrydowe_ , w przypadku których Klasa mniejszości jest nadmiernie, a większość klasy jest w tym samym czasie, jest to kolejne rozwiązanie.

Istnieje wiele zaawansowanych technik. Technika wybrany zależy od tego, właściwości danych i wyniki eksperymenty iteracyjne naukowego przetwarzania danych.

#### <a name="cost-sensitive-learning"></a>Koszt learning poufnych
W menedżerów PdM błędy, które stanowią klasy mniejszości są większe znaczenie niż przykładami normalnym. Dlatego koncentruje się głównie na wydajność algorytmu na błędy. Niepoprawnie Prognozowanie dodatnią klasy jako klasę ujemna mogą kosztować więcej niż na odwrót. Ta sytuacja jest często określana jako nierówne utratę lub asymetrycznego koszt źle klasyfikowania elementów różnych klas. Idealne klasyfikatora powinny dostarczyć dokładności prognozy wysokiej za pośrednictwem klasy mniejszości, bez obniżania dokładności dla klasy większość.

Istnieje wiele sposobów umożliwiające osiągnięcie tej równowagi. Aby rozwiązać problemu nierównego utraty, przypisz wysokich kosztów do błędnej klasyfikacji klasy mniejszości i spróbuj zminimalizowanie całkowitych kosztów. Algorytmy, takie jak _SVMs (obsługują maszyny wektorowe)_ , przyjmują tę metodę z założenia, przez umożliwienie ponoszenia kosztów pozytywnych i negatywnych w trakcie szkolenia. Podobnie, zwiększenie metod, takich jak _drzewa decyzyjne_ , zwykle pokazuje dobrą wydajność dzięki niezrównoważonym danym.

## <a name="model-evaluation"></a>Ocena modelu
Błędna klasyfikacja to poważny problem program PdM w scenariuszach, gdzie koszt fałszywych alarmów w firmie jest wysoka. Na przykład decyzję o prowadzić do uziemienia danego samolotu, oparte na niepoprawne prognozowania awarii aparatu może zakłócać harmonogramów i planów podróży. Przełączeniem w tryb offline z linii montażowej może prowadzić do utraty przychodów. Dlatego ważne jest oceny modelu za pomocą metryk prawo wydajności w odniesieniu do nowych danych testowych.

Typowe wskaźniki używane do oceny modeli menedżerów PdM omówiono poniżej:

- [Dokładność](https://en.wikipedia.org/wiki/Accuracy_and_precision) to najpopularniejsza Metryka używana do opisywania wydajności klasyfikatora. Ale dokładności jest wrażliwa na dystrybucji danych, jest nieefektywne miary dla scenariuszy z imbalanced zestawami danych. Zamiast tego używane są inne metryki. Narzędzia, takie jak niepoprawna [macierz](https://en.wikipedia.org/wiki/Confusion_matrix) , służą do obliczenia i przyczyny dokładności modelu.
- [Precyzja](https://en.wikipedia.org/wiki/Precision_and_recall) modeli PDM odnosi się do liczby fałszywych alarmów. Niższe dokładności modelu zazwyczaj odpowiada wyższy stopień fałszywych alarmów.
- Szybkość [odwoływania](https://en.wikipedia.org/wiki/Precision_and_recall) oznacza, ile błędów w zestawie testów zostało prawidłowo zidentyfikowanych przez model. Większe wycofaniu oznaczają, że modelu zakończy się w identyfikacji prawdziwe niepowodzenia.
- Wartość [F1](https://en.wikipedia.org/wiki/F1_score) jest średnią harmoniczną precyzji i odwołania, z wartością przedziału od 0 (najgorszy) do 1 (najlepiej).

Aby uzyskać Klasyfikacja binarna
- [Krzywe operacyjne odbiornika (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) to również popularna Metryka. Podczas krzywych ROC wydajności modelu jest interpretowany w oparciu o jeden Stałoprzecinkowy operacyjne na ROC.
- Jednak w przypadku problemów z PdMą _tabele Decile_ i _podnoszenia_ są bardziej szczegółowe. Skoncentruj się tylko na klasie dodatnią (błędy) i zapewnia bardziej złożonych obraz wydajności algorytm niż krzywych ROC.
  - _Tabele Decile_ są tworzone przy użyciu przykładów testowych w kolejności malejącej prawdopodobieństwa błędów. Uporządkowany przykłady są następnie grupowane w deciles (10% próbek za pomocą największe prawdopodobieństwo, a następnie 20%, 30% i tak dalej). /(Random baseline) stosunek (true dodatnich) dla każdego decile pomaga oszacować wydajność algorytm w każdym decile. Losowy punkt odniesienia przyjmuje wartości 0.1, 0.2 i tak dalej.
  - Na [wykresach przyrostowych](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) jest wykreślona wartość Decile true dodatnia, a na przykład Losowa dodatnia wartość true dla wszystkich deciles. Pierwszy deciles koncentrują się na zwykle wyników, ponieważ pokazują największy wzrost. Pierwszy deciles również są widoczne jako przedstawiciela "zagrożony", gdy jest używana dla menedżerów PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Model operacjonalizacji pod kątem konserwacji predykcyjnej

Korzyść do nauki o danych w tym ćwiczeniu zrealizowane, tylko uczonego modelu nawiązaniem operacyjnej. Oznacza to należy wdrożyć model do systemów biznesowych w celu prognozowania na podstawie danych nowy, wcześniej niewidzianych.  Nowe dane muszą być dokładnie zgodne z _podpisem modelu_ przeszkolonego modelu na dwa sposoby:
- wszystkie funkcje musi być obecny w każdym wystąpieniu logiczne (np. wiersz w tabeli) nowych danych.
- nowe dane muszą zostać wstępnie przetworzone, a każda z tych funkcji zaprojektowany w taki sam sposób, jak dane szkoleniowe.

Proces powyżej podano na wiele sposobów w literaturze akademickich i branży. Jednak poniższe instrukcje oznaczają to samo:
- _Ocena nowych danych_ przy użyciu modelu
- _Zastosuj model_ do nowych danych
- _Operacjonalizować_ model
- _Wdróż_ model
- _Uruchom model na_ podstawie nowych danych

Jak wspomniano wcześniej, operacjonalizacja modelu dla menedżerów PdM różni się od jego elementów równorzędnych. Scenariusze dotyczące wykrywania anomalii i wykrywania niepowodzeń zwykle implementują _ocenianie online_ (nazywane również _ocenianiem w czasie rzeczywistym_). W tym _miejscu model ocenia_ każdy rekord przychodzący i zwraca prognozę. Wykrywanie anomalii prognozowania jest wskazanie, że wystąpił anomalii (przykład: SVM jednej klasy). Do wykrywania awarii byłoby, typ lub klasa awarii.

Z kolei PdM obejmuje _ocenianie wsadowe_. Aby jest zgodny z podpisu modelu, funkcji w nowych danych muszą zostać uwzględnione w taki sam sposób, jak dane szkoleniowe. Dla dużych zestawach danych, które są typowe dla nowych danych funkcje są agregowane przez czas systemu windows i ocenione w ramach usługi batch. Ocenianie wsadowe jest zazwyczaj wykonywane w systemach rozproszonych, takich jak [Spark](https://spark.apache.org/) lub [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Istnieje kilka rozwiązań alternatywnych — zarówno nieoptymalne:
- Aparatów danych przesyłania strumieniowego obsługuje agregacji za pośrednictwem systemu windows w pamięci. Dlatego można utrzymywał, czy obsługują one oceniania online. Jednak te systemy są odpowiednie na potrzeby gęstą dane w oknach wąskie czasu lub rozrzedzone elementów za pośrednictwem szersze systemu windows. One może nie skalować dobrze sprawdza się w danych gęstą szersze czas systemu windows, jak pokazano w scenariuszach menedżerów PdM.
- Jeśli wsadowe ocenianie nie jest dostępna, rozwiązanie jest dostosowanie oceniania online do obsługi nowych danych w małych partiach w danym momencie.

## <a name="solution-templates-for-predictive-maintenance"></a>Szablony rozwiązań do konserwacji predykcyjnej

Końcowej części tego przewodnika zawiera listę szablonów rozwiązań menedżerów PdM, samouczki i eksperymentów zastosowanych w systemie Azure. Do subskrypcji platformy Azure w ciągu kilku minut, w niektórych przypadkach można wdrożyć te aplikacje menedżerów PdM. Mogą one służyć jako weryfikacji koncepcji demonstracji, piaskownice do eksperymentowania z alternatywnych lub akceleratory dla implementacji rzeczywistej produkcji. Szablony te znajdują się w [Azure AI Gallery](https://gallery.azure.ai) lub w witrynie [GitHub platformy Azure](https://github.com/Azure). Te przykłady różnych zostanie zawarta w tym szablonie rozwiązania wraz z upływem czasu.

| # | Tytuł | Opis |
|--:|:------|-------------|
| 2 | [Szablon rozwiązania do konserwacji predykcyjnej platformy Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Szablon rozwiązania typu "open source", który demonstruje modelowanie Azure ML i kompletną infrastrukturę platformy Azure, która może obsługiwać scenariusze konserwacji predykcyjnej w kontekście monitorowania zdalnego usługi IoT. |
| 3 | [Głębokie uczenie na potrzeby konserwacji predykcyjnej](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Notes platformy Azure z rozwiązaniem demonstracyjnym dotyczącym korzystania z sieci LSTM (Long Short-Term Memory) (klasy reneuronowychych sieci) do konserwacji predykcyjnej z [wpisem w blogu w tym przykładzie](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Przewodnik modelowania konserwacji predykcyjnej w języku R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Podręcznik modelowania menedżerów PdM za pomocą skryptów w języku R.|
| 5 | [Konserwacja predykcyjna na platformie Azure dla platformy Aerospace](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Jedna z pierwszym szablony rozwiązań program PdM w oparciu o uczenie Maszynowe Azure w wersji 1.0 samolotu konserwacji. Ten przewodnik pochodziły z tego projektu. |
| 6 | [Zestaw narzędzi platformy Azure AI dla IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI w IoT Edge przy użyciu TensorFlow; Pakiet Toolkit udostępnia modele uczenia głębokiego w Azure IoT Edge zgodnych kontenerach platformy Docker i uwidacznia te modele jako interfejsy API REST.
| 7 | [Konserwacja predykcyjna usługi Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Pakiet Azure IoT Suite komputerów — wstępnie skonfigurowanego rozwiązania. Szablon menedżerów PdM konserwacji samolotu pakietu IoT. [Inny dokument](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) i [Przewodnik](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) dotyczący tego samego projektu. |
| 8 | [Szablon konserwacji predykcyjnej używający SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Pokaz scenariusza użytkowania pozostałe opartych na usługach R. |
| 9 | [Przewodnik modelowania konserwacji predykcyjnej](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funkcja zestawu danych obsługi technicznej dla samolotów została zaprojektowana przy użyciu języka R z [eksperymentami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) i [zestawami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) danych oraz programem [Azure Notes](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) i [eksperymentami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) na platformie Azure|

## <a name="training-resources-for-predictive-maintenance"></a>Zasoby szkoleniowe pod kątem konserwacji predykcyjnej

Platforma Microsoft Azure oferuje ścieżki szkoleniowe dla podstawowe pojęcia dotyczące technik menedżerów PdM, oprócz zawartości i szkolenie ogólne pojęcia sztucznej Inteligencji i rozwiązaniem.

| Zasobów szkoleniowych  | Dostępność |
|:-------------------|--------------|
| [Ścieżka szkoleniowa dla PdM przy użyciu drzew i lasów losowych](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Public | 
| [Ścieżka szkoleniowa dla PdM przy użyciu uczenia głębokiego](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Public |
| [Deweloper AI na platformie Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Public |
| [Microsoft AI](https://aischool.microsoft.com/learning-paths) | Public |
| [Uczenie usługi Azure AI z witryny GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Public |
| [Uczenie LinkedIn](https://www.linkedin.com/learning) | Public |
| [Microsoft AI YouTube seminaria internetowe](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Public |
| [Pokaż Microsoft AI](https://channel9.msdn.com/Shows/AI-Show) | Public |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partnerzy |
| [Microsoft Partner Network](https://learningportal.microsoft.com) | Partnerzy |

Ponadto bezpłatne COURSE (ogromnych otwartych kursów online) na sztucznej Inteligencji są oferowane w tryb online przez instytucji akademickich, takich jak Stanforda i MIT i innych firm edukacyjnych.
