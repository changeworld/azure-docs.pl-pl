---
title: Tworzenie wizualizacji i rozwiązywanie problemów z zadania usługi analiza strumienia Azure
description: W tym artykule opisano sposób wizualizacji zadanie usługi Stream Analytics, za pomocą funkcji diagram diagnostyki celu samodzielnego rozwiązywania problemów.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 523802f1f9a1dda19c5b6a66da7bc26fee851bd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Tworzenie wizualizacji i rozwiązywanie problemów z zadania usługi analiza strumienia
W Stream Analytics podobnie jak w przypadku innych technologii chmurowych, rozwiązywanie problemów z jest czasami potrzebne do dlaczego zadania nie tworzy oczekiwane dane wyjściowe (lub żadnych danych wyjściowych istotnego dla badania). Dzięki koncepcji na uwadze Stream Analytics zapewnia możliwość do wizualizacji zadanie przesyłania strumieniowego. To jest również przydatne jako narzędzia do modelowania i rozwiązanie po stronie tych wymagające dokumentacji ich pracy.

W panelu wizualizacji dane wejściowe są widoczne, a także zapytania i następnie wszystkie dane wyjściowe skonfigurowane. Problemy z łącznością lub konfiguracją może okazać się bardziej i może być również przydatne wyświetlić graficzną reprezentację danej konfiguracji.

## <a name="using-the-diagnosis-diagram-tool"></a>Za pomocą narzędzia diagram diagnostyki
Aby uzyskać dostęp do tego wizualizatora, po prostu kliknij przycisk "Diagnostyki diagram" w obszarze "Ustawienia" w zadaniu Stream Analytics.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Co wejściowa i wyjściowa jest zaznaczony kolorami wskazująca bieżący stan tego składnika, jak pokazano w następujący sposób.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Gdy użytkownik chce przyjrzeć się pośrednie zapytania kroki, aby zrozumieć wzorce przepływu danych wewnątrz zadania, narzędzia wizualizacji udostępnia widok podział zapytania do jej kroków składnika i sekwencja przepływu. Kliknięcie każdego kroku zapytania zawiera odpowiedniej sekcji w zapytaniu edycji okienku zgodnie z opisami. 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

