---
title: Publikowanie aplikacji LUIS | Dokumentacja firmy Microsoft
description: Po utworzeniu i testowanie aplikacji za pomocą języka opis (LUIS), należy opublikować go jako usługi sieci web na platformie Azure.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 12a63e65a739be08d436f8f1b53df566255b1fb1
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36322058"
---
# <a name="publish-your-trained-app"></a>Publikowanie aplikacji przeszkolone
Po zakończeniu tworzenia i testowania aplikacji LUIS go opublikować. Po opublikowaniu aplikacji strony publikowania zawiera wszystkie skojarzone HTTP [punkty końcowe](luis-glossary.md#endpoint). Te punkty końcowe na [region](luis-reference-regions.md) i na [klucza](Manage-Keys.md), następnie są zintegrowane z dowolnej aplikacji klienta, chatbot lub wewnętrznej bazy danych. 

Możesz zawsze [test](interactive-test.md) aplikacji przed jej opublikowaniem. 

## <a name="production-and-staging-slots"></a>Produkcyjne i przejściowe gniazd
Możesz opublikować aplikację, aby **miejsca przemieszczania** lub **gniazda produkcyjnego**. Za pomocą dwóch miejsc publikowania, dzięki temu można mieć dwie różne wersje z opublikowanych punktów końcowych lub tej samej wersji na dwóch różnych punktów końcowych. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>Konfiguracja ustawień wymaga modelu publikowania
Opublikuj punkt końcowy po wprowadzeniu zmian w następujących ustawieniach. 

## <a name="external-services-settings"></a>Ustawienia usług zewnętrznych
Zewnętrzna usługa ustawienia obejmują **[analizy wskaźniki nastrojów klientów](#enable-sentiment-analysis)** i  **[Priming mowy](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Włącz analizę wskaźniki nastrojów klientów
W **ustawienia usług zewnętrznych**, **włączyć analizy wskaźniki nastrojów klientów** wyboru pozwala LUIS do integracji z [Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) zapewnienie wskaźniki nastrojów klientów i hasło klucza Analiza. Nie musisz podać klucz Analiza tekstu i bez żadnych opłat rozliczeń dla tej usługi do konta platformy Azure nie istnieje. Jeśli zaznaczysz to ustawienie jest trwały. 

Wskaźniki nastrojów klientów danych jest wynikiem między 1 i wskazujący dodatnich 0 (bliżej 1) lub ujemną (bliżej 0) wskaźniki nastrojów klientów danych.

Aby uzyskać więcej informacji na temat odpowiedzi JSON punktu końcowego z analizą wskaźniki nastrojów klientów, zobacz [analizy wskaźniki nastrojów klientów](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>Włącz plucia mowy 
W **ustawienia usług zewnętrznych**, **włączyć Priming mowy** wyboru pozwala uzyskać rozmowy utterance od chatbot lub aplikacji telefonicznej LUIS i odbierać LUIS jeden punkt końcowy odpowiedź prognozowania. Plucia mowy korzysta z usługi kognitywnych [Speech API](../Speech-Service/rest-apis.md). 

![Obraz okna dialogowego potwierdzenia plucia mowy](./media/luis-how-to-publish-app/speech-prime-modal.png)

Po włączeniu tej funkcji publikowania aplikacji. Podczas publikowania aplikacji LUIS modelu aplikacji są wysyłane do usługi mowy do zapisują usługi mowy. Informacje o modelu jest **nie** używane poza własnej usługi. 

Aby można było wykonywać plucia mowy, należy następujące informacje do użycia w [mowy SDK](../speech-service/speech-sdk-reference.md):
* Klucz LUIS subskrypcji.
* Identyfikator LUIS aplikacji.
* Domeny punktu końcowego określane jako "Nazwa hosta" w zestawie SDK mowy, takie jak "westus.api.cognitive.microsoft.com", gdzie pierwszy poddomeny to region, w którym aplikacja została opublikowana.

Aby uzyskać więcej informacji, zobacz [mowy do zamiar](http://aka.ms/speechsdk) próbki.

Po usunięciu LUIS aplikacji lub usługi mowy zostanie usunięta, Twoje dane modelu zostaną usunięte. 

## <a name="endpoint-url-settings"></a>Ustawienia adresu URL punktu końcowego
Ustawienia usług adres URL punktu końcowego obejmują **[strefy czasowej](#set-timezone-offset)** przesunięcia,  **[przewidzieć wszystkie wyniki konwersji](#include-all-predicted-intent-scores)**, i  **[ Moduł sprawdzania pisowni usługi Bing](#enable-bing-spell-checker)**.

### <a name="set-timezone-offset"></a>Ustawianie przesunięcia strefy czasowej 
Część wybór miejsca jest wybór strefy czasowej. To ustawienie strefy czasowej umożliwia LUIS do [alter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) kiedykolwiek datetimeV2 wbudowane wartości podczas prognozowania tak, aby dane zwróconą jednostkę są prawidłowe w zależności od wybranej strefie czasowej. 

### <a name="include-all-predicted-intent-scores"></a>Obejmują wszystkie wyniki konwersji przewidywane
**Uwzględnianie wszystkich przewidzieć wyniki konwersji** wyboru umożliwia uwzględnienie przewidywania wyników dla każdego zamiar odpowiedzi na kwerendę punktu końcowego. 

To ustawienie umożliwia z chatbot lub aplikacji telefonicznej LUIS podjęcie decyzji programowy oparty na wyniki zwrócone lokalizacji docelowych. Ogólnie top dwie opcje są najbardziej interesujące. Jeśli wynik top jest brak konwersji chatbot Twojego można wybrać zadać pytanie monitowania, które służą do ostatecznego wybór między brak konwersji i oceniania wysokiej zamiar. 

Opcje i ich wyniki są również zawarte w dziennikach punktu końcowego. Możesz [wyeksportować](create-new-app.md#export-app) tych dzienników i Analizuj wyniki. 

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

### <a name="enable-bing-spell-checker"></a>Włącz moduł sprawdzania pisowni usługi Bing 
W **ustawienia adresu url punktu końcowego**, **Bing Włącz moduł sprawdzania pisowni** wyboru pozwala LUIS można poprawić pisowni przed prognozowania. To wymaga utworzenia  **[klucz sprawdzania pisowni usługi Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. Po utworzeniu klucza dwa parametry querystring są dodawane do adresu URL punktu końcowego na stronie publikowania. 

Jeśli własne adresy URL są konstruowanie aplikacji telefonicznej LUIS, upewnij się, **sprawdzanie pisowni = true** parametr querystring i **bing pisowni wyboru subskrypcji — klucz = {YOUR_BING_KEY_HERE}**. Zastąp `{YOUR_BING_KEY_HERE}` kluczem moduł sprawdzania pisowni usługi Bing.

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

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>Publikowanie aplikacji przeszkolone punkt końcowy HTTP
Otwórz aplikację, klikając jej nazwę na **Moje aplikacje** , a następnie kliknij przycisk **publikowania** w górnym panelu. 

![Publikowanie strony](./media/luis-how-to-publish-app/publish-to-production.png)
 
Gdy aplikacja zostanie pomyślnie opublikowana, w górnej części przeglądarce pojawi się powiadomienie zielony Powodzenie. 

* Wybierz, czy chcesz opublikować **produkcji** lub **przemieszczania** , wybierając z menu rozwijanego w obszarze **wybierz gniazdo**. 

## <a name="assign-key"></a>Przypisz klucza

Jeśli chcesz użyć klucza innych niż Starter_Key bezpłatne, wyświetlany, kliknij przycisk **Dodaj klucz** przycisku. Ta akcja powoduje otwarcie okna dialogowego, które można wybrać istniejący klucz punktu końcowego można przypisać do aplikacji. Aby uzyskać więcej informacji dotyczących sposobu tworzenia i Dodaj punkt końcowy klucze do aplikacji LUIS, zobacz [zarządzanie kluczami](Manage-Keys.md).

Aby wyświetlić punkty końcowe i klucze skojarzone z innych regionów, użyj przycisków radiowych, aby przełączyć regionów. Każdy wiersz w **zasobów i klucze** tabela zawiera listę zasobów platformy Azure skojarzonych z Twoim kontem i klucze punktu końcowego skojarzonego z tym zasobem.

## <a name="endpoint-url-construction"></a>Konstruowania adresu URL punktu końcowego
Adres URL punktu końcowego odpowiada region platformy Azure skojarzoną z kluczem punktu końcowego.

Ta tabela odzwierciedla wygodny sposób publikowania konfiguracji w programie punktu końcowego adresu URL, z opcjami trasy i wartości ciągu zapytania. Jeśli są konstruowania adresami URL punktu końcowego dla aplikacji telefonicznej LUIS, upewnij się, te trasy tego samego i ciągu zapytania, wartości są ustawiane dla punktu końcowego używane — należy je ustawić.

Trasy adresu URL jest tworzony z regionu i identyfikator aplikacji. W przypadku publikowania w różnych regionach lub w innych aplikacjach, adres URL punktu końcowego można konstruować przez zmianę wartości Identyfikatora regionu i aplikacji. 

* Wybierz miejsca produkcyjnego i **publikowania** przycisku. Podczas publikowania zakończy się powodzeniem, umożliwia dostęp do aplikacji LUIS wyświetlanych końcowego adresu URL. 

### <a name="optional-query-string-parameters"></a>Parametry ciągu zapytania opcjonalne
Następujące parametry ciągu zapytania mogą służyć do adresu URL punktu końcowego:

<!-- TBD: what about speech priming? -->

|Ciąg zapytania|Typ|Przykładowa wartość|Przeznaczenie|
|--|--|--|--|
|pełne|wartość logiczna|true|Obejmują [wszystkie wyniki konwersji](#include-all-predicted-intent-scores) dla utterance|
|wartości timezoneOffset|numer (jednostka jest w minutach)|60|Ustaw [przesunięcie strefy czasowej](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) dla [datetimeV2 wbudowane jednostek](luis-reference-prebuilt-datetimev2.md)|
|Sprawdzanie pisowni|wartość logiczna|true|[Popraw pisownię](#enable-bing-spell-checker) z utterance — używany w połączeniu z parametru ciągu zapytania usługi bing pisowni wyboru subskrypcji klucza|
|Bing pisowni wyboru subskrypcji klucza|Identyfikator subskrypcji||używane w połączeniu z parametru ciągu zapytania sprawdzanie pisowni|
|przemieszczania|wartość logiczna|false|Wybierz punkt końcowy tymczasowym czy produkcyjnym|
|Dziennik|wartość logiczna|true|Dodawanie zapytań i wyniki do logowania|


## <a name="test-your-published-endpoint-in-a-browser"></a>Testowanie opublikowanego punktu końcowego w przeglądarce
Testowanie opublikowanego punktu końcowego, wybierając adres URL w **punktu końcowego** kolumny. W domyślnej przeglądarce otworzy z wygenerowany adres URL. Ustaw parametr adresu URL "& q" w kwerendzie testu. Na przykład Dołącz `&q=Book me a flight to Boston on May 4` do adresu URL, a następnie naciśnij klawisz Enter. W przeglądarce pojawi się odpowiedzi JSON punktu końcowego HTTP. 

![Odpowiedź JSON z opublikowanych punkt końcowy HTTP](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>Kolejne kroki

* Zobacz [zarządzanie kluczami](./Manage-Keys.md) Dodawanie kluczy do aplikacji LUIS i więcej informacji na temat sposobu mapowania klucze regionów.
* Zobacz [pociągu i testowanie aplikacji](interactive-test.md) instrukcje na temat testowania opublikowanych aplikacji w konsoli testu.
