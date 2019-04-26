---
title: Informacje o wersji — usługi mowy
titlesuffix: Azure Cognitive Services
description: Zobacz uruchomionej dziennik wydania funkcji, ulepszeń, poprawek błędów i znane problemy dla usług mowy platformy Azure.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 4/5/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: e40d9a2ac0e95ae72aed927f8a527eb1e092079d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480473"
---
# <a name="release-notes"></a>Informacje o wersji

## <a name="speech-sdk-141"></a>Zestaw SDK 1.4.1 mowy

To jest wersja tylko do języka JavaScript. Żadne funkcje nie zostały dodane. Wprowadzono następujące poprawki:

* Zapobiega webpack ładowania https serwera proxy agenta.

## <a name="speech-sdk-140-2019-april-release"></a>Zestaw SDK 1.4.0 mowy: Wersja 2019 kwietnia

**Nowe funkcje** 

* Zestaw SDK obsługuje teraz usługę zamiany tekstu na mowę w wersji beta. Jest on obsługiwany w Windows i Linux pulpitu z C++ i C#. Aby uzyskać więcej informacji o wyboru [zamiany tekstu na mowę Przegląd](text-to-speech.md#get-started-with-text-to-speech).
* Zestaw SDK obsługuje teraz MP3 oraz dziele/Ogg pliki audio jako pliki wejściowe strumienia. Ta funkcja jest dostępna tylko w systemie Linux z C++ i C# i jest obecnie w wersji beta (więcej szczegółów [tutaj](how-to-use-compressed-audio-input-streams.md)).
* Zestaw SDK rozpoznawania mowy dla języka Java, .NET core, C++ i języka Objective-C weszły do obsługi systemu macOS. Obsługa języka Objective-C, dla systemu macOS jest obecnie w wersji beta.
* iOS: Zestaw SDK rozpoznawania mowy dla systemu iOS (Objective-C) teraz zostało również opublikowane jako CocoaPod.
* JavaScript: Pomoc techniczna dla innych niż domyślne mikrofon jako urządzenia wejściowego.
* JavaScript: Obsługa serwera proxy dla środowiska Node.js.

**Przykłady**

* Dodano przykłady dotyczące używania zestawu SDK rozpoznawania mowy przy użyciu języka C++ i za pomocą języka Objective-C w systemie macOS.
* Dodano przykłady ukazujące użycia zamiany tekstu na mowę usługi.

**Ulepszenia / zmiany**

* Python: Dodatkowe właściwości wyniki rozpoznawania są teraz udostępniane za pośrednictwem `properties` właściwości.
* Dodatkowe obsługę programowania i debugowania można przekierować SDK rejestrowania i informacje diagnostyczne do pliku dziennika (więcej szczegółów [tutaj](how-to-use-logging.md)).
* JavaScript: Zwiększ wydajność przetwarzania audio.

**Poprawki błędów**

* Mac/iOS: Naprawiono usterkę, która doprowadziła do długim czasie oczekiwania, gdy nie można nawiązać połączenia z usługą mowy.
* Python: poprawa obsługi błędów dla argumentów wywołania zwrotne języka Python.
* JavaScript: Naprawiono nieodpowiednim stanie raportowania rozpoznawania mowy upłynął RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Zestaw SDK 1.3.1 mowy: Odświeżanie lutego 2019 r

Jest to wersja poprawki i na komputerach z zestawu SDK natywnego/zarządzanego. Nie ma wpływ na JavaScript wersję zestawu SDK.

**Naprawienie usterki**

* Naprawiono przeciek pamięci, korzystając z mikrofonu w danych wejściowych. Nie ma wpływu na podstawie Stream lub plik wejściowy.

## <a name="speech-sdk-130-2019-february-release"></a>Zestaw SDK 1.3.0 mowy: Wersji z lutego 2019 r

**Nowe funkcje**

* Zestaw SDK rozpoznawania mowy obsługuje wyboru wejściowego mikrofonu za pośrednictwem klasy AudioConfig. Umożliwia przesyłanie strumieniowe audio danych do usług przetwarzania mowy z mikrofonu innych niż domyślne. Aby uzyskać więcej informacji, zobacz temat opisujący dokumentacji [wybór urządzenia wejściowe audio](how-to-select-audio-input-devices.md). To nie jest jeszcze dostępna z poziomu języka JavaScript.
* Zestaw SDK rozpoznawania mowy obsługuje teraz Unity w wersji beta. Prześlij opinię za pośrednictwem sekcji problem [repozytorium przykładów GitHub](https://aka.ms/csspeech/samples). Ta wersja obsługuje platformy Unity na Windows x86 i x64 (pulpitu lub aplikacji Universal Windows Platform) i Android (ARM32/64 x86). Więcej informacji znajduje się w naszym [Szybki Start Unity](quickstart-csharp-unity.md).
* Plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dostarczane w poprzednich wersjach) nie jest już potrzebny. Funkcje jest teraz zintegrowany zestaw SDK core.


