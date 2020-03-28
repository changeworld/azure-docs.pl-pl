---
title: 'Szybki start: rozpoznawanie mowy z mikrofonu — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75468749"
---
W tym przewodniku Szybki start użyjesz [zestawu SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do interaktywnego rozpoznawania mowy z wejścia mikrofonu i uzyskania transkrypcji tekstu z przechwyconego dźwięku. Łatwo jest zintegrować tę funkcję z aplikacjami lub urządzeniami w celu wykonywania typowych zadań rozpoznawania, takich jak transkrypcja konwersacji. Może być również używany do bardziej złożonych integracji, takich jak za pomocą bot framework z SDK mowy do tworzenia asystentów głosowych.

Po spełnieniu kilku wymagań wstępnych rozpoznawanie mowy z mikrofonu wymaga tylko czterech kroków:

> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `SpeechRecognizer` obiekt `SpeechConfig` przy użyciu obiektu z góry.
> * Za `SpeechRecognizer` pomocą obiektu, uruchom proces rozpoznawania dla pojedynczej wypowiedź.
> * Sprawdź `SpeechRecognitionResult` zwrócony.
