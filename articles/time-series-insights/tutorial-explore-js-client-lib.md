---
title: 'Samouczek: Poznawanie biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights | Microsoft Docs'
description: Informacje na temat biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights i powiązanego z nią modelu programowania.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: a91afdbeaa2ced37b237b4f2b80a8dbbe2c4a05c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717225"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Samouczek: Poznawanie biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights

Aby wspomóc deweloperów sieci Web w tworzeniu zapytań i wizualizacji danych przechowywanych w usłudze Time Series Insights (TSI), opracowana została biblioteka klienta usługi TSI, oparta na bibliotece D3 języka JavaScript. Ten samouczek przeprowadzi Cię przy użyciu biblioteki klienta usługi TSI i modelu programowania przy użyciu hostowanego przykładową aplikację.

Samouczek zawiera szczegółowe informacje dotyczące pracy z biblioteką, jak uzyskać dostęp do danych usługi TSI i Użyj kontrolek wykresu do renderowania i wizualizacji danych. Będzie również sposób do eksperymentowania z różnymi rodzajami wykresów do wizualizacji danych. Po zakończeniu samouczka będziesz mieć możliwość włączenie funkcji usługi TSI do aplikacji sieci web za pomocą biblioteki klienta.

W szczególności poznasz:

> [!div class="checklist"]
> * Przykładowej aplikacji usługi TSI.
> * Biblioteki klienta języka JavaScript dla usługi TSI.
> * Sposobu, w jaki przykładowa aplikacja korzysta z biblioteki w celu wizualizacji danych w usłudze TSI.

> [!NOTE]
> * W tym samouczku użyto bezpłatnej, hostowaną [pokaz sieci web usługi Time Series Insights](https://insights.timeseries.azure.com/clientsample).
> * Pliki źródłowe aplikacji przykładowej usługi Time Series Insights znajdują się w [repozytorium przykładów GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

## <a name="video"></a>Połączenia wideo

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>W tym filmie przedstawiamy zestaw SDK JavaScript typu „open source” dla usługi Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku jest używana w przeglądarce **narzędzi deweloperskich** funkcji. Współczesne przeglądarki internetowe obsługują ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)i inne) zwykle zapewniają dostęp do **Widoku inspektora sieci web** za pośrednictwem `F12` klawiszy skrótu. W przeciwnym razie jest dostępny, kliknij prawym przyciskiem myszy na stronie sieci Web i wybierając **sprawdzić Element**.

## <a name="time-series-insights-sample-application"></a>Przykładowa aplikacja usługi Time Series Insights

W tym samouczku bezpłatnej, hostowaną aplikację przykładową usługi Time Series Insights jest używany do Eksploruj kod źródłowy aplikacji i biblioteki klienta JavaScript usługi TSI. Za jego pośrednictwem dowiesz się, jak korzystać z usługi TSI w języku JavaScript i wizualizowanie danych za pomocą wykresów i schematów.

