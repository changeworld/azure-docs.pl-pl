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
ms.openlocfilehash: 0b1c4c728c23d8bdfe439b3a3db69b06065dad8a
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266955"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Informacje o wersji usługi Azure SQL Data Warehouse
Ten artykuł zawiera podsumowanie nowych funkcji i ulepszeń w najnowszych wersjach [Azure SQL Data Warehouse](sql-data-warehouse-overview-what-is.md). Artykuł zawiera także listę znaczące zmiany zawartości, które nie są bezpośrednio do wersji mimo opublikowane w tym samym przedziale czasu. Ulepszenia do innych usług platformy Azure, można zobaczyć [usługi aktualizacji](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>Wersja magazynu danych SQL 10.0.10106.0 (styczeń)

### <a name="service-improvements"></a>Udoskonalenia usługi

| Udoskonalenia usługi | Szczegóły |
| --- | --- |
|**Zwróć Order By optymalizacji**|WYBIERZ... Zapytania w klauzuli ORDER BY uzyskać zwiększeniu wydajności w tej wersji.   Teraz wszystkie obliczenia węzły wysyłają ich wyników do pojedynczego obliczeniowe węzła, który scala i sortuje wyniki, które są zwracane do użytkownika za pośrednictwem węzła obliczeniowego.  Scalanie za pośrednictwem wyników obliczeń pojedynczego węzła zyskania istotnie poprawiającą wydajność, gdy zestawu wyników zapytania zawiera dużą liczbę wierszy. Wcześniej aparat wykonywania zapytania może zamówić łączność obejmującą wyniki na każdym węźle obliczeniowym i przesyłać je strumieniowo do węzła kontrolnego, która będzie następnie scal wyniki.|
|**Ulepszenia przepływu danych PartitionMove i BroadcastMove**|W Gen2 magazynu danych Azure SQL, czynności przenoszenia danych typu ShuffleMove, należy użyć danych błyskawiczne przenoszenia technik przedstawionych w [blog poświęcony ulepszenia wydajności](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). W tej wersji PartitionMove i BroadcastMove typy przenoszenia danych teraz są również obsługiwane przez te same techniki błyskawiczne danych w ruchu. Lepsza wydajność kwerend użytkowników, które wykorzystują te typy czynności przenoszenia danych zostaną uruchomione. Żadne zmiany kodu musi korzystać z zalet usprawnienia dotyczące wydajności.|

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
