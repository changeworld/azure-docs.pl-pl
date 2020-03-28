---
title: 'Szybki start: tłumaczenie mowy na wiele języków — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980808"
---
W tym przewodniku Szybki start użyjesz [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do interaktywnego tłumaczenia mowy z jednego języka na mowę w innym języku. Po spełnieniu kilku wymagań wstępnych tłumaczenie mowy na tekst w wielu językach wymaga tylko sześciu kroków:
> [!div class="checklist"]
> * Utwórz ````SpeechTranslationConfig```` obiekt z klucza subskrypcji i regionu.
> * Zaktualizuj obiekt, ````SpeechTranslationConfig```` aby określić język źródłowy rozpoznawania mowy.
> * Zaktualizuj obiekt, ````SpeechTranslationConfig```` aby określić wiele języków docelowych tłumaczenia.
> * Utwórz ````TranslationRecognizer```` obiekt ````SpeechTranslationConfig```` przy użyciu obiektu z góry.
> * Za ````TranslationRecognizer```` pomocą obiektu, uruchom proces rozpoznawania dla pojedynczej wypowiedź.
> * Sprawdź ````TranslationRecognitionResult```` zwrócony.
