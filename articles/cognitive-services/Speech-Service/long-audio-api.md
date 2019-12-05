---
title: Long audio API (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: ed00a9df46660cc6bfb4ec5fd9a93c80f5d6653e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815333"
---
# <a name="long-audio-api-preview"></a>Long audio API (wersja zapoznawcza)

Długi interfejs API audio jest przeznaczony do asynchronicznej syntezy tekstu na mowę (na przykład: książki audio). Ten interfejs API nie zwraca danych z syntezy dźwiękowej w czasie rzeczywistym, zamiast tego oczekuje na to, że będziesz sondował o odpowiedzi i zużywać dane wyjściowe, gdy staną się dostępne w usłudze. W przeciwieństwie do interfejsu API zamiany mowy na mowę, który jest używany przez zestaw mowy SDK, długi interfejs API audio umożliwia tworzenie dźwięków z dźwiękiem dłużej niż 10 minut, dzięki czemu jest idealnym rozwiązaniem dla wydawców i platform zawartości audio.

Dodatkowe korzyści wynikające z długiego interfejsu API audio:

* Funkcja syntezy mowy zwrócona przez usługę używa głosów neuronowych, które zapewniają wyjście audio o wysokiej wierności.
* Ponieważ odpowiedzi w czasie rzeczywistym nie są obsługiwane, nie ma potrzeby wdrażania punktu końcowego głosowego.

> [!NOTE]
> Długi interfejs API audio obsługuje teraz tylko [niestandardowe neuronowych](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Przepływ pracy

Zazwyczaj podczas korzystania z długiego interfejsu API audio można przesłać plik tekstowy lub pliki do wyszukania, przeprowadzić sondowanie stanu, a następnie, jeśli stan zakończy się pomyślnie, można pobrać wyjście audio.

Ten diagram zawiera ogólne omówienie przepływu pracy.

![Diagram przepływu pracy interfejsu API Long audio](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Przygotowywanie zawartości do syntezy

Podczas przygotowywania pliku tekstowego upewnij się, że:

* Jest to zwykły tekst (. txt) lub tekst SSML (. txt)
  * W przypadku zwykłego tekstu każdy akapit jest oddzielony przez naciśnięcie **klawisza ENTER/Return** -View — [przykład wprowadzania tekstu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * W przypadku tekstu SSML każdy element SSML jest traktowany jako akapit. Elementy SSML są oddzielane różnymi akapitami — widok [SSML wprowadzanie tekstu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt). Aby uzyskać kod języka, zobacz [Speech synteza Markup Language (SSML)](speech-synthesis-markup.md)
* Jest zakodowany jako [UTF-8 z oznaczeniem kolejności bajtów (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Zawiera więcej niż 10 000 znaków lub więcej niż 50 akapitów
* Jest pojedynczym plikiem, a nie plikiem ZIP

## <a name="audio-output-formats"></a>Formaty wyjściowe audio

Następujące formaty wyjściowe audio są obsługiwane przez długi interfejs API audio:

> [!NOTE]
> Domyślny format dźwięku to RIFF-16khz-16bit-mono-PCM.

* RIFF-8khz-16bit-mono-PCM
* RIFF-16khz-16bit-mono-PCM
* RIFF-24khz-16bit-mono-PCM
* RIFF-48kHz-16bit-mono-PCM
* audio-16khz-32kbitrate-mono-MP3
* audio-16khz-64kbitrate-mono-MP3
* audio-16khz-128kbitrate-mono-MP3
* audio-24khz-48kbitrate-mono-MP3
* audio-24khz-96kbitrate-mono-MP3
* audio-24khz-160kbitrate-mono-MP3

## <a name="quickstarts"></a>Szybki start

Oferujemy Przewodniki Szybki Start ułatwiające pomyślne uruchomienie długiego interfejsu API audio. Ta tabela zawiera listę długich interfejsów API audio — szybki start zorganizowanych według języka.

* [Szybki Start: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Przykładowy kod
Przykładowy kod dla długiego interfejsu API audio jest dostępny w witrynie GitHub.

* [Przykładowy kod: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Przykładowy kod:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Przykładowy kod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
