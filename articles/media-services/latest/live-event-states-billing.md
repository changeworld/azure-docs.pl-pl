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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 17fead25840e45f98478a6eb6c146bcc261dfe75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322322"
---
# <a name="live-event-states-and-billing"></a>Stany zdarzenia na żywo i rozliczeń

W usłudze Azure Media Services na żywo zawodów rozliczeń zaraz po jego stan przechodzi do **systemem**. Aby zatrzymać wydarzenie na żywo z rozliczeniami, musisz zatrzymać wydarzenie na żywo.

Podczas **LiveEventEncodingType** na Twoje [wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) jest ustawiana na standardowa, Media Services automatycznego zamknięcia systemu operacyjnego dowolne zdarzenie na żywo, która nadal znajduje się w **systemem** stanu 12-godzinny źródło danych wejściowych zostaną utracone, a nie **na żywo dane wyjściowe**uruchomione. Jednakże, możesz nadal jest naliczana czas umieszczenia zdarzenia na żywo w **systemem** stanu.

## <a name="states"></a>Stany

Wydarzenie na żywo może mieć jeden z następujących stanów.

|Stan|Opis|
|---|---|
|**Zatrzymana**| To jest wstępny stan wydarzenie na żywo po utworzeniu (chyba że autostart została ustawiona na wartość true.) Rozliczenia nie występuje w tym stanie. W tym stanie właściwości wydarzenie na żywo mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.|
|**Uruchamianie**| Wydarzenie na żywo jest uruchamiana i przydzielania zasobów. Rozliczenia nie występuje w tym stanie. Aktualizacje lub przesyłania strumieniowego nie są dozwolone w tym stanie. Jeśli wystąpi błąd, wydarzenie na żywo wróci do stanu zatrzymany.|
|**Uruchomiono**| Wygenerowano wydarzenie na żywo zasoby zostały przydzielone, pozyskiwania i adresy URL w wersji zapoznawczej i jest w stanie odbieranie transmisje strumieniowe na żywo. W tym momencie rozliczeń jest aktywny. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia.|
|**Zatrzymywanie**| Wydarzenie na żywo jest zatrzymywana i zasoby są w rozliczeniu. Rozliczenia nie występuje w tym stanie przejściowym. Aktualizacje lub przesyłania strumieniowego nie są dozwolone w tym stanie.|
|**Usuwanie**| Wydarzenie na żywo jest usuwana. Rozliczenia nie występuje w tym stanie przejściowym. Aktualizacje lub przesyłania strumieniowego nie są dozwolone w tym stanie.|

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
- [Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)
