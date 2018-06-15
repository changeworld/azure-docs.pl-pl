---
title: Opis języka (LUIS) na platformie Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące opis języka (LUIS)
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: ff7b12b623c0c910d3a8b643f3c26f35d2fc0355
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349800"
---
# <a name="language-understanding-faq"></a>Opis języka — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące opis języka (LUIS).

## <a name="luis-authoring"></a>LUIS autorstwa

### <a name="what-are-the-luis-best-practices"></a>Jakie są najlepsze rozwiązania LUIS? 
Rozpoczynać [cyklu tworzenia](luis-concept-app-iteration.md), następnie odczytywane [najlepsze rozwiązania](luis-concept-best-practices.md). 

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Co to jest najlepszy sposób, aby rozpocząć tworzenie Moja aplikacja w LUIS?

To najlepszy sposób kompilowania aplikacji za pośrednictwem [przyrostowe procesu](luis-concept-app-iteration.md). 

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Co to jest dobrze modelu intencje mojej aplikacji? Należy utworzyć bardziej szczegółowe lub bardziej ogólnym intencje?

Wybierz opcje, które nie są tak ogólne, aby były nakładające się, ale nie tak określone, że jej utrudnia LUIS odróżnienie podobne opcje. Tworzenie discriminative określonej lokalizacji docelowych jest jednym z najlepszych rozwiązań dotyczących LUIS modelowania.

### <a name="is-it-important-to-train-the-none-intent"></a>Jest to istotne w celu przeszkolenia brak konwersji?

Tak, warto uczenia z **Brak** konwersji z więcej zniesławiających, jak dodać więcej etykiet do innej metody. Dobry wskaźnik jest 1 lub 2 etykiety dodane do **Brak** co 10 etykiet dodane do celem. Ten wskaźnik zwiększa możliwości discriminative LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Jak można poprawić błędów pisowni w zniesławiających?

