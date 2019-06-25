---
title: 'Szybki start: Tworzenie i monitorowanie topologii Apache Storm w usłudze Azure HDInsight'
description: W przewodniku Szybki Start Dowiedz się, jak utworzyć i monitorować topologii Apache Storm w usłudze Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 49da6ad20b29193f0430a66658f1bf80625704b3
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274124"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Szybki start: Tworzenie i monitorowanie topologii Apache Storm w usłudze Azure HDInsight

Apache Storm to skalowalny, odporny na błędy, rozproszony system obliczeniowy działający w czasie rzeczywistym do przetwarzania strumieni danych. Dzięki platformie Storm w usłudze Azure HDInsight można utworzyć oparty na chmurze klaster Storm do wykonywania analizy danych big data w czasie rzeczywistym.

W tym przewodniku Szybki Start użyjesz przykładu z Apache [projektu storm starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projekt do tworzenia i monitorowania topologii Apache Storm do istniejącego klastra Apache Storm.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Storm na HDInsight. Zobacz [tworzenie technologii Apache Hadoop clusters, przy użyciu witryny Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz **Storm** dla **typ klastra**.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Tworzenie topologii

1. Połącz z klastrem Storm. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` o nazwie Storm klastra, a następnie wpisz polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. **WordCount** przykład znajduje się w klastrze usługi HDInsight w `/usr/hdp/current/storm-client/contrib/storm-starter/`. Topologia generuje losowe zdania i zlicza wystąpienia słów. Użyj następującego polecenia, aby rozpocząć **wordcount** topologii w klastrze:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Monitorowanie topologii

System STORM udostępnia interfejs sieci web na potrzeby pracy z uruchomionymi topologiami i znajduje się w klastrze usługi HDInsight.

Wykonaj następujące kroki, aby monitorować topologię za pomocą interfejsu użytkownika platformy Storm:

1. Aby wyświetlić interfejs użytkownika platformy Storm, otwórz w przeglądarce internetowej adres `https://CLUSTERNAME.azurehdinsight.net/stormui`. Zastąp ciąg `CLUSTERNAME` nazwą klastra.

2. W obszarze **podsumowanie topologii**, wybierz opcję **wordcount** wpis **nazwa** kolumny. Zostaną wyświetlone informacje o topologii.

    ![Pulpit nawigacyjny platformy Storm z informacjami o topologii przykładu z projektu Storm Starter o nazwie WordCount.](./media/apache-storm-quickstart/topology-summary.png)

    Nowa strona zawiera następujące informacje:

    |Właściwość | Opis |
    |---|---|
    |Topology stats|Podstawowe informacje na temat wydajności topologii podzielone na okna czasowe. Wybór określonego okna czasowego zmienia przedział czasu dla informacji wyświetlanych w innych sekcjach strony.|
    |Elementy spout|Podstawowe informacje o elementach spout, łącznie z ostatnim błędem zwróconym przez poszczególne elementy spout.|
    |Elementy bolt|Podstawowe informacje o elementach bolt.|
    |Konfiguracja topologii|Szczegółowe informacje o konfiguracji topologii.|
    |Activate|Wznowienie przetwarzania dezaktywowanej topologii.|
    |Dezaktywuj|Wstrzymanie uruchomionej topologii.|
    |Ponowne zrównoważenie|To dostosować równoległość topologii. Po zmianie liczby węzłów w klastrze należy przeprowadzić ponowne równoważenie uruchomionych topologii. Pozwoli to dostosować równoległość topologii w celu kompensacji zwiększonej/zmniejszonej liczby węzłów w klastrze. Aby uzyskać więcej informacji, zobacz [pojęcie równoległości w topologii Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Zakończ|Kończy topologii Storm po określonym czasie.|

3. Na tej stronie wybierz pozycję z sekcji **Spouts** (Elementy spout) lub **Bolts** (Elementy bolt). Zostaną wyświetlone informacje o wybranym składniku.

    ![Pulpit nawigacyjny Storm z informacjami o wybranych składnikach.](./media/apache-storm-quickstart/component-summary.png)

    Nowa strona zawiera następujące informacje:

    |Właściwość | Opis |
    |---|---|
    |Spout/Bolt stats|Podstawowe informacje na temat wydajności składników podzielone na okna czasowe. Wybór określonego okna czasowego zmienia przedział czasu dla informacji wyświetlanych w innych sekcjach strony.|
    |Statystyki danych wejściowych (tylko dla elementu bolt)|Informacje na temat składników, które tworzą dane używane przez element bolt.|
    |Statystyki danych wyjściowych|Informacje na temat danych emitowanych przez dany element bolt.|
    |Executors|Informacje na temat wystąpień danego składnika.|
    |Błędy|Błędy generowane przez dany składnik.|

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

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego przewodnika Szybki Start możesz usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usunąć klaster usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start użyto przykładu z Apache [projektu storm starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projekt do tworzenia i monitorowania topologii Apache Storm do istniejącego klastra Apache Storm. Przejdź do następnego artykułu, aby nauczyć się podstaw zarządzania i monitorowania topologii Apache Storm.

> [!div class="nextstepaction"]
>[Wdrażanie topologii Apache Storm w usłudze Azure HDInsight i zarządzanie ](./apache-storm-deploy-monitor-topology-linux.md)