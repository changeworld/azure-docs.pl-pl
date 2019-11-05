---
title: 'Szybki Start: synteza mowy — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić funkcję syntezy mowy przy użyciu zestawu Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503080"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do przekonwertowania tekstu na mowę. Po spełnieniu kilku wymagań wstępnych, renderowanie mowy do domyślnych głośników obejmuje tylko cztery kroki:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt ````SpeechSynthesizer```` przy użyciu obiektu ````SpeechConfig```` z powyższych.
> * Używanie obiektu ````SpeechSynthesizer````, aby mówić do tekstu.
> * Sprawdź ````SpeechSynthesisResult```` zwróconych pod kątem błędów.
