---
title: Wprowadzenie do wbudowanej analizy operacyjnej w Azure Cosmos DB z Apache Spark
description: Dowiedz się, jak korzystać z wbudowanej obsługi Apache Spark w Azure Cosmos DB do uruchamiania analiz operacyjnych i AI
ms.service: cosmos-db
ms.topic: overview
ms.date: 08/01/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 0f070cb9a6e300dad0ec9e0b393b09b7f22d2942
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212579"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Wbudowana analiza operacyjna w Azure Cosmos DB z Apache Spark (wersja zapoznawcza) 

Wbudowana obsługa Apache Spark w programie Azure Cosmos DB umożliwia uruchamianie analiz z Apache Spark w odniesieniu do danych przechowywanych na koncie usługi Azure Cosmos. Zapewnia ona natywną obsługę zadań Apache Spark do wykonania bezpośrednio w ramach dystrybuowanych globalnie baz danych Cosmos. Dzięki tym funkcjom, programistom, inżynierom danych i analitykom danych mogą używać Azure Cosmos DB jako elastycznej, skalowalnej i wydajnej platformy danych do uruchamiania obciążeń **OLTP i OLAP/HTAP** . 

Obliczenia platformy Spark są automatycznie dostępne we wszystkich regionach świadczenia usługi Azure skojarzonych z Twoim kontem usługi Azure Cosmos. Zadania platformy Spark wykorzystują możliwości wielu wzorców Azure Cosmos DB i mogą pisać i wysyłać zapytania względem lokalnych replik w każdym regionie. 

> [!NOTE]
> Wbudowana obsługa Apache Spark w Azure Cosmos DB jest obecnie w ograniczonej wersji zapoznawczej. Aby utworzyć konto w wersji zapoznawczej, przejdź do [strony rejestracji na stronie wersji zapoznawczej](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB). 

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

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Globalnie dystrybuowane, analiza operacyjna o małym opóźnieniu i AI

Dzięki Apache Spark w globalnie rozproszonej bazie danych Azure Cosmos można teraz szybko uzyskać wgląd w dane na całym świecie. Azure Cosmos DB oferuje **globalnie dystrybuowane i małe opóźnienia analizy operacyjnej** w elastycznej skali przy użyciu trzech kluczowych technik:

* Ponieważ baza danych usługi Azure Cosmos jest dystrybuowana globalnie, wszystkie dane są pozyskiwane lokalnie, w której znajdują się producenci danych (na przykład użytkowników). Zapytania są obsługiwane dla replik lokalnych znajdujących się najbliżej zarówno producentów, jak i konsumentów danych, niezależnie od tego, gdzie znajdują się na świecie. 

* Wszystkie zapytania analityczne są wykonywane bezpośrednio na indeksowanych danych przechowywanych wewnątrz partycji danych, bez konieczności przenoszenia danych. 

* Ze względu na to, że platforma Spark jest w Azure Cosmos DB, przeprowadzona jest mniejsza liczba przeliczeń pośrednich i ruchów danych, co zapewnia lepszą wydajność i skalowalność.

### <a name="unified-serverless-experience-for-apache-spark"></a>Ujednolicone środowisko pracy bezserwerowej dla Apache Spark

Jako wielomodelowa baza danych, Azure Cosmos DB teraz rozszerza jej obsługę interfejsów API OSS przez zapewnienie **ujednoliconego środowiska bezserwerowego dla Apache Spark** za pomocą modeli danych typu klucz-wartość, dokument, Graf i rodzina kolumn. Różne modele danych są obsługiwane za pomocą interfejsów API MongoDB, Cassandra, Gremlin, Etcd i SQL — wszystkich działających na tych samych danych bazowych. 

