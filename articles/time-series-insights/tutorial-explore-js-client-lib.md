---
title: 'Samouczek: Poznawanie biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights | Microsoft Docs'
description: Informacje na temat biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights i powiązanego z nią modelu programowania.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 5e3005eb8f548e562e037431ae5fd89f82ec2100
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60330813"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Samouczek: Poznawanie biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights

Aby wspomóc deweloperów sieci Web w tworzeniu zapytań i wizualizacji danych przechowywanych w usłudze Time Series Insights (TSI), opracowana została biblioteka klienta usługi TSI, oparta na bibliotece D3 języka JavaScript.  Ten samouczek umożliwia poznanie biblioteki klienta języka JavaScript dla usługi TSI i powiązanego z nią modelu programowania przy użyciu przykładowej aplikacji sieci Web.

Omawiane tematy dają możliwości eksperymentowania z biblioteką oraz lepszego zrozumienia sposobu, w jaki można uzyskać dostęp do danych usługi TSI oraz używać kontrolek wykresu w celu renderowania i wizualizacji danych. Celem jest dostarczenie użytkownikowi wystarczającej ilości szczegółowych informacji, które pozwolą na wykorzystanie biblioteki we własnych aplikacjach sieci Web.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
> * Przykładowej aplikacji usługi TSI.
> * Biblioteki klienta języka JavaScript dla usługi TSI.
> * Sposobu, w jaki przykładowa aplikacja korzysta z biblioteki w celu wizualizacji danych w usłudze TSI.

