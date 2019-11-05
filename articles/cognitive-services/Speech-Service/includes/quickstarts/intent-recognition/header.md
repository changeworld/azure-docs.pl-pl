---
title: 'Szybki Start: Rozpoznawanie mowy, intencji i jednostek — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 3b50ba8f2c3d21fb5bb0ab2c26cedb6bda0fab16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503857"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do interaktywnego rozpoznawania mowy z danych audio przechwytywanych z mikrofonu. Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy z mikrofonu obejmuje tylko cztery kroki:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt ````IntentRecognizer```` przy użyciu obiektu ````SpeechConfig```` z powyższych.
> * Przy użyciu obiektu ````IntentRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````IntentRecognitionResult````.