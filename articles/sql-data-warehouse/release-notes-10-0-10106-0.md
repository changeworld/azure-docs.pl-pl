---
title: Usługa Azure SQL Data Warehouse — informacje o wersji grudnia 2018 r | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 8e82e352ebea4634b1b99864245adcf606352657
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469344"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>What's new in Azure SQL Data Warehouse wersja - 10.0.10106.0?
Usługa Azure SQL Data Warehouse (usługi SQL DW) nieustannie ulepsza. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w magazyn danych SQL w wersji 10.0.10106.0.

## <a name="query-restartability---ctas-and-insertselect"></a>Zapytanie Restartability - CTAS i wstawianie/wybrać
W rzadkich sytuacjach (czyli sporadyczne problemy z połączeniem sieciowym, awarie węzłów), zapytania wykonywane w usłudze Azure SQL data Warehouse może zakończyć się niepowodzeniem. Jest już uruchomione instrukcji, takie jak [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) i operacje INSERT SELECT bardziej są widoczne dla tego problemu. W tej wersji usługi Azure SQL data Warehouse implementuje logikę ponawiania próby dla instrukcji CTAS i wybierz OPCJĘ Wstaw instrukcji (jako uzupełnienie instrukcji "SELECT" ogłoszonych wcześniej), Zezwalanie systemowi na przezroczyste obsługiwać te problemy przejściowe co uniemożliwia zapytania niepowodzenie. Liczba ponownych prób i listę błędów przejściowych, obsługiwane są skonfigurowany system.

## <a name="return-order-by-optimization"></a>Zwróć Order By optymalizacji
WYBIERZ... Zapytania w klauzuli ORDER BY uzyskać zwiększeniu wydajności w tej wersji.  Wcześniej aparat wykonywania zapytania może zamówić łączność obejmującą wyniki na każdym węźle obliczeniowym i przesyłać je strumieniowo do węzła kontrolnego, która będzie następnie scal wyniki. Za pomocą to ulepszenie wszystkich obliczeń węzłów zamiast tego wysłać ich wyników do pojedynczego obliczeniowe węzła, który następnie scala je i zwraca wyniki posortowane użytkownikowi za pośrednictwem węzła obliczeniowego.  Zapewnia to znaczące są bardziej wydajne, gdy zestawu wyników zapytania zawiera dużą liczbę wierszy.

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>Ulepszenia przepływu danych PartitionMove i BroadcastMove
W usługi Azure SQL Data Warehouse Gen2 czynności przenoszenia danych typu ShuffleMove korzystać z danych błyskawiczne przenoszenia technik przedstawionych w [ulepszenia blog poświęcony wydajności tutaj](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/).  W tej wersji PartitionMove i BroadcastMove typy przenoszenia danych teraz są również obsługiwane przez te same techniki błyskawiczne danych w ruchu.  Zapytania użytkowników, które wykorzystują te typy czynności przenoszenia danych zostaną wyświetlone w zwiększeniu wydajności.  Żadne zmiany kodu musi korzystać z zalet tych wzrost wydajności.

## <a name="next-steps"></a>Kolejne kroki
Po użytkownik podstawową wiedzę na temat usługi SQL Data Warehouse, Dowiedz się, jak szybko [utworzyć SQL Data Warehouse][create a SQL Data Warehouse]. Jeśli jesteś nowym użytkownikiem platformy Azure, może się okazać [słownik platformy Azure] [ Azure glossary] przydatne, kiedy zdobędziesz doświadczenie nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

* [Historie sukcesu klientów]
* [Blogi]
* [Żądania funkcji]
* [Filmy wideo]
* [Blogi zespołu doradczego klientów]
* [Forum Stack Overflow]
* [Twitter]


[Blogi]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogi zespołu doradczego klientów]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Historie sukcesu klientów]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Żądania funkcji]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
