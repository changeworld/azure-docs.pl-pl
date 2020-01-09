---
title: 'Szybki Start: Rozpoznawanie mowy z pliku audio — usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466932"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do rozpoznawania mowy z pliku dźwiękowego. Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy z pliku obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt ````AudioConfig````, który określa. Nazwa pliku WAV.
> * Utwórz obiekt ````SpeechRecognizer```` przy użyciu ````SpeechConfig```` i ````AudioConfig```` obiektów z powyższych.
> * Przy użyciu obiektu ````SpeechRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````SpeechRecognitionResult````.
