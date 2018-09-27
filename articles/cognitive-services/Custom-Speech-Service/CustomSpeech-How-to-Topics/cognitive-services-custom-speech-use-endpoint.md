---
title: Użyj mowy niestandardowego punktu końcowego — usługa Custom Speech Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać niestandardowego punktu końcowego mowy na tekst z usługa Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 98c1b58e58490199b0258dfcc8df183c3fe9a8bd
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223376"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Korzystanie z niestandardowego punktu końcowego funkcji zamiany mowy na tekst
Mogą wysyłać żądania do punktu końcowego usługi Azure Custom Speech Service mowy na tekst, w podobny sposób, jak to możliwe do domyślny punkt końcowy rozpoznawania mowy usług Cognitive Services. Te punkty końcowe są funkcjonalnie identyczny domyślne punkty końcowe interfejsu API rozpoznawania mowy. Dlatego taką samą funkcjonalność, która jest dostępna za pośrednictwem biblioteki klienta lub interfejsu API REST dla interfejsu API rozpoznawania mowy jest również dostępna dla niestandardowego punktu końcowego.

Punktów końcowych, które tworzysz przy użyciu tej usługi może przetwarzać różną liczbę jednoczesnych żądań. Wolumin zależy od warstwy cenowej skojarzonych z Twoją subskrypcją. Odebranie zbyt wiele żądań, wystąpi błąd. Warstwa bezpłatna ma limit miesięczny żądań.

Usługa zakłada, że dane są przesyłane w czasie rzeczywistym. Jeśli zostanie wysłany szybciej, żądanie zostanie uznane za uruchomione, dopóki nie minął czas jego trwania audio w czasie rzeczywistym.

> [!NOTE]
> Firma Microsoft obsługuje [nowego gniazda sieci web](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) jeszcze. Jeśli planujesz użycia protokołu websocket z punktem końcowym usługi mowy niestandardowej, postępuj zgodnie z instrukcjami w tym miejscu.
>
> Nowy [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) pomocy technicznej będzie dostępna wkrótce. Jeśli planujesz wywołanie punktu końcowego usługi mowy niestandardowej za pośrednictwem protokołu HTTP, postępuj zgodnie z instrukcjami w tym miejscu.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Wysyłaj żądania za pomocą biblioteki klienta mowy

