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
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 71a5edfbe388f2d4bfb48255b901cb0037665252
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069434"
---
# <a name="release-notes"></a>Informacje o wersji

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

**Poprawki błędów**

* Naprawiono niepoprawne wartości zwracanych w wyniku podczas `RecognizeAsync()` upłynie limit czasu.
* Zależność od media foundation bibliotek na Windows zostanie usunięta. Zestaw SDK używa teraz podstawowe interfejsy API z Audio.
* Poprawienie dokumentacji: dodano stronę region do opisywania, czym są obsługiwane regiony.

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
