---
title: Azure Podręcznikowym AI rozwiązań konserwacji predykcyjnej | Dokumentacja firmy Microsoft
description: Kompleksowy opis analizy danych, obsługującego konserwacji predykcyjnej rozwiązań w wielu branżach pionowy.
services: cortana-analytics
documentationcenter: ''
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: fboylu
ms.openlocfilehash: ad06617fb8c14928dca7d9ce18ad86190e8255fe
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="azure-ai-playbook-for-predictive-maintenance-solutions"></a>Azure Podręcznikowym AI konserwacji predykcyjnej rozwiązań 
## <a name="executive-summary"></a>Streszczenie
Konserwacji predykcyjnej jest jednym z najbardziej żądane aplikacje analizy predykcyjnej z unarguable korzyści, w tym bardzo dużą oszczędność kosztów. Tego podręcznika dotyczącego jest zapewnienie odwołania dla rozwiązań konserwacji predykcyjnej ze szczególnym uwzględnieniem przypadki użycia głównych.
Jest przygotowany do udzielenia czytnik zrozumienia najbardziej typowych scenariuszy biznesowych konserwacji predykcyjnej, wyzwania kwalifikujących się problemów biznesowych dla takich rozwiązań dane wymagane do rozwiązywania tych problemów biznesowych, predykcyjnej modelowania technik w celu tworzenia rozwiązań przy użyciu tych danych i najlepszych praktyk z architektury rozwiązania próbki.
Opisano również szczegółowe informacje na temat modeli predykcyjnych rozwinięte, takich jak funkcja zespołu inżynieryjnego, model rozwoju i wyników oceny. W zasadzie tego podręcznika dotyczącego zgromadzono działalności biznesowej i analitycznego wskazówki potrzebne do pomyślnego projektowania i wdrażania rozwiązań konserwacji predykcyjnej. Niniejsze wytyczne są przygotowane do pomocy odbiorców, tworzenie początkowej rozwiązania przy użyciu pakietu Cortana Intelligence Suite i specjalnie uczenia maszynowego Azure jako punktu wyjścia w ich długoterminowej strategii konserwacji predykcyjnej. Dokumentację dotyczącą pakietu Cortana Intelligence Suite i Azure Machine Learning znajdują się w [Cortana Analytics](http://www.microsoft.com/server-cloud/cortana-analytics-suite/overview.aspx) i [usługi Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) stron.

> [!TIP]
> Techniczne przewodnik dotyczący wdrażania ten szablon rozwiązania, zobacz [podręcznik techniczny do szablonu Cortana analizy rozwiązania dla konserwacji predykcyjnej](cortana-analytics-technical-guide-predictive-maintenance.md).
> Aby pobrać diagramu, który zawiera omówienie architektury tego szablonu, zobacz [architektura szablon Cortana analizy rozwiązania dla konserwacji predykcyjnej](cortana-analytics-architecture-predictive-maintenance.md).
> 
> 

## <a name="playbook-overview-and-target-audience"></a>Podręcznika dotyczącego omówienie i docelowych odbiorców.
Tego podręcznika dotyczącego jest zorganizowana korzyści zarówno pomoc techniczna i nietechniczna odbiorców z różnymi tła i udziałami w miejscu do konserwacji predykcyjnej. Podręcznika dotyczącego obejmuje zarówno aspektach wysokiego poziomu dotyczących różnych rodzajów rozwiązań konserwacji predykcyjnej oraz szczegóły dotyczące ich wdrażania. Zawartość jest rozmieszczana umożliwiający obsługę zarówno do odbiorców, którzy są tylko chcący zrozumieć obszar rozwiązania i typu aplikacji, a także tych, którzy chcą zaimplementować te rozwiązania i dlatego planuje się szczegóły techniczne.

Większość zawartości tego podręcznika dotyczącego nie zakłada się poprzednich danych nauki wiedzy i doświadczenia. Jednak niektóre części podręcznika dotyczącego wymaga nieco znajomość pojęcia nauki danych, aby można było wykonać szczegóły implementacji. Wstępne danych na poziomie umiejętności nauki są wymagane do w pełni skorzystać z materiału w tych sekcjach.

Pierwszej połowy obejmuje podręcznika dotyczącego wprowadzenie do konserwacji predykcyjnej aplikacji, jak na kwalifikować się konserwacji predykcyjnej rozwiązania, Kolekcja typowe przypadki użycia z szczegóły problem biznesowy dane otaczającego je używać przypadki i korzyści biznesowe stosowania tych rozwiązań konserwacji predykcyjnej. Sekcje te nie wymagają żadnych wiedzy technicznej w domenie analizy predykcyjnej.

W drugiej połowie podręcznikowym możemy obejmują typy technik modelowania predykcyjnego konserwacji predykcyjnej aplikacji i sposobu wdrażania tych modeli poprzez przykłady z przypadki użycia opisane w pierwszej części podręcznika dotyczącego. Jest to zilustrowane, przechodząc kroki wstępnego przetwarzania danych, takie jak etykietowanie danych i funkcji inżynierii, model najlepszych rozwiązań oceny wyboru, szkolenia/testowania i wydajności. Sekcje te są odpowiednie dla specjalistów.

## <a name="predictive-maintenance-in-iot"></a>Konserwacji predykcyjnej w IoT
Wpływ urządzeń nieplanowanych przestojów może być bardzo destrukcyjnego dla firm. Jest szczególnie ważne, aby zachować uruchomiona w celu zmaksymalizowania wykorzystania i wydajności sprzętu pola, a minimalizując kosztowne, niezaplanowanego przestoju. Oczekiwanie na błąd występuje nie jest po prostu, ekonomiczny w scenie operacje biznesowe współczesnych. Zachowanie konkurencyjności, firm wyszukać nowe sposoby zmaksymalizować wydajność zasobów poprzez wykorzystanie danych zbieranych z różnych kanałów. Jest ważne sposób analizowania takie informacje mogą korzystać z predykcyjnej techniki analityczne, które użycie wzorców historycznych w celu przewidywania przyszłych rezultatów. Jedną z najbardziej popularnych tych rozwiązań jest nazywany konserwacji predykcyjnej, który zazwyczaj może być zdefiniowany jako między innymi w najbliższej przyszłości przewidywania ryzyko wystąpienia awarii zasobów, aby zasoby mogą być monitorowane na aktywne identyfikowanie błędów i podjęcie działań przed wystąpią błędy. Tych rozwiązań wykrywania awarii wzorce ustalenie zasobów znajdujących się na największe ryzyko awarii. To wczesne identyfikację problemów ułatwia wdrażanie konserwacji ograniczone zasoby w sposób bardziej ekonomiczne rozwiązanie i zwiększyć jakość i procesy łańcucha dostaw.

Z wzrostu aplikacji Internetu rzeczy (IoT) predykcyjnej konserwacji zostały uzyskania zwiększa uwagi w branży zbierania danych i przetwarzania technologii dojrzewania za mało, aby wygenerować przesyłać, przechowywania i analizowania danych w plikach wsadowych lub w czasie rzeczywistym oraz wszelkiego rodzaju. Takich technologii Włącz ułatwiający projektowanie i wdrażania rozwiązań na trasie z rozwiązaniami zaawansowane metody analizy, z rozwiązaniami konserwacji predykcyjnej, zapewniając raczej największych korzyści.

Problemy biznesowe w zakresie domeny konserwacji predykcyjnej duże ryzyko operacyjne z powodu nieoczekiwanych awarii i ograniczona wgląd w głównej przyczyny problemów w złożonych środowiskach biznesowych. Większość tych problemów można podzielić podlega na następujące pytania biznesowe:

* Jakie jest prawdopodobieństwo, że urządzenie w najbliższej przyszłości zakończy się niepowodzeniem?
* Co to jest pozostałych użytkowania urządzenia?
* Jakie są przyczyny niepowodzenia, i jakie akcje konserwacji należy wykonać, aby rozwiązać te problemy?

Wykorzystując konserwacji predykcyjnej odpowiedzi na te pytania można firm:

* Ograniczyć ryzyko operacyjne związane i zwiększyć stopę zwrotu na zasoby o analizie błędy przed ich wystąpieniem
* Zmniejsz niepotrzebnych oparte na czasie konserwacyjnych i kontrolować koszty konserwacji
* Poprawa ogólny obraz marki, wyeliminować zły reklamy i wynikowy utracone sprzedaży z ścierania klienta.
* Niższe koszty spisu, zmniejszając poziomy spisu przez punkt zmiany kolejności
* Odnajdywanie wzorce podłączony do różnych problemów konserwacji

Rozwiązania konserwacji predykcyjnej można podać firmach z kluczowych wskaźników wydajności takich jak oceny kondycji do monitorowania stanu zasobów w czasie rzeczywistym, szacunkową żywotność pozostałe zasoby, zalecenia dotyczące działań aktywnej konserwacji oraz szacowany kolejność daty wymianę części.

## <a name="qualification-criteria-for-predictive-maintenance"></a>Kryteria kwalifikacyjne dla konserwacji predykcyjnej
Jest ważne podkreślić, że nie wszystkie przypadki użycia i problemów biznesowych, które może być skutecznie rozwiązany przez konserwacji predykcyjnej. Ważne kryteria kwalifikacyjne obejmują, czy problem dotyczy predykcyjnej charakter, czy istnieje Wyczyść ścieżkę akcji, aby zapobiec błędom w przypadku wykrycia uprzednio i najważniejsze, dane z jakością wystarczające do obsługi przypadek użycia są dostępne. W tym miejscu możemy skupić się na wymagania dotyczące danych umożliwiające tworzenie rozwiązań pomyślne konserwacji predykcyjnej.

Podczas budowania modeli predykcyjnych, używamy danych historycznych w celu nauczenia modelu, który można następnie rozpoznać ukryte wzorców i dalsze zidentyfikować te wzorce w przyszłości danych. Te modele są uczone z przykładami opisanego przez ich funkcje i docelowe prognozowania. Trenowanego modelu powinien tworzenia prognoz w celu analizując tylko funkcje nowe przykłady. Jest niezwykle ważny, że model przechwytywania relacji między funkcje i element docelowy prognozowania. W celu nauczenia skuteczne maszyny uczenie modelu, potrzebujemy dane szkoleniowe, która obejmuje funkcje mające rzeczywistości predykcyjne zasilania kierunku docelowy prognozowania, co oznacza, że dane powinny mieć zastosowanie w celu prognozowania oczekiwać dokładne prognoz.

Na przykład jeśli element docelowy do prognozowania błędów koła pociągu, dane szkoleniowe powinien zawierać kółka funkcjach (np. danych telemetrycznych odzwierciedlające stan kondycji koła przebiegu, obciążenia samochodu, itp.). Jednak w przypadku obiektu docelowego do prognozowania błędów aparatu pociągu, prawdopodobnie potrzebujemy inny zestaw danych szkoleniowych funkcje związane z aparatu. Przed zbudowaniem modeli predykcyjnych, oczekujemy biznesowe ekspertów zrozumieć wymagania dokładność danych i zapewnienia wiedzy domeny, które są potrzebne, aby wybrać odpowiednie podzbiór danych do analizy.

Istnieją trzy źródła istotnych danych, które szukamy podczas kwalifikujących się problem biznesowy, może być rozwiązanie do konserwacji predykcyjnej:

1. Historia błąd: Zazwyczaj w aplikacjach konserwacji predykcyjnej zdarzenia błędów są bardzo rzadko. Jednak podczas budowania modeli predykcyjnych, które prognozowania awarii, algorytm wymaga dowiedzieć się, ze wzorcem normalnego działania, a także wzorzec awarii w procesie szkolenia. W związku z tym istotne jest, że dane szkoleniowe zawiera wystarczającą liczbę przykłady w obu kategorii, aby dowiedzieć się tych dwóch różnych wzorców. Z tego powodu wymagamy, czy dane mają wystarczającą liczbę zdarzeń błędów. Zdarzenia błędów można znaleźć w rekordach konserwacji i historii zastąpienie części lub anomalii w danych szkoleniowych mogą służyć jako błędy określonych przez ekspertów domeny.
2. Historia konserwacji lub naprawy: Istotne źródło danych dla rozwiązań konserwacji predykcyjnej jest historii szczegółowe konserwacji zasobów zawierających informacje o składnikach zastąpione, przeprowadzenia profilaktycznej konserwacji aktywuje wykonywane itp. Jest bardzo ważne, aby przechwytywać tych zdarzeń jako wpływu tych degradacji i błędne wyniki powoduje, że te informacje są niedostępne.
3. Warunki maszyny: Aby można było przewidzieć, ile dni (godziny, mil, transakcji itp.) maszynie trwa przed jej nie powiedzie się, przyjęto założenie, że stan kondycji na komputerze powoduje spadek wraz z upływem czasu podczas jego działania. W związku z tym oczekujemy dane zawierają funkcje różnym czasie przechwytywania tego wzorca przedawnienia i wszelkich nieprawidłowości prowadzi do pogorszenia się. W aplikacjach IoT dane telemetryczne z różnych czujników reprezentują dobrym przykładem. W celu prognozowania, jeśli maszyna będzie awarii w przedziale czasu, najlepiej powinien przechwytywania danych poniżające trendu w tym czasie przed zdarzeniem rzeczywisty błąd.

Ponadto wymagane dane, które jest bezpośrednio powiązana z warunkach zasobów docelowych przewidywania. Wybór elementu docelowego opiera się na obu dostępności potrzeb i danych biznesowych. Biorąc prognozowania train kółka awarii na przykład, firma Microsoft może prognozowania "czy pokrętło będzie mieć awarii" czy "cały pociąg będzie mieć błąd". Pierwsza z nich dotyczy więcej określonego składnika, natomiast drugi dotyczy błąd pociągu. Drugim jest bardziej ogólne pytanie, które wymaga rozproszone znacznie więcej elementów danych niż pierwsza z nich, dzięki czemu trudniejsze do tworzenia modelu. Z drugiej strony w trakcie prognozowania błędów kółka tylko analizując dane warunku train wysokiego poziomu mogą nie być możliwe nie zawiera informacji na poziomie składnika. Ogólnie rzecz biorąc jest bardziej rozsądnego do prognozowania zdarzeń określony błąd niż bardziej ogólne z nich.

Jeden często zadawane pytania, który zazwyczaj jest proszony o dane historii awarii jest "jak wiele zdarzeń niepowodzenia są wymagane do uczenia modelu i uważa się za"wystarczająco"? Nie jest jasne odpowiedzi na to pytanie, jak w przypadku wielu scenariuszy analizy predykcyjnej, zazwyczaj jest to jakości danych, które nakazują, co jest akceptowalne. Jeśli zestaw danych nie zawiera funkcje, które mają zastosowanie do prognozowania awarii, następnie nawet w przypadku wielu zdarzeń niepowodzenia Budowanie modelu dobrej nie można. Jednak zasadą jest więcej awarii zdarzenia jest lepsze modelu i oszacowanie liczby awarii przykłady są wymagane bardzo kontekstu i zależne od danych miary. Ten problem omówiono w sekcji obsługi imbalanced zestawów danych, gdzie Sugerujemy metody radzenia sobie z problemem z nie ma wystarczającej liczby błędów.

## <a name="sample-use-cases"></a>Przykładowe przypadki użycia
W tej sekcji koncentruje się na kolekcję przypadki użycia konserwacji predykcyjnej z branży kilka takich jak Aerospace, narzędzia i transportu. Każdy podsekcji bardziej szczegółowy do przypadków użycia zbieranych z tych obszarów i omówiono problem biznesowy, dane otaczającego problem biznesowy i zalet rozwiązanie do konserwacji predykcyjnej.

### <a name="aerospace"></a>Aerospace
#### <a name="use-case-1-flight-delay-and-cancellations"></a>Przypadek użycia 1: Opóźnienie transmitowane i anulowania
##### <a name="business-problem-and-data-sources"></a>*Źródła problemu i danych biznesowych*
Jeden z problemów dużej firmy, które airlines krojów jest znacznych kosztów, które są skojarzone z lotach jest opóźnione z powodu problemów mechaniczne. Nie można naprawić błędy mechaniczne, lotach nawet może być anulowane. Jest to bardzo kosztowna opóźnienia tworzyć problemy w planowania i operacje, zły reputacji przyczyny i niezadowolenie klienta oraz wiele innych problemów. Airlines interesuje szczególnie przewidywania takie błędy mechaniczne z wyprzedzeniem, aby zmniejszyć opóźnienia transmitowane lub anulowania. Celem rozwiązanie do konserwacji predykcyjnej dla tych przypadków jest do prognozowania prawdopodobieństwo samolotu opóźnione lub anulowane, oparte na źródeł danych, takich jak konserwacji historii i przesyłane informacje o trasie. Dwóch źródeł danych głównych dla tego przypadku użycia są transmitowane etapy i dzienniki strony. Przesyłane dane etap obejmuje dane dotyczące szczegóły trasy transmitowane, takie jak Data i godzina wyjścia i przyjęcia, wyjścia i przyjęcia lotniskach itp. Dane dziennika strony zawiera szereg kody błędów i konserwacji, które są rejestrowane przez personel konserwacji.

##### <a name="business-value-of-the-predictive-model"></a>*Wartość biznesowa model predykcyjny*
Przy użyciu dostępnych danych historycznych, model predykcyjny został utworzony, za pomocą algorytmu wielu klasyfikacji do prognozowania typ mechaniczne problemu, co powoduje opóźnienia lub anulowania lot w ciągu 24 godzin. Dzięki tym prognozowania niezbędne prac konserwacyjnych może zostać pobrany zmniejsza ryzyko, gdy samolotu jest obsługiwany, i w związku z tym zapobiegające możliwych opóźnień lub anulowania. Przy użyciu usługi sieci web Azure Machine Learning, modeli predykcyjnych bezproblemowo i łatwo można zintegrować airlines istniejącymi platformami działania. 

#### <a name="use-case-2-aircraft-component-failure"></a>Przypadek użycia 2: Błąd składnika powietrznego
##### <a name="business-problem-and-data-sources"></a>*Źródła problemu i danych biznesowych*
Aparaty powietrznego są bardzo poufne i kosztowne części urządzeń i zamiany części aparatu są spośród najbardziej typowych zadań konserwacji w branży linii lotniczych. Obsługa rozwiązania dla linii lotniczych wymagają staranne zarządzanie dostępność podstawowych składników, dostarczania i planowania. Możliwość zebrania analizy na niezawodność składnika prowadzi do znacznego obniżenia kosztów inwestycji. Źródło danych głównych dla tego przypadku użycia jest dane telemetryczne zbierane z liczbą czujników w powietrznego informacjami pod warunkiem powietrznego. Rekordy konserwacji zostały również używane do identyfikacji czasu wystąpienia błędów składników i w zamian.

##### <a name="business-value-of-the-predictive-model"></a>Wartość biznesowa model predykcyjny
Model klasyfikacji wielu klas został utworzony, który prognozuje prawdopodobieństwo wystąpienia awarii z powodu niektórych składnika w następnym miesiącu. Zastosowanie tych rozwiązań, airlines pozwala zmniejszyć koszty napraw składnika, zwiększyć dostępność podstawowych składników, obniżenie poziomu spisu zasobów powiązanych i skrócić planowanie konserwacji.

### <a name="utilities"></a>Usługi użyteczności publicznej
#### <a name="use-case-1-atm-cash-dispense-failure"></a>Przypadek użycia 1: Niepowodzenie zwolnienie gotówkowych ATM
##### <a name="business-problem-and-data-sources"></a>*Źródła problemu i danych biznesowych*
Członkowie kadry kierowniczej w branży znacznym zasobów często stanu to podstawowy ryzyko operacyjne ich firm nieoczekiwanych awarii ich zasobów. Na przykład awaria maszyna przykład bankomatami w branży bankowości jest bardzo powszechny problem, który często występuje. Tego rodzaju problemów tworzenie rozwiązań konserwacji predykcyjnej pożądana dla operatorów takich maszyn. W tym przypadku użycia prognozowania problem jest obliczyć prawdopodobieństwo, że ATM gotówkowych wycofanie transakcji zostanie przerwane z powodu błędu rozdzielacz gotówkowych, takie jak papier lub awaria części. Główne źródła danych dla tego przypadku są odczyty czujników, które zbierają pomiarów podczas uwagi gotówkowych są są zbędne, a także konserwacji rekordów zbierane wraz z upływem czasu. Dane czujników uwzględnione odczyty czujników za każdą transakcję ukończone, a także odczyty czujników na każdym Uwaga pominięte. Pomiary odczyty podane czujnik takich jak luki pomiędzy uwagi, grubość, należy pamiętać, odległość przyjęcia itp. Dane konserwacji uwzględniane informacje naprawy i kody błędów. Zostały one używane do identyfikowania przypadków awarii.

##### <a name="business-value-of-the-predictive-model"></a>*Wartość biznesowa model predykcyjny*
Dwa modele predykcyjne zostały zaprojektowane w celu prognozowania awarii w transakcjach pieniężnych wycofania i niepowodzeń w poszczególnych dodatkowej pominięte podczas transakcji. Dzięki dostępowi do prognozowania błędów transakcji wcześniej bankomatami może być obsługiwany aktywnego aby zapobiec wystąpieniu błędów. Ponadto z prognozowania awarii należy pamiętać, jeśli transakcja jest mogą nie działać prawidłowo, przed ukończeniem z powodu notatki zwolnienie awarii, najlepiej zatrzymać proces i ostrzeganie klienta dla transakcji niekompletne zamiast oczekiwania na usługę konserwacji odebranie po wystąpieniu błędu, który może prowadzić do większych niezadowolenie klienta.

#### <a name="use-case-2-wind-turbine-failures"></a>Przypadek użycia 2: Błędy turbiny knie
##### <a name="business-problem-and-data-sources"></a>*Źródła problemu i danych biznesowych*
Zgłoś środowiska świadomości turbin knie stały się jeden z głównych źródeł generacji energii i zwykle cenie milionów dolarów. Jednym z kluczowych składników turbin knie jest silnika generator wyposażony z wielu czujników, które pomaga monitorować warunki turbiny i stanu. Odczyty czujników zawierają cenne informacje, które mogą służyć do tworzenia modelu predykcyjnego do prognozowania krytyczne kluczowych wskaźników wydajności (KPI), takie jak Średni czas do awarii składników turbiny knie. Dane dla tego przypadku użycia pochodzą z wielu turbin knie znajdujące się w trzech miejscach różnych farmy. Pomiary z bliski czujników STU z każdego turbiny zarejestrowano co 10 sekund, przez jeden rok. Te odczyty obejmują pomiarów, takie jak temperatury, generator szybkości, turbiny zasilania i generator rozwiązania.

##### <a name="business-value-of-the-predictive-model"></a>*Wartość biznesowa model predykcyjny*
Modeli predykcyjnych zostały zaprojektowane w celu oszacowania pozostałych użytkowania generatory i czujnikami temperatury. Przez prawdopodobieństwo wystąpienia błędu, techników konserwacji można skupić się na podejrzane turbin, które mogą nie działać prawidłowo wkrótce uzupełnienie systemów opartych na czas konserwacji.
Ponadto modeli predykcyjnych Przełącz wgląd do poziomu wsparcia dla różnych czynników do prawdopodobieństwo wystąpienia awarii, co ułatwia firmę w celu lepszego zrozumienia głównej przyczyny problemów.

#### <a name="use-case-3-circuit-breaker-failures"></a>Przypadek użycia 3: błędy wyłącznika
##### <a name="business-problem-and-data-sources"></a>*Źródła problemu i danych biznesowych*
Energia elektryczna i gaz operacje, które zawierają generowania, dystrybucji i sprzedaży energii elektrycznej wymagają znaczną ilość konserwacji upewnij się, że wiersze zasilania działają przez cały czas, aby zagwarantować dostarczania energii dla gospodarstw domowych. Niepowodzenie takich operacji jest szczególnie ważne, ponieważ prawie każdej jednostki dokonuje problemów związanych z zasilaniem w regionach, które występują. Obwód podziałów są krytyczne dla takich operacji, znajdują się urządzenie, które Wytnij elektrycznego bieżącej w razie problemów i obwody krótkiej, aby zapobiec szkody w wierszach zasilania. Problem biznesowy dla tego przypadku użycia jest do prognozowania błędów wyłącznika danej obsługi dzienników, historia poleceń i techniczne.

Trzy główne źródła danych dla tego przypadku są dzienniki konserwacji, które obejmują akcje naprawcze, zapobiegawczych oraz systematyczne, danych operacyjnych, które zawiera polecenia automatycznej i ręcznej przesyłają podziałów obwód, takie jak akcje otwierających i zamykających i specyfikacja techniczna dane dotyczące właściwości każdego wyłącznika, np. rok wprowadzono, lokalizacji, modelu,... itd.

##### <a name="business-value-of-the-predictive-model"></a>*Wartość biznesowa model predykcyjny*
Konserwacji predykcyjnej rozwiązania pomagają zmniejszyć naprawy koszty i zwiększyć cykl życia urządzenia, takie jak podziałów obwodu. Te modele także zwiększyć jakość sieci zasilania ponieważ modele udostępniają ostrzeżenia wcześniejsze zmniejszających nieoczekiwanych błędów, które prowadzą do mniej przerwy w usłudze.

#### <a name="use-case-4-elevator-door-failures"></a>Przypadek użycia 4: Błędy drzwi krótka
##### <a name="business-problem-and-data-sources"></a>*Źródła problemu i danych biznesowych*
Najbardziej dużym firmom krótka zwykle mają miliony windy uruchomiony na całym świecie. Uzyskanie przewagi konkurencyjnej one skupić się na niezawodność, czyli przyjazny klientom. Rysowanie na temat możliwości Internetu rzeczy, łącząc ich windy w chmurze i zbieranie danych z czujników krótka i systemów, są w stanie przekształcania danych do analizy biznesowej cenne, co znacznie zwiększa operacji oferując predykcyjnych i cenią sobie wcześniejsze konserwacji, który nie jest element co jest jeszcze dostępna konkurencji. Wymagania biznesowe w tym przypadku jest zapewnienie aplikacji predykcyjnej bazy wiedzy knowledge base, który prognozuje powoduje potencjalnych awarii drzwi. Wymagane dane dla tej implementacji składa się z trzech części, które są funkcje statyczne krótka (np. identyfikatory kontraktu częstotliwość konserwacji, typ budynku, itp.), informacje na temat wykorzystania (np. liczbę cykli drzwi drzwi Średni czas zamknięcia, itp.) i historię awarii (tj. historycznych awarii rekordów i ich przyczyn).

Model wieloklasowej Regresja logistyczna został zbudowany przy użyciu usługi Azure Machine Learning, aby rozwiązać ten problem prognozowania z zintegrowane funkcje statyczne i dane użycia jako funkcje oraz przyczyny niepowodzenia historycznych rekordów jako klasa etykiety. Ten model predykcyjny jest używany przez aplikację na urządzeniu przenośnym, używany przez techników pola, aby poprawić wydajność pracy. Gdy technik przechodzi w lokacji, aby naprawić krótka, on mogą odwoływać się do tej aplikacji dla zalecane przyczyny i najlepsze przebieg prac konserwacyjnych naprawić drzwi krótka tak szybko, jak to możliwe.

### <a name="transportation-and-logistics"></a>Transport i logistyki
#### <a name="use-case-1-brake-disc-failures"></a>Przypadek użycia 1: Awarie dysku hamulca
##### <a name="business-problem-and-data-sources"></a>*Źródła problemu i danych biznesowych*
Zasady konserwacji typowe pojazdów obejmują naprawczych i przeprowadzenia profilaktycznej konserwacji. Naprawcze konserwacji oznacza to, że po wystąpił błąd, co może spowodować pociągnięcie niedogodności do sterownika w wyniku nieoczekiwanego usterka naprawiana jest mechanizm marnuje czas na odwiedzin mechanika. Większość pojazdów również podlegają zasad przeprowadzenia profilaktycznej konserwacji, który wymaga wykonywania niektórych inspekcji na harmonogram, który nie uwzględnia rzeczywisty stan podsystemów samochodu. Żaden z tych metod nie jest pomyślnie w pełni eliminując problemy. Przypadek użycia określonego w tym miejscu jest prognozowanie awarii dysku hamulca na podstawie danych zebranych przez Czujniki zainstalowane w systemie opona samochodu, która przechowuje informacje o historycznych schematy i inne warunki, które samochód jest narażony na. Najważniejsze źródła danych dla tej sprawy jest dane czujników, które mierzą, na przykład przyspieszenia hamowania wzorce zwiększają odległości, prędkości itp. Te informacje są połączone z innych statyczne informacje takie jak funkcji samochodu kompilacji pomocy jest dobrą zbiór zmienne predykcyjne, które mogą być używane w modelu predykcyjnego. Dane awarii, który jest wywnioskowany na podstawie bazy danych części zamówienia (używany do przechowywania dat zamówienia części i liczby jako samochodów są aktualnie obsługiwanych w przedstawicielstw handlowych) jest inny zestaw ważne informacje.

##### <a name="business-value-of-the-predictive-model"></a>*Wartość biznesowa model predykcyjny*
Wartość biznesowa tutaj predykcyjnej podejście jest istotne. System konserwacji predykcyjnej można zaplanować odwiedzin pośrednik oparte na modelu predykcyjnego. Model może bazować na sensorycznej informacje, które jest reprezentujący bieżący stan samochód i kierowania historii. Tej metody można ograniczyć ryzyko nieoczekiwanych błędów, które może również wystąpić przed następnej okresowej konserwacji.
Pomaga również zmniejszyć ilość niepotrzebnych przeprowadzenia profilaktycznej konserwacji. Sterownik może być aktywne poinformowany, czy zmiany części może być konieczne w kilka tygodni i podaj pośrednik tych informacji. Pośrednik można następnie przygotowanie poszczególnych obsługi pakietu sterownika z wyprzedzeniem.

#### <a name="use-case-2-subway-train-door-failures"></a>Przypadek użycia 2: Całość train drzwi błędów
##### <a name="business-problem-and-data-sources"></a>*Źródła problemu i danych biznesowych*
Jednym z głównych powodów opóźnienia i problemy na operacje całość jest błędów drzwi samochodów pociągu. Przewidywanie Jeśli samochodu train może być awaria drzwi lub możliwość prognozy liczbę dni do następnego door awarii, jest bardzo ważne prognozowania. Zapewnia możliwość optymalizacji obsługi drzwi train i ograniczyć czas przestoju pociągu.

#### <a name="data-sources"></a>Źródła danych
Są trzy źródeł danych w przypadku użycia 

* **dane zdarzeń pociągu**, czyli historycznych rekordów zdarzeń pociągu 
* **dane dotyczące obsługi** typów konserwacji, typów kolejność pracy i kody priorytet  
* **rejestruje błędy**.

##### <a name="business-value-of-the-predictive-model"></a>*Wartość biznesowa model predykcyjny*
Dwa modele zostały zaprojektowane w celu prognozowania następnego dnia błąd prawdopodobieństwo przy użyciu klasyfikacji binarnej i dni do awarii przy użyciu regresji. Podobnie jak w starszych przypadków, tworzenie modeli ogromne możliwość poprawy jakości usług i bardziej zadowoleni, uzupełniając systemów regularnej konserwacji.

## <a name="data-preparation"></a>Przygotowywanie danych
### <a name="data-sources"></a>Źródła danych
Wspólne elementy danych dla problemów konserwacji predykcyjnej można podsumować w następujący sposób:

* Błąd historii: historię awarii komputera lub składnika na maszynie.
* Historia obsługi: Historia naprawy maszyny, np. kody błędów, poprzednie konserwacja działania lub elementy zastępcze składnika.
* Komputera warunki i użycia: warunki pracy maszyny np. danych zbieranych z czujników.
* Funkcje maszyny: funkcje maszyny, np. aparat rozmiar, producent i model, lokalizacji.
* Funkcje operatora: funkcje operatora, np. płci, poza środowisko.

Jest to możliwe i zazwyczaj przypadek, że Historia błąd znajduje się w historii konserwacji, takich jak w postaci kody błędów specjalnych lub dat zamówienia części. W takich przypadkach błędów można wyodrębnić danych konserwacji. Ponadto służbowych domen mogą mieć różnych innych źródeł danych, które wpływ awarii wzorców, które nie są wymienione w tym miejscu wyczerpujący. Te powinny być określone podczas tworzenia modeli predykcyjnych, sprawdzając ekspertów domeny.

Przykładowe powyżej elementy danych z przypadki użycia to:

Błąd historii: lotu opóźnienie daty, dat błąd składnika powietrznego i typy, ATM gotówkowych wycofanie transakcji błędów, błędów drzwi train/krótka, hamulca dysk zastępczy kolejność daty, knie turbiny awarii dat i niepowodzenia polecenia wyłącznika.

Historia obsługi: transmitowane dzienniki błędów, dzienniki błędów transakcji ATM, uczenia rekordów konserwacji, w tym typ obsługi, krótki opis itp. i wyłącznika konserwacji rekordów.

Komputera warunki i użycia: kieruje transmitowane i razy, dane czujników zebrane aparaty powietrznego, odczyty czujników z transakcji ATM pociągu dane zdarzenia, odczyty czujników z turbin knie, windy i połączone samochodów.

Funkcje maszyny: wyłącznika specyfikacji technicznych, takich jak poziomy napięcia, używanie funkcji geolokalizacji lub samochodu funkcje, takie jak marki, modelu, aparat rozmiar, typy opona zakładzie produkcyjnym itp.

Podana powyżej źródeł danych, dwa typy danych głównych obserwować w domenie konserwacji predykcyjnej są danych czasowych i danych statycznych.
Historia awarii, warunki maszyny, napraw prawie zawsze historii użycia, historii dostarczanych wraz z sygnaturą czasową wskazująca czas pobierania dla każdego elementu danych. Funkcje maszyny i operatora ogólnie są statyczne ponieważ zazwyczaj opisują techniczne maszyny lub właściwości operatora. Istnieje możliwość, że funkcje te zmiany w czasie i jeśli tak powinny być traktowane jako źródła danych z sygnaturą czasową.

### <a name="merging-data-sources"></a>Scalanie źródeł danych
Przed przejściem do dowolnego typu engineering funkcji lub etykietowania procesu, należy najpierw przygotować danych w formularzu wymagane do utworzenia funkcje programu. Ostatecznym celem jest generowanie rekord dla każdej jednostki czasu dla każdego zasobu z jego funkcji i etykiety być wprowadzone do Algorytm uczenia maszynowego. Aby przygotować oczyszczania ostatecznego zestawu danych, niektóre kroki przetwarzania wstępnego należy podjąć. Pierwszym krokiem jest dzielona wartość czasu trwania zbierania danych, w której każdy rekord należy do jednostki czasu dla zasobu jednostki czasu. Zbieranie danych również można podzielić na inne jednostki, takie jak akcje, jednak dla uproszczenia używamy jednostki czasu dla pozostałej wyjaśnienia.

Jednostka miary dla czasu może być sekundy, minuty, godziny, dni, miesiące, cykle, mile lub transakcji w zależności od wydajności Przygotowanie danych i zmiany obserwowanych w warunkach zasobów z jednostkę czasu, albo inne czynniki określonej domenie. Innymi słowy jednostki czasu nie ma być taka sama jak częstotliwość zbierania danych w wielu przypadkach dane mogą nie pokazywać różnic z jednej jednostki do drugiego. Na przykład jeśli wartości temperatury były zbierane co 10 sekund, pobrania jednostkę czasu 10 sekund dla całej analizy nadyma wiele przykładów bez konieczności podawania dodatkowych informacji. Strategia lepiej jest używany średnia ponad godzinę jako przykład.

Przykład danych typu ogólnego schematy dla źródeł danych wyjaśniono w we wcześniejszej sekcji są:

Rejestruje konserwacji: są to rekordy konserwacji akcje wykonywane. Dane pierwotne konserwacji zwykle zawiera identyfikator zasobów i sygnaturę czasową z informacjami o jakie działania obsługi mogły zostać wykonane w tym czasie. W przypadku takich danych pierwotnych działania obsługi konieczne można przetłumaczyć podzielone na kategorie kolumn z każdej kategorii odpowiadającego typowi akcja konserwacji. Schemat danych podstawowych dla rekordów konserwacji będzie zawierać kolumny akcji identyfikator, czas i konserwacji zasobów.

Rejestruje błąd: są to rekordy, które należą do obiektu docelowego prognozowania, który nazywamy błędów lub z powodu błędu. Mogą to być zdarzenia błędów zdefiniowanych przez warunek firmy lub określonych kodów błędów. W niektórych przypadkach dane obejmują wiele kody błędów, niektóre z nich odpowiadają błędów zainteresowań. Nie wszystkie błędy są docelowy prognozowania, więc inne błędy są zazwyczaj używane do skonstruowania funkcje, które mogą mieć związek z błędami. Schemat danych podstawowych rejestruje błąd to identyfikator zasobów, czas i awarii lub kolumny Przyczyna niepowodzenia Jeśli przyczyna jest dostępna.

Warunki maszyny: są to najlepiej w czasie rzeczywistym danych o warunkach danych monitorowania. Na przykład niepowodzeń drzwi drzwi otwierania i zamykania godziny są dobrym wskaźniki dotyczące bieżący stan drzwi. Schemat danych podstawowych warunków maszyny obejmuje kolumny wartości Identyfikatora, czas i stan zasobów.

Dane maszyny i operator: dane maszyny i operator może zostać scalony jeden schemat do identyfikowania zasobów, które zostało świadczona przez jaki operator wraz z właściwości zasobów i operatora. Na przykład samochodu jest zwykle należących do sterowników z atrybutów, takich jak wiek, zwiększają środowisko itp. W przypadku zmiany tych danych w czasie, te dane powinien również zawierać kolumnę czasu i powinien być traktowany jako zróżnicowanie danych generacji w funkcji czasu. Identyfikator zasobu, funkcje zasobów, identyfikator operator i operator to schemat danych podstawowych warunków maszyny.

Tabela końcowego przed generowania etykietowanie i funkcji mogą być generowane przez dołączenie tabeli Warunki maszyny z rekordów awarii na identyfikator zasobu i polach czasu po lewej. Tej tabeli może następnie być połączony z rekordów konserwacji na identyfikator zasobu i czas pól i na koniec z komputera i operator funkcje na identyfikator zasobu. Pierwszy lewe sprzężenie pozostawia wartości null dla kolumny awarii, gdy komputer jest w normalnych warunkach, te można obciążyć przez wartość wskaźnika do normalnego działania. W tej kolumnie niepowodzenia służy do tworzenia etykiety dla modelu predykcyjnego.

### <a name="feature-engineering"></a>Inżynieria funkcji
Pierwszym etapem modelowania jest funkcja zespołu inżynieryjnego. Funkcja generowania będzie koncepcyjnie opisu i abstrakcyjnej stan kondycji na komputerze w danym momencie przy użyciu danych historycznych, który został zebrany do punktu w czasie. W następnej sekcji firma Microsoft udostępnia przegląd typu techniki, które mogą służyć do konserwacji predykcyjnej i jak etykiety jest wykonywane dla każdego technik. Dokładne techniki, które mają być używane zależy od problem danych i biznesowych. Jednak funkcja metod inżynierii opisanych poniżej może służyć jako linii bazowej do tworzenia funkcji. Poniżej omówiono funkcje opóźnienia, które powinny zostać utworzone na podstawie źródeł danych, które pochodzą z sygnatury czasowe, a także funkcje statyczne utworzone na podstawie źródeł danych statycznych i zawierają przykłady z przypadków użycia.

#### <a name="lag-features"></a>Funkcja Lag funkcji
Jak wspomniano wcześniej, w trybie konserwacji predykcyjnej, dane historyczne zwykle zawiera sygnatury czasowe wskazująca czas pobierania dla każdego elementu danych. Istnieje wiele sposobów tworzenia funkcji z danych, które pochodzą z danymi oznaczony znacznikiem czasowym. W tej sekcji omówiono niektóre z tych metod, używany do konserwacji predykcyjnej. Jednak firma Microsoft nie są ograniczone tych samych metod. Ponieważ funkcja engineering jest traktowana jako kreatywność obszarów modelowania predykcyjnego, może to być wiele innych metod tworzenia funkcji. W tym miejscu firma Microsoft udostępnia pewne ogólne technik.

##### <a name="rolling-aggregates"></a>*Wprowadzanie wartości zagregowanych*
Dla każdego rekordu zasobu możemy wybierz stopniowe okno "W", która jest liczbą jednostki czasu, przez który chcielibyśmy obliczeniowe historycznych agregacji dla rozmiaru. Należy obliczyć stopniowych funkcji agregującej za pomocą kropki W przed datą tego rekordu. Przykładowe wprowadzanie wartości zagregowanych można stopniowych, liczby, oznacza, odchyleń standardowych, oparte na odchylenia standardowego wartości odstających, CUSUM mierzy wartości minimalną i maksymalną dla okna. Innej techniki interesujące jest przechwytywania trend zmiany, wartości szczytowe i poziomu przy użyciu algorytmów wykrywania anomalii w dane przy użyciu algorytmy wykrywania anomalii.

Aby demonstracyjne, zobacz rysunek 1, gdzie możemy reprezentują wartości czujnik zarejestrowane dla zasobu dla każdej jednostki czasu liniami niebieska i oznacz stopniowego obliczania średniej funkcji W = 3 rekordów w t<sub>1</sub> i t<sub>2</sub> są wskazywane przez kolor pomarańczowy i odpowiednio green grupowania.

![Rysunek 1. Wycofanie funkcje agregujące](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png)

Rysunek 1. Wycofanie funkcje agregujące

Jako przykład powietrznego awarii składników, wartości czujnika z ostatniego tygodnia, w ciągu ostatnich trzech dni i ostatni dzień zostały używany do tworzenia oznacza stopniowe, odchylenie standardowe i funkcji sum. Podobnie do błędów ATM, czujnik nieprzetworzonej wartości i wycofanie oznacza medianę, zakres, odchyleń standardowych liczbę wartości odstających poza trzy odchyleń standardowych, górny i dolny CUMSUM funkcji były używane.

Do przewidywania opóźnienie transmitowane liczy błędów kody z ostatniego tygodnia zostały użyte do utworzenia funkcji. Niepowodzeń drzwi train liczby zdarzeń w ostatnim dniu liczby zdarzeń w ciągu ostatnich 2 tygodni i odchylenie liczby zdarzeń ostatnich 15 dni były używane do tworzenia funkcji lag. Zliczanie tego samego została użyta dla zdarzeń dotyczących obsługi.

Ponadto wybierając W, który jest bardzo duże (np. lata), istnieje możliwość przyjrzeć się całej historii zasobów, takich jak inwentaryzacji wszystkich rekordów konserwacji, awarii itp. do czasu rekordu. Ta metoda została użyta do zliczania błędów wyłącznika ostatnich trzech lat. Niepowodzeń pociągu, Utwórz funkcję przechwytywania długoterminowe skutki konserwacji zostały zliczane także wszystkie zdarzenia konserwacji.

##### <a name="tumbling-aggregates"></a>*Agreguje wirowania*
Dla każdego rekordu etykietą środka trwałego, możemy wybierz okno rozmiaru "W -<sub>k</sub>" gdzie k jest liczbą lub windows chcemy, aby utworzyć funkcji lag rozmiar "W". "k" mogą być pobierane jako dużą liczbą przechwytywania długoterminowej degradacji lub małą liczbą przechwytywania krótkoterminowej efekty. Następnie używamy k wirowania systemu windows W -<sub>k</sub> , W -<sub>(k-1)</sub>,..., W -<sub>2</sub> , W -<sub>1</sub> do tworzenia funkcji agregującej okresów przed rekordów Data i godzina (patrz rysunek 2). Te są również stopniowe systemu windows na poziomie rekordu na jednostkę czasu, który nie jest przechwytywany na rysunku 2, ale są takie same jak w rysunek 1 gdzie t<sub>2</sub> służy również do zaprezentowania stopniowego efekt.

![Rysunek 2. Funkcje agregujące wirowania](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png)

Rysunek 2. Funkcje agregujące wirowania

Na przykład turbin knie, W = 1 i k = 3 miesiące użyto w celu tworzenia funkcji lag dla każdego z ostatnich 3 miesięcy, za pomocą górnej i dolnej wartości odstających.

#### <a name="static-features"></a>Funkcje statyczne
Są to technicznych sprzętu, takie jak data produkcji, numer modelu, lokalizacji itp. Funkcji lag są prawie liczbowych charakter, statycznej funkcji zwykle być podzielone na kategorie zmiennych w modelach. Jako przykład wyłącznika właściwości, takie jak napięcia bieżącego i specyfikacje zasilania wraz z typów transformatora źródłami zasilania itd. zostały użyte. Na wypadek awarii dysku hamulca typ opona koła takie tak, jakby były stopu lub stali były używane jako niektóre funkcje statyczne.

Podczas generowania funkcji można wykonać niektóre inne ważne czynności, takie jak obsługa brakujące wartości i normalizacji. Istnieje wiele metod Brak przypisywania wartości, a także normalizacji danych, który nie został opisany w tym miejscu. Jednak jest korzystne możesz wypróbować różne metody czy wzrost wydajności prognozowania jest możliwe.

W tabeli funkcji końcowego po funkcji kroków opisanych w sekcji starszych technicznych powinien wyglądać następujący przykład schemat danych, gdy dzień jest jednostka czasu:

| Identyfikator elementu zawartości | Time | Funkcja kolumn | Etykieta |
| --- | --- | --- | --- |
| 1 |1 dzień | | |
| 1 |Dzień 2 | | |
| Przyciski ... |Przyciski ... | | |
| 2 |1 dzień | | |
| 2 |Dzień 2 | | |
| Przyciski ... |Przyciski ... | | |

## <a name="modeling-techniques"></a>Techniki modelowania
Konserwacji predykcyjnej jest bardzo zaawansowane domeny często wykorzystujących pytania biznesowe, które może być w stanie pod różnymi kątami wiele perspektywy modelowania predykcyjnego. W kolejnych sekcjach udostępniamy głównego techniki, które są używane do modelu pytania biznesowej, które należy znaleźć odpowiedzi z rozwiązaniami konserwacji predykcyjnej. Mimo że istnieją podobieństwa, każdy model ma własny sposób tworzenia etykiety, które zostały szczegółowo opisane w. Jako zasób towarzyszące mogą odwoływać się do szablonu konserwacji predykcyjnej, który znajduje się w eksperymentów przykładowych w usłudze Azure Machine Learning. Łącza do materiałów online dla tego szablonu znajdują się w sekcji zasobów. Widać, jak niektórych metod inżynierii funkcji opisanych wyżej i techniki modelowania, które jest opisane w kolejnych sekcjach są stosowane do prognozowania błędów aparatu powietrznego przy użyciu usługi Azure Machine Learning.

### <a name="binary-classification-for-predictive-maintenance"></a>Klasyfikacji binarnej do konserwacji predykcyjnej
Klasyfikacji binarnej do konserwacji predykcyjnej służy do prognozowania prawdopodobieństwo, że sprzęt nie powiedzie się w przedziale czasu przyszłych. Okres czasu jest określany przez i na podstawie reguł biznesowych i danych wykonywanego. Niektóre typowe okresów to minimalny czas realizacji wymagane do zakupu zamiennych zastąpić prawdopodobnie składniki uszkodzenia lub czas wymagany do wdrażania obsługi zasobów do wykonania procedury obsługi, aby rozwiązać ten problem, który może nastąpić w tym przedziale czasu. Nazywamy tego okresu przyszłych typu horizon "X".

Aby można było używać klasyfikacji binarnej, należy zidentyfikować dwa rodzaje przykłady, które nazywamy dodatnie i ujemne. Każdy przykład jest rekord, który należy do jednostki czasu dla zasobu koncepcyjnie opisujące i abstrakcyjność jego warunków pracy do tej jednostki czasu za pomocą funkcji engineering przy użyciu historycznych i innych źródeł danych z wcześniejszym opisem. W kontekście klasyfikacji binarnej do konserwacji predykcyjnej dodatnią typu oznacza błędów (Etykieta 1) i typ ujemna oznacza normalną pracę (etykiety = 0) gdy etykiety są podzielone na kategorie typu. Celem jest znalezienie modelu, który identyfikuje każdego nowego przykład jako może zakończyć się niepowodzeniem lub nie działać normalnie w ciągu następnych X jednostki czasu.

#### <a name="label-construction"></a>Etykieta konstrukcji
Aby można było utworzyć model predykcyjny udzielenia odpowiedzi na pytanie "jakie jest prawdopodobieństwo, że zasób nie powiedzie się w ciągu następnych X jednostki czasu?", etykietowania odbywa się przez rekordy z argumentami X przed awarią zasobów i etykietowanie je jako "o do odrzucaj" (etykiety = 1) podczas etykietowania wszystkie rekordy "Normal" (etykiety = 0). W przypadku tej metody etykiety są podzielone na kategorie zmienne (patrz rysunek 3).

![Rysunek 3. Etykiety klasyfikacji binarnej](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png)

Rysunek 3. Etykiety klasyfikacji binarnej

Transmitowane opóźnienia i anulowania X jest wybierany jako jeden dzień do prognozowania opóźnienia w ciągu 24 godzin. Wszystkie lotach, które są w ciągu 24 godzin, zanim błędów zostały oznaczone jako wartości 1. Dla gotówkowych ATM zwolnienie błędów, dwa modele klasyfikacji binarnej zostały zaprojektowane na potrzeby prognozowania prawdopodobieństwo błędu transakcji w ciągu następnych 10 minut, a także do prognozowania prawdopodobieństwo wystąpienia błędu w uwagach następne 100 pominięte. Wszystkie transakcje, które wystąpiły w ciągu ostatnich 10 minut błędu są oznaczone jako 1 dla pierwszego modelu. I notatki wszystkie pominięte w ciągu ostatnich 100 informacje o awarii zostały oznaczone jako 1, drugi modelu. Niepowodzeń wyłącznika zadania jest określenie prawdopodobieństwo, że następne polecenie wyłącznika zakończy się niepowodzeniem w których przypadku X jest wybierany jako jedno polecenie przyszłych. Niepowodzeń drzwi train model klasyfikacji binarnej zaprojektowano w celu prognozowania błędy w ciągu 7 dni. Niepowodzeń turbiny knie X została wybrana jako 3 miesiące.

Wiatru turbiny i uczenia drzwi przypadków są również używane do analizy regresji do prognozowania pozostałych użytkowania przy użyciu tych samych danych, ale przy użyciu różnych strategii etykietowania, która znajduje się w następnej sekcji.

### <a name="regression-for-predictive-maintenance"></a>Regresja do konserwacji predykcyjnej
Regresji modele w trybie konserwacji predykcyjnej są używane do obliczenia pozostałego okresu użytkowania (RUL) z zasobów, która jest zdefiniowana jako ilość czasu zasobu działa przed wystąpieniem awarii dalej. Analogicznie jak klasyfikacji binarnej, każdy przykład jest rekord, który należy do jednostki "Y" czasu dla zasobu. Jednak w kontekście regresji celem jest można znaleźć modelu, który oblicza pozostałe użytkowania każdego nowego przykładu jako numer kolejny, który jest czas pozostały przed awarią. Nazywa się tym okresie niektóre wielu Y. Każdy przykład jest również pozostałego okresu użytkowania, który może zostać obliczona mierząc czas pozostały w tym przypadku przed awarią dalej.

#### <a name="label-construction"></a>Etykieta konstrukcji
Podane na pytanie "co to jest pozostałych użytkowania urządzenia?
", etykiet dla modelu regresji można skonstruować przez pobranie wszystkich rekordów przed awarią i etykietowanie ich obliczając, ile jednostki czasu pozostają przed awarią dalej. W przypadku tej metody etykiety są ciągłe, zmiennych (zobacz rysunek 4).

![Rysunek 4. Etykietowania dla regresji](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png)

Rysunek 4. Etykietowania dla regresji

Różni się od klasyfikacji binarnej dla regresji, trwałe bez żadnych błędów w danych nie można użyć do modelowania etykietowania odbywa się w odniesieniu do punktu awarii i jego obliczania nie jest możliwe bez wiedzy o ile trwałego udało przetrwać przed awarią. Ten problem dotyczy najlepiej innej techniki statystyczne o nazwie przetrwania analizy.
Zamierzamy nie omówiono w nim analizy po rzeczach niezbędnych w tego podręcznika dotyczącego ze względu na potencjalne komplikacji, które mogą wystąpić podczas stosowania techniki przypadki użycia konserwacji predykcyjnej obejmujących różne czasu danych za pomocą częstych odstępach czasu.

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasa usługi klasyfikacji konserwacji predykcyjnej
Klasa wielu klasyfikacji konserwacji predykcyjnej może służyć do przewidywania przyszłych rezultatów dwa. Pierwsza z nich jest do przypisywania zasobów do jednego z wielu możliwych okresów czasu powoduje niepowodzenie zakres czasu dla każdego zasobu. Drugim jest do identyfikowania prawdopodobieństwo wystąpienia błędu w przyszłości z jednego z kilku przyczyn. Która umożliwia obsługi technicznej, którzy są wyposażone w wiedzy do obsługi problemów z wyprzedzeniem. Innej techniki modelowania wielu klasy koncentruje się na temat określania najbardziej prawdopodobne przyczyny podane awarii. Dzięki temu zalecenia dla akcje top konserwacji do wykonania, aby naprawić błąd.
Dzięki użyciu listy uporządkowanej według rangi głównego powoduje i powiązane akcje naprawy  
pracownicy mogą być bardziej skuteczne, uwzględniając ich pierwszym akcje naprawy po awarii.

#### <a name="label-construction"></a>Etykieta konstrukcji
Podane dwie kwestie, które są "jakie jest prawdopodobieństwo, że zasób nie powiedzie się w następnej"aZ"jednostki czasu, gdzie""oznacza liczbę okresów" i "jakie jest prawdopodobieństwo, że zasób nie powiedzie się w ciągu następnych X jednostki czasu z powodu problemu" P<sub>i</sub>"gdzie"i"jest liczba możliwych przyczyn, w etykietowania odbywa się w następujący sposób dla tych zasobów w celu technik.

Pierwsze pytanie etykietowania odbywa się przez pobranie rekordów aZ przed awarią zasobów i etykietowanie je przy użyciu przedziały czasu (3Z, 2Z-Z), ponieważ ich etykiety podczas etykietowania wszystkie rekordy "Normal" (etykiety = 0). W przypadku tej metody etykieta jest podzielone na kategorie zmiennej (zobacz rysunek 5).

![Rysunek 5. Etykietowanie wieloklasowej klasyfikacji dla niepowodzenia czasowego](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Rysunek 5. Etykietowanie wieloklasowej klasyfikacji dla niepowodzenia czasowego

Na drugie pytanie etykietowania odbywa się przez rekordy z argumentami X przed awarią zasobów i etykietowanie je jako "o się niepowodzeniem z powodu problemu P<sub>i</sub>" (etykiety = P<sub>i</sub>) podczas etykietowania wszystkie rekordy "Normal" (etykiety = 0). W przypadku tej metody etykiety są podzielone na kategorie zmienne (patrz rysunek 6).

![Rysunek 6. Etykietowanie wieloklasowej klasyfikacji dla głównej przyczyny prognozowania](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Rysunek 6. Etykietowanie wieloklasowej klasyfikacji dla głównej przyczyny prognozowania

Model przypisuje prawdopodobieństwo Niepowodzenie z powodu każdego P<sub>i</sub> oraz prawdopodobieństwo bez błędów. Te prawdopodobieństwa może zostać określona przez wielkości umożliwia prognozowanie problemów, które są najbardziej prawdopodobne w przyszłości. Przypadek użycia błąd składnika powietrznego była struktura jako problem wieloklasowej klasyfikacji. Dzięki temu Prognozowanie prawdopodobieństwa awarii z powodu dwóch różnych wykorzystania zawory w następnym miesiącu.

Rekomendowanie prac konserwacyjnych po awarii, etykietowania nie trzeba przyszłych typu horizon do pobrania. Jest to spowodowane modelu nie jest prognozowanie błędu w przyszłości, ale go po prostu jest prognozowanie z najbardziej prawdopodobną przyczyną głównego po awarii została już wykonana. Krótka drzwi błędów można podzielić na trzeci przypadek, której celem jest do prognozowania przyczynę błędu podane dane historyczne dotyczące warunków roboczych. Ten model jest następnie używany do prognozowania najprawdopodobniej głównego powoduje po wystąpił błąd. Jednego klucza zaletą tego modelu jest, że pomaga niedoświadczonych pracownikom łatwe diagnozowanie i naprawianie problemów, które musieliby lat. doświadczenia.

## <a name="training-validation-and-testing-methods-in-predictive-maintenance"></a>Szkolenie, weryfikacji i metod testowych w konserwacji predykcyjnej
W konserwacji predykcyjnej, podobnie jak inne miejsca rozwiązania, zawierający dane oznaczony znacznikiem czasowym typowe uczenie i testowanie procedury należy wziąć pod uwagę aspekty różnych czasu, aby lepiej generalize w niewidocznym przyszłych danych.

### <a name="cross-validation"></a>Krzyżowe sprawdzanie poprawności
Wiele algorytmów uczenia maszynowego zależą od liczba hyperparameters, który znacznie zmienić działania modelu. Optymalne wartości hyperparameters te nie są automatycznie obliczane podczas uczenia modelu, ale powinien być określony przez naukowca danych. Istnieje kilka metod odnajdywania dobre wartości hyperparameters. Najczęściej jest "składanie k krzyżowe sprawdzanie poprawności", która dzieli przykłady losowo na złożeń "k". Dla każdego zestawu wartości hyperparameters Algorytm uczenia jest czas wykonywania k. W każdej iteracji w przykładach w bieżącym złożenia są używane jako zestaw sprawdzania poprawności, pozostałe przykłady są używane jako zestaw szkoleniowy. Algorytm pociągu za pośrednictwem przykłady szkolenia i metryki wydajności są obliczane przez przykłady sprawdzania poprawności. Na końcu pętla dla każdego zestawu wartości hyperparameter możemy obliczyć średnią z wartości metryki wydajności k, a następnie wybierz hyperparameter wartości, które mają najlepszą wydajność średnia.

Jak wspomniano wcześniej, problemy konserwacji predykcyjnej, dane są rejestrowane jako szeregu czasowego zdarzenia, które pochodzą z wielu źródeł danych. Te rekordy może zostać określona zgodnie z chwili etykietowania przykład lub rekordu. W związku z tym jeśli możemy podzielić zestawu danych losowo szkolenia i sprawdzanie poprawności zestawu, niektóre przykłady szkolenia są później w czasie niż niektóre przykłady sprawdzania poprawności. Powoduje to szacowanie przyszłych wydajności hyperparameter wartości na podstawie danych, które dotarły przed został uczenia modelu. Te uzyskać szacunkowe wartości może być nadmiernie optymistyczne, zwłaszcza, jeśli Szeregi czasowe nie są stacjonarnych i zmiany ich zachowania w czasie. W związku z tym może być nieoptymalnych hyperparameter wybranych wartości.

Lepszy sposób znajdowania dobre wartości hyperparameters jest podzielona w przykładach szkolenia i weryfikacja w sposób zależnych od czasu taki sposób, że wszystkie przykłady sprawdzania poprawności są później w czasie niż wszystkie przykłady szkolenia. Następnie dla każdego zestawu wartości hyperparameters szkolenia w zakresie algorytmu na zestaw szkoleniowy, mierzymy wydajności modelu przez ten sam zestaw sprawdzania poprawności i wybierz hyperparameter wartości, które zawierają najlepszą wydajność. Gdy dane szeregów czasowych nie jest stacjonarnych i rozwoju wraz z upływem czasu wartości hyperparameter wybierany przez train/weryfikacji podzielić doprowadzić do przyszłych lepsze "modelu wydajność w porównaniu z losowo wybranym przez krzyżowego sprawdzania poprawności wartości.

Ostatecznego modelu jest generowany przez szkolenia Algorytm uczenia przez cały danych przy użyciu najlepszych wartości hyperparameter, które zostały znalezione za pomocą szkolenia/weryfikacji podział lub krzyżowego sprawdzania poprawności.

### <a name="testing-for-model-performance"></a>Testowanie pod kątem wydajności modelu
Po utworzeniu modelu musimy oszacować jego przyszłych wydajności na nowych danych. Najprostsza szacowania można wydajności modelu danych szkoleniowych. Jednak ta szacowana jest nadmiernie optymistyczne, ponieważ model jest dostosowane do danych, które jest używane do oszacowania wydajności. Lepiej oszacować można metryki wydajności wartości hyperparameter obliczanego w zestawie weryfikacji lub metryki wydajności średni obliczana na podstawie krzyżowego sprawdzania poprawności. Ale tej samej przyczyny jak wcześniej wspomniano, są nadal zbyt optymistycznej te uzyskać szacunkowe wartości. Potrzebujemy więcej realistyczne podejścia do pomiaru wydajności modelu.

Jednym ze sposobów jest losowo podzielić dane do szkolenia, weryfikacji i testowania zestawów. Zestawy szkoleniowe i weryfikacja są używane do wybierania wartości hyperparameters i uczenia modelu z nimi. Wydajność modelu jest mierzony w zestawie testów.

Innym sposobem, które odnoszą się do konserwacji predykcyjnej jest podzielona w przykładach, sprawdzanie poprawności zestawy szkoleniowe i testowe w sposób zależnych od czasu taki sposób, że wszystkie przykłady testu są później w czasie niż wszystkie przykłady szkolenia i sprawdzania poprawności. Po podziału model pomiaru generowania i wydajności są wykonywane taki sam, jak opisano wcześniej.

Gdy stacjonarnych i łatwe do prognozowania szeregu czasowego obu podejść Generowanie podobne ocen działalności w przyszłości. Ale w przypadku stacjonarnych z systemem innym niż i/lub trudna do przewidzenia szeregów czasowych drugiej metody wygeneruje bardziej realistyczne oszacowania wydajności przyszłych niż pierwsza z nich.

### <a name="time-dependent-split"></a>Podziel zależnych od czasu
Najlepszym rozwiązaniem w tej sekcji możemy zająć bliższe spojrzenie na sposób podziału zależnych od czasu wykonania. Przedstawione zależnych od czasu dwukierunkowej podziału między zestawy szkoleniowe i testowe, jednak dokładnie tej samej logiki powinny być stosowane dla czasu zależne podziału dla zestawów szkolenia i sprawdzania poprawności.

Załóżmy, że mamy strumienia zdarzeń oznaczony znacznikiem czasowym, takich jak pomiarów z różnych czujników. Funkcje szkolenia i przykłady testu, a także ich etykiety są definiowane przez przedziały czasu, która zawiera wiele zdarzeń.
Na przykład dla klasyfikacji binarnej, zgodnie z opisem w sekcji Engineering funkcji oraz technik modelowania, funkcje są tworzone na podstawie ostatnich zdarzeń i etykiety są tworzone w oparciu o przyszłych zdarzeń w "jednostkach czasu w przyszłości X". W związku z tym etykietowania przedział czasu przykładzie pochodzi później następnie przedział czasu jego funkcje. Podziału zależnych od czasu firma Microsoft wybierz punkt w czasie, w którym firma Microsoft uczenia modelu z Zaczekaj hyperparameters przy użyciu danych historycznych do punktu. Aby uniknąć wycieków przyszłych etykiety, które wykraczają poza odcięty szkolenia w danych szkoleniowych, wybrany najnowszy przedział czasu do przykładów szkoleniowych etykiety jako X jednostki przed datą końcową szkolenia. Na rysunku 7 każdego pełne kółko reprezentuje wiersz w zestawie danych końcowego funkcji, które funkcje i etykiety są obliczane zgodnie z metodą opisane powyżej. Podano, na ilustracji przedstawiono rekordy, które powinny przejść do celów szkoleniowych i testów zestawów podczas implementowania podziału zależnych od czasu X = 2, a W = 3:

![Rysunek 7. Podziel zależnych od czasu dla klasyfikacji binarnej](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png)

Rysunek 7. Podziel zależnych od czasu dla klasyfikacji binarnej

Zielony kwadratów reprezentują rekordów należących do jednostki czasu, w których można użyć do trenowania. Zgodnie z opisem, każdy przykład szkolenia w tabeli funkcji końcowego jest generowany przez patrzeć poza 3 okresy dla funkcji generowania i 2 okresy przyszłych na etykietach zanim odcięty dzień szkolenia. Firma Microsoft nie używaj przykłady w szkolenia ustawić, gdy część 2 przyszłych okresów, w tym przypadku jest poza odcięty szkolenia, ponieważ przyjęto założenie, że nie ma widoczność poza odcięty szkolenia. Z powodu tego ograniczenia czarny przykłady stanowią rekordy końcowego etykietą zestawu danych, które nie mają być używane w zestawie danych szkoleniowych. Te rekordy nie będą używane podczas testowania albo danych, ponieważ są one zanim odcięty szkolenia i ich etykietowania przedziały czasu zależą od częściowo szkolenia przedział czasu, który nie powinien być wielkość liter jak chcielibyśmy całkowicie różnych etykietowania przedziały czasu, dla celów szkoleniowych i testów, aby uniknąć wycieków informacji etykiety.

Ta technika pozwala zakładki na dane używane na potrzeby generowania funkcji między celów szkoleniowych i testów przykłady, które wkrótce odcięty szkolenia. W zależności od dostępności danych można osiągnąć nawet poważniejsze separacji nie używając dowolnej przykładów w zestawie testów, które są W jednostkach czasu odcięty szkolenia.

W naszym pracy znaleziono modeli regresji używane do prognozowania pozostałych użytkowania więcej poważny wpływ na problem wycieku, a przy użyciu losowego podziału prowadzi do extreme overfitting. Podobnie problemy regresji podziału powinien być taki sposób, że rekordy należące do zasoby z błędami przed szkolenia odcięcia ma być używane jako zestaw szkoleniowy i zasoby z błędami po odcięcia powinna być używana do testowania zestawu.

Jako głównej metody innego ważne najlepsze rozwiązanie dotyczące podziału danych uczenie i testowanie jest użycie podziału, według Identyfikatora zasobu tak, aby żaden z zasobów, które były używane w szkolenia są używane do testowania, ponieważ testowania będzie upewnij się, że w przypadku nowych zasobów do tworzenia prognoz w modelu zawiera realistyczne wyników.

### <a name="handling-imbalanced-data"></a>Obsługa danych imbalanced
Problemy z klasyfikacji Jeśli istnieje więcej przykładów dotyczących jednej klasy niż inne, danych jest określany jako imbalanced. Najlepiej, jeśli chcemy mieć wystarczającej ilości przedstawicielom każda klasa w dane szkoleniowe, aby można było do rozróżniania różnych klas. Jedną klasę jest mniejsza niż 10% danych, możemy powiedzieć, że dane są imbalanced i nazywamy klasy mniejszości underrepresented dataset. Zróżnicowany w wielu przypadkach znaleźliśmy imbalanced zestawów danych gdy jedna klasa jest znacznie underrepresented w porównaniu do innych osób na przykład przez tylko stanowiące 0,001% punktów danych. Nierównowaga klasy jest to problem w wielu domenach w tym wykrywania oszustw, nieautoryzowanego dostępu w sieci i konserwacji predykcyjnej gdzie błędy są zazwyczaj rzadkich wystąpień w okresie istnienia zasoby, które tworzą przykłady klas mniejszości.

W przypadku nierównowaga klasy wydajności większość standardowych algorytmów uczenia zostanie naruszony, zgodnie z ich mają na celu zminimalizowania ogólną współczynnik błędów. Na przykład dla zestawu danych z przykłady klas ujemna 99% i przykłady klas dodatnią 1%, firma Microsoft można uzyskać dokładność 99% po prostu etykietowania wszystkie wystąpienia jako ujemne. Jednak to klasyfikuje wszystkie przykłady dodatnią, więc algorytm nie jest elementem przydatne, mimo że metryka dokładność jest bardzo duże. W rezultacie metryki oceny z konwencjonalnej, takich jak dokładności na współczynnik błędów nie są wystarczające w przypadku imbalanced learning. Innych metryk, takich jak dokładność, odwołanie, wyniki F1 i koszt skorygowaną krzywych ROC służą do oceny w przypadku imbalanced zestawów danych, która została szczegółowo opisana w sekcji metryki oceny.

Istnieją jednak niektórych metod, które pomagają rozwiązać klasy nierównowaga problem. Te dwie główne są próbkowania technik i kosztów learning poufnych.

#### <a name="sampling-methods"></a>Metody pobierania próbek
Stosowanie próbek metod w uczeniu imbalanced składa się z modyfikacji zestawu danych przez niektórych mechanizmów w celu zapewnienia zrównoważonym zestawu danych. Istnieje wiele różnych technik, większość z nich bezpośrednio do przodu są losowe oversampling i w obszarze pobierania próbek.

Po prostu nie stwierdzono, losowe oversampling wybiera losowej próbki z klasy mniejszości, replikowanie tych przykładów i dodawanie ich do szkoleniowy zestaw danych. Zwiększa się liczba całkowita przykłady w klasie mniejszości, a ostatecznie równowagę między liczbą przykłady różnych klas. Jeden niebezpieczeństwo oversampling jest czy wiele wystąpień niektóre przykłady może spowodować klasyfikatora stać się zbyt określonych, co może prowadzić do overfitting.
Spowodowałoby to szkolenia wysokiej dokładności, ale wydajność w niewidocznym testowanie danych może być bardzo niską. Z drugiej strony losowe w obszarze pobierania próbek jest wybranie losowej próbki z większością klasy i usuwanie tych przykładów szkoleniowy zestaw danych. Jednak usunięcie przykłady z klasy większość może spowodować klasyfikatora ważnych pojęć odnoszących się do większości klasy przeoczenie. Hybrydowe próbkowania, gdzie jest próbkowanie klasy mniejszości, a większość klasy jest w próbce w tym samym czasie jest innym rozwiązaniem działało. Brak dostępnych jest wiele innych bardziej zaawansowanych technik oraz metody próbkowania skuteczne nierównowaga klasy jest obszar popularnych research odbieranie stałej uwagi i wkładów z wielu kanałów. Korzystanie z różnych technik podjęcie decyzji na najbardziej efektywne te zwykle pozostało naukowca danych do badania i wypróbować i wysoce zależą od właściwości danych. Ponadto jest ważne, aby upewnić się, że metody pobierania próbek są stosowane tylko do szkolenia ustawić, ale nie testu ustawiona.

#### <a name="cost-sensitive-learning"></a>Koszt learning poufne
W konserwacji predykcyjnej, błędów, które stanowią klasy mniejszości są większe znaczenie niż normalne przykłady i w związku z tym koncentruje się na wydajności algorytmu na awarie jest zwykle fokus. To jest często określana jako nierówne utracie lub asymetrycznego kosztów misclassifying elementy różnych klas, w którym koszt niepoprawnie przewidywania dodatnią negatywne więcej niż na odwrót. Żądany klasyfikatora należy udzielić dokładności prognozy wysokiej za pośrednictwem klasy mniejszości, bez naruszania poważnie dokładności dla większości klasy.

Istnieje kilka metod, które można to osiągnąć. Przypisywanie wysokiego kosztu do błędów klasyfikacji klasy mniejszości, a próby zminimalizować całkowity koszt, problem nierównej utraci można skutecznie rozpatrywane. Niektóre algorytmów uczenia maszynowego za pomocą tego pomysłu z założenia, takich jak SVMs (Obsługa wektor maszyny) gdzie wartości dodatnie i ujemne przykłady można włączyć w czasie szkolenia. Podobnie zwiększania wyniku metody są używane i zwykle Pokaż dobrą wydajność w przypadku decyzji imbalanced danych, takich jak boosted algorytmy drzewa.

## <a name="evaluation-metrics"></a>Metryki oceny
Jak wspomniano wcześniej, nierównowaga klasy powoduje, że pogorszenie wydajności jako algorytmy mają tendencję do klasyfikowania przykłady klas większość lepsze w wydatków przypadków klasy mniejszości, błąd całkowita liczba błędów klasyfikacji znacznie zwiększona większość klasy jest poprawnie z etykietą. To spowoduje, że odwołania niskiej szybkości i staje się problem większe, gdy koszt fałszywe alarmy biznesową jest bardzo duże. Dokładność jest najbardziej popularnym Metryka opisujących wydajności klasyfikatora programu. Jednak zgodnie z powyższymi wskazówkami dokładność jest nieefektywne i nie uwzględnia rzeczywistą wydajność funkcji klasyfikatora programu, ponieważ jest on bardzo poufne podziału danych. Zamiast tego innych metryk oceny są używane do oceny imbalanced learning problemów. W takich przypadkach precyzja, odwoływania i F1 oceny należy początkowych metryk, aby przyjrzeć się podczas oceny wydajności modelu konserwacji predykcyjnej. W konserwacji predykcyjnej stawki odwołania określa liczbę błędów w zestawie testów zostały poprawnie zidentyfikowane przez model. Większe odwołania oznaczają, że modelu zakończy się pomyślnie w Przechwytywanie true błędów. Metryka dokładności odnosi się do częstotliwość fałszywe alarmy, których niższe stawki dokładności odpowiadają wyższej fałszywe alarmy. Wynik F1 uwzględnia zarówno dokładność i odwołania z najlepszym wartością jest 1, a najgorszych 0.

Ponadto w przypadku klasyfikacji binarnej, decile tabel i przyrostu wykresy są bardzo szczegółowych podczas oceny wydajności. One skupienie się na klasie dodatnią (błędy) i zapewnić bardziej złożonych obraz algorithm wydajności niż co jest widoczne, analizując właśnie stałego punktu działania na krzywą ROC (odbiornika operacyjnego cech).
Tabele decile są uzyskiwane przez kolejność przykłady testu zgodnie z ich przewidywane prawdopodobieństwa błędów obliczone przez model przed rozgraniczania zdecydować się na etykiecie końcowego. Przykłady uporządkowanej następnie są umieszczane w deciles (tj. Przykłady 10% z największych prawdopodobieństwa, a następnie 20%, 30% i tak dalej). Przez obliczenie stosunek true dodatnia liczba każdego decile jego losowe linii bazowej (np. 0,1, 0,2..) jeden oszacować, jak zmiany wydajności algorytm w każdym decile. Wykresy przyrostu są używane do kreślenia wartości decile przez decile szybkość dodatnią wartość true, a pary losowe szybkość dodatnią wartość true dla wszystkich deciles kreślenia. Zazwyczaj pierwszy deciles koncentrują się na wyniki od tutaj przedstawiono największych korzyści. Pierwszy deciles znajdują się również jako reprezentatywny dla "zagrożony", gdy jest używany do konserwacji predykcyjnej.

## <a name="sample-solution-architecture"></a>Przykładowa Architektura rozwiązania
Wdrażając rozwiązanie do konserwacji predykcyjnej Dbamy o kompleksowe rozwiązanie, które oferuje ciągłego cykl wprowadzanie danych, Magazyn danych dla modelu uczenia, funkcja generowania, prognozowania i wizualizacja wyników wraz z alert generowania mechanizmu, takich jak pulpit nawigacyjny monitorowania zasobu. Chcemy potok danych, który zapewnia wgląd w przyszłości do użytkownika w sposób ciągły automatycznych. Na rysunku 8 poniżej przedstawiono przykład architektura konserwacji predykcyjnej takie IoT potoku danych. W architekturze w czasie rzeczywistym dane telemetryczne są zbierane w Centrum zdarzeń, w której są przechowywane dane przesyłane strumieniowo. Te dane są pozyskanych przez usługi stream analytics w czasie rzeczywistym przetwarzania danych, takich jak funkcja generowania. Funkcje są następnie używane do wywoływania usługi sieci web model predykcyjny i wyniki są wyświetlane na pulpicie nawigacyjnym. W tym samym czasie pozyskiwane dane są także przechowywane w bazie danych historycznych i scalany z zewnętrznych źródeł danych przez takie jak danych lokalnych podstawowych do utworzenia szkolenia przykłady modelowania.
Tym samym hurtowni danych może służyć do wsadowego oceniania przykładów i przechowywania wyników, które mogą być ponownie używane do sporządzanie raportów predykcyjnej na pulpicie nawigacyjnym.

![Rysunek 8. Architektura rozwiązania przykład konserwacji predykcyjnej](./media/cortana-analytics-playbook-predictive-maintenance/example-solution-architecture-for-predictive-maintenance.png)

Rysunek 8. Architektura rozwiązania przykład konserwacji predykcyjnej

Aby uzyskać więcej informacji o poszczególnych składników architektury zapoznaj się [Azure](https://azure.microsoft.com/) dokumentacji.

