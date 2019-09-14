---
title: Wprowadzenie do interfejsu API rozpoznawania rozpoznawanie mowy Bing w celu języka C w systemie iOS | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Użyj interfejsu API rozpoznawania rozpoznawanie mowy Bing do tworzenia aplikacji dla systemu iOS, które konwertują dźwięk mówiony na tekst.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e42784e6d2751f7e76aec8caf1d6e1f9f09a9fd1
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965936"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Szybki start: Korzystanie z interfejsu API rozpoznawania rozpoznawanie mowy Bing w celu języka C w systemie iOS

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Za pomocą interfejsu API rozpoznawania mowy można opracowywać aplikacje dla systemu iOS, które używają usługi rozpoznawania mowy opartej na chmurze, aby przekonwertować dźwięk mówiony na tekst. Interfejs API obsługuje przesyłanie strumieniowe w czasie rzeczywistym, dzięki czemu aplikacja może jednocześnie i asynchronicznie odbierać częściowe wyniki rozpoznawania w tym samym czasie, wysyłając dźwięk do usługi.

W tym artykule przedstawiono przykładową aplikację, która zawiera podstawowe informacje na temat rozpoczynania pracy z interfejsem API rozpoznawania mowy do tworzenia aplikacji dla systemu iOS. Aby uzyskać pełne informacje o interfejsie API, zobacz [Dokumentacja biblioteki klienta zestawu Speech SDK](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Upewnij się, że jest zainstalowany XCode IDE Mac.

### <a name="get-the-client-library-and-examples"></a>Pobierz bibliotekę i przykłady klienta

Biblioteka klienta usługi Mowa i przykłady dla systemu iOS są dostępne w [zestawie SDK klienta mowy dla systemu iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie interfejsu API rozpoznawania mowy i pobieranie klucza subskrypcji bezpłatnej wersji próbnej

Speech API jest częścią Cognitive Services (wcześniej Project Oxford). Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać ze strony [subskrypcji Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) . Po wybraniu Speech API wybierz pozycję **Pobierz klucz interfejsu API** , aby uzyskać klucz. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tym samym limitem przydziału, więc można użyć obu kluczy.

Jeśli chcesz użyć *rozpoznawania z zamiarem*, musisz również zarejestrować się w usłudze [Language Understanding Intelligent Service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Pobierz klucz subskrypcji. Aby można było korzystać z bibliotek klienckich mowy, należy dysponować [kluczem subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
> * Użyj klucza subskrypcji. W przypadku podanej przykładowej aplikacji dla systemu iOS należy zaktualizować plik Samples/SpeechRecognitionServerExample/Settings. plist przy użyciu klucza subskrypcji. Aby uzyskać więcej informacji, zobacz [Kompilowanie i uruchamianie przykładów](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Użyj biblioteka klienta usługi Mowa

Aby dodać bibliotekę kliencką do projektu XCode, postępuj zgodnie z tymi [instrukcjami](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Aby znaleźć odwołanie do biblioteki klienta dla systemu iOS, zobacz tę [stronę sieci Web](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Kompiluj i uruchamiaj przykłady

Aby uzyskać informacje na temat tworzenia i uruchamiania przykładów, zobacz tę [stronę Readme](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Przykłady wyjaśnione

### <a name="create-recognition-clients"></a>Tworzenie klientów rozpoznawania

Poniższy kod w przykładzie pokazuje, jak utworzyć klasy klienta rozpoznawania w oparciu o scenariusze użytkownika:

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

Biblioteka klienta zawiera wstępnie zaimplementowane klasy klienta rozpoznawania dla typowych scenariuszy w funkcji rozpoznawania mowy:

* `DataRecognitionClient`: Rozpoznawanie mowy z danymi PCM (na przykład ze źródła plików lub audio). Dane są podzielone na bufory, a każdy bufor jest wysyłany do usługi mowy. Do buforów nie są wprowadzane żadne modyfikacje, dlatego użytkownicy mogą zastosować własne wykrywanie cisza, jeśli to konieczne. Jeśli dane są dostarczane z plików WAV, można wysłać dane bezpośrednio z pliku do serwera programu. W przypadku danych pierwotnych, na przykład audio przychodzących za pośrednictwem połączenia Bluetooth, należy najpierw wysłać do serwera nagłówek formatu, a następnie dane.
* `MicrophoneRecognitionClient`: Rozpoznawanie mowy z dźwiękiem pochodzącym z mikrofonu. Upewnij się, że mikrofon jest włączony i że dane z mikrofonu są wysyłane do usługi rozpoznawania mowy. Wbudowany "detektor" jest stosowany do danych mikrofonu przed jego wysłaniem do usługi rozpoznawania.
* `DataRecognitionClientWithIntent` i `MicrophoneRecognitionClientWithIntent`: Oprócz tekstu rozpoznawania te Klienci zwracają strukturalne informacje o zamierzeniu prelegenta, którego aplikacje mogą używać do kierowania dalszych akcji. Aby użyć "intencji", musisz najpierw nauczyć model przy użyciu [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Język rozpoznawania

W przypadku korzystania `SpeechRecognitionServiceFactory` z programu do tworzenia klienta należy wybrać język. Aby uzyskać pełną listę języków obsługiwanych przez usługę mowy, zobacz [obsługiwane języki](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Należy również określić `SpeechRecognitionMode` , kiedy tworzysz klienta przy użyciu `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Wypowiedź do 15 sekund. Gdy dane są wysyłane do usługi, klient otrzymuje wiele wyników częściowych i jeden wynik końcowy z wieloma najlepszymi opcjami.
* `SpeechRecognitionMode_LongDictation`: Wypowiedź maksymalnie dwie minuty. Gdy dane są wysyłane do usługi, klient otrzymuje wiele wyników częściowych i wiele ostatecznych wyników, w zależności od tego, gdzie serwer identyfikuje pauzy.

### <a name="attach-event-handlers"></a>Dołączanie programów obsługi zdarzeń

Można dołączyć różne programy obsługi zdarzeń do utworzonego klienta:

* **Zdarzenia częściowe wyniki**: To zdarzenie jest wywoływane za każdym razem, gdy usługa mowy przewidywalna to, co może się powiedzieć, nawet przed zakończeniem rozmowy `MicrophoneRecognitionClient`(Jeśli używasz) lub Zakończ wysyłanie danych ( `DataRecognitionClient`Jeśli używasz).
* **Zdarzenia błędów**: Wywoływana, gdy usługa wykrywa błąd.
* **Zdarzenia dotyczące intencji**: Wywoływana na klientach "WithIntent" (tylko w trybie ShortPhrase) po zakończeniu ostatniego wyniku rozpoznawania zostanie przeanalizowany do zamiaru strukturalnego JSON.
* **Zdarzenia wynikowe**:
  * W `SpeechRecognitionMode_ShortPhrase` trybie, to zdarzenie jest wywoływane i zwraca n najlepszych wyników po zakończeniu mówienia.
  * W `SpeechRecognitionMode_LongDictation` trybie, program obsługi zdarzeń jest wywoływany wiele razy, w zależności od tego, gdzie usługa identyfikuje zdanie pauzy.
  * **Dla każdej z najlepszych opcji**, są zwracane wartości zaufania i kilka różnych form rozpoznanego tekstu. Aby uzyskać więcej informacji, zobacz [Format danych wyjściowych](../Concepts.md#output-format).

## <a name="related-topics"></a>Tematy pokrewne

* [Dokumentacja biblioteki klienta dla systemu iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Wprowadzenie do rozpoznawania i/lub zamiaru mowy firmy Microsoft w języku Java w systemie Android](GetStartedJavaAndroid.md)
* [Wprowadzenie do Speech API firmy Microsoft w języku JavaScript](GetStartedJSWebsockets.md)
* [Wprowadzenie do Speech API firmy Microsoft za pośrednictwem REST](GetStartedREST.md)
