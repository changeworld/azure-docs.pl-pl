---
title: 'Szybki Start: Rozpoznawanie mowy z pliku audio — usługa mowy'
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
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504137"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do rozpoznawania mowy z pliku dźwiękowego. Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy z pliku obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt ````AudioConfig````, który określa. Nazwa pliku WAV.
> * Utwórz obiekt ````SpeechRecognizer```` przy użyciu ````SpeechConfig```` i ````AudioConfig```` obiektów z powyższych.
> * Przy użyciu obiektu ````SpeechRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````SpeechRecognitionResult````.