> [!NOTE]
> Pliki źródłowe aplikacji przykładowej usługi Time Series Insights można znaleźć w określonych [repozytorium przykładów GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

## <a name="video"></a>Wideo: 

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>W tym filmie przedstawiamy zestaw SDK JavaScript typu „open source” dla usługi Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek korzysta z funkcji „Narzędzia deweloperskie” (znanej także jako DevTools lub F12), którą można znaleźć w większości nowoczesnych przeglądarek internetowych, takich jak [Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) i inne. Jeśli jeszcze jej nie znasz, warto zbadać tę funkcję w swojej przeglądarce, zanim przejdziesz dalej.

## <a name="time-series-insights-sample-application"></a>Przykładowa aplikacja usługi Time Series Insights

W tym samouczku przykładowa aplikacja usługi Time Series Insights jest wykorzystywana do badania kodu źródłowego aplikacji, w tym części odpowiedzialnej za użycie biblioteki klienta języka JavaScript dla usługi TSI. Jako przykład służy aplikacja jednostronicowa sieci web (SPA, single-page application), która ilustruje sposób korzystania z biblioteki. Przykład przedstawia sposób wykonywania zapytań i wizualizacji danych z przykładowego środowiska usługi TSI.

1. Przejdź do [przykładowej aplikacji usługi Time Series Insights](https://insights.timeseries.azure.com/clientsample). Zobaczysz stronę zawierającą monit o zalogowanie, podobną do poniższej ilustracji:

   ![Przykładowy monit o zalogowanie klienta usługi TSI](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Wybierz opcję **Zaloguj** i wprowadź lub wybierz swoje poświadczenia. Możesz użyć konta przedsiębiorstwa lub organizacji (Azure Active Directory) lub konta osobistego (konto Microsoft lub MSA).

   ![Przykładowy monit o wprowadzenie poświadczeń klienta usługi TSI](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Po pomyślnym zalogowaniu zobaczysz stronę podobną do poniższej ilustracji. Na stronie pokazano różne style przykładowych wykresów, które zostały wypełnione przy użyciu danych usługi TSI. Twoje konto użytkownika i opcja **Wyloguj się** są widoczne w prawym górnym rogu:

   ![Przykładowa strona główna klienta usługi TSI po zalogowaniu](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Źródło i struktura strony

Po pierwsze, teraz wyświetlić [kod źródłowy w kodzie HTML i JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) to tła strony, który jest renderowany w przeglądarce. Nie będziemy przechodzić przez wszystkie elementy, ale poznasz główne sekcje, co pozwoli Ci zorientować się, jak działa strona:

1. Otwórz w przeglądarce funkcję **Narzędzia deweloperskie**. Zbadaj elementy HTML wchodzące w skład bieżącej strony (określane również jako drzewo HTML lub drzewo DOM).

2. Rozwiń elementy `<head>` i `<body>` i zwróć uwagę na następujące sekcje:

   - W ramach elementu `<head>` możesz znaleźć elementy, które odpowiadają za ściągnięcie dodatkowych plików biorących udział w działaniu strony:
     - Element `<script>`, który jest używany w celu odwołania do biblioteki Azure Active Directory Authentication Library **adal.min.js** (znanej także jako ADAL). ADAL to biblioteka języka JavaScript, która udostępnia funkcje uwierzytelniania w standardzie OAuth 2.0 (logowanie) oraz uzyskanie tokenu pozwalającego na dostęp do interfejsów API.
     - Wiele elementów `<link>` dla arkuszy stylów (znanych także jako CSS), takich jak **sampleStyles.css** i **tsiclient.css**. Arkusze stylów są używane do kontrolowania szczegółów stylu dotyczących wizualizacji strony, takich jak kolory, czcionki, odstępy itd.
     - Element `<script>`, który jest używany w celu odwołania do biblioteki języka JavaScript klienta TSI **tsiclient.js**. Ta biblioteka jest używana przez stronę do wywoływania interfejsów API usługi TSI i renderowania kontrolek wykresów na stronie.

     >[!NOTE]
     > Kod źródłowy biblioteki ADAL w języku JavaScript jest dostępny w [repozytorium azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > Kod źródłowy biblioteki klienta usługi TSI w języku JavaScript jest dostępny w [repozytorium tsiclient](https://github.com/Microsoft/tsiclient).

   - W ramach elementu `<body>` możesz znaleźć elementy `<div>`, które pełnią rolę kontenerów definiujących układ elementów na stronie, oraz kolejny element `<script>`:
     - Pierwszy element `<div>` określa okno dialogowe **Logowanie** (`id="loginModal"`).
     - Drugi element `<div>` pełni rolę elementu nadrzędnego dla następujących elementów:
       - Elementu nagłówkowego `<div>`, zawierającego komunikaty o stanie i informacje dotyczące logowania, wyświetlane w górnej części strony (`class="header"`).
       - Elementu `<div>` przeznaczonego dla pozostałych elementów treści strony, łącznie z wszystkimi wykresami (`class="chartsWrapper"`).
       - Sekcji `<script>`, która zawiera wszystkie elementy języka JavaScript używane do sterowania działaniem strony.

   [![Klient usługi TSI w funkcji Narzędzia deweloperskie — przykład](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Rozwiń element `<div class="chartsWrapper">` i znajdź więcej podrzędnych elementów `<div>`. Te elementy są używane do określenia położenia każdej przykładowej kontrolki wykresu. Zwróć uwagę, że dla każdego przykładowego wykresu istnieje kilka par elementów `<div>`:

   - Pierwszy element (`class="rowOfCardsTitle"`) zawiera opisowy tytuł podsumowujący, co przedstawia wykres. Na przykład: „Static Line Charts With Full-Size Legends” (Statyczne wykresy liniowe z pełnowymiarową legendą).
   - Drugi element (`class="rowOfCards"`) jest elementem nadrzędnym, zawierającym dodatkowe elementy podrzędne `<div>`, które są odpowiedzialne za umieszczenie w wierszu rzeczywistych kontrolek wykresów.

   ![Elementy div treści](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Teraz rozwiń element `<script type="text/javascript">` znajdujący się bezpośrednio pod elementem `<div class="chartsWrapper">`. Zwróć uwagę na początek sekcji języka JavaScript na poziomie strony, używanej do kompletnej obsługi logiki strony: uwierzytelniania, wywoływania interfejsów API usługi TSI, renderowania kontrolek wykresów i innych operacji:

   ![Treść — skrypt](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-javascript-client-library-concepts"></a>Pojęcia związane z biblioteką klienta języka JavaScript dla usługi TSI

Mimo że nie przeglądamy szczegółowo biblioteki klienta usługi TSI **tsclient.js**, należy zwrócić uwagę na fakt, że udostępnia ona abstrakcję dla dwóch ważnych kategorii:

- **Metody otoki na potrzeby wywoływania interfejsów API zapytań usługi TSI**: interfejsy API REST, które umożliwiają wykonywanie zapytań dotyczących danych usługi TSI przy użyciu wyrażeń agregowanych. Te metody są zorganizowane w przestrzeni nazw `TsiClient.Server` biblioteki.
- **Metody tworzenia i wypełniania kilku typów kontrolek wykresów**: metody, które są używane do renderowania zagregowanych danych TSI na stronie internetowej. Te metody są zorganizowane w przestrzeni nazw `TsiClient.UX` biblioteki.

Poniższe pojęcia są uniwersalne i mają ogólne zastosowanie do interfejsów API biblioteki klienta usługi TSI.

### <a name="authentication"></a>Authentication

Jak wspomniano wcześniej, ten przykład stanowi aplikację jednostronicową i korzysta z obsługi protokołu OAuth 2.0 w bibliotece ADAL do uwierzytelniania użytkowników. Poniżej wymieniono niektóre interesujące kwestie dotyczące tej sekcji skryptu:

1. Uwierzytelnianie za pomocą biblioteki ADAL wymaga, aby aplikacja kliencka rejestrowała się samodzielnie w rejestrze aplikacji usługi Azure Active Directory (Azure AD). Jako aplikacja jednostronicowa jest ona zarejestrowana do korzystania z przepływu „niejawnego” autoryzacji OAuth 2.0. Odpowiednio aplikacja określa niektóre właściwości rejestrowania w środowisku uruchomieniowym, takie jak identyfikator GUID klienta (`clientId`) i identyfikator URI przekierowania (`postLogoutRedirectUri`), które uczestniczą w przepływie.

2. Później aplikacja żąda od usługi Azure AD „tokenu dostępu”. Token dostępu jest wystawiany dla określonego zestawu uprawnień dla konkretnego identyfikatora usługi/interfejsu API https://api.timeseries.azure.com. Identyfikator usługi/interfejsu API jest określany także jako „odbiorca” tokenu. Uprawnienia tokenu są wystawiane w imieniu zalogowanego użytkownika. Identyfikator usługi/interfejsu API to jeszcze jedna właściwość zawarta w rejestracji aplikacji w usłudze Microsoft Azure Active Directory. Gdy biblioteka ADAL zwraca token dostępu do aplikacji, jest on przekazywany jako „token elementu nośnego” podczas uzyskiwania dostępu do interfejsów API usługi TSI.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>Identyfikacja kontrolki

Jak już wspomniano, elementy `<div>` występujące wewnątrz elementu `<body>` określają układ dla wszystkich kontrolek wykresów, które są wyświetlane na stronie. Każdy element `<div>` określa właściwości dotyczące położenia i atrybutów wizualnych kontrolki wykresu, w tym właściwość `id`. Właściwość `id` zawiera unikatowy identyfikator, który jest używany w kodzie języka JavaScript do identyfikowania i powiązania każdej kontrolki podczas renderowania i aktualizacji.

### <a name="aggregate-expressions"></a>Wyrażenia agregujące

Interfejsy API biblioteki klienta dla usługi TSI intensywnie wykorzystują wyrażenia agregujące. Wyrażenia agregujące umożliwiają konstruowanie co najmniej jednego „wyszukiwanego terminu”. Interfejsy API są zaprojektowane w sposób podobny do [eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com/demo), który wykorzystuje obszar wyszukiwania, predykat WHERE, miary i wartości używane do podziału. Większość interfejsów API biblioteki stosuje tablicę wyrażeń agregujących używanych przez usługę w celu skonstruowania zapytania do danych usługi TSI.

### <a name="call-pattern"></a>Wzorzec wywołania

Wypełnianie i renderowanie kontrolek wykresów jest zgodne ze ogólnym wzorcem. Ten wzorzec, który tworzy i ładuje kontrolki przykładowej aplikacji usługi TSI, można odnaleźć w wielu miejscach w kodzie języka JavaScript strony:

1. Zadeklaruj obiekt `array` do przechowywania co najmniej jednego wyrażenia agregującego usługi TSI:

   ```javascript
   var aes =  [];
   ```

2. Utwórz od 1 do n obiektów wyrażeń agregujących i dodaj je do tablicy wyrażeń agregujących:

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
   | `contextMenuActions` | Tablica akcji, która ma być powiązana z obiektami szeregów czasowych w wizualizacji (opcjonalnie). | Aby uzyskać więcej informacji, zobacz część „Wyskakujące menu kontekstowe” w sekcji „Funkcje zaawansowane”. |

3. Wywołaj zapytanie usługi TSI przy użyciu interfejsów API `TsiClient.Server`, aby zażądać dostępu do zagregowanych danych:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **parametry getAggregates**

   | Parametr | Opis | Przykład |
   | --------- | ----------- | ------- |
   | `token`     | Token dostępu do interfejsu API usługi TSI. |  `authContext.getTsiToken()` Aby uzyskać więcej informacji, zobacz [sekcję dotyczącą uwierzytelniania.](#authentication) |
   | `envFQDN`   | W pełni kwalifikowana nazwa domeny (FQDN) dla środowiska usługi TSI. | Na przykład w witrynie Azure Portal: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Tablica wyrażeń zapytań usługi TSI. | Użyj zmiennej `aes` zgodnie z wcześniejszym opisem: `aes.map(function(ae){return ae.toTsx()}`. |

4. Na potrzeby wizualizacji przekształć zwrócony przez zapytanie usługi TSI skompresowany wynik do postaci JSON:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Utwórz kontrolkę wykresu przy użyciu interfejsów API `TsiClient.UX` i powiąż ją z jednym z elementów `<div>` na stronie:

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Wypełnij kontrolkę wykresu przekształconymi obiektami danych JSON i renderuj ją na stronie:

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Renderowanie kontrolek

Biblioteka klienta usługi TSI obecnie udostępnia osiem unikatowych kontrolek analitycznych: wykres liniowy, wykres kołowy, wykres słupkowy, mapa cieplna, kontrolki hierarchii, dostępna siatka, osie czasu zdarzeń dyskretnych i osie czasu zmian stanu.

### <a name="line-bar-pie-chart-examples"></a>Przykłady wykresów liniowych, słupkowych i kołowych

Przyjrzyj się kodowi związanemu z niektórymi standardowymi kontrolkami wykresów pokazanych w aplikacji oraz modelowi/wzorcom programowania wykorzystanym do ich utworzenia. W szczególności zbadaj sekcję kodu HTML, znajdującą się za komentarzem `// Example 3/4/5`, która odpowiada za renderowanie kontrolek o identyfikatorach `chart3`, `chart4` i `chart5`.

Przypomnij sobie z kroku 3 w [sekcji Źródło i struktura strony](#page-source-and-structure), że kontrolki wykresów są ułożone na stronie w wierszach i każda z nich zawiera opisowy wiersz tytułu. W tym przykładzie trzy wypełnione wykresy znajdują się poniżej tytułowego elementu `<div>` „Multiple Chart Types From the Same Data” (Wiele typów wykresów z tym samym zestawem danych) i są powiązane z trzema elementami `<div>` znajdującymi się poniżej tytułu:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

Następująca sekcja kodu języka JavaScript korzysta z przedstawionych wcześniej wzorców: tworzy wyrażenia agregujące usługi TSI, wykorzystuje je do wykonania zapytań do danych usługi TSI oraz renderuje trzy wykresy. Zwróć uwagę na wykorzystane trzy typy z przestrzeni nazw `tsiClient.ux`: `LineChart`, `BarChart`, `PieChart` do utworzenia i renderowania odpowiednich wykresów. Zauważ także, że wszystkie trzy wykresy mogą korzystać z tych samych danych wyrażeń agregujących `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Po renderowaniu trzy wykresy wyglądają następująco:

[![Wiele typów wykresów z tym samym zestawem danych](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funkcje zaawansowane

Biblioteka klienta usługi TSI udostępnia również pewne opcjonalne funkcje zaawansowane, które mogą być przydatne.

### <a name="states-and-events"></a>Stany i zdarzenia

Jednym z przykładów udostępnianej zaawansowanej funkcjonalności jest możliwość dodawania do wykresów zmian stanu i zdarzeń dyskretnych. Ta funkcja jest przydatna do wyróżniania incydentów, alertów i przełączników stanu, takich jak włączenie/wyłączenie.

Spójrz na kod związany z sekcją HTML znajdującą się poniżej komentarza `// Example 10`. Kod renderuje kontrolkę wiersza znajdującą się pod tytułem „Line Charts with Multiple Series Types” (Wykresy liniowe z wieloma typami serii danych) i wiąże ją z elementem `<div>` z wartością identyfikatora `chart10`.

1. Najpierw definiowana jest struktura o nazwie `events4`, służąca do przechowywania śledzonych elementów zmiany stanu. Struktura zawiera:

   - Klucz będący ciągiem znaków o nazwie `Component States`.
   - Tablicę obiektów wartości reprezentujących stany. Każdy obiekt obejmuje:
     - Klucz będący ciągiem znaków, zawierający znacznik czasu języka JavaScript w standardzie ISO.
     - Tablicę zawierającą właściwości stanu: kolor i opis.

2. Następnie definiowana jest struktura `events5` dla elementu „Incidents” (Incydenty), zawierająca tablicę śledzonych elementów zdarzeń. Struktura tablicy ma taki sam kształt, jak struktura opisana dla elementu `events4`.

3. Ostatecznie podczas renderowania wykresu liniowego obie struktury są przekazywane przy użyciu parametrów opcji wykresu: `events:` i `states:`. Zwróć uwagę na inne parametry opcji służące do określenia `tooltip:`, `theme:` lub `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Do wskazywania incydentów używane są znaczniki w postaci rombu lub wyskakujące okienka, a kolorowe paski lub wyskakujące okienka wzdłuż osi czasu wskazują zmiany stanu:

[![Wykresy liniowe z wieloma typami serii danych](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Wyskakujące menu kontekstowe

Inny przykład funkcji zaawansowanych to niestandardowe menu kontekstowe (menu podręczne wywoływane po kliknięciu prawym przyciskiem myszy). Niestandardowe menu kontekstowe są przydatne w przypadku włączania akcji i kolejnych logicznych kroków, znajdujących się w zakresie aplikacji.

Spójrz na kod związany z sekcją HTML znajdującą się poniżej komentarza `// Example 13/14/15`. Ten kod powoduje inicjalne renderowanie wykresu liniowego pod tytułem „Line Chart with Context Menu to Create Pie/Bar Chart” (Wykres liniowy z menu kontekstowym umożliwiającym utworzenie wykresu kołowego/słupkowego), powiązanego z elementem `<div>` za pomocą identyfikatora `chart13`. Dzięki wykorzystaniu menu kontekstowych wykres liniowy umożliwia dynamiczne tworzenie wykresów kołowych i słupkowych powiązanych z elementami `<div>` za pomocą identyfikatorów `chart14` i `chart15`. Ponadto wykresy kołowy i słupkowy również umożliwiają wykorzystanie menu kontekstowych do użycia własnych funkcji: możliwości kopiowania danych z wykresu kołowego do wykresu słupkowego i drukowania danych wykresu słupkowego w oknie konsoli przeglądarki.

1. Najpierw definiowana jest seria akcji niestandardowych. Każda akcja zawiera tablicę z co najmniej jednym elementem. Każdy element definiuje pojedynczy element menu kontekstowego:

   - `barChartActions`: ta akcja definiuje menu kontekstowe wykresu kołowego, zawierające jeden element określający pojedynczy element menu:
     - `name`: tekst, który jest używany dla elementu menu: „Print parameters to console” (Parametry drukowania do konsoli).
     - `action`: akcja skojarzona z elementem menu. Ta akcja jest zawsze funkcją anonimową, przyjmującą trzy argumenty oparte na wyrażeniu agregującym używanym do tworzenia wykresu. W tym przypadku argumenty są zapisywane w oknie konsoli przeglądarki:
       - `ae`: tablica wyrażeń agregujących.
       - `splitBy`: wartość splitBy.
       - `timestamp`: znacznik czasu.

   - `pieChartActions`: ta akcja definiuje menu kontekstowe wykresu słupkowego, zawierające jeden element określający pojedynczy element menu. Kształt i schemat są takie same jak w przypadku poprzedniego elementu `barChartActions`, należy jednak zauważyć, że funkcja `action` jest zupełnie inna, ponieważ tworzy i renderuje wykres słupkowy. Należy także zauważyć, że argument `ae` jest wykorzystany do określenia tablicy wyrażeń agregujących przekazanej w czasie wykonywania w momencie otwarcia elementu menu. Funkcja ustawia również właściwość `ae.contextMenu` za pomocą menu kontekstowego `barChartActions`.
   - `contextMenuActions`: ta akcja definiuje menu kontekstowe wykresu liniowego, zawierające trzy elementy określające trzy elementy menu. Kształt i schemat dla każdego elementu są takie same jak w przypadku poprzednio opisanych elementów. Podobnie jak dla elementu `barChartActions`, pierwszy element zapisuje trzy argumenty funkcji w oknie konsoli przeglądarki. Podobnie jak dla elementu `pieChartActions`, kolejne dwa elementy tworzą i renderują odpowiednio wykresy kołowy i słupkowy. Następne dwa elementy ustawiają również swoje właściwości `ae.contextMenu` poprzez menu kontekstowe odpowiednio `pieChartActions` i `barChartActions`.

2. Następnie dwa wyrażenia agregujące są wypychane do tablicy wyrażeń agregujących `aes` i określają tablicę `contextMenuActions` dla każdego z nich. Wyrażenia są używane przez kontrolkę wykresu liniowego.

3. Ostatecznie tylko wykres liniowy jest renderowany inicjalnie, a z niego w czasie wykonywania mogą być renderowane wykresy kołowy i słupkowy.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Zrzut ekranu pokazuje wykresy wraz z właściwymi dla nich wyskakującymi menu kontekstowymi. Wykresy kołowy i słupkowy zostały utworzone dynamicznie przy użyciu opcji menu kontekstowego wykresu liniowego.

[![Wykres liniowy z menu kontekstowym umożliwiającym utworzenie wykresu kołowego/słupkowego](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pędzle

Pędzle są używane do określania zakresu czasu w celu definiowania akcji, takich jak powiększanie i eksplorowanie.

Kod używany w celu zademonstrowania działania pędzli jest pokazany w poprzednim przykładzie „Wykres liniowy z menu kontekstowym umożliwiającym utworzenie wykresu kołowego/słupkowego”, dotyczącym wyskakujących menu kontekstowych.

1. Akcje związane z pędzlem są bardzo podobne do menu kontekstowego w tym, że definiują dla pędzla szereg akcji niestandardowych. Każda akcja zawiera tablicę z co najmniej jednym elementem. Każdy element definiuje pojedynczy element menu kontekstowego:
   - `name`: tekst, który jest używany dla elementu menu: „Print parameters to console” (Parametry drukowania do konsoli).
   - `action`: skojarzona z elementem menu akcja, która jest zawsze funkcją anonimową, przyjmującą dwa argumenty. W tym przypadku argumenty są zapisywane w oknie konsoli przeglądarki:
      - `fromTime`: znacznik czasu „od” zaznaczenia za pomocą pędzla.
      - `toTime`: znacznik czasu „do” zaznaczenia za pomocą pędzla.

2. Akcje pędzla są dodawane jako jeszcze jedna właściwość opcji wykresu. Zwróć uwagę na właściwość `brushContextMenuActions: brushActions` przekazywaną do wywołania `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Wykres liniowy z menu kontekstowym umożliwiającym utworzenie wykresu kołowego/słupkowego za pomocą pędzli](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

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