---
title: 'Szybki Start: Tłumaczenie mowy na wiele języków — usługa mowy'
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
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980808"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie przetłumaczyć mowę z jednego języka na mowę w innym języku. Po spełnieniu kilku wymagań wstępnych przetłumaczenie mowy na tekst w wielu językach obejmuje tylko sześć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechTranslationConfig```` z klucza subskrypcji i regionu.
> * Zaktualizuj obiekt ````SpeechTranslationConfig````, aby określić język źródłowy rozpoznawania mowy.
> * Zaktualizuj obiekt ````SpeechTranslationConfig````, aby określić wiele języków docelowych tłumaczenia.
> * Utwórz obiekt ````TranslationRecognizer```` przy użyciu obiektu ````SpeechTranslationConfig```` z powyższych.
> * Przy użyciu obiektu ````TranslationRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````TranslationRecognitionResult````.
