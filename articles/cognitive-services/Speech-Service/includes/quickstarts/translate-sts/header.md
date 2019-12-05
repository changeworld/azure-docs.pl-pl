---
title: 'Szybki Start: Tłumaczenie mowy na mowę — usługa mowy'
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
ms.openlocfilehash: 805193c08e57ddc18dfca0a78c6b58cf895baec1
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817600"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie przetłumaczyć mowę z jednego języka na mowę w innym języku. Po spełnieniu kilku wymagań wstępnych, Tłumaczenie mowy na mowę obejmuje sześć kroków:
> [!div class="checklist"]
> * Utwórz obiekt ````SpeechTranslationConfig```` z klucza subskrypcji i regionu.
> * Zaktualizuj obiekt ````SpeechTranslationConfig````, aby określić języki źródłowe i docelowe.
> * Zaktualizuj obiekt ````SpeechTranslationConfig````, aby określić nazwę głosu wyjściowego mowy.
> * Utwórz obiekt ````TranslationRecognizer```` przy użyciu obiektu ````SpeechTranslationConfig```` z powyższych.
> * Przy użyciu obiektu ````TranslationRecognizer```` Rozpocznij proces rozpoznawania dla jednego wypowiedź.
> * Zbadaj zwróconą ````TranslationRecognitionResult````.
