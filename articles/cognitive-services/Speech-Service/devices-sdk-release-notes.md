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
ms.date: 05/22/2019
ms.author: wellsi
ms.openlocfilehash: 1c91cde45a6a420376af36f70487adf7fe0ee83a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66751814"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Informacje o wersji programu Cognitive Services zestawu Speech Devices SDK
Następujące sekcje Lista zmian w najnowszych wersjach.

## <a name="speech-devices-sdk-151"></a>Zestaw Speech Devices SDK 1.5.1:

*   Obejmują [transkrypcji konwersacji](conversation-transcription-service.md) w przykładowej aplikacji.
*   Zaktualizowano [zestaw SDK rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) składnik do wersji 1.5.1. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Usługi cognitive Services Speech Devices SDK 1.5.0: Wersja maja 2019 r

*   Zestawu Speech Devices SDK jest teraz ogólnie i nie jest już warunkowej wersji zapoznawczej.
*   Zaktualizowano [zestaw SDK rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) składnik do wersji 1.5.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).
*   Nową technologię word wznawiania oferuje jakości znaczne ulepszenia, zobacz fundamentalne zmiany.
*   Nowy potok przetwarzania audio rozpoznawanie daleko pola.

**Fundamentalne zmiany**

*   Ze względu na nową technologię word wznawiania wszystkie wyrazy wznawiania musi być ponownie utworzone w portalu programu word ulepszone wznawiania. Aby całkowicie usunąć stare słowa kluczowe z urządzenia odinstalować stare aplikację.
    - adb uninstall com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Usługi cognitive Services Speech Devices SDK 1.4.0: 2019 kwi wydania 

* Zaktualizowano [zestaw SDK rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) składnik do wersji 1.4.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew). 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Usługi cognitive Services Speech Devices SDK 1.3.1: Wydanie z marca 2019 r 

* Zaktualizowano [zestaw SDK rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.3.1. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew). 
*   Zaktualizowano słowo aktywujące obsługi, zobacz fundamentalne zmiany.
*   Przykładowa aplikacja dodaje wybór języka, zarówno usługa rozpoznawania mowy, jak i tłumaczenia.

**Fundamentalne zmiany** 

*   [Instalowanie słowo aktywujące](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) został uproszczony, jest teraz część aplikacji i nie wymaga oddzielnej instalacji na urządzeniu.
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
