---
title: Publikowanie aplikacji usługi LUIS | Dokumentacja firmy Microsoft
description: Po utworzeniu i przetestuj aplikację za pomocą usługi Language Understanding (LUIS), opublikuj go jako usługę sieci web na platformie Azure.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 1bd24af0498755b7cdcb170624fd8f9f3b39c85a
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341293"
---
# <a name="publish-your-trained-app"></a>Opublikuj aplikację uczonego
Po zakończeniu tworzenia i testowania aplikacji usługi LUIS, należy go opublikować. Po opublikowaniu aplikacji, na stronie publikowania znajdują się wszystkie skojarzone HTTP [punktów końcowych](luis-glossary.md#endpoint). Te punkty końcowe na [region](luis-reference-regions.md) i [klucz](luis-how-to-manage-keys.md), następnie są zintegrowane z dowolnej aplikacji klienta, chatbot lub wewnętrznej bazy danych. 

Zawsze możesz [test](interactive-test.md) aplikacji przed jej opublikowaniem. 

## <a name="production-and-staging-slots"></a>Produkcyjne oraz przejściowe miejsce
Możesz opublikować aplikację, aby **miejsce przejściowe** lub **miejscem produkcyjnym**. Korzystając z dwóch miejsc publikowania, dzięki temu można mieć dwie różne wersje z punktami końcowymi opublikowane lub tej samej wersji na dwa różne punkty końcowe. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>Konfiguracja ustawień wymaga modelu publikowania
Publikowanie do punktu końcowego, po wprowadzeniu zmian w następujących ustawieniach. 

## <a name="external-services-settings"></a>Ustawienia usług zewnętrznych
Ustawienia zewnętrznej usługi obejmują **[analizę tonacji](#enable-sentiment-analysis)** i  **[zalewanie mowy](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Włącz analizę tonacji
W **ustawienia usług zewnętrznych**, **Włącz analizę tonacji** pola wyboru pozwala LUIS w celu integracji z [analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) tonację i kluczowe frazy Analiza. Nie trzeba podać klucz analizy tekstu, a nie są pobierane opłaty rozliczeń dla tej usługi do konta platformy Azure. Jeśli zaznaczysz to ustawienie, jest trwały. 

Dane opinii jest wynik w zakresie od 1 i 0, wskazując pozytywny (bliżej 1) lub ujemną (bliżej 0) tonacji danych.

Aby uzyskać więcej informacji na temat odpowiedzi JSON punktu końcowego za pomocą analizy opinii, zobacz [analizy tonacji](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>Włącz zalewanie mowy 
W **ustawienia usług zewnętrznych**, **Włącz zalewanie mowy** pola wyboru pozwala na korzystanie z pojedynczego punktu końcowego pobieranie wypowiedzianych wypowiedź z chatbot lub aplikacja wywołująca LUIS i odbierać usługi LUIS Prognozowanie odpowiedzi. Zalewanie mowy używa usługi Cognitive service [interfejsu API rozpoznawania mowy](../Speech-Service/rest-apis.md). 

![Obraz okna dialogowego potwierdzenia zalewanie mowy](./media/luis-how-to-publish-app/speech-prime-modal.png)

Po włączeniu tej funkcji, Opublikuj aplikację. Po opublikowaniu aplikacji LUIS modelu aplikacji są wysyłane do usługi rozpoznawania mowy zainicjowanie usługi mowy. Informacje o modelu są **nie** używane poza własnej usługi. 

Aby można było ukończyć użytkowania zalewanie mowy, potrzebne są następujące informacje do użycia w [zestaw SDK rozpoznawania mowy](../speech-service/speech-sdk-reference.md):
* Klucz punktu końcowego usługi LUIS.
* Identyfikator aplikacji usługi LUIS.
* Domeny punktu końcowego, określane jako "Hostname" w zestawie SDK rozpoznawania mowy, takie jak "westus.api.cognitive.microsoft.com", gdzie pierwszy domeny podrzędnej jest region, w którym aplikacja zostanie opublikowana.

Aby uzyskać więcej informacji, zobacz [zamiana mowy na intencję](http://aka.ms/speechsdk) próbki.

Po usunięciu aplikacją usługi LUIS lub usługa mowy zostanie usunięta, dane modelu są usuwane. 

## <a name="endpoint-url-settings"></a>Ustawienia adresu URL punktu końcowego
Ustawienia usług adres URL punktu końcowego obejmują **[strefy czasowej](#set-timezone-offset)** przesunięcia,  **[wszystkie przewidywane wyniki intencji](#include-all-predicted-intent-scores)**, i  **[ Moduł sprawdzania pisowni Bing](#enable-bing-spell-checker)**.

### <a name="set-timezone-offset"></a>Ustawianie przesunięcia strefy czasowej 
Wybór miejsca jest wybór strefy czasowej. To ustawienie strefy czasowej umożliwia usługi LUIS do [alter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) ilekroć wstępnie datetimeV2 wartości podczas prognozowania tak, aby dane zwrócone jednostki jest prawidłowa i zgodna z wybranej strefie czasowej. 

### <a name="include-all-predicted-intent-scores"></a>Obejmują wszystkie przewidywanych wskaźników intencji
**Uwzględnij wszystkie przewidywane wyniki intencji** pola wyboru pozwala odpowiedzi na zapytanie punkt końcowy do uwzględnienia wyników prognoz dla każdego intencji. 

To ustawienie pozwala używać chatbot lub wywoływania usługi LUIS aplikacji, aby wprowadzić programowe decyzję oparciu o wyniki zwrócone intencji. Ogólnie rzecz biorąc najważniejsze intencji dwa są najbardziej interesujące. W przypadku oceny najważniejszych intencji Twojego chatbot możliwość Zadaj pytanie monitowania, dzięki której ostatecznego wyboru między Brak intencji i oceniania wysokiej intencji None. 

Intencji i ich wyniki są również uwzględnione w dziennikach punktu końcowego. Możesz [wyeksportować](create-new-app.md#export-app) tych dzienników i przeanalizować wyniki. 

```
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Włączanie sprawdzania pisowni Bing 
W **ustawienia adresu url punktu końcowego**, **sprawdzania pisowni Bing Włącz** pola wyboru pozwala usługi LUIS do poprawianie błędnie napisanych wyrazów przed prognozy. To wymaga utworzenia  **[klucz sprawdzania pisowni Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. Po utworzeniu klucza dwa parametry querystring są dodawane do adresu URL punktu końcowego, na stronie publikowania. 

Jeśli własne adresy URL są konstruowanie aplikacji telefonicznej usługi LUIS, upewnij się, **sprawdzania pisowni = true** parametr querystring i **bing pisowni wyboru subscription-key = {YOUR_BING_KEY_HERE}**. Zastąp `{YOUR_BING_KEY_HERE}` kluczem moduł sprawdzania pisowni Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>Opublikuj aplikację przeszkolonych na punkcie końcowym HTTP
Otwórz aplikację, klikając jego nazwę **Moje aplikacje** strony, a następnie kliknij przycisk **Publikuj** w górnym panelu. 

![Publikowanie strony —](./media/luis-how-to-publish-app/publish-to-production.png)
 
Po pomyślnym opublikowaniu aplikacji u góry strony w przeglądarce zostanie wyświetlone powiadomienie o powodzeniu zielony. 

* Określ, czy chcesz opublikować **produkcji** lub **przemieszczania** , wybierając z menu rozwijane w obszarze **wybierz miejsce**. 

## <a name="assign-key"></a>Przypisz klucza

Aby klucza innego niż Starter_Key bezpłatnej, wyświetlany, kliknij pozycję **Dodaj klucz** przycisku. Ta akcja powoduje otwarcie okna dialogowego, które umożliwia wybranie istniejącego klucza punktu końcowego, który można przypisać do niej. Aby uzyskać więcej informacji na temat sposobu tworzenia i Dodaj punkt końcowy kluczy z aplikacją usługi LUIS, zobacz [zarządzanie kluczami](luis-how-to-manage-keys.md).

Aby wyświetlić punkty końcowe i klucze skojarzone z innymi regionami, należy użyć przycisków radiowych do przełączania regionów. Każdy wiersz w **zasobów i klucze** tabela zawiera listę zasobów platformy Azure skojarzony z Twoim kontem i kluczami punktu końcowego, skojarzone z tego zasobu.

## <a name="endpoint-url-construction"></a>Konstrukcja adres URL punktu końcowego
Adres URL punktu końcowego odnosi się do regionu platformy Azure skojarzoną z kluczem punktu końcowego.

Ta tabela odzwierciedla wygodny sposób publikowania konfiguracji w programie punktu końcowego adresu URL za pomocą opcji trasy i wartości ciągu zapytania. Jeśli są konstruowanie adresami URL punktu końcowego dla aplikacji usługi LUIS wywoływania, upewnij się, używać tych tras w tej samej i ciągu zapytania, wartości są ustawiane dla punktu końcowego — należy je ustawić.

Trasy adresu URL jest zbudowany z regionu i identyfikator aplikacji. W przypadku publikowania w innych regionach lub w innych aplikacjach można skonstruować adres URL punktu końcowego, zmieniając wartości Identyfikatora regionu i aplikacji. 

* Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj). Po pomyślnym zakończeniu publikowania, należy użyć adres URL punktu końcowego wyświetlanych dostęp do aplikacji usługi LUIS. 

### <a name="optional-query-string-parameters"></a>Parametry ciągu zapytania opcjonalne
Następujące parametry ciągu zapytania może służyć do adresu URL punktu końcowego:

<!-- TBD: what about speech priming? -->

|Ciąg zapytania|Typ|Przykładowa wartość|Przeznaczenie|
|--|--|--|--|
|pełne|wartość logiczna|true|Obejmują [wszystkie wyniki intencji](#include-all-predicted-intent-scores) dla wypowiedź|
|timezoneOffset|Liczba (jednostka jest minut)|60|Ustaw [przesunięcie strefy czasowej](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) dla [datetimeV2 ze wstępnie utworzonych jednostek](luis-reference-prebuilt-datetimev2.md)|
|Sprawdzanie pisowni|wartość logiczna|true|[poprawianie pisowni](#enable-bing-spell-checker) z wypowiedź — używany w połączeniu z parametru ciągu zapytania usługi bing — pisowni wyboru subscription-key|
|Bing — pisowni wyboru subscription-key|Identyfikator subskrypcji||używany w połączeniu z parametru ciągu zapytania sprawdzania pisowni|
|przemieszczania|wartość logiczna|false|Wybierz punkt końcowy przejściowych lub produkcyjnych|
|Dziennik|wartość logiczna|true|Dodawanie zapytań i wyniki do logowania|


## <a name="test-your-published-endpoint-in-a-browser"></a>Testowanie opublikowanej punktu końcowego w przeglądarce
Testowanie opublikowanej punktu końcowego, wybierając adresu URL w **punktu końcowego** kolumny. W domyślnej przeglądarce otworzy się za pomocą wygenerowanego adresu URL. Ustaw parametr adresu URL "& pytania" do zapytania testu. Na przykład dołączyć `&q=Book me a flight to Boston on May 4` do adresu URL, a następnie naciśnij klawisz Enter. W przeglądarce pojawi się odpowiedź JSON punktu końcowego HTTP. 

![Odpowiedź JSON z opublikowanych punkt końcowy HTTP](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>Kolejne kroki

* Zobacz [zarządzanie kluczami](./luis-how-to-manage-keys.md) do dodawania kluczy z aplikacją usługi LUIS i Dowiedz się, jak klucze mapowania regionów.
* Zobacz [szkolenie i testowanie aplikacji](interactive-test.md) instrukcje na temat testowania Twojej opublikowanej aplikacji w konsoli testów.
