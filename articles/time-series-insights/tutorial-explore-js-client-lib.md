---
title: 'Samouczek: Poznawanie biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights | Microsoft Docs'
description: Samouczek, aby dowiedzieć się więcej na temat biblioteki klienta Azure Time Series Insights JavaScript i powiązanego modelu programowania.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 2c1013829a7e6e94712e22ae235a47490c8c5eaa
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677657"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Samouczek: Poznawanie biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights

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

Utwórz konto bezpłatnej [subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek używa funkcji **Narzędzia deweloperskie** przeglądarki. Nowoczesne przeglądarki sieci Web ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)i inne) zwykle zapewniają dostęp do **widoku inspektora sieci Web** za pomocą klawisza F12 na klawiaturze. Innym sposobem uzyskania dostępu do widoku jest kliknięcie prawym przyciskiem myszy na stronie sieci Web, a następnie wybranie polecenia **Zbadaj element**.

## <a name="video"></a>Połączenia wideo

W tym filmie wideo wprowadzamy zestaw SDK języka JavaScript Time Series Insights Open Source:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="time-series-insights-sample-application"></a>Przykładowa aplikacja usługi Time Series Insights

W tym samouczku korzystamy z bezpłatnej, hostowanej Time Series Insights przykładowej aplikacji do eksplorowania kodu źródłowego w ramach aplikacji oraz do eksplorowania Time Series Insights biblioteki klienckiej JavaScript. Korzystając z przykładowej aplikacji, dowiesz się, jak korzystać z Time Series Insights w języku JavaScript i wizualizować dane za pomocą wykresów i grafów.

