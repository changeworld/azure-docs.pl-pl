---
title: Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft w języku Java w systemie Android | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Użyj Speech API Microsoft, aby opracowywać aplikacje dla systemu Android, które konwertują dźwięk mówiony na tekst.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 77fee2ecee9cfabe3fad9c1df2c41c7803c3367e
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966828"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Szybki start: Korzystanie z interfejsu API rozpoznawania rozpoznawanie mowy Bing w języku Java w systemie Android

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Za pomocą interfejsu API rozpoznawania rozpoznawanie mowy Bing można opracowywać aplikacje dla systemu Android, które używają usługi rozpoznawanie mowy Bing opartej na chmurze, aby przekonwertować mówiony dźwięk na tekst. Interfejs API obsługuje przesyłanie strumieniowe w czasie rzeczywistym, dzięki czemu aplikacja może jednocześnie i asynchronicznie odbierać częściowe wyniki rozpoznawania w tym samym czasie, wysyłając dźwięk do usługi.

W tym artykule użyto przykładowej aplikacji, aby zademonstrować, jak za pomocą biblioteka klienta usługi Mowa dla systemu Android opracowywać aplikacje mowy w języku Java dla urządzeń z systemem Android.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Przykład jest opracowywany przez [Android Studio](https://developer.android.com/sdk/index.html) dla systemu Windows w języku Java.

### <a name="get-the-client-library-and-sample-application"></a>Pobierz bibliotekę kliencką i przykładową aplikację

Biblioteka klienta usługi Mowa i przykłady dla systemu Android są dostępne w [zestawie SDK klienta mowy dla systemu Android](https://github.com/microsoft/cognitive-speech-stt-android). Możesz znaleźć przykład do skompilowania w katalogu Samples/SpeechRecoExample. Dwie biblioteki potrzebne do użycia we własnych aplikacjach można znaleźć w SpeechSDK/libs w folderze armeabi i x86. Rozmiar pliku libandroid_platform. so to 22 MB, ale jego czas wdrożenia zmniejsza się do 4 MB.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Zasubskrybuj Speech API i uzyskaj bezpłatny klucz subskrypcji wersji próbnej

Speech API jest częścią Cognitive Services (wcześniej Project Oxford). Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać ze strony [subskrypcji Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) . Po wybraniu Speech API wybierz pozycję **Pobierz klucz interfejsu API** , aby uzyskać klucz. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tym samym limitem przydziału, więc można użyć obu kluczy.

Jeśli chcesz użyć *rozpoznawania z zamiarem*, musisz również zarejestrować się w usłudze [Language Understanding Intelligent Service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Pobierz klucz subskrypcji. Aby można było korzystać z bibliotek klienckich mowy, należy dysponować [kluczem subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
>* Użyj klucza subskrypcji. Korzystając z dostarczonej przykładowej aplikacji dla systemu Android, zaktualizuj pliki Samples/SpeechRecoExample/res/Values/String. XML przy użyciu kluczy subskrypcji. Aby uzyskać więcej informacji, zobacz [Kompilowanie i uruchamianie przykładów](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Użyj biblioteka klienta usługi Mowa

Aby użyć biblioteki klienta w aplikacji, postępuj zgodnie z [instrukcjami](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Odwołanie do biblioteki klienckiej dla systemu Android można znaleźć w folderze Docs [zestawu SDK klienta mowy dla systemu Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Kompiluj i uruchamiaj przykłady

Aby dowiedzieć się, jak kompilować i uruchamiać przykłady, zobacz tę [stronę](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Przykłady wyjaśnione

### <a name="create-recognition-clients"></a>Tworzenie klientów rozpoznawania

Kod w poniższym przykładzie pokazuje, jak utworzyć klasy klienta rozpoznawania w oparciu o scenariusze użytkownika:

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

Biblioteka klienta zawiera wstępnie zaimplementowane klasy klienta rozpoznawania dla typowych scenariuszy w funkcji rozpoznawania mowy:

* `DataRecognitionClient`: Rozpoznawanie mowy z danymi PCM (na przykład ze źródła plików lub audio). Dane są podzielone na bufory, a każdy bufor jest wysyłany do usługi mowy. Do buforów nie są wprowadzane żadne modyfikacje, więc użytkownik może zastosować własne wykrywanie wyciszenia w razie potrzeby. Jeśli dane są dostarczane z plików WAV, można wysyłać dane z pliku bezpośrednio do usługi mowy. W przypadku danych pierwotnych, na przykład audio przychodzących za pośrednictwem połączenia Bluetooth, należy najpierw wysłać nagłówek formatu do usługi mowy, a następnie dane.
* `MicrophoneRecognitionClient`: Rozpoznawanie mowy z dźwiękiem pochodzącym z mikrofonu. Upewnij się, że mikrofon jest włączony, a dane z mikrofonu są wysyłane do usługi rozpoznawania mowy. Wbudowany "detektor" jest stosowany do danych mikrofonu przed jego wysłaniem do usługi rozpoznawania.
* `DataRecognitionClientWithIntent` i `MicrophoneRecognitionClientWithIntent`: Ci klienci zwracają, oprócz tekstu rozpoznawania, strukturalne informacje o zamiarze prelegenta, które mogą służyć do kierowania dalszych akcji przez aplikacje. Aby użyć "intencji", musisz najpierw nauczyć model przy użyciu [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Język rozpoznawania

W przypadku korzystania `SpeechRecognitionServiceFactory` z programu do tworzenia klienta należy wybrać język. Aby uzyskać pełną listę języków obsługiwanych przez usługę mowy, zobacz [obsługiwane języki](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Należy również określić `SpeechRecognitionMode` , kiedy tworzysz klienta przy użyciu `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: Wypowiedź do 15 sekund. Gdy dane są wysyłane do usługi, klient otrzymuje wiele wyników częściowych i jeden wynik końcowy z wieloma najlepszymi opcjami.
* `LongDictation`: Wypowiedź maksymalnie dwie minuty. Gdy dane są wysyłane do usługi, klient otrzymuje wiele wyników częściowych i wiele ostatecznych wyników, w zależności od tego, gdzie usługa identyfikuje pauzy.

### <a name="attach-event-handlers"></a>Dołączanie programów obsługi zdarzeń

Można dołączyć różne programy obsługi zdarzeń do utworzonego klienta:

* **Zdarzenia częściowe wyniki**: To zdarzenie jest wywoływane za każdym razem, gdy usługa mowy przeanalizuje to, co może się powiedzieć, nawet przed zakończeniem `MicrophoneRecognitionClient`rozmowy (Jeśli używasz) lub zakończyć wysyłanie danych `DataRecognitionClient`(Jeśli używasz).
* **Zdarzenia błędów**: Wywoływana, gdy usługa wykrywa błąd.
* **Zdarzenia dotyczące intencji**: Wywoływana na klientach "WithIntent" (tylko `ShortPhrase` w trybie) po ostatecznym wyniku rozpoznawania zostanie przeanalizowany do zamiaru strukturalnego JSON.
* **Zdarzenia wynikowe**:
  * W `ShortPhrase` trybie, to zdarzenie jest wywoływane i zwraca n najlepszych wyników po zakończeniu mówienia.
  * W `LongDictation` trybie, program obsługi zdarzeń jest wywoływany wiele razy, w zależności od tego, gdzie usługa identyfikuje zdanie pauzy.
  * **Dla każdej z najlepszych opcji**, są zwracane wartości zaufania i kilka różnych form rozpoznanego tekstu. Aby uzyskać więcej informacji, zobacz [Format danych wyjściowych](../Concepts.md#output-format).

## <a name="related-topics"></a>Tematy pokrewne

* [Dokumentacja biblioteki klienta dla systemu Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Wprowadzenie do Speech API firmy Microsoft w C# systemie Windows w programie .NET](GetStartedCSharpDesktop.md)
* [Wprowadzenie do Speech API firmy Microsoft w celu języka C w systemie iOS](Get-Started-ObjectiveC-iOS.md)
* [Wprowadzenie do Speech API firmy Microsoft w języku JavaScript](GetStartedJSWebsockets.md)
* [Wprowadzenie do Speech API firmy Microsoft za pośrednictwem REST](GetStartedREST.md)
