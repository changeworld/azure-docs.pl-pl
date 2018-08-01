---
title: Dane wyjściowe polityk błędów w usłudze Azure Stream Analytics
description: Więcej informacji na temat obsługi zasady dostępne w usłudze Azure Stream Analytics błędów danych wyjściowych.
services: stream-analytics
author: sidram
ms.author: sidram
manager: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: f854e8ce35ac9d0a99b4a7dc1cdc66724114a5c4
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391739"
---
# <a name="output-error-policy"></a>Zasady dotyczące błędów danych wyjściowych
W tym artykule opisano dane wyjściowe dane obsługi błędów, zasady, które można skonfigurować w usłudze Azure Stream Analytics.

Obsługa błędów danych dane wyjściowe, które zasady mają zastosowanie tylko do błędów konwersji danych, które występują, gdy zdarzenie wyjściowe utworzone przez zadanie usługi Stream Analytics nie jest zgodny ze schematem ujścia docelowego. Można skonfigurować te zasady, wybierając opcję **ponów** lub **porzucić**. W witrynie Azure portal podczas gdy w ramach zadania usługi Stream Analytics w obszarze **Konfiguruj**, wybierz opcję **zasady dotyczące błędów** można dokonać wyboru.

![Zasady dotyczące błędów — lokalizacji](./media/stream-analytics-error-policy/stream-analytics-error-policy-locate.PNG)


## <a name="retry"></a>Ponawianie próby
Gdy wystąpi błąd, Azure Stream Analytics ponownych prób, zapisywania zdarzenia, dopóki nie zakończy się pomyślnie zapisu. Brak limitu czasu dla prób. Przetwarzania zdarzeń, która ponawia próbę po pewnym czasie wszystkich kolejnych zdarzeń są blokowane. Ta opcja jest błąd danych wyjściowych domyślną obsługę zasad.

## <a name="drop"></a>Porzuć
Usługa Azure Stream Analytics będzie się zmniejszać dowolnego zdarzenia w danych wyjściowych, które powoduje błąd konwersji danych. Nie można odzyskać porzuconych zdarzeń do ponownego przetworzenia później.


Wszystkie błędy przejściowe (na przykład błędy sieci) zostaną ponowione niezależnie od konfiguracji zasad obsługi błędów, dane wyjściowe.


## <a name="next-steps"></a>Kolejne kroki
[Przewodnik rozwiązywania problemów dla usługi Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
