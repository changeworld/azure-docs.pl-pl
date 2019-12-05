---
title: 'Szybki Start: Rozpoznawanie mowy z pliku audio — usługa mowy'
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
ms.openlocfilehash: 2f7ec24667514fb131af29321a53f97210e58fc9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819425"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do rozpoznawania mowy z pliku dźwiękowego. Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy z pliku obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Utwórz obiekt ````AudioConfig````, który określa. Nazwa pliku WAV.
> * Utwórz obiekt ````SpeechRecognizer```` przy użyciu ````SpeechConfig```` i ````AudioConfig```` obiektów z powyższych.
> * Przy użyciu obiektu ````SpeechRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````SpeechRecognitionResult````.
