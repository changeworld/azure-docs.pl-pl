---
title: Debugowanie zapytań usługi Azure Stream Analytics przy użyciu SELECT INTO
description: W tym artykule opisano przykładowe zapytanie środku danych w ramach zadania usługi Azure Stream Analytics przy użyciu instrukcji SELECT INTO w składni zapytań.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: b056d4c29464451d3dc0ef62437f934535820489
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697996"
---
# <a name="debug-queries-by-using-select-into-statements"></a>Debugowanie zapytań przy użyciu instrukcji SELECT INTO

W przetwarzaniu danych w czasie rzeczywistym wiedząc, jak wygląda danych w trakcie wykonywania zapytania mogą być pomocne. Ponieważ dane wejściowe lub kroki zadania usługi Azure Stream Analytics może zostać odczytany wielokrotnie, można napisać dodatkowych instrukcji SELECT INTO. Wykonanie tej czynności wysyła pośrednie dane do magazynu co pozwala sprawdzić poprawność danych, podobnie jak *Obserwuj zmienne* czy podczas debugowania programu.

## <a name="use-select-into-to-check-the-data-stream"></a>Użycie SELECT INTO w celu sprawdzenia strumień danych

Poniższe przykładowe zapytanie zadania usługi Azure Stream Analytics ma jeden strumień danych wejściowych, dwa wejścia danych referencyjnych i dane wyjściowe do usługi Azure Table Storage. Zapytanie łączy dane z Centrum zdarzeń i odwołanie do dwóch obiektów blob, można pobrać informacji o nazwie i kategorii:

![Przykładowe zapytanie SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Należy pamiętać, że zadanie zostało uruchomione, ale żadne zdarzenia nie jest generowany w danych wyjściowych. Na **monitorowanie** kafelka, pokazano tutaj widać, że dane wejściowe jest tworzenie danych, ale nie wiesz, który krok **Dołącz** spowodowane wszystkich zdarzeń, które ma zostać przerwane.

![Kafelek monitorowanie](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
W takiej sytuacji można dodać kilka dodatkowych instrukcji SELECT INTO "logowania" wyniki pośrednie sprzężenia i danych, które są odczytywane z danych wejściowych.

W tym przykładzie dodaliśmy dwa nowe "tymczasowe dane wyjściowe." Mogą być dowolnego ujścia, jaką chcesz. Tutaj używamy usługi Azure Storage, na przykład:

![Dodawanie dodatkowych instrukcji SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Następnie można ponownie napisać zapytanie następująco:

![Nowych zapytanie SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Teraz ponownie uruchomić zadanie i pozwolić mu działać przez kilka minut. Następnie utworzyć zapytanie względem temp1 i temp2 za pomocą programu Visual Studio Cloud Explorer do produkcji w poniższych tabelach:

**Tabela temp1**
![SELECT INTO tabeli temp1](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**Tabela temp2**
![SELECT INTO tabeli temp2](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak widać, temp1 i temp2 znajdują się dane, a w temp2 w kolumnie Nazwa została wpisana poprawnie. Jednakże ponieważ nadal nie ma danych w danych wyjściowych, coś jest nie tak:

![Polecenie SELECT INTO tabeli output1 bez danych](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Przez pobieranie próbek danych, użytkownik może być prawie pewne, czy problem dotyczy drugi sprzężenia. Można pobrać danych referencyjnych z magazynu obiektów blob i zapoznaj się z:

![Wybierz do tabeli referencyjnej](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak widać, format identyfikatora GUID w tymi danymi referencyjnymi różni się od formatu [z] kolumny temp2. Dlatego dane nie dotrze do output1, zgodnie z oczekiwaniami.

Można naprawić format danych, przekaż go do odwoływać się do obiektu blob, a następnie spróbuj ponownie:

![Wybierz do tabeli tymczasowej](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tym razem danych w danych wyjściowych jest sformatowany i wypełniane zgodnie z oczekiwaniami.

![Wybierz do tabeli końcowej](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

