---
title: Mapowanie przekształcenie obiektu Sink przepływu danych w usłudze Azure Data Factory
description: Mapowanie przekształcenie obiektu Sink przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 795b8072bbd9b248f982d061d699f490b1b63b17
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272116"
---
# <a name="azure-data-factory-mapping-data-flow-sink-transformation"></a>Mapowanie przekształcenie obiektu Sink przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Opcje ujścia](media/data-flow/windows1.png "ujścia 1")

Po ukończeniu swojej Przekształcanie przepływu danych można ujścia przekształconych danych do docelowego zestawu danych. Podczas przekształcania ujścia możesz wybrać definicji zestawu danych, które mają być używane miejsce docelowe danych wyjściowych.

Powszechną praktyką konta dla zmieniających się danych przychodzących i uwzględnić dryfu schematu jest ujście danych wyjściowych do folderu bez zdefiniowanego schematu w wyjściowy zestaw danych. Można również uwzględnione wszystkie zmiany kolumny w źródłach, wybierając pozycję "Zezwalaj na schemat odejściem od tego stanu" w źródłowej, a następnie automatycznego wszystkie pola w ujściu.

Można zastąpić, dołączania lub się nie powieść przepływ danych wychwytywania do zestawu danych.

Możesz również "Mapuj automatycznie" do ujścia wszystkie pola przychodzących. Jeśli chcesz wybrać pola, które mają być zlew do miejsca docelowego lub chcesz zmienić nazwy pól w miejscu docelowym, to należy wybrać "Off" Mapuj "automatycznie", a następnie kliknij na karcie mapowania, aby zamapować pola danych wyjściowych:

![Opcje ujścia](media/data-flow/sink2.png "ujścia 2")

## <a name="output-to-one-file"></a>Dane wyjściowe do jednego pliku
W przypadku typów ujścia usługi Azure Storage Blob lub Data Lake zwróci przekształconych danych do folderu. Spark spowoduje wygenerowanie plików danych partycjonowanych danych wyjściowych oparte na schemat partycjonowania używany w transformacji ujścia. Możesz ustawić schematu partycjonowania, klikając kartę "Optymalizuj". Jeśli chcesz ADF, aby scalić dane wyjściowe do pojedynczego pliku, kliknij przycisk radiowy "Jednej partycji".

![Opcje ujścia](media/data-flow/opt001.png "ujścia opcje")

## <a name="blob-storage-folder"></a>Folder magazynu obiektów blob
Podczas wychwytywania przekształceń danych do obiektu Blob Store, wybierz obiekt blob *folderu* jako ścieżki folderu docelowego, nie jest plik. Przepływ danych ADF wygeneruje pliki wyjściowe dla Ciebie w tym folderze.

![Ścieżka folderu](media/data-flow/folderpath.png "ścieżka folderu")

## <a name="optional-azure-sql-data-warehouse-sink"></a>Ujścia magazynu danych opcjonalne usługi Azure SQL

Udostępniamy wczesne beta zestaw danych będący ujściem ADW dla przepływu danych. Pozwoli to powiązana przekształcone dane bezpośrednio do usługi Azure SQL data Warehouse w ramach przepływu danych bez konieczności dodawania działania kopiowania w potoku.

Rozpocznij od utworzenia zestawu danych ADW, tak jak w przypadku wszelkich innych potoku usługi ADF, za pomocą połączonej usługi, która obejmuje poświadczenia ADW i wybierz bazę danych, którą chcesz nawiązać połączenie. Nazwa tabeli wybierz istniejącą tabelę lub wpisz nazwę tabeli, czy chcesz się przepływ ma automatycznie utworzyć z przychodzącego pól danych.

![Opcje ujścia](media/data-flow/adw3.png "ujścia 3")

Po powrocie na przekształcenia ujścia (ADW jest obecnie obsługiwane tylko jako obiekt Sink) należy wybrać zestaw danych ADW, który został utworzony, a także konta magazynu, które chcesz używać do przemieszczania danych dla programu Polybase ładowania ADW. W polu Ścieżka ma format: "containername/foldername".

![Opcje ujścia](media/data-flow/adw1.png "ujścia 4")

### <a name="save-policy"></a>Zapisz zasady

Zastępowanie spowoduje obciąć tabeli, jeśli istnieje, a następnie ponownie go utworzyć i załadować dane. Dołącz będzie wstawianie nowych wierszy. Jeśli tabeli na podstawie nazwy tabeli zestawu danych nie istnieje na wszystkich docelowych ADW, przepływ danych będzie utworzyć tabelę, a następnie załadowanie danych.

Jeśli wyłączysz "Map automatycznie", można mapować pól do tabeli docelowej ręcznie.

![Obiekt sink opcje ADW](media/data-flow/adw2.png "adw ujścia")

### <a name="max-concurrent-connections"></a>Maksymalna liczba równoczesnych połączeń

Maksymalna liczba równoczesnych połączeń można ustawić w transformacji ujścia, podczas zapisywania danych do połączenia z bazą danych Azure.

![Opcje połączenia](media/data-flow/maxcon.png "połączeń")
