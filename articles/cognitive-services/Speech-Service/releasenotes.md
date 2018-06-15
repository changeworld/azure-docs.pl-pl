---
title: Dokumentacji zestawu SDK usług kognitywnych mowy | Dokumentacja firmy Microsoft
description: Informacje o wersji - co się zmieniło w najnowszych wersjach
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0b1559d288380cf3d0c180a225278cc13d22a5d0
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356300"
---
# <a name="release-notes"></a>Informacje o wersji

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Usług kognitywnych mowy wersji zestawu SDK 0.4.0: czerwiec 2018

**Zmiany funkcjonalne**

- AudioInputStream

  Aparat rozpoznawania mogą teraz używać strumienia źródła audio. Aby uzyskać szczegółowe informacje, zobacz pokrewne [przewodnik](how-to-use-audio-input-streams.md).

- Format szczegółowych danych wyjściowych

  Podczas tworzenia `SpeechRecognizer`, możesz poprosić o `Detailed` lub `Simple` format danych wyjściowych. `DetailedSpeechRecognitionResult` Zawiera wynik zaufania, rozpoznany, raw leksykalne formularza, znormalizowana postać i znormalizowana postać z maskowanego niestosownych wyrażeń.

**Fundamentalne zmiany**

- Zmień na `SpeechRecognitionResult.Text` z `SpeechRecognitionResult.RecognizedText` w języku C#.

**Poprawki błędów**

- Rozwiąż problem wywołania zwrotnego w warstwie USP podczas zamykania.

- Jeśli aparat rozpoznawania wykorzystany pliku wejściowego audio, został zawierający do dojście do pliku dłużej niż to konieczne.

- Usunąć kilka zakleszczenie między przekazywanie komunikatów i aparatu rozpoznawania.

- Fire `NoMatch` wynikiem odpowiedź z usługi zostanie przekroczony limit czasu.

- Biblioteki media foundation w systemie Windows są załadowane z opóźnieniem. Ta biblioteka jest tylko wymagane dla danych wejściowych mikrofon.

- Szybkość przekazywania danych audio jest ograniczona do o dwa razy oryginalnego szybkości audio.

- W systemie Windows, C# .NET zestawy są teraz o silnych nazwach.

- Poprawka dokumentacji: `Region` jest informacje wymagane do utworzenia aparat rozpoznawania.

Więcej przykładów zostały dodane i są stale aktualizowane. Dla zestawu najnowsze przykłady, zobacz [repozytorium GitHub próbki SDK mowy](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Usług kognitywnych mowy wersji zestawu SDK 0.2.12733: 2018 maja

Pierwszy publicznej wersji zapoznawczej kognitywnych usługi SDK mowy.
