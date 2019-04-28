---
title: Dokumentacja zestawu SDK urządzenia usługi rozpoznawania mowy
titleSuffix: Azure Cognitive Services
description: Informacje o wersji — co zmieniło się w najnowszych wersjach
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: gracez
ms.openlocfilehash: 33a03b24de56ab1090cc8e07c9619eda17f33e27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293539"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Informacje o wersji programu Cognitive Services zestawu Speech Devices SDK

Następujące sekcje Lista zmian w najnowszych wersjach.

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Usługi cognitive Services Speech Devices SDK 1.4.0: 2019 kwi wydania 

* Zaktualizowano [zestaw SDK rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) składnik do wersji 1.4.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew). 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Usługi cognitive Services Speech Devices SDK 1.3.1: Wydanie z marca 2019 r 

* Zaktualizowano [zestaw SDK rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.3.1. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew). 
*   Zaktualizowano słowo aktywujące obsługi, zobacz fundamentalne zmiany.
*   Przykładowa aplikacja dodaje wybór języka, zarówno usługa rozpoznawania mowy, jak i tłumaczenia.

**Fundamentalne zmiany** 

*   [Instalowanie słowo aktywujące](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg#run-a-sample-application) został uproszczony, jest teraz część aplikacji i nie wymaga oddzielnej instalacji na urządzeniu.
*   Rozpoznawanie word wznawiania została zmieniona, a obsługiwane są dwa zdarzenia.
    - RecognizingKeyword, wskazuje, że wynik mowy zawiera tekst (niezweryfikowanych) — słowo kluczowe.
    - RecognizedKeyword, wskazuje, że — słowo kluczowe rozpoznawania ukończone rozpoznawaniu dane słowo kluczowe.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Usługi cognitive Services Speech Devices SDK 1.1.0: Wersja listopada 2018 r. 

* Zaktualizowano [zestaw SDK rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.1.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew). 
* Skrajna dokładności rozpoznawania mowy pola została ulepszona przy użyciu naszego algorytmu rozszerzonego przetwarzania audio.
* Przykładowa aplikacja dodano obsługę rozpoznawania mowy chińskim.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Usługi cognitive Services Speech Devices SDK 1.0.1: Wersja z października 2018 r. 

* Zaktualizowano [zestaw SDK rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) składnik do wersji 1.0.1. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew). 
* Dokładność rozpoznawania mowy, zostanie on ulepszony przy użyciu naszego algorytmu Ulepszone przetwarzanie audio  
* Jeden ciągły rozpoznawania dźwięku sesji naprawienia usterki.

**Fundamentalne zmiany** 

* W tej wersji wprowadzono szereg istotnych zmian. Sprawdź, czy [na tej stronie](https://aka.ms/csspeech/breakingchanges_1_0_0) Aby uzyskać szczegółowe informacje odnoszące się do interfejsów API. 
* Pliki modelu KWS nie są zgodne z zestawu Speech Devices SDK 1.0.1. Istniejące pliki funkcji programu Word zostaną usunięte po nowe pliki funkcji programu Word zapisywanych na urządzeniu. 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Usługi cognitive Services Speech Devices SDK wersję 0.5.0: Wersji z sierpnia 2018 r.

* Ulepszona dokładności rozpoznawania mowy, naprawiania usterki w kodzie przetwarzania audio.
* Zaktualizowano [zestaw SDK rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) składnik do wersji wersję 0.5.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Usługi cognitive Services Speech Devices SDK 0.2.12733: Wersja maja 2018 r.

Pierwsza wersja publiczna wersja zapoznawcza Cognitive Services Speech Devices SDK.