Zobacz [7 interfejsu API sprawdzania pisowni usługi Bing](luis-tutorial-bing-spellcheck.md) samouczka. LUIS wymusza ograniczeń narzuconych przez 7 interfejsu API sprawdzania pisowni usługi Bing. 

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Jak edytować Moja aplikacja LUIS programowo?
Aby edytować aplikację LUIS programowo, należy użyć [tworzenia interfejsu API](https://aka.ms/luis-authoring-apis). Zobacz [LUIS wywołania interfejsu API tworzenia](./luis-quickstart-node-add-utterance.md) i [tworzenie aplikacji LUIS programowo przy użyciu środowiska Node.js](./luis-tutorial-node-import-utterances-csv.md) przykłady do wywołania interfejsu API tworzenia. Tworzenie interfejsu API wymaga użycia [tworzenia klucza](luis-concept-keys.md#authoring-key) zamiast klucza punktu końcowego. Programowe tworzenie umożliwia do 1 000 000 wywołania miesięcznie i pięć transakcje na sekundę. Aby uzyskać więcej informacji o kluczach używających LUIS, zobacz [zarządzanie kluczami](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Gdy funkcja wzorca, który podane wyrażenie regularne jest zgodne?
Poprzedni **funkcja wzorca** jest obecnie przestarzałe, zastępuje  **[wzorce](luis-concept-patterns.md)**. 

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Jak używać jednostki, aby wysunąć prawidłowe dane? 
Zobacz [jednostek](luis-concept-entity-types.md) i [wyodrębniania danych](luis-concept-data-extraction.md).

## <a name="luis-endpoint"></a>Punkt końcowy LUIS

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Dlaczego LUIS dodaje spacje do zapytania wokół lub w środku słowa?
LUIS [tokenizes](luis-glossary.md#token) na podstawie utterance [kultury](luis-supported-languages.md#tokenization). Zarówno w oryginalnej wartości, jak i w plikach wartość są dostępne dla [wyodrębniania danych](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak utworzyć i przypisać LUIS klucz punktu końcowego?
[Utwórz klucz punktu końcowego](luis-how-to-azure-subscription.md#create-luis-endpoint-key) platformy Azure dla Twojego [usługi](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) poziom. [Przypisz klucz](Manage-keys.md#assign-endpoint-key) na **[publikowania](publishapp.md)** strony. Nie ma żadnego odpowiedniego interfejsu API dla tej akcji. Następnie należy zmienić żądania HTTP do punktu końcowego do [użycia nowego klucza punktu końcowego](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Jak interpretować wyniki LUIS? 
System powinien używać najwyższy zamiar oceniania niezależnie od jego wartości. Na przykład wynik poniżej 0,5 (mniej niż 50%) nie musi oznaczać czy LUIS ma zaufanie niski. Zapewnianie więcej danych szkoleniowych może pomóc zwiększyć wynik najbardziej prawdopodobna zamiarem.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Dlaczego nie widzę mojego trafień punktu końcowego na pulpicie nawigacyjnym Moja aplikacja
Liczba całkowita punktu końcowego na pulpicie nawigacyjnym aplikacji są okresowo aktualizowane, ale metryki skojarzony z kluczem LUIS subskrypcję w portalu Azure są aktualizowane częściej. 

Jeśli nie widzisz trafień zaktualizowano punkt końcowy na pulpicie nawigacyjnym, zaloguj się do portalu Azure i Znajdź zasobu skojarzonego z kluczem LUIS subskrypcji, a następnie otwórz **metryki** wybierz **całkowita liczba wywołań** metryki. Jeśli klucz Subskrypcja jest używana do więcej niż jedną aplikację LUIS, metryki w portalu Azure zawiera łączna liczba wywołań z wszystkich aplikacji LUIS, które go używają.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Moja aplikacja LUIS opracowanego wczoraj, ale obecnie 403 błędów występujących. Nie można zmienić aplikacji. Jak rozwiązać ten problem? 
Po [instrukcje](#how-do-i-create-and-assign-a-luis-endpoint-key) w następnym — często zadawane pytania do tworzenia klucza punktu końcowego LUIS i przypisz go do aplikacji. Następnie należy zmienić żądania HTTP do punktu końcowego do [użycia nowego klucza punktu końcowego](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Jak zabezpieczyć mój LUIS punkt końcowy? 
Zobacz [zabezpieczanie punktu końcowego](luis-concept-security.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Praca w granicach LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Co to jest maksymalna liczba lokalizacji docelowych i jednostkami, którymi może obsługiwać aplikacja LUIS?
Zobacz [granice](luis-boundaries.md) odwołania.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Chcę tworzenie aplikacji LUIS z więcej niż maksymalna liczba lokalizacji docelowych. Co mam zrobić?

Zobacz [najlepszych rozwiązań dotyczących intencje](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Chcę, aby utworzyć aplikację w LUIS z więcej niż maksymalna liczba jednostek. Co mam zrobić?

Zobacz [najlepszych rozwiązań dotyczących jednostki](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Jakie są limity liczby i rozmiaru frazy listy?
Dla maksymalnego [listy frazy](./luis-concept-feature.md), zobacz [granice](luis-boundaries.md) odwołania.

### <a name="what-are-the-limits-on-example-utterances"></a>Jakie są ograniczenia na przykład zniesławiających?
Zobacz [granice](luis-boundaries.md) odwołania.

## <a name="testing-and-training"></a>Testowanie i uczenie

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Błędy w zadaniu wsadowym testowania w przypadku niektórych modeli w mojej aplikacji w okienku są widoczne. Jak można rozwiązać ten problem?

Błędy oznacza, że pewne rozbieżności między etykiety i prognozowanie z modeli. Aby rozwiązać ten problem, wykonaj jedną lub obie następujące czynności:
* Aby ułatwić LUIS poprawy dyskryminacji lokalizacji docelowych, należy dodać więcej etykiety.
* Aby ułatwić LUIS szybciej Dowiedz się, dodać funkcje Lista fraz wprowadzenie słownictwa specyficznego dla domeny.

Zobacz [testowania partii](luis-tutorial-batch-testing.md) samouczka.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Gdy aplikacja jest wyeksportować, a następnie ponownie zaimportować do nowej aplikacji (z nowym Identyfikatorem aplikacji), wyniki prognozowania LUIS są różne. Dlaczego to możliwe? 

Zobacz [prognozowania różnice między kopie tej samej aplikacji](luis-concept-prediction-score.md#differences-with-predictions).

## <a name="app-publishing"></a>Publikowanie aplikacji

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Co to jest identyfikator dzierżawcy w oknie "Dodaj klucz do aplikacji"?
Na platformie Azure dzierżawca reprezentuje klienta lub organizacja, która jest skojarzona z usługą. Znajdź swój identyfikator dzierżawy w portalu Azure w **identyfikator katalogu** pola, wybierając **usługi Azure Active Directory** > **Zarządzaj**  >  **Właściwości**.

![Identyfikator dzierżawy w portalu Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

### <a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>Dlaczego są istnieje więcej klucze subskrypcji na moją aplikację strona publikowania niż przypisane do aplikacji? 
Każda aplikacja LUIS ma klucz tworzenia początkowego. Klucze subskrypcji LUIS utworzone w czasie GA są widoczne na stronie publikowania, niezależnie od tego, czy zostały dodane do aplikacji. To zostało zrobione ułatwić GA migracji. Klucze subskrypcji LUIS nie są wyświetlane na stronie publikowania. 

## <a name="app-management"></a>Zarządzanie aplikacjami

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak przenieść własność aplikacji LUIS?
Aby przetransferować aplikacji LUIS do innej subskrypcji platformy Azure, eksportowanie aplikacji LUIS i zaimportuj go za pomocą nowego konta. Aktualizuj LUIS identyfikator aplikacji w aplikacji klienckiej, która wywołuje go. Nowa aplikacja może zwrócić LUIS nieco inne wyniki z oryginalnej aplikacji. 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Jak pobrać dziennik zniesławiających użytkownika?
Domyślnie aplikacja LUIS rejestruje zniesławiających od użytkowników. Aby pobrać dziennik zniesławiających wysyłających użytkowników do aplikacji LUIS, przejdź do **Moje aplikacje**i kliknij przycisk wielokropka (***...*** ) na liście aplikacji. Następnie kliknij przycisk **wyeksportować dzienniki punktu końcowego**. Dziennik jest w formacie pliku wartości rozdzielanych przecinkami (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Jak wyłączyć rejestrowanie zniesławiających?
Można wyłączyć rejestrowanie zniesławiających użytkownika przez ustawienie `log=false` w adresie URL punktu końcowego używaną do zapytania LUIS aplikacji klienta. Jednak wyłączenie rejestrowania wyłącza możliwość aplikację LUIS Sugeruj zniesławiających oraz pomagają w poprawieniu wydajności na podstawie kwerend użytkownika. Jeśli ustawisz `log=false` z powodu prywatności danych, nie można pobrać rekordu zniesławiających tych użytkowników z LUIS lub za pomocą tych zniesławiających zwiększyć aplikacji.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Dlaczego nie chcesz Moje zniesławiających punktu końcowego rejestrowane?
Jeśli używasz dziennik analizy prognozowania, nie należy przechwytywać zniesławiających testu w dzienniku.

## <a name="data-management"></a>Zarządzanie danymi

### <a name="can-i-delete-data-from-luis"></a>Z LUIS można usunąć dane? 

* Przykład zniesławiających używane na potrzeby uczenia LUIS zawsze można usunąć. Usunięcie z aplikacji LUIS utterance przykład, zostanie usunięta z usługi sieci web LUIS i jest niedostępny dla operacji eksportowania.
* Zniesławiających można usunąć z listy zniesławiających użytkownika, które sugeruje LUIS w **Przejrzyj zniesławiających punktu końcowego** strony. Usunięcie zniesławiających z tej listy uniemożliwia sugerowane, ale nie powoduje usunięcia ich z dzienników.
* Jeśli usuniesz konto, wszystkie aplikacje są usuwane, wraz z ich zniesławiających przykład i dzienniki. Dane są przechowywane na serwerach przez 60 dni przed jego trwałe skasowanie.

## <a name="language-and-translation-support"></a>Obsługa języka i tłumaczenia 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mam aplikacji w jednym języku i chcesz utworzyć aplikację równoległe w innym języku. Co to jest najprostszym sposobem, aby to zrobić?
1. Eksportowanie aplikacji.
2. Tłumaczenie etykietą zniesławiających w pliku JSON wyeksportowanej aplikacji języka docelowego.
3. Konieczne może być zmiana nazwy lokalizacji docelowych i jednostek lub pozostaw je, ponieważ są one.
4. Na koniec importować aplikacji na aplikację LUIS w języku docelowym.

## <a name="app-notification"></a>Powiadomienie aplikacji

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Dlaczego otrzymuję wiadomość e-mail z informacją, że chcę prawie poza limit przydziału?
Klucz tworzenia starter jest dozwolony tylko 1000 miesiąca wysyła zapytanie do punktu końcowego. Utwórz klucz subskrypcji LUIS (wolne lub płatną) i używać tego klucza, podczas tworzenia kwerend punktu końcowego. W przypadku wprowadzania zapytań punktu końcowego z robotów lub innej aplikacji, musisz zmienić klucz punktu końcowego LUIS istnieje. 

## <a name="integrating-luis"></a>Integrowanie LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Gdzie Moja aplikacja LUIS została utworzona podczas procesu subskrypcji bot aplikacji sieci web platformy Azure?
Wybierz szablon LUIS i zaznaczenie **wybierz** przycisku w okienku szablon, w okienku po lewej stronie zmiany obejmują typ szablonu i prosi o w regionie, jakie można utworzyć szablon LUIS. Proces bot aplikacji sieci web nie tworzy jednak LUIS subskrypcji.

![Region aplikacji sieci web LUIS szablonu w bot](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jakie regionów LUIS obsługuje plucia mowy Bot Framework?
[Plucia mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) jest obsługiwana tylko w przypadku aplikacji LUIS w wystąpieniu centralny (USA). 

## <a name="luis-service"></a>Usługa LUIS 

### <a name="is-luis-available-on-premise-or-in-private-cloud"></a>Działa LUIS dostępne lokalnie lub w chmurze prywatnej?
Nie. 

## <a name="changes-to-the-docs"></a>Zmiany do dokumentów

### <a name="where-did-the-tutorials-go"></a>Gdzie samouczków? 
Artykuły, które wcześniej znajdowały się w sekcji samouczka są dostępne w sekcji porad dokumentów. 

|Samouczek|
|--|
|Integracja LUIS z robotów z [C#](luis-csharp-tutorial-build-bot-framework-sample.md) i [Node.js](luis-nodejs-tutorial-build-bot-framework-sample.md)|
|Dodawanie usługi Application Insights do robotów z [C#](luis-tutorial-bot-csharp-appinsights.md) i [Node.js](luis-tutorial-function-appinsights.md)|
|Tworzenie aplikacji LUIS programowo przy użyciu [Node.js](luis-tutorial-node-import-utterances-csv.md)|
|Użyj [złożonego jednostki](luis-tutorial-composite-entity.md) wyodrębnić zgrupowanych danych|
|Dodaj [listy jednostki](luis-tutorial-list-entity.md) wykrywania zwiększona jednostki przy użyciu środowiska Node.js|
|Zwiększenia dokładności prognozy z [listy frazy](luis-tutorial-interchangeable-phrase-list.md), [wzorce](luis-tutorial-pattern.md), i [testowania partii](luis-tutorial-batch-testing.md)|
|[Popraw pisownię](luis-tutorial-batch-testing.md) z 7 API sprawdzania pisowni usługi Bing

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Na konferencji 2018 kompilacji podobno o języku opis funkcji lub demonstracyjnej, ale nie pamiętam, co zostało wywołane? 

Następujące funkcje zostały wydane na konferencji 2018 kompilacji:

|Name (Nazwa)|Zawartość|
|--|--|
|Ulepszenia|[Wyrażenie regularne](luis-concept-data-extraction.md##regular-expression-entity-data) jednostki i [frazy klucza](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) jednostki
|Wzorce|Wzorce [koncepcji](luis-concept-patterns.md), [samouczek](luis-tutorial-pattern.md), [porad](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) tym koncepcji jednostki [jawną listę](luis-concept-patterns.md#explicit-lists) wyjątków<br>[Role](luis-concept-roles.md) koncepcji|
|Integracje|[Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) integracji [analizy wskaźniki nastrojów klientów](publishapp.md#enable-sentiment-analysis)<br>[Mowy](https://docs.microsoft.com/azure/cognitive-services/speech) integracji [plucia mowy](publishapp.md#enable-speech-priming) w połączeniu z [mowy SDK](https://aka.ms/SpeechSDK)|
|Narzędzia wysyłki|Część [narzędzia BotBuilder](https://github.com/Microsoft/botbuilder-tools), wiersza polecenia wysyłania [narzędzia](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) do łączenia wielu LUIS i Maker — strona główna aplikacji w jednej aplikacji LUIS lepsze rozpoznawania konwersji w robotów

Tworzenie dodatkowych [trasy interfejsu API](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md) zostały uwzględnione. 

Wideo: 
* [Azure piątek w kompilacji 2018: Usługi kognitywnych — języka (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Pokaż AI 2018 kompilacji — Nowości dotyczące języka opis usługi](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [2018 kompilacji sesji - Bot analizy mowę i NLU najlepsze rozwiązania](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [2018 kompilacji - LUIS aktualizacji](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projekty: 
* [Bot contoso Cafe](https://github.com/botbuilderbuild2018/build2018demo) pokaz - kodu źródłowego w witrynie Github

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat LUIS, zobacz następujące zasoby:
* [LUIS oznakowane pytania przepełnienie stosu](https://stackoverflow.com/questions/tagged/luis)
* [Opis inteligentnego języka MSDN (LUIS) Forum usług](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 

[LUIS]:luis-reference-regions.md#luis-website
