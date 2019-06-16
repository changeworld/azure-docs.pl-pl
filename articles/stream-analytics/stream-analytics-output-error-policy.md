---
title: Dane wyjściowe polityk błędów w usłudze Azure Stream Analytics
description: Więcej informacji na temat obsługi zasady dostępne w usłudze Azure Stream Analytics błędów danych wyjściowych.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b31530966d2c5ca9a3f82f3e74ba349e66053a83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478938"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Zasady dotyczące błędów danych wyjściowych usługi Azure Stream Analytics
W tym artykule opisano dane wyjściowe dane obsługi błędów, zasady, które można skonfigurować w usłudze Azure Stream Analytics.

Obsługa błędów danych dane wyjściowe, które zasady mają zastosowanie tylko do błędów konwersji danych, które występują, gdy zdarzenie wyjściowe utworzone przez zadanie usługi Stream Analytics nie jest zgodny ze schematem ujścia docelowego. Można skonfigurować te zasady, wybierając opcję **ponów** lub **porzucić**. W witrynie Azure portal podczas gdy w ramach zadania usługi Stream Analytics w obszarze **Konfiguruj**, wybierz opcję **zasady dotyczące błędów** można dokonać wyboru.

![Usługa Azure Stream Analytics, dane wyjściowe błędu lokalizacji zasad](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Ponawianie próby
Gdy wystąpi błąd, Azure Stream Analytics ponownych prób, zapisywania zdarzenia, dopóki nie zakończy się pomyślnie zapisu. Brak limitu czasu dla prób. Przetwarzania zdarzeń, która ponawia próbę po pewnym czasie wszystkich kolejnych zdarzeń są blokowane. Ta opcja jest błąd danych wyjściowych domyślną obsługę zasad.

## <a name="drop"></a>Porzuć
Usługa Azure Stream Analytics będzie się zmniejszać dowolnego zdarzenia w danych wyjściowych, które powoduje błąd konwersji danych. Nie można odzyskać porzuconych zdarzeń do ponownego przetworzenia później.


Wszystkie błędy przejściowe (na przykład błędy sieci) zostaną ponowione niezależnie od konfiguracji zasad obsługi błędów, dane wyjściowe.


## <a name="next-steps"></a>Kolejne kroki
[Przewodnik rozwiązywania problemów dla usługi Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
