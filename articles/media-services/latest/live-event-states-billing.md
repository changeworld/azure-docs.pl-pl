---
title: Stany LiveEvent i rozliczenia w usłudze Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono omówienie stanów usługi Azure Media Services LiveEvent i rozliczeń.
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
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543079"
---
# <a name="live-event-states-and-billing"></a>Stany zdarzeń na żywo i rozliczenia

W usłudze Azure Media Services zdarzenie na żywo rozpoczyna się od rozliczeń, gdy tylko jego stan przejdzie do **uruchomionego**. Zostanie naliczona naliczona, nawet jeśli przez usługę nie przepływa żaden film. Aby zatrzymać rozliczenia za wydarzenie na żywo, musisz zatrzymać wydarzenie na żywo. Transkrypcja na żywo jest rozliczana w taki sam sposób, jak wydarzenie na żywo.

Gdy **LiveEventEncodingType** w [zdarzeniu](https://docs.microsoft.com/rest/api/media/liveevents) na żywo jest ustawiona na Standard lub Premium1080p, Usługa Media Services automatycznie wyłącza wszelkie zdarzenia na żywo, które jest nadal w stanie **uruchomione** 12 godzin po utracie źródła danych wejściowych i nie ma żadnych **danych wyjściowych na żywo**s uruchomiony. Jednak nadal będą naliczane płatności za czas, w którym wydarzenie na żywo było w stanie **uruchomiona.**

> [!NOTE]
> Przekazywanie zdarzeń na żywo nie są automatycznie wyłączane i muszą być jawnie zatrzymane za pośrednictwem interfejsu API, aby uniknąć nadmiernego fakturowania. 

## <a name="states"></a>Stany

Wydarzenie na żywo może znajdować się w jednym z następujących stanów.

|Stan|Opis|
|---|---|
|**Zatrzymano**| Jest to początkowy stan zdarzenia na żywo po utworzeniu (chyba że autostart został ustawiony na true.) W tym stanie nie ma żadnych rozliczeń. W tym stanie właściwości zdarzenia na żywo można zaktualizować, ale przesyłanie strumieniowe jest niedozwolone.|
|**Zaczynając**| Wydarzenie na żywo jest uruchamiane i przydzielane są zasoby. W tym stanie nie ma żadnych rozliczeń. Aktualizacje lub przesyłanie strumieniowe nie są dozwolone w tym stanie. Jeśli wystąpi błąd, zdarzenie na żywo powraca do stanu Zatrzymane.|
|**Uruchomiono**| Zasoby wydarzenia na żywo zostały przydzielone, wygenerowano adresy URL pozyskiwania i wersji zapoznawczej i mogą odbierać transmisje na żywo. W tym momencie rozliczenia są aktywne. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia.|
|**Zatrzymywanie**| Zdarzenie na żywo jest zatrzymywany i zasoby są usuwane z aprowizowania. W tym przejściowym stanie nie ma żadnych rozliczeń. Aktualizacje lub przesyłanie strumieniowe nie są dozwolone w tym stanie.|
|**Usuwanie**| Wydarzenie na żywo jest usuwane. W tym przejściowym stanie nie ma żadnych rozliczeń. Aktualizacje lub przesyłanie strumieniowe nie są dozwolone w tym stanie.|

Podczas tworzenia wydarzenia na żywo można włączyć transkrypcje na żywo. Jeśli to zrobisz, zostanie naliczona naliczona naliczona faktura za transkrypcje na żywo, gdy zdarzenie na żywo jest w stanie **uruchomiona.** Pamiętaj, że opłata zostanie naliczona, nawet jeśli przez wydarzenie na żywo nie przepływa dźwięk.

## <a name="next-steps"></a>Następne kroki

- [Omówienie transmisji na żywo](live-streaming-overview.md)
- [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
