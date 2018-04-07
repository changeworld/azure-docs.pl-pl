---
title: Jak skonfigurować dane wyjściowe do zadania usługi analiza strumienia Azure
description: Konfigurowanie danych wyjściowych do zadania usługi analiza strumienia | Learning segmentu ścieżki.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/26/2017
ms.openlocfilehash: 2e54b7970e4748ff56844fb06fa5177ddb207fa6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Jak skonfigurować dane wyjściowe do zadania usługi analiza strumienia

Zadania usługi analiza strumienia Azure mogą być połączone z co najmniej jeden danych dane wyjściowe, które definiowane jest połączenie z istniejącą ujścia danych. Zadania usługi analiza strumienia przetwarza i przekształca przychodzących danych, strumień danych zdarzeń dane wyjściowe są zapisywane dane wyjściowe z zadania.

Strumień danych analizy, wyniki mogą być używane do pobierania pulpitów nawigacyjnych w czasie rzeczywistym lub alerty, wyzwalają przepływy pracy przepływu danych lub po prostu archiwizowanie danych podczas przetwarzania wsadowego później. Analiza strumienia ma pierwszej klasie integracji z kilku usług platformy Azure, które opisano szczegółowo w tym miejscu.

Można dodać danych wyjściowych do zadania usługi analiza strumienia:

1. W [portalu Azure](https://portal.azure.com)Otwórz swoją pracę i kliknij **dane wyjściowe** , a następnie kliknij przycisk **Dodaj** w wyświetlonym bloku danych wyjściowych.
   
    ![Dodawanie danych wyjściowych](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
2. Podaj przyjazną nazwę dla tego raportu w **Alias wyjściowy** pole. Ta nazwa może być używana w zapytaniu zadania kopii później do odwoływania się do danych wyjściowych.  
   
    Wprowadź pozostałe właściwości połączenia wymagane do nawiązania połączenia danych wyjściowych.  Te pola zależą od typu danych wyjściowych i są zdefiniowane tutaj.  
   
    ![Wybierz typ przepływu danych](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
3. W zależności od typu danych wyjściowych może być konieczne Określ sposób serializacji lub sformatowane dane. W tym miejscu opisano ustawienia serializacji określonego dla każdego typu danych wyjściowych.
   
    Wprowadź pozostałe właściwości połączenia wymagane do nawiązania połączenia źródła danych. Te pola zależą od typu danych wejściowych i źródła i są zdefiniowane w części [artykułu Utwórz zadanie](stream-analytics-create-a-job.md).  

> [!Note]
>
> Dowolny element danych wyjściowych dodane do zadania, musi istnieć przed rozpoczęcia zadania i uruchomić zdarzeń przepływu. Na przykład jeśli używasz magazynu obiektów Blob jako dane wyjściowe zadania nie utworzy konta magazynu automatycznie. Musi być utworzony przez użytkownika, przed uruchomieniem zadania ASA.
> 
 

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

