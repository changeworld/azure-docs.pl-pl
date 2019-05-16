---
title: Wprowadzenie do wbudowaną analizę operacyjną w usłudze Azure Cosmos DB z platformą Apache Spark
description: Dowiedz się, jak użyć wbudowanej obsługi dla platformy Apache Spark w usłudze Azure Cosmos DB w celu uruchomienia, operacyjne analizę i sztuczną Inteligencję
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/10/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: c62639feed7ced9d92e29715e350b952465a94a1
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517724"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Wbudowana analiza operacyjna w usłudze Azure Cosmos DB z platformą Apache Spark (wersja zapoznawcza) 

Wbudowana obsługa platformy Apache Spark w usłudze Azure Cosmos DB umożliwia uruchamianie analizy z platformy Apache Spark względem danych przechowywanych na koncie usługi Azure Cosmos. Zapewnia macierzystą obsługę platformy Apache Spark zadań do wykonania bezpośrednio w globalnie rozproszonych baz danych Cosmos. Te możliwości analitykom danych, inżynierami danych i deweloperom można użyć usługi Azure Cosmos DB jako elastyczne, skalowalne i wydajne platformę danych, aby uruchomić zarówno **OLTP i OLAP/HTAP** obciążeń. 

Platforma Spark obliczeniowej, są automatycznie dostępne we wszystkich regionach platformy Azure skojarzony z Twoim kontem usługi Azure Cosmos. Zadania Spark używają wielu wzorców możliwości usługi Azure Cosmos DB i można ich zapisać lub zapytanie względem lokalnej repliki w każdym regionie. 

> [!NOTE]
> Wbudowana obsługa platformy Apache Spark w usłudze Azure Cosmos DB jest obecnie w ograniczonej wersji zapoznawczej. Aby utworzyć konto wersji zapoznawczej, przejdź do [skorzystania z wersji zapoznawczej](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB) strony. 

Obsługa platformy Apache Spark w usłudze Azure Cosmos DB zapewnia następujące korzyści:

* Możesz uzyskać najkrótszy czas do wglądu dla użytkowników geograficznie rozproszonych i danych.

* Można uprościć architektury rozwiązania i małe [całkowity koszt użytkowania](total-cost-ownership.md) (TCO). System będzie mieć najmniejszej liczby składników przetwarzania danych i pozwala uniknąć wszelkich przenoszenia zbędnych danych między nimi.

* Tworzy [zabezpieczeń](secure-access-to-data.md), [zgodności](compliance.md)i inspekcji granicy, która obejmuje wszystkie dane objęte zarządzaniem.

* Udostępnia "zawsze włączona" lub [o wysokiej dostępności](high-availability.md) analytics przez użytkownika końcowego, która jest wspierana przez rygorystyczne umowy SLA.

![Obsługa platformy Apache Spark w usłudze Azure Cosmos DB wizualizacji](./media/spark-api-introduction/spark-api-visualization.png)
 
Korzystając z pomocy technicznej platformy Apache Spark w usłudze Azure Cosmos DB, można je tworzyć i wdrażać rozwiązania, takie jak sztuczna Inteligencja i głębokiego uczenia modeli analizy predykcyjnej, zalecenia, IoT, aplikacja customer 360, wykrywanie oszustw, tonacji tekstu, analiza strumienia kliknięć. Te rozwiązania pracować bezpośrednio w odniesieniu do danych usługi Azure Cosmos DB.

Można skonfigurować usługi batch i przesyłania strumieniowego zadań ETL w usłudze Azure Cosmos DB, bez konieczności wychodzenia poza usługa bazy danych lub dodanie dodatkowych usług obliczeniowych. Środowisko obliczeniowe można elastycznie skalować, gdy trzeba wykonywać zadania ETL i przeskalujesz ją ponownie w dół po zakończeniu zadania.

Obsługa platformy Apache Spark w usłudze Azure Cosmos DB oferuje wbudowaną obsługę usługi Machine Learning w środowisk uruchomieniowych platformy Apache Spark. Środowiska uruchomieniowego obejmują Biblioteka MLLib platformy Spark, Microsoft Machine Learning dla platformy Spark, Azure Machine Learning i Cognitive Services. Z tymi funkcjami analitykami danych, inżynierami danych i analityków danych można tworzyć i zoperacjonalizować modele uczenia maszynowego bezpośrednio z poziomu usługi Azure Cosmos DB w zaledwie ułamku czasie i przy niskich kosztach.


## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Analiza operacyjna globalnie rozproszona o niskich opóźnieniach i sztucznej Inteligencji

Przy użyciu platformy Apache Spark w globalnie rozproszonej bazy danych Azure Cosmos możesz teraz uzyskać wszystkie skrócić czas do wglądu w całym świecie. Usługa Azure Cosmos DB umożliwia **analiza operacyjna globalnie rozproszona o niskich opóźnieniach** skali elastycznej przy użyciu trzech technik klucza:

* Ponieważ bazy danych Azure Cosmos jest globalnie dystrybuowana, wszystkie dane są pozyskiwane lokalnie gdzie znajdują się producentów danych (na przykład użytkowników). Zapytania są obsługiwane dla lokalnej repliki najbliżej producenci i konsumenci danych niezależnie od tego, gdzie znajdują się na całym świecie. 

