---
title: 'Szybki Start: translacja zamiany mowy na tekst — usługa mowy'
titleSuffix: Azure Cognitive Services
description: do ustalenia
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980907"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie przetłumaczyć mowę z jednego języka na tekst w innym języku. Po spełnieniu kilku wymagań wstępnych, przetłumaczenie zamiany mowy na tekst obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Zaktualizuj obiekt ````SpeechConfig````, aby określić języki źródłowe i docelowe.
> * Utwórz obiekt ````TranslationRecognizer```` przy użyciu obiektu ````SpeechConfig```` z powyższych.
> * Przy użyciu obiektu ````TranslationRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````TranslationRecognitionResult````.
