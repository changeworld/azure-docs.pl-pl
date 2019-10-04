---
title: 'Samouczek: Eksplorowanie Azure Time Series Insights biblioteki klienckiej JavaScript | Microsoft Docs'
description: Samouczek, aby dowiedzieć się więcej na temat biblioteki klienta Azure Time Series Insights JavaScript i powiązanego modelu programowania.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: a5184b49f4608acebbe6bf4734dde99857d16fc9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845254"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Samouczek: Eksplorowanie Azure Time Series Insights biblioteki klienckiej JavaScript

Biblioteka klienta Azure Time Series Insights oparta na języku JavaScript D3 została opracowana w celu ułatwienia deweloperom sieci Web zapytań i wizualizacji danych przechowywanych w Time Series Insights. Ten samouczek przeprowadzi Cię przez Time Series Insights bibliotekę klienta i model programowania przy użyciu hostowanej przykładowej aplikacji.

Samouczek zawiera szczegółowe informacje na temat sposobu pracy z biblioteką, uzyskiwania dostępu do danych Time Series Insights i używania formantów wykresu do renderowania i wizualizacji danych. Dowiesz się również, jak eksperymentować z różnymi rodzajami wykresów, aby wizualizować dane. Na końcu samouczka będziesz mieć możliwość używania biblioteki klienckiej do dołączania funkcji Time Series Insights do własnej aplikacji sieci Web.

Zapoznaj się z tematem:

> [!div class="checklist"]
> * Przykładowa aplikacja Time Series Insights
> * Biblioteka klienta Time Series Insights JavaScript
> * Jak aplikacja Przykładowa używa biblioteki do wizualizacji danych Time Series Insights

