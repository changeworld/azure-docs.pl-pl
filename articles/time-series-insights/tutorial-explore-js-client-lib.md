---
title: 'Samouczek: Poznawanie biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights | Microsoft Docs'
description: Informacje na temat biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights i powiązanego z nią modelu programowania.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 7208c0d42cba0e7f04fc6876bf3ada9fa65a00d9
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991454"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Samouczek: Poznawanie biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights

Biblioteki klienta JavaScript D3 na podstawie usługi Azure Time Series Insights został opracowany, by pomocy zapytania deweloperom sieci web i wizualizuj dane przechowywane w usłudze Time Series Insights. Ten samouczek przeprowadzi Cię przez bibliotekę klienta usługi Time Series Insights oraz model programowania przy użyciu hostowanego przykładową aplikację.

Samouczka przedstawiono sposób pracy z biblioteką, jak uzyskać dostęp do danych usługi Time Series Insights i jak za pomocą kontrolek wykresów renderowania i wizualizuj dane. Będzie również sposób do eksperymentowania z różnymi rodzajami wykresów do wizualizacji danych. Na końcu tego samouczka będziesz mieć możliwość włączenie funkcji usługi Time Series Insights do aplikacji sieci web za pomocą biblioteki klienta.

W szczególności poznasz:

> [!div class="checklist"]
> * Przykładowa aplikacja usługi Time Series Insights
> * Biblioteki klienta JavaScript Insights serii czasu
> * Jak przykładowa aplikacja korzysta z biblioteki do wizualizacji danych usługi Time Series Insights

