---
title: Przegląd biblioteki usługi Azure BulkExecutor DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat biblioteki Azure rozwiązania Cosmos DB BulkExecutor, zalety używania biblioteki i jej architektury.
keywords: Moduł wykonujący zbiorczego języka Java
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: d395376ad6cf191f8f355f6308f27e525da2911f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-bulkexecutor-library-overview"></a>Przegląd biblioteki usługi Azure BulkExecutor DB rozwiązania Cosmos
 
Azure DB rozwiązania Cosmos jest usługą bazy danych szybkie, elastyczne i globalnie rozproszone, który umożliwia elastycznie skalować: 

* Duży odczytu i zapisu przepustowość (w milionach operacji na sekundę).  
* Przechowywania dużych ilości (setki terabajtów lub jeszcze więcej) danych transakcyjnych i działa za pomocą wartości prognozowanych milisekundy opóźnienia.  

Biblioteka BulkExecutor ułatwia to ogromnej przepływności i magazynu, biblioteka BulkExecutor umożliwia wykonywanie zbiorczego operacji w usłudze Azure DB rozwiązania Cosmos za pomocą importowania zbiorczego i zbiorcze aktualizacji interfejsów API. Możesz przeczytać więcej na temat funkcji biblioteki BulkExecutor w poniższych sekcjach. 

> [!NOTE] 
> Obecnie biblioteka Bulkxecutor obsługuje importowania i operacje aktualizacji i ta biblioteka jest obsługiwana przez tylko konta interfejsu API Azure rozwiązania Cosmos bazy danych SQL. Zobacz [.NET](sql-api-sdk-bulk-executor-dot-net.md) i [Java](sql-api-sdk-bulk-executor-java.md) informacje o wersji programu żadnych aktualizacji do biblioteki.
 
## <a name="key-features-of-the-bulkexecutor-library"></a>Najważniejsze funkcje biblioteki BulkExecutor  
 
* Znacznie zmniejsza zasoby obliczeniowe po stronie klienta, konieczne jest nasycenia przepływności przydzielone do kontenera. Pojedyncza aplikacja wątków, która zapisuje danych przy użyciu interfejsu API importowania zbiorczego osiąga 10 razy większa przepływność zapisu w porównaniu do aplikacji wielowątkowej, który zapisuje dane równocześnie nasycenia klienta Procesora komputera.  

* Optymalizacji abstracts żmudnych zadaniach pisania logiki aplikacji do obsługi ograniczanie żądań przekroczeń limitu czasu żądania i pozostałe wyjątki przejściowej przez wydajnie obsługuje je w bibliotece.  

* Zapewnia uproszczony mechanizm aplikacji operacji zbiorczej do skalowania w poziomie. Pojedyncze wystąpienie BulkExecutor uruchomione na maszynie Wirtualnej platformy Azure, jaką może wykorzystać większa niż 500 KB RU/s i można osiągnąć wyższy przepustowość, dodając dodatkowe wystąpienia na kliencie poszczególnych maszyn wirtualnych.  
 
* Go zbiorcze zaimportować więcej niż terabajtów danych w ciągu jednej godziny, za pomocą architektury skalowalnego w poziomie.  

* Go zbiorcze danych istniejących aktualizacji w kontenerach bazy danych Azure rozwiązania Cosmos jako poprawki. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Jak wykonujący zbiorczego czy działa? 

Po wyzwoleniu operacji zbiorczej do importowania lub zaktualizować dokumenty z partii jednostek one początkowo przesuwane do zasobników odpowiadający ich zakresem kluczy partycji bazy danych Azure rozwiązania Cosmos. W ramach każdego zasobnik, która odpowiada zakresowi klucza partycji ich są rozkładane na partie mini i każdej partii mini act jako ładunek, która zostaje zatwierdzona po stronie serwera. Biblioteka BulkExecutor ma wbudowany optymalizacje równoczesne wykonywanie tych mini-partii zarówno w obrębie oraz zakresy klucza partycji. Poniższa ilustracja przedstawia, jak BulkExecutory partii danych do różnych kluczach partycji:  

![Architektura modułu wykonującego zbiorcze](./media/bulk-executor-overview/bulk-executor-architecture.png)

Biblioteka modułu wykonującego zbiorczego upewnia się, maksymalnie wykorzystać przepływności przydzielone do kolekcji. Używa [mechanizmu kontroli przeciążenia stylu AIMD](https://tools.ietf.org/html/rfc5681) dla każdej bazy danych rozwiązania Cosmos Azure partycji zakresem kluczy do efektywnej obsługi ograniczania przepustowości i przekroczeń limitu czasu. 

## <a name="next-steps"></a>Następne kroki 
  
* Dowiedz się więcej za wypróbowanie przykładowych aplikacji korzystających z biblioteki program zbiorczego w [.NET](bulk-executor-dot-net.md) i [Java](bulk-executor-java.md).  
* Zapoznaj się z zestawu SDK BulkExecutor informacji i wersji uwagi w [.NET](sql-api-sdk-bulk-executor-dot-net.md) i [Java](sql-api-sdk-bulk-executor-java.md).
* Biblioteka modułu wykonującego zbiorczego jest zintegrowany z łącznika rozwiązania Cosmos DB Spark, aby dowiedzieć się więcej, zobacz [łącznika usługi Azure Spark DB rozwiązania Cosmos](spark-connector.md) artykułu.  
* Biblioteka BulkExecutor również jest zintegrowany z nowej wersji [łącznika Azure DB rozwiązania Cosmos](https://aka.ms/bulkexecutor-adf-v2) dla fabryki danych Azure skopiować dane.
