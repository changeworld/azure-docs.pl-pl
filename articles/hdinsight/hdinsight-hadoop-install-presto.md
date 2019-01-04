---
title: Instalowanie Presto, w klastrach usługi Azure HDInsight w systemie Linux
description: Dowiedz się, jak zainstalować Presto oraz Airpal w klastrach opartych na systemie Linux usługi HDInsight Hadoop za pomocą akcji skryptu.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 785223f7da1f59288f4fca6e7a3955a6b3af41c0
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975002"
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Instalowanie i używanie Presto w klastrach usługi HDInsight Hadoop

W tym dokumencie nauczysz się Zainstaluj skrypt Presto w klastrach usługi HDInsight Hadoop za pomocą akcji skryptu. Poznasz również sposób instalowania Airpal w istniejącym klastrze HDInsight Presto.

HDInsight oferuje również Presto Gwiazda z aplikacji w przypadku klastrów Apache Hadoop. Aby uzyskać więcej informacji, zobacz [instalowanie aplikacji innych firm w usłudze Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications)

> [!IMPORTANT]  
> Kroki opisane w tym dokumencie wymagają **klastra platformy Hadoop w HDInsight 3.5** , użyto systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [wersji HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Co to jest Presto?
[Presto](https://prestodb.io/overview.html) jest szybkie rozproszonych aparatu zapytań SQL dla danych big data. Presto jest odpowiednia dla interaktywne zapytania petabajtów danych. Aby uzyskać więcej informacji na temat składników środowiska Presto i jak one współdziałają ze sobą, zobacz [Presto pojęcia](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]  
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane i Microsoft Support pomoże wyizolować i rozwiązać problemy związane z tych składników.
> 
> Składniki niestandardowe, takie jak Presto, otrzymują uzasadnioną komercyjnie pomoc techniczną, aby pomóc rozwiązać ten problem. Może to spowodować rozwiązuje problem lub pytaniem, dzięki którym można zaangażować dostępne kanały dla technologii "open source", gdzie znajduje się specjalistyczna dla tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [Forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Projektów Apache mieć witryny projektu na [ https://apache.org ](https://apache.org), na przykład: [Hadoop](https://hadoop.apache.org/).


## <a name="install-presto-using-script-action"></a>Zainstaluj skrypt Presto za pomocą akcji skryptu

Ta sekcja zawiera instrukcje dotyczące sposobu używania przykładowy skrypt, podczas tworzenia nowego klastra za pomocą witryny Azure portal. 

1. Uruchom aprowizację klastra wykonując kroki opisane w [opartych na systemie Linux Provision HDInsight clusters](hdinsight-hadoop-create-linux-clusters-portal.md). Upewnij się, możesz utworzyć przy użyciu klastra **niestandardowe** klastra tworzenia przepływu. Klaster musi spełniać następujące wymagania.

    * Musi to być klastra Hadoop z HDInsight w wersji 3.6.

    * Musi ona używać usługi Azure Storage jako magazynu danych. Za pomocą Presto w klastrze, który używa usługi Azure Data Lake Storage jako opcji magazynu nie jest jeszcze opcję.

    ![Tworzenie klastra HDInsight przy użyciu opcji niestandardowych](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Na **Zaawansowane ustawienia** wybierz opcję **akcji skryptu**i podaj poniższe informacje. Można również wybrać opcję "Zainstaluj Presto", typ skryptu.
   
   * **NAZWA**: Wprowadź przyjazną nazwę dla akcji skryptu.
   * **Identyfikator URI skryptu powłoki systemowej**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HEAD**: Zaznacz tę opcję.
   * **PROCES ROBOCZY**: Zaznacz tę opcję.
   * **DOZORCY**: Pozostaw to pole wyboru puste.
   * **PARAMETRY**: Pozostaw to pole puste.


3. W dolnej części **akcji skryptu** obszaru, kliknij przycisk **wybierz** przycisk, aby zapisać konfigurację. Na koniec kliknij **wybierz** znajdujący się u dołu **Zaawansowane ustawienia** obszar, aby zapisać informacje o konfiguracji.

4. Kontynuuj, inicjowania obsługi klastra zgodnie z opisem w [opartych na systemie Linux Provision HDInsight clusters](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > Program Azure PowerShell, wiersza polecenia platformy Azure Classic, zestawu .NET SDK HDInsight lub szablonów usługi Azure Resource Manager można również zastosować akcji skryptu. Można również zastosować akcji skryptu na już działające klastry. Aby uzyskać więcej informacji, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Używanie środowiska Presto z HDInsight

Aby pracować Presto w klastrze usługi HDInsight, należy użyć następujące czynności:

1. Nawiąż połączenie z klastrem usługi HDInsight przy użyciu protokołu SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Uruchom powłokę Presto, używając następującego polecenia.
   
        presto --schema default

3. Uruchom kwerendę na przykładową tabelę **hivesampletable**, który jest dostępny we wszystkich klastrach HDInsight domyślnie.
   
        select count (*) from hivesampletable;
   
    Domyślnie [Apache Hive](https://prestodb.io/docs/current/connector/hive.html) i [TPCH](https://prestodb.io/docs/current/connector/tpch.html) łączników na potrzeby Presto zostały już skonfigurowane. Łącznik programu hive jest skonfigurowany do używania domyślnie zainstalowaną instalacji programu Hive, więc wszystkie tabele z gałęzi będą automatycznie widoczne w Presto.

    Aby uzyskać więcej informacji, zobacz [Presto dokumentacji](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Za pomocą usług Airpal Presto

[Airpal](https://github.com/airbnb/airpal#airpal) jest interfejsem zapytania oparte na sieci web typu open source dla środowiska Presto. Aby uzyskać więcej informacji na temat Airpal, zobacz [dokumentacji Airpal](https://github.com/airbnb/airpal#airpal).

Aby zainstalować Airpal na węźle krawędzi, wykonaj następujące kroki:

1. Przy użyciu protokołu SSH, połączenie z węzłem głównym klastra HDInsight, który zainstalował Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po nawiązaniu połączenia, uruchom następujące polecenie.

        sudo slider registry  --name presto1 --getexp presto 
   
    Zostaną wyświetlone dane wyjściowe podobne do następujących JSON:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Z danych wyjściowych, zanotuj wartość dla **wartość** właściwości. Ta wartość będzie potrzebna podczas instalowania Airpal na edgenode klastra. W powyższych danych wyjściowych, wartości, który będzie potrzebny jest **10.0.0.12:9090**.

4. Użyj szablonu **[tutaj](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** utworzyć edgenode klastra HDInsight i podaj wartości, jak pokazano na poniższym zrzucie ekranu.

    ![HDInsight instalacji Airpal klastrze Presto](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Kliknij pozycję **Kup**.

6. Gdy zmiany zostaną zastosowane do konfiguracji klastra, możesz uzyskać dostęp Airpal interfejs sieci web za pomocą następujących kroków z [witryny Azure portal](https://portal.azure.com):

    1. Z menu po lewej stronie wybierz **wszystkich usług**.

    1. W obszarze **ANALYTICS**, wybierz opcję **klastry HDInsight**.

    1. Wybierz klaster, z listy, co spowoduje otwarcie widoku domyślnego.

    1. W widoku domyślnego w obszarze **ustawienia**, wybierz opcję **aplikacji**.

        ![HDInsight uruchamiania Airpal klastrze Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. Z **zainstalowane aplikacje** strony lokalnych wpis tabeli, aby uzyskać **airpal**i wybierz **Portal**.

        ![HDInsight uruchamiania Airpal klastrze Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. Po wyświetleniu monitu wprowadź poświadczenia administratora, które określono podczas tworzenia klastra usługi HDInsight Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Dostosowywanie Presto instalacji w klastrze HDInsight

Do dostosowania instalacji, wykonaj następujące kroki:

1. Przy użyciu protokołu SSH, połączenie z węzłem głównym klastra HDInsight, który zainstalował Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Wprowadź zmiany w konfiguracji w pliku `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Aby uzyskać więcej informacji na temat konfiguracji Presto, zobacz [Presto konfigurację dla klastrów usługi YARN](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Zatrzymaj i kill bieżącego działającego wystąpienia Presto.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Uruchom nowe wystąpienie klasy Presto dostosowując.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Poczekaj, aż nowe wystąpienie będzie gotowa i zanotuj adres presto koordynatora.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generowanie wyników testów dla klastrów HDInsight, które Uruchom Presto

TPC-DS to branżowy standard do pomiaru wydajności wiele systemów obsługi decyzję, w tym systemy danych big data. Możesz użyć Presto do generowania danych i oceny w porównaniu z użyciem własnych danych porównawczych HDInsight. Aby uzyskać więcej informacji, zobacz [tutaj](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Zobacz także
* [Instalowanie i korzystanie z rozwiązania Hue w klastrach HDInsight](hdinsight-hadoop-hue-linux.md). HUE to internetowy interfejs użytkownika, który ułatwia tworzenie, uruchamianie i zapisywanie Apache Pig i Hive zadania.

* [Instalowanie systemu Apache Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install-linux.md). Użyj dostosowywania klastra, aby zainstalować system Giraph w klastrach usługi HDInsight Hadoop. System Giraph umożliwia przetwarzanie za pomocą usługi Hadoop wykresów i mogą być używane z usługi Azure HDInsight.

* [Instalowanie Apache Solr w klastrach HDInsight](hdinsight-hadoop-solr-install-linux.md). Użyj dostosowywania klastra, aby zainstalować platformę Solr w klastrach usługi HDInsight Hadoop. Solr umożliwia wykonywanie operacji wyszukiwania zaawansowanego w przechowywanych danych.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
