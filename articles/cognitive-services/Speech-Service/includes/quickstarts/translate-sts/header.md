---
title: 'Szybki start: tłumaczenie mowy na mowę — usługa mowy'
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
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980687"
---
W tym przewodniku Szybki start użyjesz [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do interaktywnego tłumaczenia mowy z jednego języka na mowę w innym języku. Po spełnieniu kilku wymagań wstępnych, tłumaczenie mowy na mowę wymaga tylko sześciu kroków:
> [!div class="checklist"]
> * Utwórz ````SpeechTranslationConfig```` obiekt z klucza subskrypcji i regionu.
> * Zaktualizuj obiekt, ````SpeechTranslationConfig```` aby określić języki źródłowe i docelowe.
> * Zaktualizuj obiekt, ````SpeechTranslationConfig```` aby określić nazwę głosową wyjścia mowy.
> * Utwórz ````TranslationRecognizer```` obiekt ````SpeechTranslationConfig```` przy użyciu obiektu z góry.
> * Za ````TranslationRecognizer```` pomocą obiektu, uruchom proces rozpoznawania dla pojedynczej wypowiedź.
> * Sprawdź ````TranslationRecognitionResult```` zwrócony.
