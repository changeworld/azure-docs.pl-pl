---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft w języku Java w systemie Android | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji systemu Android, które przekonwertowana na tekst audio rozmowy za pomocą interfejsu API mowy firmy Microsoft.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: a10f7be1c36fb431016a9867f606e26be858069e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347449"
---
# <a name="get-started-with-speech-recognition-in-java-on-android"></a>Wprowadzenie do rozpoznawania mowy w języku Java w systemie Android

Przy użyciu interfejsu API rozpoznawania mowy można programować aplikacje systemu Android korzystających z usług chmurowych mowy można przekonwertować na tekst rozmowy audio. Interfejs API obsługuje przesyłania strumieniowego w czasie rzeczywistym, dzięki czemu aplikacja może jednocześnie i asynchronicznie otrzymywać wyniki częściowe rozpoznawania w tym samym czasie, który wysyła do usługi audio.

W tym artykule używa przykładowej aplikacji do pokazują, jak przy użyciu biblioteki mowy klienta dla systemu Android do tworzenia aplikacji mowy na tekst w języku Java dla urządzeń z systemem Android.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Przykład opracowanego przez [Android Studio](http://developer.android.com/sdk/index.html) dla systemu Windows w języku Java.

### <a name="get-the-client-library-and-sample-application"></a>Pobierz klienta biblioteki i przykładowa aplikacja

Biblioteka klienta mowy i próbki dla systemu Android są dostępne w [mowy klienta zestawu SDK dla systemu Android](https://github.com/microsoft/cognitive-speech-stt-android). Próbka była kompilowana w katalogu przykładów/SpeechRecoExample można znaleźć. Dwie biblioteki należy użyć w aplikacjach w SpeechSDK/biblioteki w obszarze armeabi i x86 można znaleźć folderu. Rozmiar pliku libandroid_platform.so jest 22 MB, ale zostanie zmniejszona do 4 MB, w czasie wdrażania.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie API mowy i uzyskiwanie klucza bezpłatnej subskrypcji próbnej

Interfejs API mowy jest częścią usługi kognitywnych (wcześniej Oxford projektu). Możesz uzyskać klucze bezpłatnej subskrypcji próbnej z [subskrypcji usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API mowy wybierz **uzyskać klucz interfejsu API** uzyskać klucza. Zwraca wartość klucza podstawowego i pomocniczego. Obydwu kluczy są powiązane z tego samego przydziału, dzięki czemu można użyć albo klucza.

Jeśli chcesz użyć *rozpoznawania z zamiarem*, należy zarejestrować się w celu [usługi inteligentnego opis języka (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Pobierz klucz subskrypcji. Zanim będzie można użyć bibliotek klienckich mowy, musisz mieć [klucza subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
>* Użyj klucza subskrypcji. Podana dla systemu Android przykładowej aplikacji aktualizacji samples/SpeechRecoExample/res/values/strings.xml pliku z kluczami Twojej subskrypcji. Aby uzyskać więcej informacji, zobacz [skompilować i uruchomić przykłady](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Użyj biblioteki klienckiej mowy

Aby użyć biblioteki klienta w aplikacji, wykonaj [instrukcje](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Znajduje się klient odwołanie do biblioteki dla systemu Android w folderze dokumenty [mowy klienta zestawu SDK dla systemu Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Tworzenie i uruchamianie przykładów

Aby dowiedzieć się, jak skompilować i uruchomić przykłady, zobacz [stronę README](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Przykłady wyjaśniono

### <a name="create-recognition-clients"></a>Utwórz rozpoznawania klientów

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

Biblioteka klienta udostępnia wstępnie zaimplementowanym rozpoznawania klasy klienta dla typowych scenariuszy rozpoznawanie mowy:

* `DataRecognitionClient`: Rozpoznawanie mowy z danymi PCM (na przykład z pliku lub audio źródła). Danych został podzielony na buforów, a każdy buforu jest wysyłane do usługi mowy. Modyfikacja do buforów, odbywa się tak, aby użytkownik może stosować własne wykrywania wyciszenia, w razie potrzeby. W przypadku danych z plików WAV możesz wysłać dane od prawej pliku do usługi mowy. Jeśli masz nieprzetworzone dane, na przykład audio przesyłanych przez sieć Bluetooth, należy najpierw wysłać nagłówka formatu do mowy usługi następuje danych.
* `MicrophoneRecognitionClient`: Rozpoznawanie mowy pochodzące z mikrofon dźwięku. Upewnij się, mikrofon jest włączony i dane z mikrofon jest wysyłane do usługi rozpoznawania mowy. Wbudowane "wyciszenia detektora" jest stosowany do danych mikrofon przed wysłaniem do usługi rozpoznawania.
* `DataRecognitionClientWithIntent` i `MicrophoneRecognitionClientWithIntent`: zwraca tych klientów, oprócz tekstu rozpoznawania strukturę informacji dotyczących przeznaczenia prelegenta, która może służyć do kierowania dalszych działań przez aplikacje. Aby użyć "Opcje", musisz najpierw uczenia modelu przy użyciu [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Język rozpoznawania

Jeśli używasz `SpeechRecognitionServiceFactory` można utworzyć klienta, musisz wybrać język. Aby uzyskać pełną listę języków obsługiwanych przez usługę mowy, zobacz [obsługiwanych języków](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Należy również określić `SpeechRecognitionMode` podczas tworzenia klienta z `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: Czas w sekundach utterance maksymalnie 15. Dane są wysyłane do usługi, klient otrzyma wyniki częściowe wielu oraz jeden wynik końcowy wiele opcji najlepiej n.
* `LongDictation`: Utterance do dwóch minut long. Dane są wysyłane do usługi, klient otrzyma wielu wyniki częściowe oraz wielu wyników końcowych, oparte na którym usługa identyfikuje pauzy zdanie.

### <a name="attach-event-handlers"></a>Dołącz procedury obsługi zdarzeń

Możesz dołączyć różnych programów obsługi zdarzeń do klienta utworzonego:

* **Częściowe wyniki zdarzenia**: to zdarzenie jest wywoływana za każdym razem, gdy usługi mowy prognozuje, co możesz może być informujący o tym, nawet przed zakończeniem mówiąc (Jeśli używasz `MicrophoneRecognitionClient`) lub zakończenie wysyłania danych (Jeżeli używasz `DataRecognitionClient`).
* **Zdarzenia błędu**: wywoływane, gdy usługa wykryje błąd.
* **Zdarzenia konwersji**: Wywołano "WithIntent" klientów (tylko w `ShortPhrase` tryb) po wynik końcowy rozpoznawania jest analizowana w strukturze zamiar JSON.
* **Powoduje zdarzenia**:
  * W `ShortPhrase` tryb, to zdarzenie jest nazywany i zwraca n najlepsze wyniki, po zakończeniu mówiąc.
  * W `LongDictation` trybie obsługi zdarzeń jest wywołana wiele razy, oparte na którym usługa identyfikuje zdanie pauzy.
  * **Dla każdej opcji najlepiej n**, zwracane są wartości zaufania i kilka różnych metod rozpoznany. Aby uzyskać więcej informacji, zobacz [format danych wyjściowych](../Concepts.md#output-format).

## <a name="related-topics"></a>Powiązane tematy

* [Informacje o bibliotece klienta dla systemu Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Rozpoczynanie pracy z interfejsem API mowy firmy Microsoft w języku C# dla systemu Windows w środowisku .NET](GetStartedCSharpDesktop.md)
* [Rozpoczynanie pracy z interfejsem API mowy firmy Microsoft w języku Objective-C w systemie iOS](Get-Started-ObjectiveC-iOS.md)
* [Wprowadzenie do interfejsu API usługi Microsoft mowy w języku JavaScript](GetStartedJSWebsockets.md)
* [Rozpoczynanie pracy z interfejsem API mowy Microsoft za pośrednictwem interfejsu REST](GetStartedREST.md)
