---
title: 'Szybki Start: Rozpoznawanie mowy, intencji i jednostek — usługa mowy'
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
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a87ed9355a5939393fd5e20f395cc96f35e7f150
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816076"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do interaktywnego rozpoznawania mowy z danych audio przechwytywanych z mikrofonu. Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy z mikrofonu obejmuje tylko cztery kroki:
> [!div class="checklist"]
>
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt ````IntentRecognizer```` przy użyciu obiektu ````SpeechConfig```` z powyższych.
> * Przy użyciu obiektu ````IntentRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````IntentRecognitionResult````.
