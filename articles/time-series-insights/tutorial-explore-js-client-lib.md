---
title: Poznawanie biblioteki klienta języka JavaScript dla usługi Time Series Insights
description: Informacje na temat biblioteki klienta języka JavaScript dla usługi Times Series Insights i powiązanego z nią modelu programowania.
documentationcenter: ''
services: time-series-insights
author: BryanLa
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: time-series-insights
ms.workload: na
ms.tgt_pltfrm: ''
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: 5b845f36dbb65b38d0e4ac2a118277027239b3d6
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-explore-the-time-series-insights-javascript-client-library"></a>Samouczek: Poznaj bibliotekę klienta języka JavaScript dla usługi Times Series Insights

Aby wspomóc deweloperów w tworzeniu zapytań i wizualizacji danych przechowywanych w usłudze Time Series Insights (TSI), opracowaliśmy bibliotekę kontrolek opartą na bibliotece D3 języka JavaScript, która ułatwia tę pracę. Ten samouczek umożliwia poznanie biblioteki klienta języka JavaScript dla usługi TSI i powiązanego z nią modelu programowania przy użyciu przykładowej aplikacji sieci Web. 

Omawiane tematy dają możliwości eksperymentowania oraz lepszego zrozumienia, jak można uzyskać dostęp do danych usługi TSI i używać kontrolek wykresu w celu renderowania i wizualizacji danych. Celem jest dostarczenie użytkownikowi wystarczającej ilości szczegółowych informacji, które umożliwia wykorzystanie biblioteki we własnych aplikacjach sieci Web.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
> * Przykładowej aplikacji usługi TSI 
> * Biblioteki klienta języka JavaScript dla usługi TSI
> * Sposobu, w jaki przykładowa aplikacja korzysta z biblioteki w celu wizualizacji danych w usłudze TSI

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek korzysta z funkcji „Narzędzia deweloperskie” (znanej także jako DevTools lub F12), którą można znaleźć w większości nowoczesnych przeglądarek sieci Web, takich jak [Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) i inne. Jeśli jeszcze jej nie znasz, możesz zbadać tę funkcję w swojej przeglądarce, zanim będziesz kontynuować. 

## <a name="the-time-series-insights-sample-application"></a>Przykładowa aplikacja usługi Time Series Insights

W tym samouczku przykładowa aplikacja usługi Time Series Insights jest wykorzystywana do analizowania kodu źródłowego aplikacji, w tym części odpowiedzialnej za użycie biblioteki klienta JavaScript dla usługi TSI. Aplikacja jest jednostronicową aplikacją sieci Web (SPA, Single-Page Web Application), ilustrującą użycie biblioteki podczas tworzenia zapytań i wykonywania wizualizacji danych w przykładowym środowisku usługi TSI. 

