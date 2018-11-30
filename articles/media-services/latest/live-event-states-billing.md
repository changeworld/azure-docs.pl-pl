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
ms.openlocfilehash: 1a49f62d7b5e21fe9d6483f71b729a9100aff1a3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585553"
---
# <a name="liveevent-states-and-billing"></a>Element LiveEvent stanów i rozliczenia

W usłudze Azure Media Services element LiveEvent rozpoczyna się rozliczeń zaraz po jego stan przechodzi do **systemem**. Aby zatrzymać element LiveEvent z rozliczeniami, musisz zatrzymać element LiveEvent.

Gdy **LiveEventEncodingType** na usługi [element LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) jest ustawiana na standardowa (Basic), usługa Media Services automatycznego zamknięcia systemu operacyjnego wszelkie element LiveEvent, który nadal znajduje się w **systemem** stanu 12 godzin Po źródła danych wejściowych zostaną utracone, a nie **LiveOutput**uruchomione. Jednak możesz nadal jest naliczana czas był element LiveEvent **systemem** stanu.

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
