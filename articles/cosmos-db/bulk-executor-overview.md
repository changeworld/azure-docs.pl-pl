---
title: Azure Cosmos DB bulk executor library overview (Omówienie biblioteki funkcji wykonawczej operacji zbiorczych w usłudze Azure Cosmos DB)
description: Wykonywanie operacji zbiorczych w usłudze Azure Cosmos DB za pomocą importu zbiorczego i zbiorczego aktualizowania interfejsów API oferowanych przez bibliotekę zbiorczego executora.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 9d335bcf6daf0b38e7a68ca2d40894dd64c93e40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442150"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB bulk executor library overview (Omówienie biblioteki funkcji wykonawczej operacji zbiorczych w usłudze Azure Cosmos DB)
 
Usługa Azure Cosmos DB to szybka, elastyczna i globalnie rozproszona baza danych zaprojektowana pod kątem elastycznego skalowania w poziomie i obsługi następujących funkcji: 

* Duża przepływność odczytu i zapisu (miliony operacji na sekundę).  
* Przechowywanie dużych ilości (setki terabajtów lub jeszcze więcej) danych transakcyjnych i operacyjnych przy przewidywalnym opóźnieniu liczonym w milisekundach.  

Biblioteka funkcji wykonawczej operacji zbiorczych ułatwia korzystanie z tej ogromnej przepływności i magazynu. Biblioteka funkcji wykonawczej operacji zbiorczych umożliwia wykonywanie operacji zbiorczych w usłudze Azure Cosmos DB za pośrednictwem interfejsów API importu zbiorczego i aktualizacji zbiorczej. Więcej informacji o funkcjach biblioteki funkcji wykonawczej operacji zbiorczych znajdziesz w poniższych sekcjach. 

> [!NOTE] 
> Obecnie biblioteka zbiorczego executora obsługuje operacje importowania i aktualizacji, a ta biblioteka jest obsługiwana tylko przez interfejs API SQL usługi Azure Cosmos DB i konta interfejsu API Gremlin.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Najważniejsze cechy biblioteki wykonawców zbiorczych  
 
* Znacznie zmniejsza zasoby obliczeniowe po stronie klienta potrzebne do nasycenia przepływności przydzielonej do kontenera. Aplikacja jednowątkowa, która zapisuje dane przy użyciu interfejsu API importu zbiorczego, osiąga 10 razy większą przepustowość zapisu w porównaniu z aplikacją wielowątkową, która zapisuje dane równolegle podczas nasycenia procesora CPU komputera klienckiego.  

* Abstrakcje od żmudnych zadań pisania logiki aplikacji do obsługi ograniczania szybkości żądania, limity czasu żądania i innych wyjątków przejściowych, skutecznie obsługi ich w bibliotece.  

* Zapewnia uproszczony mechanizm dla aplikacji wykonujących operacje zbiorcze w celu skalowania w poziomie. Wystąpienie pojedynczego executora zbiorczego uruchomionego na maszynie wirtualnej platformy Azure może zużywać więcej niż 500 000 RU/s i można osiągnąć wyższą szybkość przepływności, dodając dodatkowe wystąpienia na maszynach wirtualnych poszczególnych klientów.  
 
* Można zbiorczo importować więcej niż terabajt danych w ciągu godziny przy użyciu architektury skalowania w poziomie.  

* Można zbiorczo aktualizować istniejące dane w kontenerach usługi Azure Cosmos jako poprawki. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Jak działa wykonawca luzem? 

Gdy operacja zbiorcza do importowania lub aktualizowania dokumentów jest wyzwalana z partii jednostek, są one początkowo tasowane do zasobników odpowiadających ich zakres klucza partycji usługi Azure Cosmos DB. W każdym zasobniku, który odpowiada zakres klucza partycji, są one podzielone na mini-partii i każdy mini-partii działać jako ładunek, który jest zatwierdzony po stronie serwera. Biblioteka executora zbiorczego została wbudowana w optymalizacje dla równoczesnego wykonywania tych mini-partii zarówno w zakresach kluczy partycji, jak i między nich. Na poniższej ilustracji przedstawiono, jak zbiorczy wykonawca parti danych do różnych kluczy partycji:  

![Architektura zbiorczego wykonawcy](./media/bulk-executor-overview/bulk-executor-architecture.png)

Biblioteka executor zbiorczej upewnia się, aby maksymalnie wykorzystać przepływność przydzieloną do kolekcji. Używa [mechanizmu kontroli przeciążenia w stylu AIMD](https://tools.ietf.org/html/rfc5681) dla każdego zakresu klucza partycji usługi Azure Cosmos DB, aby skutecznie obsługiwać limity szybkości i limity czasu. 

## <a name="next-steps"></a>Następne kroki 
  
* Dowiedz się więcej, wypróbowywając przykładowe aplikacje zużywające bibliotekę zbiorczą executora w [programach .NET](bulk-executor-dot-net.md) i [Java](bulk-executor-java.md).  
* Zapoznaj się z informacjami sdk sdk wykonawca zbiorczy i informacje o wydaniu w [.NET](sql-api-sdk-bulk-executor-dot-net.md) i [Java](sql-api-sdk-bulk-executor-java.md).
* Biblioteka executora zbiorczego jest zintegrowana ze łącznikiem platformy Spark usługi Cosmos DB, aby dowiedzieć się więcej, zobacz artykuł [łącznika platformy Azure Cosmos DB Spark.](spark-connector.md)  
* Biblioteka zbiorczego executora jest również zintegrowana z nową wersją [łącznika usługi Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) dla usługi Azure Data Factory w celu skopiowania danych.
