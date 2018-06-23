---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft w języku Objective-C w systemie iOS | Dokumentacja firmy Microsoft
description: Umożliwia tworzenie aplikacji systemu iOS, które przekonwertowana na tekst rozmowy audio API rozpoznawania mowy firmy Microsoft.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: bbb8d3975cdab537135b97ca9bbf6e845aa3fa0e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347444"
---
# <a name="get-started-with-the-speech-recognition-api-in-objective-c-on-ios"></a>Rozpoczynanie pracy z interfejsem API rozpoznawania mowy w języku Objective-C w systemie iOS

Aplikacje systemu iOS, korzystających z usług chmurowych mowy można przekonwertować na tekst rozmowy audio można tworzyć przy użyciu interfejsu API rozpoznawania mowy. Interfejs API obsługuje przesyłania strumieniowego w czasie rzeczywistym, dzięki czemu aplikacja może jednocześnie i asynchronicznie otrzymywać wyniki częściowe rozpoznawania w tym samym czasie, który wysyła do usługi audio.

W tym artykule wykorzystano przykładowej aplikacji, aby zademonstrować podstawy jak rozpocząć pracę z interfejsem API rozpoznawania mowy do opracowywania aplikacji systemu iOS. Pełną dokumentację interfejsu API, zobacz [informacje o bibliotece klienta SDK mowy](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Upewnij się, czy zainstalowano Mac XCode IDE.

### <a name="get-the-client-library-and-examples"></a>Pobierz klienta biblioteki i przykłady

Biblioteka klienta mowy i przykłady dla systemu iOS są dostępne na [mowy klient zestawu SDK dla systemu iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie API rozpoznawania mowy i uzyskiwanie klucza bezpłatnej subskrypcji próbnej

Interfejs API mowy jest częścią usługi kognitywnych (wcześniej Oxford projektu). Możesz uzyskać klucze bezpłatnej subskrypcji próbnej z [subskrypcji usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API mowy wybierz **uzyskać klucz interfejsu API** uzyskać klucza. Zwraca wartość klucza podstawowego i pomocniczego. Obydwu kluczy są powiązane z tego samego przydziału, dzięki czemu można użyć albo klucza.

Jeśli chcesz użyć *rozpoznawania z zamiarem*, należy zarejestrować się w celu [usługi inteligentnego opis języka (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Pobierz klucz subskrypcji. Zanim będzie można użyć bibliotek klienckich mowy, musisz mieć [klucza subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
> * Użyj klucza subskrypcji. Z podanych iOS przykładowej aplikacji należy zaktualizować plik Samples/SpeechRecognitionServerExample/settings.plist kluczem subskrypcji. Aby uzyskać więcej informacji, zobacz [skompilować i uruchomić przykłady](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Użyj biblioteki klienckiej mowy

Aby dodać biblioteki klienta do projektu XCode, wykonaj następujące [instrukcje](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Aby znaleźć klienta odwołanie do biblioteki dla systemu iOS, zobacz [strony sieci Web](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Tworzenie i uruchamianie przykładów

Aby uzyskać informacje na temat sposobu tworzenia i uruchamiania przykładów, zobacz [stronę README](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Przykłady wyjaśniono

### <a name="create-recognition-clients"></a>Utwórz rozpoznawania klientów

Następujący kod w przykładzie przedstawiono sposób tworzenia klasy klienta rozpoznawanie na podstawie scenariuszy użytkowników:

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

Biblioteka klienta udostępnia wstępnie zaimplementowanym rozpoznawania klasy klienta dla typowych scenariuszy rozpoznawanie mowy:

* `DataRecognitionClient`: Rozpoznawanie mowy z danymi PCM (na przykład z pliku lub audio źródła). Danych został podzielony na buforów, a każdy buforu jest wysyłane do usługi mowy. Żadnych modyfikacji odbywa się do buforów, dlatego użytkownicy mogą stosować własne wykrywania wyciszenia, w razie potrzeby. W przypadku danych z plików WAV można wysłać danych po prawej stronie pliku na serwerze. Jeśli masz nieprzetworzone dane, na przykład audio przesyłanych przez sieć Bluetooth, najpierw wysłać nagłówka formatu do serwera, a następnie dane.
* `MicrophoneRecognitionClient`: Rozpoznawanie mowy pochodzące z mikrofon dźwięku. Upewnij się, że mikrofon jest włączony i danych z mikrofon jest wysyłane do usługi rozpoznawania mowy. Wbudowane "wyciszenia detektora" jest stosowany do danych mikrofon przed wysłaniem do usługi rozpoznawania.
* `DataRecognitionClientWithIntent` i `MicrophoneRecognitionClientWithIntent`: oprócz rozpoznawania tekstu, te klienci będą zwracać strukturalnych informacji dotyczących przeznaczenia prelegenta, które aplikacje umożliwiające sterowanie dalsze akcje. Aby użyć "Opcje", musisz najpierw uczenia modelu przy użyciu [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Język rozpoznawania

Jeśli używasz `SpeechRecognitionServiceFactory` można utworzyć klienta, musisz wybrać język. Aby uzyskać pełną listę języków obsługiwanych przez usługę mowy, zobacz [obsługiwanych języków](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Należy również określić `SpeechRecognitionMode` podczas tworzenia klienta z `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Czas w sekundach utterance maksymalnie 15. Dane są wysyłane do usługi, klient otrzyma wyniki częściowe wielu oraz jeden wynik końcowy wiele opcji najlepiej n.
* `SpeechRecognitionMode_LongDictation`: Utterance do dwóch minut long. Dane są wysyłane do usługi, klient otrzyma wielu wyniki częściowe oraz wielu wyników końcowych, oparte na którym serwer identyfikuje pauzy zdanie.

### <a name="attach-event-handlers"></a>Dołącz procedury obsługi zdarzeń

Możesz dołączyć różnych programów obsługi zdarzeń do klienta utworzonego:

* **Częściowe wyniki zdarzenia**: to zdarzenie jest wywoływana za każdym razem, gdy usługi mowy prognozuje, co możesz może być informujący o tym, nawet przed zakończeniem mówiąc (Jeśli używasz `MicrophoneRecognitionClient`) lub zakończenie wysyłania danych (Jeżeli używasz `DataRecognitionClient`).
* **Zdarzenia błędu**: wywoływane, gdy usługa wykryje błąd.
* **Zdarzenia konwersji**: wywoływana na klientach "WithIntent" (tylko w trybie ShortPhrase) po ostatnim rozpoznawania wyników jest analizowana w strukturze zamiar JSON.
* **Powoduje zdarzenia**:
  * W `SpeechRecognitionMode_ShortPhrase` tryb, to zdarzenie jest nazywany i zwraca n najlepsze wyniki, po zakończeniu mówiąc.
  * W `SpeechRecognitionMode_LongDictation` trybie obsługi zdarzeń jest wywołana wiele razy, oparte na którym usługa identyfikuje zdanie pauzy.
  * **Dla każdej opcji najlepiej n**, zwracane są wartości zaufania i kilka różnych metod rozpoznany. Aby uzyskać więcej informacji, zobacz [format danych wyjściowych](../Concepts.md#output-format).

## <a name="related-topics"></a>Powiązane tematy

* [Informacje o bibliotece klienta dla systemu iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Wprowadzenie do rozpoznawania mowy firmy Microsoft i/lub zamiar w języku Java w systemie Android](GetStartedJavaAndroid.md)
* [Wprowadzenie do interfejsu API usługi Microsoft mowy w języku JavaScript](GetStartedJSWebsockets.md)
* [Rozpoczynanie pracy z interfejsem API mowy Microsoft za pośrednictwem interfejsu REST](GetStartedREST.md)