**Przykłady**

Następujące Nowa zawartość jest dostępna w naszym [przykładowego repozytorium](https://aka.ms/csspeech/samples):

* Więcej przykładów dla AudioConfig.FromMicrophoneInput.
* Dodatkowe przykłady w języku Python rozpoznawanie intencji i tłumaczenia.
* Dodatkowe przykłady dotyczące używania obiektu połączenia w systemie iOS.
* Dodatkowe przykłady języka Java do tłumaczenia z danymi wyjściowymi audio.
* Nowy przykład użytkowania [interfejsu API REST transkrypcji partii](batch-transcription.md).

**Ulepszenia / zmiany**

* Python
  * Ulepszone parametr weryfikacji i komunikaty o błędach w SpeechConfig.
  * Dodanie obsługi dla obiektu połączenia.
  * Obsługa 32-bity, Python (x86) na Windows.
  * Zestaw SDK rozpoznawania mowy w języku Python jest poza beta.
* iOS
  * Zestaw SDK jest obecnie wbudowany względem zestawu SDK w wersji 12.1 dla systemu iOS.
  * Zestaw SDK obsługuje teraz system iOS w wersji 9.2 lub nowszej.
  * Poprawić dokumentację referencyjną i naprawić kilka nazw właściwości.
* JavaScript
  * Dodanie obsługi dla obiektu połączenia.
  * Dodaj pliki definicji typów dla wbudowanej języka JavaScript
  * Początkowej obsługi i implementację wskazówek frazę.
  * Zwraca kolekcję właściwości z usługą JSON w celu rozpoznawania
* Biblioteki dll Windows zawierają teraz zasobu wersji.
* Jeśli tworzysz aparat rozpoznawania `FromEndpoint` parametrów można dodać bezpośrednio do adresu URL punktu końcowego. Za pomocą `FromEndpoint` nie można skonfigurować aparat rozpoznawania przy użyciu właściwości konfiguracji standardowej.

**Poprawki błędów**

* Proxy puste nazwy użytkownika i hasła serwera proxy nie były obsługiwane poprawnie. W tej wersji Jeśli nazwa użytkownika serwera proxy i hasło serwera proxy jest ustawiona na pustym ciągiem, ich będzie nie można przesłać podczas nawiązywania połączenia z serwerem proxy.
* Identyfikator sesji użytkownika utworzone przez zestaw SDK nie były zawsze rzeczywiście losowy w przypadku niektórych języków&nbsp;/ środowisk. Dodany generator losowych inicjowania, aby rozwiązać ten problem.
* Poprawa obsługi token autoryzacji. Jeśli chcesz użyć tokenu autoryzacji, określ w SpeechConfig i pozostaw puste, klucz subskrypcji. Następnie utwórz aparat rozpoznawania w zwykły sposób.
* W niektórych przypadkach połączenia obiektu nie został wydany poprawnie. Ten problem został rozwiązany.
* Naprawiono próbki kodu JavaScript do obsługi sygnału audio dla syntezy tłumaczenia również w przeglądarce Safari.

## <a name="speech-sdk-121"></a>Zestaw SDK 1.2.1 mowy

To jest wersja tylko do języka JavaScript. Żadne funkcje nie zostały dodane. Wprowadzono następujące poprawki:

* Wyzwolenie koniec strumienia turn.end, a nie speech.end.
* Naprawiano błąd w przepompowni audio, harmonogram nie obok wysłał Jeśli bieżące wysyłanie nie powiodło się.
* Napraw rozpoznawania ciągłe przy użyciu tokenu uwierzytelniania.
* Naprawienie usterki dla różnych rozpoznawania / punktów końcowych.
* Udoskonalenia dokumentacji.

## <a name="speech-sdk-120-2018-december-release"></a>Zestaw SDK 1.2.0 mowy: Wersja grudnia 2018 r.

**Nowe funkcje**

* Python
  * Wersji Beta programu obsługi języka Python (w wersji 3.5 i nowsze) jest dostępna w tej wersji. Aby uzyskać więcej informacji zobacz here](quickstart-python.md).
