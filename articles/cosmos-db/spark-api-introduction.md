---
title: Wprowadzenie do platformy Spark usługi Azure Cosmos DB interfejsu API
description: Dowiedz się, jak można użyć interfejsu API usługi Azure Cosmos DB Spark przeprowadzić analizę operacyjną i sztuczną Inteligencję
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/06/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: de920f40f2968942b7ac66414170b43bd9317cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65081094"
---
# <a name="introduction-to-the-azure-cosmos-db-spark-api-preview"></a>Wprowadzenie do usługi Azure Cosmos DB interfejs API platformy Spark (wersja zapoznawcza) 

Interfejs API platformy Spark w usłudze Azure Cosmos DB umożliwia uruchamianie analizy z platformy Apache Spark względem danych przechowywanych na koncie usługi Azure Cosmos.

Interfejs API platformy Spark w usłudze Azure Cosmos DB zapewnia natywną obsługę platformy Apache Spark zadań do wykonania bezpośrednio w globalnie rozproszonych baz danych Cosmos. Te możliwości analitykom danych, inżynierami danych i deweloperom można użyć usługi Azure Cosmos DB jako elastyczne, skalowalne i wydajne platformę danych, aby uruchomić zarówno **OLTP i OLAP/HTAP** obciążeń. 

> [!NOTE]
> Interfejs API usługi Azure Cosmos DB Spark jest obecnie w ograniczonej wersji zapoznawczej. Aby utworzyć konto wersji zapoznawczej, przejdź do [skorzystania z wersji zapoznawczej](https://aka.ms/cosmos-spark-preview) strony. 

Interfejs API platformy Spark w usłudze Azure Cosmos DB zapewnia następujące korzyści:

* Możesz uzyskać najkrótszy czas do wglądu dla użytkowników geograficznie rozproszonych i danych.

* Można uprościć architektury rozwiązania i małe [całkowity koszt użytkowania](total-cost-ownership.md) (TCO). System będzie mieć najmniejszej liczby składników przetwarzania danych i pozwala uniknąć wszelkich przenoszenia zbędnych danych między nimi.

* Tworzy [zabezpieczeń](secure-access-to-data.md), [zgodności](compliance.md)i inspekcji granicy, która obejmuje wszystkie dane objęte zarządzaniem.

* Udostępnia "zawsze włączona" lub [o wysokiej dostępności](high-availability.md) analytics przez użytkownika końcowego, która jest wspierana przez rygorystyczne umowy SLA.

![Usługa Azure Cosmos DB — interfejs API platformy Spark wizualizacji](./media/spark-api-introduction/spark-api-visualization.png)
 
Za pomocą interfejsu API usługi Azure Cosmos DB platformy Spark, można je tworzyć i wdrażać rozwiązania, takie jak sztuczna Inteligencja i głębokiego uczenia modeli analizy predykcyjnej, zalecenia, IoT, aplikacja customer 360, wykrywanie oszustw, tonacji tekstu, analiza strumienia kliknięć. Działają one bezpośrednio w odniesieniu do danych usługi Azure Cosmos DB.

Można skonfigurować usługi batch i przesyłania strumieniowego zadań ETL w usłudze Azure Cosmos DB, bez konieczności wychodzenia poza usługa bazy danych lub dodanie dodatkowych usług obliczeniowych. Środowisko obliczeniowe można elastycznie skalować, gdy trzeba wykonywać zadania ETL i przeskalujesz ją ponownie w dół po zakończeniu zadania.

Interfejs API platformy Spark w usłudze Azure Cosmos DB obsługuje wbudowanej obsługi usługi Machine Learning w środowisk uruchomieniowych platformy Apache Spark. Środowiska uruchomieniowego obejmują Biblioteka MLLib platformy Spark, Microsoft Machine Learning dla platformy Spark, Azure Machine Learning i Cognitive Services. Z tymi funkcjami analitykami danych, inżynierami danych i analityków danych można tworzyć i zoperacjonalizować modele uczenia maszynowego bezpośrednio z poziomu usługi Azure Cosmos DB w zaledwie ułamku czasie i przy niskich kosztach.


## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Analiza operacyjna globalnie rozproszona o niskich opóźnieniach i sztucznej Inteligencji

Przy użyciu platformy Apache Spark w globalnie rozproszonej bazy danych Azure Cosmos możesz teraz uzyskać wszystkie skrócić czas do wglądu w całym świecie. Usługa Azure Cosmos DB umożliwia **analiza operacyjna globalnie rozproszona o niskich opóźnieniach** skali elastycznej przy użyciu trzech technik klucza:

* Ponieważ bazy danych Azure Cosmos jest globalnie dystrybuowana, wszystkie dane są pozyskiwane lokalnie gdzie znajdują się producentów danych (na przykład użytkowników). Zapytania są obsługiwane dla lokalnej repliki najbliżej producenci i konsumenci danych niezależnie od tego, gdzie znajdują się na całym świecie. 

* Wszystkie zapytania analityczne są wykonywane bezpośrednio na indeksowane dane przechowywane w partycji danych bez konieczności przenoszenia wszelkich zbędnych danych. 

* Ponieważ platforma Spark jest wspólnie przechowywane w usłudze Azure Cosmos DB, mniej tłumaczenia pośrednich oraz danych miejsce, skutkuje lepszą wydajność i skalowalność.

### <a name="unified-serverless-experience-for-apache-spark"></a>Ujednolicone środowisko bezserwerowe dla platformy Apache Spark

Jako wielomodelowa baza danych usługi Azure Cosmos DB teraz rozwija swoją obsługę interfejsów API OSS na, zapewniając **ujednolicone środowisko bezserwerowe dla platformy Apache Spark** za pomocą pary klucz wartość, dokumentów, wykres, modele danych rodziny kolumn. Różne modele danych są obsługiwane przy użyciu bazy danych MongoDB, Cassandra, Gremlin, Etcd i interfejsy API SQL — działających na tych samych danych. 

Za pomocą interfejsu API platformy Spark można natywnie obsługuje aplikacje napisane w języku Scala, Python, Java i korzystać z kilku bibliotek ściśle zintegrowanych dla programu SQL. Biblioteki te obejmują ([Spark SQL](https://spark.apache.org/sql/)), usługi machine learning (Spark [MLlib](https://spark.apache.org/mllib/)), przetwarzanie strumienia ([Spark Structured Streaming](https://spark.apache.org/streaming/)) i przetwarzanie wykresów (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Te narzędzia ułatwiają korzystanie z interfejsu API platformy Spark dla różnych przypadków użycia. Nie masz do czynienia z zarządzaniem Spark lub klastrów platformy Spark. Można użyć znanej interfejsy API systemu Apache Spark i **notesów programu Jupyter** analizy i interfejsu API SQL lub dowolne OSS API NoSQL, takie jak Cassandra dla przetwarzania transakcyjnego na tych samych danych, w tym samym czasie.

### <a name="no-schema-or-index-management"></a>Bez zarządzania schematami lub indeksami

W przeciwieństwie do tradycyjnych analitycznych baz danych za pomocą usługi Azure Cosmos DB, inżynierami danych i analitykom danych nie są już potrzebne do czynienia z kłopotliwe schematu i zarządzania indeksami. Aparat bazy danych w usłudze Azure Cosmos DB nie wymaga żadnych jawnych zarządzaniem schematami lub indeksami i może automatycznie indeksować wszystkie dane, które pozyskuje go do obsługi zapytań platformy Apache Spark szybko. 

### <a name="consistency-choices"></a>Opcji spójności

Ponieważ w partycji danych bazy danych Azure Cosmos wykonywane są zadania platformy Apache Spark, otrzyma zapytania [pięć dobrze zdefiniowanych opcji spójności](consistency-levels.md). Te modele spójności zapewniają elastyczność wyboru ścisłej spójności w celu zapewnienia najbardziej dokładne wyniki algorytmów uczenia bez narażania oczekiwania i wysokiej dostępności maszyny. 

### <a name="slas"></a>Umowy SLA

Zadania platformy Apache Spark odniesie korzyści Azure Cosmos DB, takie jak branży kompleksowe [umowy SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) bez żadnych związanym z zarządzaniem osobne klastry platformy Apache Spark... Tych umów SLA obejmować przepustowość, opóźnienie w 99. percentylu, spójności i wysokiej dostępności. 

### <a name="mixed-workloads"></a>Mieszane obciążenia

Integracja programu Apache Spark do usługi Azure Cosmos DB mostków separacji transakcyjne i analityczne, które jest jedną z najważniejszych klientów słabe punkty podczas tworzenia aplikacji natywnych dla chmury w skali globalnej. 

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat zalet usługi Azure Cosmos DB, zobacz [Przegląd](introduction.md) artykułu.
* [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](mongodb-introduction.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](cassandra-introduction.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](graph-introduction.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](table-introduction.md)




