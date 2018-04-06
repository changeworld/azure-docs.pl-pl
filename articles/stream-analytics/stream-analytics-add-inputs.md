---
title: Dodawanie danych wejściowych do zadań usługi Stream Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak podłączenie do zadania usługi analiza strumienia jako dane wejściowe z usługi Event Hubs lub odwołanie do danych z magazynu blogu strumieniowe źródła danych.
keywords: danych wejściowych, przesyłanie strumieniowe danych
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
editor: ''
ms.assetid: 9e59bd24-2a80-4ecb-b6b2-309a07c70bcd
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: ca96f0a9abc13cd5d3cc93210c0347064c6503b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Dodaj dane przesyłane strumieniowo danych wejściowych lub odwołanie do zadania usługi analiza strumienia
Dowiedz się, jak podłączenie do zadania usługi analiza strumienia jako dane wejściowe z usługi Event Hubs lub odwołanie do danych z magazynu obiektów Blob strumieniowe źródła danych.

Zadania usługi analiza strumienia Azure można podłączyć do wprowadzania danych jednego lub więcej, z których każdy zdefiniować połączenie z istniejącym źródłem danych. Ponieważ dane są wysyłane do tego źródła danych, jest używane przez zadanie usługi analiza strumienia i przetwarzane w czasie rzeczywistym jako strumienia danych. Analiza strumienia ma pierwszej klasie integracji z [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) i [magazynu obiektów Blob Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md) w obrębie i poza zadanie subskrypcji.

W tym artykule jest krokiem w [ścieżka szkoleniowa dotycząca usługi analiza strumienia](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Dane wejściowe: przesyłanie strumieniowe danych i danych referencyjnych
Istnieją dwa różne typy dane wejściowe, Stream Analytics: strumienie danych i danych referencyjnych.

* **Strumienie danych**: zadania usługi analiza strumienia musi zawierać co najmniej jeden danych wejściowych strumienia używane i przekształcenia przez zadanie. Azure Blob storage i Azure Event Hubs są obsługiwane jako źródeł dla wejścia strumienia danych. Usługa Azure Event Hubs są używane do zbierania strumieni zdarzeń z połączonych urządzeń, usług i aplikacji. Magazyn obiektów Blob Azure może służyć jako źródło danych wejściowych do wprowadzania danych zbiorczego jako strumień.  
* **Danych referencyjnych**: analiza strumienia obsługuje drugi typ pomocniczy odwołanie do danych wejściowych o nazwie danych.  W przeciwieństwie do danych w ruchu te dane są statyczne lub spowalniając zmiana.  Zwykle służy do wyszukania i korelacji strumieni danych do utworzenia bardziej zaawansowane funkcje zestawu danych.  Magazyn obiektów Blob Azure jest obecnie obsługiwane tylko źródła danych wejściowych danych referencyjnych.  

Aby dodać dane wejściowe do zadania usługi analiza strumienia:

1. W portalu Azure kliknij **dane wejściowe** , a następnie kliknij przycisk **dodać dane wejściowe** w zadania usługi analiza strumienia.
   
    ![Portal Azure — Dodawanie danych wejściowych.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    W portalu Azure kliknij **dane wejściowe** kafelka w zadania usługi analiza strumienia.  
   
    ![Portal Azure — Dodawanie danych wejściowych.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. Określ typ danych wejściowych: albo **strumienia danych** lub **danych referencyjnych**.
   
    ![Dodaj prawidłowe dane wejściowe, strumieniowo lub odwołania](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![Dodaj prawidłowe dane wejściowe, strumieniowo lub odwołania](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. W przypadku tworzenia elementu wejściowego strumienia danych, określ typ źródła danych wejściowych.  Ten krok można pominąć podczas tworzenia danych referencyjnych jako tylko obiektu Blob magazynu jest obsługiwana w tej chwili.
   
    ![Dodawanie danych strumienia danych wejściowych](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![Dodawanie danych strumienia danych wejściowych](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. Podaj przyjazną nazwę dla tych danych wejściowych w polu Alias wejściowy.  Ta nazwa będzie używana w zapytaniu zadania kopii później do odwoływania się do danych wejściowych.
   
    Wprowadź pozostałe właściwości połączenia wymagane do nawiązania połączenia źródła danych. Te pola zależą od typu danych wejściowych i źródła i szczegółowo zdefiniowane [tutaj](stream-analytics-create-a-job.md).  
   
    ![Dodawanie zdarzeń centrum danych wejściowych](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. Określ ustawienia serializacji dla danych wejściowych:
   
   * Aby zapytania mogły działać zgodnie z oczekiwaniami, należy określić **Format serializacji zdarzeń** przychodzących danych.  Serializacja obsługiwane formaty to JSON, CSV i Avro.
   * Sprawdź **kodowanie** danych.  UTF-8 to jedyny obsługiwany obecnie format kodowania.
     
     ![Ustawienia danych serializacji w danych wejściowych](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![Ustawienia danych serializacji w danych wejściowych](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. Po zakończeniu tworzenia wejściowych, Stream Analytics sprawdzi, czy można nawiązać źródła danych wejściowych.  Stan operacji Test połączenia można wyświetlić w Centrum powiadomień.
   
    ![Testowanie połączenia dane przesyłane strumieniowo danych wejściowych](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![Testowanie połączenia dane przesyłane strumieniowo danych wejściowych](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Uzyskaj pomoc dotyczącą strumienia danych wejściowych danych
Aby uzyskać dalszą pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

