---
title: Łączenie programu Forcepoint DLP z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć program Forcepoint DLP z usługą Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588250"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Łączenie programu Forcepoint DLP z usługą Azure Sentinel

> [!IMPORTANT]
> Łącznik danych Forcepoint Data Loss Prevention (DLP) w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Łącznik Forcepoint DLP umożliwia automatyczne eksportowanie danych zdarzeń DLP do usługi Azure Sentinel. Można go użyć, aby uzyskać wgląd w działania użytkownika i zdarzenia utraty danych. Umożliwia również korelacje z danymi z obciążeń platformy Azure i innych źródeł danych oraz zwiększa możliwości monitorowania za pomocą skoroszytów w usłudze Azure Sentinel.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchomiona usługa Azure Sentinel.

## <a name="configure-and-connect-forcepoint-dlp"></a>Konfigurowanie i łączenie programu Forcepoint DLP

Skonfiguruj program Forcepoint DLP do przekazywania danych zdarzeń w formacie JSON do obszaru roboczego platformy Azure za pośrednictwem interfejsu API REST, jak wyjaśniono w [Przewodniku integracji programu Forcepoint DLP.](https://frcpnt.com/dlp-sentinel)


## <a name="find-your-data"></a>Znajdowanie danych

Po skonfigurowaniu łącznika DLP programu Forcepoint dane pojawią się w usłudze Log Analytics w obszarze **ForcepointDLPEvents_CL**Dzienniki niestandardowe .


Aby użyć odpowiedniego schematu w usłudze Log Analytics for Forcepoint DLP, wyszukaj **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności

Może upłynąć ponad 20 minut, aż dzienniki zaczną pojawiać się w usłudze Log Analytics.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak połączyć forcepoint DLP z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:

- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)