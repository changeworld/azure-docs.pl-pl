---
title: Informacje o wersji — usługa mowy
titleSuffix: Azure Cognitive Services
description: Uruchomiony dziennik wersji funkcji usługi mowy, ulepszeń, poprawek błędów i znanych problemów.
services: cognitive-services
author: brianem
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: cbe9c7cbd0f402e38d1163050d77b055f89948ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219644"
---
# <a name="release-notes"></a>Informacje o wersji
## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: wydanie 2020-luty

**Nowe funkcje**
 - Dodano pakiety Pythona do obsługi nowej wersji 3.8 Pythona.
 - Obsługa systemu Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 (C++, C#, Java, Python).
   > [!NOTE] 
   > Klienci muszą skonfigurować OpenSSL zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
 - Linux ARM32 obsługuje Debiana i Ubuntu.
 - DialogServiceConnector obsługuje teraz opcjonalny parametr "bot ID" na BotFrameworkConfig. Ten parametr umożliwia korzystanie z wielu botów direct line speech z jednym zasobem mowy platformy Azure. Bez określonego parametru zostanie użyty domyślny bot (określony przez stronę konfiguracji kanału direct line speech).
 - DialogServiceConnector ma teraz właściwość SpeechActivityTemplate. Zawartość tego ciągu JSON będzie używana przez direct line speech do wstępnego wypełniania szerokiej gamy obsługiwanych pól we wszystkich działaniach, które osiągają bota direct line speech, w tym działania generowane automatycznie w odpowiedzi na zdarzenia, takie jak rozpoznawanie mowy.
 - TTS teraz używa klucza subskrypcji do uwierzytelniania, zmniejszając opóźnienie pierwszego bajtów pierwszego wyniku syntezy po utworzeniu syntezatora.
 - Zaktualizowano modele rozpoznawania mowy dla 19 ustawień regionalnych dla średniego poziomu błędu słowa o 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). Nowe modele wprowadzają znaczące ulepszenia w wielu domenach, w tym dyktowanie, transkrypcję centrum obsługi telefonicznej i indeksowanie wideo scenariuszy.

