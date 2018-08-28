---
title: Dokumentacja zestawu SDK usługi rozpoznawania mowy usług cognitive Services | Dokumentacja firmy Microsoft
description: Informacje o wersji — co zmieniło się w najnowszych wersjach
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 08/16/2018
ms.author: wolfma
ms.openlocfilehash: bbf3c5930de2ec6c709b6b527ae3eac107382420
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047803"
---
# <a name="release-notes"></a>Informacje o wersji

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Services cognitive mowy wersji zestawu SDK 0.6.0: sierpnia 2018 r.

**Nowe funkcje**

* Aplikacje platformy UWP skompilowanych przy użyciu zestawu SDK rozpoznawania mowy, można teraz przekazywać Windows App certyfikacji Kit (WACK).
  Zapoznaj się z naszym [Szybki Start platformy uniwersalnej systemu Windows](quickstart-csharp-uwp.md).
* Obsługa .NET Standard 2.0 w systemie Linux (Ubuntu 16.04 x 64).
* Eksperymentalne: Obsługuje Java 8 Windows (wersja 64-bitowa) i systemu Linux (Ubuntu 16.04 x 64).
  Zapoznaj się z [Szybki Start środowiska wykonawczego języka Java](quickstart-java-jre.md)

**Zmiany funkcjonalne**

* Udostępnianie dodatkowe szczegółowe informacje na temat błędów połączenia.

**Fundamentalne zmiany**

* W języku Java (Android) `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` funkcja nie wymaga już parametr ścieżki. Ścieżka teraz jest wykrywany automatycznie na wszystkich obsługiwanych platformach.
* Get akcesora właściwości `EndpointUrl` w środowiskach Java i C# został usunięty.

**Poprawki błędów**

* W języku Java wynik rozpoznawania translacji dla syntezy audio jest teraz implementowane.
* Naprawiono usterkę, która może spowodować, że nieaktywny wątków i zwiększonej liczby gniazd nieużywane i Otwórz.
* Rozwiązano problem, gdzie długotrwałe rozpoznawania można zakończyć w środku transmisji.
* Stała wyścigu rozpoznawania zamknięcia.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Services cognitive mowy wersji zestawu SDK wersję 0.5.0: lipca 2018 r.

**Nowe funkcje**

* Obsługa platformy systemu Android (interfejsu API 23: system Android Marshmallow 6.0 lub nowszej).
  Zapoznaj się z [Szybki Start dla systemu Android](quickstart-java-android.md).
* Obsługa .NET Standard 2.0 na Windows.
  Zapoznaj się z [Szybki Start platformy .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Eksperymentalne: Obsługa platformy uniwersalnej systemu Windows na Windows (w wersji 1709 lub nowszej)
  * Zapoznaj się z naszym [Szybki Start platformy uniwersalnej systemu Windows](quickstart-csharp-uwp.md).
  * Uwaga: Aplikacje platformy UWP skompilowanych przy użyciu zestawu SDK rozpoznawania mowy nie są jeszcze przekazywane Windows App certyfikacji Kit (WACK).
* Obsługa długotrwałych rozpoznawanie przy użyciu automatycznego ponownego połączenia.

**Zmiany funkcjonalne**

* `StartContinuousRecognitionAsync()` Obsługa długotrwałych rozpoznawania
* Wynik rozpoznawania zawiera więcej pól: Przesunięcie od początku audio i czas trwania (zarówno w impulsach) rozpoznany tekst dodatkowe wartości reprezentujący stan rozpoznawania, np. `InitialSilenceTimeout`, `InitialBabbleTimeout`.
* Obsługa tworzenia wystąpienia fabryki AuthorizationToken.

**Fundamentalne zmiany**

* Zdarzenia rozpoznawania: NoMatch typ zdarzenia zostanie scalona w zdarzeniu błędu.
* SpeechOutputFormat w języku C# jest zmieniana na OutputFormat zapewnienie wyrównane przy użyciu języka C++.
* Zwracany typ niektóre metody `AudioInputStream` interfejsu nieznacznie zmienione:
   * W języku Java `read` metoda teraz zwraca `long` zamiast `int`.
   * W języku C# `Read` metoda teraz zwraca `uint` zamiast `int`.
   * W języku C++ `Read` i `GetFormat` teraz zwrotu metody `size_t` zamiast `int`.
* C++: wystąpienia strumienie wejściowe audio mogą teraz być przekazywane jako `shared_ptr`.

**Poprawki błędów**

* Naprawiono niepoprawne wartości zwracanych w wyniku podczas `RecognizeAsync()` upłynie limit czasu.
* Zależność od media foundation bibliotek na Windows zostanie usunięta. Zestaw SDK używa teraz podstawowe interfejsy API z Audio.
* Poprawienie dokumentacji: dodano [regionów](regions.md) strony do opisywania, czym są obsługiwane regiony.

**Znane problemy**

* Zestaw SDK rozpoznawania mowy dla systemu Android nie raportuje wyniki synteza mowy do tłumaczenia.
  Ten problem zostanie rozwiązany w następnej wersji.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Services cognitive mowy wersji zestawu SDK 0.4.0: czerwca 2018 r.

**Zmiany funkcjonalne**

- AudioInputStream

  Aparat rozpoznawania mogą teraz wykorzystywać strumienia jako źródła audio. Aby uzyskać szczegółowe informacje, zobacz powiązane [poradnik](how-to-use-audio-input-streams.md).

- Format szczegółowych danych wyjściowych

  Podczas tworzenia `SpeechRecognizer`, możesz poprosić o `Detailed` lub `Simple` format danych wyjściowych. `DetailedSpeechRecognitionResult` Zawiera współczynnik ufności, rozpoznany tekst, nieprzetworzonej postaci leksykalne, znormalizowana postać i znormalizowana postać z maskowanego wulgaryzmów.

**Zmiana powodująca niezgodność**

- Zmień `SpeechRecognitionResult.Text` z `SpeechRecognitionResult.RecognizedText` w języku C#.

**Poprawki błędów**

- Rozwiąż problem wywołania zwrotnego w warstwie USP podczas zamykania.

- Jeśli aparat rozpoznawania używany plik audio w wejściowych, jego został utrzymuje dojście do pliku dłużej niż jest to konieczne.

- Usunięte kilka zakleszczenia między "pompy komunikatów" i rozpoznawania.

- Ogień `NoMatch` powodować podczas odpowiedź z usługi Przekroczono limit czasu.

- Biblioteki foundation multimediów na Windows są załadowane z opóźnieniem. Ta biblioteka jest tylko wymagane dla danych wejściowych z mikrofonu.

- Szybkość przekazywania danych audio jest ograniczone do informacje o dwa razy oryginalnego szybkości audio.

- W Windows zestawy .NET C# są teraz o silnej nazwie.

- Poprawienie dokumentacji: `Region` informacja jest wymagana do utworzenia aparat rozpoznawania.

Więcej przykładów zostały dodane i są stale aktualizowane. Aby uzyskać najnowszy zestaw przykładów, zobacz [repozytorium GitHub Przykładowy zestaw SDK rozpoznawania mowy](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Services cognitive mowy wersji zestawu SDK 0.2.12733: maj 2018 r.

Pierwsza wersja publiczna wersja zapoznawcza zestawu SDK mowy usługi Cognitive Services.
