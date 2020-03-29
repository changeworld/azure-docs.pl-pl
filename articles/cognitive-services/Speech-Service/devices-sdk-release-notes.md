---
title: Dokumentacja sdk urządzeń mowy
titleSuffix: Azure Cognitive Services
description: Informacje o wersji zawierają dziennik aktualizacji, ulepszeń, poprawek błędów i zmian w SDK urządzeń mowy. Ten artykuł jest aktualizowany o każdą wersję SDK urządzeń mowy.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371615"
---
# <a name="release-notes-speech-devices-sdk"></a>Informacje o wydaniu: SDK urządzeń mowy

Poniższe sekcje listy zmian w najnowszych wersjach.

## <a name="speech-devices-sdk-190"></a>Urządzenia mowy SDK 1.9.0:

- Dostępne są początkowe pliki binarne [dla Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64).
- Roobo v1 używa teraz Maven dla speech SDK
- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.9.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-170"></a>Urządzenia mowy SDK 1.7.0:

- Linux ARM jest teraz obsługiwany.
- Dostępne są początkowe pliki binarne [dla Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) (Linux ARM64).
- Użytkownicy systemu `AudioConfig.fromDefaultMicrophoneInput()` `AudioConfig.fromMicrophoneInput(deviceName)` Windows mogą używać lub określać mikrofon, który ma być używany.
- Rozmiar biblioteki został zoptymalizowany.
- Obsługa rozpoznawania wielu kolejek przy użyciu tego samego obiektu rozpoznawania mowy/intencji.
- Napraw sporadyczne zawieszanie się, które miałoby miejsce podczas zatrzymywania rozpoznawania.
- Przykładowe aplikacje zawierają teraz przykładowy plik participants.properties, aby zademonstrować format pliku.
- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.7.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>Urządzenia mowy SDK 1.6.0:

- Obsługa [usługi Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) w systemie Windows i Linux za pomocą wspólnej [przykładowej aplikacji](https://aka.ms/sdsdk-download)
- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.6.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>Urządzenia mowy SDK 1.5.1:

- Dołącz [transkrypcję konwersacji](conversation-transcription-service.md) w przykładowej aplikacji.
- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.5.1. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech Devices SDK 1.5.0: wydanie 2019-Maj

- SDK urządzeń mowy jest teraz GA i nie jest już bramą podglądu.
- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.5.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).
- Nowa technologia słów kluczowych przynosi znaczną poprawę jakości, zobacz Breaking Changes.
- Nowy potok przetwarzania dźwięku dla lepszego rozpoznawania dalekiego pola.

**Fundamentalne zmiany**

- Ze względu na nową technologię słów kluczowych wszystkie słowa kluczowe muszą zostać ponownie utworzone w naszym ulepszonym portalu słów kluczowych. Aby całkowicie usunąć stare słowa kluczowe z urządzenia odinstaluj starą aplikację.
  - adb odinstalować com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Urządzenia mowy SDK 1.4.0: wydanie 2019-Apr

- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.4.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1: wydanie 2019-Mar

- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.3.1. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).
- Zaktualizowano obsługę słów kluczowych, zobacz Przełamywanie zmian.
- Przykładowa aplikacja dodaje wybór języka zarówno do rozpoznawania mowy, jak i tłumaczenia.

**Fundamentalne zmiany**

- [Instalacja słowa kluczowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) została uproszczona, jest teraz częścią aplikacji i nie wymaga oddzielnej instalacji na urządzeniu.
- Rozpoznawanie słów kluczowych uległo zmianie i obsługiwane są dwa zdarzenia.
  - `RecognizingKeyword,`wskazuje, że wynik mowy zawiera (niezweryfikowany) tekst słowa kluczowego.
  - `RecognizedKeyword`, wskazuje, że rozpoznawanie słów kluczowych zostało zakończone, rozpoznając dane słowo kluczowe.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Urządzenia mowy SDK 1.1.0: wydanie 2018-Nov

- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.1.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).
- Dokładność rozpoznawania mowy dalekiego pola została ulepszona dzięki naszemu ulepszonemu algorytmowi przetwarzania dźwięku.
- Przykładowa aplikacja dodała chińską obsługę rozpoznawania mowy.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Urządzenia mowy SDK 1.0.1: wydanie 2018-Paź

- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 1.0.1. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](https://aka.ms/csspeech/whatsnew).
- Dokładność rozpoznawania mowy zostanie poprawiona dzięki naszemu ulepszonego algorytmowi przetwarzania dźwięku
- Naprawiono jeden błąd sesji audio ciągłego rozpoznawania.

**Fundamentalne zmiany**

- W tym wydaniu wprowadza się szereg przełomowych zmian. Sprawdź [tę stronę,](https://aka.ms/csspeech/breakingchanges_1_0_0) aby uzyskać szczegółowe informacje dotyczące interfejsów API.
- Pliki modelu KWS nie są zgodne z programem SDK 1.0.1 urządzeń mowy. Istniejące pliki słów kluczowych zostaną usunięte po zapisaniu nowych plików słów kluczowych na urządzeniu.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0: wydanie 2018-Sie

- Poprawiono dokładność rozpoznawania mowy, naprawiając błąd w kodzie przetwarzania dźwięku.
- Zaktualizowano składnik [SDK mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do wersji 0.5.0. Aby uzyskać więcej informacji, zobacz jego [informacje o wersji](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733: wydanie 2018-Maj

Pierwsza publiczna wersja podglądu sdk urządzeń mowy usług Cognitive Services.