1. Przejdź do [przykładowej aplikacji usługi Time Series Insights](https://insights.timeseries.azure.com/clientsample). Pojawi się następujący znak w wierszu polecenia:

   [![Monit logowania przykładowe TSI klienta](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Wybierz **Zaloguj** wprowadź lub wybierz swoje poświadczenia. Użyj konta organizacji przedsiębiorstwa (Azure Active Directory) lub konto osobiste (Account firmy Microsoft lub konta Microsoft).

   [![Monitowanie o poświadczenie przykładowe TSI klienta](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Po podpisywania, zostanie wyświetlona strona z różnych typów wykresów wypełniony danymi TSI. Twoje konto użytkownika i opcja **Wyloguj się** są widoczne w prawym górnym rogu:

   [![Strona główna przykładowej usługi TSI klienta po zalogowaniu](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Źródło i struktura strony

<div id="page-source-and-structure"></div>

Po pierwsze, teraz wyświetlić [kod źródłowy w kodzie HTML i JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) sieci Web renderowanych stronicowanej:

1. Otwórz w przeglądarce funkcję **Narzędzia deweloperskie**. Zbadaj elementy HTML wchodzące w skład bieżącej strony (określane również jako drzewo HTML lub drzewo DOM).

1. Rozwiń `<head>` i `<body>` elementy i sprawdź, czy następujące sekcje:

   * W obszarze `<head>` elementu, można znaleźć metadanych strony i zależności, które umożliwiają uruchomienie aplikacji:
     * Element `<script>`, który jest używany w celu odwołania do biblioteki Azure Active Directory Authentication Library **adal.min.js** (znanej także jako ADAL). ADAL to biblioteka języka JavaScript, która udostępnia funkcje uwierzytelniania w standardzie OAuth 2.0 (logowanie) oraz uzyskanie tokenu pozwalającego na dostęp do interfejsów API.
     * Wiele elementów `<link>` dla arkuszy stylów (znanych także jako CSS), takich jak **sampleStyles.css** i **tsiclient.css**. Arkusze stylów są używane do kontrolowania szczegółów stylu dotyczących wizualizacji strony, takich jak kolory, czcionki, odstępy itd.
     * Element `<script>`, który jest używany w celu odwołania do biblioteki języka JavaScript klienta TSI **tsiclient.js**. Ta biblioteka jest używana przez stronę do wywoływania interfejsów API usługi TSI i renderowania kontrolek wykresów na stronie.

     >[!NOTE]
     > * Kod źródłowy biblioteki ADAL w języku JavaScript jest dostępny w [repozytorium azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Kod źródłowy biblioteki klienta usługi TSI w języku JavaScript jest dostępny w [repozytorium tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * W obszarze `<body>` elementu, można znaleźć `<div>` elementów, które pomagają zdefiniować układ elementów na stronie, a druga `<script>` elementu:
     * Pierwszy element `<div>` określa okno dialogowe **Logowanie** (`id="loginModal"`).
     * Drugi element `<div>` pełni rolę elementu nadrzędnego dla następujących elementów:
       * Elementu nagłówkowego `<div>`, zawierającego komunikaty o stanie i informacje dotyczące logowania, wyświetlane w górnej części strony (`class="header"`).
       * Elementu `<div>` przeznaczonego dla pozostałych elementów treści strony, łącznie z wszystkimi wykresami (`class="chartsWrapper"`).
       * Sekcji `<script>`, która zawiera wszystkie elementy języka JavaScript używane do sterowania działaniem strony.

   [![Klient usługi TSI w funkcji Narzędzia deweloperskie — przykład](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Rozwiń `<div class="chartsWrapper">` elementu w którym znajdziesz więcej podrzędnych `<div>` elementów. Te elementy są używane do określenia położenia każdej przykładowej kontrolki wykresu. Zwróć uwagę, że dla każdego przykładowego wykresu istnieje kilka par elementów `<div>`:

   * Pierwszy element (`class="rowOfCardsTitle"`) zawiera opisowy tytuł podsumowujący, co przedstawia wykres. Na przykład: `Static Line Charts With Full-Size Legends.`
   * Drugi element (`class="rowOfCards"`) jest elementem nadrzędnym, zawierającym dodatkowe elementy podrzędne `<div>`, które są odpowiedzialne za umieszczenie w wierszu rzeczywistych kontrolek wykresów.

   [![Elementy div treści](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Teraz rozwiń element `<script type="text/javascript">` znajdujący się bezpośrednio pod elementem `<div class="chartsWrapper">`. Zwróć uwagę na początek sekcji JavaScript na poziomie strony, która jest używana do obsługi całą logikę strony (uwierzytelnianie podczas wywoływania usługi TSI interfejsów API, renderowania kontrolek wykresów i inne):

   [![Treść skryptu](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="tsi-javascript-client-library-concepts"></a>Pojęcia związane z biblioteką klienta języka JavaScript dla usługi TSI

Biblioteka klienta usługi TSI (**tsclient.js**) zawiera elementy abstrakcji do dwie ważne funkcje języka JavaScript:

* **Metody otoki na potrzeby wywoływania interfejsów API zapytań usługi TSI**: interfejsy API REST, które umożliwiają wykonywanie zapytań dotyczących danych usługi TSI przy użyciu wyrażeń agregowanych. Te metody są zorganizowane w przestrzeni nazw `TsiClient.Server` biblioteki.

* **Metody tworzenia i wypełniania kilku typów kontrolek wykresów**: metody, które są używane do renderowania zagregowanych danych TSI na stronie internetowej. Te metody są zorganizowane w przestrzeni nazw `TsiClient.UX` biblioteki.

Za pomocą tych definiowaniu deweloperzy mogą tworzyć składniki programu graph i wykres interfejsu użytkownika, które są obsługiwane przy użyciu danych z usługi TSI łatwiejsze.

### <a name="authentication"></a>Authentication

[Usługi Time Series Insights Przykładowa aplikacja](https://insights.timeseries.azure.com/clientsample) jest jednostronicowej aplikacji z obsługą uwierzytelniania użytkownika ADAL OAuth 2.0:

1. Podczas uwierzytelniania przy użyciu biblioteki ADAL, aplikacja kliencka musi być zarejestrowany w usłudze Azure Active Directory. W rzeczywistości zarejestrowana aplikacja jednostronicowa w celu używania [OAuth 2.0 niejawne udzielić przepływ](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. W efekcie aplikacji należy określić niektóre właściwości rejestrowania w czasie wykonywania. Obejmują one identyfikator GUID klienta (`clientId`) i identyfikator URI przekierowania (`postLogoutRedirectUri`).
1. Później, aplikacja prosi **token dostępu** z usługi Azure Active Directory. Token dostępu został wystawiony dla skończony zestaw uprawnień dla identyfikatora określonego usługę/interfejs API (`https://api.timeseries.azure.com`). Uprawnienia tokenu są wystawiane w imieniu zalogowanego użytkownika. Identyfikator usługi/interfejsu API jest inną właściwość, która znajduje się w aplikacji usługi Azure Active Directory rejestracji.
1. Po ADAL zwraca token dostępu do aplikacji, jest przekazywany jako **tokenu elementu nośnego** podczas uzyskiwania dostępu do TSI interfejsów API usługi.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

### <a name="control-identification"></a>Identyfikacja kontrolki

W podanym przykładzie `<div>` elementy są uporządkowane w obiekcie nadrzędnym `<body>` elementu, aby zapewnić rozsądne układu dla wszystkich kontrolek wykresu renderowane na stronie.

Każdy `<div>` element określa właściwości umieszczania i atrybuty visual kontrolek wykresu. HTML element `id` właściwości służyć jako unikatowych identyfikatorów, które można powiązać do określonych formantów do renderowania i aktualizowania ułatwia wizualizowanie danych.

### <a name="aggregate-expressions"></a>Wyrażenia agregujące

Biblioteka klienta usługi TSI interfejsów API Użyj wyrażeniami dotyczącymi agregacji:

* Wyrażenie agregujące oferuje możliwość skonstruowania co najmniej jeden **terminy wyszukiwania**.

* Interfejsy API klienta są przeznaczone do zapewniają podobne funkcje do innej aplikacji pokaz Podaj ( [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com/demo)), który używa zakresu wyszukiwania, predykat where, miar i podział według wartości.

* Większość interfejsów API biblioteki klienckiej zająć tablicę wyrażeniami dotyczącymi agregacji, które są używane przez usługę do skonstruowania danych usługa TSI Przesyła zapytania.

### <a name="call-pattern"></a>Wzorzec wywołania

Podczas wypełniania i renderowanie kontrolek wykresu jest zgodna ze wzorcem ogólne. Ten wzorzec ogólne można obserwować w całym przykładową aplikację i pomoże Ci, gdy za pomocą biblioteki klienckiej:

1. Zadeklaruj obiekt `array` do przechowywania co najmniej jednego wyrażenia agregującego usługi TSI:

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
   | `predicateObject` | Wyrażenie filtrowania danych. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Nazwa właściwości odnoszącej się do używanej miary. | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Żądane agregacje właściwości miary. | `['avg', 'min']` |
   | `searchSpan`      | Czas trwania i wielkość interwału wyrażenia agregującego. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Właściwość określająca ciąg znaków, którego chcesz użyć do podziału (opcjonalna — może mieć wartość null). | `{property: 'Station', type: 'String'}` |
   | `color`         | Kolor obiektów, który chcesz zastosować podczas renderowania. | `'pink'` |
   | `alias`           | Przyjazna nazwa wyrażenia agregującego. | `'Factory3Temperature'` |
   | `contextMenuActions` | Tablica akcji, która ma być powiązana z obiektami szeregów czasowych w wizualizacji (opcjonalnie). | Aby uzyskać więcej informacji, zobacz sekcję [menu kontekstowe wyskakujących](#contextMenu) |

1. Wywołaj zapytanie usługi TSI przy użyciu interfejsów API `TsiClient.Server`, aby zażądać dostępu do zagregowanych danych:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **parametry getAggregates**

   | Parametr | Opis | Przykład |
   | --------- | ----------- | ------- |
   | `token`     | Token dostępu do interfejsu API usługi TSI. |  `authContext.getTsiToken()` Aby uzyskać więcej informacji, zobacz [sekcję dotyczącą uwierzytelniania.](#authentication) |
   | `envFQDN`   | W pełni kwalifikowana nazwa domeny (FQDN) dla środowiska usługi TSI. | Na przykład w witrynie Azure Portal: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Tablica wyrażeń zapytań usługi TSI. | Użyj zmiennej `aes` zgodnie z wcześniejszym opisem: `aes.map(function(ae){return ae.toTsx()}`. |

1. Na potrzeby wizualizacji przekształć zwrócony przez zapytanie usługi TSI skompresowany wynik do postaci JSON:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Utwórz kontrolkę wykresu przy użyciu interfejsów API `TsiClient.UX` i powiąż ją z jednym z elementów `<div>` na stronie:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Wypełnij kontrolkę wykresu przekształconymi obiektami danych JSON i renderuj ją na stronie:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Renderowanie kontrolek

Biblioteka kliencka TSI zawiera osiem formantów unikatowy, out-of--box, analizy:

* **Wykres liniowy**
* **Wykres kołowy**
* **Wykres słupkowy**
* **heatmap**
* **Formanty hierarchii**
* **dostępne siatki**
* **Zdarzenie dyskretnych osi czasu**
* **Osie czasu przejścia stanu**

### <a name="line-bar-pie-chart-examples"></a>Przykłady wykresów liniowych, słupkowych i kołowych

Spójrz na kod wersji demonstracyjnej, używany do renderowania, niektóre z formantu standardowego wykresu. Zwróć uwagę, model programowania i wzorce do tworzenia tych kontrolek. W szczególności Sprawdź sekcję kodu HTML, w obszarze `// Example 3/4/5` komentarz, który renderuje formantów HTML `id` wartości `chart3`, `chart4`, i `chart5`.

Jak pamiętamy z artykułu **kroku 3** z [stronie sekcji źródło i struktury](#page-source-and-structure) czy kontrolek wykresów są rozmieszczone w wierszach, na stronie, z których każdy ma wiersz opisowy tytuł. W tym przykładzie trzy wykresy zostaną wypełnione pod tytułem `"Multiple Chart Types From the Same Data"` `<div>` elementu i są powiązane z trzech `<div>` elementy znajdujące się poniżej tytułu:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

Następująca sekcja kodu języka JavaScript korzysta z przedstawionych wcześniej wzorców: tworzy wyrażenia agregujące usługi TSI, wykorzystuje je do wykonania zapytań do danych usługi TSI oraz renderuje trzy wykresy. Zwróć uwagę na wykorzystane trzy typy z przestrzeni nazw `tsiClient.ux`: `LineChart`, `BarChart`, `PieChart` do utworzenia i renderowania odpowiednich wykresów. Zauważ także, że wszystkie trzy wykresy mogą korzystać z tych samych danych wyrażeń agregujących `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Po renderowaniu trzy wykresy wyglądają następująco:

[![Wiele typów wykresów z tym samym zestawem danych](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funkcje zaawansowane

Biblioteka klienta usługi TSI ma kilka dodatkowych funkcji służących do zaimplementowania kreatywnie wizualizacji danych.

### <a name="states-and-events"></a>Stany i zdarzenia

Jednym z zaawansowanych funkcji jest możliwość dodawania stanami i dyskretnych zdarzenia do wykresów. Ta funkcja jest przydatna do wyróżnienia zdarzeń, alertów i tworzenie przełączników stanu (włączenia/wyłączenia przełączników, na przykład).

Przyjrzyj się wokół kodu `// Example 10` komentarz. Ten kod renderuje kontrolkę wiersza pod tytułem `"Line Charts with Multiple Series Types"`i wiąże go do `<div>` element z kodem HTML `id` wartość `chart10`.

1. Po pierwsze, strukturę o nazwie `events4` jest definiowana, aby pomieścić elementy zmiany stanu do śledzenia. Struktura zawiera:

   * Klucz będący ciągiem znaków o nazwie `Component States`.
   * Tablicę obiektów wartości reprezentujących stany. Każdy obiekt obejmuje:
     * Klucz będący ciągiem znaków, zawierający znacznik czasu języka JavaScript w standardzie ISO.
     * Tablicę zawierającą właściwości stanu: kolor i opis.

2. Następnie `events5` struktura jest zdefiniowana dla `Incidents`, który zawiera tablicę elementów zdarzenia do śledzenia. Struktura tablicy ma taki sam kształt, jak struktura opisana dla elementu `events4`.

3. Ostatecznie podczas renderowania wykresu liniowego obie struktury są przekazywane przy użyciu parametrów opcji wykresu: `events:` i `states:`. Zwróć uwagę na inne parametry opcji służące do określenia `tooltip:`, `theme:` lub `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Do wskazywania incydentów używane są znaczniki w postaci rombu lub wyskakujące okienka, a kolorowe paski lub wyskakujące okienka wzdłuż osi czasu wskazują zmiany stanu:

[![Wykresy liniowe z wieloma typami serii danych](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Wyskakujące menu kontekstowe

<div id="contextMenu"></div>

Inny zaawansowanych funkcji jest możliwość tworzenia niestandardowego menu kontekstowego (menu podręcznym kliknij prawym przyciskiem myszy). Niestandardowe menu kontekstowe są przydatne w przypadku włączania akcji i kolejnych logicznych kroków, znajdujących się w zakresie aplikacji.

Poszukaj kodu `// Example 13/14/15` komentarz. Ten kod renderuje początkowo wykres liniowy pod tytułem `"Line Chart with Context Menu to Create Pie/Bar Chart"` a wykresu jest powiązany z `<div>` element z kodem HTML `id` wartość `chart13`.

Dzięki wykorzystaniu menu kontekstowych wykres liniowy umożliwia dynamiczne tworzenie wykresów kołowych i słupkowych powiązanych z elementami `<div>` za pomocą identyfikatorów `chart14` i `chart15`. Ponadto wykresy kołowy i słupkowy również umożliwiają wykorzystanie menu kontekstowych do użycia własnych funkcji: możliwości kopiowania danych z wykresu kołowego do wykresu słupkowego i drukowania danych wykresu słupkowego w oknie konsoli przeglądarki.

1. Najpierw definiowana jest seria akcji niestandardowych. Każda akcja zawiera tablicę z co najmniej jednym elementem. Każdy element definiuje pojedynczy element menu kontekstowego:

   * `barChartActions`: ta akcja definiuje menu kontekstowe wykresu kołowego, zawierające jeden element określający pojedynczy element menu:
     * `name`: tekst, który jest używany dla elementu menu: „Print parameters to console” (Parametry drukowania do konsoli).
     * `action`: akcja skojarzona z elementem menu. Ta akcja jest zawsze funkcją anonimową, przyjmującą trzy argumenty oparte na wyrażeniu agregującym używanym do tworzenia wykresu. W tym przypadku argumenty są zapisywane w oknie konsoli przeglądarki:
       * `ae`: tablica wyrażeń agregujących.
       * `splitBy`: `splitBy` Wartość.
       * `timestamp`: znacznik czasu.

   * `pieChartActions`: ta akcja definiuje menu kontekstowe wykresu słupkowego, zawierające jeden element określający pojedynczy element menu. Kształt i schemat są takie same jak w przypadku poprzedniego elementu `barChartActions`, należy jednak zauważyć, że funkcja `action` jest zupełnie inna, ponieważ tworzy i renderuje wykres słupkowy. Należy także zauważyć, że argument `ae` jest wykorzystany do określenia tablicy wyrażeń agregujących przekazanej w czasie wykonywania w momencie otwarcia elementu menu. Funkcja ustawia również właściwość `ae.contextMenu` za pomocą menu kontekstowego `barChartActions`.
   * `contextMenuActions`: ta akcja definiuje menu kontekstowe wykresu liniowego, zawierające trzy elementy określające trzy elementy menu. Kształt i schemat dla każdego elementu są takie same jak w przypadku poprzednio opisanych elementów. Podobnie jak dla elementu `barChartActions`, pierwszy element zapisuje trzy argumenty funkcji w oknie konsoli przeglądarki. Podobnie jak dla elementu `pieChartActions`, kolejne dwa elementy tworzą i renderują odpowiednio wykresy kołowy i słupkowy. Następne dwa elementy ustawiają również swoje właściwości `ae.contextMenu` poprzez menu kontekstowe odpowiednio `pieChartActions` i `barChartActions`.

2. Następnie dwa wyrażenia agregujące są wypychane do tablicy wyrażeń agregujących `aes` i określają tablicę `contextMenuActions` dla każdego z nich. Wyrażenia są używane przez kontrolkę wykresu liniowego.

3. Ostatecznie tylko wykres liniowy jest renderowany inicjalnie, a z niego w czasie wykonywania mogą być renderowane wykresy kołowy i słupkowy.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Zrzut ekranu pokazuje wykresy wraz z właściwymi dla nich wyskakującymi menu kontekstowymi. Wykresy kołowy i słupkowy zostały utworzone dynamicznie przy użyciu opcji menu kontekstowego wykresu liniowego.

[![Wykres liniowy z menu kontekstowym umożliwiającym utworzenie wykresu kołowego/słupkowego](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pędzle

Pędzle są używane do określania zakresu czasu w celu definiowania akcji, takich jak powiększanie i eksplorowanie.

Kod używany w celu zademonstrowania działania pędzli jest pokazany w poprzednim przykładzie „Wykres liniowy z menu kontekstowym umożliwiającym utworzenie wykresu kołowego/słupkowego”, dotyczącym wyskakujących menu kontekstowych.

1. Akcje związane z pędzlem są bardzo podobne do menu kontekstowego w tym, że definiują dla pędzla szereg akcji niestandardowych. Każda akcja zawiera tablicę z co najmniej jednym elementem. Każdy element definiuje pojedynczy element menu kontekstowego:
   * `name`: tekst, który jest używany dla elementu menu: „Print parameters to console” (Parametry drukowania do konsoli).
   * `action`: skojarzona z elementem menu akcja, która jest zawsze funkcją anonimową, przyjmującą dwa argumenty. W tym przypadku argumenty są zapisywane w oknie konsoli przeglądarki:
      * `fromTime`: `from` Sygnaturę czasową wybór pędzla.
      * `toTime`: `to` Sygnaturę czasową wybór pędzla.

2. Akcje pędzla są dodawane jako jeszcze jedna właściwość opcji wykresu. Zwróć uwagę na właściwość `brushContextMenuActions: brushActions` przekazywaną do wywołania `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Wykres liniowy z menu kontekstowego, aby utworzyć wykres kołowy/słupkowy przy użyciu pędzli](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Logowanie i eksplorowanie przykładowej aplikacji usługi TSI i jej źródła.
> * Korzystanie z interfejsów API w bibliotece klienta języka JavaScript dla usługi TSI.
> * Tworzenie i wypełnianie kontrolek wykresu z użyciem danych usługi TSI za pomocą języka JavaScript.

Jak widać, usługa TSI Przesyła Przykładowa aplikacja korzysta z zestawem danych demonstracyjnych. Aby dowiedzieć się więcej na temat tworzenia własnego środowiska i zestawu danych w usłudze TSI, należy przejść do następującego artykułu:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie środowiska usługi Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

Lub Wyświetl pliki źródłowe TSI przykładowej aplikacji:

> [!div class="nextstepaction"]
> [Usługa TSI Przesyła przykładowe repozytorium aplikacji](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)
