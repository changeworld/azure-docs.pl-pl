---
title: O opis języka (LUIS) na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą języka opis (LUIS) możliwości aplikacji uczenia maszynowego.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/22/2017
ms.author: v-geberr
ms.openlocfilehash: c40c643abefa609017ef76209ecc0d20a636f71b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266100"
---
# <a name="what-is-language-understanding-luis"></a>Co to jest opis języka (LUIS)?
Opis języka (LUIS) to usługa oparta na chmurze, która dotyczy niestandardowej uczeniu maszynowym konwersacji, tekstu języka naturalnego użytkownika do prognozowania ogólnego znaczenia i wysunąć istotne, szczegółowe informacje. 

Aplikacja kliencka dla LUIS można dowolnej konwersacji aplikacji, która komunikuje się z użytkownikiem w języku naturalnym do wykonania zadania. Przykładami aplikacje klienckie aplikacji mediów społecznościowych, chatbots i aplikacje z obsługą mowy.  

![Obrazu koncepcyjnego 3 aplikacji informacje info LUIS zasilania](./media/luis-overview/luis-entry-point.png)

Aplikacja kliencka (na przykład chatbot) wysyła tekst użytkownika osoba chce własnymi słowami do LUIS w żądaniu HTTP. LUIS dotyczy nauczony model języka naturalnego rozsądnie danych wejściowych użytkownika i zwraca odpowiedź formatu JSON. Aplikacja kliencka używa odpowiedzi JSON odpowiedzi na żądania użytkownika. 

![Koncepcyjny obrazów z LUIS Praca z Chatbot](./media/luis-overview/luis-overview-process-2.png)

## <a name="what-is-a-luis-app"></a>Co to jest aplikacja LUIS?
Aplikacji LUIS to model języka specyficznego dla domeny, które projektowania. Można uruchomić aplikacji z modelem wbudowane domeny, tworzenie własnych lub blend części wbudowane domeny niestandardowe informacje o.

Model rozpoczyna się od listy zamiarach użytkownika ogólne, nazywany _intencje_, na przykład "Książki lot" lub "Skontaktuj się z pomocą techniczną." Podaj fraz przykład użytkownika, nazywany _zniesławiających_ dla lokalizacji docelowych. Następnie oznacz znaczących słów ani fraz w utterance, nazywany _jednostek_.

[Modele wbudowane domeny] [ prebuilt-domains] zawierają wszystkie te elementy i to doskonały sposób na rozpoczęcie korzystania z LUIS szybko.

<a name="Accessing-LUIS"></a>

Po modelu jest wbudowana i opublikowane, aplikacja kliencka wysyła zniesławiających LUIS [punkt końcowy interfejsu API] [ endpoint-apis] i otrzymuje wyniki prognozowania jako odpowiedzi JSON.

### <a name="example-of-json-endpoint-response"></a>Przykład odpowiedź w formacie JSON punktu końcowego

Odpowiedzi JSON punktu końcowego, co najmniej zawiera utterance zapytania i oceniania zamiar górnej. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

<a name="Key-LUIS-concepts"></a>

## <a name="what-is-a-luis-model"></a>Co to jest LUIS model?
LUIS model zawiera następujące elementy:

