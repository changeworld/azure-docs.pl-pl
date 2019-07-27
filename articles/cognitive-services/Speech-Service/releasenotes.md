---
title: Informacje o wersji — usługa mowy
titleSuffix: Azure Cognitive Services
description: Zapoznaj się z uruchomionym dziennikiem wersji funkcji, ulepszeń, poprawek błędów i znanych problemów dotyczących usługi Speech.
services: cognitive-services
author: BrianMouncer
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 12eecc4998153cbeedeb907ecad33c56141a50e6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559123"
---
# <a name="release-notes"></a>Informacje o wersji

## <a name="speech-sdk-160-2019-june-release"></a>1\.6.0 zestawu mowy SDK: 2019 — wydanie z czerwca

**Przykłady**
*   Przykłady szybkiego startu dla zamiany tekstu na mowę w platformy UWP i Unity
*   Przykład szybkiego startu dla SWIFT w systemie iOS
*   Przykłady aparatu Unity dla rozpoznawanie intencji & mowy i tłumaczenia
*   Zaktualizowane przykłady przewodnika Szybki Start dla DialogServiceConnector

**Ulepszenia/zmiany**
* Przestrzeń nazw okna dialogowego:
    * Zmieniono nazwę SpeechBotConnector na DialogServiceConnector
    * Zmieniono nazwę BotConfig na DialogServiceConfig
    * BotConfig:: FromChannelSecret () został ponownie zmapowany do DialogServiceConfig:: FromBotSecret ()
    * Wszystkie istniejące bezpośrednie komputery klienckie mowy są nadal obsługiwane po zmianie nazwy
* Aktualizowanie karty REST usługi TTS do obsługi serwera proxy, trwałego połączenia
* Ulepsz komunikat o błędzie po przekazaniu nieprawidłowego regionu
* SWIFT/cel-C:
    * Ulepszone raportowanie błędów: Metody, które mogą spowodować błąd, są teraz obecne w dwóch wersjach: Taki, który uwidacznia `NSError` obiekt do obsługi błędów, i jeden, który wywołuje wyjątek. Dawniej są dostępne dla Swift. Ta zmiana wymaga adaptacji do istniejącego kodu SWIFT.
    * Ulepszona obsługa zdarzeń

**Poprawki błędów**
*   Poprawka dla usługi TTS: gdzie SpeakTextAsync w przyszłości, bez oczekiwania na ukończenie renderowania dźwięku
*   Poprawka dotycząca przekazywania ciągów w programie C# w celu zapewnienia obsługi pełnego języka
*   Naprawa problemu z aplikacją .NET Core w celu załadowania biblioteki podstawowej z platformą docelową net461 w przykładach
*   Naprawa sporadycznych problemów związanych z wdrażaniem bibliotek natywnych w folderze wyjściowym w przykładach
*   Naprawa niezawodnego zamykania gniazda internetowego
*   Naprawianie możliwej awarii podczas otwierania połączenia w przypadku bardzo dużego obciążenia w systemie Linux
*   Naprawianie brakujących metadanych w pakiecie struktury dla macOS
*   Rozwiązywanie problemów z `pip install --user` systemem Windows


## <a name="speech-sdk-151"></a>Zestaw Speech SDK 1.5.1

Jest to poprawka usterek i ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję języka JavaScript zestawu SDK.

**Poprawki błędów**

* Należy rozwiązać FromSubscription, gdy jest używany z transkrypcją konwersacji.
* Usuń usterkę w wykrywania trendów słowa kluczowego dla wirtualnego asystenta głosowego.


## <a name="speech-sdk-150-2019-may-release"></a>1\.5.0 zestawu mowy SDK: 2019 — może wydać

**Nowe funkcje**

