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
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: cd45657beacd04eb2376af3e4297eae051157778
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816926"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie przetłumaczyć mowę z jednego języka na tekst w innym języku. Po spełnieniu kilku wymagań wstępnych, przetłumaczenie zamiany mowy na tekst obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechConfig```` z klucza subskrypcji i regionu.
> * Zaktualizuj obiekt ````SpeechConfig````, aby określić języki źródłowe i docelowe.
> * Utwórz obiekt ````TranslationRecognizer```` przy użyciu obiektu ````SpeechConfig```` z powyższych.
> * Przy użyciu obiektu ````TranslationRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````TranslationRecognitionResult````.
