---
title: 'Azure DB rozwiązania Cosmos: Wykonywać analizy wykresu przy użyciu platformy Spark i Apache TinkerPop Gremlin | Dokumentacja firmy Microsoft'
description: Ten artykuł zawiera instrukcje dotyczące konfigurowania i uruchamiania wykres analizy i równolegle obliczeń w usłudze Azure DB rozwiązania Cosmos z platformy Spark i TinkerPop SparkGraphComputer.
services: cosmosdb
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.custom: quick start connect
ms.devlang: gremlin
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: sngun
ms.openlocfilehash: 7cc68bfabff5a3afafb89bdb6bb393480946a7a7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796043"
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure DB rozwiązania Cosmos: Wykonywać analizy wykresu przy użyciu platformy Spark i Apache TinkerPop Gremlin

[Azure DB rozwiązania Cosmos](introduction.md) globalnie rozproszone i wiele modeli bazy danych usługi firmy Microsoft. Można tworzyć i kwerend dokumentu, klucza i wartości i wykres baz danych, które korzystają z możliwości globalnego dystrybucji i skalowania poziomego Fundament bazy danych Azure rozwiązania Cosmos. Obciążenia graph (OLTP), które wykorzystują przetwarzania transakcji online obsługuje bazę danych systemu Azure rozwiązania Cosmos [Apache TinkerPop Gremlin](graph-introduction.md).