* Funkcja Wake Word (keyword wykrywania trendów/KWS) jest teraz dostępna dla systemów Windows i Linux. Funkcja KWS może działać z dowolnym typem mikrofonu, oficjalną obsługą KWS, ale jest obecnie ograniczona do tablic mikrofonów znajdujących się na sprzęcie platformy Azure urządzenia Kinect DK lub zestawu Speech Devices SDK.
* Funkcja podpowiedzi frazy jest dostępna za pomocą zestawu SDK. Aby uzyskać więcej informacji, zobacz [tutaj](how-to-phrase-lists.md).
* Funkcja transkrypcji konwersacji jest dostępna za pomocą zestawu SDK. Zobacz [tutaj](conversation-transcription-service.md).
* Dodaj obsługę asystentów wirtualnych w pierwszej kolejności głosowej przy użyciu kanału mowy w linii bezpośredniej.

**Przykłady**

* Dodano przykłady dla nowych funkcji lub nowych usług obsługiwanych przez zestaw SDK.

**Ulepszenia/zmiany**

* Dodano różne właściwości aparatu rozpoznawania w celu dostosowania zachowania usługi lub wyników usług (takich jak maskowanie wulgarności i innych).
* Teraz można skonfigurować aparat rozpoznawania przy użyciu standardowych właściwości konfiguracji, nawet jeśli aparat rozpoznawania `FromEndpoint`został utworzony.
* Cel-C: `OutputFormat` właściwość została dodana do SPXSpeechConfiguration.
* Zestaw SDK obsługuje teraz Debian 9 jako dystrybucję systemu Linux.

**Poprawki błędów**

* Rozwiązano problem polegający na tym, że zasób osoby mówiącej był zbyt wczesny w zamian tekstu na mowę.
## <a name="speech-sdk-142"></a>Zestaw mowy SDK 1.4.2

Jest to poprawka usterek i ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję języka JavaScript zestawu SDK.

## <a name="speech-sdk-141"></a>Zestaw Speech SDK 1.4.1

Jest to wersja tylko w języku JavaScript. Nie dodano żadnych funkcji. Wprowadzono następujące poprawki:

* Zapobiegaj ładowaniu protokołu HTTPS-proxy-Agent przez pakiet internetowy.

## <a name="speech-sdk-140-2019-april-release"></a>1\.4.0 zestawu mowy SDK: 2019 — wydanie z kwietnia

**Nowe funkcje** 

* Zestaw SDK obsługuje teraz usługę zamiany tekstu na mowę jako wersję beta. Jest ona obsługiwana na komputerach z C++ systemami Windows i Linux C#w systemach i. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem zamiany tekstu na mowę](text-to-speech.md#get-started-with-text-to-speech).
* Zestaw SDK obsługuje teraz pliki audio MP3 i Opus/OGG jako pliki wejściowe strumienia. Ta funkcja jest dostępna tylko w systemie Linux C++ z C# systemów i i jest obecnie w wersji beta (więcej informacji znajduje się [tutaj](how-to-use-codec-compressed-audio-input-streams.md)).
* Zestaw Speech SDK dla języka Java, .NET Core C++ i dążyć-C uzyskał pomoc techniczną macOS. Obsługa języka C dla macOS jest obecnie w wersji beta.
* iOS: Zestaw Speech SDK dla systemu iOS (cel-C) jest teraz również publikowany jako CocoaPod.
* JavaScript: Obsługa mikrofonu innego niż domyślny jako urządzenia wejściowego.
* JavaScript: Obsługa serwera proxy dla środowiska Node. js.

**Przykłady**

* Przykłady użycia zestawu Speech SDK z C++ i z obiektyw-C w systemie macOS zostały dodane.
* Przykłady pokazujące użycie usługi zamiany tekstu na mowę zostały dodane.

**Ulepszenia/zmiany**

* Python: Dodatkowe właściwości wyników rozpoznawania są teraz udostępniane za pośrednictwem `properties` właściwości.
* Aby uzyskać dodatkową pomoc w zakresie programowania i debugowania, możesz przekierować informacje dotyczące rejestrowania i diagnostyki zestawu SDK do pliku dziennika (więcej informacji znajdziesz [tutaj](how-to-use-logging.md)).
* JavaScript: Popraw wydajność przetwarzania dźwięku.

