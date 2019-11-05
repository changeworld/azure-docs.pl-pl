---
title: 'Szybki Start: Tłumaczenie mowy na mowę — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 77b060a5caf9865a1296cd6644cf0c51e3253a0e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502877"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie przetłumaczyć mowę z jednego języka na mowę w innym języku. Po spełnieniu kilku wymagań wstępnych, Tłumaczenie mowy na mowę obejmuje sześć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechTranslationConfig```` z klucza subskrypcji i regionu.
> * Zaktualizuj obiekt ````SpeechTranslationConfig````, aby określić języki źródłowe i docelowe.
> * Zaktualizuj obiekt ````SpeechTranslationConfig````, aby określić nazwę głosu wyjściowego mowy.
> * Utwórz obiekt ````TranslationRecognizer```` przy użyciu obiektu ````SpeechTranslationConfig```` z powyższych.
> * Przy użyciu obiektu ````TranslationRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````TranslationRecognitionResult````.
