---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu — usługa mowy'
titleSuffix: Azure Cognitive Services
description: do ustalenia
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468749"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie rozpoznać mowę z wejścia mikrofonu i uzyskać transkrypcję tekstu z przechwyconego dźwięku. Tę funkcję można łatwo zintegrować z aplikacjami lub urządzeniami w celu wykonywania typowych zadań rozpoznawania, takich jak Konwersacje jego przepisywania. Można go również użyć w celu uzyskania bardziej złożonej integracji, na przykład przy użyciu platformy bot z zestawem SDK mowy do tworzenia asystentów głosowych.

Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy z mikrofonu obejmuje tylko cztery kroki:

> [!div class="checklist"]
> * Utwórz obiekt `SpeechConfig` z klucza subskrypcji i regionu.
> * Utwórz obiekt `SpeechRecognizer` przy użyciu obiektu `SpeechConfig` z powyższych.
> * Przy użyciu obiektu `SpeechRecognizer` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą `SpeechRecognitionResult`.
