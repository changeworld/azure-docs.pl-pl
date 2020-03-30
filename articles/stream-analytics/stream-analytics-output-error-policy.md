---
title: Zasady błędów danych wyjściowych w usłudze Azure Stream Analytics
description: Dowiedz się więcej o zasadach obsługi błędów wyjściowych dostępnych w usłudze Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431622"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Zasady błędów danych wyjściowych usługi Azure Stream Analytics
W tym artykule opisano zasady obsługi błędów danych wyjściowych, które można skonfigurować w usłudze Azure Stream Analytics.

Zasady obsługi błędów danych wyjściowych mają zastosowanie tylko do błędów konwersji danych, które występują, gdy zdarzenie wyjściowe produkowane przez zadanie usługi Stream Analytics nie jest zgodne ze schematem obiektu docelowego. Tę zasadę można skonfigurować, wybierając opcję **Ponów próbę** lub **Upuść**. W witrynie Azure portal, podczas gdy w zadaniu usługi Stream Analytics, w obszarze **Konfigurowanie**, wybierz **zasady błędów,** aby dokonać wyboru.

![Lokalizacja zasad błędu danych wyjściowych usługi Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Ponawianie próby
Gdy wystąpi błąd, usługa Azure Stream Analytics ponawia procesy zapisywania zdarzenia przez czas nieokreślony, dopóki zapis nie powiedzie się. Nie ma limitu czasu dla ponownych prób. Po pewnym czasie wszystkie kolejne zdarzenia są blokowane z przetwarzania przez zdarzenie, które jest ponawiane. Ta opcja jest domyślną zasadą obsługi błędów danych wyjściowych.

## <a name="drop"></a>Upuść
Usługa Azure Stream Analytics porzuci wszelkie zdarzenia wyjściowe powodujące błędy konwersji danych. Porzuconych zdarzeń nie można odzyskać na potrzeby ponownego przetwarzania później.


Wszystkie błędy przejściowe (na przykład błędy sieciowe) są ponawiane niezależnie od konfiguracji zasad obsługi błędów wyjściowych.


## <a name="next-steps"></a>Następne kroki
[Przewodnik dotyczący rozwiązywania problemów z usługą Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
