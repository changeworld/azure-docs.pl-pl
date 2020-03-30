---
title: 'Szybki start: odbieranie zdarzeń przy użyciu usługi Apache Storm — usługi Azure Event Hubs'
description: 'Szybki start: Ten artykuł zawiera informacje na temat sposobu odbierania zdarzeń z usługi Azure Event Hubs przy użyciu usługi Apache Storm.'
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
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 90293da07d3a7ef1c32e5f82d35198d4ffa536b1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73717612"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Szybki start: odbieranie zdarzeń z centrów zdarzeń przy użyciu usługi Apache Storm

[Apache Storm](https://storm.incubator.apache.org) to rozproszony system obliczeń w czasie rzeczywistym, który upraszcza niezawodne przetwarzanie nieograniczonych strumieni danych. W tej sekcji pokazano, jak używać wylewki Burzy usług Azure Event Hubs do odbierania zdarzeń z centrów zdarzeń. Korzystając z usługi Apache Storm, można podzielić zdarzenia na wiele procesów hostowanych w różnych węzłach. Integracja centrów zdarzeń z storm upraszcza zużycie zdarzeń, w sposób przejrzysty, wykonując punkty kontrolne jego postępy przy użyciu instalacji Zookeeper storm, zarządzając trwałymi punktami kontrolnymi i równoległymi odbieraniami z Centrów zdarzeń.

Aby uzyskać więcej informacji o wzorcach odbierania w centrach zdarzeń, zobacz [omówienie centrów zdarzeń][Event Hubs overview].

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z **przewodnikiem**Szybki start utwórz obszar nazw centrum zdarzeń i centrum zdarzeń . Użyj [witryny Azure Portal,](https://portal.azure.com) aby utworzyć obszar nazw typu Usługi zdarzeń i uzyskać poświadczenia zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Tworzenie projektu i dodawanie kodu

W tym samouczku użyto instalacji [hdinsight storm,][HDInsight Storm] która jest dostępna z wylewką centrum zdarzeń już dostępną.

1. Postępuj zgodnie z procedurą [HDInsight Storm — wprowadzenie,](../hdinsight/storm/apache-storm-overview.md) aby utworzyć nowy klaster HDInsight i połączyć się z nim za pośrednictwem pulpitu zdalnego.
2. Skopiuj `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` plik do lokalnego środowiska programistycznego. Zawiera zdarzenia storm-spout.
3. Użyj następującego polecenia, aby zainstalować pakiet w lokalnym magazynie Maven. Dzięki temu można dodać go jako odwołanie w projekcie Storm w późniejszym kroku.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. W programie Eclipse utwórz nowy projekt Maven (kliknij przycisk **Plik**, a następnie **Nowy**, a następnie **Project**).
   
    ![Plik -> Nowy -> Projekt][12]
5. Wybierz **pozycję Użyj domyślnej lokalizacji obszaru roboczego,** a następnie kliknij przycisk **Dalej**
6. Wybierz **archetyp maven-archetypu archiotów i paskuszek szybki start,** a następnie kliknij przycisk **Dalej**
7. Wstawianie **obiektu GroupId** i **artifactId**, a następnie kliknij przycisk **Zakończ**
8. W **pom.xml**dodaj następujące zależności `<dependency>` w węźle.

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

9. W folderze **src** utwórz plik o nazwie **Config.properties** i `receive rule key` skopiuj następującą zawartość, zastępując i `event hub name` wartości:

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
    Wartość **eventhub.receiver.credits** określa, ile zdarzeń jest wsadowych przed zwolnieniem ich do potoku Storm. Ze względu na prostotę w tym przykładzie ustawia tę wartość na 10. W produkcji, to zwykle powinny być ustawione na wyższe wartości; na przykład 1024.
10. Utwórz nową klasę o nazwie **LoggerBolt** z następującym kodem:
    
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
    
    Ta śruba Storm rejestruje zawartość otrzymanych zdarzeń. Można to łatwo rozszerzyć do przechowywania krotek w usłudze magazynu. W [przykładzie usługi HDInsight Storm with Event Hub] użyto tego samego podejścia do przechowywania danych w usłudze Azure Storage i usłudze Power BI.
11. Utwórz klasę o nazwie **LogTopology** z następującym kodem:
    
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

    Ta klasa tworzy nową wylewkę centrum zdarzeń, używając właściwości w pliku konfiguracyjnym do wystąpienia go. Należy pamiętać, że w tym przykładzie tworzy tyle wylewek zadań, jak liczba partycji w centrum zdarzeń, aby użyć maksymalnego równoległości dozwolone przez tego centrum zdarzeń.

## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs][Event Hubs overview]
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Przykład usługi HDInsight Storm z Centrum zdarzeń]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
