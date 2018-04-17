---
title: Używających Apache Kafka Storm w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Apache Kafka jest instalowany z systemu Apache Storm w usłudze HDInsight. Dowiedz się, jak zapisać Kafka, a następnie przeczytaj z niego, przy użyciu składników KafkaBolt i KafkaSpout, ile z systemu Storm. Również Dowiedz się, jak za pomocą architektury strumień do definiowania i przesyłania topologii Storm.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: larryfr
ms.openlocfilehash: be62705ce0217235b75ec5ad220ad6f32dfd3c10
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-apache-kafka-with-storm-on-hdinsight"></a>Używających Apache Kafka Storm w usłudze HDInsight

Dowiedz się, jak używać systemu Apache Storm do odczytu i zapisu Apache Kafka. Również w tym przykładzie pokazano, jak zapisać dane od topologii Storm do systemu plików HDFS zgodnego używane przez usługi HDInsight.

> [!NOTE]
> Kroki opisane w tym dokumencie Tworzenie grupy zasobów platformy Azure, który zawiera zarówno Storm w usłudze HDInsight i Kafka w klastrze usługi HDInsight. Te klastry są oba znajdujących się w sieci wirtualnej platformy Azure, dzięki czemu klaster Storm do bezpośredniego komunikowania się z Kafka klastra.
> 
> Po zakończeniu kroków w tym dokumencie, pamiętaj, aby usuwać te klastry, aby zapobiec zmianom nadmiarowe.

## <a name="get-the-code"></a>Uzyskiwanie kodu