1. Przejdź do [Time Series Insights przykładowej aplikacji](https://insights.timeseries.azure.com/clientsample). Zostanie wyświetlony następujący monit logowania:

   [![Przykładowy monit logowania klienta Time Series Insights](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Wybierz pozycję **Zaloguj** się, aby wprowadzić lub wybrać swoje poświadczenia. Użyj konta organizacji przedsiębiorstwa (Azure Active Directory) lub konta osobistego (konto Microsoft).

   [![Monit o poświadczenia przykładowego klienta Time Series Insights](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Po zalogowaniu zostanie wyświetlona strona zawierająca wykresy wypełnione danymi Time Series Insights. Twoje konto użytkownika i opcja **Wyloguj się** są widoczne w prawym górnym rogu:

   [![Przykładowa Strona główna Time Series Insights klienta po zalogowaniu](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Źródło i struktura strony

Najpierw wyświetlmy [kod źródłowy HTML i JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) renderowanej strony sieci Web:

1. Otwórz w przeglądarce funkcję **Narzędzia deweloperskie**. Zbadaj elementy HTML wchodzące w skład bieżącej strony (określane również jako drzewo HTML lub drzewo DOM).

1. Rozwiń elementy `<body>` i i obserwuj następujące sekcje: `<head>`

   * W obszarze `<head>` elementu znajdziesz metadane strony i zależności, które umożliwiają uruchomienie aplikacji:
     * Element, który jest używany do odwoływania się do pliku biblioteki uwierzytelniania Azure Active Directory (ADAL) plik *ADAL. min. js.* `<script>` ADAL to biblioteka języka JavaScript, która udostępnia funkcje uwierzytelniania w standardzie OAuth 2.0 (logowanie) oraz uzyskanie tokenu pozwalającego na dostęp do interfejsów API.
     * Wiele `<link>` elementów arkuszy stylów (nazywanych również *CSS*), takich jak *sampleStyles. css* i *tsiclient. css*. Arkusze stylów kontrolują szczegóły stylu strony wizualnej, takie jak kolory, czcionki i odstępy.
     * Element, który jest używany do odwoływania się do Time Series Insights biblioteki klienckiej JavaScript *tsiclient. js.* `<script>` Strona używa biblioteki do wywoływania interfejsów API usługi Time Series Insights i renderowania formantów wykresu na stronie.

     >[!NOTE]
     > * Kod źródłowy biblioteki ADAL JavaScript jest dostępny w [repozytorium Azure-ActiveDirectory-Library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Kod źródłowy biblioteki klienta Time Series Insights JavaScript jest dostępny w [repozytorium tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * W obszarze `<div>` `<script>` elementu znajdziesz elementy, które ułatwiają Definiowanie układu elementów na stronie i inny element: `<body>`
     * Pierwszy `<div>` element określa okno dialogowe **logowania** (`id="loginModal"`).
     * Drugi element `<div>` pełni rolę elementu nadrzędnego dla następujących elementów:
       * Elementu nagłówkowego `<div>`, zawierającego komunikaty o stanie i informacje dotyczące logowania, wyświetlane w górnej części strony (`class="header"`).
       * Element dla pozostałej części elementów treści strony, w tym wykresów (`class="chartsWrapper"`). `<div>`
       * `<script>` Sekcja zawierająca kod JavaScript używany do kontrolowania strony.

   [![Przykład Time Series Insights klienta z Narzędzia deweloperskie](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Rozwiń element, aby znaleźć więcej elementów podrzędnych `<div>`. `<div class="chartsWrapper">` Te elementy są używane do określenia położenia każdej przykładowej kontrolki wykresu. Istnieje kilka par `<div>` elementów, jeden dla każdego przykładu wykresu:

   * Pierwszy element (`class="rowOfCardsTitle"`) zawiera opisowy tytuł podsumowujący, co przedstawia wykres. Na przykład: `Static Line Charts With Full-Size Legends.`
   * Drugi element (`class="rowOfCards"`) jest elementem nadrzędnym, który zawiera dodatkowe `<div>` elementy podrzędne, które umieszczają rzeczywiste kontrolki wykresu w wierszu.

   [![Elementy DIV Body](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Rozwiń element, który znajduje się bezpośrednio `<div class="chartsWrapper">` poniżej elementu. `<script type="text/javascript">` Początek sekcji JavaScript na poziomie strony służy do obsługi wszystkich logiki strony (uwierzytelnianie, wywoływanie Time Series Insights interfejsów API usługi, renderowanie formantów wykresu i inne):

   [![Skrypt treści](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>Time Series Insights pojęć dotyczących biblioteki klienta JavaScript

Biblioteka klienta Time Series Insights (*tsiclient. js*) oferuje abstrakcje dla dwóch ważnych funkcji języka JavaScript:

* **Metody otoki do wywoływania interfejsów API zapytań Time Series Insights**: Interfejsy API REST, których można użyć do wykonywania zapytań dotyczących danych Time Series Insights przy użyciu wyrażeń agregujących. Metody są zorganizowane w przestrzeni nazw TsiClient. Server biblioteki.

* **Metody tworzenia i wypełniania kilku typów kontrolek wykresów**: Metody, których można użyć do renderowania Time Series Insights zagregowanych danych na stronie sieci Web. Metody są zorganizowane w przestrzeni nazw TsiClient. UX biblioteki.

Dzięki tym uproszczeniu deweloperzy mogą łatwiej tworzyć grafy interfejsu użytkownika i składniki wykresu, które są zasilane Time Series Insights danych.

### <a name="authentication"></a>Authentication

[Przykładowa aplikacja Time Series Insights](https://insights.timeseries.azure.com/clientsample) jest aplikacją jednostronicową z obsługą uwierzytelniania użytkownika ADAL OAuth 2,0:

1. W przypadku używania biblioteki ADAL do uwierzytelniania aplikacja kliencka musi być zarejestrowana w Azure Active Directory (Azure AD). W rzeczywistości aplikacja jednostronicowa jest zarejestrowana w celu użycia niejawnego [przepływu OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Aplikacja musi określić niektóre właściwości rejestracji w czasie wykonywania. Właściwości obejmują identyfikator GUID klienta (`clientId`) i identyfikator URI przekierowania (`postLogoutRedirectUri`).
1. Później aplikacja żąda *tokenu dostępu* z usługi Azure AD. Token dostępu jest wystawiony dla skończonego zestawu uprawnień dla określonej usługi lub identyfikatora interfejsu API (https:\//API.TIMESERIES.Azure.com). Uprawnienia tokenu są wystawiane w imieniu zalogowanego użytkownika. Identyfikator usługi lub interfejsu API to inna właściwość, która jest zawarta w rejestracji aplikacji usługi Azure AD.
1. Gdy biblioteka ADAL zwróci token dostępu do aplikacji, zostanie ona przeniesiona jako *token okaziciela* , gdy uzyskuje dostęp do interfejsów api usługi Time Series Insights.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Aby dowiedzieć się więcej na temat bibliotek uwierzytelniania usługi Azure AD obsługiwanych przez firmę Microsoft, zobacz [dokumentację dotyczącą biblioteki uwierzytelniania Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identyfikacja kontrolki

W podanym przykładzie elementy `<div>` są rozmieszczone w elemencie nadrzędnym `<body>` , aby zapewnić rozsądny układ formantów wykresu, które są renderowane na stronie.

Każdy `<div>` element określa właściwości położenia i wizualizacji atrybutów formantów wykresu. Właściwości elementu `id` HTML służy jako unikatowe identyfikatory powiązane z konkretnymi kontrolkami w celu renderowania i aktualizowania wizualizacji danych.

### <a name="aggregate-expressions"></a>Wyrażenia agregujące

Interfejsy API biblioteki klienta Time Series Insights używają wyrażeń agregujących:

* Wyrażenie agregujące zapewnia możliwość konstruowania co najmniej jednego *terminu wyszukiwania*.

* Interfejsy API klienta zostały zaprojektowane w taki sposób, aby zapewniały podobną funkcjonalność do innej aplikacji demonstracyjnej ( [Eksplorator Time Series Insights](https://insights.timeseries.azure.com/demo)) `where` , która używa zakresu wyszukiwania, `splitBy` predykatów, miar i wartości.

* Większość interfejsów API biblioteki klienta pobiera tablicę wyrażeń agregujących, których usługa używa do tworzenia zapytania o dane Time Series Insights.

### <a name="call-pattern"></a>Wzorzec wywołania

Wypełnianie i renderowanie formantów wykresu następuje po ogólnym wzorcu. Możesz obsłużyć ogólny wzorzec w całej przykładowej aplikacji i może pomóc podczas korzystania z biblioteki klienta:

1. Zadeklaruj `array` element do przechowywania co najmniej jednego Time Series Insights wyrażeń agregujących:

   ```javascript
   var aes =  [];
   ```

1. Kompiluj *1* do *n* obiektów wyrażeń agregujących. Następnie dodaj je do tablicy wyrażeń agregujących:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **Parametry aggregateExpression**

   | Parametr | Opis | Przykład |
   | --------- | ----------- | ------- |
   | `predicateObject` | Wyrażenie filtrowania danych |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Nazwa właściwości miary, która jest używana | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Agregacje właściwości miary, która ma zostać wybrana | `['avg', 'min']` |
   | `searchSpan`      | Czas trwania i rozmiar interwału wyrażenia agregacji | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Właściwość ciągu, która ma zostać podzielona przez (opcjonalnie: może mieć wartość null). | `{property: 'Station', type: 'String'}` |
   | `color`         | Kolor obiektów, które mają być renderowane. | `'pink'` |
   | `alias`           | Przyjazna nazwa wyrażenia agregującego | `'Factory3Temperature'` |
   | `contextMenuActions` | Tablica akcji, które mają być powiązane z obiektami szeregów czasowych w wizualizacji (opcjonalnie) | Aby uzyskać więcej informacji, zobacz [menu kontekstowe](#pop-up-context-menus)podręcznych. |

1. Wywołaj zapytanie Time Series Insights przy użyciu interfejsów API TsiClient. Server, aby zażądać danych agregacji:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **parametry getAggregates**

   | Parametr | Opis | Przykład |
   | --------- | ----------- | ------- |
   | `token`     | Token dostępu dla interfejsu API Time Series Insights |  `authContext.getTsiToken()`<br />Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). |
   | `envFQDN`   | W pełni kwalifikowana nazwa domeny (FQDN) dla środowiska Time Series Insights | Z Azure Portal. Na przykład: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Tablica wyrażeń zapytania Time Series Insights | Użyj zmiennej `aes` zgodnie z wcześniejszym opisem `aes.map(function(ae){return ae.toTsx()}`:. |

1. Przekształć skompresowany wynik zwrócony z kwerendy Time Series Insights na kod JSON dla wizualizacji:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Utwórz formant wykresu przy użyciu interfejsów API TsiClient. UX. Powiąż go z jednym z `<div>` elementów na stronie:

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

Przyjrzyj się kodowi demonstracyjnemu, który służy do renderowania niektórych standardowych kontrolek wykresu. Zanotuj model programowania i wzorce do tworzenia tych kontrolek. W celu zapoznania się z kodem `// Example 3/4/5` HTML w komentarzu, który renderuje `id` kontrolki `chart4`z wartościami `chart5` `chart3`HTML, i.

Odwołaj się od kroku 3 [sekcji źródła strony i struktury](#page-source-and-structure) , które są rozmieszczone w wierszach na stronie. Każdy formant wykresu ma opisowy wiersz tytułu. W tym przykładzie trzy wykresy są wypełniane pod `Multiple Chart Types From the Same Data` elementem title `<div>` i są powiązane z trzema `<div>` elementami, które znajdują się poniżej tytułu:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

W poniższej sekcji kodu JavaScript użyto wzorców, które zostały opisane wcześniej: build Time Series Insights wyrażeń agregujących, użyj ich do wykonywania zapytań dotyczących Time Series Insights danych, a następnie renderowania trzech wykresów. Z przestrzeni nazw tsiClient. UX są używane trzy typy wykresów: `LineChart`, `BarChart`, i `PieChart`. Typy wykresów są używane do tworzenia i renderowania odpowiednich wykresów. Wszystkie trzy wykresy mogą używać tych samych danych `transformedResult`wyrażeń agregujących:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Po renderowaniu trzy wykresy wyglądają następująco:

[![Wiele typów wykresów z tym samym zestawem danych](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funkcje zaawansowane

Biblioteka klienta Time Series Insights zawiera kilka dodatkowych funkcji, których można użyć do zaimplementowania wizualizacji danych.

### <a name="states-and-events"></a>Stany i zdarzenia

Zaawansowana funkcja to możliwość dodawania przejść między Stanami i zdarzeń dyskretnych do wykresów. Ta funkcja jest przydatna w przypadku wyróżniania zdarzeń, alertów i tworzenia przełączników stanu (na przykład przełączników włączania/wyłączania).

Przyjrzyj się kodowi otaczającemu `// Example 10` komentarz. Kod renderuje kontrolkę wiersza pod tytułem `Line Charts with Multiple Series Types` i wiąże ją `<div>` z elementem z wartością `chart10`HTML `id` .

Poniższe kroki opisują proces:

1. Struktura o nazwie `events4` jest zdefiniowana do przechowywania elementów zmiany stanu do śledzenia. Struktura zawiera:

   * Klucz będący ciągiem znaków o nazwie `Component States`.
   * Tablicę obiektów wartości reprezentujących stany. Każdy obiekt obejmuje:
     * Klucz będący ciągiem znaków, zawierający znacznik czasu języka JavaScript w standardzie ISO.
     * Tablicę zawierającą właściwości stanu: kolor i opis.

1. Struktura jest zdefiniowana dla `Incidents`, która przechowuje tablicę elementów zdarzenia do śledzenia. `events5` Struktura tablicy ma taki sam kształt, jak struktura opisana dla elementu `events4`.

1. Wykres liniowy jest renderowany i przekazywany do dwóch struktur z parametrami opcji wykresu: `events:` i. `states:` Zwróć uwagę na inne parametry opcji `tooltip:`, które określają, `theme:`, `grid:`lub.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Znaczniki rombów/okna podręczne, które są używane do wskazywania zdarzeń i kolorowe paski/okna podręczne, wzdłuż skali czasu wskazują zmiany stanu:

[![Wykresy liniowe z wieloma typami serii](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Wyskakujące menu kontekstowe

Kolejną zaawansowaną funkcjonalnością jest możliwość tworzenia niestandardowych menu kontekstowych (menu podręczne kliknij prawym przyciskiem myszy). Niestandardowe menu kontekstowe są przydatne w przypadku włączania akcji i kolejnych logicznych kroków, znajdujących się w zakresie aplikacji.

Przyjrzyj się kodowi do `// Example 13/14/15` komentarza. Ten kod początkowo renderuje wykres liniowy pod tytułem `Line Chart with Context Menu to Create Pie/Bar Chart`. Wykres jest powiązany `<div>` z elementem z wartością `chart13`HTML `id` .

Dzięki wykorzystaniu menu kontekstowych wykres liniowy umożliwia dynamiczne tworzenie wykresów kołowych i słupkowych powiązanych z elementami `<div>` za pomocą identyfikatorów `chart14` i `chart15`. Zarówno wykres kołowy, jak i wykres słupkowy korzystają również z menu kontekstowego, aby włączyć własne funkcje: możliwość kopiowania danych z wykresu kołowego do wykresu słupkowego i drukowania danych wykresu słupkowego odpowiednio do okna konsoli przeglądarki.

Poniższe kroki opisują proces:

1. Zdefiniowano szereg akcji niestandardowych. Każda akcja zawiera tablicę z co najmniej jednym elementem. Każdy element definiuje pojedynczy element menu kontekstowego:

   * `barChartActions`: ta akcja definiuje menu kontekstowe wykresu kołowego, zawierające jeden element określający pojedynczy element menu:
     * `name`: tekst, który jest używany dla elementu menu: „Print parameters to console” (Parametry drukowania do konsoli).
     * `action`: akcja skojarzona z elementem menu. Ta akcja jest zawsze funkcją anonimową, przyjmującą trzy argumenty oparte na wyrażeniu agregującym używanym do tworzenia wykresu. W tym przypadku argumenty są zapisywane w oknie konsoli przeglądarki:
       * `ae`: tablica wyrażeń agregujących.
       * `splitBy`: `splitBy` Wartość.
       * `timestamp`: znacznik czasu.

   * `pieChartActions`: ta akcja definiuje menu kontekstowe wykresu słupkowego, zawierające jeden element określający pojedynczy element menu. Kształt i schemat są takie same jak `barChartActions` opisane wcześniej elementy, `action` ale funkcja jest bardzo różna: tworzy wystąpienie i renderuje wykres słupkowy. `ae` Argument jest używany do określenia tablicy wyrażeń agregujących, która jest przesyłana w czasie wykonywania, gdy zostanie otwarty element menu. Funkcja ustawia również właściwość `ae.contextMenu` za pomocą menu kontekstowego `barChartActions`.
   * `contextMenuActions`: ta akcja definiuje menu kontekstowe wykresu liniowego, zawierające trzy elementy określające trzy elementy menu. Kształt i schemat dla każdego elementu są takie same jak elementy, które zostały opisane wcześniej. Podobnie jak dla elementu `barChartActions`, pierwszy element zapisuje trzy argumenty funkcji w oknie konsoli przeglądarki. Podobnie jak w przypadku elementu,drugidwaelementytworzyirenderujeodpowiedniowykreskołowyiwykressłupkowy.`pieChartActions` Następne dwa elementy ustawiają również swoje właściwości `ae.contextMenu` poprzez menu kontekstowe odpowiednio `pieChartActions` i `barChartActions`.

1. Dwa wyrażenia agregujące są wypychane do `aes` tablicy wyrażeń agregujących. Określają `contextMenuActions` one tablicę dla każdego elementu. Wyrażenia są używane przez kontrolkę wykresu liniowego.

1. Tylko wykres liniowy jest renderowany jako pierwszy, z którego wykres kołowy i wykres słupkowy mogą być renderowane w czasie wykonywania.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Poniższy zrzut ekranu przedstawia wykresy z odpowiednimi menu kontekstowymi podskakujących okienek. Wykres kołowy i wykres słupkowy zostały utworzone dynamicznie przy użyciu opcji menu kontekstowego wykresu liniowego.

[![Wykres liniowy z menu kontekstowym do utworzenia wykresu kołowego i wykresu słupkowego](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pędzle

Możesz użyć pędzli, aby określić zakres czasu do definiowania akcji, takich jak powiększenie i eksplorowanie.

Kod, który jest używany do ilustrowania pędzli pokazano w `Line Chart with Context Menu to Create Pie/Bar Chart` przykładzie, który opisuje menu kontekstowe podręcznych.

* Akcje związane z pędzlem są bardzo podobne do menu kontekstowego w tym, że definiują dla pędzla szereg akcji niestandardowych. Każda akcja zawiera tablicę, która zawiera co najmniej jeden element. Każdy element definiuje pojedynczy element menu kontekstowego:
   * `name`: tekst, który jest używany dla elementu menu: „Print parameters to console” (Parametry drukowania do konsoli).
   * `action`: skojarzona z elementem menu akcja, która jest zawsze funkcją anonimową, przyjmującą dwa argumenty. W tym przypadku argumenty są zapisywane w oknie konsoli przeglądarki:
     * `fromTime`: `from` Sygnatura czasowa zaznaczenia pędzla.
     * `toTime`: `to` Sygnatura czasowa zaznaczenia pędzla.

* Akcje pędzla są dodawane jako jeszcze jedna właściwość opcji wykresu. Właściwość jest przenoszona `linechart.Render` do wywołania. `brushContextMenuActions: brushActions`

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Wykres liniowy z menu kontekstowym, aby utworzyć wykres kołowy i wykres słupkowy przy użyciu pędzli](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu samouczka Wyczyść utworzone zasoby:

1. W menu po lewej stronie w [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**, Znajdź Azure Time Series Insights grupę zasobów.
1. Usuń całą grupę zasobów (i wszystkie znajdujące się w niej zasoby), wybierając pozycję **Usuń** lub Usuń osobno każdy zasób.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zaloguj się i Eksploruj Time Series Insights przykładową aplikację i jej Źródło
> * Używanie interfejsów API w Time Series Insights bibliotece klienckiej JavaScript
> * Używanie języka JavaScript do tworzenia i wypełniania formantów wykresu za pomocą danych Time Series Insights

Time Series Insights Przykładowa aplikacja używa demonstracyjnego zestawu danych. Aby dowiedzieć się, jak utworzyć własne środowisko Time Series Insights i zestaw danych, przeczytaj następujący artykuł:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie środowiska usługi Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

Można też wyświetlić przykładowe pliki źródłowe aplikacji Time Series Insights:

> [!div class="nextstepaction"]
> [Time Series Insights przykładowe repozytorium aplikacji](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Przeczytaj dokumentację dotyczącą interfejsu API klienta Time Series Insights:

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API Time Series Insights](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