* **[intencje](#intents)**: kategorie zamiarach użytkownika (zamierzonej akcji lub wynik)
* **[jednostki](#entities)**: określonych typów danych w zniesławiających, takie jak nazwa, wiadomości e-mail lub numer
* **[przykład zniesławiających](#example-utterances)**: Przykładowy tekst użytkownik wprowadzi w aplikacji klienta

### <a name="intents"></a>Intencje 
[Zamiar][add-intents], skrót _zamiar_, jest celem lub celem wyrażone w utterance użytkownika, takie jak rezerwacji lot, zwracając rachunek lub wyszukiwanie artykułu wiadomości. Możesz utworzyć celem dla każdej akcji. Aplikacja podróży może zdefiniować celem o nazwie "BookFlight." Aplikacja kliencka służy górnej oceniania zamiar akcja wyzwalacza. Na przykład "BookFlight" zamiar zwracanie z LUIS aplikacji klienta może wyzwolić wywołanie interfejsu API dla rezerwacji biletu płaszczyzny zewnętrznej usługi.

### <a name="entities"></a>Jednostki
[Jednostki] [ add-entities] reprezentuje szczegółowe informacje w utterance, istotnych dla żądania użytkownika. Na przykład w utterance "Książki bilet, aby Paryża" bilet pojedynczego żądania, a "Paryża" prowadzi do lokalizacji. Dwie jednostki znaleziono "biletu" pojedynczego biletu i wskazujący miejsce docelowe "Paryża". 

Po LUIS zwraca jednostki, w utterance użytkownika, aplikacja klienta można użyć listy jednostek jako parametry akcji wyzwalane. Na przykład rezerwacji lot wymaga jednostek, takich jak celem podróży, datę i linii lotniczych.

LUIS udostępnia kilka sposobów, aby zidentyfikować i kategoryzowanie jednostek.

* **Wbudowane jednostek** LUIS ma wiele modeli domeny wbudowane profile, zniesławiających, w tym i [wbudowane jednostek][prebuilt-entities]. Wbudowane jednostek można użyć bez konieczności używania intencje i zniesławiających wbudowane modelu. Wbudowane jednostki zaoszczędzić czas.

* **Niestandardowych obiektów** LUIS udostępnia kilka sposobów, aby zidentyfikować własne [jednostek] [ entity-concept] tym jednostek rozpoznane maszyny, określonych lub literałem jednostek i kombinację rozpoznane maszyny i literału.

### <a name="example-utterances"></a>Przykład zniesławiających
Przykład [utterance] [ add-example-utterances] jest wprowadzanie tekstu od użytkownika, które aplikacji należy zrozumieć. Może być zdania, takich jak "Zarezerwować biletu do Paryża" lub fragmentu zdania, takich jak "Rezerwacji" lub "Paryża transmitowane." Zniesławiających nie zawsze są poprawnie sformułowany i może istnieć wiele zmian utterance dla konkretnego zamiaru. Dodaj 10-20 przykład zniesławiających do każdego zamiar i oznacz jednostek w każdym utterance.

|Przykład utterance użytkownika|Celem|Jednostki|
|-----------|-----------|-----------|
|"Zarezerwować lot __Seattle__?"|BookFlight|Seattle|
|"Jeśli jest sklepu __Otwórz__?"|StoreHoursAndLocation|otwórz|
|"Zaplanuj spotkanie w __13 będzie__ z __Bob__ w dystrybucji"|ScheduleMeeting|13: 00, Roberta|

## <a name="improve-prediction-accuracy"></a>Poprawić precyzję przewidywania
Po aplikacja została opublikowana i odbiera zniesławiających rzeczywistego użytkownika, LUIS zapewnia kilka metod w celu zwiększenia dokładności prognozy: [active learning](#active-learning) z punktu końcowego zniesławiających, [frazę list](#phrase-lists) dla Włączenie programu word domeny, i [wzorce](#patterns) Aby zmniejszyć liczbę zniesławiających potrzebne.

### <a name="active-learning"></a>Aktywne uczenie
W [active learning](label-suggested-utterances.md) proces, LUIS pozwala dostosować aplikację, do rzeczywistych zniesławiających, wybierając zniesławiających odebrała w punkcie końcowym do przeglądu. Można zaakceptować lub Popraw prognozowania punktu końcowego, ponownego próbkowania i ponownie opublikować dane. LUIS uczy się szybko z tym procesem iteracyjnym biorąc minimalna ilość czasu i uwagi. 

### <a name="phrase-lists"></a>Wyrażenie listy 
Udostępnia LUIS [zwroty list](luis-concept-feature.md) tak ważne słów ani fraz może wskazywać do modelu domeny. LUIS używa tych list można dodać dodatkowe znaczenie do tych słów i wyrażeń, które mogłyby w przeciwnym razie nie można odnaleźć w modelu.

### <a name="patterns"></a>Wzorce 
Wzorce pozwalają uprościć zamiar utterance kolekcji do wspólnego [szablony] [ patterns] word wybór i kolejność słów. Dzięki temu LUIS dowiedzieć się przyspieszają przez wymagające mniejszej liczby zniesławiających przykład dla opcji. Wzorce mogą system hybrydowego rozpoznane maszyny wyrażeń i wyrażenia regularne. 

## <a name="using-luis"></a>Przy użyciu LUIS
Można utworzyć aplikację LUIS z [www.luis.ai](http://www.luis.ai) witryny sieci Web lub możesz utworzyć aplikację programistycznie z [tworzenia](https://aka.ms/luis-authoring-apis) interfejsów API. Dostęp do opublikowanej aplikacji LUIS przez zapytanie [punktu końcowego](https://aka.ms/luis-endpoint-apis). 

## <a name="what-technologies-work-with-luis"></a>Które technologie współpracuje z LUIS?
Kilka technologii firmy Microsoft pracować z LUIS:

* [API sprawdzania pisowni usługi Bing] [ bing-spell-check-api] zapewnia korekty tekstu przed prognozowania. 
* [Bot Framework] [ bot-framework] umożliwia chatbot do komunikowania się z użytkownikiem za pośrednictwem wprowadzania tekstu. Wybierz [3.x](https://github.com/Microsoft/BotBuilder) lub [4.x](https://github.com/Microsoft/botbuilder-dotnet) zestawu SDK dla środowiska bot pełną.
* [Maker — strona główna] [ qnamaker] umożliwia kilka typów tekstu do łączenia w bazie wiedzy pytania i odpowiedzi.
* [Mowy] [ speech] konwertuje żądań używany na tekst. Po konwersji na tekst, LUIS przetwarza żądania. Zobacz [mowy SDK](https://aka.ms/csspeech) Aby uzyskać więcej informacji.
* [Analiza tekstu] [ text-analytics] zawiera wskaźniki nastrojów klientów analizy i klucz frazy danych wyodrębniania.

## <a name="next-steps"></a>Kolejne kroki
Utwórz [nowej aplikacji LUIS](LUIS-get-started-create-app.md).

<!-- Reference-style links -->
[create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[azure-portal]: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account
[publish-app]: https://docs.microsoft.com/azure/cognitive-services/luis/PublishApp#test-your-published-endpoint-in-a-browser
[luis-concept-entity-types]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types
[add-example-utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-add-example-utterances
[prebuilt-entities]: https://docs.microsoft.com/azure/cognitive-services/luis/pre-builtentities
[prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-use-prebuilt-domains
[label-suggested-utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/label-suggested-utterances
[intro-video]: https://aka.ms/LUIS-Intro-Video
[bot-framework]: https://docs.microsoft.com/bot-framework/
[speech]: https://docs.microsoft.com/azure/cognitive-services/Speech/index.md
[flow]: https://docs.microsoft.com/connectors/luis/
[entity-concept]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types
[add-intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-add-intents
[add-entities]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-add-entities
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[text-analytics]: https://azure.microsoft.com/services/cognitive-services/text-analytics/
[patterns]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-patterns
[bing-spell-check-api]: https://azure.microsoft.com/services/cognitive-services/spell-check/
[qnamaker]: https://qnamaker.ai/