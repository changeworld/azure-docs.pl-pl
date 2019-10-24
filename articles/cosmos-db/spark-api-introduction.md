---
title: Wprowadzenie do wbudowanej analizy operacyjnej w Azure Cosmos DB z Apache Spark
description: Dowiedz się, jak korzystać z wbudowanej obsługi Apache Spark w Azure Cosmos DB do uruchamiania analiz operacyjnych i AI
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/26/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 45f70d9a9d2cb450cab1242a080077c771218ba2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754840"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark"></a>Wbudowana analiza operacyjna w Azure Cosmos DB z Apache Spark

Za pomocą Azure Cosmos DB można uruchamiać dystrybucje globalnie, analizy o małym opóźnieniu i AI na danych transakcyjnych. Dzięki natywnej obsłudze notesów Apache Spark i Jupyter Azure Cosmos DB pomaga skrócić czas do wglądu w szczegółowe dane. Ponieważ dane są pozyskiwane, obsługiwane i analiza jest uruchamiana w ramach lokalnej repliki bazy danych w regionie świadczenia usługi Azure. Można bezpośrednio wykonywać Apache Spark zapytania dotyczące indeksowanych danych wielomodelowych przechowywanych w ramach partycji danych.

Wbudowana obsługa Apache Spark w programie Azure Cosmos DB umożliwia uruchamianie analiz z Apache Spark w odniesieniu do danych przechowywanych na koncie usługi Azure Cosmos. Zapewnia ona natywną obsługę zadań Apache Spark do wykonania bezpośrednio w ramach dystrybuowanych globalnie baz danych Cosmos. Dzięki tym funkcjom, programistom, inżynierom danych i analitykom danych mogą używać Azure Cosmos DB jako elastycznej, skalowalnej i wydajnej platformy danych do uruchamiania obciążeń **OLTP i OLAP/HTAP** .

Obsługa Apache Spark w programie Azure Cosmos DB oferuje następujące korzyści:

* Możesz uzyskać najszybszy czas, aby uzyskać wgląd w informacje dotyczące użytkowników i danych rozproszonych geograficznie.

* Możesz uprościć architekturę rozwiązania i obniżyć [całkowity koszt posiadania](total-cost-ownership.md) (TCO). System będzie miał mniejszą liczbę składników przetwarzania danych i pozwala uniknąć niepotrzebnego przenoszenia danych między nimi.

* Tworzy granicę [zabezpieczeń](secure-access-to-data.md), [zgodności](compliance.md)i inspekcji, która obejmuje wszystkie dane objęte zarządzaniem.

* Zapewnia analizę "Always On" lub " [wysoką dostępność](high-availability.md) " dla użytkowników końcowych, które są obsługiwane przez rygorystyczne umowy SLA.

![Obsługa Apache Spark w wizualizacji Azure Cosmos DB](./media/spark-api-introduction/spark-api-visualization.png)
 
Korzystając z Apache Spark pomocy technicznej w Azure Cosmos DB, możesz tworzyć i wdrażać rozwiązania takie jak modele AI i głębokiego uczenia, Analiza predykcyjna, zalecenia, IoT, klient 360, wykrywanie oszustw, tonacji tekstu, analiza strumienia kliknięć. Te rozwiązania działają bezpośrednio w odniesieniu do danych Azure Cosmos DB.

Można skonfigurować zadanie usługi Batch i przesyłania strumieniowego w usłudze Azure Cosmos DB, bez konieczności przechodzenia poza usługę bazy danych lub dodawania dodatkowych usług obliczeniowych. Można elastycznie skalować środowisko obliczeniowe, gdy trzeba wykonać zadanie ETL i skalować je ponownie po zakończeniu zadania.

