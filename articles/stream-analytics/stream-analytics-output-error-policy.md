---
title: Dane wyjściowe polityk błędów w usłudze Azure Stream Analytics
description: Więcej informacji na temat obsługi zasady dostępne w usłudze Azure Stream Analytics błędów danych wyjściowych.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431622"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Zasady dotyczące błędów danych wyjściowych usługi Azure Stream Analytics
W tym artykule opisano dane wyjściowe dane obsługi błędów, zasady, które można skonfigurować w usłudze Azure Stream Analytics.

Obsługa błędów danych dane wyjściowe, które zasady mają zastosowanie tylko do błędów konwersji danych, które występują, gdy zdarzenie wyjściowe utworzone przez zadanie usługi Stream Analytics nie jest zgodny ze schematem ujścia docelowego. Można skonfigurować te zasady, wybierając opcję **ponów** lub **porzucić**. W witrynie Azure portal podczas gdy w ramach zadania usługi Stream Analytics w obszarze **Konfiguruj**, wybierz opcję **zasady dotyczące błędów** można dokonać wyboru.

![Usługa Azure Stream Analytics, dane wyjściowe błędu lokalizacji zasad](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Ponawianie próby
Gdy wystąpi błąd, Azure Stream Analytics ponownych prób, zapisywania zdarzenia, dopóki nie zakończy się pomyślnie zapisu. Brak limitu czasu dla prób. Przetwarzania zdarzeń, która ponawia próbę po pewnym czasie wszystkich kolejnych zdarzeń są blokowane. Ta opcja jest błąd danych wyjściowych domyślną obsługę zasad.

## <a name="drop"></a>Porzuć
Usługa Azure Stream Analytics porzuci wszelkie zdarzenia wyjściowe powodujące błędy konwersji danych. Porzuconych zdarzeń nie można odzyskać na potrzeby ponownego przetwarzania później.


Wszystkie błędy przejściowe (na przykład błędy sieci) zostaną ponowione niezależnie od konfiguracji zasad obsługi błędów, dane wyjściowe.


## <a name="next-steps"></a>Następne kroki
[Przewodnik rozwiązywania problemów dla usługi Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
