---
title: Element LiveEvent stanów i rozliczeń w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie usługi Azure Media Services w element LiveEvent stanów i rozliczeń.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: 588aeede123848900fac6fab663dd1f6c6c169b6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719425"
---
# <a name="liveevent-states-and-billing"></a>Element LiveEvent stanów i rozliczenia

W usłudze Azure Media Services element LiveEvent rozpoczyna się rozliczeń zaraz po jego stan przechodzi do **systemem**. Aby zatrzymać element LiveEvent z rozliczeniami, musisz zatrzymać element LiveEvent.

Podczas **LiveEventEncodingType** na Twoje [element LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) jest ustawiana na standardowa, Media Services automatycznego zamknięcia systemu operacyjnego wszelkie element LiveEvent, który nadal znajduje się w **systemem** stanu 12-godzinny źródło danych wejściowych zostaną utracone, a nie **LiveOutput**uruchomione. Jednak możesz nadal jest naliczana czas był element LiveEvent **systemem** stanu.

## <a name="states"></a>Stany

Element LiveEvent może mieć jeden z następujących stanów.

|Stan|Opis|
|---|---|
|**Zatrzymana**| To jest wstępny stan element LiveEvent po utworzeniu (chyba że ustawiono automatyczne uruchamianie na wartość true.) Rozliczenia nie występuje w tym stanie. W tym stanie właściwości element LiveEvent mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.|
|**Uruchamianie**| Element LiveEvent jest uruchamiany i przydzielania zasobów. Rozliczenia nie występuje w tym stanie. Aktualizacje lub przesyłania strumieniowego nie są dozwolone w tym stanie. Jeśli wystąpi błąd, element LiveEvent powróci do stanu zatrzymany.|
|**Uruchamianie**| Wygenerowano element LiveEvent zasoby zostały przydzielone, pozyskiwania i adresy URL w wersji zapoznawczej i jest w stanie odbieranie transmisje strumieniowe na żywo. W tym momencie rozliczeń jest aktywny. Należy jawnie wywołać Stop zasobu element LiveEvent, aby zatrzymać dalsze rozliczeń.|
|**Zatrzymywanie**| Element LiveEvent jest zatrzymywana i zasoby są w rozliczeniu. Rozliczenia nie występuje w tym stanie przejściowym. Aktualizacje lub przesyłania strumieniowego nie są dozwolone w tym stanie.|
|**Usuwanie**| Element LiveEvent jest usuwana. Rozliczenia nie występuje w tym stanie przejściowym. Aktualizacje lub przesyłania strumieniowego nie są dozwolone w tym stanie.|

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
- [Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)
