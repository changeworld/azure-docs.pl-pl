---
title: 'Szybki Start: synteza mowy w plikach audio — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502947"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do przekonwertowania tekstu na wymowę z dźwiękiem w pliku audio. Po spełnieniu kilku wymagań wstępnych, synteza mowy do pliku obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt konfiguracji audio, który określa. Nazwa pliku WAV.
> * Utwórz obiekt ````SpeechSynthesizer```` przy użyciu obiektów konfiguracji z powyższych.
> * Za pomocą obiektu ````SpeechSynthesizer```` przekonwertuj swój tekst na mowę, zapisując go w określonym pliku audio.
> * Sprawdź ````SpeechSynthesizer```` zwróconych pod kątem błędów.
