---
title: Jak utworzyć zadanie przetwarzania analizy danych dla usługi Azure Stream Analytics
description: Utwórz zadanie przetwarzania analizy danych dla usługi Stream Analytics | Learning segmentu ścieżki.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 349d99a003df4fd211842033a467aa3051b50c77
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
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
Aby uzyskać dalszą pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

