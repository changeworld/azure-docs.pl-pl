---
title: Dokumentacja zestawu SDK usługi Speech Service
titlesuffix: Azure Cognitive Services
description: Informacje o wersji — co zmieniło się w najnowszych wersjach
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: e2c676932541866df081f6db54fa0b1ec4da7088
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218597"
---
# <a name="release-notes"></a>Informacje o wersji

## <a name="speech-service-sdk-110"></a>Usługa rozpoznawania mowy SDK 1.1.0

**Nowe funkcje**

* Obsługa systemu Android x86/x64.
* Obsługa serwera proxy: W obiekcie SpeechConfig można teraz wywołać funkcję, aby ustawić informacje o serwerze proxy (nazwa hosta, portu, nazwy użytkownika i hasło). Ta funkcja nie jest jeszcze dostępne w systemie iOS.
* Kod błędu ulepszone i komunikatów. Jeśli rozpoznawania zwrócił błąd, to już ustawiona `Reason` (w przypadku anulowane) lub `CancellationDetails` (w wyniku rozpoznawania) do `Error`. Anulowano zdarzeń zawiera teraz dwa dodatkowe elementy członkowskie `ErrorCode` i `ErrorDetails`. Jeśli serwer zwrócił dodatkowe informacje o błędzie z zgłoszonego błędu, będzie teraz dostępna w nowych elementów członkowskich.

**Ulepszenia**

* Dodano dodatkowa weryfikacja w konfiguracji rozpoznawania i dodano dodatkowe komunikat.
* Ulepszona obsługa wieloletni wyciszenia w środku plik audio.
* Pakiet NuGet: dla platformy .NET Framework projektów uniemożliwić kompilowanie za pomocą konfiguracji AnyCPU.

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

* Dodano przykładu w języku C++ dla ściąganie i wypychanie strumienia użycia w naszym [przykładowego repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-service-sdk-101"></a>Usługa rozpoznawania mowy 1.0.1 zestawu SDK

Ulepszenia niezawodności i poprawki:

* Naprawiono potencjalne błąd krytyczny z powodu sytuacji wyścigu w operacji Dispose rozpoznawania
* Naprawiono potencjalny błąd krytyczny w razie nie ustawiono właściwości.
* Dodano dodatkowe błędów i Sprawdzanie parametrów.
* Cel C: stały możliwy błąd krytyczny spowodowane przez nazwę zastępowanie w NSString.
* Cel C: dostosować widoczność interfejsu API
* JavaScript: Naprawiono dotyczące zdarzenia i ich ładunków.
* Udoskonalenia dokumentacji.

W naszym [przykładowego repozytorium](https://aka.ms/csspeech/samples), dodano nowe próbki dla języka JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Services cognitive mowy wersji zestawu SDK 1.0.0: września 2018 r.

**Nowe funkcje**

* Obsługa języka Objective-C w systemie iOS. Zapoznaj się z naszym [języka Objective-C Przewodnik Szybki Start dla systemu iOS](quickstart-objectivec-ios.md).
* Obsługa języka JavaScript w przeglądarce. Zapoznaj się z naszym [Szybki Start JavaScript](quickstart-js-browser.md).

**Fundamentalne zmiany**

* W tej wersji wprowadzono szereg istotnych zmian.
  Sprawdź, czy [na tej stronie](https://aka.ms/csspeech/breakingchanges_1_0_0) Aby uzyskać szczegółowe informacje.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Services cognitive mowy wersji zestawu SDK 0.6.0: sierpnia 2018 r.

**Nowe funkcje**

* Aplikacje platformy UWP skompilowanych przy użyciu zestawu SDK rozpoznawania mowy, teraz można przekazywać Windows App certyfikacji Kit (WACK).
  Zapoznaj się z [Szybki Start platformy uniwersalnej systemu Windows](quickstart-csharp-uwp.md).
* Obsługa .NET Standard 2.0 w systemie Linux (Ubuntu 16.04 x 64).
* Eksperymentalne: Obsługuje Java 8 Windows (wersja 64-bitowa) i systemu Linux (Ubuntu 16.04 x 64).
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

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Services cognitive mowy wersji zestawu SDK wersję 0.5.0: lipca 2018 r.

**Nowe funkcje**

* Obsługa platformy systemu Android (interfejsu API 23: system Android Marshmallow 6.0 lub nowszej). Zapoznaj się z [Szybki Start dla systemu Android](quickstart-java-android.md).
* Obsługa .NET Standard 2.0 na Windows. Zapoznaj się z [Szybki Start platformy .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Eksperymentalne: Obsługa platformy uniwersalnej systemu Windows na Windows (w wersji 1709 lub nowszej).
  * Zapoznaj się z [Szybki Start platformy uniwersalnej systemu Windows](quickstart-csharp-uwp.md).
  * Uwaga: Aplikacje platformy UWP skompilowanych przy użyciu zestawu SDK rozpoznawania mowy nie są jeszcze przekazywane Windows App certyfikacji Kit (WACK).
* Obsługuje rozpoznawanie długotrwałych z automatycznego ponownego połączenia.

**Zmiany funkcjonalne**

* `StartContinuousRecognitionAsync()` obsługuje rozpoznawanie długoterminowych.
* Wynik rozpoznawania zawiera więcej pól. Są one przesunięcie od początku audio i czas trwania (zarówno w impulsach) rozpoznany tekst i dodatkowe wartości, które reprezentują rozpoznawania stanu, na przykład `InitialSilenceTimeout` i `InitialBabbleTimeout`.
* Obsługa tworzenia wystąpienia fabryki AuthorizationToken.

**Fundamentalne zmiany**

* Zdarzenia rozpoznawania: typ zdarzenia NoMatch zostały scalone z zdarzenie błędu.
* SpeechOutputFormat w języku C# została zmieniona na OutputFormat pozostanie wyrównane przy użyciu języka C++.
* Zwracany typ niektóre metody `AudioInputStream` interfejsu nieznacznie zmienione:
   * W języku Java `read` metoda teraz zwraca `long` zamiast `int`.
   * W języku C# `Read` metoda teraz zwraca `uint` zamiast `int`.
   * W języku C++ `Read` i `GetFormat` teraz zwrotu metody `size_t` zamiast `int`.
* C++: Wystąpienia strumieni dźwiękowych w danych wejściowych teraz może być przekazywany tylko jako `shared_ptr`.

**Poprawki błędów**

* Naprawiono niepoprawne wartości zwracanych w wyniku podczas `RecognizeAsync()` upłynie limit czasu.
* Usunięto zależność od media foundation bibliotek na Windows. Zestaw SDK używa teraz podstawowe interfejsy API z Audio.
* Poprawienie dokumentacji: dodano [regionów](regions.md) strony do opisania obsługiwanych regionów.

**Znany problem**

* Zestaw SDK rozpoznawania mowy dla systemu Android nie raportują wyniki synteza mowy do tłumaczenia. Ten problem zostanie rozwiązany w następnej wersji.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Services cognitive mowy wersji zestawu SDK 0.4.0: czerwca 2018 r.

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Services cognitive mowy wersji zestawu SDK 0.2.12733: maj 2018 r.

Ta wersja jest pierwsza wersja publiczna wersja zapoznawcza zestawu SDK mowy usługi Cognitive Services.
