---
title: 'Szybki start: tłumaczenie mowy na tekst — usługa mowy'
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
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980907"
---
W tym przewodniku Szybki start użyjesz [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do interaktywnego tłumaczenia mowy z jednego języka na tekst w innym języku. Po spełnieniu kilku wymagań wstępnych tłumaczenie mowy na tekst wykonuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz ````SpeechConfig```` obiekt z klucza subskrypcji i regionu.
> * Zaktualizuj obiekt, ````SpeechConfig```` aby określić języki źródłowe i docelowe.
> * Utwórz ````TranslationRecognizer```` obiekt ````SpeechConfig```` przy użyciu obiektu z góry.
> * Za ````TranslationRecognizer```` pomocą obiektu, uruchom proces rozpoznawania dla pojedynczej wypowiedź.
> * Sprawdź ````TranslationRecognitionResult```` zwrócony.
