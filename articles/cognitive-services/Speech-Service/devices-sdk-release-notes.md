---
title: Dokumentacja zestawu SDK urządzeń mowy
titleSuffix: Azure Cognitive Services
description: Informacje o wersji zawierają dziennik aktualizacji, ulepszeń, poprawek błędów i zmian w zestawie SDK urządzeń mowy. Ten artykuł jest aktualizowany przy użyciu każdej wersji zestawu SDK urządzeń typu Speech.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: wellsi
ms.openlocfilehash: 3a74f3eb9aece3535f5505d69833a074c2dd0ed2
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091194"
---
# <a name="release-notes-speech-devices-sdk"></a>Informacje o wersji: zestaw SDK urządzeń mowy

W poniższych sekcjach przedstawiono zmiany w najnowszych wersjach.

## <a name="speech-devices-sdk-170"></a>1\.7.0 zestawu SDK urządzeń mowy:

*   System Linux ARM jest teraz obsługiwany.
*   Dostępne są początkowe pliki binarne dla Roobov2 (Linux ARM64).
*   Użytkownicy systemu Windows mogą używać AudioConfig. fromDefaultMicrophoneInput () lub AudioConfig. fromMicrophoneInput (DeviceName), aby określić mikrofon, który ma być używany.
*   Rozmiar biblioteki został zoptymalizowany.
*   Obsługa rozpoznawania wieloskładnikowego przy użyciu tego samego obiektu rozpoznawania mowy/konwersji.
*   Usuń sporadyczne zawieszenie, które nastąpiło podczas zatrzymywania rozpoznawania.
*   Przykładowe aplikacje zawierają teraz przykładowy plik uczestników. właściwości, aby przedstawić format pliku.
*   Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.7.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>1\.6.0 zestawu SDK urządzeń mowy:

- Obsługa [usługi Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/) w systemach Windows i Linux przy użyciu typowej [aplikacji przykładowej](https://aka.ms/sdsdk-download)
- Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.6.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>1\.5.1 zestawu SDK urządzeń mowy:

- Uwzględnij [transkrypcję konwersacji](conversation-transcription-service.md) w przykładowej aplikacji.
- Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.5.1. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech Devices SDK 1.5.0:2019 — może wydać

- Zestaw SDK urządzeń mowy jest teraz w wersji GA i nie jest już w trakcie przetwarzania warunkowego.
- Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.5.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).
- Nowa technologia słowa kluczowego zapewnia znaczące ulepszenia jakości, zobacz artykuł wprowadzenie zmian.
- Nowy potok przetwarzania audio dla ulepszonego rozpoznawania pól.

**Fundamentalne zmiany**

- Ze względu na nową technologię słów kluczowych wszystkie słowa kluczowe muszą zostać utworzone w naszym ulepszonym portalu słów kluczowych. Aby w pełni usunąć stare słowa kluczowe z urządzenia, Odinstaluj starą aplikację.
  - ADB Odinstaluj com. Microsoft. coginitiveservices. Speech. Samples. sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0:2019-kwi Release

- Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.4.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1:2019 — wydanie mar

- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.3.1. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).
- Obsługa zaktualizowanych słów kluczowych, zobacz istotne zmiany.
- Przykładowa aplikacja dodaje wybór języka dla rozpoznawania mowy i tłumaczenia.

**Fundamentalne zmiany**

- [Instalacja słowa kluczowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) została uproszczona, jest teraz częścią aplikacji i nie wymaga osobnej instalacji na urządzeniu.
- Rozpoznawanie słów kluczowych zostało zmienione i obsługiwane są dwa zdarzenia.
  - RecognizingKeyword, wskazuje, że wynik mowy zawiera (niezweryfikowany) tekst słowa kluczowego.
  - RecognizedKeyword, wskazuje, że rozpoznawanie słów kluczowych zostało zakończone rozpoznanie danego słowa kluczowego.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0:2018-lis Release

- Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.1.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).
- Ulepszona precyzja rozpoznawania mowy z użyciem udoskonalonego algorytmu przetwarzania audio.
- Przykładowa aplikacja dodała chińską obsługę rozpoznawania mowy.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1:2018 — wydanie z października

- Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.0.1. Aby uzyskać więcej informacji, zobacz informacje o [wersji](https://aka.ms/csspeech/whatsnew).
- Dokładność rozpoznawania mowy zostanie ulepszona dzięki ulepszonemu algorytmowi przetwarzania dźwięku
- Została naprawiona jedna usterka sesji audio ciągłego rozpoznawania.

**Fundamentalne zmiany**

- W tej wersji wprowadzono szereg istotnych zmian. Sprawdź [Tę stronę](https://aka.ms/csspeech/breakingchanges_1_0_0) , aby uzyskać szczegółowe informacje dotyczące interfejsów API.
- Pliki modelu KWS są niezgodne z zestawem SDK urządzeń Speech 1.0.1. Istniejące pliki słów kluczowych zostaną usunięte po zapisaniu nowych plików słów kluczowych na urządzeniu.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0:2018-sie Release

- Ulepszono dokładność rozpoznawania mowy przez naprawianie usterki w kodzie przetwarzania audio.
- Zaktualizowano składnik [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 0.5.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733:2018 — może wydać

Pierwsza publiczna wersja zapoznawcza zestawu SDK urządzeń Cognitive Services Speech.