> [!NOTE]
> * W tym samouczku użyto bezpłatne hostowanych [pokaz sieci web usługi Time Series Insights](https://insights.timeseries.azure.com/clientsample).
> * Pliki źródłowe aplikacji przykładowej usługi Time Series Insights znajdują się w [repozytorium przykładów GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Odczyt [Dokumentacja referencyjna klienta usługi Time Series Insights](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Wideo

W tym filmie wprowadzeniu zestawu SDK typu open-source czas serii Insights JavaScript:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku jest używana w przeglądarce **narzędzi deweloperskich** funkcji. Współczesne przeglądarki internetowe obsługują ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)i inne) zwykle zapewniają dostęp do **Widoku inspektora sieci web** za pomocą klawisza F12 na klawiaturze. Innym sposobem na dostęp do widoku jest kliknij prawym przyciskiem myszy na stronie sieci Web, a następnie wybierz pozycję **sprawdzić Element**.

## <a name="time-series-insights-sample-application"></a>Przykładowa aplikacja usługi Time Series Insights

W tym samouczku korzystamy z bezpłatnej, hostowanej usługi Time Series Insights przykładowej aplikacji Eksploruj kod źródłowy aplikacji i zapoznać się z biblioteki klienta JavaScript Insights serii czasu. Za pomocą aplikacji przykładowej, dowiesz się, jak korzystać z usługi Time Series Insights w języku JavaScript i wizualizowanie danych za pomocą wykresów i schematów.

1. Przejdź do [usługi Time Series Insights Przykładowa aplikacja](https://insights.timeseries.azure.com/clientsample). Wyświetlany jest następujący monit logowania:

   [![Czas Series Insights klienta przykładowe monit logowania](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Wybierz **Zaloguj** wprowadź lub wybierz swoje poświadczenia. Użyj konta organizacji przedsiębiorstwa (Azure Active Directory) lub osobiste konto (konto Microsoft).

   [![Monit o poświadczenia przykładowe klienta Series Insights czasu](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Po zalogowaniu, jest wyświetlana strona, wyświetlająca wykresy, które zawierają dane usługi Time Series Insights. Twoje konto użytkownika i opcja **Wyloguj się** są widoczne w prawym górnym rogu:

   [![Czas Series Insights klienta przykładowej strony głównej po zalogowaniu](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Źródło i struktura strony

Po pierwsze, teraz wyświetlić [kod źródłowy w kodzie HTML i JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) renderowanej strony sieci Web:

1. Otwórz w przeglądarce funkcję **Narzędzia deweloperskie**. Zbadaj elementy HTML wchodzące w skład bieżącej strony (określane również jako drzewo HTML lub drzewo DOM).

1. Rozwiń `<head>` i `<body>` elementy i sprawdź, czy następujące sekcje:

   * W obszarze `<head>` elementu, można znaleźć metadanych strony i zależności, które umożliwiają uruchomienie aplikacji:
     * A `<script>` element, który jest używany do odwołania się do interfejsów Azure Active Directory Authentication Library (ADAL) pliku *adal.min.js*. ADAL to biblioteka języka JavaScript, która udostępnia funkcje uwierzytelniania w standardzie OAuth 2.0 (logowanie) oraz uzyskanie tokenu pozwalającego na dostęp do interfejsów API.
     * Wiele `<link>` elementy dla arkuszy stylów (znany także jako *CSS*) takich jak *sampleStyles.css* i *tsiclient.css*. Arkusze stylów kontrolować szczegóły style visual strony, takich jak kolory, czcionki i odstępy.
     * A `<script>` element, który jest używany do odwoływać się do biblioteki klienckiej JavaScript Insights serii czasu *tsiclient.js*. Strona korzysta z biblioteki do wywołania usługi Time Series Insights interfejsów API oraz do renderowania kontrolek wykresów na stronie.

     >[!NOTE]
     > * Kod źródłowy dla biblioteki ADAL JavaScript jest dostępna w [repozytorium azure-activedirectory biblioteki do js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Kod źródłowy biblioteki klienta JavaScript Insights serii czasu jest dostępna w [repozytorium tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * W obszarze `<body>` elementu, można znaleźć `<div>` elementów, które pomagają zdefiniować układ elementów na stronie, a druga `<script>` elementu:
     * Pierwszy `<div>` element Określa **Zaloguj** okno dialogowe (`id="loginModal"`).
     * Drugi element `<div>` pełni rolę elementu nadrzędnego dla następujących elementów:
       * Elementu nagłówkowego `<div>`, zawierającego komunikaty o stanie i informacje dotyczące logowania, wyświetlane w górnej części strony (`class="header"`).
       * A `<div>` elementu w pozostałej części strony elementów treści, w tym wykresy (`class="chartsWrapper"`).
       * A `<script>` sekcję zawierającą języka JavaScript, który służy do sterowania strony.

   [![Próbki klienta Insights serii czasu za pomocą narzędzi dla deweloperów](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Rozwiń `<div class="chartsWrapper">` elementu w którym znajdziesz więcej podrzędnych `<div>` elementów. Te elementy są używane do określenia położenia każdej przykładowej kontrolki wykresu. Istnieje kilka pary `<div>` elementy, po jednym dla każdego przykład wykresu:

   * Pierwszy element (`class="rowOfCardsTitle"`) zawiera opisowy tytuł podsumowujący, co przedstawia wykres. Na przykład: `Static Line Charts With Full-Size Legends.`
   * Drugi (`class="rowOfCards"`) element jest elementem nadrzędnym, który zawiera dodatkowe podrzędne `<div>` elementy, które położenie formantów całego pakietu w wierszu.

   [![Elementy div treści](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Rozwiń `<script type="text/javascript">` element, który jest bezpośrednio poniżej `<div class="chartsWrapper">` elementu. Początkowi sekcji kodu JavaScript na poziomie strony jest używana do obsługi całą logikę strony (uwierzytelnianie podczas wywoływania usługi Time Series Insights interfejsów API, renderowania kontrolek wykresów i inne):

   [![Treść skryptu](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>Pojęcia biblioteki klienta JavaScript Insights serii czasu

Biblioteka klienta usługi Time Series Insights (*tsclient.js*) zawiera elementy abstrakcji do dwie ważne funkcje języka JavaScript:

* **Otoka metod wywoływania interfejsów API zapytań czas serii Insights**: Interfejsy API REST, służących do zapytania usługi Time Series Insights danych przy użyciu wyrażeń agregujących. Te metody są zorganizowane w przestrzeni nazw TsiClient.Server biblioteki.

* **Metody tworzenia i wypełniania kilku typów kontrolek wykresów**: Metody można użyć do renderowania usługi Time Series Insights agregować dane na stronie sieci Web. Te metody są zorganizowane w przestrzeni nazw TsiClient.UX biblioteki.

Za pomocą tych definiowaniu deweloperzy mogą łatwiej tworzyć składniki programu graph i wykres interfejsu użytkownika, które są obsługiwane z danymi usługi Time Series Insights.

### <a name="authentication"></a>Authentication

[Usługi Time Series Insights Przykładowa aplikacja](https://insights.timeseries.azure.com/clientsample) jest aplikacja jednostronicowa, który obsługuje uwierzytelnianie użytkownika ADAL OAuth 2.0:

1. Korzystając z biblioteki ADAL do uwierzytelniania, aplikacja kliencka musi być zarejestrowany w usłudze Azure Active Directory (Azure AD). W rzeczywistości zarejestrowana aplikacja jednostronicowa w celu używania [OAuth 2.0 niejawne udzielić przepływ](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Aplikacja musi określić niektóre właściwości rejestrowania w czasie wykonywania. Właściwości obejmują identyfikator GUID klienta (`clientId`) oraz identyfikatora URI przekierowania (`postLogoutRedirectUri`).
1. Później, aplikacja prosi *token dostępu* z usługi Azure AD. Token dostępu został wystawiony dla skończony zestaw uprawnień dla określonej usługi lub identyfikator interfejsu API (https:\//api.timeseries.azure.com). Uprawnienia tokenu są wystawiane w imieniu zalogowanego użytkownika. Identyfikator usługi lub interfejsu API jest inną właściwość, która jest zawarta w rejestracji usługi Azure AD aplikacji.
1. Po ADAL zwraca token dostępu do aplikacji, jest przekazywany jako *tokenu elementu nośnego* kiedy uzyskuje dostęp do interfejsów API usługi Time Series Insights.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Aby dowiedzieć się więcej na temat biblioteki uwierzytelniania usługi Azure AD obsługiwane przez firmę Microsoft, zobacz [dokumentacja usługi Azure Active Directory Authentication Library](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identyfikacja kontrolki

W podanym przykładzie `<div>` elementy są uporządkowane w obiekcie nadrzędnym `<body>` element zapewniają rozsądne układ wykresu steruje tym renderowania na stronie.

Każdy `<div>` element określa właściwości umieszczania i atrybuty visual kontrolek wykresu. HTML element `id` właściwości będzie służyć jako unikatowych identyfikatorów, które można powiązać do określonych formantów do renderowania i zaktualizuj wizualizowania danych.

### <a name="aggregate-expressions"></a>Wyrażenia agregujące

Interfejsów API biblioteki klienckiej usługi Time Series Insights za pomocą wyrażeń agregacji:

* Wyrażenie agregujące oferuje możliwość skonstruowania co najmniej jeden *terminy wyszukiwania*.

* Klient interfejsów API mają na celu zapewniają podobne funkcje do innej wersji demonstracyjnej aplikacji ( [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com/demo)), który używa wyszukiwania zasięg, `where` predykatów, miary, i `splitBy` wartości.

* Większość biblioteki klienckiej interfejsów API zająć tablicę wyrażeniami dotyczącymi agregacji, używanych przez usługę w celu skonstruowania zapytania danych usługi Time Series Insights.

### <a name="call-pattern"></a>Wzorzec wywołania

Podczas wypełniania i renderowanie kontrolek wykresu jest zgodna ze wzorcem ogólne. Można obserwować ogólny wzorzec w całym przykładową aplikację i mogą pomóc w uzyskaniu przy użyciu biblioteki klienta:

1. Zadeklaruj `array` zawierającą co najmniej jednego wyrażenia agregujące usługi Time Series Insights:

   ```javascript
   var aes =  [];
   ```

1. Tworzenie *1* do *n* agregacji obiektów wyrażeń. Następnie należy dodać je do tablicy wyrażeniu agregującym:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **Parametry aggregateExpression**

   | Parametr | Opis | Przykład |
   | --------- | ----------- | ------- |
   | `predicateObject` | Wyrażenie filtrowania danych |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Nazwa właściwości miary, która jest używana | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Agregacje właściwość miary, która ma | `['avg', 'min']` |
   | `searchSpan`      | Czas trwania i interwał rozmiaru wyrażeniu agregującym | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Właściwość ciągu, którą chcesz podzielić przez (opcjonalny: może mieć wartości null) | `{property: 'Station', type: 'String'}` |
   | `color`         | Kolor obiektów, które ma być renderowany | `'pink'` |
   | `alias`           | Przyjazna nazwa dla wyrażenia agregującego | `'Factory3Temperature'` |
   | `contextMenuActions` | Tablica działania, aby powiązać obiekty serii czasu w wizualizacji (opcjonalnie) | Aby uzyskać więcej informacji, zobacz [menu kontekstowe wyskakującego](#pop-up-context-menus). |

1. Wywołania zapytania usługi Time Series Insights przy użyciu interfejsów API TsiClient.Server żądania zagregowane dane:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **parametry getAggregates**

   | Parametr | Opis | Przykład |
   | --------- | ----------- | ------- |
   | `token`     | Token dostępu dla interfejsu API usługi Insights serii czasu |  `authContext.getTsiToken()`<br />Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). |
   | `envFQDN`   | W pełni kwalifikowana nazwa domeny (FQDN) dla środowiska usługi Time Series Insights | W witrynie Azure portal. Na przykład: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Tablica wyrażenia zapytania usługi Time Series Insights | Użyj `aes` zmiennej zgodnie z wcześniejszym opisem: `aes.map(function(ae){return ae.toTsx()}`. |

1. Przekształcanie skompresowany wynik, który jest zwracany z kwerendy usługi Time Series Insights do postaci JSON wizualizacji:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Tworzenie formantu wykresu za pomocą interfejsów API TsiClient.UX. Powiąż go do jednej z `<div>` elementów na stronie:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Wypełnij formant wykresu przy użyciu przekształconych obiektów danych JSON i renderowania kontrolki na stronie:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Renderowanie kontrolek

Biblioteka klienta usługi Time Series Insights zapewnia osiem formantów unikatowy, out-of--box analytics:

* **Wykres liniowy**
* **Wykres kołowy**
* **Wykres słupkowy**
* **heatmap**
* **Formanty hierarchii**
* **dostępne siatki**
* **Zdarzenie dyskretnych osi czasu**
* **Osie czasu przejścia stanu**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Wykres liniowy, wykres słupkowy i przykłady wykres kołowy

Spójrz na kod wersji demonstracyjnej, który jest używany do renderowania niektóre formanty standardowe wykresu. Należy pamiętać, model programowania i wzorce do tworzenia tych kontrolek. W szczególności sprawdź HTML w obszarze `// Example 3/4/5` komentarz, który renderuje formantów HTML `id` wartości `chart3`, `chart4`, i `chart5`.

Odwołaj z kroku 3 [stronie sekcji źródło i struktury](#page-source-and-structure) czy kontrolek wykresów są rozmieszczone w wierszach, na stronie. Każdy formant wykresu zawiera wiersz opisowy tytuł. W tym przykładzie trzy wykresy są wypełnione w obszarze `Multiple Chart Types From the Same Data` tytuł `<div>` elementu i są powiązane z trzech `<div>` elementy, które znajdują się poniżej tytułu:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

Poniższa sekcja kodu JavaScript wykorzystuje wzorce, które zostały opisane wcześniej: Tworzenie usługi Time Series Insights wyrażeniami dotyczącymi agregacji, ich używać do wykonywania zapytań dla danych usługi Time Series Insights i renderowania trzy wykresy. Trzy typy wykresów są używane z przestrzeni nazw tsiClient.ux: `LineChart`, `BarChart`, i `PieChart`. Typy wykresów są używane do tworzenia i renderowanie odpowiednich wykresów. Wszystkie trzy wykresy można użyć tych samych danych w wyrażeniu agregującym `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Po renderowaniu trzy wykresy wyglądają następująco:

[![Wiele typów wykresów z tym samym zestawem danych](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funkcje zaawansowane

Biblioteka klienta usługi Time Series Insights ma kilka dodatkowych funkcji, które można użyć do zaimplementowania kreatywnie wizualizacji danych.

### <a name="states-and-events"></a>Stany i zdarzenia

Zaawansowane funkcje jest możliwość dodawania stanami i dyskretnych zdarzenia do wykresów. Ta funkcja jest przydatna do wyróżnienia zdarzeń, alertów i tworzenie przełączników stanu (włączenia/wyłączenia przełączników, na przykład).

Przyjrzyj się wokół kodu `// Example 10` komentarz. Ten kod renderuje kontrolkę wiersza pod tytułem `Line Charts with Multiple Series Types` i wiąże go do `<div>` element z kodem HTML `id` wartość `chart10`.

W poniższych krokach opisano proces:

1. Struktury o nazwie `events4` jest definiowana, aby pomieścić elementy zmiany stanu do śledzenia. Struktura zawiera:

   * Klucz będący ciągiem znaków o nazwie `Component States`.
   * Tablicę obiektów wartości reprezentujących stany. Każdy obiekt obejmuje:
     * Klucz będący ciągiem znaków, zawierający znacznik czasu języka JavaScript w standardzie ISO.
     * Tablicę zawierającą właściwości stanu: kolor i opis.

1. `events5` Struktura jest zdefiniowana dla `Incidents`, który zawiera tablicę elementów zdarzenia do śledzenia. Struktura tablicy ma taki sam kształt, jak struktura opisana dla elementu `events4`.

1. Wykres liniowy jest renderowana i przekazuje parametry dwie struktury za pomocą opcji wykresu: `events:` i `states:`. Należy pamiętać, inne parametry opcji do określania `tooltip:`, `theme:`, lub `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Romb znaczniki/podręcznego-dokonywania systemu windows, które są używane do wskazania zdarzenia i kolorowe paski/podręcznego-dokonywania windows wzdłuż osi czasu wskazują zmiany stanu:

[![Wykresy liniowe z wieloma typami serii](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Wyskakujące menu kontekstowe

Inny zaawansowanych funkcji jest możliwość tworzenia niestandardowego menu kontekstowego (menu podręcznym kliknij prawym przyciskiem myszy). Niestandardowe menu kontekstowe są przydatne w przypadku włączania akcji i kolejnych logicznych kroków, znajdujących się w zakresie aplikacji.

Przyjrzyj się kodowi wokół `// Example 13/14/15` komentarz. Ten kod renderuje początkowo wykres liniowy pod tytułem `Line Chart with Context Menu to Create Pie/Bar Chart`. Wykres jest powiązany z `<div>` element z kodem HTML `id` wartość `chart13`.

Dzięki wykorzystaniu menu kontekstowych wykres liniowy umożliwia dynamiczne tworzenie wykresów kołowych i słupkowych powiązanych z elementami `<div>` za pomocą identyfikatorów `chart14` i `chart15`. Wykres kołowy i wykres słupkowy również umożliwiają menu kontekstowe własne funkcje: możliwość kopiowanie danych z wykres kołowy, wykres słupkowy i odpowiednio Drukuj dane wykresu słupkowego w oknie konsoli przeglądarki.

W poniższych krokach opisano proces:

1. Zdefiniowano szereg działań niestandardowych. Każda akcja zawiera tablicę z co najmniej jednym elementem. Każdy element definiuje pojedynczy element menu kontekstowego:

   * `barChartActions`: ta akcja definiuje menu kontekstowe wykresu kołowego, zawierające jeden element określający pojedynczy element menu:
     * `name`: tekst, który jest używany dla elementu menu: „Print parameters to console” (Parametry drukowania do konsoli).
     * `action`: akcja skojarzona z elementem menu. Ta akcja jest zawsze funkcją anonimową, przyjmującą trzy argumenty oparte na wyrażeniu agregującym używanym do tworzenia wykresu. W tym przypadku argumenty są zapisywane w oknie konsoli przeglądarki:
       * `ae`: tablica wyrażeń agregujących.
       * `splitBy`: `splitBy` Wartość.
       * `timestamp`: znacznik czasu.

   * `pieChartActions`: ta akcja definiuje menu kontekstowe wykresu słupkowego, zawierające jeden element określający pojedynczy element menu. Kształt, a schemat jest taka sama jak `barChartActions` elementu opisanego wcześniej, ale `action` funkcja różni się znacznie: tworzy wystąpienia i renderuje wykres słupkowy. `ae` Argument jest używany do określenia wyrażeniu agregującym tablicy, który jest przekazywany w czasie wykonywania, po otwarciu elementu menu. Funkcja ustawia również właściwość `ae.contextMenu` za pomocą menu kontekstowego `barChartActions`.
   * `contextMenuActions`: ta akcja definiuje menu kontekstowe wykresu liniowego, zawierające trzy elementy określające trzy elementy menu. Kształt i schematu dla każdego elementu jest taka sama, jak elementy, które zostały opisane wcześniej. Podobnie jak dla elementu `barChartActions`, pierwszy element zapisuje trzy argumenty funkcji w oknie konsoli przeglądarki. Podobnie jak `pieChartActions` elementu, Utwórz wystąpienie dwa kolejne elementy i odpowiednio renderowania wykresu kołowego i wykres słupkowy. Następne dwa elementy ustawiają również swoje właściwości `ae.contextMenu` poprzez menu kontekstowe odpowiednio `pieChartActions` i `barChartActions`.

1. Dwoma wyrażeniami dotyczącymi agregacji są wypychane na `aes` wyrażeniu agregującym tablicy. Określają one `contextMenuActions` tablicy dla każdego elementu. Wyrażenia są używane przez kontrolkę wykresu liniowego.

1. Ten wykres liniowy jest początkowo renderowany, z którego wykresu kołowego i wykres słupkowy, może być renderowany w czasie wykonywania.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Poniższy zrzut ekranu przedstawia wykresy z ich odpowiedniego kontekstu wyskakujących menu. Wykres kołowy z wykresem słupkowym, zostały utworzone dynamicznie przy użyciu opcji menu kontekstowe wykresu wiersza.

[![Wykres liniowy z menu kontekstowego, aby utworzyć wykres kołowy i wykres słupkowy](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pędzle

Pędzle służy do określania zakresu zakres czasu do zdefiniowania akcji, takich jak powiększenia i zapoznaj się z.

Kod, który jest używany w celu zilustrowania pędzle jest wyświetlany w `Line Chart with Context Menu to Create Pie/Bar Chart` przykładu, który opisuje kontekst wyskakujących menu.

* Akcje związane z pędzlem są bardzo podobne do menu kontekstowego w tym, że definiują dla pędzla szereg akcji niestandardowych. Każda akcja zawiera tablicę, która ma co najmniej jeden element. Każdy element definiuje pojedynczy element menu kontekstowego:
   * `name`: tekst, który jest używany dla elementu menu: „Print parameters to console” (Parametry drukowania do konsoli).
   * `action`: skojarzona z elementem menu akcja, która jest zawsze funkcją anonimową, przyjmującą dwa argumenty. W tym przypadku argumenty są zapisywane w oknie konsoli przeglądarki:
     * `fromTime`: `from` Sygnaturę czasową wybór pędzla.
     * `toTime`: `to` Sygnaturę czasową wybór pędzla.

* Akcje pędzla są dodawane jako jeszcze jedna właściwość opcji wykresu. `brushContextMenuActions: brushActions` Właściwość jest przekazywana do `linechart.Render` wywołania.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Wykres liniowy z menu kontekstowego, aby utworzyć wykres kołowy i wykres słupkowy przy użyciu pędzli](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zaloguj się i eksplorowanie przykładowej aplikacji usługi Time Series Insights i jego źródło
> * Użyj interfejsów API w bibliotece klienckiej JavaScript Insights serii czasu
> * Tworzenie i wypełnianie kontrolek wykresów z danymi usługi Time Series Insights za pomocą języka JavaScript

Usługa Time Series Insights Przykładowa aplikacja korzysta z zestawu danych demonstracyjnych. Aby dowiedzieć się, jak można tworzyć własne środowiska usługi Time Series Insights oraz zestawu danych, przeczytaj następujący artykuł:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie środowiska usługi Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

Można także wyświetlić pliki źródłowe aplikacji przykładowej usługi Time Series Insights:

> [!div class="nextstepaction"]
> [Czas Series Insights przykładowe repozytorium aplikacji](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Przeczytaj dokumentację referencyjną interfejsu API klienta usługi Time Series Insights:

> [!div class="nextstepaction"]
> [Dokumentacja referencyjna interfejsu API usługi Insights serii czasu](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
