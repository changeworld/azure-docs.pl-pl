---
title: LiveEvent Stany i rozliczenia w Azure Media Services | Microsoft Docs
description: Ten temat zawiera Azure Media Services Omówienie LiveEvent stanów i rozliczeń.
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
ms.openlocfilehash: af3d4b51dadfaa99a166ca0ce475c5a110d8f6e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933677"
---
# <a name="live-event-states-and-billing"></a>Stany wydarzeń na żywo i rozliczenia

W Azure Media Services wydarzenie na żywo rozpoczyna rozliczanie od razu po zmianie stanu na **uruchomiony**. Aby zatrzymać rozliczanie wydarzenia na żywo, należy zatrzymać wydarzenie na żywo.

Gdy **LiveEventEncodingType** w [zdarzeniu na żywo](https://docs.microsoft.com/rest/api/media/liveevents) ma wartość standardowa lub Premium1080p, Media Services automatycznie zamyka wszystkie zdarzenia na żywo, które nadal w stanie **uruchomienia** 12 godzin po utracie danych wejściowych, i nie ma żadnych **danych wyjściowych na żywo** s uruchomione. Jednak nadal będą naliczane opłaty za czas **działania** zdarzenia na żywo.

> [!NOTE]
> Zdarzenia przekazywane na żywo nie są automatycznie wyłączane i muszą zostać jawnie zatrzymane za pomocą interfejsu API, aby uniknąć nadmiernego rozliczania. 

## <a name="states"></a>Stany

Wydarzenie na żywo może być w jednym z następujących stanów.

|Stan|Opis|
|---|---|
|**Przerwać**| Jest to początkowy stan zdarzenia na żywo po utworzeniu (chyba że Autostart został ustawiony na wartość true). W tym stanie nie ma rozliczeń. W tym stanie właściwości zdarzenia na żywo mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.|
|**Uruchamianie**| Trwa Rozpoczynanie zdarzenia na żywo i alokowanie zasobów. W tym stanie nie ma rozliczeń. Aktualizacje lub przesyłanie strumieniowe są niedozwolone w tym stanie. Jeśli wystąpi błąd, zdarzenie na żywo powróci do stanu zatrzymania.|
|**Uruchomiono**| Zasoby zdarzeń na żywo zostały przydzieloną, Wygenerowano adresy URL pozyskiwania i podglądu oraz można odbierać strumienie na żywo. W tym momencie rozliczenia są aktywne. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia.|
|**Zatrzymanie**| Zdarzenie na żywo jest przerywane, a zasoby są zwalniane. W tym stanie przejściowym nie ma rozliczeń. Aktualizacje lub przesyłanie strumieniowe są niedozwolone w tym stanie.|
|**Usunąć**| Zdarzenie na żywo jest usuwane. W tym stanie przejściowym nie ma rozliczeń. Aktualizacje lub przesyłanie strumieniowe są niedozwolone w tym stanie.|

## <a name="next-steps"></a>Następne kroki

- [Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)
- [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
