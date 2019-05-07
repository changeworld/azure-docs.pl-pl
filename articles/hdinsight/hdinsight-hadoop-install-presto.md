---
title: Instalowanie Presto, w klastrach usługi Azure HDInsight w systemie Linux
description: Dowiedz się, jak zainstalować Presto oraz Airpal w klastrach opartych na systemie Linux usługi HDInsight Hadoop za pomocą akcji skryptu.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 7ce2cf40dafc09d86d0c424a3954a334ae83143a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145477"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Instalowanie i używanie Presto w klastrach HDInsight opartych na usłudze Hadoop

> [!IMPORTANT]  
> PRZECZYTAJ: Poniższe instrukcje są one przestarzałe. Zapoznaj się ze wskazówkami najnowsze [danych Gwiazda](http://docs.starburstdata.com/latest/azure.html) do zainstalowania i uruchomienia Presto w usłudze Azure HDInsight.  

W tym artykule opisano sposób Zainstaluj skrypt Presto w klastrach HDInsight opartych na usłudze Hadoop platformy Azure przy użyciu akcji skryptu. Poznasz również sposób instalowania Airpal w istniejącym klastrze HDInsight Presto.

HDInsight oferuje również Presto Gwiazda z aplikacji w przypadku klastrów Apache Hadoop. Aby uzyskać więcej informacji, zobacz [instalowanie aplikacji Apache Hadoop innych firm w usłudze Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications).

> [!IMPORTANT]  
> Kroki opisane w tym artykule wymaga klastra usługi HDInsight 3.5 Hadoop, który używa systemu Linux. Linux jest jedynym systemem operacyjnym na HDInsight w wersji 3.4 lub nowszej. Aby uzyskać więcej informacji, zobacz [wersji HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Co to jest Presto?
[Presto](https://prestosql.io) jest dystrybuowane fast aparatu zapytań SQL dla danych big data. Presto jest odpowiednia dla interaktywne zapytania petabajtów danych. Aby uzyskać więcej informacji na temat składników środowiska Presto i jak one współdziałają ze sobą, zobacz [Presto pojęcia](https://prestosql.io/docs/current/overview/concepts.html).

> [!WARNING]  
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane. Microsoft Support pomoże wyizolować i rozwiązać problemy związane z tych składników.
> 
> Składniki niestandardowe, takie jak Presto otrzymują uzasadnioną komercyjnie pomoc techniczną, aby pomóc Ci rozwiązać problem. Ta funkcja może rozwiązać ten problem. Lub może być konieczne zaangażowania, kanałów dostępnych dla technologii open source, w którym znajduje się specjalistyczna dla tej technologii. Istnieje wiele witryn społeczności, których można użyć. Należą do nich [forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) i [Stack Overflow](https://stackoverflow.com). 
>
> Projektów Apache również mają projektu na witryn [witryny sieci Web Apache](https://apache.org). Na przykład [Hadoop](https://hadoop.apache.org/).


## <a name="install-presto-by-using-script-actions"></a>Zainstaluj skrypt Presto przy użyciu akcji skryptu

W tej sekcji wyjaśniono, jak użyć przykładowego skryptu podczas tworzenia nowego klastra przy użyciu witryny Azure portal: 

1. Rozpocznij aprowizowanie klastra, wykonując kroki opisane [Tworzenie klastrów z systemem Linux w HDInsight przy użyciu witryny Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md). Upewnij się, Utwórz klaster przy użyciu **niestandardowe** klastra tworzenia przepływu. Klaster musi spełniać następujące wymagania:

   * Musi to być klastra Hadoop z HDInsight w wersji 3.6.

   * Musi ona używać usługi Azure Storage jako magazynu danych. Za pomocą Presto w klastrze, który używa usługi Azure Data Lake Storage jako opcji magazynu nie jest jeszcze opcję.

     ![HDInsight, niestandardowe (rozmiar, ustawienia, aplikacje)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. W **Zaawansowane ustawienia** wybierz opcję **akcji skryptu**. Podaj następujące informacje. Można również wybrać **zainstalować Presto** opcję Typ skryptu:
   
   * **NAZWA**. Wprowadź przyjazną nazwę dla akcji skryptu.
   * **Identyfikator URI skryptu powłoki systemowej**. `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`.
   * **HEAD**. Wybierz tę opcję.
   * **PROCES ROBOCZY**. Wybierz tę opcję.
   * **ZOOKEEPER**. Pozostaw to pole wyboru puste.
   * **PARAMETRY**. Pozostaw to pole puste.


3. W dolnej części **akcji skryptu** obszaru, wybierz **wybierz** przycisk, aby zapisać konfigurację. Na koniec wybierz **wybierz** znajdujący się u dołu **Zaawansowane ustawienia** obszar, aby zapisać informacje o konfiguracji.

4. Aprowizowanie klastra zgodnie z opisem w w dalszym ciągu [Tworzenie klastrów z systemem Linux w HDInsight przy użyciu witryny Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > Program Azure PowerShell, wiersza polecenia platformy Azure Classic, zestawu .NET SDK HDInsight lub szablonów usługi Azure Resource Manager można również zastosować akcji skryptu. Można również zastosować akcji skryptu na już działające klastry. Aby uzyskać więcej informacji, zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Używanie środowiska Presto z HDInsight

Aby pracować Presto w klastrze usługi HDInsight, wykonaj następujące czynności:

1. Połącz się z klastrem HDInsight przy użyciu protokołu SSH:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Uruchom powłokę Presto, uruchamiając następujące polecenie:
   
    `presto --schema default`

3. Uruchom kwerendę na przykładową tabelę **hivesampletable**, który jest dostępny we wszystkich klastrach HDInsight domyślnie:
   
    `select count (*) from hivesampletable;`
   
    Domyślnie [Apache Hive](https://prestosql.io/docs/current/connector/hive.html) i [TPCH](https://prestosql.io/docs/current/connector/tpch.html) łączników na potrzeby Presto zostały już skonfigurowane. Łącznik programu Hive jest skonfigurowany do używania domyślnej instalacji programu Hive. Dlatego wszystkie tabele programu Hive są automatycznie widoczne w Presto.

    Aby uzyskać więcej informacji, zobacz [Presto dokumentacji](https://prestosql.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Za pomocą usług Airpal Presto

[Airpal](https://github.com/airbnb/airpal#airpal) jest interfejsem zapytania oparte na sieci web typu open source dla środowiska Presto. Aby uzyskać więcej informacji na temat Airpal, zobacz [dokumentacji Airpal](https://github.com/airbnb/airpal#airpal).

Wykonaj poniższe kroki, aby zainstalować Airpal w węźle brzegowym:

1. Przy użyciu protokołu SSH, połączenie z węzłem głównym klastra HDInsight, który zainstalował Presto:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po nawiązaniu, uruchom następujące polecenie:

    `sudo slider registry  --name presto1 --getexp presto` 
   
    Zostaną wyświetlone dane wyjściowe podobne do następujących JSON:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Z danych wyjściowych, zanotuj wartość dla **wartość** właściwości. Ta wartość jest potrzebna, podczas instalacji Airpal na węźle krawędzi klastra. W powyższym danych wyjściowych jest wartość, która należy **10.0.0.12:9090**.

4. Użyj [ten szablon](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json) utworzyć węzeł krawędzi klastra HDInsight. Podaj wartości pokazano na poniższym zrzucie ekranu.

    ![Wdrożenie niestandardowe](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Wybierz pozycję **Kup**.

6. Gdy zmiany zostaną zastosowane do konfiguracji klastra, uzyskać dostęp do Airpal interfejs sieci web, wykonując następujące kroki [witryny Azure portal](https://portal.azure.com):

    1. Z menu po lewej stronie wybierz **wszystkich usług**.

    1. W obszarze **ANALYTICS**, wybierz opcję **klastry HDInsight**.

    1. Wybierz klaster, z listy, co spowoduje otwarcie widoku domyślnego.

    1. W widoku domyślnego w obszarze **ustawienia**, wybierz opcję **aplikacji**.

        ![HDInsight, aplikacje](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. Z **zainstalowane aplikacje** strony, zlokalizować wpis tabeli dla **airpal**. Wybierz **Portal**.

        ![Zainstalowane aplikacje](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. Po wyświetleniu monitu wprowadź poświadczenia administratora określone podczas tworzenia klastra HDInsight opartych na usłudze Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Dostosowywanie Presto instalacji w klastrze HDInsight

Aby dostosować instalację, wykonaj następujące czynności:

1. Przy użyciu protokołu SSH, połączenie z węzłem głównym klastra HDInsight, który zainstalował Presto:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Wprowadź zmiany w konfiguracji w pliku `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Aby uzyskać więcej informacji na temat konfiguracji Presto, zobacz [opcje konfiguracji Presto klastrów usługi YARN](https://prestosql.github.io/presto-yarn/installation-yarn-configuration-options.html).

3. Zatrzymaj i Zakończ bieżącego działającego wystąpienia Presto:

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. Uruchom nowe wystąpienie klasy Presto dostosowując:

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. Poczekaj, aż nowe wystąpienie będzie gotowa. Zanotuj adres Presto koordynator:

    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generowanie wyników testów dla klastrów HDInsight, które Uruchom Presto

TPC-DS to branżowy standard do pomiaru wydajności wiele systemów wspomaganie decyzji, w tym systemy danych big data. Możesz użyć Presto do generowania danych i oceny w porównaniu z użyciem własnych danych porównawczych HDInsight. Aby uzyskać więcej informacji, zobacz [tpcds hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="next-steps"></a>Kolejne kroki
* [Instalowanie i korzystanie z rozwiązania Hue w klastrach usługi HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). HUE to internetowy interfejs użytkownika, który ułatwia tworzenie, uruchamianie i zapisywanie Apache Pig i Hive zadania.

* [Zainstaluj Apache Giraph w klastrach usługi HDInsight Hadoop, a przetwarzanie dużych wykresów za pomocą systemu Giraph](hdinsight-hadoop-giraph-install-linux.md). Użyj dostosowywania klastra, aby zainstalować system Giraph w klastrach HDInsight opartych na usłudze Hadoop. Za pomocą systemu Giraph można wykonać przetwarzanie wykresów za pomocą usługi Hadoop. Może również służyć za pomocą usługi Azure HDInsight.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
