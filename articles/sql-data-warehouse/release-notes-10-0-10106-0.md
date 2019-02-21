---
title: Usługa Azure SQL Data Warehouse — informacje o wersji | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 2de7c335e56117f2a99db5150575ed94616467e9
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455589"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Informacje o wersji usługi Azure SQL Data Warehouse
Ten artykuł zawiera podsumowanie nowych funkcji i ulepszeń w najnowszych wersjach [Azure SQL Data Warehouse](sql-data-warehouse-overview-what-is.md). Artykuł zawiera także listę znaczące zmiany zawartości, które nie są bezpośrednio do wersji mimo opublikowane w tym samym przedziale czasu. Ulepszenia do innych usług platformy Azure, można zobaczyć [usługi aktualizacji](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>Wersja magazynu danych SQL 10.0.10106.0 (styczeń)

### <a name="service-improvements"></a>Udoskonalenia usługi

| Udoskonalenia usługi | Szczegóły |
| --- | --- |
|**Zwróć Order By optymalizacji**|WYBIERZ... Zapytania w klauzuli ORDER BY uzyskać zwiększeniu wydajności w tej wersji.   Teraz wszystkie obliczenia węzły wysyłają ich wyników do pojedynczego obliczeniowe węzła, który scala i sortuje wyniki, które są zwracane do użytkownika za pośrednictwem węzła obliczeniowego.  Scalanie za pośrednictwem wyników obliczeń pojedynczego węzła zyskania istotnie poprawiającą wydajność, gdy zestawu wyników zapytania zawiera dużą liczbę wierszy. Wcześniej aparat wykonywania zapytania może zamówić łączność obejmującą wyniki na każdym węźle obliczeniowym i przesyłać je strumieniowo do węzła kontrolnego, która będzie następnie scal wyniki.|
|**Ulepszenia przepływu danych PartitionMove i BroadcastMove**|W Gen2 magazynu danych Azure SQL, czynności przenoszenia danych typu ShuffleMove, należy użyć danych błyskawiczne przenoszenia technik przedstawionych w [blog poświęcony ulepszenia wydajności](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). W tej wersji PartitionMove i BroadcastMove typy przenoszenia danych teraz są również obsługiwane przez te same techniki błyskawiczne danych w ruchu. Lepsza wydajność kwerend użytkowników, które wykorzystują te typy czynności przenoszenia danych zostaną uruchomione. Żadne zmiany kodu musi korzystać z zalet usprawnienia dotyczące wydajności.|
|**Istotne błędy**|Nieprawidłowa wersja usługi Azure SQL Data Warehouse — "Wybierz@VERSION" może zwrócić wersję nieodpowiednią 10.0.9999.0. Poprawnej wersji dla bieżącej wersji jest 10.0.10106.0. Ten problem został zgłoszony i jest w trakcie przeglądu.

### <a name="documentation-improvements"></a>Udoskonalenia dokumentacji

| Udoskonalenia dokumentacji | Szczegóły |
| --- | --- |
|brak | |
| | |

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie bazy danych w usłudze SQL Data Warehouse](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Więcej informacji
- [Blog — Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Blogi zespołu doradczego klientów](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Historie sukcesu klientów](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Forum Stack Overflow](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Filmy wideo](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [słownik platformy Azure](../azure-glossary-cloud-terminology.md)
