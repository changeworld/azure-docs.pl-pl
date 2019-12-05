---
title: 'Szybki Start: synteza mowy w plikach audio — usługa mowy'
titleSuffix: Azure Cognitive Services
description: do ustalenia
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 7d9a03f5a57473ce651560b261a4cf84b4ca4824
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818013"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do przekonwertowania tekstu na wymowę z dźwiękiem w pliku audio. Po spełnieniu kilku wymagań wstępnych, synteza mowy do pliku obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt konfiguracji audio, który określa. Nazwa pliku WAV.
> * Utwórz obiekt ````SpeechSynthesizer```` przy użyciu obiektów konfiguracji z powyższych.
> * Za pomocą obiektu ````SpeechSynthesizer```` przekonwertuj swój tekst na mowę, zapisując go w określonym pliku audio.
> * Sprawdź ````SpeechSynthesizer```` zwróconych pod kątem błędów.