Obsługa Apache Spark w programie Azure Cosmos DB oferuje wbudowaną obsługę Machine Learning w środowisku uruchomieniowym Apache Spark. Środowisko uruchomieniowe obejmuje platformę Spark MLLib, Machine Learning firmy Microsoft dla platformy Spark, Azure Machine Learning i Cognitive Services. Dzięki tym funkcjom analityków danych, inżynierowie danych i analitycy danych mogą tworzyć i operacjonalizować modele uczenia maszynowego bezpośrednio w ramach Azure Cosmos DB, w części czasu i przy niskich kosztach.


## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="low-latency-operational-analytics-and-ai"></a>Analiza operacyjna małych opóźnień i AI

Dzięki Apache Spark w globalnie rozproszonej bazie danych Azure Cosmos można teraz szybko uzyskać wgląd w dane na całym świecie. Azure Cosmos DB oferuje **globalnie dystrybuowane i małe opóźnienia analizy operacyjnej** w elastycznej skali przy użyciu trzech kluczowych technik:

* Ponieważ baza danych usługi Azure Cosmos jest dystrybuowana globalnie, wszystkie dane są pozyskiwane lokalnie, w której znajdują się producenci danych (na przykład użytkowników). Zapytania są obsługiwane dla replik lokalnych znajdujących się najbliżej zarówno producentów, jak i konsumentów danych, niezależnie od tego, gdzie znajdują się na świecie.

* Wszystkie zapytania analityczne są wykonywane bezpośrednio na indeksowanych danych przechowywanych wewnątrz partycji danych, bez konieczności przenoszenia danych.

* Ze względu na to, że platforma Spark jest w Azure Cosmos DB, przeprowadzona jest mniejsza liczba przeliczeń pośrednich i ruchów danych, co zapewnia lepszą wydajność i skalowalność.

* Wszystkie Azure Cosmos DB podstawowe funkcje, takie jak wiele wzorców, automatyczne przełączanie do trybu failover, Strefy dostępności itp., są dostępne dla wbudowanych Apache Spark w Azure Cosmos DB.

### <a name="unified-serverless-experience-for-apache-spark"></a>Ujednolicone środowisko pracy bezserwerowej dla Apache Spark

Jako wielomodelowa baza danych, Azure Cosmos DB teraz rozszerza jej obsługę interfejsów API OSS przez zapewnienie **ujednoliconego środowiska bezserwerowego dla Apache Spark** za pomocą modeli danych typu klucz-wartość, dokument, Graf i rodzina kolumn. Różne modele danych są obsługiwane za pomocą interfejsów API MongoDB, Cassandra, Gremlin, Etcd i SQL — wszystkich działających na tych samych danych bazowych. 

