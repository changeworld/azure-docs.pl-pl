---
title: Debugowania zapytań usługi analiza strumienia Azure przy użyciu SELECT INTO | Dokumentacja firmy Microsoft
description: Przykładowe zapytanie pośredniej danych przy użyciu instrukcji SELECT INTO w analiza strumienia
keywords: ''
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: f232ba9804886f416ac0941ddc7e602782b6ebe2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="debug-queries-by-using-select-into-statements"></a>Debugowania zapytania przy użyciu instrukcji SELECT INTO

Podczas przetwarzania danych w czasie rzeczywistym wiedząc, jak wygląda danych w trakcie wykonywania zapytania mogą być pomocne. Ponieważ dane wejściowe lub kroki zadanie usługi analiza strumienia Azure może zostać odczytany wiele razy, można napisać dodatkowy instrukcji SELECT INTO. Dzięki temu generuje pośrednich danych do magazynu i można sprawdzić poprawności danych, podobnie jak *Obejrzyj zmienne* czy podczas debugowania programu.

## <a name="use-select-into-to-check-the-data-stream"></a>Użyj SELECT INTO w celu sprawdzenia strumienia danych

Poniższe przykładowe zapytanie zadanie usługi analiza strumienia Azure ma jednego strumienia danych wejściowych, dwóch parametrów wejściowych danych odwołania, a dane wyjściowe do magazynu tabel Azure. Zapytanie sprzężenia danych z Centrum zdarzeń i obiekty BLOB dwa odwołania można pobrać informacji o nazwie i kategorii:

![Przykładowe zapytanie SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Należy pamiętać, że zadanie zostało uruchomione, ale żadne zdarzenia nie są tworzonym w danych wyjściowych. Na **monitorowanie** kafelka, w tym miejscu pokazano widać, że danych wejściowych jest zwracające dane, ale nie wiadomo, które kroku **JOIN** spowodował wszystkich zdarzeń ma być przerwane.

![Na kafelku monitorowanie](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
W takiej sytuacji można dodać kilka dodatkowych instrukcji SELECT INTO "logowania" pośrednich wyników sprzężenia, a dane są odczytywane z danych wejściowych.

W tym przykładzie dodano dwa nowe "tymczasowego danych wyjściowych." Mogą to być wszystkie zbiornika, który chcesz. W tym miejscu używamy usługi Azure Storage, na przykład:

![Dodawanie dodatkowych instrukcji SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Następnie można ponownie napisać zapytanie następująco:

![Ponownie zapisane zapytanie SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Teraz ponownie uruchomić zadanie i pozwól mu Uruchom kilka minut. Następnie zapytania temp1 i temp2 z programu Visual Studio Cloud Explorer wygenerowało następujących tabel:

**Tabela temp1**
![SELECT INTO tabeli temp1](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**Tabela temp2**
![SELECT INTO tabeli temp2](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak widać, temp1 i temp2 znajdują się dane, a w temp2 kolumnie Nazwa została wpisana poprawnie. Jednak ponieważ nie jeszcze żadnych danych w danych wyjściowych, element jest nieprawidłowy:

![Wybierz do tabeli output1 bez danych](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Poprzez próbkowanie danych, możesz mieć niemal pewność, że problem dotyczy drugi sprzężenia. Możesz pobrać z obiektu blob danych referencyjnych i zapoznaj się z:

![Wybierz do tabeli referencyjnej](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak widać, format identyfikatora GUID w tym danych referencyjnych różni się od formatu [z] kolumny temp2. Dlatego dane nie zostały dostarczone w output1 zgodnie z oczekiwaniami.

Można rozwiązać format danych, przekaż go do odwoływania się do obiektów blob i spróbuj ponownie:

![Wybierz do tabeli tymczasowej](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Dane w danych wyjściowych ten czas jest sformatowany i wypełniane zgodnie z oczekiwaniami.

![Wybierz do tabeli końcowej](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