Dzięki obsłudze Apache Spark w Azure Cosmos DB można natywnie obsługiwać aplikacje Scala, Python, Java i korzystać z kilku ściśle zintegrowanych bibliotek dla języka SQL. Te biblioteki obejmują ([Spark SQL](https://spark.apache.org/sql/)), Machine Learning (Spark [MLlib](https://spark.apache.org/mllib/)), przetwarzanie strumieni (strumień[strukturalny Spark](https://spark.apache.org/streaming/)) i przetwarzanie grafu (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Te narzędzia ułatwiają używanie Apache Spark do różnych przypadków użycia. Nie musisz obsługiwać zarządzania klastrami Spark i Spark. Możesz użyć znanych interfejsów API Apache Spark i **notesów Jupyter** na potrzeby analiz i interfejsu API SQL lub dowolnego interfejsu API usługi OSS NoSQL, takiego jak Cassandra do przetwarzania transakcyjnego na tych samych danych źródłowych w tym samym czasie.

### <a name="no-schema-or-index-management"></a>Bez zarządzania schematami lub indeksami

W przeciwieństwie do tradycyjnych analitycznych baz danych, w przypadku Azure Cosmos DB, inżynierów danych i analityków danych nie trzeba już rozwiązywać żadnych nieskomplikowanych zarządzania schematami i indeksami. Aparat bazy danych w Azure Cosmos DB nie wymaga żadnego jawnego zarządzania schematem lub indeksem i może automatycznie indeksować wszystkie dane, które pozyskają, aby szybko obsłużyć Apache Sparke zapytania. 

### <a name="consistency-choices"></a>Opcje spójności

Ponieważ Apache Spark zadania są wykonywane w partycjach danych bazy danych Azure Cosmos, w kwerendach zostaną wyświetlone [pięć dobrze zdefiniowanych opcji spójności](consistency-levels.md). Te modele spójności zapewniają elastyczność pozwalającą wybrać ścisłą spójność, aby zapewnić najbardziej precyzyjne wyniki dla algorytmów uczenia maszynowego bez wpływu na opóźnienia i wysoką dostępność. 

### <a name="comprehensive-slas"></a>Kompleksowe umowy SLA

Apache Spark zadania będą mieć Azure Cosmos DB korzyści, takie jak wiodące w branży kompleksowe [umowy SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999), bez jakichkolwiek nakładów związanych z zarządzaniem oddzielnymi klastrami Apache Spark. Te umowy SLA obejmują przepływność, opóźnienie w 99 percentylu, spójności i wysokiej dostępności. 

### <a name="mixed-workloads"></a>Obciążenia mieszane

Integracja Apache Spark do Azure Cosmos DB mostkuje rozdzielenia transakcyjnego i analitycznego, który był jednym z głównych punktów sprzedaży, podczas tworzenia aplikacji natywnych dla chmury w skali globalnej. 

## <a name="scenarios-for-azure-cosmos-db-spark-support"></a>Scenariusze obsługi Azure Cosmos DB Spark

### <a name="retail-and-consumer-goods"></a>Sprzedaż detaliczna i towary konsumenckie

Możesz użyć obsługi platformy Spark w Azure Cosmos DB, aby dostarczyć rekomendacje i oferty w czasie rzeczywistym. Możesz pomóc klientom w znalezieniu elementów, których potrzebują, z personalizacją w czasie rzeczywistym i zaleceniami dotyczącymi produktów.

* Możesz użyć wbudowanej obsługi Machine Learning dostarczonej przez środowisko uruchomieniowe Apache Spark, aby wygenerować zalecenia w czasie rzeczywistym w ramach katalogów produktów.

* Możesz kliknąć pozycję dane przesyłane strumieniowo, kupić dane i dane klientów, aby zapewnić zamierzone zalecenia dotyczące wartości okresu istnienia.

* Korzystając z funkcji globalnej dystrybucji Azure Cosmos DB, duże ilości danych produktu, które są rozmieszczone w różnych regionach, można analizować w milisekundach.

* Możesz szybko uzyskać wgląd w szczegółowe informacje dla użytkowników i danych rozproszonych geograficznie. Możesz poprawić szybkość konwersji dla promocji, udostępniając odpowiednią reklamę odpowiednim użytkownikom w odpowiednim czasie.

* Możesz wykorzystać wbudowaną funkcję przetwarzania strumieniowego Spark do wzbogacania danych na żywo, łącząc ją ze statycznymi danymi klienta. Dzięki temu możesz dostarczać bardziej spersonalizowanych i strategicznych reklam w czasie rzeczywistym i w kontekście działania wykonywanego przez klientów.

Na poniższej ilustracji przedstawiono sposób, w jaki Azure Cosmos DB obsługa platformy Spark służy do optymalizowania cen i promocji:

![Azure Cosmos DB obsługa platformy Spark w celu zoptymalizowania cen i promocji](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


Na poniższej ilustracji przedstawiono, jak Azure Cosmos DB obsługa platformy Spark jest używana w aparacie rekomendacji w czasie rzeczywistym:

![Azure Cosmos DB obsługa platformy Spark w aparacie rekomendacji w czasie rzeczywistym](./media/spark-api-introduction/real-time-recommendation-engine.png)

### <a name="manufacturing-and-iot"></a>Produkcja i IoT

Wbudowana platforma analityczna Azure Cosmos DB umożliwia włączenie analizy danych IoT w czasie rzeczywistym z milionów urządzeń na skalę globalną. Możesz tworzyć nowoczesne innowacje, takie jak przewidywanie wzorców pogodowych, analizy predykcyjnej i optymalizacji zużycia energii.

* Za pomocą Azure Cosmos DB można pogodzić dane, takie jak metryki zasobów w czasie rzeczywistym i czynniki pogodowe, a następnie zastosować inteligentne analizy siatki w celu zoptymalizowania wydajności podłączonych urządzeń w polu. Inteligentna analiza siatki jest kluczem do kontrolowania kosztów operacyjnych, zwiększania niezawodności sieci i dostarczania spersonalizowanych usług energetycznych do klientów.

Na poniższej ilustracji przedstawiono, w jaki sposób obsługa platformy Spark w Azure Cosmos DB jest używana do odczytywania metryk z urządzeń IoT i stosowania inteligentnej analizy siatki:

![Azure Cosmos DB obsługa platformy Spark w celu odczytywania metryk z urządzeń IoT](./media/spark-api-introduction/read-metrics-from-iot-devices.png)


### <a name="predictive-maintenance"></a>Konserwacja predykcyjna

* Utrzymywanie zasobów takich jak kompresory, które są używane w małych zbiorach wiertniczych na platformach głębokiej wody, jest złożonym Endeavor. Te zasoby znajdują się na całym świecie i generują petabajtów danych. Za pomocą Azure Cosmos DB można utworzyć kompleksowy Potok danych predykcyjnych, który korzysta z przesyłania strumieniowego Spark, aby przetwarzać duże ilości danych telemetrycznych czujnika, części zasobów magazynu i mapowania czujników.

* Możesz tworzyć i wdrażać modele uczenia maszynowego w celu przewidywania niepowodzeń zasobów przed ich wystąpieniem i wystawiać zlecenia służbowe do konserwacji przed wystąpieniem awarii.

Na poniższej ilustracji przedstawiono, w jaki sposób obsługa platformy Spark Azure Cosmos DB jest używana do tworzenia systemu konserwacji predykcyjnej:

![Azure Cosmos DB obsługi platformy Spark w celu utworzenia systemu konserwacji predykcyjnej](./media/spark-api-introduction/predictive-maintenance-system.png)

Na poniższej ilustracji przedstawiono, w jaki sposób obsługa platformy Spark Azure Cosmos DB jest używana do kompilowania systemu diagnostycznego w czasie rzeczywistym:

![Azure Cosmos DB obsługi platformy Spark w celu utworzenia systemu diagnostycznego w czasie rzeczywistym](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

### <a name="gaming"></a>Gry

* Dzięki wbudowanej obsłudze platformy Spark Azure Cosmos DB umożliwia łatwe tworzenie, skalowanie i wdrażanie zaawansowanych modeli analizy i uczenia maszynowego w ciągu kilku minut w celu utworzenia najlepszej możliwego środowiska gier.

* Możesz analizować dane dotyczące odtwarzacza, zakupu i zachowania, aby utworzyć odpowiednie spersonalizowane oferty, aby osiągnąć wysoką szybkość konwersji.

* Za pomocą usługi Spark Machine Learning można analizować i uzyskiwać wgląd w dane telemetryczne gier. Można diagnozować i zapobiegać spowolnieniom czasów ładowania i problemów w grach.

Na poniższej ilustracji przedstawiono, w jaki sposób obsługa platformy Spark w Azure Cosmos DB jest używana w analizie gier:

![Obsługa platformy Spark Azure Cosmos DB w analizie gier](./media/spark-api-introduction/gaming-analytics.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o zaletach Azure Cosmos DB, zobacz artykuł z [omówieniem](introduction.md) .
* [Wprowadzenie do interfejsu API Azure Cosmos DB MongoDB](mongodb-introduction.md)
* [Rozpocznij pracę z Azure Cosmos DB interfejs API Cassandra](cassandra-introduction.md)
* [Wprowadzenie do interfejsu API Azure Cosmos DB Gremlin](graph-introduction.md)
* [Rozpocznij pracę z Azure Cosmos DB interfejs API tabel](table-introduction.md)