**Poprawki błędów**

* Mac/iOS: Błąd, który doprowadził do długiego oczekiwania, gdy nie można nawiązać połączenia z usługą mowy, został naprawiony.
* Python: poprawianie obsługi błędów dla argumentów w wywołaniach zwrotnych języka Python.
* JavaScript: Naprawiono nieprawidłowe raportowanie stanu dla mowy zakończono w dniu RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Zestaw mowy SDK 1.3.1: 2019 – luty Refresh

Jest to poprawka usterek i ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję języka JavaScript zestawu SDK.

**Poprawka błędu**

* Naprawiono przeciek pamięci podczas korzystania z mikrofonu. Nie ma to żadnego oddziaływania na dane wejściowe lub oparte na strumieniu.

## <a name="speech-sdk-130-2019-february-release"></a>1\.3.0 zestawu mowy SDK: 2019 — wydanie z lutego

**Nowe funkcje**

* Zestaw Speech SDK obsługuje wybór mikrofonu wejściowego za pomocą klasy AudioConfig. Pozwala to na przesyłanie strumieniowe danych audio do usługi mowy z mikrofonu innego niż domyślny. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [wyboru urządzenia wejściowego audio](how-to-select-audio-input-devices.md). Ta funkcja nie jest jeszcze dostępna w języku JavaScript.
* Zestaw Speech SDK obsługuje teraz środowisko Unity w wersji beta. Prześlij opinię w sekcji problemu w [repozytorium przykładowym usługi GitHub](https://aka.ms/csspeech/samples). Ta wersja obsługuje środowisko Unity w systemie Windows x86 i x64 (aplikacje klasyczne lub platforma uniwersalna systemu Windows) oraz Android (ARM32/64, x86). Więcej informacji można znaleźć w naszym [przewodniku szybki start dla aparatu Unity](quickstart-csharp-unity.md).
* Plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dostarczany w poprzednich wersjach) nie jest już wymagany. Ta funkcja jest teraz zintegrowana z podstawowym zestawem SDK.


**Przykłady**

