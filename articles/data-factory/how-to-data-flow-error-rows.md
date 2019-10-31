---
title: Obsługa wierszy błędów z mapowaniem przepływów danych w Azure Data Factory
description: Dowiedz się, jak obsłużyć błędy obcinania SQL w Azure Data Factory przy użyciu mapowania przepływów danych.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 3fe3403ad06d82ba5ccd33d2718bf0e5eff64490
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166540"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Obsługa wierszy błędów obcięcia SQL w Data Factory mapowania przepływów danych

Typowym scenariuszem w Data Factory podczas korzystania z mapowania przepływów danych jest zapisanie przekształconych danych do bazy danych Azure SQL Database. W tym scenariuszu typowym warunkiem błędu, które należy zablokować, jest możliwość obcięcia kolumny. Wykonaj następujące kroki, aby zapewnić rejestrowanie kolumn, które nie mieszczą się w docelowej kolumnie ciągu, co pozwoli na kontynuowanie przepływu danych w tych scenariuszach.

## <a name="scenario"></a>Scenariusz

1. Istnieje docelowa tabela usługi Azure SQL Database z kolumną ```nvarchar(5)``` o nazwie "name".

2. W ramach przepływu danych chcemy zmapować tytuły filmów z ujścia do tej docelowej kolumny "name".

    ![Przepływ danych filmu 1](media/data-flow/error4.png)
    
3. Problem polega na tym, że tytuł filmu nie pasuje do kolumny ujścia, która może zawierać tylko 5 znaków. Po wykonaniu tego przepływu danych zostanie wyświetlony komunikat o błędzie podobny do tego: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>Jak projektować dookoła tego stanu

1. W tym scenariuszu Maksymalna długość kolumny "name" wynosi pięć znaków. Dodajmy przekształcenie podziału warunkowego, które umożliwi nam rejestrowanie wierszy z "tytułami", które są dłuższe niż pięć znaków, a także Zezwalanie na pozostałą część wierszy, które mogą się zmieścić w tym miejscu w celu zapisu w bazie danych.

    ![podział warunkowy](media/data-flow/error1.png)

2. Ta transformacja podziału warunkowego określa maksymalną długość tytułu równą pięć. Każdy wiersz, który jest mniejszy niż lub równy pięć, przejdzie do strumienia ```GoodRows```. Wszystkie wiersze, które są większe niż pięć, przechodzą do strumienia ```BadRows```.

3. Teraz musimy zarejestrować wiersze, które nie powiodły się. Dodaj transformację ujścia do strumienia ```BadRows```, aby rejestrować. W tym miejscu będziemy "mapować teraz wszystkie pola, aby rejestrować kompletne rekordy transakcji. Jest to rozdzielany tekstem plik CSV danych wyjściowych do pojedynczego pliku w Blob Storage. Wywołamy plik dziennika "badrows. csv".

    ![Złe wiersze](media/data-flow/error3.png)
    
4. Ukończony przepływ danych jest przedstawiony poniżej. Teraz można rozdzielić wiersze błędów, aby uniknąć błędów obcinania SQL i umieścić te wpisy w pliku dziennika. W tym czasie udane wiersze mogą nadal zapisywać w naszej docelowej bazie danych.

    ![Ukończ przepływ danych](media/data-flow/error2.png)

## <a name="next-steps"></a>Następne kroki

* Utwórz resztę logiki przepływu danych, korzystając z mapowania [przekształceń](concepts-data-flow-overview.md)przepływów danych.
