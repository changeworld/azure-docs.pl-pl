---
title: Punkt końcowy mowy niestandardowych za pomocą niestandardowej usługi rozpoznawania mowy na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać niestandardowych endpoint mowy na tekst z usługą mowy niestandardowe w usługach kognitywnych.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: d28065d7962ee660cafd4b3321abdd6a8f94abcb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347160"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Użyj niestandardowych endpoint mowy na tekst
Mogą wysyłać żądania do Azure niestandardowe mowy mowy na tekst punktu końcowego usługi, w podobny sposób, jak do domyślnego punktu końcowego mowy usługi kognitywnych. Te punkty końcowe są funkcjonalnie takie same jak domyślne punkty końcowe interfejsu API mowy. W związku z tym te same funkcje, który jest dostępny za pomocą biblioteki klienta lub interfejsu API REST dla interfejsu API mowy jest również dostępny dla niestandardowych punktu końcowego.

Punkty końcowe, utworzone za pomocą tej usługi może przetwarzać różne liczby równoczesnych żądań. Wolumin zależy od warstwy cenowej skojarzonych z Twoją subskrypcją. Odebranie zbyt wiele żądań, występuje błąd. Warstwa bezpłatna ma miesięczny limit żądań.

Usługa przyjęto założenie, że dane są przesyłane w czasie rzeczywistym. Jeśli jest wysyłane szybciej, żądanie jest uważany za działać do chwili upłynął jej czas audio w czasie rzeczywistym.

> [!NOTE]
> Obsługujemy [nowe gniazda sieci web](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) jeszcze. Jeśli planujesz gniazda sieci web za pomocą niestandardowych mowy punktu końcowego, postępuj zgodnie z instrukcjami w tym miejscu.
>
> Nowy [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) będzie wkrótce obsługiwany. Jeśli zamierzasz wywołać mowy niestandardowe punktu końcowego za pośrednictwem protokołu HTTP, postępuj zgodnie z instrukcjami w tym miejscu.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Wysyłanie żądań za pomocą biblioteki klienta mowy