W naszym [przykładowym repozytorium](https://aka.ms/csspeech/samples)jest dostępna następująca Nowa zawartość:

* Dodatkowe przykłady dla AudioConfig. FromMicrophoneInput.
* Dodatkowe przykłady w języku Python dotyczące rozpoznawania i tłumaczenia zamierzeń.
* Dodatkowe przykłady dotyczące korzystania z obiektu połączenia w systemie iOS.
* Dodatkowe przykłady języka Java do tłumaczenia z danymi wyjściowymi audio.
* Nowy przykład do korzystania z [interfejsu API REST transkrypcji usługi Batch](batch-transcription.md).

**Ulepszenia/zmiany**

* Python
  * Ulepszona Weryfikacja parametrów i komunikaty o błędach w SpeechConfig.
  * Dodaj obsługę obiektu połączenia.
  * Obsługa 32-bitowego języka Python (x86) w systemie Windows.
  * Zestaw Speech SDK dla języka Python jest poza wersją beta.
* iOS
  * Zestaw SDK jest teraz oparty na zestawie SDK systemu iOS w wersji 12,1.
  * Zestaw SDK obsługuje teraz system iOS w wersji 9,2 lub nowszej.
  * Popraw dokumentację referencyjną i popraw kilka nazw właściwości.
* JavaScript
  * Dodaj obsługę obiektu połączenia.
  * Dodaj pliki definicji typów dla powiązanego języka JavaScript
  * Wstępna pomoc techniczna i implementacja dla wskazówek dotyczących fraz.
  * Zwracanie kolekcji właściwości przy użyciu notacji JSON usługi dla rozpoznawania
* Biblioteki DLL systemu Windows zawierają teraz zasób wersji.
* Jeśli tworzysz aparat rozpoznawania `FromEndpoint` , możesz dodać parametry bezpośrednio do adresu URL punktu końcowego. `FromEndpoint` Nie można skonfigurować aparatu rozpoznawania przy użyciu standardowych właściwości konfiguracji.

**Poprawki błędów**

* Pusta nazwa użytkownika serwera proxy i hasło serwera proxy nie zostały poprawnie obsłużone. W tej wersji, jeśli ustawisz nazwę użytkownika serwera proxy i hasło serwera proxy na pusty ciąg, nie zostaną one przesłane podczas nawiązywania połączenia z serwerem proxy.
* Identyfikator sesji utworzony przez zestaw SDK nie zawsze jest losowo obsługiwany w niektórych językach&nbsp;/środowiskach. Dodano losową inicjalizację generatora, aby rozwiązać ten problem.
* Poprawa obsługi tokenu autoryzacji. Jeśli chcesz użyć tokenu autoryzacji, określ wartość w SpeechConfig i pozostaw klucz subskrypcji pusty. Następnie utwórz aparat rozpoznawania w zwykły sposób.
* W niektórych przypadkach obiekt Connection nie został prawidłowo wydano. Ten problem został rozwiązany.
* Przykład JavaScript został rozwiązany do obsługi danych wyjściowych audio na potrzeby syntezy tłumaczenia również w przeglądarce Safari.

## <a name="speech-sdk-121"></a>Zestaw Speech SDK 1.2.1

Jest to wersja tylko w języku JavaScript. Nie dodano żadnych funkcji. Wprowadzono następujące poprawki:

* Wygeneruj strumieniowo koniec strumienia. end, a nie na mowę. end.
* Usuń usterkę w pompie audio, która nie zaplanował następnego wysłania, jeśli bieżące wysyłanie nie powiodło się.
* Naprawianie ciągłego rozpoznawania przy użyciu tokenu uwierzytelniania.
* Poprawka usterki dla różnych aparatów rozpoznawania/punktów końcowych.
* Udoskonalenia dokumentacji.

## <a name="speech-sdk-120-2018-december-release"></a>1\.2.0 zestawu mowy SDK: 2018 — wydanie z grudnia

**Nowe funkcje**

* Python
  * Wersja beta obsługi języka Python (3,5 i nowszych) jest dostępna w tej wersji. Aby uzyskać więcej informacji, zobacz tutaj] (Szybki Start-python.md).
