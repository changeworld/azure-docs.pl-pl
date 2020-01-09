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
ms.date: 1/02/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a833d39ab91cd803f066d707306a6ff648d37e8f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660555"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) i usługi Language UNDERSTANDING (Luis) do rozpoznawania intencji z danych audio przechwytywanych z mikrofonu. W tym celu należy użyć zestawu Speech SDK do przechwytywania mowy i prekompilowanej domeny z LUIS, aby identyfikować intencje dla automatyzacji domowej, takie jak Włączanie i wyłączanie oświetlenia. 

Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy i identyfikowanie intencji z mikrofonu trwa tylko kilka kroków:

> [!div class="checklist"]
>
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt ````IntentRecognizer```` przy użyciu obiektu ````SpeechConfig```` z powyższych.
> * Przy użyciu obiektu ````IntentRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````IntentRecognitionResult````.
