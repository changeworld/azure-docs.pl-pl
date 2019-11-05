---
title: Dokumentacja zestawu SDK urządzeń mowy
titleSuffix: Azure Cognitive Services
description: Informacje o wersji — zmiany w najnowszych wersjach
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: wellsi
ms.openlocfilehash: ba1db1ccba6a1849756e75c9b9f7078371da5bfb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464743"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Informacje o wersji zestawu SDK urządzeń Cognitive Services Speech
W poniższych sekcjach przedstawiono zmiany w najnowszych wersjach.

## <a name="speech-devices-sdk-160"></a>1\.6.0 zestawu SDK urządzeń mowy:

*   Obsługa [usługi Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/) w systemach Windows i Linux przy użyciu typowej [aplikacji przykładowej](https://aka.ms/sdsdk-download)
*   Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.6.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>1\.5.1 zestawu SDK urządzeń mowy:

*   Uwzględnij [transkrypcję konwersacji](conversation-transcription-service.md) w przykładowej aplikacji.
*   Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.5.1. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Cognitive Services zestawu SDK 1.5.0 urządzeń mowy: 2019 — może wydać

*   Zestaw SDK urządzeń mowy jest teraz w wersji GA i nie jest już w trakcie przetwarzania warunkowego.
*   Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.5.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).
*   Nowa technologia słowa kluczowego zapewnia znaczące ulepszenia jakości, zobacz artykuł wprowadzenie zmian.
*   Nowy potok przetwarzania audio dla ulepszonego rozpoznawania pól.

**Istotne zmiany**

*   Ze względu na nową technologię słów kluczowych wszystkie słowa kluczowe muszą zostać utworzone w naszym ulepszonym portalu słów kluczowych. Aby w pełni usunąć stare słowa kluczowe z urządzenia, Odinstaluj starą aplikację.
    - ADB Odinstaluj com. Microsoft. coginitiveservices. Speech. Samples. sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Cognitive Services zestawu 1.4.0 Speech Devices SDK: 2019-kwi Release

* Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.4.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Cognitive Services Speech Devices SDK 1.3.1:2019 — wydanie mar

* Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.3.1. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).
*   Obsługa zaktualizowanych słów kluczowych, zobacz istotne zmiany.
*   Przykładowa aplikacja dodaje wybór języka dla rozpoznawania mowy i tłumaczenia.

**Istotne zmiany**

*   [Instalacja słowa kluczowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) została uproszczona, jest teraz częścią aplikacji i nie wymaga osobnej instalacji na urządzeniu.
*   Rozpoznawanie słów kluczowych zostało zmienione i obsługiwane są dwa zdarzenia.
    - RecognizingKeyword, wskazuje, że wynik mowy zawiera (niezweryfikowany) tekst słowa kluczowego.
    - RecognizedKeyword, wskazuje, że rozpoznawanie słów kluczowych zostało zakończone rozpoznanie danego słowa kluczowego.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Cognitive Services zestawu SDK 1.1.0 urządzeń mowy: 2018 — wydanie lis

* Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.1.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).
* Ulepszona precyzja rozpoznawania mowy z użyciem udoskonalonego algorytmu przetwarzania audio.
* Przykładowa aplikacja dodała chińską obsługę rozpoznawania mowy.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Cognitive Services zestawu SDK urządzeń 1.0.1:2018 — wydanie z października

* Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.0.1. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).
* Dokładność rozpoznawania mowy zostanie ulepszona dzięki ulepszonemu algorytmowi przetwarzania dźwięku  
* Została naprawiona jedna usterka sesji audio ciągłego rozpoznawania.

**Istotne zmiany**

* W tej wersji wprowadzono kilka istotnych zmian. Sprawdź [Tę stronę](https://aka.ms/csspeech/breakingchanges_1_0_0) , aby uzyskać szczegółowe informacje dotyczące interfejsów API.
* Pliki modelu KWS są niezgodne z zestawem SDK urządzeń Speech 1.0.1. Istniejące pliki słów kluczowych zostaną usunięte po zapisaniu nowych plików słów kluczowych na urządzeniu.

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Cognitive Services zestawu 0.5.0 Speech Devices SDK: 2018-sie

* Ulepszono dokładność rozpoznawania mowy przez naprawianie usterki w kodzie przetwarzania audio.
* Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 0.5.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Cognitive Services zestawu SDK 0.2.12733 urządzeń mowy: 2018 — może wydać

Pierwsza publiczna wersja zapoznawcza zestawu SDK urządzeń Cognitive Services Speech.
