---
title: Uniknąć przerw w działaniu usługi z zadania usługi analiza strumienia Azure | Dokumentacja firmy Microsoft
description: Wskazówki na temat tworzenia Twojej usługi analiza strumienia zadań uaktualniania odporność.
services: stream-analytics
documentationCenter: ''
author: jseb225
manager: ryanw
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 9f5306e4cbcfc2b554a688e2d502cc4b40845d60
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Zagwarantować niezawodność zadania usługi analiza strumienia podczas aktualizacji usługi

Część jest w pełni zarządzana usługa jest możliwość wprowadzenia nowych funkcji usługi i ulepszenia w tempie szybkie. W związku z tym usługi analiza strumienia może mieć aktualizacji usługi wdrażanie na podstawie co tydzień (lub częstsze). Niezależnie od tego, ile testów jest wykonywane jest nadal ryzyko, że istniejący, uruchomione zadanie może zostać przerwane z powodu wprowadzenia usterki. Dla klientów, którzy uruchomić krytycznego zadania przesyłania strumieniowego przetwarzania te zagrożenia, należy unikać. Mechanizm klienci mogą użyć, aby zmniejszyć to ryzyko jest Azure **[sparowanego region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelu. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak regiony platformy Azure sparowanym rozwiązania tego problemu?

Analiza strumienia gwarantuje, że zadania w parach regiony są aktualizowane w oddzielnych partiach. W związku z tym jest wystarczające odstęp czasu między aktualizacjami, aby zidentyfikować potencjalne błędy podziału i usuwać z nich.

_Z wyjątkiem Indie środkowe_ (których sparowanego regionu, Indie Południowe nie ma obecności Stream Analytics), wdrożenie aktualizacji do usługi Stream Analytics nie może mieć miejsce, w tym samym czasie w zestawie regionów pary. Wdrożenia w wielu regionach **w tej samej grupie** może wystąpić **w tym samym czasie**.

Artykuł na temat **[dostępności i regiony sparowanego](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** zawiera najbardziej aktualne informacje, na którym są skojarzone regionów.

Klienci są zalecane jest wdrażanie identyczne zadania dla obu sparowanego regionów. Oprócz Stream Analytics wewnętrzny możliwości monitorowania, klienci także zalecana do monitorowania zadań tak, jakby **zarówno** zadań produkcji. Jeśli podział została zidentyfikowana jako wynik aktualizacji usługi Stream Analytics, eskalować odpowiednio i pracy awaryjnej żadnych użytkowników podrzędne w danych wyjściowych zadania w dobrej kondycji. Eskalacji do obsługi będzie zapobiec wpływowi nowe wdrożenie sparowanego regionu i zachowanie spójności sparowanego zadań.
