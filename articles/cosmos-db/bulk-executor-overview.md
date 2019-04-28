---
title: Zbiorcze importowanie i zaktualizować dane w usługi Azure Cosmos DB przy użyciu biblioteki przetwarzania zbiorczego
description: Wykonywały operacje zbiorcze w usłudze Azure Cosmos DB za pomocą importowania zbiorczego i Zbiorcza aktualizacja interfejsów API oferowane przez bibliotekę przetwarzania zbiorczego.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: e0176af0ce77e9306f6f0031122f8ba8a7b27e61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894476"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Omówienie biblioteki wykonawca zbiorczego usługi Azure Cosmos DB
 
Usługa Azure Cosmos DB to usługa szybkie, elastyczne i globalnie rozproszonej bazy danych, która umożliwia elastycznie skalować w poziomie: 

* Duże odczytu i zapisu przepustowość (w milionach operacji na sekundę).  
* Przechowywanie dużych ilości (setki terabajtów lub jeszcze więcej) danych transakcyjnych i operacyjnych przy opóźnieniu przewidywalne Milisekunda.  

Biblioteka przetwarzania zbiorczego pomaga korzystać z tej ogromnej przepustowości i magazynu. Biblioteka przetwarzania zbiorczego można wykonywać zbiorcze operacje w usłudze Azure Cosmos DB za pomocą importowania zbiorczego i wykonywania zbiorczych aktualizacji interfejsów API. Możesz dowiedzieć się więcej o funkcjach biblioteki przetwarzania zbiorczego w poniższych sekcjach. 

> [!NOTE] 
> Obecnie zbiorcze wykonawca biblioteka obsługuje import i operacje aktualizacji i ta biblioteka jest obsługiwana przez tylko konta interfejsu API SQL usługi Azure Cosmos DB i interfejs API Gremlin.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Najważniejsze funkcje biblioteki przetwarzania zbiorczego  
 
* Zmniejsza to znacznie zasoby obliczeniowe po stronie klienta, potrzebne do saturate przepływności przydzielanych do kontenera. Pojedynczej aplikacji wielowątkowych, która zapisuje dane przy użyciu interfejsu API importu zbiorczego osiąga 10 razy większą przepływność zapisu w porównaniu do aplikacji wielowątkowej, która zapisuje dane w sposób równoległy określanie nasycenia klienta Procesora komputera.  

* Jego wagę monotonnych zadań pisania logiki aplikacji w celu obsługi żądania, przekroczenia limitu czasu żądania i inne przejściowych wyjątków ograniczania szybkości dzięki efektywnej obsłudze je w bibliotece.  

* Zapewnia uproszczoną mechanizm dla aplikacji wykonywania zbiorczych operacji skalowania w poziomie. Wystąpienie funkcji wykonawczej pojedynczej zbiorczej, uruchomione na Maszynie wirtualnej platformy Azure mogą wykorzystywać większy niż 500 KB jednostek RU/s i wyższy poziom przepływności można osiągnąć przez dodanie dodatkowych wystąpień na kliencie poszczególnych maszyn wirtualnych.  
 
* Jej zbiorcze importowanie ponad terabajta danych w ciągu godziny, przy użyciu architektury skalowalnego w poziomie.  

* Jego zbiorczej aktualizacji istniejących danych w kontenerach usługi Azure Cosmos DB jako poprawki. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Jak będzie działać przetwarzania zbiorczego 

Po wyzwoleniu operacji zbiorczej do importowania lub aktualizowanie dokumentów z usługą batch jednostek są początkowo przekazanych do zasobników odpowiadający ich zakres kluczy partycji usługi Azure Cosmos DB. W ramach każdego przedziału, który odpowiada zakres kluczy partycji one są podzielone na mini partie i działanie każdego mini usługi batch jako ładunek, która stawia po stronie serwera. Biblioteka przetwarzania zbiorczego ma wbudowane optymalizacje dla wykonania tych mini-partii w obrębie i różnych zakresów kluczy partycji. Poniższy rysunek ilustruje, jak zbiorczo wykonawca partii danych do różnych kluczach partycji:  

![Architektura przetwarzania zbiorczego](./media/bulk-executor-overview/bulk-executor-architecture.png)

Biblioteka przetwarzania zbiorczego zapewnia, że przechowywany wykorzystanie przepustowości, przydzielone do kolekcji. Używa ona [mechanizmu kontroli przeciążenia AIMD stylu](https://tools.ietf.org/html/rfc5681) dla każdej usługi Azure Cosmos DB partycji zakres kluczy efektywnie obsłużyć ograniczania szybkości i przekroczeń limitu czasu. 

## <a name="next-steps"></a>Następne kroki 
  
* Dowiedz się więcej, Wypróbowując przykładowych aplikacji, korzystanie z biblioteki przetwarzania zbiorczego w [.NET](bulk-executor-dot-net.md) i [Java](bulk-executor-java.md).  
* Zapoznaj się z zbiorcze wykonawca SDK informacji i wersji uwagi w [.NET](sql-api-sdk-bulk-executor-dot-net.md) i [Java](sql-api-sdk-bulk-executor-java.md).
* Biblioteka przetwarzania zbiorczego jest zintegrowana z łącznika Spark bazy danych Cosmos, aby dowiedzieć się więcej, zobacz [łącznika usługi Azure Cosmos DB Spark](spark-connector.md) artykułu.  
* Biblioteka przetwarzania zbiorczego jest również zintegrowana do nowej wersji [łącznika usługi Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) dla usługi Azure Data Factory do kopiowania danych.