* JavaScript
  * Zestaw SDK rozpoznawania mowy dla języka JavaScript została open source. Kod źródłowy jest dostępny na [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Obsługujemy teraz Node.js, więcej informacji można znaleźć [tutaj](quickstart-js-node.md).
  * Została usunięta ograniczenia długości sesji audio, ponowne łączenie nastąpi automatycznie w ramach pokrycia.
* Obiekt połączenia
  * Z aparatu rozpoznawania można uzyskać dostęp do obiektu połączenia. Ten obiekt umożliwia jawnie zainicjować połączenia z usługą i subskrypcji do nawiązywania połączeń i zdarzenia rozłączenia.
    (To nie jest jeszcze dostępny w języku JavaScript i Python).
* Obsługa systemu Ubuntu 18.04.
* Android
  * Włączone narzędzia ProGuard obsługuje podczas generowania pliku APK.

**Ulepszenia**

* Ulepszenia użycie wewnętrznego wątku, zmniejszając liczbę wątków, blokad, muteksów.
* Ulepszone raportowanie błędów / informacje. W kilku przypadkach aż się nie zostały rozpropagowane komunikaty o błędach.
* Zaktualizowano zależności programowania w języku JavaScript, używać modułów aktualne.

**Poprawki błędów**

* Naprawiono przecieki pamięci z powodu niezgodności typu RecognizeAsync.
* W niektórych przypadkach zostały wyciek wyjątków.
* Naprawianie przeciek pamięci w argumentach zdarzeń tłumaczenia.
* Naprawiono blokowania problemu na ponowne nawiązanie połączenia w długich uruchamianie sesji.
* Rozwiązano problem, który może prowadzić do brakujących wynik końcowy w poszukiwaniu tłumaczeń nie powiodło się.
* C#: Jeśli w operacji asynchronicznej nie był oczekiwany w wątku głównym, było możliwe, aparat rozpoznawania może zostać zlikwidowany przed ukończeniem zadania asynchronicznego.
* Java: Rozwiązano problem, co spowoduje awarię maszyny wirtualnej Java.
* Cel — C: Mapowanie stałym enum; RecognizedIntent została zwrócona zamiast RecognizingIntent.
* JavaScript: Ustaw domyślnego formatu danych wyjściowych do prostego, w SpeechConfig.
* JavaScript: Usuwanie niespójność między właściwościami obiektu konfiguracji w języku JavaScript i innych języków.

**Przykłady**

* Zaktualizowane i Naprawiono kilka przykładów (na przykład danych wyjściowych głosów tłumaczenia itp.).
* Dodano przykłady dla platformy Node.js w [przykładowego repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Zestaw SDK 1.1.0 mowy

**Nowe funkcje**

* Obsługa systemu Android x86/x64.
* Obsługa serwera proxy: W obiekcie SpeechConfig teraz można wywołać funkcję, aby ustawić informacje o serwerze proxy (nazwa hosta, portu, nazwy użytkownika i hasło). Ta funkcja nie jest jeszcze dostępne w systemie iOS.
* Kod błędu ulepszone i komunikatów. Jeśli rozpoznawania zwrócił błąd, to już ustawiona `Reason` (w przypadku anulowane) lub `CancellationDetails` (w wyniku rozpoznawania) do `Error`. Anulowano zdarzeń zawiera teraz dwa dodatkowe elementy członkowskie `ErrorCode` i `ErrorDetails`. Jeśli serwer zwrócił dodatkowe informacje o błędzie z zgłoszonego błędu, będzie teraz dostępna w nowych elementów członkowskich.

**Ulepszenia**

* Dodano dodatkowa weryfikacja w konfiguracji rozpoznawania i dodano dodatkowe komunikat.
* Ulepszona obsługa wieloletni wyciszenia w środku plik audio.
* Pakiet NuGet: dla projektów programu .NET Framework zapobiega kompilowanie za pomocą konfiguracji AnyCPU.

**Poprawki błędów**

* Naprawiono kilka wyjątków w aparatów rozpoznawania gestów. Ponadto wyjątki są przechwytywane i konwertowane na zdarzenia anulowane.
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

## <a name="speech-sdk-101"></a>Zestaw SDK 1.0.1 mowy

Ulepszenia niezawodności i poprawki:

* Naprawiono potencjalne błąd krytyczny z powodu sytuacji wyścigu w operacji Dispose rozpoznawania
* Naprawiono potencjalny błąd krytyczny w razie nie ustawiono właściwości.
* Dodano dodatkowe błędów i Sprawdzanie parametrów.
* Cel — C: Stały możliwy błąd krytyczny spowodowane przez nazwę zastępowanie w NSString.
* Cel — C: Skorygowane widoczność interfejsu API
* JavaScript: Naprawiono dotyczące zdarzenia i ich ładunków.
* Udoskonalenia dokumentacji.

W naszym [przykładowego repozytorium](https://aka.ms/csspeech/samples), dodano nowe próbki dla języka JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Mowy usług cognitive Services SDK 1.0.0: Wydanie z września 2018 r.

**Nowe funkcje**

* Obsługa języka Objective-C w systemie iOS. Zapoznaj się z naszym [języka Objective-C Przewodnik Szybki Start dla systemu iOS](quickstart-objectivec-ios.md).
* Obsługa języka JavaScript w przeglądarce. Zapoznaj się z naszym [Szybki Start JavaScript](quickstart-js-browser.md).

**Fundamentalne zmiany**

* W tej wersji wprowadzono szereg istotnych zmian.
  Sprawdź, czy [na tej stronie](https://aka.ms/csspeech/breakingchanges_1_0_0) Aby uzyskać szczegółowe informacje.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Mowy usług cognitive Services SDK 0.6.0: Wersji z sierpnia 2018 r.

**Nowe funkcje**

* Aplikacje platformy UWP skompilowanych przy użyciu zestawu SDK rozpoznawania mowy, teraz można przekazywać Windows App certyfikacji Kit (WACK).
  Zapoznaj się z [Szybki Start platformy uniwersalnej systemu Windows](quickstart-csharp-uwp.md).
* Obsługa .NET Standard 2.0 w systemie Linux (Ubuntu 16.04 x 64).
* Eksperymentalne: Obsługuje Java 8, Windows (wersja 64-bitowa) i systemu Linux (Ubuntu 16.04 x 64).
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

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Mowy usług cognitive Services SDK wersję 0.5.0: Wersja z lipca 2018 r.

**Nowe funkcje**

* Obsługa platformy systemu Android (interfejsu API 23: System android Marshmallow 6.0 lub nowszej). Zapoznaj się z [Szybki Start dla systemu Android](quickstart-java-android.md).
* Obsługa .NET Standard 2.0 na Windows. Zapoznaj się z [Szybki Start platformy .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Eksperymentalne: Obsługuje platformy uniwersalnej systemu Windows na Windows (w wersji 1709 lub nowszej).
  * Zapoznaj się z [Szybki Start platformy uniwersalnej systemu Windows](quickstart-csharp-uwp.md).
  * Uwaga: Aplikacje platformy UWP skompilowanych przy użyciu zestawu SDK rozpoznawania mowy nie przekazuj jeszcze Windows App certyfikacji Kit (WACK).
* Obsługuje rozpoznawanie długotrwałych z automatycznego ponownego połączenia.

**Zmiany funkcjonalne**

* `StartContinuousRecognitionAsync()` obsługuje rozpoznawanie długoterminowych.
* Wynik rozpoznawania zawiera więcej pól. Są one przesunięcie od początku audio i czas trwania (zarówno w impulsach) rozpoznany tekst i dodatkowe wartości, które reprezentują rozpoznawania stanu, na przykład `InitialSilenceTimeout` i `InitialBabbleTimeout`.
* Obsługa tworzenia wystąpienia fabryki AuthorizationToken.

**Fundamentalne zmiany**

* Rozpoznawanie zdarzenia: Typ zdarzenia NoMatch zostały scalone z zdarzenie błędu.
* SpeechOutputFormat w języku C# została zmieniona na OutputFormat pozostanie wyrównane przy użyciu języka C++.
* Zwracany typ niektóre metody `AudioInputStream` interfejsu nieznacznie zmienione:
   * W języku Java `read` metoda teraz zwraca `long` zamiast `int`.
   * W języku C# `Read` metoda teraz zwraca `uint` zamiast `int`.
   * W języku C++ `Read` i `GetFormat` teraz zwrotu metody `size_t` zamiast `int`.
* C++: Wystąpienia elementu wejściowego strumieni audio teraz może być przekazywany tylko jako `shared_ptr`.

**Poprawki błędów**

* Naprawiono niepoprawne wartości zwracanych w wyniku podczas `RecognizeAsync()` upłynie limit czasu.
* Usunięto zależność od media foundation bibliotek na Windows. Zestaw SDK używa teraz podstawowe interfejsy API z Audio.
* Poprawienie dokumentacji: Dodano [regionów](regions.md) strony do opisania obsługiwanych regionów.

**Znany problem**

* Zestaw SDK rozpoznawania mowy dla systemu Android nie raportują wyniki synteza mowy do tłumaczenia. Ten problem zostanie rozwiązany w następnej wersji.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Mowy usług cognitive Services SDK 0.4.0: Wersja z czerwca 2018 r.

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Mowy usług cognitive Services SDK 0.2.12733: Wersja maja 2018 r.

Ta wersja jest pierwsza wersja publiczna wersja zapoznawcza zestawu SDK mowy usługi Cognitive Services.
