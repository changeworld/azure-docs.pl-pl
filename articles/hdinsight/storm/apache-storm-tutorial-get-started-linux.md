---
title: Przykłady z projektu Storm Starter dotyczące pracy z platformą Apache Storm w usłudze HDInsight — Azure | Microsoft Docs
description: Dowiedz się, jak przeprowadzać analizę danych big data i przetwarzać dane w czasie rzeczywistym przy użyciu platformy Apache Storm i przykładów z projektu Storm Starter w usłudze HDInsight.
keywords: projekt Storm Starter, przykład z platformy Apache Storm
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 0490ba63e651b0ef16175dca46b85a3e48bfd26f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-apache-storm-on-hdinsight-using-the-storm-starter-examples"></a>Wprowadzenie do platformy Apache Storm w usłudze HDInsight przy użyciu przykładów z projektu Storm Starter

Dowiedz się, jak używać platformy Apache Storm w usłudze HDInsight przy użyciu przykładów z projektu Storm Starter.

Apache Storm to skalowalny, odporny na błędy, rozproszony system obliczeniowy działający w czasie rzeczywistym do przetwarzania strumieni danych. Dzięki platformie Storm w usłudze Azure HDInsight można utworzyć oparty na chmurze klaster Storm do wykonywania analizy danych big data w czasie rzeczywistym.

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Znajomość protokołów SSH i SCP**. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-storm-cluster"></a>Tworzenie klastra Storm

Aby utworzyć klaster platformy Storm w usłudze HDInsight, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **+ Utwórz zasób** i pozycję **Dane + analiza**, a następnie pozycję **HDInsight**.

    ![Tworzenie klastra usługi HDInsight](./media/apache-storm-tutorial-get-started-linux/create-hdinsight.png)

2. W sekcji **Podstawowe** wprowadź następujące informacje:

    * **Nazwa klastra**: nazwa klastra usługi HDInsight.
    * **Subskrypcja**: wybierz subskrypcję, której chcesz użyć.
    * **Nazwa użytkownika logowania klastra** i **Hasło logowania klastra**: dane logowania podczas uzyskiwania dostępu do klastra przy użyciu protokołu HTTPS. Te poświadczenia umożliwiają dostęp do takich usług jak interfejs użytkownika sieci Web Ambari lub interfejs API REST.
    * **Nazwa użytkownika SSH (Secure Shell)**: nazwa logowania używana podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu SSH. Domyślnie hasło jest takie samo jak hasło logowania klastra.
    * **Grupa zasobów**: grupa zasobów, w której ma zostać utworzony klaster.
    * **Lokalizacja**: region platformy Azure, w którym ma zostać utworzony klaster.

   ![Wybieranie subskrypcji](./media/apache-storm-tutorial-get-started-linux/hdinsight-basic-configuration.png)

3. Wybierz pozycję **Typ klastra**, a następnie ustaw następujące wartości w sekcji **Konfiguracja klastra**:

    * **Typ klastra**: Storm

    * **System operacyjny**: Linux

    * **Wersja**: Storm 1.1.0 (HDI 3.6)

   Zapisz ustawienia przy użyciu przycisku **Wybierz**.

    ![Wybieranie typu klastra](./media/apache-storm-tutorial-get-started-linux/set-hdinsight-cluster-type.png)

4. Po wybraniu typu klastra ustaw typ klastra przy użyciu przycisku __Wybierz__. Następnie zakończ konfigurację podstawową za pomocą przycisku __Dalej__.

5. W sekcji **Magazyn** wybierz lub utwórz konto magazynu. Na potrzeby procedury w tym dokumencie pozostaw wartości domyślne w pozostałych polach w tej sekcji. Zapisz konfigurację magazynu za pomocą przycisku __Dalej__.

    ![Konfigurowanie ustawień konta magazynu dla usługi HDInsight](./media/apache-storm-tutorial-get-started-linux/set-hdinsight-storage-account.png)

