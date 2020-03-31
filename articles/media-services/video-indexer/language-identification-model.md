---
title: Automatyczne identyfikowanie języków mówionych za pomocą indeksatora wideo — Azure
titleSuffix: Azure Media Services
description: W tym artykule opisano, jak model identyfikacji języka indeksatora wideo jest używany do automatycznego identyfikowania języka mówionego w filmie wideo.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 7a2e03b8dacbf6c3ff20e02c804804b671e86d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513885"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Automatyczne identyfikowanie języka mówionego za pomocą modelu identyfikacji języka

Video Indexer obsługuje automatyczną identyfikację języka (LID), która jest procesem automatycznej identyfikacji treści języka mówionego z audio i wysyłania pliku multimedialnego do transkrypcji w dominującym zidentyfikowanym języku. Obecnie LID obsługuje angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, rosyjski i portugalski (brazylijski). 

## <a name="choosing-auto-language-identification-on-indexing"></a>Wybieranie automatycznej identyfikacji języka podczas indeksowania

Podczas indeksowania lub [ponownego indeksowania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) wideo za `auto detect` pomocą interfejsu `sourceLanguage` API, wybierz opcję w parametrze.

Korzystając z portalu, przejdź do **filmów z konta** na stronie głównej [indeksatora wideo](https://www.videoindexer.ai/) i umieść wskaźnik myszy na nazwie filmu, który chcesz ponownie indeksować. W prawym dolnym rogu kliknij przycisk ponownego indeksowania. W oknie dialogowym **Ponowne indeksowanie wideo** wybierz pozycję *Automatyczne wykrywanie* z listy rozwijanej **Język źródłowy wideo.**

![Autowykrywanie](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Dane wyjściowe modelu

Indeksator wideo transkrybuje film według najbardziej prawdopodobnego języka, `> 0.6`jeśli zaufanie do tego języka jest . Jeśli język nie może być identyfikowany z ufnością, zakłada, że językiem mówionym jest angielski. 

Model dominujący język jest dostępny w `sourceLanguage` insights JSON jako atrybut (w katalogu głównym / filmy / insights). Odpowiedni wynik zaufania jest również `sourceLanguageConfidence` dostępny w ramach atrybutu.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Wytyczne i ograniczenia

* Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, rosyjski i brazylijski portugalski.
* Jeśli dźwięk zawiera języki inne niż obsługiwana lista powyżej, wynik jest nieoczekiwany.
* Jeśli indeksator wideo nie może zidentyfikować języka`>0.6`z wystarczająco wysokim zaufaniem ( ), językiem rezerwowym jest angielski.
* Nie ma bieżącej obsługi plików z mieszanymi językami audio. Jeśli dźwięk zawiera języki mieszane, wynik jest nieoczekiwany. 
* Dźwięk o niskiej jakości może mieć wpływ na wyniki modelu.
* Model wymaga co najmniej jednej minuty mowy w audio.
* Model jest przeznaczony do rozpoznawania spontanicznej mowy konwersacyjnej (nie poleceń głosowych, śpiewu itp.).

## <a name="next-steps"></a>Następne kroki

* [Przegląd](video-indexer-overview.md)
* [Automatyczne identyfikowanie i transkrybowanie treści wielojęzycznych](multi-language-identification-transcription.md)