* Wszystkie zapytania analityczne są wykonywane bezpośrednio na indeksowane dane przechowywane w partycji danych bez konieczności przenoszenia wszelkich zbędnych danych. 

* Ponieważ platforma Spark jest wspólnie przechowywane w usłudze Azure Cosmos DB, mniej tłumaczenia pośrednich oraz danych miejsce, skutkuje lepszą wydajność i skalowalność.

### <a name="unified-serverless-experience-for-apache-spark"></a>Ujednolicone środowisko bezserwerowe dla platformy Apache Spark

Jako wielomodelowa baza danych usługi Azure Cosmos DB teraz rozwija swoją obsługę interfejsów API OSS na, zapewniając **ujednolicone środowisko bezserwerowe dla platformy Apache Spark** za pomocą pary klucz wartość, dokumentów, wykres, modele danych rodziny kolumn. Różne modele danych są obsługiwane przy użyciu bazy danych MongoDB, Cassandra, Gremlin, Etcd i interfejsy API SQL — działających na tych samych danych. 

Dzięki obsłudze platformy Apache Spark w usłudze Azure Cosmos DB natywnie można obsługuje aplikacje napisane w języku Scala, Python, Java i korzystać z kilku bibliotek ściśle zintegrowanych dla programu SQL. Biblioteki te obejmują ([Spark SQL](https://spark.apache.org/sql/)), usługi machine learning (Spark [MLlib](https://spark.apache.org/mllib/)), przetwarzanie strumienia ([Spark Structured Streaming](https://spark.apache.org/streaming/)) i przetwarzanie wykresów (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Te narzędzia ułatwiają Wykorzystaj platformę Apache Spark dla różnych przypadków użycia. Nie masz do czynienia z zarządzaniem Spark lub klastrów platformy Spark. Można użyć znanej interfejsy API systemu Apache Spark i **notesów programu Jupyter** analizy i interfejsu API SQL lub dowolne OSS API NoSQL, takie jak Cassandra dla przetwarzania transakcyjnego na tych samych danych, w tym samym czasie.

### <a name="no-schema-or-index-management"></a>Bez zarządzania schematami lub indeksami

W przeciwieństwie do tradycyjnych analitycznych baz danych za pomocą usługi Azure Cosmos DB, inżynierami danych i analitykom danych nie są już potrzebne do czynienia z kłopotliwe schematu i zarządzania indeksami. Aparat bazy danych w usłudze Azure Cosmos DB nie wymaga żadnych jawnych zarządzaniem schematami lub indeksami i może automatycznie indeksować wszystkie dane, które pozyskuje go do obsługi zapytań platformy Apache Spark szybko. 

### <a name="consistency-choices"></a>Opcji spójności

Ponieważ w partycji danych bazy danych Azure Cosmos wykonywane są zadania platformy Apache Spark, otrzyma zapytania [pięć dobrze zdefiniowanych opcji spójności](consistency-levels.md). Te modele spójności zapewniają elastyczność wyboru ścisłej spójności w celu zapewnienia najbardziej dokładne wyniki algorytmów uczenia bez narażania oczekiwania i wysokiej dostępności maszyny. 

### <a name="slas"></a>Umowy SLA

Zadania platformy Apache Spark odniesie korzyści Azure Cosmos DB, takie jak branży kompleksowe [umowy SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) bez żadnych związanym z zarządzaniem osobne klastry platformy Apache Spark... Tych umów SLA obejmować przepustowość, opóźnienie w 99. percentylu, spójności i wysokiej dostępności. 

### <a name="mixed-workloads"></a>Mieszane obciążenia

Integracja programu Apache Spark do usługi Azure Cosmos DB mostków separacji transakcyjne i analityczne, które jest jedną z najważniejszych klientów słabe punkty podczas tworzenia aplikacji natywnych dla chmury w skali globalnej. 

## <a name="built-in-jupyter-notebooks-support"></a>Wbudowaną obsługą notesów Jupyter

Usługa Azure Cosmos DB obsługuje notesy Jupyter notebook dla wszystkich interfejsów API, takie jak Cassandra, MongoDB, SQL, Gremlin i tabeli. Notesy Jupyter notebook uruchamiane w ramach konta usługi Azure Cosmos DB i mogą poprawić funkcjonalność z perspektywy dewelopera. Notes wbudowaną obsługę wszystkich interfejsów API usługi Azure Cosmos DB i modeli danych, pozwala uruchamiać interakcyjne zapytania. Można również wykonać modeli uczenia maszynowego i analizy danych przechowywanych w bazach danych usługi Azure Cosmos. Za pomocą środowisko notesu programu Jupyter, można analizować przechowywanych danych, tworzenie i szkolenie modeli uczenia maszynowego i wykonać wnioskowania na danych w witrynie Azure portal, jak pokazano na poniższej ilustracji:

![Obsługa notesów programu Jupyter w usłudze Azure Cosmos DB](./media/spark-api-introduction/jupyter-notebooks-portal.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat zalet usługi Azure Cosmos DB, zobacz [Przegląd](introduction.md) artykułu.
* [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](mongodb-introduction.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](cassandra-introduction.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](graph-introduction.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](table-introduction.md)




