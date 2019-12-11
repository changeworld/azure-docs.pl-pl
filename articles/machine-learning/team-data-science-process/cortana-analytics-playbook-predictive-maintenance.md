---
title: Przewodnik sztucznej Inteligencji platformy Azure dla rozwiązania do konserwacji zapobiegawczej - zespołu danych dla celów naukowych
description: Kompleksowy opis do nauki o danych, zapewniająca rozwiązania do konserwacji zapobiegawczej w wielu branżach pionowy.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/11/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: d5201cd2e7c117e1229fcd04d77e8c429c1fc8ba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977135"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Przewodnik sztucznej Inteligencji platformy Azure dla rozwiązania do konserwacji zapobiegawczej

## <a name="summary"></a>Podsumowanie

Konserwacja zapobiegawcza (**menedżerów PdM**) jest aplikacją popularnych analizy predykcyjnej, które mogą pomóc firmom w branżach osiągnięcia wykorzystania zasobów o wysokiej i oszczędności kosztów operacyjnych. Ten przewodnik umożliwia połączenie ze sobą biznesowych i analitycznych wskazówki i najlepsze rozwiązania pomyślnie opracowywać i wdrażać rozwiązania menedżerów PdM przy użyciu [platformy Microsoft Azure AI](https://azure.microsoft.com/overview/ai-platform) technologii.

Po pierwsze ten przewodnik wprowadzenie scenariusze biznesowe specyficzne dla branży i proces kwalifikujących się te scenariusze dla menedżerów PdM. Wymagania dotyczące danych i technik modelowania, do tworzenia rozwiązań menedżerów PdM również są dostarczone. Główną zawartością przewodnik znajduje się na proces analizy danych — w tym kroki przygotowywania danych, technicznego opracowywania funkcji, tworzenia modelu i operacjonalizacji modeli. Jako uzupełnienie tych kluczowych pojęć, ten przewodnik zawiera zestaw szablonów rozwiązań ułatwia przyspieszenie procesu opracowywania aplikacji menedżerów PdM. Przewodnik wskazuje również zasoby szkoleniowe przydatne do lekarza dowiedzieć się więcej o sztucznej Inteligencji za do nauki o danych. 

### <a name="data-science-guide-overview-and-target-audience"></a>Danych analizy przewodnik Przegląd i docelowej grupy odbiorców
Pierwsza połowa tego przewodnika opisano problemy, typowych godzin pracy, korzyści płynące z zainstalowania program PdM w celu rozwiązania tych problemów i zawiera listę niektórych typowych przypadków użycia. Osobom podejmującym decyzje biznesowe (BDMs) będą mogli korzystać z tej zawartości. Drugą połowę wyjaśnia do nauki o danych za menedżerów PdM i zawiera listę rozwiązań menedżerów PdM utworzonych za pomocą zasad przedstawionych w tym przewodniku. Zapewnia również ścieżki szkoleniowe i wskaźników do materiałów szkoleniowych. Osób podejmujących decyzje techniczne (TDMs) będą przydatne tej zawartości.

| Zaczynać... | Jeśli jesteś... |
|:---------------|:---------------|
| [Przypadek biznesowy pod kątem konserwacji predykcyjnej](#business-case-for-predictive-maintenance) |Osoba podejmująca decyzje biznesowe (BDM) chcących zmniejszenia przestojów i kosztów operacyjnych i poprawić wykorzystanie sprzętu |
| [Do nauki o danych pod kątem konserwacji predykcyjnej](#data-science-for-predictive-maintenance) |techniczne osobą podejmującą decyzje (TDM) ocenia technologie menedżerów PdM, aby zrozumieć unikatowy przetwarzania danych i sztucznej Inteligencji wymagania dotyczące konserwacji predykcyjnej |
| [Szablony rozwiązań do konserwacji predykcyjnej](#solution-templates-for-predictive-maintenance)|architekt oprogramowania lub deweloper sztucznej Inteligencji, które chcą szybko paru pokaz lub Weryfikacja koncepcji |
| [Zasoby szkoleniowe pod kątem konserwacji predykcyjnej](#training-resources-for-predictive-maintenance) | niektóre lub wszystkie z powyższych i chcesz dowiedzieć się podstawowe pojęcia dotyczące do nauki o danych, narzędzia i techniki.

### <a name="prerequisite-knowledge"></a>Wymagana wiedza
Zawartość BDM nie oczekuje czytnika mieć żadnej wiedzy do nauki o danych z poprzedniego. Dla zawartości TDM podstawową wiedzę na temat statystyk i analizy danych jest pomocne. Zaleca się wiedzę na temat danych na platformę Azure i usług sztucznej Inteligencji, Python, R, XML i JSON. Techniki sztucznej Inteligencji są implementowane w pakiety języka Python i R. Szablony rozwiązań są implementowane za pomocą usług platformy Azure, narzędzia deweloperskie i zestawy SDK.

## <a name="business-case-for-predictive-maintenance"></a>Przypadek biznesowy pod kątem konserwacji predykcyjnej

Firmy wymagają szczytowej wydajności i maksymalnego użycia od sprzętu o kluczowym znaczeniu, aby móc uzyskać zwrot z inwestycji kapitałowych. Do takich zasobów należą silniki lotnicze, turbiny, windy lub chłodziarki przemysłowe, które kosztują miliony złotych, a także urządzenia do codziennego użytku, takie jak kserokopiarki, ekspresy do kawy i chłodnice wody.
- Domyślnie większość przedsiębiorstw polega na _naprawcze konserwacji_, której elementy są zastępowane co i kiedy mogą zakończyć się niepowodzeniem. Konserwacja naprawcza pozwala na pełne użycie części (okres używania składnika nie jest marnowany), ale jest kosztowne dla firmy w czasie przestoju, pracy i niezaplanowanej konserwacji (poza godzinami pracy lub w niedogodnych miejscach).
- W następnej poziomu, rozwiązanie firmy _konserwacji prewencyjnej_, których określić przydatne informacje o czasie dla części i zachować lub zamień go przed awarii. Konserwacja prewencyjna pozwala zapobiec niezaplanowanym i krytycznym awariom. Ale nadal niepełnego wykorzystania składnik przed jego pełna okres istnienia, użytkowania i pracy nadal wysokie koszty zaplanowane przerwy w działaniu.
- Celem _konserwacji predykcyjnej_ ma na celu optymalizację równowagę między naprawcze i zapobiegawcze konserwacji, włączając _dokładnie na czas_ zastąpienie elementów. Takie rozwiązanie pozwala wymieniać składniki tylko krótko przed awarią. Dzięki wydłużeniu okresu eksploatacji składników (względem konserwacji prewencyjnej) i obniżeniu kosztów niezaplanowanej konserwacji i pracy (względem konserwacji naprawczej) firmy mogą oszczędzać pieniądze i zwiększyć swoją konkurencyjność.

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

- _Analitycy danych_ na analizowanie i rozwiązywanie określonych problemów predykcyjne.
- _w chmurze architektów i deweloperów_ dozą kompleksowe rozwiązanie.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kwalifikowanie problemów pod kątem konserwacji predykcyjnej
Należy podkreślić, że nie wszystkie przypadki użycia lub problemy biznesowe, które może być skutecznie rozwiązany przez menedżerów PdM. Istnieją trzy ważne kwalifikującym się kryteria, które należy wziąć pod uwagę podczas wyboru problem:

- Ten problem ma się charakter; oznacza to powinien istnieć obiekt docelowy lub wynik do prognozowania. Problem powinien mieć również przejrzystą ścieżkę działania w celu uniknięcia błędów podczas ich wykrycia.
- Problem powinien mieć rekord operacyjnej Historia sprzętu, który zawiera _wyników zarówno dobre i złe_. Zbiór działania podjęte w celu złagodzenia nieprawidłowych wyników również powinny być dostępne w ramach tych rekordów. Napraw raportów o błędach, dzienniki konserwacji spadek wydajności, i Zastąp dzienniki są równie ważne. Ponadto naprawy podejmowanych w celu ich zastąpienie rekordy i są także przydatne.
- Historia zarejestrowane powinien być zostaną uwzględnione w _odpowiednie_ dane o _wystarczające_ wystarczająco dużo jakości obsługuje przypadek użycia. Aby uzyskać więcej informacji na temat istotności data i wynikających zobacz [wymagania dotyczące danych pod kątem konserwacji predykcyjnej](#data-requirements-for-predictive-maintenance).
- Na koniec firmy powinny mieć eksperci domeny poczuć problemu. Powinny one być świadome wewnętrznych procesów i praktyk pomocy przez analityka, zrozumieć i zinterpretować danych. Należy je również mogą wprowadzać niezbędne zmiany istniejące procesy biznesowe, aby ułatwić zbieranie odpowiednich danych, w razie problemów, jeśli to konieczne.

## <a name="sample-pdm-use-cases"></a>Przykładowe przypadki użycia menedżerów PdM
Ta sekcja koncentruje się na zbiór przypadki użycia program PdM w branżach, takich jak lotnictwa i Kosmonautyki, narzędzia i transportu. Każda sekcja rozpoczyna się od problemu biznesowego, a w tym artykule omówiono korzyści wynikające z menedżerów PdM, odpowiednie dane otaczającego problemu biznesowego, a na koniec korzyści rozwiązania menedżerów PdM.

| Problem biznesowy | Korzyści związane z menedżerów PdM |
|:-----------------|-------------------|
|**Lotnictwa**      |                   |
|_We wdrożeniu pakietu testowego opóźnienia i anulowanych rezerwacjach_ ze względu na problemy mechanicznych. Błędy, które nie można naprawić w czasie mogą spowodować lotów zostaną anulowane i zakłócić planowania i operacji. |Rozwiązania menedżerów PdM przewidzieć prawdopodobieństwo samolotu są opóźnione lub zostało anulowane z powodu awarii mechanicznych.|
|_Niepowodzenie części aparatu samolotu_: wymiana części silnika samolotu znajdują się wśród najbardziej typowych zadań konserwacji w branży lotniczej. Konserwacja rozwiązania wymagają staranne zarządzanie dostępność podstawowych składników, dostarczania i planowania|Możliwość zebrania analizy na niezawodność składnika prowadzi do znacznej redukcji kosztów inwestycyjnych.|
|**Finanse** |                         |
|_Uszkodzenie_ jest to powszechny problem w branży bankowości. Problem polega na tym zgłoszenia prawdopodobieństwo, że ATM środków pieniężnych wycofania transakcji zostanie przerwane z powodu błędu zakleszczenie lub części dokument rozdzielacz środków pieniężnych. Oparte na prognozy błędów transakcji, bankomatami może być obsługiwany aktywnie aby zapobiec występowaniu błędów.| A nie zezwolić na maszynę aby zakończyć się niepowodzeniem przy użyciu transakcji w połowie drogi, pożądane alternatywą jest program maszyny do odmowy usługi oparte na prognozowania.|
|**Energii** |                          |
|_Awarie programu wiatru turbin_: turbiny wiatrowe są głównym źródłem energii w krajach/regionach odpowiedzialnych za środowisko i obejmują wysokie koszty. Kluczowym czynnikiem wiatru turbiny jest mechanicznych generator. jego uszkodzenia renderuje turbiny nieefektywne. Jest również bardzo drogie do naprawienia.|Prognozowanie kluczowe wskaźniki wydajności, takich jak MTTF (średniego czasu awarii) może pomóc firmom energetycznym zapobiec błędom turbiny i upewnij się, minimalnym czasem przestojów. Prawdopodobieństwa niepowodzeń informuje pracowników technicznych, aby monitorować turbiny, które mogą wkrótce się nie powieść i planowanie systemów opartych na czas konserwacji. Modele predykcyjne zapewniają wgląd w różne czynniki wpływające na błędy, co pomaga techników lepiej zrozumieć głównych przyczyn problemów.|
|_Błędy wyłącznik_: dystrybucja energii elektrycznej do odporności domów i firm wymaga linie zasilania mają działać przez cały czas, aby zagwarantować dostarczanie energii. Wyłączniki pomóc ograniczyć lub uniknąć uszkodzenia power warunki pogodowe wierszy podczas przeciążenie lub niepożądane. W tym miejscu problem biznesowy jest przewiduje awarie wyłącznika.| Program PdM rozwiązania pomagają obniżenie kosztów napraw i zwiększenie cyklem życia urządzeń, takich jak wyłączniki. Pomagają poprawić jakość sieci zasilania, zmniejszając nieoczekiwanych awarii i przerw w działaniu usług.|
|**Transport i logistyka** |    |
|_Błędy drzwi elevator_: windy dużych firm oferuje usługi pełnego stosu dla milionów funkcjonalności windy na całym świecie. Windy bezpieczeństwa, niezawodności i czas pracy to główne kwestie dla swoich klientów. Te firmy śledzić te i różnych innych atrybutów za pośrednictwem czujników, aby pomóc im dzięki naprawcze i prewencyjnej konserwacji. W elevator najczęściej występujący problem klienta działa poprawnie drzwi elevator. Problem biznesowy w tym przypadku jest zapewnienie bazy wiedzy knowledge base predykcyjna aplikacja która przewiduje się, że powoduje, że ryzyko błędów drzwi biblioteki.| Windy są inwestycji kapitałowych dla potencjalnie czas 20 – 30 roku. Dlatego może być wysoce konkurencyjnych; każdej potencjalnej sprzedaży Dlatego do obsługi i pomocy technicznej oczekują. Konserwacji predykcyjnej można zapewnić tym firmom z przewagę nad ich konkurentów w swoich produktach i oferty usług.|
|_Kółka błędy_: kółka konta błędy połowę wszystkich szkolenie derailments i koszt miliardów w branży globalnego szyny. Błędy kółka również powodować rails się pogarszać, czasami powodując szyny włamanie się przedwcześnie. Podziały szyny prowadzić do katastrofami, takich jak derailments. Aby uniknąć tych wystąpień, kolei monitorować wydajność koła i zastąpić je w sposób prewencyjnej. Problem biznesowy w tym miejscu jest przewidywanie awarii koła.| Konserwacja predykcyjna kół pomoże just-in-time zastąpienie plików w formacie Wheel |
|_Błędy drzwi train całość_: główną przyczyną opóźnienia w operacjach całość jest błędy drzwi train samochodów. Problem biznesowy w tym miejscu jest przewiduje awarie drzwi pociągu.|Wczesne świadomości awarii drzwi lub liczba dni do awarii drzwi może pomóc w optymalizacji biznesowej szkolenie drzwi obsługi harmonogramy.|

Następna sekcja pobiera Szczegóły sposobu osiągnięcia korzyści menedżerów PdM omówione powyżej.

## <a name="data-science-for-predictive-maintenance"></a>Do nauki o danych pod kątem konserwacji predykcyjnej

Ta sekcja zawiera ogólne wytyczne dotyczące zasad do nauki o danych i praktyki dla menedżerów PdM. Jest ona przeznaczona do pomagają TDM, architekt rozwiązań lub deweloper zrozumienie wymagań wstępnych i proces tworzenia aplikacji sztucznej Inteligencji end-to-end dla menedżerów PdM. Można przeczytać tę sekcję, wraz z przeglądu pokazy i weryfikacji koncepcji szablonów wymienionych w [szablony rozwiązań do konserwacji predykcyjnej](#solution-templates-for-predictive-maintenance). Można następnie użyć tych zasad i najlepsze rozwiązania do implementowania rozwiązania menedżerów PdM na platformie Azure.

> [!NOTE]
> Ten przewodnik nie jest przeznaczona do uczyć czytnik do nauki o danych. Kilka źródeł przydatne znajdują się na dalsze informacje w sekcji, aby [zasobów szkoleniowych pod kątem konserwacji predykcyjnej](#training-resources-for-predictive-maintenance). [Szablony rozwiązań](#solution-templates-for-predictive-maintenance) w przewodniku pokazują niektóre z tych technik sztucznej Inteligencji dla konkretnych problemów menedżerów PdM.

## <a name="data-requirements-for-predictive-maintenance"></a>Wymagania dotyczące danych pod kątem konserwacji predykcyjnej

Powodzenie wszelkie learning zależy od tego, czego dotyczy przykład, (jakość i (b) zdolność uczeń. Modele predykcyjne Dowiedz się wzorców na podstawie danych historycznych i prognozowania przyszłych wyników przy użyciu określonego prawdopodobieństwa na podstawie tych wzorców obserwowanych. Model predykcyjny dokładność zależy od tego, dokładność, dostateczną ilość i jakość danych szkoleniowych i testów. Nowe dane, które są "oceniane" przy użyciu tego modelu powinny mieć takie same funkcje i schematu jako dane szkoleniowe i testowania. Funkcja właściwości nowych danych (typ, gęstość, dystrybucji i tak dalej) powinien odpowiadać okresowi istnienia zestawów danych szkoleniowych i testów. Ta sekcja koncentruje się na wymagania dotyczące tych danych.

### <a name="relevant-data"></a>Odpowiednie dane

Po pierwsze, dane muszą być _dotyczą problem_. Należy wziąć pod uwagę _kółka błąd_ omówione przypadek użycia powyżej - danych szkoleniowych powinna zawierać funkcje związane z operacjami koła. Jeśli ten problem był przewidywanie awarii _system rozwinięty_, ma dane szkoleniowe w celu objęcia wszystkich składników systemu rozwinięty. Pierwszy przypadek jest przeznaczony dla określonego składnika, natomiast w drugim przypadku jest przeznaczony dla awarii większych podsystemu. Ogólnie zalecane jest projektowanie systemów prognozy dotyczące określonych składników, a nie z większych podsystemów, od momentu jego będzie mieć więcej rozproszonych danych. Eksperta domeny (zobacz [kwalifikujących się problemów pod kątem konserwacji predykcyjnej](#qualifying-problems-for-predictive-maintenance)) powinny pomóc w wyborze najbardziej istotnych podzbiór danych do analizy. Źródła danych zostały omówione bardziej szczegółowo w [przygotowywania danych pod kątem konserwacji predykcyjnej](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Ilością danych
Często zadawane pytania dwa w odniesieniu do danych historii błąd: (1) "ile zdarzenia błędów są wymagane do nauczenia modelu?" (2) "ile rekordów jest traktowanych jako" wystarczające "? Brak ostatecznych odpowiedzi, ale tylko reguły elementu kciuk. (1), aby uzyskać bardziej liczba zdarzeń błędów lepiej modelu. (2) i dokładna liczba zdarzeń błędów jest zależny od danych i kontekście problem jest rozwiązany. Ale na drugiej strony, jeśli maszyna zbyt często nie powiedzie się następnie firmy spowoduje zastąpienie, któremu zostanie obniżony wystąpienia awarii. W tym miejscu ponownie wskazówki od ekspertów domeny jest ważne. Jednak istnieją metody radzenia sobie z problemem z _rzadkich zdarzenia_. Zostały one omówione w sekcji [postępowania z danymi imbalanced](#handling-imbalanced-data).

### <a name="quality-data"></a>Dane dotyczące jakości
Jakość danych ma kluczowe znaczenie — każdy predykcyjne wartość atrybutu musi być _dokładne_ w połączeniu z wartością zawiera zmienną docelową. Dobrą jakość danych jest dobrze przebadanych obszar w statystyk i zarządzanie danymi i tym samym poziomie zakresu, w tym przewodniku.

> [!NOTE]
> Istnieje kilka zasobów i produktami w przedsiębiorstwie dostarczać dane dotyczące jakości. Poniżej znajduje się przykład odwołania:
> - Dasu, T, Johnson, wyszukiwanie danych T., poznawczego i czyszczenie danych, Wiley, 2003.
> - [Analiza danych poznawczych, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Dane ilościowe Hellerstein, "j", czyszczenie w przypadku dużych baz danych](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge E, van der szukiwanie, M, wprowadzenie do czyszczenia danych, przy użyciu języka R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

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
Dane przesyłane strumieniowo czujnika na podstawie (lub innych) urządzeń w ramach operacji są źródłem ważne dane. Kluczowych założeń w menedżerów PdM jest, że stan kondycji na komputerze spadku wraz z upływem czasu, podczas jego działania procedur. Dane powinien zawierać w różnym czasie funkcje, które zbierają ten wzorzec przedawnienia i wszelkie anomalie, które prowadzi do spadku. Danych czasowych aspektu danych jest wymagana dla algorytmu dowiedzieć się awarii i wzorce bez błędów, wraz z upływem czasu. Oparte na następujących punktów danych, algorytm dowiaduje się przewidzieć liczbę jednostek więcej czasu maszynę można kontynuować pracę, zanim zakończy się niepowodzeniem.

#### <a name="static-feature-data"></a>Dane funkcji statycznych
Statyczne funkcje są metadane dotyczące urządzenia. Należą do nich Marka sprzętu, modelu, jest to data, start Data service, lokalizacji, systemu i inne specyfikacje techniczne.

Przykładowe dane istotne dla [menedżerów PdM przykładowe przypadki użycia](#sample-pdm-use-cases) są przedstawione w poniższej tabeli:

| Przypadek użycia | Przykłady odpowiednie dane |
|:---------|---------------------------|
|_Opóźnienia lotów i anulowanych rezerwacji_ | Informacje o lotach trasy w formie nogi lotu i dzienniki strony. Dane dotyczące etapu lotu obejmują szczegóły routingu, takie jak Data wyjazdu/przybycia, czas, Port lotniczy, layovers itp. Dziennik stron zawiera serię kodów błędów i obsługi zarejestrowanych przez personel konserwacyjny.|
|_Błąd części silnika samolotu_ | Dane zbierane z czujników w samolotu, który zawiera informacje na temat stanu różnych części. Rekordy konserwacji pomagać w identyfikacji podczas wystąpienia awarii składników sprzętowych, i po nich zostały zastąpione.|
|_Błąd ATM_ | Odczyty czujników, dla każdej transakcji (złożenie środków pieniężnych/wyboru) i wydawanie środków pieniężnych. Informacje o pomiarze przerwy między notatkami, grubością notatki, odległością przybycia uwagi, sprawdzaniem atrybutów itp. Rekordy konserwacji, które zawierają kody błędów, informacje o naprawie, podczas ostatniego zapełniania rozdzielacza środków pieniężnych.|
|_Błąd turbiny wiatru_ | Czujniki czujników, takie jak temperatura, kierunek wiatru, wygenerowana energia, szybkość generatora itp. Dane są zbierane z wielu turbin wiatrów z Farm wiatrów, które znajdują się w różnych regionach. Zazwyczaj każdego turbiny mają wiele odczyty czujników, przekazywanie pomiarów w stałych przedziałach czasu.|
|_Błędy wyłącznika_ | Dzienniki konserwacji, które obejmują akcje naprawcze, prewencyjnej i systematyczny. Dane operacyjne obejmuje automatyczne i ręczne polecenia wysyłane do wyłączniki takich jak otwierających i zamykających akcji. Metadane urządzenia, takie jak data produkcji, lokalizacja, model itp. Specyfikacje wyłącznika, takie jak poziomy napięcia, geolokalizacja, warunki otoczenia.|
|_Błędy drzwi elevator_| Windy metadane, takie jak typ wind, jest to data, częstotliwość konserwacji, typ tworzenia i tak dalej. Informacje operacyjne, takie jak liczba cykli drzwi, drzwi Średni czas Zamknij. Historia niepowodzeń z przyczyn.|
|_Błędy koło_ | Dane czujnika, które mierzą przyspieszenie kół, wystąpienia hamowania, odległość, prędkość itp. Informacje statyczne na kołach, takie jak producent, Data wyprodukowania. Dane awarii wywnioskować na podstawie bazy danych zamówień części, śledzenia dat zamówień i ilości.|
|_Całość train drzwi błędów_ | Drzwi biblioteki otwarcia i zamknięcia godzinach innych danych operacyjnych, takich jak bieżący stan drzwi train. Dane statyczne obejmuje identyfikator zasobu, czasu i warunek wartość kolumny.|

### <a name="data-types"></a>Typy danych
Dwa typy danych głównego obserwowane w przypadku menedżerów PdM domeny, biorąc pod uwagę powyższe źródeł danych, są:

- _Dane czasowe_: telemetrii operacyjnej, warunki maszyny, kolejność roboczych kody priorytetu, które mają sygnatury czasowe w czasie rejestrowania. Błąd, konserwacji lub naprawy i historyczne dane użycia będą również mają sygnatury czasowe, skojarzone z każdym zdarzeniem.
- _Dane statyczne_: maszyny i funkcje operatora ogólnie rzecz biorąc są statyczne ponieważ opisują one specyfikacje techniczne maszyn lub operator atrybutów. Jeśli te funkcje można zmienić z czasem, powinni mieć także sygnatury czasowe skojarzonych z nimi.

Zmienne predykcyjnych i docelowy powinien być wstępnie przetworzonych/przekształcane [typy danych liczbowych, podzielone na kategorie i inne](https://www.statsdirect.com/help/basics/measurement_scales.htm) w zależności od algorytm.

### <a name="data-preprocessing"></a>Przetwarzanie wstępne danych
Jako warunek wstępny do _Inżynieria funkcji_, przygotowania danych z różnych strumieni do tworzenia schemat, z którego jest tworzenie funkcji jest łatwe. Najpierw wizualizować te dane jako tabelę rekordów. Każdy wiersz w tabeli reprezentuje wystąpienie szkolenia i kolumny reprezentują _predykcyjne_ funkcji (nazywanych również atrybuty niezależnie od lub zmienne). Organizowanie danych w taki sposób, że jest ostatniej kolumny _docelowej_ (zmienna zależnych od ustawień lokalnych). Dla każdego wystąpienia szkolenia przypisać _etykiety_ jako wartość tej kolumny.

W przypadku danych czasowych podzielić czas trwania danych z czujników na jednostki czasu. Każdy rekord powinien należeć do jednostka czasu dla zasobu, _i powinno oferować różne informacje_. Jednostki czasu są definiowane w zależności od potrzeb biznesowych wielokrotności sekundy, minuty, godziny, dni, miesięcy, i tak dalej. Jednostka czasu _nie musi być taka sama jak częstotliwość zbierania danych_. W przypadku wysokiej częstotliwości dane nie mogą być wyświetlane istotną różnicą z jednej jednostki do drugiego. Na przykład załóżmy, że temperatury otoczenia został zebrany co 10 sekund. Tylko przy użyciu tego samego interwału danych szkoleniowych zwiększa liczbę przykłady nie oferuje żadnych dodatkowych informacji. Dla tego przypadku lepszą strategię byłoby użyć średniej ponad 10 minut lub godzinę dane oparte na uzasadnienie biznesowe.

W przypadku danych statycznych
- _Rekordy konserwacji_: dane pierwotne konserwacji ma identyfikator zasobu i sygnaturę czasową z informacji na temat czynności konserwacyjnych, które zostały wykonane w danym punkcie w czasie. Przekształcanie czynności konserwacyjnych na _podzielonych na kategorie_ kolumn, w którym jednoznacznie mapuje akcja konserwacji określonego deskryptora każdej kategorii. Schemat dla rekordów konserwacji obejmuje identyfikator zasobu, czasu i akcja konserwacji.

- _Rejestruje błąd_: błędy lub przyczyny niepowodzenia może zostać zarejestrowana jako konkretnych kodach błędów lub zdarzenia błędów zdefiniowanych przez warunki firmy. W przypadkach, gdy urządzenie ma wiele kodów błędu eksperta domeny powinna pomagać w identyfikacji te, które są odpowiednie do zawiera zmienną docelową. Konstruowania za pomocą pozostałych kodów błędów i warunki _predykcyjne_ funkcje, które odnoszą się tych błędów. Schemat dla rekordów błędów obejmuje identyfikator zasobu, czasu, Niepowodzenie lub Przyczyna niepowodzenia — Jeśli jest dostępny.

- _Metadane maszyny i operator_: scalania danych maszyn i operator w jeden schemat do kojarzenia środka trwałego operatorem wraz z ich odpowiednich atrybutów. Identyfikator zasobu, funkcje zasobów, identyfikator — operator i operator funkcji obejmuje schematu dla warunków maszyny.

Inne dane, przetwarzanie wstępne kroki obejmują _obsługi brakujące wartości_ i _normalizacji_ wartości atrybutów. Szczegółowe omówienie wykracza poza zakres tego podręcznika — w następnej sekcji niektóre przydatne dane.

Za pomocą powyższych wstępnie przetworzony źródeł danych w miejscu, przekształcenie końcowego przed technicznego opracowywania funkcji do dołączenia do powyższych tabel na podstawie identyfikatora zasobu i sygnatura czasowa. Tabeli wynikowej musi wartości null dla kolumny błąd, gdy komputer jest w normalnych warunkach. Te wartości null można kalkulacyjne za pomocą wskaźnika do normalnego działania. Ta kolumna niepowodzenia służy do tworzenia _etykiety dla modelu predykcyjnego_. Aby uzyskać więcej informacji, zobacz sekcję na [modelowania technik pod kątem konserwacji predykcyjnej](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Inżynieria funkcji
Inżynieria funkcji jest pierwszym krokiem przed modelowania danych. Jego rolę w procesie nauki o danych [opisany tutaj](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). A _funkcji_ jest atrybutem predykcyjnego modelu — takich jak temperatury, wykorzystanie, wibracje i tak dalej. Dla menedżerów PdM technicznego opracowywania funkcji obejmuje abstrakcyjność kondycja maszyny historycznych danych zbieranych za pośrednictwem sporej liczbie czasu trwania. W tym sensie jest inny niż jego elementów równorzędnych, takich jak zdalne monitorowanie, wykrywanie anomalii i wykrywanie awarii. 

### <a name="time-windows"></a>Czas systemu windows
Zdalne monitorowanie powoduje zgłoszenie zdarzenia mające miejsce w _punkty w czasie_. Modele wykrywania anomalii oceny (wynik) przychodzących strumieni danych na anomalie flagi od punktów w czasie. Wykrywanie błędów klasyfikuje błędów będą określone typy występujące punkty w czasie. Z kolei menedżerów PdM pociąga za sobą przewidywanie awarii za pośrednictwem _przyszłego okresu_, zależnie od funkcji, które reprezentują zachowanie maszyny za pośrednictwem _historycznych okres_. Dla menedżerów PdM, funkcja dane z punktami czasu są zbyt hałaśliwa się. Aby dane dla każdej funkcji muszą być _smoothened_ przez agregowanie punktów danych na przestrzeni przedziału czasu.

### <a name="lag-features"></a>Funkcja Lag funkcji
Wymagania biznesowe definiują, jak daleko ma modelu do prognozowania w przyszłości. Z kolei ten czas trwania pomaga określają "jak daleko tyłu modelu ma szukać" do tych przewidywania przyszłych zdarzeń. Nazywa się to "możliwą" okres _opóźnienie_, a funkcje odtwarzane w tym okresie opóźnienia są nazywane _opóźnione funkcji_. W tej sekcji omówiono funkcje opóźnienie, które mogą zostać utworzone na podstawie źródła danych za pomocą sygnatur czasowych i tworzenia funkcji ze źródeł danych statycznych. Funkcje opóźnienia są zwykle _liczbowe_ charakter.

> [!IMPORTANT]
> Rozmiar okna jest określany za pośrednictwem eksperymentów, a powinien finalizowane przy pomocy eksperta domeny. Tym samym zastrzeżenie: przechowuje zaznaczenie i definicji funkcji lag, ich agregacji i typ systemu windows.

#### <a name="rolling-aggregates"></a>Stopniowe agregacji
Dla każdego rekordu zasobu okno stopniowe rozmiaru "W" jest wybierany jako liczba jednostek czasu do obliczenia wartości zagregowanych. Opóźnienie funkcje są obliczane przy użyciu okresów W _przed datą_ tego rekordu. Na rysunku 1 niebieskie linie pokazują wartości z czujników zarejestrowanych dla zasobu dla każdej jednostki czasu. Średnia krocząca wartości funkcji one oznaczają przedziale rozmiar W = 3. Średnia krocząca jest obliczana przez wszystkie rekordy z sygnatury czasowe w zakresie t<sub>1</sub> (na pomarańczowo) do t<sub>2</sub> (w kolorze zielonym). Wartość dla parametru W jest zwykle w ciągu kilku minut lub godzin w zależności od charakteru danych. Ale w przypadku niektórych problemów i pobierania dużych W (np. 12 miesięcy) może zapewnić całej historii elementu zawartości, aż do rekordu.

![Rysunek 1. Stopniowe funkcje agregujące](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png)

Rysunek 1. Stopniowe funkcje agregujące

Przykłady stopniowe agregacje w przedziale czasu to liczba, średnia, środki CUMESUM (suma zbiorczej), wartości minimalnej/maksymalnej. Ponadto odchylenie, odchylenie standardowe i liczba wartości odstających poza odchyleń standardowych N są często używane. Przykłady agregacji, które mogą być stosowane dla [przypadki użycia](#sample-pdm-use-cases) w tym przewodniku są wymienione poniżej. 
- _We wdrożeniu pakietu testowego opóźnienie_: liczba kodów błędów przez ostatni dzień/tydzień.
- _Awaria części silnika samolotu_: Metoda krocząca, odchylenie standardowe i suma w ciągu ostatniego dnia, tydzień itd. Ta Metryka powinna być określona wraz z ekspertem domeny biznesowej.
- _Błędy ATM_: stopniowe oznacza, że mediana, zakres, odchyleń standardowych, liczby wartości odstających ponad trzech odchyleń standardowych, górny i dolny CUMESUM.
- _Błędy drzwi train całość_: liczba zdarzeń w ciągu doby tydzień, dwa tygodnie itp.
- _Błędy wyłącznik_: błąd zlicza ciągu ostatniego tygodnia, rok, trzech lat itd.

Inną przydatną techniką w menedżerów PdM jest Przechwytywanie zmian trendów, wzrostów i zmiany na poziomie przy użyciu algorytmów, wykrywanie anomalii w danych.

#### <a name="tumbling-aggregates"></a>Agregacje wirowania
Dla każdego oznaczonego rekordem elementu zawartości jest zdefiniowane okno rozmiaru z _-<sub>k</sub>_  , gdzie _k_ jest liczbą okien rozmiaru _w_. Agregacje są następnie tworzone za _pomocą_ _wirowania systemu Windows_ _w-k, w-<sub>(k-1)</sub>,..., z-<sub>2</sub>, z-<sub>1</sub>_  dla okresów przed sygnaturą czasową rekordu. _k_ może być małą liczbą krótkoterminowej efektów przechwycić lub dużą liczbą do wykrywania długoterminowych wzorców spadek przechwytywania. (patrz rysunek 2).

![Rysunek 2. Wirowania funkcje agregujące](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png)

Rysunek 2. Wirowania funkcje agregujące

Na przykład funkcja lag funkcje dla przypadku użycia turbiny wiatru mogą zostać utworzone za pomocą W = 1 i k = 3. Oznaczają opóźnienia dla każdego z ostatnich trzech miesięcy, przy użyciu górnej i dolnej wartości odstających.

### <a name="static-features"></a>Funkcje statyczne

Przedstawiono dane techniczne urządzeń, takie jak data produkcji, numer modelu, lokalizacji, przedstawiono kilka przykładów statycznych funkcji. Będą one traktowane jako _podzielonych na kategorie_ zmienne modelowania. Przykłady dla przypadku użycia wyłącznik są napięcia, bieżące, pojemności usługi power, typu transformer i źródła zasilania. W przypadku błędów kółka typ opona koła (stopu vs stali) znajduje się przykład.

Wysiłek przygotowywania danych omówionych do tej pory powinny prowadzić do dane są organizowane, jak pokazano poniżej. Szkolenia, testowanie i Walidacja danych powinny mieć ten schemat logicznych (w tym przykładzie przedstawiono czas w jednostkach dni).

| Identyfikator elementu zawartości | Czas | \<kolumny funkcji > | Etykieta |
| ---- | ---- | --- | --- |
| A123 |1 dzień | . . . | . |
| A123 |2 dni | . . . | . |
| Przyciski ...  |Przyciski ...   | . . . | . |
| B234 |1 dzień | . . . | . |
| B234 |2 dni | . . . | . |
| Przyciski ...  |Przyciski ...   | . . . | . |

Ostatnim krokiem w technicznego opracowywania funkcji jest **etykietowania** zmiennej docelowej. Ten proces jest zależna od technik modelowania. Z kolei techniki modelowania zależy od problemu biznesowego i charakter dostępnych danych. Etykietowanie jest omówiona w następnej sekcji.

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
- _Minimalny czas realizacji_ wymagane, aby zastąpić składników, wdrażanie zasobów konserwacji, przeprowadzania konserwacji, aby uniknąć problemu, który może nastąpić w tym okresie.
- _Minimalna liczba zdarzeń_ , może się zdarzyć, zanim wystąpi problem.

W tej metodzie są identyfikowane dwa typy przykładów szkoleniowych. Przykład dodatnią _co oznacza błąd_, z etykietą = 1. Ujemna przykładu, który oznacza normalną pracę, przy użyciu etykiety = 0. Zmienna docelowa, i dlatego są wartości etykiet _podzielonych na kategorie_. Model, należy zidentyfikować każdy nowy przykład jako może zakończyć się niepowodzeniem lub normalną pracę w ciągu kolejnych X jednostki czasu.

#### <a name="label-construction-for-binary-classification"></a>Konstrukcja etykiety klasyfikacji binarne
Pytanie, w tym miejscu jest: "co to jest prawdopodobieństwo, że zasób zakończy się niepowodzeniem w ciągu następnych X jednostki czasu?" Odpowiedzi na to pytanie, rekordy Etykieta X przed awarią zasobu jako "o do odrzucaj" (Etykieta = 1) i oznaczać wszystkie rekordy jako "normal" (Etykieta = 0). (patrz rysunek 3).

![Rysunek 3. Klasyfikacja binarna etykietowania](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png)

Rysunek 3. Klasyfikacja binarna etykietowania

Poniżej przedstawiono przykłady etykietowania strategii dla niektórych przypadków użycia.
- _We wdrożeniu pakietu testowego opóźnienia_: X może zostać wybrany jako 1 dzień do prognozowania opóźnień w ciągu 24 godzin. Następnie wszystkie loty znajdujących się w ciągu 24 godzin zanim błędy są oznaczone jako 1.
- _Środków pieniężnych ATM rozdzielić błędy_: celem może być ustalenie prawdopodobieństwo awarii transakcji w ciągu jednej godziny. W takim przypadku wszystkie transakcje, które wystąpiły w ciągu ostatniej godziny błędu są oznaczone jako 1. Aby przewidzieć prawdopodobieństwo awarii za pośrednictwem dalej waluty N uwagi zbędne, wszystkie informacje o pominięte w ciągu ostatnich uwagi N awarii są oznaczone jako 1.
- _Błędy wyłącznik_: celem może być do prognozowania następny błąd polecenia wyłącznika. W takim przypadku X jest wybierany jako jednego polecenia w przyszłości.
- _Szkolenie błędy drzwi_: X może być wybrana jako dwa dni.
- _Błędy turbiny wiatru_: X może być wybrana jako dwa miesiące.

### <a name="regression-for-predictive-maintenance"></a>Regresja pod kątem konserwacji predykcyjnej
Modele regresji są używane do _obliczenia pozostałego okresu eksploatacji (RUL) środka trwałego_. Wartość pozostałego czasu eksploatacji jest zdefiniowany jako czas, który zasobu jest operacyjnej zanim nastąpi następny błąd. Każdy przykład szkolenia jest rekordem, której należy jednostka czasu _nY_ dla zasobu, gdzie _n_ jest wielokrotnością. Model należy obliczyć wartość pozostałego czasu eksploatacji, każdy nowy przykładu jako _numer kolejny_. Liczba ta wskazuje, że czas pozostały przed awarią.

#### <a name="label-construction-for-regression"></a>Konstrukcja etykiety dla regresji
Pytanie, w tym miejscu jest: "Co to jest pozostałego okresu eksploatacji (RUL) urządzeń?" Dla każdego rekordu przed awarią obliczyć etykiety jako liczba jednostek czasu pozostały następny błąd. W przypadku tej metody etykiety są ciągłe zmiennych. (Zobacz rysunek 4)

![Rysunek 4. Etykietowania regresji](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png)

Rysunek 4. Etykietowania regresji

Aby uzyskać regresji etykietowania odbywa się z odniesieniem do punktu awarii. Nie wiedząc o tym, jak długo element zawartości został przetrwały przed wystąpieniem awarii nie jest możliwe jego obliczeń. Dlatego w przeciwieństwie do klasyfikacji binarnej zasoby bez żadnych przeszkód w danych nie można używać do modelowania. Ten problem dotyczy najlepiej innej techniki statystyczne o nazwie [analiza przeżycia](https://en.wikipedia.org/wiki/Survival_analysis). Ale potencjalnych kompilacji mogą wystąpić w przypadku stosowania tej techniki do menedżerów PdM przypadki użycia obejmują danych w różnym czasie z częstotliwością. Aby uzyskać więcej informacji na temat analiza przeżycia, zobacz [tym jeden pagera](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasyfikacji wieloklasowej pod kątem konserwacji predykcyjnej
Technik klasyfikacji wieloklasowej może służyć w rozwiązaniach program PdM w przypadku dwóch scenariuszy:
- Przewidywanie _dwóch przyszłych wyników_: pierwszy wynik jest _zakres czasu do awarii_ dla zasobu. Element zawartości jest przypisany do jednego z wielu możliwych okresach czasu. Drugi wynik jest prawdopodobieństwo wystąpienia błędu w przyszłości ze względu na _jeden z kilku głównych powoduje, że_. Ta prognozowania umożliwia załogi konserwacji do obserwacji objawy i harmonogramy konserwacji planu.
- Przewidywanie _najbardziej prawdopodobne przyczyny_ danego błędu. Ten wynik zaleca odpowiedniego zestawu działań konserwacyjnych, aby naprawić błąd. Uporządkowaną listę głównych przyczyn i zalecane naprawy może pomóc techników priorytety swoich akcji naprawy po awarii.

#### <a name="label-construction-for-multi-class-classification"></a>Konstrukcja etykiety klasyfikacji wieloklasowej
Pytanie, w tym miejscu jest: "co to jest prawdopodobieństwo, że zasób usługi zakończy się niepowodzeniem w ciągu następnych _nZ_ jednostki czasu, gdy _n_ to liczba okresów?" Aby odpowiedzieć na to pytanie, etykiety rekordów nZ przed awarią zasobów przy użyciu przedziały czasu (3Z 2Z-Z). Wszystkie inne etykiety rejestruje "normal" (Etykieta = 0). W przypadku tej metody zawiera zmienną docelową _podzielonych na kategorie_ wartości. (Zobacz rysunek 5).

![Rysunek 5. Etykiety prognoz czasu niepowodzeń dla klasyfikacji wieloklasowej](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Rysunek 5. Etykiety klasyfikacji wieloklasowej dla błędu czasu prognoz

Pytanie, w tym miejscu jest: "co to jest prawdopodobieństwo, że zasób zakończy się niepowodzeniem w ciągu następnych X jednostki czasu z powodu problemu z/głównej przyczyny _P<sub>i</sub>_ ?" gdzie _i_ jest liczba możliwych przyczyn. Odpowiedzi na to pytanie, rekordy Etykieta X przed awarią zasobu jako "wkrótce się niepowodzeniem z powodu przyczyny _P<sub>i</sub>_ " (etykieta = _P<sub>i</sub>_ ). Etykieta wszystkie rekordy jako "normal" (Etykieta = 0). W przypadku tej metody etykiety są również podzielone na kategorie (zobacz rysunek 6).

![Rysunek 6. Główne przyczyny dla klasyfikacji wieloklasowej](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Rysunek 6. Etykiety klasyfikacji wieloklasowej dla głównej przyczyny prognoz

Model przypisuje prawdopodobieństwo awarii, ze względu na każdym _P<sub>i</sub>_  oraz prawdopodobieństwo bez błędów. Tych prawdopodobieństw może zostać określona przez wielkość umożliwia prognozowanie problemy, które z największym prawdopodobieństwem mogą wystąpić w przyszłości.

Pytanie, w tym miejscu jest: "jakie akcje konserwacji polecisz po awarii?" Aby odpowiedzieć na to pytanie etykietowania _nie wymaga przyszłych horizon, które mają zostać pobrane_, ponieważ model nie jest prognozowanie błędu w przyszłości. Jego jest po prostu Prognozowanie najbardziej prawdopodobne przyczyny _po awarii zostało już przeprowadzone_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Szkolenia, weryfikacji i testowania metod pod kątem konserwacji predykcyjnej
[Zespołu danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) zapewnia pełne cyklu train testów sprawdzania poprawności modelu. W tej sekcji opisano aspekty unikatowe dla menedżerów PdM.

### <a name="cross-validation"></a>Krzyżowe sprawdzanie poprawności
Celem [krzyżowe sprawdzanie poprawności](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) jest zdefiniowanie zestawu danych na "test" model w fazie szkolenia. Ten zestaw danych jest nazywany _zestawu weryfikacji_. Ta technika ułatwia limit problemów takich jak _overfitting_ i zapewnia wgląd w sposób modelu będzie uogólnić do niezależnego zestawu danych. Oznacza to nieznany zestawu danych, które może być z prawdziwego problemu. Procedura szkolenia i testowania dla menedżerów PdM należy wziąć pod uwagę czas różnych aspektów, które lepiej generalize niewidzianych przyszłych danych.

Wiele algorytmów uczenia maszynowego zależą od liczby hiperparametrów, która może znacznie zmieniać wydajności modelu. Optymalne wartości hiperparametrów te nie są automatycznie obliczane, podczas uczenia modelu. Powinny one być określone, badacz danych. Istnieje kilka metod znajdowania dobre wartości hiperparametrów.

Najczęściej jest jeden _składanie k krzyżowego sprawdzania poprawności_ dzieli się przykłady losowo do _k_ złożeń. Dla każdego zestawu wartości hiperparametrów Uruchom algorytmu uczenia _k_ razy. W każdej iteracji Użyj przykłady w bieżącej zwijania jako zestaw sprawdzania poprawności i pozostałych przykładów jako zestaw szkoleniowy. Szkolenia w zakresie algorytmu za pośrednictwem przykłady szkolenia i obliczenia metryk wydajności za pośrednictwem weryfikacji przykłady. Na koniec ta pętla obliczenia średniej _k_ metryk wydajności. Wybierz mają najlepszych średniej wydajności dla każdego zestawu wartości hiperparametrycznego. Wybranie hiperparametrów jest często eksperymentalne charakter.

Problemy z menedżerów PdM dane są zapisywane jako szereg zdarzeń, które pochodzą z wielu źródeł danych w czasie. Te rekordy mogą być uporządkowane według ich etykietowania. W związku z tym jeśli zestaw danych jest podzielona _losowo_ do szkolenia i sprawdzanie poprawności zestawu, _niektóre przykłady szkolenia mogą być później w czasie niż niektóre przykłady sprawdzania poprawności_. Wydajność hiperparametrycznego wartości w przyszłości będzie można oszacować, na podstawie pewnych danych dotarła _przed_ został uczony model. Te szacunki mogą być nadmiernie optymistycznej, zwłaszcza, jeśli szeregu czasowego jest nieruchomy i ewoluuje wraz z upływem czasu. Co w efekcie może być suboptymalny hiperparametrycznego wybranej wartości.

Zalecaną metodą jest podzielenie przykładów na zestaw szkoleniowy i sprawdzania poprawności w _zależne od czasu_ sposób, w których później w czasie niż we wszystkich przykładach szkolenia we wszystkich przykładach sprawdzania poprawności. Dla każdego zestawu wartości hiperparametrycznego uczenie algorytmu za pośrednictwem szkoleniowy zestaw danych. Mierzenie wydajności modelu za pośrednictwem tego samego zestawu sprawdzania poprawności. Wybierz wartości hiperparametrycznego, które pokazują najlepszą wydajność. Wybrany przez wynik dzielenia train/weryfikacji lepiej przyszłych modelu wydajności niż przy użyciu wartości wybierane losowo przez krzyżowego sprawdzania poprawności wartości Hiperparametrycznego.

Końcowego modelu mogą być generowane przez uczenie algorytmu uczenia szkolenia cały danych przy użyciu najlepszych wartości hiperparametrycznego.

### <a name="testing-for-model-performance"></a>Testowanie pod kątem wydajności modelu
Po skompilowaniu modelu oszacowanie realizowaniem przyszłych na nowych danych jest wymagana. Jest dobrą oszacować Metryka wydajności wartości hiperparametrycznego obliczona dla zestawu weryfikacji lub metrykę średnią wydajność obliczane na podstawie krzyżowego sprawdzania poprawności. Te szacunki są często nadmiernie optymistyczne. Firma może często mają pewne dodatkowe wytyczne dotyczące sposobu chciałby testowania modelu.

Zalecaną metodą dla menedżerów PdM jest podzielona w przykładach szkolenia, sprawdzanie poprawności, i zestawy danych testowych _zależne od czasu_ sposób. Wszystkie przykłady testu powinien być później w czasie niż wszystkie przykłady szkolenia i sprawdzania poprawności. Po podziału Generowanie modelu i zmierzyć jej wydajność, zgodnie z wcześniejszym opisem.

W przypadku stałych i łatwy do przewidzenia szeregów czasowych losowych i zależne od czasu metod Generowanie podobne szacunki działalności w przyszłości. Ale w przypadku innych stacjonarnych i/lub trudna do przewidzenia szeregów czasowych podejście zależne od czasu generuje szacunków bardziej realistycznego wydajność w przyszłości.

### <a name="time-dependent-split"></a>Podziel zależne od czasu
W tej sekcji opisano najlepsze rozwiązania w celu zaimplementowania podziału zależne od czasu. Poniżej opisano podziału dwukierunkowe zależne od czasu między zestawy szkoleniowe i testowe.

Przyjęto założenie, strumień oznaczony sygnaturą czasową zdarzenia, takie jak miar z różnymi czujników. Definiowanie funkcji i etykiety, szkolenia oraz przykłady testu za pośrednictwem ramy czasowe, które zawierają wiele zdarzeń. Na przykład w przypadku klasyfikacji binarnej Utwórz funkcje oparte na przeszłych zdarzeniach, a następnie Utwórz etykiety na podstawie przyszłych zdarzeń w jednostkach "X" czasu w przyszłości (zobacz sekcję dotyczącą [inżynierów funkcji](#feature-engineering) i technik modelowania). W związku z tym etykietowania przedział czasu przykładem jest dostarczany później niż przedział czasu jego funkcji.

Podział zależne od czasu, można wybrać _szkolenia odcięcia czasu T<sub>c</sub>_  jaką do nauczenia modelu, za pomocą hiperparametrów dopasowane, przy użyciu danych historycznych maksymalnie T<sub>c</sub>. Aby zapobiec wyciekom przyszłych etykiety, które wykraczają poza T<sub>c</sub> do danych szkoleniowych, wybierz ostatni czas przykłady szkolenia etykiety jako X jednostki przed T<sub>c</sub>. W tym przykładzie pokazano na rysunku 7 każdy kwadrat reprezentuje rekordu w zestawie danych, których funkcji i etykiety są obliczane zgodnie z powyższym opisem. Na ilustracji przedstawiono rekordy, które powinny przejść do szkolenia i testowania zestawów dla X = 2, a W = 3:

![Rysunek 7. Zależne od czasu podział dla klasyfikacji binarnej](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png)

Rysunek 7. Zależne od czasu podział dla klasyfikacji binarnej

Zielony kwadratów reprezentują rekordy należące do jednostki czasu, których można użyć do trenowania. Każdy przykład szkolenia jest generowany, biorąc pod uwagę przeszłości trzy kropki na potrzeby generowania funkcji i dwa kolejne okresy etykietowania przed T<sub>c</sub>. Jeśli jakakolwiek część dwa kolejne okresy wykracza poza T<sub>c</sub>, wykluczyć tego przykładu z szkoleniowy zestaw danych, ponieważ nie widoczność zakłada, że poza T<sub>c</sub>.

Czarne kwadraty reprezentują rekordy końcowy etykietami zestaw danych, które nie mają być używane w zestawie danych szkoleniowych, biorąc pod uwagę powyższe ograniczenia. Te rekordy również nie będą używane podczas badania danych, ponieważ są one przed T<sub>c</sub>. Ponadto ich etykietowania horyzontach czasowych częściowo zależą od szkolenia przedziału czasu, który nie jest idealnym rozwiązaniem. Dane szkoleniowe i testowe powinny mieć osobne etykietowania przedziały czasu, aby zapobiec wyciekom informacji etykiety.

Technika omówionych do tej pory umożliwia nakładanie się szkolenia i testowania przykładów, które mają sygnatury czasowe w pobliżu T<sub>c</sub>. To rozwiązanie do osiągnięcia mocniej jest wykluczenie przykładów, które znajdują się w W jednostkach czasu T<sub>c</sub> z testu. Ale agresywne podziału jest zależna od dostępności wystarczającą ilość danych.

Problem wycieku bardziej poważnie dotyczy modele regresji, używany do przewidywania pozostałego czasu eksploatacji. Za pomocą metody split losowe prowadzi do extreme nadmiernego dopasowywania. W przypadku problemów regresji, powinny być podziału taki sposób, że rekordy należące do zasobów za pomocą zakończonych niepowodzeniem przed T<sub>c</sub> przejdź do zestawu szkoleniowego. Rekordy zasobów, które mają błędy po odcięcia przejdź do zestawu testów.

Inny najlepszym rozwiązaniem jest do dzielenia danych celów szkoleniowych i testów jest użycie podział według identyfikatora zasobu. Taki sposób, że żaden z zasobów używanych w zestaw treningowy są używane do testowania wydajności modelu, powinien być podziału. W ten sposób modelu ma szanse na dostarczanie bardziej realistycznego wyniki nowe zasoby.

### <a name="handling-imbalanced-data"></a>Obsługa danych imbalanced
W klasyfikacji problemów, jeśli istnieje więcej przykładów dotyczących jednej klasy niż inne, zestaw danych jest określany jako _imbalanced_. W idealnym przypadku wystarczająco dużo przedstawicieli każda klasa w dane szkoleniowe są preferowane umożliwia rozróżnianie między różnymi klasami. Jeśli jedna klasa jest mniej niż 10% danych, dane są uważane za imbalanced. Klasa underrepresented jest nazywana _klasy mniejszości_.

Wiele problemów z menedżerów PdM stoją w obliczu takich imbalanced zestawów danych, gdzie jest poważnie underrepresented jednej klasy w porównaniu do innych klasy lub klas. W niektórych sytuacjach klasy mniejszości może stanowić tylko 0,001% punktów Łączna ilość danych. Brak równowagi klasy nie jest unikatowy dla menedżerów PdM. Innych domen, których rzadko błędów i anomalii stoją w obliczu podobny problem, aby uzyskać przykłady, wykrywanie oszustw i włamania do sieci. Przykłady klas mniejszości składają się tych błędów.

Za pomocą klasy nierównowagi danych wydajność większości standardowych algorytmów uczenia zostanie naruszony, ponieważ ich dążyć do zminimalizowania współczynnik błędów ogólną. Dla zestawu danych za pomocą 99% ujemny i przykładów prawdziwych 1% modelu mogą być wyświetlane do etykietowania wszystkich wystąpień negatywne mają dokładność 99%. Jednak model będzie źle klasyfikowanie wszystkich przykładów prawdziwych; Dlatego nawet jeśli jego dokładności jest wysoka, algorytm nie jest przydatne. W związku z tym metryk oceny są konwencjonalne funkcje, takie jak _dokładności ogólny współczynnik błędów_ są niewystarczające do imbalanced uczenia. W obliczu imbalanced zestawów danych, innych metryk służą do oceny modelu:
- Precyzja
- Odwołaj
- Wyniki F1
- Koszt korygowany ROC (receiver charakterystyk operacyjnych)

Aby uzyskać więcej informacji o tych wskaźnikach, zobacz [modelu oceny](#model-evaluation).

Istnieją jednak niektóre metody, które pomagają rozwiązać klasy nierównowagi problem. Te dwa główne są _próbkowanie technik_ i _koszt poufnych learning_.

#### <a name="sampling-methods"></a>Metody pobierania próbek
Imbalanced learning polega na użyciu metody służące do modyfikowania szkoleniowy zestaw danych do zestawu danych o zrównoważonym obciążeniu do pobierania próbek. Metody pobierania próbek nie mają być stosowane do zestawu testów. Chociaż istnieje kilka technik próbkowania, większość z nich proste to _losowych minority_ i _w obszarze pobierania próbek_.

_Losowe minority_ polega na wybraniu losowej próbki z klasy mniejszości, replikowanie tych przykładów i dodawanie ich do szkoleniowy zestaw danych. W związku z tym liczba przykłady w klasie mniejszości jest zwiększana, a ostatecznie równomierny podział liczby przykłady różnych klas. Wadą minority to, że wiele wystąpień niektóre przykłady może spowodować klasyfikatory stają się zbyt określonych, co prowadzi do nadmiernego dopasowywania. Model może zawierać szkolenia wysokiej dokładności, ale jej wydajności na danych testowych niewidzianych może być nieoptymalne.

Z drugiej strony _losowych w obszarze pobierania próbek_ jest wybranie losowej próbki z klasy większość i usuwanie tych przykładów szkoleniowy zestaw danych. Jednak usunięcie przykłady z większością klasy, może spowodować klasyfikatora do pominięcia ważnych pojęć odnoszących się do klasy głosów. _Próbkowanie hybrydowego_ gdzie mniejszości klasy jest nadmiernie próbki, a większość klas jest w obszarze próbkowana w tym samym czasie, jest kolejną metodą możliwego do użycia.

Istnieje wiele zaawansowanych technik. Technika wybrany zależy od tego, właściwości danych i wyniki eksperymenty iteracyjne naukowego przetwarzania danych.

#### <a name="cost-sensitive-learning"></a>Koszt learning poufnych
W menedżerów PdM błędy, które stanowią klasy mniejszości są większe znaczenie niż przykładami normalnym. Dlatego koncentruje się głównie na wydajność algorytmu na błędy. Niepoprawnie Prognozowanie dodatnią klasy jako klasę ujemna mogą kosztować więcej niż na odwrót. Ta sytuacja jest często określana jako nierówne utratę lub asymetrycznego koszt źle klasyfikowania elementów różnych klas. Idealne klasyfikatora powinny dostarczyć dokładności prognozy wysokiej za pośrednictwem klasy mniejszości, bez obniżania dokładności dla klasy większość.

Istnieje wiele sposobów umożliwiające osiągnięcie tej równowagi. Aby rozwiązać problemu nierównego utraty, przypisz wysokich kosztów do błędnej klasyfikacji klasy mniejszości i spróbuj zminimalizowanie całkowitych kosztów. Algorytmy, takie jak _SVMs (Obsługa wektor maszyn)_ natury, przyjmuje tej metody, umożliwiając koszt przykłady pozytywne i negatywne, należy określić podczas szkolenia. Podobnie, takich jak zwiększania wyniku metody _wzmocnione drzewa decyzyjne_ zwykle Pokaż dobrą wydajność przy użyciu imbalanced danych.

## <a name="model-evaluation"></a>Ocena modelu
Błędna klasyfikacja to poważny problem program PdM w scenariuszach, gdzie koszt fałszywych alarmów w firmie jest wysoka. Na przykład decyzję o prowadzić do uziemienia danego samolotu, oparte na niepoprawne prognozowania awarii aparatu może zakłócać harmonogramów i planów podróży. Przełączeniem w tryb offline z linii montażowej może prowadzić do utraty przychodów. Dlatego ważne jest oceny modelu za pomocą metryk prawo wydajności w odniesieniu do nowych danych testowych.

Typowe wskaźniki używane do oceny modeli menedżerów PdM omówiono poniżej:

- [Dokładność](https://en.wikipedia.org/wiki/Accuracy_and_precision) jest najbardziej popularnych metrykę używaną do opisywania wydajności klasyfikatora. Ale dokładności jest wrażliwa na dystrybucji danych, jest nieefektywne miary dla scenariuszy z imbalanced zestawami danych. Zamiast tego używane są inne metryki. Narzędzia takie jak [macierz pomyłek](https://en.wikipedia.org/wiki/Confusion_matrix) są używane do obliczeń i poprawić dokładność modelu.
- [Dokładność](https://en.wikipedia.org/wiki/Precision_and_recall) z menedżerów PdM modeli odnoszą się do szybkości fałszywych alarmów. Niższe dokładności modelu zazwyczaj odpowiada wyższy stopień fałszywych alarmów.
- [Odwołaj](https://en.wikipedia.org/wiki/Precision_and_recall) współczynnik wskazuje, jak wiele błędów w zestawie testów zostały poprawnie zidentyfikowane przez model. Większe wycofaniu oznaczają, że modelu zakończy się w identyfikacji prawdziwe niepowodzenia.
- [Wynik F1](https://en.wikipedia.org/wiki/F1_score) harmoniczne średnią dokładności i wycofaniu z jego wartość z zakresu od 0 (najgorsze) do 1 (najlepiej).

Aby uzyskać Klasyfikacja binarna
- [Odbiornik operacyjnego krzywych (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) jest również popularnych metryki. Podczas krzywych ROC wydajności modelu jest interpretowany w oparciu o jeden Stałoprzecinkowy operacyjne na ROC.
- Ale w przypadku problemów z menedżerów PdM _tabel decile_ i _lift wykresy_ są bardziej szczegółowy. Skoncentruj się tylko na klasie dodatnią (błędy) i zapewnia bardziej złożonych obraz wydajności algorytm niż krzywych ROC.
  - _Tabele decile_ są tworzone przy użyciu przykładów testu malejącej prawdopodobieństwa niepowodzeń. Uporządkowany przykłady są następnie grupowane w deciles (10% próbek za pomocą największe prawdopodobieństwo, a następnie 20%, 30% i tak dalej). /(Random baseline) stosunek (true dodatnich) dla każdego decile pomaga oszacować wydajność algorytm w każdym decile. Losowy punkt odniesienia przyjmuje wartości 0.1, 0.2 i tak dalej.
  - [Przenoszenie wykresy](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) decile true dodatnich i losowe dodatnich wartość true dla wszystkich deciles wykresu. Pierwszy deciles koncentrują się na zwykle wyników, ponieważ pokazują największy wzrost. Pierwszy deciles również są widoczne jako przedstawiciela "zagrożony", gdy jest używana dla menedżerów PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Model operacjonalizacji pod kątem konserwacji predykcyjnej

Korzyść do nauki o danych w tym ćwiczeniu zrealizowane, tylko uczonego modelu nawiązaniem operacyjnej. Oznacza to należy wdrożyć model do systemów biznesowych w celu prognozowania na podstawie danych nowy, wcześniej niewidzianych.  Nowe dane musi być zgodna dokładnie z _podpisu modelu_ trenowanego modelu na dwa sposoby:
- wszystkie funkcje musi być obecny w każdym wystąpieniu logiczne (np. wiersz w tabeli) nowych danych.
- nowe dane muszą zostać wstępnie przetworzone, a każda z tych funkcji zaprojektowany w taki sam sposób, jak dane szkoleniowe.

Proces powyżej podano na wiele sposobów w literaturze akademickich i branży. Jednak poniższe instrukcje oznaczają to samo:
- _Oceniać nowe dane_ przy użyciu modelu
- _Zastosowanie modelu_ do nowych danych
- _Operacjonalizowanie_ modelu
- _Wdrażanie_ modelu
- _Uruchomić model_ odniesieniu do nowych danych

Jak wspomniano wcześniej, operacjonalizacja modelu dla menedżerów PdM różni się od jego elementów równorzędnych. Implementowanie scenariuszy obejmujących wykrywanie anomalii i wykrywanie awarii zazwyczaj _online oceniania_ (nazywane również _oceniania w czasie rzeczywistym_). Tutaj, model _wyniki_ każdy rekord przychodzących i zwraca prognozę. Wykrywanie anomalii prognozowania jest wskazanie, że wystąpił anomalii (przykład: SVM jednej klasy). Do wykrywania awarii byłoby, typ lub klasa awarii.

Z kolei obejmuje menedżerów PdM _wsadowe ocenianie przez_. Aby jest zgodny z podpisu modelu, funkcji w nowych danych muszą zostać uwzględnione w taki sam sposób, jak dane szkoleniowe. Dla dużych zestawach danych, które są typowe dla nowych danych funkcje są agregowane przez czas systemu windows i ocenione w ramach usługi batch. Wsadowe ocenianie przez odbywa się zwykle w systemach rozproszonych, takich jak [Spark](https://spark.apache.org/) lub [usługi Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Istnieje kilka rozwiązań alternatywnych — zarówno nieoptymalne:
- Aparatów danych przesyłania strumieniowego obsługuje agregacji za pośrednictwem systemu windows w pamięci. Dlatego można utrzymywał, czy obsługują one oceniania online. Jednak te systemy są odpowiednie na potrzeby gęstą dane w oknach wąskie czasu lub rozrzedzone elementów za pośrednictwem szersze systemu windows. One może nie skalować dobrze sprawdza się w danych gęstą szersze czas systemu windows, jak pokazano w scenariuszach menedżerów PdM.
- Jeśli wsadowe ocenianie nie jest dostępna, rozwiązanie jest dostosowanie oceniania online do obsługi nowych danych w małych partiach w danym momencie.

## <a name="solution-templates-for-predictive-maintenance"></a>Szablony rozwiązań do konserwacji predykcyjnej

Końcowej części tego przewodnika zawiera listę szablonów rozwiązań menedżerów PdM, samouczki i eksperymentów zastosowanych w systemie Azure. Do subskrypcji platformy Azure w ciągu kilku minut, w niektórych przypadkach można wdrożyć te aplikacje menedżerów PdM. Mogą one służyć jako weryfikacji koncepcji demonstracji, piaskownice do eksperymentowania z alternatywnych lub akceleratory dla implementacji rzeczywistej produkcji. Te szablony są zlokalizowane w [galerii Azure AI](https://gallery.azure.ai) lub [Azure w witrynie GitHub](https://github.com/Azure). Te przykłady różnych zostanie zawarta w tym szablonie rozwiązania wraz z upływem czasu.

| # | Tytuł | Opis |
|--:|:------|-------------|
| 2 | [Szablon rozwiązania konserwacji predykcyjnej platformy Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Szablon rozwiązania typu "open source" demonstrujący modelowanie Azure ML i kompletną infrastrukturę platformy Azure, która umożliwia obsługę predykcyjnych scenariuszy konserwacji w kontekście monitorowania zdalnego IoT. |
| 3 | [Uczenie głębokie dla konserwacji predykcyjnej](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Notesu platformy Azure za pomocą rozwiązania wersji demonstracyjnej, z użyciem sieci LSTM (Long krótkoterminowe pamięci) (klasa sieci neuronowych) pod kątem konserwacji predykcyjnej [wpis w blogu w tym przykładzie](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Podręcznik modelowania konserwacji predykcyjnej w języku R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Podręcznik modelowania menedżerów PdM za pomocą skryptów w języku R.|
| 5 | [Usługa Azure Konserwacja zapobiegawcza na potrzeby lotnictwa i Kosmonautyki](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Jedna z pierwszym szablony rozwiązań program PdM w oparciu o uczenie Maszynowe Azure w wersji 1.0 samolotu konserwacji. Ten przewodnik pochodziły z tego projektu. |
| 6 | [Zestaw narzędzi sztucznej Inteligencji platformy Azure dla IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI w IoT Edge przy użyciu TensorFlow; Pakiet Toolkit udostępnia modele uczenia głębokiego w Azure IoT Edge zgodnych kontenerach platformy Docker i uwidacznia te modele jako interfejsy API REST.
| 7 | [Usługa Azure IoT do konserwacji predykcyjnej](https://github.com/Azure/azure-iot-predictive-maintenance) | Pakiet Azure IoT Suite komputerów — wstępnie skonfigurowanego rozwiązania. Szablon menedżerów PdM konserwacji samolotu pakietu IoT. [Innym dokumencie](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) i [wskazówki](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) związane z tym samym projekcie. |
| 8 | [Szablon konserwacji predykcyjnej przy użyciu usługi R Services programu SQL Server](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Pokaz scenariusza użytkowania pozostałe opartych na usługach R. |
| 9 | [Podręcznik modelowania konserwacji predykcyjnej](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funkcja zestawu danych konserwacji w zakresie samolotu zaprojektowany przy użyciu języka R z [eksperymentów](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) i [zestawów danych](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) i [notesu platformy Azure](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) i [eksperymentów](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)w wersji 1.0 usługi Azure ml|

## <a name="training-resources-for-predictive-maintenance"></a>Zasoby szkoleniowe pod kątem konserwacji predykcyjnej

Platforma Microsoft Azure oferuje ścieżki szkoleniowe dla podstawowe pojęcia dotyczące technik menedżerów PdM, oprócz zawartości i szkolenie ogólne pojęcia sztucznej Inteligencji i rozwiązaniem.

| Zasobów szkoleniowych  | Dostępność |
|:-------------------|--------------|
| [Ścieżka szkoleniowa dla menedżerów PdM przy użyciu drzewa i lasu Random](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Publiczne | 
| [Ścieżka szkoleniowa dla menedżerów PdM korzystające z uczenia głębokiego](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Publiczne |
| [Deweloper sztucznej Inteligencji na platformie Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Publiczne |
| [Służbowe Microsoft AI](https://aischool.microsoft.com/learning-paths) | Publiczne |
| [Learning sztucznej Inteligencji platformy Azure z usługi GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Publiczne |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Publiczne |
| [Microsoft AI YouTube seminaria internetowe](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Publiczne |
| [Pokaż sztucznej Inteligencji firmy Microsoft](https://channel9.msdn.com/Shows/AI-Show) | Publiczne |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partnerzy |
| [Microsoft Partner Network](https://learningportal.microsoft.com) | Partnerzy |

Ponadto bezpłatne COURSE (ogromnych otwartych kursów online) na sztucznej Inteligencji są oferowane w tryb online przez instytucji akademickich, takich jak Stanforda i MIT i innych firm edukacyjnych.