Do wysyłania żądań do punktu końcowego niestandardowych za pomocą biblioteki klienta mowy, uruchom klienta rozpoznawania. Użyj mowy klienta SDK z [NuGet](http://nuget.org/). Wyszukaj *rozpoznawania mowy*i wybierz pakiet rozpoznawania mowy firmy Microsoft dla danej platformy. Niektóre przykładowy kod znajduje się na [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows). Zestaw SDK mowy klienta zawiera klasę fabryki **SpeechRecognitionServiceFactory**, który udostępnia następujące metody:

  *   ```CreateDataClient(...)```Klient rozpoznawania danych.
  *   ```CreateDataClientWithIntent(...)```: Dane rozpoznawania klienta z celem.
  *   ```CreateMicrophoneClient(...)```: Mikrofon rozpoznawania klienta.
  *   ```CreateMicrophoneClientWithIntent(...)```: Mikrofon rozpoznawania klienta z celem.

Szczegółowa dokumentacja zobacz [API mowy usługi Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home). Punkty końcowe usługi mowy niestandardowe obsługują tego samego zestawu SDK.

Dane klienta rozpoznawania jest odpowiednia dla rozpoznawanie mowy z danych, takich jak plik lub inne źródła audio. Klient rozpoznawania mikrofon jest odpowiednia dla rozpoznawanie mowy z mikrofonu. Korzystanie z celem w kliencie albo może zwracać strukturalne wyniki konwersji z [usługi inteligentnego opis języka](https://www.luis.ai/) (LUIS), jeśli dla danego scenariusza utworzono aplikację LUIS.

Wszystkie cztery typy klientów można wdrożyć na dwa sposoby. W pierwszym sposobie używana standardowa kognitywnych API mowy usługi. Drugi sposób można określić adres URL, który odpowiada niestandardowe punktu końcowego utworzone za pomocą niestandardowej usługi rozpoznawania mowy.

Na przykład można utworzyć **DataRecognitionClient** który wysyła żądania do punktu końcowego niestandardowych przy użyciu następujących metod:

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

**Your_subscriptionId** i **endpointURL** można znaleźć klucza subskrypcji i adres URL gniazda sieci web, odpowiednio na **informacje na temat wdrażania** strony.

**AuthenticationUri** służy do odbierania tokenu z usługi uwierzytelniania. Ten identyfikator URI musi być ustawiona oddzielnie, jak pokazano w poniższym kodzie próbki.

Ten przykładowy kod przedstawia sposób użycia zestawu SDK klienta:

```csharp
var dataClient = SpeechRecognitionServiceFactory.CreateDataClient(
  SpeechRecognitionMode.LongDictation,
  "en-us",
  "your_subscriptionId",
  "your_subscriptionId",
  "endpointURL");
// set the authorization Uri
dataClient.AuthenticationUri = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
```

> [!NOTE]
> Jeśli używasz **Utwórz** metody w zestawie SDK, należy podać identyfikator subskrypcji dwukrotnie z powodu przeciążania **Utwórz** metody.
>

Usługa rozpoznawania mowy niestandardowe używa dwóch różnych adresów URL rozpoznawania skróconej i długiej. Zarówno są wyświetlane na **wdrożeń** strony. Użyj adresu URL właściwego punktu końcowego dla określonego formularza, którego chcesz użyć.

Aby uzyskać więcej informacji na temat wywoływania różnych klientom rozpoznawania niestandardowych punktu końcowego, zobacz [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) klasy. Dokumentacja na tej stronie odwołuje się do dostosowania modelu akustycznego, ale dotyczy ona wszystkie punkty końcowe utworzone za pomocą niestandardowej usługi rozpoznawania mowy.

## <a name="send-requests-by-using-the-speech-protocol"></a>Wysyłanie żądań za pomocą protokołu rozpoznawania mowy

Punkty końcowe wyświetlany dla [protokołu mowy](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) są punkty końcowe dla protokołu mowy otwarte gniazda sieci Web źródła.

Implementacja tylko oficjalnego klienta jest obecnie dla [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Jeśli chcesz zacząć przykładowe pod warunkiem, wprowadź następujące zmiany w kodzie i ponownie utworzyć przykład:

1. W _src\sdk\speech.browser\SpeechConnectionFactory.ts_, zastąp nazwę hosta "wss://speech.platform.bing.com" nazwą hosta, wyświetlany na stronie Szczegóły wdrożenia. Nie wstawić pełny identyfikator URI tutaj ale właśnie *wss* protokołu schemat i nazwy hosta. Na przykład:

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Ustaw _recognitionMode_ parametru w _samples\browser\Samples.html_ zgodnie z wymaganiami:
    * _RecognitionMode.Interactive_ obsługuje żądania do 15 sekund.
    * _RecognitionMode.Conversation_ i _RecognitionMode.Dictation_ (oba są równoważne w niestandardowej usługi rozpoznawania mowy) obsługę żądań do 10 minut.

3. Tworzyć przykładowy kod za pomocą "system gulp kompilacji" przed jego użyciem.

> [!NOTE]
> Upewnij się, użyj poprawny identyfikator URI dla tego protokołu. Wymagany schemat jest *wss* (nie *http* jak protokół klienta). 

Aby uzyskać więcej informacji, zobacz [API mowy usługi Bing](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) dokumentacji.

## <a name="send-requests-by-using-http"></a>Wysyłanie żądań za pośrednictwem protokołu HTTP

Wysyłanie żądania do punktu końcowego niestandardowych przy użyciu protokołu HTTP post przypomina wysyłanie żądania przez HTTP kognitywnych API mowy usługi Bing. Zmodyfikuj adres URL, aby odzwierciedlały adres niestandardowego wdrożenia.

Istnieją pewne ograniczenia dotyczące żądania wysyłane za pośrednictwem protokołu HTTP dla punktu końcowego kognitywnych mowy usługi i niestandardowe punkty końcowe, utworzone za pomocą tej usługi. Żądanie HTTP nie może zwracać wyniki częściowe podczas procesu rozpoznawania. Ponadto czas trwania żądania jest ograniczone do 10 sekund do zawartości audio i 14 sekund ogólnej.

Aby utworzyć żądanie post, wykonaj te same czynności, używanych w przypadku kognitywnych Services API mowy.

1. Uzyskaj token dostępu za pomocą swojego identyfikatora subskrypcji. Token ten jest wymagane do uzyskania dostępu rozpoznawania punktu końcowego. Mogą być ponownie używane przez 10 minut.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      **SubscriptionId** powinien być ustawiony na identyfikator subskrypcji, możesz użyć dla tego wdrożenia. Odpowiedź jest zwykły token potrzebnych dla następnego żądania.

2. Po audio z punktem końcowym, używając POST.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    **Tokenu** jest token dostępu otrzymany z poprzedniego wywołania. **Https_endpoint** jest pełny adres Twojej niestandardowych końcowego mowy na tekst wyświetlany na **informacje na temat wdrażania** strony.

Aby uzyskać więcej informacji na temat parametrów post protokołu HTTP i format odpowiedzi, zobacz [kognitywnych usług Bing mowy HTTP interfejsu API usługi Microsoft](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Wysyłanie żądania przy użyciu biblioteki usługi
Biblioteka usługi umożliwia usłudze użyć chmury przekształcania Microsoft Speech można przekonwertować na tekst w czasie rzeczywistym język rozmowy, tak, aby Twoja aplikacja kliencka można wysyłać audio i odbierać wyniki częściowe rozpoznawania wstecz równocześnie i asynchronicznie. Szczegóły zestawu SDK usług można znaleźć [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> Podczas korzystania z biblioteki usługi trzeba zmienić identyfikator URI dostawcy autoryzacji w ramach wdrożenia **IAuthorizationProvider** do https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>Kolejne kroki
* Zwiększyć dokładność z Twojej [niestandardowych modelu akustycznego](cognitive-services-custom-speech-create-acoustic-model.md).
* Zwiększyć dokładność z [model niestandardowych języka](cognitive-services-custom-speech-create-language-model.md).