6. W sekcji **Podsumowanie** przejrzyj konfigurację klastra. Zmień niepoprawne ustawienia przy użyciu linków __Edytuj__. Wreszcie utwórz klaster przy użyciu przycisku __Utwórz__.

    ![Podsumowanie konfiguracji klastra](./media/apache-storm-tutorial-get-started-linux/hdinsight-configuration-summary.png)

    > [!NOTE]
    > Tworzenie klastra może potrwać do 20 minut.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Uruchamianie przykładu z projektu Storm Starter w usłudze HDInsight

1. Nawiąż połączenie z klastrem usługi HDInsight przy użyciu protokołu SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!TIP]
    > Twój klient SSH może informować, że nie można ustalić autentyczności hosta. Jeśli tak się stanie, wprowadź wartość `yes`, aby kontynuować.

    > [!NOTE]
    > Jeśli do zabezpieczenia konta użytkownika SSH użyto hasła, zostanie wyświetlony monit o jego wprowadzenie. Jeśli używasz klucza publicznego, może być konieczne użycie parametru `-i` w celu wskazania zgodnego klucza prywatnego. Na przykład `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj następującego polecenia, aby uruchomić przykładową topologię:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount

    To polecenie uruchamia w klastrze przykładową topologię WordCount. Ta topologia generuje losowe zdania i zlicza wystąpienia słów. Przyjazna nazwa tej topologii to `wordcount`.

    > [!NOTE]
    > Podczas przesyłania własnych topologii do klastra przed użyciem polecenia `storm` należy skopiować plik JAR zawierający klaster. Skopiuj plik za pomocą polecenia `scp`. Na przykład: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > Przykład WordCount i inne przykłady z projektu Storm Starter znajdują się już w klastrze w lokalizacji `/usr/hdp/current/storm-client/contrib/storm-starter/`.

Jeśli interesuje Cię wyświetlenie źródła przykładów z projektu Storm Starter, możesz znaleźć kod w lokalizacji [https://github.com/apache/storm/tree/1.1.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.1.x-branch/examples/storm-starter). Ten link dotyczy platformy Storm 1.1.x, która jest dostarczana z usługą HDInsight 3.6. W przypadku innych wersji platformy Storm należy za pomocą przycisku __Branch__ (Gałąź) w górnej części strony wybrać inną wersję platformy Storm.

## <a name="monitor-the-topology"></a>Monitorowanie topologii

Interfejs użytkownika platformy Storm udostępnia interfejs sieci Web do pracy z uruchomionymi topologiami i jest zawarty w klastrze usługi HDInsight.

Wykonaj następujące kroki, aby monitorować topologię za pomocą interfejsu użytkownika platformy Storm:

1. Aby wyświetlić interfejs użytkownika platformy Storm, otwórz w przeglądarce internetowej adres `https://CLUSTERNAME.azurehdinsight.net/stormui`. Zastąp ciąg **CLUSTERNAME** nazwą klastra.

    > [!NOTE]
    > Jeśli zostanie wyświetlony monit o podanie nazwy użytkownika i hasła, wprowadź nazwę administratora klastra (admin) i hasło użyte podczas tworzenia klastra.

