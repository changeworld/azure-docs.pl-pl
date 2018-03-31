---
title: Jak utworzyć zadanie przetwarzania analizy danych dla usługi Stream Analytics | Dokumentacja firmy Microsoft
description: Utwórz zadanie przetwarzania analizy danych dla usługi Stream Analytics | Learning segmentu ścieżki.
keywords: przetwarzanie analizy danych
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 03f21c076624ee758d313c4f58e29c616226de57
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Jak utworzyć zadanie przetwarzania analizy danych dla usługi analiza strumienia
Zasób najwyższego poziomu w Azure Stream Analytics jest zadania usługi analiza strumienia.  Składa się z co najmniej jedno źródło danych wejściowych, zapytania, przedstawiając przekształcania danych i co najmniej jeden wyjściowy obiektów docelowych, które wyniki są zapisywane do. Razem te umożliwiają użytkownikowi wykonywać analizy danych przetwarzania do strumieniowego przesyłania danych.

Aby rozpocząć korzystanie z usługi Stream Analytics, Rozpocznij od utworzenia nowego zadania usługi analiza strumienia.  Należy zauważyć, że ta akcja nie daje żadnych skutków rozliczeń do momentu rozpoczęcia zadania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz **Utwórz zasób** > **dane i analiza** > **zadanie usługi Stream Analytics**.
3. Wybierz pozycję **Utwórz**.
   
3. Określ odpowiednią konfigurację zadania usługi analiza strumienia.
   
   * W **Nazwa zadania** wprowadź nazwę identyfikującą zadania usługi analiza strumienia. Gdy **Nazwa zadania** jest weryfikowane, w polu Nazwa zadania zostanie wyświetlony zielony znacznik wyboru. **Nazwa zadania** może zawierać tylko znaki alfanumeryczne oraz '-' znaków i musi zawierać od 3 do 63 znaków.
   * Użyj **lokalizacji** można określić lokalizację geograficzną, w którym chcesz uruchomić zadanie.
   * Określ nową lub istniejącą **grupy zasobów** do przechowywania powiązane zasoby aplikacji.
4. Wybierz pozycję **Utwórz**.
Może upłynąć kilka minut, aż zadanie usługi analiza strumienia, który ma zostać utworzony. Aby sprawdzić stan, można monitorować postęp w Centrum powiadomień.
    
   ![Azure w portalu analityka danych przetwarzania zadania tworzenia zadania](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Nowe zadanie będzie wyświetlany stan **utworzony**. Zwróć uwagę, że **Start** przycisk jest niedostępny. Przed rozpoczęciem zadania, należy skonfigurować dane wejściowe zadania, zapytań i danych wyjściowych.

   
   ![Azure w portalu analityka danych przetwarzania stanu zadania](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

