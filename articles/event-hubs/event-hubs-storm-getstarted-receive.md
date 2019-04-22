---
title: Odbieranie zdarzeń za pomocą platformy Apache Storm — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat sposobu odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu platformy Apache Storm.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 75a96127c48186befc48b2240f78e49cd5914239
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679265"
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>Odbieranie zdarzeń z usługi Event Hubs przy użyciu platformy Apache Storm

[Apache Storm](https://storm.incubator.apache.org) to system rozproszonych obliczeń w czasie rzeczywistym, który upraszcza niezawodne przetwarzanie niepowiązanych strumieniach danych. W tej sekcji pokazano, jak za pomocą usługi Azure Event Hubs Storm spout odbieranie zdarzeń z usługi Event Hubs. Korzystanie z systemu Apache Storm, można podzielić zdarzenia między wieloma procesami hostowania w różnych węzłach. Integracja usługi Event Hubs przy użyciu systemu Storm upraszcza przyjmowania zdarzeń w sposób niewidoczny dla użytkownika punkt kontrolny z jej postęp przy użyciu systemu Storm w dozorcy instalacji, zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami z tej usługi Event Hubs.

Aby uzyskać więcej informacji na temat usługi Event Hubs otrzymywać wzorców, zobacz [Przegląd usługi Event Hubs][Event Hubs overview].

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z tego przewodnika Szybki Start **tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń**. Użyj [witryny Azure portal](https://portal.azure.com) do utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z Centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Utwórz projekt i dodać kod

W tym samouczku [HDInsight Storm] [ HDInsight Storm] instalacji, który jest dostarczany z już dostępną spout usługi Event Hubs.

1. Postępuj zgodnie z [HDInsight Storm — wprowadzenie](../hdinsight/storm/apache-storm-overview.md) procedurę, aby utworzyć nowy klaster HDInsight i nawiązać z nim za pośrednictwem pulpitu zdalnego.
2. Kopiuj `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` plik do swojego lokalnego środowiska deweloperskiego. Zawiera spout storm zdarzenia.
3. Użyj następującego polecenia, aby zainstalować pakiet w lokalnym magazynie narzędzia Maven. Dzięki temu można dodać go jako odwołania w projekcie systemu Storm w późniejszym kroku.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. W programie Eclipse Utwórz nowy projekt narzędzia Maven (kliknij **pliku**, następnie **New**, następnie **projektu**).
   
    ![Plik -> Nowy -> Projekt][12]
5. Wybierz **Użyj domyślną lokalizację obszaru roboczego**, następnie kliknij przycisk **dalej**
6. Wybierz **maven-archetype-quickstart** archetype, następnie kliknij przycisk **dalej**
7. Wstaw **GroupId** i **ArtifactId**, następnie kliknij przycisk **Zakończ**
8. W **pom.xml**, Dodaj następujące zależności w `<dependency>` węzła.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. W **src** folderze utwórz plik o nazwie **Config.properties** i skopiuj następujące zawartości, zastępując `receive rule key` i `event hub name` wartości:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    Wartość **eventhub.receiver.credits** Określa, ile zdarzeń są przetwarzane wsadowo przed ich do potoku Storm. Dla uproszczenia w tym przykładzie ustawia tę wartość do 10. W środowisku produkcyjnym należy zwykle ją ustawić na wyższe wartości. na przykład 1024.
10. Utwórz nową klasę o nazwie **LoggerBolt** następującym kodem:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Ten element bolt Storm rejestruje zawartość odebranych zdarzeń. To może łatwo rozszerzyć, aby przechowywać krotek w usłudze magazynu. [HDInsight Storm przykładu Centrum zdarzeń] używa tej samej metody do przechowywania danych w usłudze Azure Storage i Power BI.
11. Utwórz klasę o nazwie **LogTopology** następującym kodem:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Ta klasa tworzy nowe spout usługi Event Hubs przy użyciu właściwości w pliku konfiguracji do tworzenia jego instancji. Należy pamiętać, w tym przykładzie tworzy zadań elementów spout jako liczba partycji w Centrum zdarzeń, aby można było używać równoległości maksymalną dozwoloną przez tego Centrum zdarzeń.

## <a name="next-steps"></a>Kolejne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs][Event Hubs overview]
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[HDInsight Storm przykładu Centrum zdarzeń]: https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