Kod używany w przykładzie podanym w tym dokumencie jest dostępny na [ https://github.com/Azure-Samples/hdinsight-storm-java-kafka ](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Aby skompilować ten projekt, należy następującej konfiguracji dla swojego środowiska programowania:

* [Java JDK 1.8](http://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) lub nowszej. HDInsight w wersji 3.5 lub nowszej wymagają Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Klient SSH (należy `ssh` i `scp` poleceń) — Aby uzyskać informacje, zobacz [używanie SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Edytor tekstu lub IDE.

Po zainstalowaniu na deweloperskiej stacji roboczej Java i zestaw JDK, który można konfigurować następujące zmienne środowiskowe. Jednak należy sprawdzić, czy istnieją i że zawierają one poprawne wartości dla systemu.

* `JAVA_HOME` -powinny wskazywać na katalog, w którym jest zainstalowany zestaw JDK.
* `PATH` — powinna zawierać następujących ścieżkach:
  
    * `JAVA_HOME` (lub równoważne ścieżki).
    * `JAVA_HOME\bin` (lub równoważne ścieżki).
    * Katalog, w którym zainstalowano Maven.

## <a name="create-the-clusters"></a>Tworzenie klastrów

Kafka Apache na HDInsight nie zapewnia dostępu do brokerzy Kafka za pośrednictwem publicznej sieci internet. Wszystkie elementy, które komunikuje się Kafka musi być w tej samej sieci wirtualnej platformy Azure jako węzły w klastrze Kafka. W tym przykładzie zarówno Kafka, jak i Storm klastrach znajdują się w sieci wirtualnej platformy Azure. Na poniższym diagramie przedstawiono sposób komunikacji przepływa między klastrami:

![Diagram klastrów Storm i Kafka w sieci wirtualnej platformy Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Inne usługi w klastrze, takich jak SSH i Ambari są dostępne za pośrednictwem Internetu. Aby uzyskać więcej informacji na portach publicznego dostępne z usługą HDInsight, zobacz [portów i identyfikatory URI używany przez HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Podczas tworzenia sieci wirtualnej platformy Azure, Kafka, i ręcznie klastrów Storm, łatwiej jest użyć szablonu usługi Azure Resource Manager. Wykonaj następujące kroki, aby wdrożyć sieci wirtualnej platformy Azure, Kafka, a Storm klastrów do subskrypcji platformy Azure.

1. Poniższy przycisk umożliwia logowanie do platformy Azure i otwórz szablon w portalu Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Szablon usługi Azure Resource Manager znajduje się pod adresem **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json**. Tworzy następujące zasoby:
    
    * Grupa zasobów platformy Azure
    * Azure Virtual Network
    * Konto usługi Azure Storage
    * Kafka w usłudze HDInsight w wersji 3,6 (trzech węzłów procesu roboczego)
    * STORM w usłudze HDInsight w wersji 3,6 (trzech węzłów procesu roboczego)

  > [!WARNING]
  > Aby zapewnić dostępność platformy Kafka w usłudze HDInsight, klaster musi zawierać co najmniej trzy węzły procesu roboczego. Ten szablon umożliwia tworzenie klastra Kafka, który zawiera trzy węzłów procesu roboczego.

2. Użyj poniższych wskazówek, aby wypełnić wpisy na **wdrożenie niestandardowe** sekcji:
   
    ![Niestandardowe wdrożenie usługi HDInsight](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Grupa zasobów**: Utwórz grupę lub wybierz istniejący. Ta grupa zawiera klastra usługi HDInsight.
   
    * **Lokalizacja**: Wybierz lokalizację lokalizacji geograficznej blisko.

    * **Podstawowa nazwa klastra**: Ta wartość jest używana jako nazwa podstawowa dla Storm i klastry Kafka. Na przykład wprowadzenie **hdi** jest tworzony klaster Storm o nazwie **storm hdi** i Kafka klastra o nazwie **kafka hdi**.
   
    * **Nazwa użytkownika logowania klastra**: nazwa użytkownika administratora w przypadku klastrów Storm i Kafka.
   
    * **Hasło logowania klastra**: hasła użytkownika administratora w przypadku klastrów Storm i Kafka.
    
    * **Nazwa użytkownika SSH**: użytkownika SSH do tworzenia klastrów Storm i Kafka.
    
    * **Hasło SSH**: hasło dla użytkownika SSH w przypadku klastrów Storm i Kafka.

3. Odczyt **warunków i postanowień**, a następnie wybierz **akceptuję warunki i postanowienia, o których wspomniano**.

4. Ponadto sprawdź **Przypnij do pulpitu nawigacyjnego** , a następnie wybierz **zakupu**. Tworzenie klastrów trwa około 20 minut.

Po utworzeniu zasobów jest wyświetlany w sekcji grupy zasobów.

![Sekcja grupy zasobów dla sieci wirtualnej i klastrów](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Należy zauważyć, że nazwy klastrów usługi HDInsight są **nazwę BAZOWĄ storm** i **nazwę BAZOWĄ kafka**, gdzie nazwę BAZOWĄ jest podana nazwa do szablonu. Te nazwy można użyć w kolejnych krokach, podczas nawiązywania połączenia z klastrów.

## <a name="understanding-the-code"></a>Opis kodu

Ten projekt zawiera dwie topologie:

* **KafkaWriter**: zdefiniowane przez **writer.yaml** pliku, ta topologia zapisuje losowe zdań Kafka przy użyciu KafkaBolt dostarczane z systemu Apache Storm.

    Ta topologia używa niestandardowego **SentenceSpout** składnik do generowania losowego zdań.

* **KafkaReader**: zdefiniowane przez **reader.yaml** plik, ta topologia odczytuje dane z Kafka przy użyciu KafkaSpout dostarczane z systemu Apache Storm, a następnie rejestruje dane do stdout.

    Ta topologia używa Storm HdfsBolt do zapisu danych do magazynu domyślnego dla klastra Storm.
### <a name="flux"></a>Flux

Topologie są definiowane przy użyciu [strumień](https://storm.apache.org/releases/1.1.2/flux.html). Strumień została wprowadzona w systemie Storm 0.10.x i umożliwia rozdzielenie konfiguracji topologii z kodu. Dla topologie, wykorzystujące framework strumienia topologia jest zdefiniowany w pliku yaml programu. Może to być plik yaml programu zawarte w ramach topologii. Można także użyć podczas przesyłania topologii autonomiczny plik. Strumień obsługuje również podstawienie zmiennej w czasie wykonywania, który jest używany w tym przykładzie.

Następujące parametry są ustawione w czasie wykonywania dla tych topologii:

* `${kafka.topic}`: Nazwa Kafka temat topologii odczytu/zapisu do.

* `${kafka.broker.hosts}`: Uruchom hostów, które przekazuje informacje dotyczące Kafka na. Informacje o brokera jest używany przez KafkaBolt podczas zapisywania Kafka.

* `${kafka.zookeeper.hosts}`: Hostów, które dozorcy działa na Kafka klastra.

Aby uzyskać więcej informacji dotyczących topologii strumienia, zobacz [ https://storm.apache.org/releases/1.1.2/flux.html ](https://storm.apache.org/releases/1.1.2/flux.html).

## <a name="download-and-compile-the-project"></a>Pobierz i kompilacji projektu

1. Na środowiska deweloperskiego pobrać projekt z [ https://github.com/Azure-Samples/hdinsight-storm-java-kafka ](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), Otwórz wiersza polecenia i przejdź do lokalizacji został pobrany projekt.

2. Z **hdinsight-storm-java-kafka** katalogu, użyj następującego polecenia, aby skompilować projekt i utworzenie pakietu wdrażania:

  ```bash
  mvn clean package
  ```

    Proces pakietu tworzy plik o nazwie `KafkaTopology-1.0-SNAPSHOT.jar` w `target` katalogu.

3. Użyj następujących poleceń, aby skopiować pakiet do Storm w klastrze usługi HDInsight. Zastąp **USERNAME** z nazwą użytkownika SSH dla klastra. Zastąp **nazwę BAZOWĄ** o nazwie podstawowej używane podczas tworzenia klastra.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    Po wyświetleniu monitu wprowadź hasło, które zostały użyte podczas tworzenia klastrów.

## <a name="configure-the-topology"></a>Konfigurowanie topologii

1. Jedną z następujących metod używane do wykrywania hostów brokera Kafka dla **Kafka** w klastrze usługi HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]
    > W poniższym przykładzie Bash, przy założeniu, że `$CLUSTERNAME` zawiera nazwę __Kafka__ nazwy klastra. Założono również, że [jq](https://stedolan.github.io/jq/) jest zainstalowany w wersji 1.5 lub nowszego. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    Wartość zwracana jest podobny do następującego tekstu:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Może być więcej niż dwa hosty broker dla klastra, nie trzeba zapewniają pełną listę wszystkich hostów do klientów. Jedno lub dwa jest wystarczająca.

2. Użyj jednej z następujących metod, aby dowiedzieć się, dozorcy obsługuje dla __Kafka__ w klastrze usługi HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]
    > W poniższym przykładzie Bash, przy założeniu, że `$CLUSTERNAME` zawiera nazwę __Kafka__ klastra. Założono również, że [jq](https://stedolan.github.io/jq/) jest zainstalowany. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    Wartość zwracana jest podobny do następującego tekstu:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > W czasie więcej niż dwa węzły dozorcy zapewniają pełną listę wszystkich hostów do klientów nie jest konieczne. Jedno lub dwa jest wystarczająca.

    Zapisz tę wartość, ponieważ jest używana później.

3. Edytuj `dev.properties` w katalogu głównym projektu. Dodaj informacje o hostach brokera i dozorcy dla __Kafka__ klastra zgodnych wierszy w tym pliku. Poniższy przykład jest konfigurowana przy użyciu przykładowe wartości z poprzednich kroków:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. Zapisz `dev.properties` plik, a następnie użyj następującego polecenia w celu przekazania go do **Storm** klastra:

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Zastąp **USERNAME** z nazwą użytkownika SSH dla klastra. Zastąp **nazwę BAZOWĄ** o nazwie podstawowej używane podczas tworzenia klastra.

## <a name="start-the-writer"></a>Uruchom moduł zapisujący

> [!IMPORTANT]
> Kroki opisane w tej sekcji założono, aby tworzenie klastrów Storm i Kafka używane łącze szablonu usługi Azure Resource Manager w tym dokumencie. Ten szablon umożliwia automatyczne tworzenie tematów Kafka klastra.
>
> Domyślnie Kafka w usłudze HDInsight nie zezwalaj na automatyczne tworzenie tematów, dlatego jeśli używasz innej metody tworzenia klastra Kafka, należy ręcznie utworzyć temat. Informacje dotyczące ręcznego tworzenia temat, zobacz [rozpoczynać Kafka w usłudze HDInsight](./kafka/apache-kafka-get-started.md) dokumentu.

1. Do nawiązania połączenia należy użyć następującego **Storm** klastra przy użyciu protokołu SSH. Zastąp **USERNAME** przy użyciu nazwy użytkownika SSH używany podczas tworzenia klastra. Zastąp **nazwę BAZOWĄ** o nazwie podstawowej używany podczas tworzenia klastra.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    Po wyświetleniu monitu wprowadź hasło, które zostały użyte podczas tworzenia klastrów.
   
    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Połączenie SSH z klastrem Storm Użyj następującego polecenia można uruchomić modułu zapisującego topologii:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Parametry używane w tym poleceniu są:

    * `org.apache.storm.flux.Flux`: Użyj strumienia do skonfigurowania i uruchomienia tej topologii.

    * `--remote`: Przesyłania topologii do Nimbus. Topologia jest dystrybuowana do węzłów procesu roboczego w klastrze.

    * `-R /writer.yaml`: Użyj `writer.yaml` pliku, aby skonfigurować topologię. `-R` Wskazuje, że ten zasób jest uwzględniona w pliku jar. Znajduje się w folderze głównym jar, więc `/writer.yaml` jest ścieżką do niego.

    * `--filter`: Wypełnić wpisów w `writer.yaml` topologię za pomocą wartości w `dev.properties` pliku. Na przykład wartość `kafka.topic` wpis w pliku jest używany do zastępowania `${kafka.topic}` wejścia w definicji topologii.

5. Po rozpoczęciu topologii, użyj następującego polecenia, aby sprawdzić, czy jest zapisywania danych do tematu Kafka:

    > [!IMPORTANT]
    > Zastąp `$KAFKAZKHOSTS` z dozorcy hosta informacji o __Kafka__ klastra.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    To polecenie używa skryptu dostarczone z Kafka do monitorowania tematu. Po chwili należy uruchomić, zwracanie losowe zdania, które zostały zapisane w temacie. Dane wyjściowe są podobne do poniższego przykładu:

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Użyj klawiszy Ctrl + c, aby zatrzymać skrypt.

## <a name="start-the-reader"></a>Uruchom czytnik

> [!NOTE]
> Podczas wyświetlania czytnika w Interfejsie użytkownika Storm, może zostać wyświetlony __elementach spout topologii lag błąd__ sekcji. Na przykład możesz zignorować ten błąd.

1. W sesji SSH z klastrem Storm Użyj następującego polecenia można uruchomić topologii czytnika:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. Po uruchomieniu topologii Otwórz interfejsu użytkownika platformy Storm. Interfejs użytkownika znajduje się w tej witrynie sieci web `https://storm-BASENAME.azurehdinsight.net/stormui`. Zastąp __nazwę BAZOWĄ__ o nazwie podstawowej używane podczas tworzenia klastra. 

    Po wyświetleniu monitu użyj nazwy logowania administratora (domyślnie `admin`) i hasło używane podczas tworzenia klastra. Widzisz stronę sieci web podobną do poniższej ilustracji:

    ![STORM interfejsu użytkownika](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Z poziomu interfejsu użytkownika Storm, wybierz __czytnika kafka__ łącze w __podsumowanie topologii__ sekcji, aby wyświetlić informacje o __kafka czytnika__ topologii.

    ![Sekcji podsumowania topologii Storm interfejsu użytkownika sieci web](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Aby wyświetlić informacje na temat wystąpień elementu bolt rejestratora, zaznacz __bolt rejestratora__ łącze w __elementów Bolt (czas wszystkich)__ sekcji.

    ![Łącze bolt rejestratora w sekcji elementów bolt](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. W __modułów__ wybierz łącze w __portu__ kolumny do wyświetlania rejestrowania informacji dotyczących tego wystąpienia składnika.

    ![Łącze modułów](./media/hdinsight-apache-storm-with-kafka/executors.png)

    Dziennik zawiera dziennik odczytanych z tematu Kafka danych. Informacje w dzienniku jest podobny do następującego tekstu:

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Zatrzymywanie topologii

W sesji SSH z klastrem Storm Użyj następujących poleceń, aby zatrzymać topologii Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ponieważ kroki opisane w tym dokumencie utworzyć obu klastrów w tej samej grupy zasobów platformy Azure, można usunąć grupy zasobów w portalu Azure. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów tworzone przez wykonanie tego dokumentu.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej przykładowe topologie, które mogą być używane z systemu Storm w usłudze HDInsight, zobacz [topologii Storm przykład i składniki](storm/apache-storm-example-topology.md).

Aby uzyskać informacje dotyczące wdrażania i monitorowania topologii na HDInsight opartych na systemie Linux, zobacz [wdrażanie i zarządzanie topologiami Apache Storm na HDInsight opartych na systemie Linux](storm/apache-storm-deploy-monitor-topology-linux.md)
