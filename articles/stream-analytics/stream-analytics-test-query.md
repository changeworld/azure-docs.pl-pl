---
title: Azure Stream Analytics query testowania | Dokumentacja firmy Microsoft
description: Jak przetestować zapytań w zadania usługi analiza strumienia.
keywords: Testowanie kwerendy, rozwiązywanie problemów z kwerendy
documentation center: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 50bfce426bf48ba986887f8a2e2873fe04ea2507
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Testowanie zapytań usługi Azure Stream Analytics w portalu Azure

Z usługi Azure Stream Analytics można przetestować zapytań w portalu Azure, bez konieczności uruchamiania lub zatrzymywania zadania.

## <a name="test-the-input"></a>Dane wejściowe testu

1. Aby przetestować przykładowych danych wejściowych, kliknij prawym przyciskiem myszy dane wejściowe, a następnie wybierz **przekazać dane przykładowe z pliku**. Obecnie można przekazać tylko dane w formacie JSON. Jeśli dane są w różnych formatach, takich jak CSV, należy ją przekonwertować na format JSON, przed przesłaniem. Można użyć dowolnego narzędzia konwersji opensource, takich jak [woluminu CSV do przetwarzania JSON](http://www.convertcsv.com/csv-to-json.htm) można przekonwertować danych na notację JSON.

    ![Edytor testów zapytania zapytań usługi Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. Po zakończeniu pobierania kliknij **Test** do przetestowania tego zapytania dotyczącego podano przykładowe dane.

    ![Edytor testów przykładowych danych zapytań usługi Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

Dane wyjściowe kwerendy jest wyświetlany w przeglądarce, za pomocą linku wyniki pobierania powinien chcesz zapisać wyniki testu do późniejszego użycia. Można teraz łatwo i wielokrotnie powtarzane zmodyfikuj zapytanie i przetestować go wielokrotnie, aby zobaczyć, jak zmiany danych wyjściowych.

![Edytor przykładowe dane wyjściowe zapytań usługi Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Wielu wyjść użytych w zapytaniu możesz wyświetlić wyniki zarówno dane wyjściowe oddzielnie i łatwo przełączać się między nimi.

Po zakończeniu wyniki wyświetlane w przeglądarce, można zapisać zapytania, uruchom zadanie i pozwolić przetworzyć zdarzenia bez błędów.

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
