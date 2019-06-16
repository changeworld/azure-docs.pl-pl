---
title: Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft w języku Java w systemie Android | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Opracowywanie aplikacji dla systemu Android, które Konwertuj dźwięk mówiony na tekst za pomocą interfejsu API mowy firmy Microsoft.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 147042e300e629dd7e354d4e9079cc4855a8146c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515194"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Szybki start: Używanie rozpoznawania mowy Bing interfejsu API w języku Java w systemie Android

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Za pomocą interfejsu API rozpoznawania mowy Bing można opracować aplikacje dla systemu Android, które umożliwia konwertowanie dźwięku mówionego na tekst oparte na chmurze usługa rozpoznawania mowy Bing. Interfejs API obsługuje przesyłanie strumieniowe w czasie rzeczywistym, dzięki czemu można równocześnie i asynchronicznie otrzymywać częściowe wyniki rozpoznawania na tym samym czasie, który wysyła audio do usługi aplikacji.

Tym artykule użyto przykładowej aplikacji do pokazują, jak tworzyć aplikacje mowy na tekst w języku Java dla urządzeń z systemem Android za pomocą biblioteki klienta mowy, dla systemu Android.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Próbka jest opracowany przez [Android Studio](https://developer.android.com/sdk/index.html) for Windows w języku Java.

### <a name="get-the-client-library-and-sample-application"></a>Pobieranie klienta biblioteki i przykładowej aplikacji

Biblioteki klienta mowy i przykłady dla systemu Android są dostępne w [mowy zestaw SDK klienta dla systemu Android](https://github.com/microsoft/cognitive-speech-stt-android). Można znaleźć przykład możliwej do skompilowania samples/SpeechRecoExample w katalogu. Dwie biblioteki należy używać w aplikacjach w SpeechSDK i bibliotek, w obszarze armeabi i x86 można znaleźć folderu. Rozmiar pliku libandroid_platform.so jest 22 MB, ale jest on skracany do 4 MB w czasie wdrażania.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie do interfejsu API mowy i Uzyskaj klucz subskrypcji wersji próbnej

Interfejs API mowy jest częścią usług Cognitive Services (wcześniej Project Oxford). Możesz uzyskać bezpłatną subskrypcję próbną kluczy z [subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API rozpoznawania mowy, wybierz opcję **Uzyskaj klucz interfejsu API** można pobrać klucza. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tego samego limitu przydziału, aby można było używać żadnego z nich.

Jeśli chcesz używać *uznanie z zamiarem*, należy również zarejestrować [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Pobierz klucz subskrypcji. Korzystanie z biblioteki klienta mowy, konieczne jest posiadanie [klucz subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
>* Użyj klucza subskrypcji. Podana dla systemu Android przykładowej aplikacji należy zaktualizować samples/SpeechRecoExample/res/values/strings.xml pliku przy użyciu klucze subskrypcji. Aby uzyskać więcej informacji, zobacz [kompilowanie i uruchamianie przykładów](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Korzystanie z biblioteki klienta mowy

Aby korzystać z biblioteki klienta w aplikacji, wykonaj [instrukcje](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Można znaleźć klienta odwołanie do biblioteki dla systemu Android w folderze dokumenty [mowy zestaw SDK klienta dla systemu Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Tworzenie i uruchamianie przykładów

Aby dowiedzieć się, jak utworzyć i uruchamianie przykładów, zobacz ten [stronę README](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Przykłady wyjaśniono

### <a name="create-recognition-clients"></a>Tworzenie klientów do rozpoznawania

Kod w poniższym przykładzie przedstawiono sposób tworzenia klasy klienta rozpoznawanie na podstawie scenariuszy użytkowników:

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

Biblioteka klienta zapewnia wstępnie zaimplementowano rozpoznawania klasy klienta dla typowych scenariuszy rozpoznawanie mowy:

* `DataRecognitionClient`: Rozpoznawanie mowy z danymi PCM (na przykład z pliku lub nagrania dźwiękowego źródła). Dane są dzielone na buforów, a każdy bufor są wysyłane do usługi rozpoznawania mowy. Brak możliwości modyfikacji odbywa się do buforów, dzięki czemu użytkownik można zastosować własne wykrywania wyciszenia, w razie potrzeby. Jeśli nie podano danych z plików WAV, umożliwia wysyłanie danych z prawej strony plików do usługi rozpoznawania mowy. Jeśli masz dane pierwotne, na przykład, audio, dostępne za pośrednictwem połączenia Bluetooth, najpierw wyślesz nagłówka formatu do usługi rozpoznawania mowy, oraz dane.
* `MicrophoneRecognitionClient`: Rozpoznawanie mowy z dźwięku z mikrofonu. Upewnij się, mikrofon jest włączony i dane z mikrofonu są wysyłane do usługi rozpoznawania mowy. Wbudowane "wyciszenia detektora" są stosowane do danych mikrofonu, przed wysłaniem ich do usługi rozpoznawania.
* `DataRecognitionClientWithIntent` i `MicrophoneRecognitionClientWithIntent`: Ci klienci Zwróć oprócz rozpoznawanie tekstu, ze strukturą informacje na temat zamiar osoby mówiącej, która może służyć do kierowania dalsze akcje przy użyciu aplikacji. Aby użyć "Opcje", musisz najpierw wytrenuj model przy użyciu [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Rozpoznawania języka

Kiedy używasz `SpeechRecognitionServiceFactory` tworzenia klienta, musisz wybrać język. Aby uzyskać pełną listę języków obsługiwanych przez usługę rozpoznawania mowy, zobacz [obsługiwane języki](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Należy także określić `SpeechRecognitionMode` po utworzeniu klienta z `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: Długości wypowiedź maksymalnie 15 sekund. Ponieważ dane są wysyłane do usługi, klient odbierze wiele wyników częściowych i jeden wynik końcowy z wieloma opcjami n najlepszych.
* `LongDictation`: Wypowiedź maksymalnie dwóch minut long. Ponieważ dane są wysyłane do usługi, klient odbierze wiele wyników częściowych i wiele wyników końcowych, w którym usługa identyfikuje wstrzymuje zdanie w oparciu.

### <a name="attach-event-handlers"></a>Dołącz procedury obsługi zdarzeń

Różne programy obsługi zdarzeń można dołączyć do klienta został utworzony:

* **Częściowe wyniki zdarzeń**: To zdarzenie jest wywoływane za każdym razem, gdy usługa rozpoznawania mowy przewiduje, użytkownik może być opinie, nawet zakończeniem wypowiedzi (Jeśli używasz `MicrophoneRecognitionClient`) lub zakończenie wysyłania danych (Jeśli używasz `DataRecognitionClient`).
* **Zdarzenia błędu**: Wywołuje się, gdy usługa wykrywa błąd.
* **Zdarzenia intencji**: Wywoływane na klientach "WithIntent" (tylko w `ShortPhrase` tryb) po wynik końcowy rozpoznawania jest przekształcany do ze strukturą intencji JSON.
* **Wynik zdarzenia**:
  * W `ShortPhrase` tryb, to zdarzenie jest wywoływana i zwraca wyniki n najlepszych, po zakończeniu mówić.
  * W `LongDictation` tryb, program obsługi zdarzeń jest wywoływana wiele razy, oparte na którym usługa identyfikuje wstrzymuje zdania.
  * **Dla każdej opcji n najlepszych**, zwracane są wartości zaufania i kilka różnych sposobów rozpoznany tekst. Aby uzyskać więcej informacji, zobacz [format danych wyjściowych](../Concepts.md#output-format).

## <a name="related-topics"></a>Tematy pokrewne

* [Dokumentacja biblioteki klienta dla systemu Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Rozpoczynanie pracy z usługą Microsoft Speech API w języku C# dla Windows na platformie .NET](GetStartedCSharpDesktop.md)
* [Rozpoczynanie pracy z usługą Microsoft Speech API w języku Objective C w systemie iOS](Get-Started-ObjectiveC-iOS.md)
* [Rozpoczynanie pracy z usługą Microsoft Speech API w języku JavaScript](GetStartedJSWebsockets.md)
* [Wprowadzenie do interfejsu API mowy usługi Microsoft za pośrednictwem interfejsu REST](GetStartedREST.md)