* JavaScript
  * Zestaw Speech SDK dla języka JavaScript został otwarty jako źródło. Kod źródłowy jest dostępny w serwisie [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Teraz obsługujemy Node. js. więcej informacji można znaleźć [tutaj](quickstart-js-node.md).
  * Ograniczenie długości sesji audio zostało usunięte, ponowne połączenie zostanie wykonane automatycznie pod pokryciem.
* Obiekt połączenia
  * Z poziomu aparatu rozpoznawania można uzyskać dostęp do obiektu połączenia. Ten obiekt umożliwia jawne zainicjowanie połączenia z usługą i subskrypcję w celu nawiązania połączenia i rozłączenia zdarzeń.
    (Ta funkcja nie jest jeszcze dostępna w języku JavaScript i Python).
* Obsługa Ubuntu 18,04.
* Android
  * Włączono obsługę funkcji preguard podczas generowania APK.

**Ulepszenia**

* Udoskonalenia wewnętrznego użycia wątku, Zmniejszanie liczby wątków, blokad i muteksów.
* Ulepszone raportowanie błędów/informacje. W kilku przypadkach komunikaty o błędach nie zostały jeszcze rozpropagowane.
* Zaktualizowano zależności programistyczne w języku JavaScript w celu korzystania z aktualnych modułów.

**Poprawki błędów**

* Naprawiono przecieki pamięci ze względu na niezgodność typów w RecognizeAsync.
* W niektórych przypadkach wyjątki były wyciekami.
* Naprawianie przecieku pamięci w argumentach zdarzeń tłumaczenia.
* Rozwiązano problem z blokowaniem po ponownym nawiązaniu połączenia w długotrwałych sesjach.
* Rozwiązano problem, który może prowadzić do braku końcowego wyniku dla nieudanych tłumaczeń.
* C#: Jeśli operacja asynchroniczna nie została oczekiwana w wątku głównym, możliwe, że aparat rozpoznawania mógłby zostać usunięty przed ukończeniem zadania asynchronicznego.
* Java: Rozwiązano problem powodujący awarię maszyny wirtualnej Java.
* Cel-C: Stałe mapowanie wyliczenia; RecognizedIntent został zwrócony zamiast RecognizingIntent.
* JavaScript: Ustaw domyślny format wyjściowy na "Simple" w SpeechConfig.
* JavaScript: Usuwanie niespójności między właściwościami obiektu konfiguracji w języku JavaScript i innych językach.

**Przykłady**

* Zaktualizowano i Naprawiono kilka próbek (na przykład głosy wyjściowe dla tłumaczenia itp.).
* Dodano przykłady dla środowiska Node. js w [przykładowym repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Zestaw Speech SDK 1.1.0

**Nowe funkcje**

* Obsługa systemu Android x86/x64.
* Obsługa serwera proxy: W obiekcie SpeechConfig można teraz wywołać funkcję, aby ustawić informacje o serwerze proxy (nazwa hosta, port, nazwa użytkownika i hasło). Ta funkcja nie jest jeszcze dostępne w systemie iOS.
* Kod błędu ulepszone i komunikatów. Jeśli rozpoznawania zwrócił błąd, to już ustawiona `Reason` (w przypadku anulowane) lub `CancellationDetails` (w wyniku rozpoznawania) do `Error`. Anulowano zdarzeń zawiera teraz dwa dodatkowe elementy członkowskie `ErrorCode` i `ErrorDetails`. Jeśli serwer zwrócił dodatkowe informacje o błędzie z zgłoszonego błędu, będzie teraz dostępna w nowych elementów członkowskich.

**Ulepszenia**

* Dodano dodatkowa weryfikacja w konfiguracji rozpoznawania i dodano dodatkowe komunikat.
* Ulepszona obsługa wieloletni wyciszenia w środku plik audio.
* Pakiet NuGet: dla projektów programu .NET Framework zapobiega kompilowanie za pomocą konfiguracji AnyCPU.

**Poprawki błędów**

* Naprawiono kilka wyjątków w aparatów rozpoznawania gestów. Ponadto wyjątki są przechwytywane i konwertowane na zdarzenie anulowane.
* Napraw przeciek pamięci w przystawce Zarządzanie właściwości.
* Naprawiono usterkę, w którym plik audio w wejściowych mogło powodować awarię aparat rozpoznawania.
* Usunięto usterkę, w którym zdarzenia można odebrane po wystąpieniu zdarzenia zatrzymanie sesji.
* Rozwiązano niektóre sytuacje wyścigu w wielowątkowości.
* Naprawiono systemu iOS problem ze zgodnością, co może spowodować awarię.
* Ulepszenia obsługi systemu Android mikrofonu.
* Usunięto usterkę, w którym rozpoznawania w języku JavaScript będzie ignorować języka rozpoznawania.
* Naprawiono usterkę uniemożliwiającą ustawienie EndpointId (w niektórych przypadkach) w języku JavaScript.
* Kolejność parametrów zmienione w AddIntent w języku JavaScript i dodano Brak podpisu AddIntent JavaScript.

**Przykłady**

* Dodano C++ i C# samplea ściąganie i wypychanie użycia strumienia w [przykładowego repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Zestaw Speech SDK 1.0.1

Ulepszenia niezawodności i poprawki:

* Naprawiono potencjalne błąd krytyczny z powodu sytuacji wyścigu w operacji Dispose rozpoznawania
* Naprawiono potencjalny błąd krytyczny w razie nie ustawiono właściwości.
* Dodano dodatkowe błędów i Sprawdzanie parametrów.
* Cel-C: Stały możliwy błąd krytyczny spowodowany zastąpieniem nazwy w NSString.
* Cel-C: Dostosowany widoczność interfejsu API
* JavaScript: Stałe dotyczące zdarzeń i ich ładunków.
* Udoskonalenia dokumentacji.

W naszym [przykładowego repozytorium](https://aka.ms/csspeech/samples), dodano nowe próbki dla języka JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services zestawu 1.0.0 mowy: 2018 — wydanie z września

**Nowe funkcje**

* Obsługa języka Objective-C w systemie iOS. Zapoznaj się z naszym [języka Objective-C Przewodnik Szybki Start dla systemu iOS](quickstart-objectivec-ios.md).
* Obsługa języka JavaScript w przeglądarce. Zapoznaj się z naszym [Szybki Start JavaScript](quickstart-js-browser.md).

**Fundamentalne zmiany**

* W tej wersji wprowadzono kilka istotnych zmian.
  Sprawdź [Tę stronę](https://aka.ms/csspeech/breakingchanges_1_0_0) , aby uzyskać szczegółowe informacje.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services zestawu 0.6.0 mowy: 2018 — wydanie z sierpnia

**Nowe funkcje**

* Aplikacje platformy UWP skompilowanych przy użyciu zestawu SDK rozpoznawania mowy, teraz można przekazywać Windows App certyfikacji Kit (WACK).
  Zapoznaj się z [Szybki Start platformy uniwersalnej systemu Windows](quickstart-csharp-uwp.md).
* Obsługa .NET Standard 2.0 w systemie Linux (Ubuntu 16.04 x 64).
* Głowonogów Obsługa języka Java 8 w systemie Windows (64-bitowy) i Linux (Ubuntu 16,04 x64).
  Zapoznaj się z [Szybki Start Java Runtime Environment](quickstart-java-jre.md).

**Zmiana funkcjonalności**

* Uwidocznić dodatkowe szczegółowe informacje na temat błędów połączenia.

**Fundamentalne zmiany**

* W języku Java (Android) `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` funkcja nie wymaga już parametr ścieżki. Teraz ścieżki jest wykrywany automatycznie na wszystkich obsługiwanych platformach.
* Get akcesora właściwości `EndpointUrl` w środowiskach Java i C# został usunięty.

**Poprawki błędów**

* W języku Java wynik rozpoznawania translacji dla audio syntezy zaimplementowano teraz.
* Naprawiono usterkę, która może spowodować, że nieaktywny wątków i zwiększonej liczby gniazd nieużywane i open.
* Rozwiązano problem, gdzie rozpoznawania długotrwałych można zakończyć w trakcie transmisji.
* Stała wyścigu rozpoznawania zamknięcia.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services zestawu 0.5.0 mowy: 2018 — wydanie z lipca

**Nowe funkcje**

* Obsługa platformy Android (API 23: System Android 6,0 Marshmallow lub nowszy). Zapoznaj się z [Szybki Start dla systemu Android](quickstart-java-android.md).
* Obsługa .NET Standard 2.0 na Windows. Zapoznaj się z [Szybki Start platformy .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Głowonogów Obsługa platformy UWP w systemie Windows (wersja 1709 lub nowsza).
  * Zapoznaj się z [Szybki Start platformy uniwersalnej systemu Windows](quickstart-csharp-uwp.md).
  * Uwaga: Aplikacje platformy UWP utworzone przy użyciu zestawu Speech SDK nie przechodzą jeszcze zestawu certyfikacji aplikacji systemu Windows (WACK).
* Obsługuje rozpoznawanie długotrwałych z automatycznego ponownego połączenia.

**Zmiany funkcjonalne**

* `StartContinuousRecognitionAsync()` obsługuje rozpoznawanie długoterminowych.
* Wynik rozpoznawania zawiera więcej pól. Są one przesunięcie od początku audio i czas trwania (zarówno w impulsach) rozpoznany tekst i dodatkowe wartości, które reprezentują rozpoznawania stanu, na przykład `InitialSilenceTimeout` i `InitialBabbleTimeout`.
* Obsługa tworzenia wystąpienia fabryki AuthorizationToken.

**Fundamentalne zmiany**

* Zdarzenia rozpoznawania: Typ zdarzenia nomatch został scalony w zdarzeniu błędu.
* SpeechOutputFormat w języku C# została zmieniona na OutputFormat pozostanie wyrównane przy użyciu języka C++.
* Zwracany typ niektóre metody `AudioInputStream` interfejsu nieznacznie zmienione:
   * W języku Java `read` metoda teraz zwraca `long` zamiast `int`.
   * W języku C# `Read` metoda teraz zwraca `uint` zamiast `int`.
   * W języku C++ `Read` i `GetFormat` teraz zwrotu metody `size_t` zamiast `int`.
* C++: Wystąpienia strumieni wejścia audio mogą teraz być przesyłane tylko jako `shared_ptr`.

**Poprawki błędów**

* Naprawiono niepoprawne wartości zwracanych w wyniku podczas `RecognizeAsync()` upłynie limit czasu.
* Usunięto zależność od media foundation bibliotek na Windows. Zestaw SDK używa teraz podstawowe interfejsy API z Audio.
* Poprawka do dokumentacji: Dodano stronę [regiony](regions.md) , aby opisać Obsługiwane regiony.

**Znany problem**

* Zestaw SDK rozpoznawania mowy dla systemu Android nie raportują wyniki synteza mowy do tłumaczenia. Ten problem zostanie rozwiązany w następnej wersji.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services zestawu 0.4.0 mowy: 2018 — wydanie z czerwca

**Zmiany funkcjonalne**

- AudioInputStream

  Aparat rozpoznawania teraz mogą wykorzystywać strumienia jako źródła audio. Aby uzyskać więcej informacji, zobacz powiązane [poradnik](how-to-use-audio-input-streams.md).

- Format szczegółowych danych wyjściowych

  Po utworzeniu `SpeechRecognizer`, możesz poprosić o `Detailed` lub `Simple` format danych wyjściowych. `DetailedSpeechRecognitionResult` Zawiera współczynnik ufności, rozpoznany tekst, nieprzetworzonej postaci leksykalne, znormalizowana postać i znormalizowana postać z maskowanego wulgaryzmów.

**Zmiana powodująca niezgodność**

- Zmieniono na `SpeechRecognitionResult.Text` z `SpeechRecognitionResult.RecognizedText` w języku C#.

**Poprawki błędów**

- Rozwiązano problem z wywołania zwrotnego w warstwie USP podczas zamykania.

- Jeśli aparat rozpoznawania używany plik audio w wejściowych, jego został utrzymuje dojście do pliku dłużej niż jest to konieczne.

- Usunięte kilka zakleszczenia między "pompy komunikatów" i aparat rozpoznawania.

- Ogień `NoMatch` powodować podczas odpowiedź z usługi Przekroczono limit czasu.

- Biblioteki foundation multimediów na Windows są ładowane z opóźnieniem. Ta biblioteka jest wymagana do mikrofonu tylko dane wejściowe.

- Szybkość przekazywania danych audio jest ograniczone do informacje o dwa razy oryginalnego szybkości audio.

- W Windows zestawy języka C# .NET teraz są silnej nazwy.

- Poprawienie dokumentacji: `Region` informacja jest wymagana do utworzenia aparat rozpoznawania.

Więcej przykładów zostały dodane i są stale aktualizowane. Aby uzyskać najnowszy zestaw przykładów, zobacz [repozytorium GitHub samples zestaw SDK rozpoznawania mowy](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services zestawu 0.2.12733 mowy: 2018 — może wydać

Ta wersja jest pierwsza wersja publiczna wersja zapoznawcza zestawu SDK mowy usługi Cognitive Services.