Dzięki obsłudze Apache Spark w Azure Cosmos DB można natywnie obsługiwać aplikacje Scala, Python, Java i korzystać z kilku ściśle zintegrowanych bibliotek dla języka SQL. Te biblioteki obejmują ([Spark SQL](https://spark.apache.org/sql/)), Machine Learning (Spark [MLlib](https://spark.apache.org/mllib/)), przetwarzanie strumieni (strumień[strukturalny Spark](https://spark.apache.org/streaming/)) i przetwarzanie grafu (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Te narzędzia ułatwiają używanie Apache Spark do różnych przypadków użycia. Nie musisz obsługiwać zarządzania klastrami Spark i Spark. Możesz użyć znanych interfejsów API Apache Spark i **notesów Jupyter** na potrzeby analiz i interfejsu API SQL lub dowolnego interfejsu API usługi OSS NoSQL, takiego jak Cassandra do przetwarzania transakcyjnego na tych samych danych źródłowych w tym samym czasie.

### <a name="no-schema-or-index-management"></a>Bez zarządzania schematami lub indeksami

W przeciwieństwie do tradycyjnych analitycznych baz danych, w przypadku Azure Cosmos DB, inżynierów danych i analityków danych nie trzeba już rozwiązywać żadnych nieskomplikowanych zarządzania schematami i indeksami. Aparat bazy danych w Azure Cosmos DB nie wymaga żadnego jawnego zarządzania schematem lub indeksem i może automatycznie indeksować wszystkie dane, które pozyskają, aby szybko obsłużyć Apache Sparke zapytania.

### <a name="consistency-choices"></a>Opcje spójności

Ponieważ Apache Spark zadania są wykonywane w partycjach danych bazy danych Azure Cosmos, w kwerendach zostaną wyświetlone [pięć dobrze zdefiniowanych opcji spójności](consistency-levels.md). Te modele spójności zapewniają elastyczność pozwalającą wybrać ścisłą spójność, aby zapewnić najbardziej precyzyjne wyniki dla algorytmów uczenia maszynowego bez wpływu na opóźnienia i wysoką dostępność.

### <a name="comprehensive-slas"></a>Kompleksowe umowy SLA

Apache Spark zadania będą mieć Azure Cosmos DB korzyści, takie jak wiodące w branży kompleksowe [umowy SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999), bez jakichkolwiek nakładów związanych z zarządzaniem oddzielnymi klastrami Apache Spark. Te umowy SLA obejmują przepływność, opóźnienie w 99 percentylu, spójności i wysokiej dostępności. 

### <a name="mixed-operational-and-analytical-workloads-with-complete-isolation"></a>Mieszane obciążenia operacyjne i analityczne z pełną izolacją

Integracja Apache Spark do Azure Cosmos DB mostkuje rozdzielenia transakcyjnego i analitycznego, który był jednym z głównych punktów sprzedaży, podczas tworzenia aplikacji natywnych dla chmury w skali globalnej. Obciążenia OLTP i OLAP są uruchamiane obok siebie i nie zakłócają siebie nawzajem.

### <a name="low-latency-analytical-and-transactional-storage"></a>Magazyn analityczny o małym opóźnieniu i transakcyjny

Azure Cosmos DB natywnie przechowuje dane w dwóch odrębnych warstwach magazynowania: transakcyjny (zorientowany wierszowo) i magazyn analityczny (zorientowane na kolumny w formacie pliku Apache parquet). Replikuje dane w każdej warstwie globalnie i umożliwia użytkownikom niezależne zarządzanie danymi w tych warstwach na podstawie zasad przechowywania.

Ta architektura magazynu umożliwia obsługę zarówno obciążeń transakcyjnych, jak i analitycznych o znaczeniu krytycznym dla tych samych globalnie dystrybuowanych danych. W przypadku Cosmos DB nie są wymagane żadne operacje ETL ani nie są wykonywane żadne niepotrzebne przenoszenie danych. Można po prostu uruchomić zarówno obciążenia transakcyjne, jak i analityczne na tych samych danych bazowych. Obciążenia transakcyjne mogą używać znanych interfejsów API dostępu transakcyjnego, w tym SQL, Cassandra, MongoDB, Gremlin i Etcd. Podczas gdy obciążenia analityczne i AI mogą korzystać z wbudowanych Apache Spark SQL, platform ML i całych Apache Spark łańcucha narzędzi.

### <a name="snapshots-and-historical-analytical-queries"></a>Migawki i historyczne zapytania analityczne

Można tworzyć migawki na żądanie lub w harmonogramie dla kolumn skompresowanych danych przechowywanych w warstwie analitycznej, aby wykonywać zapytania analityczne bezpośrednio względem konkretnej migawki. Ta funkcja umożliwia Flashback lub przejazdowe zapytania analityczne, wycofywanie, pełne historyczne zapisy inspekcji oraz możliwość przeprowadzenia operacji uczenia maszynowego i eksperymentów AI.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o zaletach Azure Cosmos DB, zobacz artykuł z [omówieniem](introduction.md) .
* [Wprowadzenie do interfejsu API Azure Cosmos DB MongoDB](mongodb-introduction.md)
* [Rozpocznij pracę z Azure Cosmos DB interfejs API Cassandra](cassandra-introduction.md)
* [Wprowadzenie do interfejsu API Azure Cosmos DB Gremlin](graph-introduction.md)
* [Rozpocznij pracę z Azure Cosmos DB interfejs API tabel](table-introduction.md)