---
title: Połącz dane squadra technologies secRMM z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane programu Squadra Technologies secRMM z usługą Azure Sentinel.
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
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588114"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Łączenie danych squadra technologies secRMM z usługą Azure Sentinel 

> [!IMPORTANT]
> Łącznik danych Squadra Technologies Security Removable Media Manager (secRMM) w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Złącze Squadra Technologies secRMM umożliwia łatwe łączenie dzienników rozwiązań zabezpieczających Squadra Technologies secRMM z usługą Azure Sentinel. Umożliwia wyświetlanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i usprawnienie badania. To złącze zapewnia wgląd w zdarzenia pamięci wymiennej USB. Integracja między Squadra Technologies secRMM i Azure Sentinel korzysta z interfejsu API REST.


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchomiona usługa Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Konfigurowanie i łączenie squadra Technologies secRMM 

Squadra Technologies secRMM może integrować i eksportować dzienniki bezpośrednio do usługi Azure Sentinel.
1. W portalu Azure Sentinel kliknij pozycję Łączniki danych i wybierz pozycję Squadra Technologies secRMM, a następnie otwórz stronę łącznika.

2. Wykonaj kroki opisane w [przewodniku po dołączaniu technologii Squadra dla usługi Azure Sentinel,](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) aby uzyskać dane Squadra secRMM w usłudze Azure Sentinel.   


## <a name="find-your-data"></a>Znajdowanie danych

Po nawiązaniu pomyślnego połączenia dane są wyświetlane w usłudze Log Analytics w obszarze SecRMM_CL CustomLogs.
Aby użyć odpowiedniego schematu w usłudze Log Analytics dla programu Squadra Technologies secRMM, wyszukaj secRMM_CL.

## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności
Może upłynąć ponad 20 minut, aż dzienniki zaczną pojawiać się w usłudze Log Analytics. 


## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć program Squadra Technologies secRMM z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)