Aby wysyłać żądania do niestandardowego punktu końcowego za pomocą biblioteki klienta mowy, uruchom klienta rozpoznawania. Używaj mowy klienta zestawu SDK z [NuGet](http://nuget.org/). Wyszukaj *rozpoznawania mowy*i wybierz pakiet rozpoznawania mowy firmy Microsoft dla danej platformy. Przykładowy kod znajduje się na [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows). Zestaw SDK rozpoznawania mowy klienta zawiera klasę fabryki **SpeechRecognitionServiceFactory**, która zapewnia następujące metody:

  *   ```CreateDataClient(...)```: Dane rozpoznawania klienta.
  *   ```CreateDataClientWithIntent(...)```: Dane rozpoznawania klient z zamiarem.
  *   ```CreateMicrophoneClient(...)```: Mikrofon rozpoznawania klienta.
  *   ```CreateMicrophoneClientWithIntent(...)```Klient rozpoznawania mikrofonu, z zamiarem.

Aby uzyskać szczegółową dokumentację, zobacz [modułu Speech API Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home). Punkty końcowe usługi Custom Speech Service obsługują tego samego zestawu SDK.

Klient rozpoznawania danych jest odpowiednia dla rozpoznawania mowy z danych, takich jak plik lub innego źródła dźwięku. Klient rozpoznawania mikrofonu jest odpowiednia dla rozpoznawania mowy z mikrofonu. Korzystanie z celem w dowolnym kliencie może zwrócić ze strukturą intencji wyniki z [Language Understanding Intelligent Service](https://www.luis.ai/) (LUIS), jeśli dla danego scenariusza utworzoną aplikacją usługi LUIS.

Wszystkie cztery typy klientów mogą być utworzone na dwa sposoby. W pierwszym sposobie używana standardowa mowy interfejsu API Cognitive Services. Drugi sposób pozwala określić adres URL, który odnosi się do niestandardowego punktu końcowego utworzonych za pomocą usługa Custom Speech Service.

Na przykład można utworzyć **DataRecognitionClient** , wysyła żądania do niestandardowego punktu końcowego przy użyciu następującej metody:

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

**Your_subscriptionId** i **endpointURL** można znaleźć klucz subskrypcji i adres URL sieci web sockets, odpowiednio, na **informacje na temat wdrażania** strony.

**AuthenticationUri** jest używany do odbierania token z usługi uwierzytelniania. Ten identyfikator URI musi być ustawiony oddzielnie, jak pokazano w poniższym przykładowym kodzie.

Ten przykładowy kod pokazuje, jak używać zestawu SDK klienta:

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
> Kiedy używasz **Utwórz** metody w zestawie SDK, należy podać identyfikator subskrypcji dwukrotnie z powodu przeciążenie funkcji **Utwórz** metody.
>

Custom Speech Service używa dwóch różnych adresów URL, dla rozpoznawania krótkich i długich formularza. Oba są wyświetlane na **wdrożeń** strony. Użyj właściwego punktu końcowego adresu URL dla określonych formularza, którego chcesz użyć.

Aby uzyskać więcej informacji na temat wywoływania różnych klientów rozpoznawanie przy użyciu niestandardowego punktu końcowego, zobacz [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) klasy. Dokumentacja na tej stronie odnosi się do dostosowania model akustyczny, ale ma zastosowanie do wszystkich punktów końcowych utworzone za pomocą Custom Speech Service.

## <a name="send-requests-by-using-the-speech-protocol"></a>Wysyłaj żądania za pomocą protokołu rozpoznawania mowy

Punkty końcowe dla wyświetlane [protokołu rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) to punkty końcowe dla protokołu rozpoznawania mowy gniazda sieci Web w usłudze Otwórz źródło.

Obecnie jest implementacji tylko oficjalne klienta dla [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Jeśli chcesz rozpocząć od przykładowym, wprowadzić następujące zmiany w kodzie i ponownie skompilować przykład:

1. W _src\sdk\speech.browser\SpeechConnectionFactory.ts_, Zastąp nazwy hosta "wss://speech.platform.bing.com" z nazwą hosta wyświetlane na stronie szczegółów danego wdrożenia. Nie wstawiaj pełny identyfikator URI w tym miejscu jednak po prostu *wss* protokołu schemat i nazwy hosta. Na przykład:

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Ustaw _recognitionMode_ parametru w _samples\browser\Samples.html_ zgodnie z wymaganiami:
    * _RecognitionMode.Interactive_ obsługuje żądania do 15 sekund.
    * _RecognitionMode.Conversation_ i _RecognitionMode.Dictation_ (oba są równoważne w usłudze Custom Speech Service) żądania pomocy technicznej do 10 minut.

3. Skompilować przykład za pomocą "gulp kompilacji", zanim go użyjesz.

> [!NOTE]
> Upewnij się, użyj poprawny identyfikator URI dla tego protokołu. Wymagany schemat jest *wss* (nie *http* jak protokół klienta). 

Aby uzyskać więcej informacji, zobacz [modułu Speech API Bing](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) dokumentacji.

## <a name="send-requests-by-using-http"></a>Wysyłaj żądania przy użyciu protokołu HTTP

Wysyłanie żądania do niestandardowego punktu końcowego za pomocą metody post protokołu HTTP jest podobny do wysyłania żądania przy użyciu protokołu HTTP do interfejsu API Cognitive Services Bing mowy. Zmodyfikowanie adresu URL, aby uwzględnić adres niestandardowe wdrożenie.

Istnieją pewne ograniczenia dotyczące żądania wysyłane za pośrednictwem protokołu HTTP dla punktu końcowego mowy w usłudze Cognitive Services i niestandardowe punkty końcowe utworzone za pomocą tej usługi. Żądanie HTTP nie mogą zwracać wyniki częściowe w procesie rozpoznawania. Ponadto czas trwania żądań jest ograniczona do 10 sekund na zawartość audio i 14 sekund ogólnej.

Aby utworzyć żądanie post, wykonaj ten sam proces, którego używasz dla interfejsu API rozpoznawania mowy usług Cognitive.

1. Uzyskaj token dostępu za pomocą identyfikatora subskrypcji. Ten token jest wymagany do dostępu do endpoint rozpoznawania. Może zostać użyte przez 10 minut.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      **SubscriptionId** powinna być ustawiona na identyfikator subskrypcji, możesz użyć dla tego wdrożenia. Odpowiedź jest zwykły tokenu, czego potrzebujesz dla następnego żądania.

2. Opublikuj audio do punktu końcowego, używając POST.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    **Tokenu** jest tokenem dostępu otrzymany z poprzedniego wywołania. **Https_endpoint** jest pełny adres niestandardowego punktu końcowego mowy na tekst, na **informacje na temat wdrażania** strony.

Aby uzyskać więcej informacji na temat parametrów post protokołu HTTP i format odpowiedzi, zobacz [Microsoft Cognitive Services API rozpoznawania mowy Bing HTTP](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Wysyłaj żądania przy użyciu biblioteki usługi
Biblioteka usług umożliwia usługi umożliwiają korzystanie z chmury transkrypcji Microsoft Speech w celu Konwertuj język mówiony na tekst w czasie rzeczywistym, tak, aby wysyłać audio i otrzymywać aplikację kliencką częściowe wyniki rozpoznawania kopii równocześnie i asynchronicznie. Szczegółowe informacje na temat zestawu SDK usługi można znaleźć [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> Podczas korzystania z biblioteki usługi trzeba zmienić identyfikator URI dostawcę autoryzacji w implementacji **IAuthorizationProvider** do https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>Kolejne kroki
* Zwiększ dokładność za pomocą usługi [niestandardowy model akustyczny](cognitive-services-custom-speech-create-acoustic-model.md).
* Zwiększ dokładność z [model języka niestandardowego](cognitive-services-custom-speech-create-language-model.md).
