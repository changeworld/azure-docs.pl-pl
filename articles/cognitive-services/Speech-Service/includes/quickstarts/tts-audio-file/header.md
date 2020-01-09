---
title: 'Szybki Start: synteza mowy w plikach audio — usługa mowy'
titleSuffix: Azure Cognitive Services
description: do ustalenia
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467870"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do przekonwertowania tekstu na wymowę z dźwiękiem w pliku audio. Po spełnieniu kilku wymagań wstępnych, synteza mowy do pliku obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt konfiguracji audio, który określa. Nazwa pliku WAV.
> * Utwórz obiekt ````SpeechSynthesizer```` przy użyciu obiektów konfiguracji z powyższych.
> * Za pomocą obiektu ````SpeechSynthesizer```` przekonwertuj swój tekst na mowę, zapisując go w określonym pliku audio.
> * Sprawdź ````SpeechSynthesizer```` zwróconych pod kątem błędów.