**Poprawki błędów**
 - Naprawiono błąd, który powodował, że transcriber konwersacji nie czekał poprawnie w interfejsach API java 
 - Android x86 emulator naprawić problem Xamarin [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Dodaj brak (Pobierz| Set)Metody właściwości audioconfig
 - Napraw błąd TTS, w którym nie można zatrzymać audioDataStream, gdy połączenie nie powiedzie się
 - Używanie punktu końcowego bez regionu spowodowałoby błędy USP dla translatora konwersacji
 - Generowanie identyfikatorów w uniwersalnych aplikacjach systemu Windows używa teraz odpowiednio unikatowego algorytmu GUID; wcześniej i nieumyślnie domyślnie do implementacji stubbed, które często produkowane kolizji przez duże zestawy interakcji.
 
 **Próbki**
 - Przykład Unity do korzystania z zestawu SDK mowy z [mikrofonem Unity i strumieniowaniem w trybie push](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Inne zmiany**
 - [Zaktualizowano dokumentację konfiguracją OpenSSL dla systemu Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: wydanie 2020-styczeń

**Nowe funkcje**

- Konwersacja na wielu urządzeniach: podłącz wiele urządzeń do tej samej konwersacji opartej na mowie lub tekście i opcjonalnie tłumacz wiadomości wysyłane między nimi. Dowiedz się więcej w [tym artykule](multi-device-conversation.md). 
- Dodano obsługę rozpoznawania słów kluczowych dla pakietu Android .aar i dodano obsługę smaków x86 i x64. 
- Objective-C: `SendMessage` `SetMessageProperty` i metody `Connection` dodane do obiektu. Zobacz dokumentację [tutaj](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection).
- Interfejs API TTS C++ obsługuje `std::wstring` teraz jako wejście tekstu syntezy, eliminując konieczność konwersji wstringu na ciąg przed przekazaniem go do SDK. Zobacz szczegóły [tutaj](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [Identyfikator języka](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) i [config języka źródłowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) są teraz dostępne.
- JavaScript: Dodano funkcję `Connection` do obiektu, aby przejść przez niestandardowe wiadomości z usługi mowy jako wywołania zwrotnego `receivedServiceMessage`.
- JavaScript: Dodano `FromHost API` obsługę, aby ułatwić korzystanie z kontenerów na prem i suwerennych chmur. Zobacz dokumentację [tutaj](speech-container-howto.md).
- JavaScript: Teraz `NODE_TLS_REJECT_UNAUTHORIZED` honor dzięki wkład z [orgads](https://github.com/orgads). Zobacz szczegóły [tutaj](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).


**Fundamentalne zmiany**

- `OpenSSL`został zaktualizowany do wersji 1.1.1b i jest statycznie połączony z biblioteką rdzenia SDK mowy dla systemu Linux. Może to spowodować przerwę, `OpenSSL` jeśli skrzynka `/usr/lib/ssl` odbiorcza nie została zainstalowana w katalogu w systemie. Zapoznaj się z [naszą dokumentacją](how-to-configure-openssl-linux.md) w obszarze Dokumenty SDK mowy, aby obejść ten problem.
- Zmieniliśmy typ danych zwrócony `WordLevelTimingResult.Offset` dla `int` `long` języka C# `WordLevelTimingResults` z do, aby umożliwić dostęp do, gdy dane mowy jest dłuższy niż 2 minuty.
- `PushAudioInputStream`a `PullAudioInputStream` teraz wysyłać informacje nagłówka wav do usługi mowy na `AudioStreamFormat`podstawie , opcjonalnie określone, kiedy zostały utworzone. Klienci muszą teraz korzystać z [obsługiwanego formatu wejścia audio](how-to-use-audio-input-streams.md). Wszelkie inne formaty uzyskają nieoptymalne wyniki rozpoznawania lub mogą powodować inne problemy. 


**Poprawki błędów**

- Zobacz `OpenSSL` aktualizację w obszarze Łamanie zmian powyżej. Naprawiliśmy zarówno przerywany awaria, jak i problem z wydajnością (rywalizacja o blokadę przy dużym obciążeniu) w linuksie i javie. 
- Java: Wprowadzono ulepszenia do zamykania obiektów w scenariuszach wysokiej współbieżności.
- Zrestrukturyzowana nasz pakiet NuGet. Usunęliśmy trzy `Microsoft.CognitiveServices.Speech.core.dll` kopie `Microsoft.CognitiveServices.Speech.extension.kws.dll` folderów lib i pod nich, dzięki czemu pakiet NuGet jest mniejszy i szybszy do pobrania, a także dodaliśmy nagłówki potrzebne do skompilowania niektórych aplikacji natywnych języka C++.
- Naprawiono tutaj próbki szybkiego [startu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Były to wyjście bez wyświetlania wyjątku "nie znaleziono mikrofonu" w systemach Linux, MacOS, Windows.
- Naprawiono awarię SDK z długimi wynikami rozpoznawania mowy na niektórych ścieżkach kodu, takich jak [ten przykład.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)
- Naprawiono błąd wdrażania SDK w środowisku aplikacji Azure Web App w celu rozwiązania [tego problemu z klientem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Naprawiono błąd TTS `<voice>` podczas `<audio>` używania wielu tagów lub tagów w celu rozwiązania [tego problemu z klientem.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) 
- Naprawiono błąd TTS 401, gdy SDK jest odzyskiwany z zawieszenia.
- JavaScript: Poprawiono okrągły import danych audio dzięki wkładowi [euirim](https://github.com/euirim). 
- JavaScript: dodano obsługę ustawiania właściwości usługi, jak dodano w 1.7.
- JavaScript: rozwiązał problem polegający na tym, że błąd połączenia mógł spowodować ciągłe, nieudane próby ponownego połączenia w sieci Web.


**Próbki**

- Dodano przykład rozpoznawania słów kluczowych dla [Androida tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- Dodano próbkę TTS dla scenariusza serwera [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Dodano szybkie uruchamianie konwersacji na wielu urządzeniach dla języka C# i C++ [tutaj](quickstarts/multi-device-conversation.md).


**Inne zmiany**

- Zoptymalizowany rozmiar biblioteki podstawowej SDK w systemie Android.
- Zestaw SDK w wersji 1.9.0 `int` `string` i nowszej obsługuje zarówno typy, jak i typy w polu wersji podpisu głosowego dla transcribera konwersacji.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: wydanie 2019-listopadowe

**Nowe funkcje**

- Dodano `FromHost()` interfejs API, aby ułatwić korzystanie z kontenerów na przedwzmowych i suwerennych chmur.
- Dodano automatyczne wykrywanie języka źródłowego do rozpoznawania mowy (w językach Java i C++)
- Dodano `SourceLanguageConfig` obiekt do rozpoznawania mowy, używany do określania oczekiwanych języków źródłowych (w językach Java i C++)
- Dodano `KeywordRecognizer` obsługę pakietów NuGet i Unity w systemach Windows (UWP), Android i iOS
- Dodano interfejs API Java do zdalnej konwersacji do transkrypcji konwersacji w partiach asynchronicznych.

**Fundamentalne zmiany**

- Funkcje transcribera konwersacji przeniesione w obszarze nazw `Microsoft.CognitiveServices.Speech.Transcription`.
- Część konwersacji transcriber metody `Conversation` są przenoszone do nowej klasy.
- Porzucona obsługa 32-bitowego (ARMv7 i x86) systemu iOS

**Poprawki błędów**

- Napraw awarię, `KeywordRecognizer` jeśli lokalny jest używany bez prawidłowego klucza subskrypcji usługi mowy

**Próbki**

- Próbka xamarin dla`KeywordRecognizer`
- Próbka jedności dla`KeywordRecognizer`
- C++ i Java przykłady do automatycznego wykrywania języka źródłowego.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: wydanie 2019-Wrzesień

**Nowe funkcje**

- Dodano obsługę wersji beta platformy Xamarin na uniwersalnej platformie Windows (UWP), Android i iOS
- Dodano obsługę systemu iOS dla unity
- Dodano `Compressed` obsługę danych wejściowych dla ALaw, Mulaw, FLAC na Androida, iOS i Linux
- Dodano `SendMessageAsync` `Connection` w klasie do wysyłania wiadomości do serwisu
- Dodano `SetMessageProperty` `Connection` w klasie do ustawiania właściwości wiadomości
- TTS dodano powiązania dla Java (Jre i Android), Python, Swift i Objective-C
- TTS dodał obsługę odtwarzania dla systemów macOS, iOS i Android.
- Dodano informacje o granicy słowa dla TTS.

**Poprawki błędów**

- Naprawiono problem z kompilacją IL2CPP na Unity 2019 dla Androida
- Naprawiono błąd, który powodował nieprawidłowe przetwarzanie nieprawidłowego przetwarzania nagłówków nieprawidłowo w danych wejściowych pliku wav
- Naprawiono błąd, który powodował, że identyfikatory UUID nie były unikatowe w niektórych właściwościach połączenia
- Poprawiono kilka ostrzeżeń dotyczących specyfikatorów nieważność w swift powiązania (może wymagać drobnych zmian kodu)
- Naprawiono błąd, który powodował, że połączenia websocket były zamykane bezgranicznie pod obciążeniem sieci
- Naprawiono błąd, który powodował, że w systemie Android czasami pojawiały się identyfikatory zduplikowanych wyświetleń`DialogServiceConnector`
- Poprawa stabilności połączeń w interakcjach wielokierunkowych oraz zgłaszanie awarii `Canceled` (za pośrednictwem zdarzeń), gdy występują one z`DialogServiceConnector`
- `DialogServiceConnector`rozpocznie się sesja będzie teraz poprawnie dostarczać zdarzenia, w tym podczas `ListenOnceAsync()``StartKeywordRecognitionAsync()`
- Rozwiązano problem związany `DialogServiceConnector` z odbieraniem działań

**Próbki**

- Szybki start dla platformy Xamarin
- Zaktualizowano skrócony start CPP z informacjami o systemie Linux ARM64
- Zaktualizowany przewodnik Szybki start unity z informacjami o systemach iOS

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: wydanie 2019-Czerwiec

**Próbki**

- Przykłady szybkiego startu dla tekstu do mowy na platformie uniwersalnej systemu wyu według platformy uniwersalnej systemu i jedności
- Próbka szybkiego startu dla programu Swift w iOS
- Próbki unity dla rozpoznawania mowy & intencji i tłumaczenia
- Zaktualizowano przykłady szybkiego startu`DialogServiceConnector`

**Ulepszenia / Zmiany**

- Obszar nazw okna dialogowego:
  - Zmieniono nazwę polecenia `SpeechBotConnector` na `DialogServiceConnector`
  - Zmieniono nazwę polecenia `BotConfig` na `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`został ponownie odserwowany na`DialogServiceConfig::FromBotSecret()`
  - Wszystkie istniejące klienci direct line speech są nadal obsługiwani po zmianie nazwy
- Aktualizowanie karty TTS REST do obsługi serwera proxy, połączenia trwałego
- Poprawianie komunikatu o błędzie po przekazaniu nieprawidłowego regionu
- Swift/Objective-C:
  - Ulepszone raportowanie błędów: Metody, które mogą spowodować błąd są `NSError` teraz obecne w dwóch wersjach: Jeden, który udostępnia obiekt do obsługi błędów i jeden, który zgłasza wyjątek. Te pierwsze są narażone na Swift. Ta zmiana wymaga dostosowania do istniejącego kodu Swift.
  - Ulepszona obsługa zdarzeń

**Poprawki błędów**

- Poprawka dla TTS: gdzie `SpeakTextAsync` przyszłość powróciła bez czekania, aż dźwięk zakończy renderowanie
- Poprawka dla rozsyłania ciągów w języku C#, aby włączyć obsługę pełnego języka
- Napraw problem z aplikacją .NET core, aby załadować bibliotekę podstawową z platformą docelową net461 w przykładach
- Rozwiązywanie sporadyczne problemy z wdrażaniem bibliotek natywnych w folderze wyjściowym w przykładach
- Napraw niezawodne zamykanie gniazd internetowych
- Napraw możliwość awarii podczas otwierania połączenia pod bardzo dużym obciążeniem na Linuksie
- Poprawka braku metadanych w pakiecie framework dla systemu macOS
- Rozwiązywanie problemów `pip install --user` z systemem Windows

## <a name="speech-sdk-151"></a>Mowa SDK 1.5.1

Jest to wydanie poprawki błędów i dotyczy tylko natywnego/zarządzanego SDK. Nie ma to wpływu na wersję javascript sdk.

**Poprawki błędów**

- Napraw FromSubscription, gdy jest używany z transkrypcją konwersacji.
- Napraw błąd w wykrywaniu słów kluczowych dla asystentów głosowych.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: wydanie 2019-Maj

**Nowe funkcje**

- Wykrywanie słów kluczowych (KWS) jest teraz dostępne dla systemów Windows i Linux. Funkcja KWS może działać z dowolnym typem mikrofonu, oficjalna obsługa KWS jest jednak obecnie ograniczona do macierzy mikrofonów znalezionych w sprzęcie usługi Azure Kinect DK lub sdk urządzeń mowy.
- Funkcja podpowiedzi do wyrażenia jest dostępna za pośrednictwem sdk. Aby uzyskać więcej informacji, zobacz [tutaj](how-to-phrase-lists.md).
- Funkcja transkrypcji konwersacji jest dostępna za pośrednictwem sdk. Zobacz [tutaj](conversation-transcription-service.md).
- Dodaj obsługę asystentów głosowych za pomocą kanału Direct Line Speech.

**Próbki**

- Dodano przykłady nowych funkcji lub nowych usług obsługiwanych przez zestaw SDK.

**Ulepszenia / Zmiany**

- Dodano różne właściwości aparatu rozpoznawania, aby dostosować zachowanie usługi lub wyniki usługi (takie jak maskowanie wulgaryzmów i innych).
- Teraz można skonfigurować aparat rozpoznawania za pomocą standardowych właściwości konfiguracji, `FromEndpoint`nawet jeśli utworzono aparat rozpoznawania .
- Objective-C: `OutputFormat` właściwość `SPXSpeechConfiguration`została dodana do .
- SDK obsługuje teraz Debiana 9 jako dystrybucję Linuksa.

**Poprawki błędów**

- Naprawiono błąd, który powodował, że zasób głośnika był zbyt wczesny w zamian za zamianę tekstu na mowę.

## <a name="speech-sdk-142"></a>Mowa SDK 1.4.2

Jest to wydanie poprawki błędów i dotyczy tylko natywnego/zarządzanego SDK. Nie ma to wpływu na wersję javascript sdk.

## <a name="speech-sdk-141"></a>SDK mowy 1.4.1

Jest to wersja dostępna tylko w języku JavaScript. Nie dodano żadnych funkcji. Wprowadzono następujące poprawki:

- Zapobiegaj ładowaniu pakietu internetowego przez https-proxy-agenta.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: wydanie 2019-kwiecień

**Nowe funkcje**

- Zestaw SDK obsługuje teraz usługę zamiany tekstu na mowę w wersji beta. Jest obsługiwany w systemach Windows i Linux Desktop z języka C++ i C#. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem zamiany tekstu na mowę](text-to-speech.md#get-started).
- Zestaw SDK obsługuje teraz pliki audio MP3 i Opus/OGG jako pliki wejściowe strumienia. Ta funkcja jest dostępna tylko w systemie Linux z języka C++ i C# i jest obecnie w wersji beta (więcej szczegółów [tutaj](how-to-use-codec-compressed-audio-input-streams.md)).
- SDK mowy dla języka Java, .NET core, C++ i Objective-C zyskały obsługę systemu macOS. Obsługa funkcji Objective-C dla systemu macOS jest obecnie w wersji beta.
- iOS: SDK mowy dla systemu iOS (Objective-C) jest teraz również publikowany jako CocoaPod.
- JavaScript: Obsługa mikrofonu nie domyślnego jako urządzenia wejściowego.
- JavaScript: Obsługa serwera proxy dla pliku Node.js.

**Próbki**

- Dodano przykłady użycia zestawu SDK mowy w języku C++ i z objectiveem C w systemie macOS.
- Dodano przykłady przedstawiające użycie usługi zamiany tekstu na mowę.

**Ulepszenia / Zmiany**

- Python: Dodatkowe właściwości wyników rozpoznawania są `properties` teraz udostępniane za pośrednictwem właściwości.
- Aby uzyskać dodatkową obsługę rozwoju i debugowania, można przekierować informacje logowania i diagnostyki SDK do pliku dziennika (więcej szczegółów [tutaj](how-to-use-logging.md)).
- JavaScript: Zwiększ wydajność przetwarzania dźwięku.

**Poprawki błędów**

- Mac/iOS: Naprawiono błąd, który prowadził do długiego oczekiwania, gdy nie można ustanowić połączenia z usługą mowy.
- Python: usprawnij obsługę błędów dla argumentów w wywołaniach zwrotnych języka Python.
- JavaScript: Poprawiono nieprawidłowe raportowanie stanu dla mowy zakończone na RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>SDK mowy 1.3.1: odświeżanie 2019-luty

Jest to wydanie poprawki błędów i dotyczy tylko natywnego/zarządzanego SDK. Nie ma to wpływu na wersję javascript sdk.

**Poprawka**

- Naprawiono wyciek pamięci podczas korzystania z wejścia mikrofonu. Nie ma to wpływu na dane wejściowe oparte na strumieniu lub pliku.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: wydanie 2019-luty

**Nowe funkcje**

- Zestaw SDK mowy obsługuje wybór mikrofonu wejściowego za pośrednictwem `AudioConfig` klasy. Dzięki temu można przesyłać strumieniowo dane audio do usługi Mowy z mikrofonu nienawicznego. Aby uzyskać więcej informacji, zobacz dokumentację opisującą [wybór urządzenia wejściowego audio](how-to-select-audio-input-devices.md). Ta funkcja nie jest jeszcze dostępna w języku JavaScript.
- Zestaw SDK mowy obsługuje teraz unity w wersji beta. Przekazywać opinie za pośrednictwem sekcji problemu w [przykładowym repozytorium GitHub](https://aka.ms/csspeech/samples). Ta wersja obsługuje unity w systemach Windows x86 i x64 (aplikacje dla komputerów stacjonarnych lub uniwersalnych platformy systemu Windows) i android (ARM32/64, x86). Więcej informacji można znaleźć w naszym [przewodniku Unity Quickstart.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)
- Plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dostarczany w poprzednich wersjach) nie jest już potrzebny. Funkcja jest teraz zintegrowana z podstawowym SDK.

**Próbki**

W naszym [przykładowym repozytorium](https://aka.ms/csspeech/samples)dostępna jest następująca nowa zawartość:

- Dodatkowe próbki `AudioConfig.FromMicrophoneInput`dla .
- Dodatkowe przykłady języka Python do rozpoznawania intencji i tłumaczenia.
- Dodatkowe przykłady do `Connection` korzystania z obiektu w iOS.
- Dodatkowe przykłady języka Java do tłumaczenia z wyjściem audio.
- Nowy przykład do użycia [interfejsu API REST transkrypcji partii](batch-transcription.md).

**Ulepszenia / Zmiany**

- Python
  - Ulepszona weryfikacja parametrów i komunikaty o błędach w `SpeechConfig`pliku .
  - Dodaj obsługę `Connection` obiektu.
  - Obsługa 32-bitowego języka Python (x86) w systemie Windows.
  - SDK mowy dla języka Python jest poza wersję beta.
- iOS
  - SDK jest teraz zbudowany na skusi iOS W wersji 12.1.
  - Zestaw SDK obsługuje teraz system iOS w wersji 9.2 i nowszych.
  - Popraw dokumentację referencyjną i napraw kilka nazw właściwości.
- JavaScript
  - Dodaj obsługę `Connection` obiektu.
  - Dodawanie plików definicji typu dla dołączonego języka JavaScript
  - Początkowa obsługa i implementacja wskazówek dotyczących fraz.
  - Zbieranie właściwości zwrotu z usługą JSON do rozpoznawania
- Biblioteki DLL systemu Windows zawierają teraz zasób wersji.
- Jeśli utworzysz `FromEndpoint` aparat rozpoznawania, możesz dodać parametry bezpośrednio do adresu URL punktu końcowego. Za `FromEndpoint` pomocą nie można skonfigurować aparat rozpoznawania za pomocą standardowych właściwości konfiguracji.

**Poprawki błędów**

- Pusta nazwa użytkownika serwera proxy i hasło serwera proxy nie były obsługiwane poprawnie. W tej wersji jeśli ustawisz nazwę użytkownika serwera proxy i hasło serwera proxy na pusty ciąg, nie zostaną one przesłane podczas łączenia się z serwerem proxy.
- SessionId's utworzone przez SDK nie zawsze były&nbsp;naprawdę losowe dla niektórych języków / środowisk. Dodano inicjowanie generatora losowego, aby rozwiązać ten problem.
- Usprawnij obsługę tokenu autoryzacji. Jeśli chcesz użyć tokenu autoryzacji, `SpeechConfig` określ w kluczu subskrypcji i pozostaw klucz subskrypcji pusty. Następnie utwórz aparat rozpoznawania jak zwykle.
- W niektórych `Connection` przypadkach obiekt nie został poprawnie zwolniony. Ten problem został rozwiązany.
- Próbka JavaScript została naprawiona w celu obsługi wyjścia audio do syntezy tłumaczeń również w przeglądarce Safari.

## <a name="speech-sdk-121"></a>SDK mowy 1.2.1

Jest to wersja dostępna tylko w języku JavaScript. Nie dodano żadnych funkcji. Wprowadzono następujące poprawki:

- Koniec ognia strumienia na turn.end, a nie na speech.end.
- Napraw błąd w pompie audio, który nie zaplanował następnego wysłania, jeśli bieżące wysłanie nie powiodło się.
- Napraw ciągłe rozpoznawanie za pomocą tokenu omytowego.
- Poprawka błędu dla różnych aparat rozpoznawania / punktów końcowych.
- Ulepszenia dokumentacji.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: wydanie 2018-grudzień

**Nowe funkcje**

- Python
  - Wersja beta obsługi języka Python (3.5 lub powyżej) jest dostępna w tej wersji. Aby uzyskać więcej informacji, zobacz tutaj](szybki start-python.md).
- JavaScript
  - SDK mowy dla języka JavaScript został open source. Kod źródłowy jest dostępny w [serwisie GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Teraz obsługujemy Node.js, więcej informacji można znaleźć [tutaj](quickstart-js-node.md).
  - Usunięto ograniczenie długości sesji audio, ponowne połączenie nastąpi automatycznie pod osłoną.
- `Connection`Obiektu
  - Z `Recognizer`obiektu można uzyskać `Connection` dostęp do obiektu. Ten obiekt umożliwia jawne inicjowanie połączenia usługi i subskrybowanie zdarzeń łączenia i rozłączania.
    (Ta funkcja nie jest jeszcze dostępna w językach JavaScript i Pythonie).
- Wsparcie dla Ubuntu 18.04.
- Android
  - Obsługa proguard podczas generowania apk.

**Ulepszenia**

- Ulepszenia użycia wątku wewnętrznego, zmniejszając liczbę wątków, blokad, muteksów.
- Ulepszone raportowanie błędów / informacje. W kilku przypadkach komunikaty o błędach nie zostały propagowane przez całe wyjście.
- Zaktualizowano zależności programistyczne w języku JavaScript w celu użycia aktualnych modułów.

**Poprawki błędów**

- Naprawiono przecieki pamięci spowodowane niezgodnością `RecognizeAsync`typu w pliku .
- W niektórych przypadkach wyciekały wyjątki.
- Naprawianie przecieku pamięci w argumentach zdarzeń tłumaczenia.
- Naprawiono błąd blokujący podczas ponownego łączenia się podczas długotrwałych sesji.
- Naprawiono błąd, który mógł prowadzić do braku wyniku końcowego dla nieudanych tłumaczeń.
- C#: Jeśli `async` operacja nie była oczekiwana w wątku głównym, możliwe było, że aparat rozpoznawania może zostać usunięty przed zakończeniem zadania asynchronicznego.
- Java: Naprawiono błąd powodujący awarię maszyny Wirtualnej Java.
- Objective-C: Poprawiono mapowanie wyliczenia; RecognizedIntent został zwrócony `RecognizingIntent`zamiast .
- JavaScript: Ustaw domyślny format wyjściowy na "prosty" w . `SpeechConfig`
- JavaScript: Usuwanie niespójności między właściwościami obiektu konfiguracyjnego w języku JavaScript i innych językach.

**Próbki**

- Zaktualizowano i poprawiono kilka próbek (na przykład głosy wyjściowe do tłumaczenia itp.).
- Dodano przykłady node.js w [przykładowym repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Mowa SDK 1.1.0

**Nowe funkcje**

- Obsługa systemu Android x86/x64.
- Obsługa serwera proxy: W `SpeechConfig` obiekcie można teraz wywołać funkcję, aby ustawić informacje proxy (nazwa hosta, port, nazwa użytkownika i hasło). Ta funkcja nie jest jeszcze dostępna w systemach iOS.
- Ulepszony kod błędu i komunikaty. Jeśli uznanie zwróciło błąd, to `Reason` już ustawione (w `CancellationDetails` odwołanym zdarzeniu) lub (w wyniku uznania) do `Error`. Anulowane wydarzenie zawiera teraz dwóch `ErrorCode` `ErrorDetails`dodatkowych członków i . Jeśli serwer zwrócił dodatkowe informacje o błędzie ze zgłoszonym błędem, będzie teraz dostępny w nowych członkach.

**Ulepszenia**

- Dodano dodatkową weryfikację w konfiguracji aparatu rozpoznawania i dodano dodatkowy komunikat o błędzie.
- Poprawiono obsługę długotrwałej ciszy w środku pliku audio.
- Pakiet NuGet: dla projektów .NET Framework zapobiega tworzeniu za pomocą konfiguracji AnyCPU.

**Poprawki błędów**

- Naprawiono kilka wyjątków znalezionych w aparatach rozpoznawania. Ponadto wyjątki są przechwytywały `Canceled` i konwertowane na zdarzenie.
- Napraw przeciek pamięci w zarządzaniu właściwościami.
- Naprawiono błąd, w którym plik wejściowy audio mógł spowodować awarię aparatu rozpoznawania.
- Naprawiono błąd, który powodował, że zdarzenia mogły być odbierane po zdarzeniu zatrzymania sesji.
- Poprawiono niektóre warunki wyścigu w gwintowaniu.
- Naprawiono błąd ze zgodnością systemu iOS, który mógł spowodować awarię.
- Ulepszenia stabilności dla obsługi mikrofonu Android.
- Naprawiono błąd, który powodował, że aparat rozpoznawania w języku JavaScript ignorował język rozpoznawania.
- Naprawiono błąd uniemożliwiający `EndpointId` ustawienie (w niektórych przypadkach) w języku JavaScript.
- Zmieniono kolejność parametrów w addintent `AddIntent` w języku JavaScript i dodano brakujący podpis JavaScript.

**Próbki**

- Dodano przykłady języka C++ i C# do użycia strumienia ściągania i wypychania w [repozytorium próbek.](https://aka.ms/csspeech/samples)

## <a name="speech-sdk-101"></a>Mowa SDK 1.0.1

Ulepszenia niezawodności i poprawki błędów:

- Naprawiono potencjalny błąd krytyczny spowodowany stanem wyścigu w utylizowaniu aparatu rozpoznawania
- Naprawiono potencjalny błąd krytyczny w przypadku nieustawionych właściwości.
- Dodano dodatkowe sprawdzanie błędów i parametrów.
- Objective-C: Naprawiono możliwy błąd krytyczny spowodowany zastępowaniem nazwy w NSString.
- Objective-C: Dostosowana widoczność API
- JavaScript: Poprawiono zdarzenia i ich ładunki.
- Ulepszenia dokumentacji.

W naszym [przykładowym repozytorium](https://aka.ms/csspeech/samples)dodano nowy przykład dla języka JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: wydanie 2018-Wrzesień

**Nowe funkcje**

- Obsługa języka Objective-C w systemu iOS. Sprawdź nasz [przewodnik Objective-C dla systemu iOS.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)
- Obsługa javascript w przeglądarce. Sprawdź nasz [Szybki start JavaScript](quickstart-js-browser.md).

**Fundamentalne zmiany**

- W tej wersji wprowadzono szereg przełomowych zmian.
  Sprawdź [tę stronę,](https://aka.ms/csspeech/breakingchanges_1_0_0) aby uzyskać szczegółowe informacje.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: wydanie 2018-sierpień

**Nowe funkcje**

- Aplikacje platformy uniwersalnej systemu Windows utworzone za pomocą zestawu SDK mowy mogą teraz przekazać zestaw certyfikacji aplikacji systemu Windows (WACK).
  Zapoznaj się z [programem Szybki start platformy Uniwersalnej Systemu Wschowego](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Obsługa .NET Standard 2.0 w systemie Linux (Ubuntu 16.04 x64).
- Eksperymentalne: Obsługa Java 8 w systemie Windows (64-bit) i Linux (Ubuntu 16.04 x64).
  Zapoznaj się z [przewodnikiem Szybki start środowiska wykonawczego Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Zmiana funkcjonalności**

- Uwidacznia dodatkowe informacje o błędach dotyczących błędów połączeń.

**Fundamentalne zmiany**

- W języku Java `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` (Android) funkcja nie wymaga już parametru ścieżki. Teraz ścieżka jest automatycznie wykrywana na wszystkich obsługiwanych platformach.
- Get-akcesor `EndpointUrl` właściwości w języku Java i C# został usunięty.

**Poprawki błędów**

- W języku Java wynik syntezy audio na aparat rozpoznawania tłumaczenia jest zaimplementowany teraz.
- Naprawiono błąd, który mógł powodować nieaktywne wątki i zwiększoną liczbę otwartych i nieużywanych gniazd.
- Naprawiono błąd, który powodował, że długotrwałe rozpoznawanie mogło zakończyć się w środku transmisji.
- Naprawiono stan wyścigu w zamykaniu aparatu rozpoznawania.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>SDK mowy usług Cognitive Services 0.5.0: wydanie 2018-lipiec

**Nowe funkcje**

- Obsługa platformy Android (API 23: Android 6.0 Marshmallow lub nowsza). Sprawdź [Szybki start systemu Android](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Obsługa platformy .NET Standard 2.0 w systemie Windows. Zapoznaj się z [programem szybki start programu .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Eksperymentalne: Obsługa platformy uniwersalnej systemu Windows (wersja 1709 lub nowsza).
  - Zapoznaj się z [programem Szybki start platformy Uniwersalnej Systemu Wschowego](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Uwaga: Aplikacje platformy uniwersalnej systemu Windows utworzone za pomocą zestawu SDK mowy nie przechodzą jeszcze zestawu certyfikacji aplikacji systemu Windows (WACK).
- Obsługa długotrwałego rozpoznawania z automatycznym ponownym podłączeniu.

**Zmiany funkcjonalne**

- `StartContinuousRecognitionAsync()`obsługuje długotrwałe rozpoznawanie.
- Wynik rozpoznawania zawiera więcej pól. Są one odsunięte od początku i czasu trwania dźwięku (zarówno w zaznaczeniach) `InitialSilenceTimeout` rozpoznanego tekstu, jak i dodatkowych wartości, które reprezentują stan rozpoznawania, na przykład i `InitialBabbleTimeout`.
- Obsługa AuthorizationToken do tworzenia wystąpień fabrycznych.

**Fundamentalne zmiany**

- Rozpoznawanie `NoMatch` zdarzeń: typ zdarzenia `Error` został scalony ze zdarzeniem.
- SpeechOutputFormat w języku C# `OutputFormat` został zmieniony, aby zachować wyrównany z C++.
- Typ zwracania niektórych `AudioInputStream` metod interfejsu zmienił się nieznacznie:
  - W języku `read` Java metoda `long` zwraca `int`teraz zamiast .
  - W języku `Read` C#metoda `uint` zwraca teraz `int`zamiast .
  - W `Read` języku C++ `GetFormat` metody `size_t` i metody `int`zwracają teraz zamiast .
- C++: Wystąpienia strumieni wejściowych audio mogą być `shared_ptr`teraz przekazywane tylko jako .

**Poprawki błędów**

- Naprawiono nieprawidłowe wartości `RecognizeAsync()` zwracane w wyniku, gdy przesunie się czas.
- Usunięto zależność od bibliotek fundacji multimediów w systemie Windows. SDK używa teraz podstawowych interfejsów API audio.
- Poprawka dokumentacji: Dodano stronę [regionów,](regions.md) aby opisać obsługiwane regiony.

**Znany problem**

- SDK mowy dla systemu Android nie zgłasza wyników syntezy mowy do tłumaczenia. Ten problem zostanie rozwiązany w następnej wersji.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>SDK mowy usług Cognitive Services 0.4.0: wydanie 2018-Czerwiec

**Zmiany funkcjonalne**

- AudioInputStream (Strumień audio)

  Aparat rozpoznawania może teraz zużywać strumień jako źródło dźwięku. Aby uzyskać więcej informacji, zobacz [powiązany przewodnik po instrukcjach](how-to-use-audio-input-streams.md).

- Szczegółowy format danych wyjściowych

  Podczas tworzenia `SpeechRecognizer`formatu , `Detailed` `Simple` można zażądać lub format wyjściowy. Zawiera `DetailedSpeechRecognitionResult` wynik zaufania, rozpoznany tekst, surową leksykalne, znormalizowane formy i znormalizowane formy z zamaskowanym wulgaryzmami.

**Przełomowa zmiana**

- Zmieniono `SpeechRecognitionResult.Text` `SpeechRecognitionResult.RecognizedText` na w języku C#.

**Poprawki błędów**

- Naprawiono możliwy problem z wywołaniem zwrotnym w warstwie USP podczas zamykania systemu.
- Jeśli aparat rozpoznawania używane audio plik wejściowy, trzymał się dojścia do pliku dłużej niż to konieczne.
- Usunięto kilka zakleszczenia między pompą wiadomości a aparatem rozpoznawania.
- Zwolnij `NoMatch` wynik, gdy przesunie się limit czasu odpowiedzi z serwisu.
- Biblioteki fundacji multimediów w systemie Windows są opóźnione. Ta biblioteka jest wymagana tylko dla wejścia mikrofonu.
- Szybkość przesyłania danych audio jest ograniczona do około dwukrotnie wyższej niż oryginalna prędkość dźwięku.
- W systemie Windows zestawy C# .NET są teraz silne nazwane.
- Poprawka dokumentacji: `Region` jest wymagane informacje do utworzenia aparatu rozpoznawania.

Dodano więcej próbek i są stale aktualizowane. Aby uzyskać najnowszy zestaw przykładów, zobacz [przykłady zestawu SDK mowy repozytorium GitHub](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>SDK mowy usług Cognitive Services 0.2.12733: wydanie 2018-majowe

Ta wersja jest pierwszą publiczną wersją podglądu sdk mowy usług Cognitive Services.