2. W obszarze **Topology summary** (Podsumowanie topologii) wybierz pozycję **wordcount** w kolumnie **Name** (Nazwa). Zostaną wyświetlone informacje o topologii.

    ![Pulpit nawigacyjny platformy Storm z informacjami o topologii przykładu z projektu Storm Starter o nazwie WordCount.](./media/apache-storm-tutorial-get-started-linux/topology-summary.png)

    Ta strona przedstawia następujące informacje:

    * **Topology stats** (Statystyka topologii) — podstawowe informacje na temat wydajności topologii podzielone na okna czasowe.

        > [!NOTE]
        > Wybór określonego okna czasowego zmienia przedział czasu dla informacji wyświetlanych w innych sekcjach strony.

    * **Spouts** (Elementy spout) — podstawowe informacje o elementach spout, łącznie z ostatnim błędem zwróconym przez poszczególne elementy spout.

    * **Bolts** (Elementy bolt) — podstawowe informacje o elementach bolt.

    * **Topology configuration** (Konfiguracja topologii) — szczegółowe informacje o konfiguracji topologii.

    Ta strona zawiera również akcje, które można wykonać w odniesieniu do topologii:

    * **Activate** (Aktywuj) — wznowienie przetwarzania dezaktywowanej topologii.

    * **Deactivate** (Dezaktywuj) — wstrzymanie uruchomionej topologii.

    * **Rebalance** (Ponowne równoważenie) — dopasowanie równoległości topologii. Po zmianie liczby węzłów w klastrze należy przeprowadzić ponowne równoważenie uruchomionych topologii. Pozwoli to dostosować równoległość topologii w celu kompensacji zwiększonej/zmniejszonej liczby węzłów w klastrze. Aby uzyskać więcej informacji, zobacz temat [Pojęcie równoległości w topologii Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    * **Kill** (Kasuj) — zakończenie działania topologii Storm po określonym czasie.

3. Na tej stronie wybierz pozycję z sekcji **Spouts** (Elementy spout) lub **Bolts** (Elementy bolt). Zostaną wyświetlone informacje o wybranym składniku.

    ![Pulpit nawigacyjny Storm z informacjami o wybranych składnikach.](./media/apache-storm-tutorial-get-started-linux/component-summary.png)

    Ta strona zawiera następujące informacje:

    * **Spout/Bolt stats** (Statystyki elementów spout/bolt) — podstawowe informacje na temat wydajności składników podzielone na okna czasowe.

        > [!NOTE]
        > Wybór określonego okna czasowego zmienia przedział czasu dla informacji wyświetlanych w innych sekcjach strony.

    * **Input stats** (Statystyki danych wejściowych) tylko dla elementów bolt — informacje na temat składników, które tworzą dane używane przez elementy bolt.

    * **Output stats** (Statystyki danych wyjściowych) — informacje na temat danych emitowanych przez dany element bolt.

    * **Executors** (Wykonawcy) — informacje dotyczące wystąpień danego składnika.

    * **Errors** (Błędy) — błędy generowane przez dany składnik.

4. Wyświetlając szczegółowe informacje o elemencie spout lub bolt, wybierz pozycję w kolumnie **Port** w sekcji **Executors** (Wykonawcy), aby wyświetlić szczegóły dotyczące określonego wystąpienia składnika.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    W tym przykładzie wyraz **seven** wystąpił 1493957 razy. Tyle razy został on napotkany od momentu uruchomienia tej topologii.

## <a name="stop-the-topology"></a>Zatrzymywanie topologii

Wróć do strony **Topology summary** (Podsumowanie topologii) dla topologii WordCount, a następnie naciśnij przycisk **Kill** (Kasuj) w obszarze **Topology actions** (Akcje topologii). Po wyświetleniu monitu wprowadź 10 jako liczbę sekund oczekiwania przed zatrzymaniem topologii. Po upłynięciu limitu czasu topologia nie będzie już wyświetlana w obszarze **interfejsu użytkownika platformy Storm** pulpitu nawigacyjnego.

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

W razie problemów podczas tworzenia klastra usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a id="next"></a>Następne kroki

W tym samouczku dotyczącym platformy Apache Storm przedstawiono podstawy pracy z platformą Storm w usłudze HDInsight. W dalszej kolejności zapoznaj się ze sposobem [opracowywania topologii opartych na języku Java za pomocą programu Maven](apache-storm-develop-java-topology.md).

Jeśli umiesz już opracowywać topologie oparte na języku Java, zobacz dokument [Wdrażanie topologii Apache Storm w usłudze HDInsight i zarządzanie nimi](apache-storm-deploy-monitor-topology-linux.md).

Jeśli jesteś deweloperem platformy .NET, możesz tworzyć topologie C# lub hybrydowe topologie C#/Java za pomocą programu Visual Studio. Aby uzyskać więcej informacji na ten temat, zobacz [Develop C# topologies for Apache Storm on HDInsight using Hadoop tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) (Tworzenie topologii C# dla Apache Storm w usłudze HDInsight przy użyciu narzędzi Hadoop dla programu Visual Studio).

Aby uzyskać przykładowe topologie, które mogą być używane z narzędziem Storm w usłudze HDInsight, zobacz następujące przykłady:

* [Przykładowe topologie dla systemu Storm w usłudze HDInsight](apache-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[preview-portal]: https://portal.azure.com/