[Platforma Spark](http://spark.apache.org/) jest Apache Software Foundation projektu, który koncentruje się na przetwarzanie danych ogólnego przeznaczenia przetwarzania analitycznego online (OLAP). Spark zapewnia hybrydowych w pamięci/opartej na dysku rozproszonego przetwarzania danych model podobny do modelu MapReduce z Hadoop. Apache Spark w chmurze można wdrożyć za pomocą [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

Połączenie bazy danych Azure rozwiązania Cosmos z Spark, możesz wykonać obciążeń zarówno OLTP i OLAP używania Gremlin. W tym artykule szybki start przedstawiono sposób wykonywania kwerend Gremlin do bazy danych Azure rozwiązania Cosmos w klastrze usługi Azure HDInsight Spark.

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem tego przykładu muszą być spełnione następujące wymagania wstępne:
* Azure HDInsight Spark klastra 2.0
* JDK 1.8 + (Jeśli nie masz JDK, uruchom `apt-get install default-jdk`.)
* Maven (Jeśli nie masz Maven, uruchom `apt-get install maven`.)
* Subskrypcja platformy Azure ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Aby uzyskać informacje o sposobie konfigurowania klastra Spark w usłudze HDInsight Azure, zobacz [klastrów inicjowania obsługi usługi HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Tworzenie konta bazy danych Azure DB rozwiązania Cosmos

Najpierw utwórz konto bazy danych z interfejsu API programu Graph, wykonując następujące czynności:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>Dodawanie kolekcji

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Pobierz Apache TinkerPop

Pobierz Apache TinkerPop, wykonując następujące czynności:

1. Zdalny do węzła głównego klastra usługi HDInsight `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`.

2. Klonowanie kodu źródłowego TinkerPop3, skompiluj go lokalnie i zainstaluj go w pamięci podręcznej Maven.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Zainstaluj Spark Gremlin wtyczki 

    a. Instalacja dodatku jest obsługiwany przez moszcz. Wypełnij informacje repozytoria gronowego, można pobrać wtyczki i jego zależności. 

      Utwórz plik konfiguracji winogronowego, jeśli nie jest obecny w `~/.groovy/grapeConfig.xml`. Użyj następujących ustawień:

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. Uruchom konsolę Gremlin `bin/gremlin.sh`.
        
    c. Zainstaluj Spark Gremlin wtyczki z wersją 3.3.0-SNAPSHOT, które zostały utworzone w poprzednich krokach:

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. Sprawdź, czy `Hadoop-Gremlin` jest aktywowany przy `:plugin list`. Wyłącz ten dodatek, ponieważ może zakłócać Spark Gremlin wtyczki `:plugin unuse tinkerpop.hadoop`.

## <a name="prepare-tinkerpop3-dependencies"></a>Przygotowanie TinkerPop3 zależności

Gdy TinkerPop3 jest utworzony w poprzednim kroku, proces również pobierane wszystkie zależności jar dla platformy Spark i Hadoop w katalogu docelowym. Użyj słoików, które są wstępnie zainstalowane z HDI i ściągnąć dodatkowe zależności tylko niezbędne.

1. Przejdź do katalogu docelowego konsoli Gremlin na `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

2. Przenieś wszystkie słoików w obszarze `ext/` do `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`.

3. Usuń wszystkie jar biblioteki w obszarze `lib/` nie znajdują się w następującej listy:

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Pobierz łącznik usługi Azure Spark DB rozwiązania Cosmos

1. Pobierz łącznik usługi Azure Spark DB rozwiązania Cosmos `azure-cosmosdb-spark-0.0.3-SNAPSHOT.jar` i rozwiązania Cosmos DB Java SDK `azure-documentdb-1.12.0.jar` z [łącznika usługi Azure rozwiązania Cosmos DB Spark w usłudze GitHub](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11).

2. Alternatywnie można utworzyć ją lokalnie. Ponieważ najnowsza wersja Spark Gremlin został skompilowany z Spark 1.6.1 i nie jest zgodny z Spark pkt 2.0.2, który jest obecnie używana w łączniku Spark DB rozwiązania Cosmos Azure, możesz skompilować kod TinkerPop3 najnowsze i ręcznie zainstalować słoików. Wykonaj następujące czynności:

    a. Klonowanie łącznika usługi Azure Spark DB rozwiązania Cosmos.

    b. Tworzenie TinkerPop3 (już wykonane w poprzednich krokach). Zainstaluj wszystkie słoików TinkerPop 3.3.0-SNAPSHOT lokalnie.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. Aktualizacja `tinkerpop.version` `azure-documentdb-spark/pom.xml` do `3.3.0-SNAPSHOT`.
    
    d. Kompiluj z Maven. Słoików potrzebne są umieszczane w `target` i `target/alternateLocation`.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Skopiuj słoików wymienione wcześniej w lokalnym katalogu na ~ / azure-documentdb-spark:

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Dystrybuuj zależności z węzłami procesu roboczego Spark 

1. Ponieważ przekształcania danych wykresu jest zależna od TinkerPop3, musisz przeprowadzić dystrybucję powiązane zależności do wszystkich węzłów procesu roboczego platformy Spark.

2. Skopiuj wymienione wcześniej zależności Gremlin, CosmosDB Spark jar łącznika i zestawu SDK Java CosmosDB do węzłów procesu roboczego w następujący sposób:

    a. Skopiuj wszystkie słoików do `~/azure-documentdb-spark`.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. Pobierz listę wszystkich węzłów procesu roboczego Spark, które można znaleźć na pulpicie nawigacyjnym Ambari, w `Spark2 Clients` na liście `Spark2` sekcji.

    c. Kopiowanie katalogu do każdego z węzłów.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Ustawianie zmiennych środowiskowych

1. Znajdź wersję HDP klastra Spark. Jest to nazwa katalogu, w obszarze `/usr/hdp/` (na przykład 2.5.4.2-7).

2. Ustaw hdp.version dla wszystkich węzłów. Na pulpicie nawigacyjnym Ambari, przejdź do **sekcji YARN** > **Configs** > **zaawansowane**, a następnie wykonaj następujące czynności: 
 
    a. W `Custom yarn-site`, Dodaj nową właściwość `hdp.version` o wartości wersji HDP w węźle głównym. 
     
    b. Zapisz konfiguracje. Wystąpiły ostrzeżenia, które można zignorować. 
     
    c. Uruchom ponownie usługi YARN i Oozie, jak wskazują ikony powiadomień.

3. Ustaw następujące zmienne środowiskowe w węźle głównym (Zastąp wartości zależnie od potrzeb):

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Przygotowanie konfiguracji wykresu

1. Utwórz plik konfiguracji z ustawieniami Spark i parametry połączenia bazy danych Azure rozwiązania Cosmos i umieszcza je w `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Aktualizacja `spark.driver.extraClassPath` i `spark.executor.extraClassPath` można dołączyć podanego katalogu słoików, rozproszonych w poprzednim kroku, w tym przypadku `/home/sshuser/azure-documentdb-spark/*`.

3. Dla bazy danych rozwiązania Cosmos platformy Azure, należy podać następujące dane:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>Załadować wykresu TinkerPop i zapisać go do bazy danych Azure rozwiązania Cosmos
Aby zademonstrować sposób utrwalić wykres do bazy danych z rozwiązania Cosmos platformy Azure, w tym przykładzie używa TinkerPop wstępnie zdefiniowane TinkerPop nowoczesnych wykresu. Wykres są przechowywane w formacie Kryo i jest dostępny w repozytorium TinkerPop.

1. Ponieważ Gremlin działają w trybie YARN, należy udostępnić dane wykresu w systemie plików usługi Hadoop. Użyj następujących poleceń, aby udostępnić katalogu i skopiuj do niego pliku lokalnego wykresu. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Tymczasowo zaktualizować `gremlin-spark.properties` plik `GryoInputFormat` do odczytu na wykresie. Również wskazywać `inputLocation` jako katalog można utworzyć, co przedstawiono poniżej:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Uruchom konsolę Gremlin, a następnie utwórz następujące kroki obliczeń do utrwalenia danych do skonfigurowanej kolekcji bazy danych rozwiązania Cosmos Azure:  

    a. Utwórz wykres `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`.

    b. Użyj SparkGraphComputer do zapisu `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. Z poziomu Eksploratora danych można sprawdzić utrwalone dane do bazy danych Azure rozwiązania Cosmos.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>Ładowanie wykres z bazy danych Azure rozwiązania Cosmos i uruchamiać zapytania Gremlin

1. Aby załadować wykresu, należy edytować `gremlin-spark.properties` można ustawić `graphReader` do `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Załadować wykresu, przechodzenie przez dane i uruchamiać zapytania Gremlin z nim, wykonując następujące czynności:

    a. Uruchom konsolę Gremlin `bin/gremlin.sh`.

    b. Utwórz wykres z konfiguracją `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`.

    c. Utwórz przechodzenie wykres z SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`.

    d. Uruchom następujące kwerendy wykres Gremlin:

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> Aby wyświetlić bardziej szczegółowe rejestrowanie, należy ustawić dziennika w `conf/log4j-console.properties` na pełniejsze poziom.
>

## <a name="next-steps"></a>Kolejne kroki

W tym artykule szybki start kiedy znasz już sposób pracy z wykresy przez połączenie bazy danych Azure rozwiązania Cosmos i Spark.

> [!div class="nextstepaction"]
