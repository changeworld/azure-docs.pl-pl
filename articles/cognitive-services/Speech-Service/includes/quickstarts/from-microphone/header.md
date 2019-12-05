---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu — usługa mowy'
titleSuffix: Azure Cognitive Services
description: do ustalenia
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: a72597163a8c11cd8b515d052dc69992784e61e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818821"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie rozpoznać mowę z wejścia mikrofonu i uzyskać transkrypcję tekstu z przechwyconego dźwięku. Tę funkcję można łatwo zintegrować z aplikacjami lub urządzeniami w celu wykonywania typowych zadań rozpoznawania, takich jak Konwersacje jego przepisywania. Można go również użyć w celu uzyskania bardziej złożonej integracji, na przykład przy użyciu platformy bot z zestawem SDK mowy do tworzenia asystentów głosowych.

Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy z mikrofonu obejmuje tylko cztery kroki:

> [!div class="checklist"]
> * Utwórz obiekt `SpeechConfig` z klucza subskrypcji i regionu.
> * Utwórz obiekt `SpeechRecognizer` przy użyciu obiektu `SpeechConfig` z powyższych.
> * Przy użyciu obiektu `SpeechRecognizer` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą `SpeechRecognitionResult`.