1. Przejdź do [przykładowej aplikacji usługi Time Series Insights](https://insights.timeseries.azure.com/clientsample). Zobaczysz stronę podobną do poniższej, wyświetlającą monit o zalogowanie: ![przykładowy monit o zalogowanie klienta usługi TSI](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Kliknij przycisk „Zaloguj” i wprowadź lub wybierz swoje poświadczenia. Możesz użyć konta przedsiębiorstwa/organizacji (Azure Active Directory) lub konta osobistego (konto Microsoft lub MSA). 

   ![Monit o wprowadzenie poświadczeń klienta usługi TSI — przykład](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Po pomyślnym zalogowaniu zobaczysz stronę podobną do widocznej poniżej, zawierającą różne rodzaje przykładowych wykresów opartych o dane z usługi TSI. Zwróć również uwagę na Twoje konto użytkownika i link „Wyloguj się” umieszczone w prawym górnym rogu: ![Główna strona klienta usługi TSI po zalogowaniu — przykład](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Źródło i struktura strony

Na początku obejrzyjmy kod źródłowy HTML i JavaScript strony renderowanej w przeglądarce. Nie będziemy przechodzić przez wszystkie elementy, ale poznasz główne sekcje, co pozwoli Ci zorientować się, jak działa strona:

1. Otwórz w przeglądarce funkcję „Narzędzia deweloperskie” i zbadaj elementy HTML wchodzące w skład bieżącej strony, określane również jako drzewo HTML lub drzewo DOM.

2. Rozwiń elementy `<head>` i `<body>` i zwróć uwagę na następujące sekcje:
   - W sekcji `<head>` możesz znaleźć elementy, które odpowiadają za ściągnięcie dodatkowych plików biorących udział w działaniu strony:
     - element `<script>` odwołujący się do biblioteki Azure Active Directory Authentication Library (adal.min.js), znanej również jako ADAL — jest to biblioteka języka JavaScript, która zawiera funkcje uwierzytelniania OAuth 2.0 (logowanie) i pobierania tokenu, używane podczas uzyskiwania dostępu do interfejsów API;
     - elementy `<link>` dla arkuszy stylów (sampleStyles.css, tsiclient.css), znane również jako CSS — są używane do sterowania szczegółami stylów wpływającymi na wygląd strony, takimi jak kolory, czcionki, odstępy itp.; 
     - element `<script>` odpowiedzialny za odwołanie do biblioteki klienta usługi TSI (tsiclient.js) — jest to biblioteka języka JavaScript, używana przez stronę do wywoływania interfejsów API usługi TSI i renderowania kontrolek wykresów na stronie.

     >[!NOTE]
     > Kod źródłowy biblioteki ADAL w języku JavaScript jest dostępny w [repozytorium azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  
     > Kod źródłowy biblioteki klienta usługi TSI w języku JavaScript jest dostępny w [repozytorium tsiclient](https://github.com/Microsoft/tsiclient).

   - W sekcji `<body>` możesz znaleźć elementy `<div>`, które pełnią rolę kontenerów definiujących układ elementów na stronie, oraz kolejny element `<script>`:
     - pierwszy element `<div>` określa okno dialogowe „Logowanie” (`id="loginModal"`);
     - drugi element `<div>` pełni rolę elementu nadrzędnego dla następujących elementów:
       - nagłówka `<div>` zawierającego komunikaty o stanie i informacje dotyczące logowania, wyświetlane w górnej części strony (`class="header"`);
       - elementu `<div>` przeznaczonego dla pozostałych elementów treści strony, łącznie z wszystkimi wykresami (`class="chartsWrapper"`);
       - sekcji `<script>`, która zawiera wszystkie elementy języka JavaScript używane do sterowania działaniem strony.

   [![Klient usługi TSI w DevTools — przykład](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Rozwiń element `<div class="chartsWrapper">` i zwróć uwagę na wiele podrzędnych elementów `<div>`, używanych do pozycjonowania każdej przykładowej kontrolki wykresu. Dla każdego przykładu wykresu istnieje kilka par elementów `<div>`:
   - Pierwszy z nich (`class="rowOfCardsTitle"`) zawiera opisowy tytuł podsumowujący, co przedstawia wykres. Na przykład: „Static Line Charts With Full-Size Legends” (Statyczne wykresy liniowe z pełnowymiarową legendą)
   - Drugi (`class="rowOfCards"`) jest elementem nadrzędnym, zawierającym dodatkowe elementy podrzędne `<div>`, które są odpowiedzialne za umieszczenie w wierszu rzeczywistych kontrolek wykresów. 

  ![Przeglądanie elementów „div” treści](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Teraz rozwiń element `<script type="text/javascript">`, znajdujący się bezpośrednio pod elementem `<div class="chartsWrapper">`. Zostanie wyświetlony początek sekcji języka JavaScript na poziomie strony, używanej do kompletnej obsługi logiki strony, obejmującej uwierzytelnianie, wywoływanie interfejsów API usługi TSI, renderowanie kontrolek wykresów i inne operacje:

  ![Przeglądanie skryptu treści](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-client-javascript-library-concepts"></a>Pojęcia związane z biblioteką klienta języka JavaScript dla usługi TSI

Mimo że nie przeglądamy szczegółowo biblioteki klienta usługi TSI (tsclient.js), trzeba zwrócić uwagę na fakt, że udostępnia ona abstrakcję dla dwóch ważnych kategorii:

- **Metody opakowujące wywołania interfejsów API zapytań usługi TSI** — interfejsy API REST, które pozwalają użytkownikowi na tworzenie zapytań do danych w usłudze TSI przy użyciu wyrażeń agregujących i są zorganizowane w przestrzeni nazw `TsiClient.Server` biblioteki. 
- **Metody tworzenia i wypełniania różnych typów kontrolek wykresów** — używane do renderowania zagregowanych danych usługi TSI na stronie sieci Web i są zorganizowane w przestrzeni nazw `TsiClient.UX` biblioteki. 

Poniższe pojęcia są uniwersalne i mają ogólne zastosowanie do interfejsów API biblioteki klienta usługi TSI. 

### <a name="authentication"></a>Authentication

Jak wspomniano wcześniej, ten przykład wykorzystuje aplikację jednostronicową i korzysta z obsługi protokołu OAuth 2.0 w bibliotece ADAL do uwierzytelniania użytkowników. Poniżej wymieniono niektóre interesujące kwestie dotyczące tej sekcji skryptu:

1. Uwierzytelnianie za pomocą biblioteki ADAL wymaga, aby aplikacja kliencka rejestrowała się samodzielnie w rejestrze aplikacji usługi Azure Active Directory (Azure AD). Jako aplikacja jednostronicowa jest ona zarejestrowana do korzystania z przepływu „niejawnego” autoryzacji OAuth 2.0. Odpowiednio aplikacja określa niektóre właściwości rejestrowania w środowisku uruchomieniowym, takie jak identyfikator GUID klienta (`clientId`) i identyfikator URI przekierowania (`postLogoutRedirectUri`), które uczestniczą w przepływie.

2. Później aplikacja żąda od usługi Azure AD „tokenu dostępu”. Token dostępu jest wystawiany dla określonego zestawu uprawnień dla konkretnego identyfikatora usługi/interfejsu API (https://insights.timeseries.azure.com), określanych również jako „odbiorcy” tokenu. Uprawnienia tokenu są wystawiane w imieniu zalogowanego użytkownika. Identyfikator usługi/interfejsu API występuje jeszcze w innej właściwości zawartej w rejestracji aplikacji w usłudze Azure AD. Gdy biblioteka ADAL zwraca token dostępu do aplikacji, jest on przekazywany jako „token okaziciela” podczas uzyskiwania dostępu do interfejsów API usługi TSI. 

   [!code-javascript[head-sample](~/samples-javascript/pages/index.html?range=140-199&highlight=4-9,36-39)]

### <a name="control-identification"></a>Identyfikacja kontrolki

Jak już wspomniano, elementy `<div>` występujące wewnątrz elementu `<body>` określają układ dla wszystkich kontrolek wykresów wyświetlanych na stronie. Każdy z nich określa właściwości dotyczące położenia i atrybutów wizualnych kontrolki wykresu, w tym właściwość `id`. Właściwość `id` zawiera unikatowy identyfikator, który jest używany w kodzie języka JavaScript do identyfikowania i powiązania każdej kontrolki podczas renderowania i aktualizacji. 

### <a name="aggregate-expressions"></a>Wyrażenia agregujące

Interfejsy API biblioteki klienta dla usługi TSI intensywnie wykorzystują wyrażenia agregujące. Wyrażenia agregujące umożliwiają konstruowanie co najmniej jednego „wyszukiwanego terminu”. Interfejsy API są podobne do sposobu, w jaki [eksplorator usługi Time Series Insights](https://insights.timeseries.azure.com/demo) wykorzystuje obszar wyszukiwania, predykat WHERE, miary i wartości używane do podziału. Większość interfejsów API bibliotek stosuje tablice wyrażeń agregujących używanych przez usługę w celu skonstruowania zapytania do danych usługi TSI.

### <a name="call-pattern"></a>Wzorzec wywołania

Wypełnianie i renderowanie kontrolek wykresów jest zgodne ze ogólnym wzorcem. Ten wzorzec, który tworzy i ładuje kontrolki przykładowej aplikacji usługi TSI, można odnaleźć w wielu miejscach w kodzie języka JavaScript strony:

1. Zadeklaruj tablicę do przechowywania co najmniej jednego wyrażenia agregującego usługi TSI.  

   ```javascript
   var aes =  [];
   ```

2. Utwórz od 1 do n obiektów wyrażeń agregujących i dodaj je do tablicy wyrażeń agregujących.  

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```
   **Parametry aggregateExpression**

   | Parametr | Opis | Przykład |
   | --------- | ----------- | ------- |
   | predicateObject | Wyrażenie filtrowania danych. |`{predicateString: "Factory = 'Factory3'"}` |
   | measureObject   | Nazwa właściwości odnoszącej się do używanej miary. | `{property: 'Temperature', type: "Double"}` |
   | measureTypes    | Żądane agregacje właściwości miary. | `['avg', 'min']` |
   | searchSpan      | Czas trwania i wielkość interwału wyrażenia agregującego. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | splitByObject   | Właściwość określająca ciąg znaków, którego chcesz użyć do podziału (opcjonalnie — może mieć wartość null). | `{property: 'Station', type: 'String'}` |
   | color           | Kolor obiektów, który chcesz zastosować podczas renderowania. | `'pink'` |
   | alias           | Przyjazna nazwa wyrażenia agregującego. | `'Factory3Temperature'` |
   | contextMenuActions | Tablica akcji, która ma być powiązana z obiektami szeregów czasowych w wizualizacji (opcjonalnie). | Zobacz temat [Wyskakujące menu kontekstowe w sekcji Funkcje zaawansowane.](#popup-context-menus) |

3. Wywołaj zapytanie usługi TSI przy użyciu interfejsów API `TsiClient.Server`, aby zażądać dostępu do zagregowanych danych.  

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```
   **parametry getAggregates**

   | Parametr | Opis | Przykład |
   | --------- | ----------- | ------- |
   | token     | Token dostępu do interfejsu API usługi TSI | `authContext.getTsiToken()` Zobacz [sekcja dotycząca uwierzytelniania.](#authentication) |
   | envFQDN     | W pełni kwalifikowana nazwa domeny dla środowiska TSI | Na przykład w witrynie Azure Portal `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` |
   | aeTsxArray | Tablica wyrażeń zapytań usługi TSI | Użyj zmiennej `aes` zgodnie z wcześniejszym opisem: `aes.map(function(ae){return ae.toTsx()}` |

4. Na potrzeby wizualizacji przekształć skompresowany wynik zwrócony przez zapytanie usługi TSI do postaci JSON.

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Utwórz kontrolkę wykresu przy użyciu interfejsów API `TsiClient.UX` i powiąż ją z jednym z elementów `<div>` na stronie.

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Wypełnij kontrolkę wykresu przekształconymi obiektami danych JSON i renderuj ją na stronie.

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Renderowanie kontrolek

Biblioteka udostępnia obecnie osiem unikatowych kontrolek analitycznych. Należą do nich wykres liniowy, wykres kołowy, wykres słupkowy, mapa cieplna, kontrolki hierarchii, dostępna siatka, osie czasu zdarzeń dyskretnych i osie czasu zmian stanu.   

### <a name="line-bar-pie-chart-examples"></a>Przykłady wykresów liniowych, słupkowych i kołowych 

Najpierw przyjrzyjmy się kodowi związanemu z niektórymi standardowymi kontrolkami wykresów pokazanych w aplikacji oraz modelowi/wzorcom programowania wykorzystanym do ich utworzenia. W szczególności zbadasz sekcję kodu HTML, znajdującą się za komentarzem `// Example 3/4/5`, która odpowiada za renderowanie kontrolek o identyfikatorach `chart3`, `chart4` i `chart5`. 

Przypomnij sobie z kroku 3 w [sekcji Źródło i struktura strony](#page-source-and-structure), że kontrolki wykresów są ułożone na stronie w wierszach i każda z nich zawiera opisowy wiersz tytułu. W tym przykładzie trzy wypełnione wykresy znajdują się poniżej tytułowego elementu `<div>` „Multiple Chart Types From the Same Data” (Wiele typów wykresów z tym samym zestawem danych) i są powiązane z trzema znajdującymi się poniżej elementami `<div>`:

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/index.html?range=60-74&highlight=1,5,9,13)]

Następująca sekcja kodu języka JavaScript korzysta z przedstawionego wcześniej wzorca do utworzenia wyrażeń agregujących usługi TSI, wykorzystania ich do wykonania zapytania do danych usługi TSI oraz renderowania trzech wykresów. Zwróć uwagę na trzy typy użyte z przestrzeni nazw `tsiClient.ux`, `LineChart`, `BarChart`, `PieChart` do utworzenia i renderowania odpowiednich wykresów. Zauważ także, że wszystkie trzy wykresy mogą korzystać z tych samych danych wyrażeń agregujących, `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/index.html?range=236-257&highlight=13-14,16-17,19-20)]

Po renderowaniu trzy wykresy wyglądają następująco:

[![Wiele typów wykresów z tym samym zestawem danych](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funkcje zaawansowane

Biblioteka udostępnia również opcjonalne funkcje zaawansowane, które mogą być przydatne.  

### <a name="states-and-events"></a>Stany i zdarzenia

Jednym z przykładów udostępnianej zaawansowanej funkcjonalności jest możliwość dodawania do wykresów zmian stanu i zdarzeń dyskretnych. Ta funkcja jest przydatna do wyróżniania incydentów, alertów i przełączników stanu, takich jak włączenie/wyłączenie. 

Spojrzymy na kod związany z sekcją HTML poniżej komentarza `// Example 10`. Kod renderuje kontrolkę wiersza znajdującą się pod tytułem „Line Charts with Multiple Series Types” (Wykresy liniowe z wieloma typami serii danych), wiążąc ją z elementem `<div>` z wartością identyfikatora `chart10`:

1. Najpierw definiowana jest struktura o nazwie `events4`, służąca do przechowywania elementów zmiany stanu, które mają być śledzone. Zawiera ona:
   - Klucz będący ciągiem znaków o nazwie `"Component States"` 
   - Tablicę obiektów wartości reprezentujących stany, z których każdy obejmuje:
     - Klucz będący ciągiem znaków, zawierający sygnaturę czasową języka JavaScript w standardzie ISO
     - Tablicę zawierającą charakterystykę stanu
       - kolor
       - opis

2. Następnie definiowana jest struktura `events5` dla elementu `"Incidents"`, zawierająca tablicę elementów zdarzeń, które mają być śledzone. Struktura tablicy ma taki sam kształt, jak opisany dla elementu `events4`. 

3. Ostatecznie podczas renderowania wykresu liniowego obie struktury są przekazywane przy użyciu parametrów opcji wykresu: `events:` i `states:`. Zwróć uwagę na inne parametry opcji służące do określenia `tooltip:`, `theme:` lub `grid:`. 

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/index.html?range=332-384&highlight=5,26,51)]

Do wskazywania incydentów używane są znaczniki w postaci rombu lub wyskakujące okienka, a kolorowe paski lub wyskakujące okienka wzdłuż osi czasu wskazują zmiany stanu:

[![Wykresy liniowe z wieloma typami serii danych](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="popup-context-menus"></a>Wyskakujące menu kontekstowe

Innym przykładem funkcji zaawansowanych są niestandardowe menu kontekstowe (podręczne menu wywoływane za pomocą prawego przycisku myszy), które są przydatne do włączania akcji i kolejnych logicznych kroków w ramach zakresu aplikacji.

Teraz przyjrzymy się kodowi związanemu z kodem HTML znajdującym się pod komentarzem `// Example 13/14/15`. Ten kod powoduje inicjalne renderowanie wykresu liniowego pod tytułem „Line Chart with Context Menu to Create Pie/Bar Chart” (Wykres liniowy z menu kontekstowym umożliwiającym utworzenie wykresu kołowego/słupkowego), powiązanego z elementem `<div>` za pomocą identyfikatora `chart13`. Przy użyciu menu kontekstowych wykres liniowy umożliwia dynamiczne tworzenie wykresów kołowych i słupkowych, powiązanych z elementami `<div>` za pomocą identyfikatorów `chart14` i `chart15`. Ponadto wykresy kołowy i słupkowy również umożliwiają wykorzystanie menu kontekstowych do użycia własnych funkcji: możliwości kopiowania danych z wykresu kołowego do wykresu słupkowego i drukowania danych wykresu słupkowego w oknie konsoli przeglądarki.

1. Najpierw definiowana jest seria akcji niestandardowych. Każda zawiera tablicę z co najmniej jednym elementem, w której każdy element definiuje pojedynczy element menu kontekstowego: 
   - `barChartActions`: definiuje menu kontekstowe wykresu słupkowego, zawierające jeden element określający pojedynczy element menu:
     - `name`: tekst używany w elemencie menu: „Print parameters to console” (Drukuj parametry na konsoli)
     - `action`: akcja skojarzona z elementem menu, która jest zawsze funkcją anonimową, przyjmującą trzy argumenty oparte na wyrażeniu agregującym używanym do tworzenia wykresu. W tym przypadku w oknie konsoli przeglądarki są zapisywane:
       - `ae`: tablica wyrażeń agregujących
       - `splitBy`: wartość splitBy
       - `timestamp`: sygnatura czasowa
   - `pieChartActions`: definiuje menu kontekstowe wykresu kołowego, w którym jeden element określa pojedynczy element menu. Kształt i schemat są takie same jak w przypadku poprzedniego elementu `barChartActions`, należy jednak zauważyć, że funkcja `action` jest zupełnie inna, ponieważ tworzy i renderuje wykres słupkowy. Należy także zauważyć, że używa ona argumentu `ae` do określenia tablicy wyrażeń agregujących przekazanej w czasie wykonywania w momencie pojawienia się elementu menu. Funkcja ustawia również właściwość `ae.contextMenu` za pomocą menu kontekstowego `barChartActions`.
   - `contextMenuActions`: definiuje menu kontekstowe wykresu liniowego, zawierające trzy elementy określające trzy elementy menu. Kształt i schemat dla każdego elementu są takie same jak poprzednio. Podobnie jak dla elementu `barChartActions`, pierwszy element zapisuje trzy argumenty funkcji w oknie konsoli przeglądarki. Podobnie jak dla elementu `pieChartActions`, następne dwa elementy tworzą i renderują odpowiednio wykresy kołowe i słupkowe. Następne dwa elementy ustawiają również swoje właściwości `ae.contextMenu` poprzez menu kontekstowe odpowiednio `pieChartActions` i `barChartActions`.

2. Następnie dwa wyrażenia agregujące są wysyłane do tablicy wyrażeń agregujących `aes`, określając tablicę `contextMenuActions` dla każdego z nich. Są one używane przez kontrolkę wykresu liniowego.

3. Ostatecznie tylko wykres liniowy jest renderowany inicjalnie, a z niego w czasie wykonywania mogą być renderowane wykresy kołowy i słupkowy.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/index.html?range=456-535&highlight=7,16,29,61-64,78)]

Zrzut ekranu pokazuje wykresy wraz z właściwymi dla nich wyskakującymi menu kontekstowymi. Wykresy kołowy i słupkowy zostały utworzone dynamicznie przy użyciu opcji menu kontekstowego wykresu liniowego:

[![Wykres liniowy z menu kontekstowym umożliwiającym utworzenie wykresu kołowego/słupkowego](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pędzle

Pędzli można użyć do określania zakresu czasu w celu definiowania akcji, takich jak powiększanie i eksplorowanie. 

Kod używany w celu uwidocznienia pędzli jest pokazany także w poprzednim przykładzie „Wykres liniowy z menu kontekstowym umożliwiającym utworzenie wykresu kołowego/słupkowego”, dotyczącym [wyskakujących menu kontekstowych](#popup-context-menus-section). 

1. Akcje związane z pędzlem są bardzo podobne do menu kontekstowego, definiującego szereg akcji niestandardowych pędzla. Każda zawiera tablicę z co najmniej jednym elementem, w której każdy element definiuje pojedynczy element menu kontekstowego:
   - `name`: tekst używany w elemencie menu: „Print parameters to console” (Drukuj parametry na konsoli)
   - `action`: akcja skojarzona z elementem menu, która jest zawsze funkcją anonimową, przyjmującą dwa argumenty. W tym przypadku w oknie konsoli przeglądarki są zapisywane:
      - `fromTime`: sygnatura czasowa „od” zaznaczenia za pomocą pędzla
      - `toTime`: sygnatura czasowa „do” zaznaczenia za pomocą pędzla

2. Akcje pędzla są dodawane jako jeszcze jedna właściwość opcji wykresu. Zwróć uwagę na właściwość `brushContextMenuActions: brushActions` przekazywaną do wywołania `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/index.html?range=521-535&highlight=1,13)]

![Wykres liniowy z menu kontekstowym umożliwiającym utworzenie wykresu kołowego/słupkowego za pomocą pędzli](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Logowanie i eksplorowanie przykładowej aplikacji usługi TSI i jej źródła
> * Korzystanie z interfejsów API w bibliotece klienta języka JavaScript dla usługi TSI
> * Tworzenie i wypełnianie kontrolek wykresu z użyciem danych usługi TSI za pomocą języka JavaScript

Zgodnie z opisem, przykładowa aplikacja usługi TSI korzysta z demonstracyjnego zestawu danych. Aby dowiedzieć się więcej na temat tworzenia własnego środowiska i zestawu danych w usłudze TSI, należy przejść do następującego artykułu:

> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Azure Time Series Insights](time-series-insights-environment-planning.md)


