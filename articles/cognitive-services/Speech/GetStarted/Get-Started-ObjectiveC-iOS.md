---
title: Wprowadzenie do interfejsu API rozpoznawania mowy Bing w języku Objective C w systemie iOS | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Tworzenie aplikacji dla systemu iOS, które Konwertuj dźwięk mówiony na tekst, należy użyć interfejsu API rozpoznawania mowy Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 09b7e8961e59bd6fad49408c28e9ee9a4a209cae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515293"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Szybki start: Użyj interfejsu API rozpoznawania mowy Bing w języku Objective C w systemie iOS

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Za pomocą interfejsu API rozpoznawania mowy można opracować aplikacje dla systemu iOS, które umożliwia konwertowanie dźwięku mówionego na tekst oparte na chmurze usługa rozpoznawania mowy. Interfejs API obsługuje przesyłanie strumieniowe w czasie rzeczywistym, dzięki czemu można równocześnie i asynchronicznie otrzymywać częściowe wyniki rozpoznawania na tym samym czasie, który wysyła audio do usługi aplikacji.

W tym artykule używa przykładowej aplikacji, aby zademonstrować podstawy jak rozpocząć pracę z interfejsem API rozpoznawania mowy do opracowywania aplikacji systemu iOS. Aby uzyskać pełną dokumentację interfejsu API, zobacz [odwołanie do biblioteki klienta mowy SDK](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Upewnij się, że środowisko IDE programu XCode Mac jest zainstalowany.

### <a name="get-the-client-library-and-examples"></a>Pobieranie klienta, biblioteki i przykłady

Biblioteki klienta mowy i przykłady dla systemu iOS są dostępne w [mowy zestawu SDK klienta dla systemu iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie do interfejsu API rozpoznawania mowy i Uzyskaj klucz subskrypcji wersji próbnej

Interfejs API mowy jest częścią usług Cognitive Services (wcześniej Project Oxford). Możesz uzyskać bezpłatną subskrypcję próbną kluczy z [subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API rozpoznawania mowy, wybierz opcję **Uzyskaj klucz interfejsu API** można pobrać klucza. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tego samego limitu przydziału, aby można było używać żadnego z nich.

Jeśli chcesz używać *uznanie z zamiarem*, należy również zarejestrować [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Pobierz klucz subskrypcji. Korzystanie z biblioteki klienta mowy, konieczne jest posiadanie [klucz subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
> * Użyj klucza subskrypcji. Przy użyciu podanych dla systemu iOS przykładowej aplikacji musisz zaktualizować plik Samples/SpeechRecognitionServerExample/settings.plist z kluczem subskrypcji. Aby uzyskać więcej informacji, zobacz [kompilowanie i uruchamianie przykładów](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Korzystanie z biblioteki klienta mowy

Aby dodać biblioteki klienckiej w projekcie XCode, postępuj zgodnie z tymi [instrukcje](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Aby znaleźć klienta odwołanie do biblioteki dla systemu iOS, zobacz [strony sieci Web](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Tworzenie i uruchamianie przykładów

Aby uzyskać informacje na temat sposobu tworzenia i uruchamiania przykładów, zobacz ten [stronę README](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Przykłady wyjaśniono

### <a name="create-recognition-clients"></a>Tworzenie klientów do rozpoznawania

Poniższy kod w przykładzie przedstawiono sposób tworzenia klasy klienta rozpoznawanie na podstawie scenariuszy użytkowników:

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

Biblioteka klienta zapewnia wstępnie zaimplementowano rozpoznawania klasy klienta dla typowych scenariuszy rozpoznawanie mowy:

* `DataRecognitionClient`: Rozpoznawanie mowy z danymi PCM (na przykład z pliku lub nagrania dźwiękowego źródła). Dane są dzielone na buforów, a każdy bufor są wysyłane do usługi rozpoznawania mowy. Brak możliwości modyfikacji odbywa się do buforów, dzięki czemu użytkownicy mogą stosować własne wykrywania wyciszenia, w razie potrzeby. Jeśli nie podano danych z plików WAV, umożliwia wysyłanie danych z prawej strony plików do serwera. Jeśli masz dane pierwotne, na przykład, audio, dostępne za pośrednictwem połączenia Bluetooth, najpierw wysyłasz nagłówka formatu do serwera, a następnie dane.
* `MicrophoneRecognitionClient`: Rozpoznawanie mowy z dźwięku z mikrofonu. Upewnij się, że mikrofon jest włączony i danych z mikrofonu są wysyłane do usługi rozpoznawania mowy. Wbudowane "wyciszenia detektora" są stosowane do danych mikrofonu, przed wysłaniem ich do usługi rozpoznawania.
* `DataRecognitionClientWithIntent` i `MicrophoneRecognitionClientWithIntent`: Oprócz rozpoznawanie tekstu Ci klienci zwrócone ze strukturą informacje na temat zamiar osoby mówiącej, Twoje aplikacje mogą używać do dysku dalsze akcje. Aby użyć "Opcje", musisz najpierw wytrenuj model przy użyciu [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Rozpoznawania języka

Kiedy używasz `SpeechRecognitionServiceFactory` tworzenia klienta, musisz wybrać język. Aby uzyskać pełną listę języków obsługiwanych przez usługę rozpoznawania mowy, zobacz [obsługiwane języki](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Należy także określić `SpeechRecognitionMode` po utworzeniu klienta z `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Długości wypowiedź maksymalnie 15 sekund. Ponieważ dane są wysyłane do usługi, klient odbierze wiele wyników częściowych i jeden wynik końcowy z wieloma opcjami n najlepszych.
* `SpeechRecognitionMode_LongDictation`: Wypowiedź maksymalnie dwóch minut long. Ponieważ dane są wysyłane do usługi, klient odbierze wiele wyników częściowych i wiele wyników końcowych, w którym serwer identyfikuje wstrzymuje zdanie w oparciu.

### <a name="attach-event-handlers"></a>Dołącz procedury obsługi zdarzeń

Różne programy obsługi zdarzeń można dołączyć do klienta został utworzony:

* **Częściowe wyniki zdarzeń**: To zdarzenie jest wywoływane za każdym razem, gdy usługi mowy przewiduje, użytkownik może być opinie, nawet zakończeniem wypowiedzi (Jeśli używasz `MicrophoneRecognitionClient`) lub zakończenie wysyłania danych (Jeśli używasz `DataRecognitionClient`).
* **Zdarzenia błędu**: Wywołuje się, gdy usługa wykrywa błąd.
* **Zdarzenia intencji**: Wywoływana na klientach "WithIntent" (tylko w trybie ShortPhrase) po ostatnim rozpoznawania wynik jest przekształcany do ze strukturą intencji JSON.
* **Wynik zdarzenia**:
  * W `SpeechRecognitionMode_ShortPhrase` tryb, to zdarzenie jest wywoływana i zwraca wyniki n najlepszych, po zakończeniu mówić.
  * W `SpeechRecognitionMode_LongDictation` tryb, program obsługi zdarzeń jest wywoływana wiele razy, oparte na którym usługa identyfikuje wstrzymuje zdania.
  * **Dla każdej opcji n najlepszych**, zwracane są wartości zaufania i kilka różnych sposobów rozpoznany tekst. Aby uzyskać więcej informacji, zobacz [format danych wyjściowych](../Concepts.md#output-format).

## <a name="related-topics"></a>Powiązane tematy

* [Dokumentacja biblioteki klienta dla systemu iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Rozpoczynanie pracy z usługą rozpoznawania mowy firmy Microsoft i/lub przeznaczenie w języku Java w systemie Android](GetStartedJavaAndroid.md)
* [Rozpoczynanie pracy z usługą Microsoft Speech API w języku JavaScript](GetStartedJSWebsockets.md)
* [Wprowadzenie do interfejsu API mowy usługi Microsoft za pośrednictwem interfejsu REST](GetStartedREST.md)