> [!NOTE]
> * Samouczek używa bezpłatnego, hostowanego [demonstracji Time Series Insights sieci Web](https://insights.timeseries.azure.com/clientsample).
> * Przykładowe pliki źródłowe aplikacji Time Series Insights są udostępniane w [przykładowym repozytorium GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Przeczytaj [dokumentację dotyczącą programu Time Series Insights Client Reference](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

Utwórz konto [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek używa funkcji **Narzędzia deweloperskie** przeglądarki. Nowoczesne przeglądarki sieci Web ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)i inne) zwykle zapewniają dostęp do **widoku inspektora sieci Web** za pomocą klawisza F12 na klawiaturze. Innym sposobem uzyskania dostępu do widoku jest kliknięcie prawym przyciskiem myszy na stronie sieci Web, a następnie wybranie polecenia **Zbadaj element**.

## <a name="review-video"></a>Przejrzyj wideo

W tym filmie wideo wprowadzamy zestaw SDK języka JavaScript Time Series Insights Open Source:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="sample-application"></a>Przykładowa aplikacja

W tym samouczku korzystamy z bezpłatnej, hostowanej Time Series Insights przykładowej aplikacji do eksplorowania kodu źródłowego w ramach aplikacji oraz do eksplorowania Time Series Insights biblioteki klienckiej JavaScript. Korzystając z przykładowej aplikacji, dowiesz się, jak korzystać z Time Series Insights w języku JavaScript i wizualizować dane za pomocą wykresów i grafów.

1. Przejdź do [Time Series Insights przykładowej aplikacji](https://insights.timeseries.azure.com/clientsample). Zostanie wyświetlony następujący monit logowania:

   [Przykładowy monit logowania klienta usługi @no__t 1Time Series Insights](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Wybierz pozycję **Zaloguj** się, aby wprowadzić lub wybrać swoje poświadczenia. Użyj konta organizacji przedsiębiorstwa (Azure Active Directory) lub konta osobistego (konto Microsoft).

   [monit dotyczący przykładowych poświadczeń klienta @no__t 1Time Series Insights](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Po zalogowaniu zostanie wyświetlona strona zawierająca wykresy wypełnione danymi Time Series Insights. Twoje konto użytkownika i opcja **Wyloguj** są widoczne w prawym górnym rogu:

   [Przykładowa Strona główna klienta usługi 1Time Series Insights po zalogowaniu @no__t](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Źródło i struktura strony

Najpierw wyświetlmy [kod źródłowy HTML i JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) renderowanej strony sieci Web:

1. Otwórz **Narzędzia deweloperskie** w przeglądarce. Sprawdź elementy HTML, które tworzą bieżącą stronę (znaną również jako drzewo HTML lub DOM).

1. Rozwiń elementy `<head>` i `<body>` i obserwuj następujące sekcje:

   * W elemencie `<head>` znajdują się metadane strony i zależności, które umożliwiają uruchomienie aplikacji:
     * Element `<script>`, który służy do odwoływania się do pliku biblioteki uwierzytelniania Azure Active Directory (ADAL) *ADAL. min. js*. ADAL to biblioteka języka JavaScript, która zapewnia uwierzytelnianie OAuth 2,0 (logowanie) i pozyskiwanie tokenu do uzyskiwania dostępu do interfejsów API.
     * Wiele elementów `<link>` dla arkuszy stylów (nazywanych również *CSS*), takich jak *sampleStyles. css* i *tsiclient. css*. Arkusze stylów kontrolują szczegóły stylu strony wizualnej, takie jak kolory, czcionki i odstępy.
     * Element `<script>` używany do odwoływania się do Time Series Insights JavaScript *tsiclient. js*biblioteki klienta. Strona używa biblioteki do wywoływania interfejsów API usługi Time Series Insights i renderowania formantów wykresu na stronie.

     >[!NOTE]
     > * Kod źródłowy biblioteki ADAL JavaScript jest dostępny w [repozytorium Azure-ActiveDirectory-Library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Kod źródłowy biblioteki klienta Time Series Insights JavaScript jest dostępny w [repozytorium tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * W elemencie `<body>` znajdują się elementy `<div>`, które ułatwiają Definiowanie układu elementów na stronie oraz inny element `<script>`:
     * Pierwszy element `<div>` określa okno dialogowe **logowania** (`id="loginModal"`).
     * Drugi element `<div>` działa jako element nadrzędny dla:
       * Nagłówek `<div>` elementu, który jest używany w komunikatach o stanie i informacje logowania w górnej części strony (`class="header"`).
       * Element `<div>` dla pozostałej części elementów treści strony, w tym wykresów (`class="chartsWrapper"`).
       * Sekcja `<script>`, która zawiera kod JavaScript używany do kontrolowania strony.

   [przykład klienta @no__t 1Time Series Insights z Narzędzia deweloperskie](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Rozwiń element `<div class="chartsWrapper">` i znajdziesz więcej elementów podrzędnych `<div>`. Te elementy są używane do pozycjonowania każdego przykładu formantu wykresu. Istnieje kilka par elementów `<div>`, jeden dla każdego przykładu wykresu:

   * Pierwszy element (`class="rowOfCardsTitle"`) zawiera opisowy tytuł podsumowujący ilustracje wykresów. Na przykład: `Static Line Charts With Full-Size Legends.`
   * Drugi element (`class="rowOfCards"`) jest elementem nadrzędnym, który zawiera dodatkowe elementy podrzędne `<div>`, które umieszczają rzeczywiste kontrolki wykresu w wierszu.

   [elementy DIV @no__t 1Body](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Rozwiń element `<script type="text/javascript">`, który jest bezpośrednio poniżej elementu `<div class="chartsWrapper">`. Początek sekcji JavaScript na poziomie strony służy do obsługi wszystkich logiki strony (uwierzytelnianie, wywoływanie Time Series Insights interfejsów API usługi, renderowanie formantów wykresu i inne):

   [skrypt @no__t 1Body](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="javascript-client-library"></a>Biblioteka kliencka JavaScript

Biblioteka klienta Time Series Insights (*tsiclient. js*) oferuje abstrakcje dla dwóch ważnych funkcji języka JavaScript:

* **Metody otoki do wywoływania interfejsów API zapytań Time Series Insights**: interfejsy API REST, których można użyć do wykonywania zapytań dotyczących Time Series Insights danych przy użyciu wyrażeń agregujących. Metody są zorganizowane w przestrzeni nazw TsiClient. Server biblioteki.

* **Metody tworzenia i wypełniania kilku typów formantów wykresów**: metod, których można użyć do renderowania Time Series Insights zagregowanych danych na stronie sieci Web. Metody są zorganizowane w przestrzeni nazw TsiClient. UX biblioteki.

Dzięki tym uproszczeniu deweloperzy mogą łatwiej tworzyć grafy interfejsu użytkownika i składniki wykresu, które są zasilane Time Series Insights danych.

### <a name="authentication"></a>Uwierzytelnianie

[Przykładowa aplikacja Time Series Insights](https://insights.timeseries.azure.com/clientsample) jest aplikacją jednostronicową z obsługą uwierzytelniania użytkownika ADAL OAuth 2,0:

1. W przypadku używania biblioteki ADAL do uwierzytelniania aplikacja kliencka musi być zarejestrowana w Azure Active Directory (Azure AD). W rzeczywistości aplikacja jednostronicowa jest zarejestrowana w celu użycia [niejawnego przepływu OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Aplikacja musi określić niektóre właściwości rejestracji w czasie wykonywania. Właściwości obejmują identyfikator GUID klienta (`clientId`) i identyfikator URI przekierowania (`postLogoutRedirectUri`).
1. Później aplikacja żąda *tokenu dostępu* z usługi Azure AD. Token dostępu jest wystawiony dla skończonego zestawu uprawnień dla określonej usługi lub identyfikatora interfejsu API (https: \//API. szeregów czasowych. Azure. com). Uprawnienia tokenu są wydawane w imieniu zalogowanego użytkownika. Identyfikator usługi lub interfejsu API to inna właściwość, która jest zawarta w rejestracji aplikacji usługi Azure AD.
1. Gdy biblioteka ADAL zwróci token dostępu do aplikacji, zostanie ona przeniesiona jako *token okaziciela* , gdy uzyskuje dostęp do interfejsów api usługi Time Series Insights.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Aby dowiedzieć się więcej na temat bibliotek uwierzytelniania usługi Azure AD obsługiwanych przez firmę Microsoft, zobacz [dokumentację dotyczącą biblioteki uwierzytelniania Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identyfikacja kontrolki

W podanym przykładzie elementy `<div>` są rozmieszczone w elemencie nadrzędnym `<body>`, aby zapewnić rozsądny układ formantów wykresu, które są renderowane na stronie.

Każdy element `<div>` Określa właściwości umieszczania i wizualne atrybutów formantów wykresu. Element HTML `id` właściwości służy jako unikatowe identyfikatory do powiązania z określonymi kontrolkami w celu renderowania i aktualizowania wizualizacji danych.

### <a name="aggregate-expressions"></a>Wyrażenia agregujące

Interfejsy API biblioteki klienta Time Series Insights używają wyrażeń agregujących:

* Wyrażenie agregujące zapewnia możliwość konstruowania co najmniej jednego *terminu wyszukiwania*.

* Interfejsy API klienta zostały zaprojektowane w taki sposób, aby zapewniały podobną funkcjonalność do innej aplikacji demonstracyjnej ( [eksplorator Time Series Insights](https://insights.timeseries.azure.com/demo)), która używa zakresu wyszukiwania, predykatów `where`, miar i wartości `splitBy`.

* Większość interfejsów API biblioteki klienta pobiera tablicę wyrażeń agregujących, których usługa używa do tworzenia zapytania o dane Time Series Insights.

### <a name="call-pattern"></a>Wzorzec wywołania

Wypełnianie i renderowanie formantów wykresu następuje po ogólnym wzorcu. Możesz obsłużyć ogólny wzorzec w całej przykładowej aplikacji i może pomóc podczas korzystania z biblioteki klienta:

1. Zadeklaruj `array`, aby przechowywać co najmniej jedno wyrażenie agregujące Time Series Insights:

   ```javascript
   var aes =  [];
   ```

1. Kompiluj *1* do *n* obiektów wyrażeń agregujących. Następnie dodaj je do tablicy wyrażeń agregujących:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **parametry aggregateExpression**

   | Parametr | Opis | Przykład |
   | --------- | ----------- | ------- |
   | `predicateObject` | Wyrażenie filtrowania danych |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Nazwa właściwości miary, która jest używana | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Agregacje właściwości miary, która ma zostać wybrana | `['avg', 'min']` |
   | `searchSpan`      | Czas trwania i rozmiar interwału wyrażenia agregacji | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Właściwość ciągu, która ma zostać podzielona przez (opcjonalnie: może mieć wartość null). | `{property: 'Station', type: 'String'}` |
   | `color`         | Kolor obiektów, które mają być renderowane. | `'pink'` |
   | `alias`           | Przyjazna nazwa wyrażenia agregującego | `'Factory3Temperature'` |
   | `contextMenuActions` | Tablica akcji, które mają być powiązane z obiektami szeregów czasowych w wizualizacji (opcjonalnie) | Aby uzyskać więcej informacji, zobacz [menu kontekstowe podręcznych](#pop-up-context-menus). |

1. Wywołaj zapytanie Time Series Insights przy użyciu interfejsów API TsiClient. Server, aby zażądać danych agregacji:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getaggregates — parametry**

   | Parametr | Opis | Przykład |
   | --------- | ----------- | ------- |
   | `token`     | Token dostępu dla interfejsu API Time Series Insights |  `authContext.getTsiToken()`<br />Aby uzyskać więcej informacji, zobacz [uwierzytelnianie](#authentication). |
   | `envFQDN`   | W pełni kwalifikowana nazwa domeny (FQDN) dla środowiska Time Series Insights | Z Azure Portal. Na przykład: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Tablica wyrażeń zapytania Time Series Insights | Użyj zmiennej `aes` zgodnie z wcześniejszym opisem: `aes.map(function(ae){return ae.toTsx()}`. |

1. Przekształć skompresowany wynik zwrócony z kwerendy Time Series Insights na kod JSON dla wizualizacji:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Utwórz formant wykresu przy użyciu interfejsów API TsiClient. UX. Powiąż go z jedną z elementów `<div>` na stronie:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Wypełnij formant wykresu przy użyciu przekształconych obiektów danych JSON i Renderuj formant na stronie:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Renderowanie formantów

Biblioteka klienta Time Series Insights zapewnia osiem unikatowych, wbudowanych kontrolek analitycznych:

* **Wykres liniowy**
* **Wykres kołowy**
* **Wykres słupkowy**
* **mapę cieplną**
* **kontrolki hierarchii**
* **dostępna siatka**
* **osie czasu zdarzeń dyskretnych**
* **osie czasu przejścia stanu**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Przykłady wykresu liniowego, wykresu słupkowego i wykresu kołowego

Przyjrzyj się kodowi demonstracyjnemu, który służy do renderowania niektórych standardowych kontrolek wykresu. Zanotuj model programowania i wzorce do tworzenia tych kontrolek. W celu przeanalizowania kodu HTML pod komentarzem `// Example 3/4/5`, które renderuje kontrolki z wartościami @no__t HTML-1 `chart3`, `chart4` i `chart5`.

Odwołaj się od kroku 3 [sekcji źródła strony i struktury](#page-source-and-structure) , które są rozmieszczone w wierszach na stronie. Każdy formant wykresu ma opisowy wiersz tytułu. W tym przykładzie trzy wykresy są wypełniane w tytule `Multiple Chart Types From the Same Data` `<div>` i są powiązane z trzema elementami `<div>`, które znajdują się poniżej tytułu:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

W poniższej sekcji kodu JavaScript użyto wzorców, które zostały opisane wcześniej: build Time Series Insights wyrażeń agregujących, użyj ich do wykonywania zapytań dotyczących Time Series Insights danych, a następnie renderowania trzech wykresów. Z przestrzeni nazw tsiClient. UX są używane trzy typy wykresów: `LineChart`, `BarChart` i `PieChart`. Typy wykresów są używane do tworzenia i renderowania odpowiednich wykresów. Wszystkie trzy wykresy mogą używać tych samych danych wyrażeń agregujących `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Trzy wykresy są wyświetlane w następujący sposób podczas renderowania:

[@no__t typy wykresów 1Multiple z tych samych danych](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="learn-about-advanced-features"></a>Więcej informacji na temat funkcji zaawansowanych

Biblioteka klienta Time Series Insights zawiera kilka dodatkowych funkcji, których można użyć do zaimplementowania wizualizacji danych.

### <a name="states-and-events"></a>Stany i zdarzenia

Zaawansowana funkcja to możliwość dodawania przejść między Stanami i zdarzeń dyskretnych do wykresów. Ta funkcja jest przydatna w przypadku wyróżniania zdarzeń, alertów i tworzenia przełączników stanu (na przykład przełączników włączania/wyłączania).

Przyjrzyj się kodowi otaczającemu komentarz `// Example 10`. Kod renderuje kontrolkę wiersza pod tytułem `Line Charts with Multiple Series Types` i wiąże ją z elementem `<div>` z wartością HTML `id` `chart10`.

Poniższe kroki opisują proces:

1. Struktura o nazwie `events4` jest zdefiniowana do przechowywania elementów zmiany stanu do śledzenia. Struktura zawiera:

   * Klucz ciągu o nazwie `Component States`.
   * Tablica obiektów wartości reprezentujących Stany. Każdy obiekt zawiera:
     * Klucz ciągu zawierający sygnaturę czasową ISO języka JavaScript.
     * Tablica zawierająca charakterystykę stanu: kolor i opis.

1. Struktura `events5` jest zdefiniowana dla `Incidents`, która przechowuje tablicę elementów zdarzenia do śledzenia. Struktura tablic jest tym samym kształtem, co struktura, która jest przedkreślona dla `events4`.

1. Wykres liniowy jest renderowany i przekazywany do dwóch struktur z parametrami opcji wykresu: `events:` i `states:`. Zwróć uwagę na inne parametry opcji określania `tooltip:`, `theme:` lub `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Znaczniki rombów/okna podręczne, które są używane do wskazywania zdarzeń i kolorowe paski/okna podręczne, wzdłuż skali czasu wskazują zmiany stanu:

[Wykresy @no__t 1Line z wieloma typami serii](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Menu kontekstowe wyskakujących okienek

Kolejną zaawansowaną funkcjonalnością jest możliwość tworzenia niestandardowych menu kontekstowych (menu podręczne kliknij prawym przyciskiem myszy). Niestandardowe menu kontekstowe są przydatne do włączania akcji i logicznego wykonywania dalszych kroków w zakresie aplikacji.

Spójrz na kod wokół komentarza `// Example 13/14/15`. Ten kod początkowo renderuje wykres liniowy pod tytułem `Line Chart with Context Menu to Create Pie/Bar Chart`. Wykres jest powiązany z elementem `<div>` z wartością HTML `id` `chart13`.

Za pomocą menu kontekstowego wykres liniowy zapewnia możliwość dynamicznego tworzenia wykresu kołowego i słupkowego, które są powiązane z elementami `<div>` z identyfikatorami `chart14` i `chart15`. Zarówno wykres kołowy, jak i wykres słupkowy korzystają również z menu kontekstowego, aby włączyć własne funkcje: możliwość kopiowania danych z wykresu kołowego do wykresu słupkowego i drukowania danych wykresu słupkowego odpowiednio do okna konsoli przeglądarki.

Poniższe kroki opisują proces:

1. Zdefiniowano szereg akcji niestandardowych. Każda akcja zawiera tablicę z co najmniej jednym elementem. Każdy element definiuje pojedynczy element menu kontekstowego:

   * `barChartActions`: Ta akcja definiuje menu kontekstowe wykresu kołowego, który zawiera jeden element do zdefiniowania pojedynczego elementu:
     * `name`: tekst używany dla elementu menu: "Drukuj parametry do konsoli".
     * `action`: Akcja, która jest skojarzona z elementem menu. Akcja jest zawsze anonimową funkcją, która przyjmuje trzy argumenty, które są oparte na wyrażeniu agregującym używanym do tworzenia wykresu. W takim przypadku argumenty są zapisywane w oknie konsoli przeglądarki:
       * `ae`: tablica wyrażeń agregujących.
       * `splitBy`: wartość `splitBy`.
       * `timestamp`: sygnatura czasowa.

   * `pieChartActions`: Ta akcja definiuje menu kontekstowe wykresu słupkowego, który zawiera jeden element do zdefiniowania pojedynczego elementu. Kształt i schemat są takie same jak wcześniej opisany poniżej element `barChartActions`, ale funkcja `action` jest bardzo różna: tworzy wystąpienie i renderuje wykres słupkowy. Argument `ae` służy do określenia tablicy wyrażeń agregujących, która jest przesyłana w czasie wykonywania, gdy zostanie otwarty element menu. Funkcja ustawia również właściwość `ae.contextMenu` z menu kontekstowym `barChartActions`.
   * `contextMenuActions`: Ta akcja definiuje menu kontekstowe wykresu liniowego, który zawiera trzy elementy do definiowania trzech elementów menu. Kształt i schemat dla każdego elementu są takie same jak elementy, które zostały opisane wcześniej. Podobnie jak w przypadku elementu `barChartActions` pierwszy element zapisuje trzy argumenty funkcji w oknie konsoli przeglądarki. Podobnie jak w przypadku elementu `pieChartActions` druga dwa elementy tworzy i renderuje odpowiednio wykres kołowy i wykres słupkowy. Pozostałe dwa elementy również ustawiają właściwości `ae.contextMenu` z menu kontekstowe `pieChartActions` i `barChartActions`.

1. Dwa wyrażenia agregujące są wypychane do tablicy wyrażeń agregujących `aes`. Określają tablicę `contextMenuActions` dla każdego elementu. Te wyrażenia są używane z kontrolką wykres liniowy.

1. Tylko wykres liniowy jest renderowany jako pierwszy, z którego wykres kołowy i wykres słupkowy mogą być renderowane w czasie wykonywania.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Poniższy zrzut ekranu przedstawia wykresy z odpowiednimi menu kontekstowymi podskakujących okienek. Wykres kołowy i wykres słupkowy zostały utworzone dynamicznie przy użyciu opcji menu kontekstowego wykresu liniowego.

[![Line wykres z menu kontekstowym w celu utworzenia wykresu kołowego i wykresu słupkowego](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pędzle

Możesz użyć pędzli, aby określić zakres czasu do definiowania akcji, takich jak powiększenie i eksplorowanie.

Kod, który jest używany do ilustrowania pędzli, jest widoczny w przykładzie `Line Chart with Context Menu to Create Pie/Bar Chart`, który opisuje menu kontekstowe podręcznych.

* Akcje pędzla są podobne do menu kontekstowego, które definiują serię akcji niestandardowych dla pędzla. Każda akcja zawiera tablicę, która zawiera co najmniej jeden element. Każdy element definiuje pojedynczy element menu kontekstowego:
   * `name`: tekst używany dla elementu menu: "Drukuj parametry do konsoli".
   * `action`: Akcja, która jest skojarzona z elementem menu, który jest zawsze anonimową funkcją, która przyjmuje dwa argumenty. W takim przypadku argumenty są zapisywane w oknie konsoli przeglądarki:
     * `fromTime`: sygnatura czasowa `from` zaznaczenia pędzla.
     * `toTime`: sygnatura czasowa `to` zaznaczenia pędzla.

* Akcje pędzla są dodawane jako inna Właściwość opcji wykresu. Właściwość `brushContextMenuActions: brushActions` jest przenoszona do wywołania `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Line wykres z menu kontekstowym, aby utworzyć wykres kołowy i wykres słupkowy przy użyciu pędzli](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu samouczka Wyczyść utworzone zasoby:

1. W menu po lewej stronie w [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**, Znajdź Azure Time Series Insights grupę zasobów.
1. Usuń całą grupę zasobów (i wszystkie znajdujące się w niej zasoby), wybierając pozycję **Usuń** lub Usuń osobno każdy zasób.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wykonywania tych instrukcji:

> [!div class="checklist"]
> * Zaloguj się i Eksploruj Time Series Insights przykładową aplikację i jej Źródło
> * Używanie interfejsów API w Time Series Insights bibliotece klienckiej JavaScript
> * Używanie języka JavaScript do tworzenia i wypełniania formantów wykresu za pomocą danych Time Series Insights

Time Series Insights Przykładowa aplikacja używa demonstracyjnego zestawu danych. Aby dowiedzieć się, jak utworzyć własne środowisko Time Series Insights i zestaw danych, przeczytaj następujący artykuł:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie środowiska Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

Można też wyświetlić przykładowe pliki źródłowe aplikacji Time Series Insights:

> [!div class="nextstepaction"]
> [Time Series Insights przykładowe repozytorium aplikacji](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Przeczytaj dokumentację dotyczącą interfejsu API klienta Time Series Insights:

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API Time Series Insights](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
