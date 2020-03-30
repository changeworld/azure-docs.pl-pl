---
title: 'Szybki start: tworzenie topologii usługi Apache Storm — usługa Azure HDInsight'
description: W przewodniku Szybki start dowiedz się, jak utworzyć i monitorować topologię usługi Apache Storm w usłudze Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 24173b553f30f652caf20b1ec7500fd9c4d2f7a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73241226"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Szybki start: tworzenie i monitorowanie topologii usługi Apache Storm w usłudze Azure HDInsight

Apache Storm to skalowalny, odporny na błędy, rozproszony system obliczeniowy działający w czasie rzeczywistym do przetwarzania strumieni danych. Dzięki platformie Storm w usłudze Azure HDInsight można utworzyć oparty na chmurze klaster Storm do wykonywania analizy danych big data w czasie rzeczywistym.

W tym przewodniku Szybki start można użyć przykładu z projektu [uruchamiania burzy](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) Apache do tworzenia i monitorowania topologii burzy Apache do istniejącego klastra Burza Apache.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Burzy Apache w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz opcję **Burza** dla **typu klastra**.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Tworzenie topologii

1. Połącz się z klastrem Burzowym. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra Burzowego, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Przykład **WordCount** znajduje się w klastrze `/usr/hdp/current/storm-client/contrib/storm-starter/`HDInsight w programie . Topologia generuje losowe zdania i zlicza, ile razy występują słowa. Użyj następującego polecenia, aby uruchomić topologię **wordcount** w klastrze:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Monitorowanie topologii

Storm udostępnia interfejs internetowy do pracy z topologią i jest dołączony do klastra HDInsight.

Wykonaj następujące kroki, aby monitorować topologię za pomocą interfejsu użytkownika platformy Storm:

1. Aby wyświetlić interfejs użytkownika platformy Storm, otwórz w przeglądarce internetowej adres `https://CLUSTERNAME.azurehdinsight.net/stormui`. Zastąp ciąg `CLUSTERNAME` nazwą klastra.

2. W obszarze **Podsumowanie topologii**wybierz wpis **liczby wyrazów** w kolumnie **Nazwa.** Zostaną wyświetlone informacje o topologii.

    ![Pulpit nawigacyjny platformy Storm z informacjami o topologii przykładu z projektu Storm Starter o nazwie WordCount.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    Nowa strona zawiera następujące informacje:

    |Właściwość | Opis |
    |---|---|
    |Statystyki topologii|Podstawowe informacje na temat wydajności topologii, zorganizowane w okna czasowe. Wybór określonego okna czasowego zmienia przedział czasu dla informacji wyświetlanych w innych sekcjach strony.|
    |Ustniki|Podstawowe informacje o wylewkach, w tym ostatni błąd zwracany przez każdego dziobka.|
    |Śruby|Podstawowe informacje o śrubach.|
    |Konfiguracja topologii|Szczegółowe informacje o konfiguracji topologii.|
    |Activate|Wznawia przetwarzanie dezaktywacji topologii.|
    |Dezaktywuj|Wstrzymuje topologię biegu.|
    |Równowaga|Dostosowuje równoległość topologii. Po zmianie liczby węzłów w klastrze należy przeprowadzić ponowne równoważenie uruchomionych topologii. Pozwoli to dostosować równoległość topologii w celu kompensacji zwiększonej/zmniejszonej liczby węzłów w klastrze. Aby uzyskać więcej informacji, zobacz [Opis równoległości topologii burzy Apache](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Zabić|Kończy topologię burzy po upływie określonego limitu czasu.|

3. Na tej stronie wybierz pozycję z sekcji **Spouts** (Elementy spout) lub **Bolts** (Elementy bolt). Zostaną wyświetlone informacje o wybranym składniku.

    ![Pulpit nawigacyjny Storm z informacjami o wybranych składnikach.](./media/apache-storm-quickstart/hdi-component-summary.png)

    Na nowej stronie są wyświetlane następujące informacje:

    |Właściwość | Opis |
    |---|---|
    |Statystyki wylewki/śruby|Podstawowe informacje o wydajności składnika, zorganizowane w okna czasowe. Wybór określonego okna czasowego zmienia przedział czasu dla informacji wyświetlanych w innych sekcjach strony.|
    |Statystyki wejściowe (tylko śruba)|Informacje o komponentach, które generują dane zużywane przez śrubę.|
    |Statystyki wyjściowe|Informacje o danych emitowanych przez tę śrubę.|
    |Executors|Informacje o wystąpieniach tego składnika.|
    |Errors|Błędy wywoływane przez ten składnik.|

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

Po zakończeniu szybkiego startu można usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [Usuwanie klastra HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto przykładu z projektu [uruchamiania burzy](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) Apache do tworzenia i monitorowania topologii burzy Apache do istniejącego klastra Burzy Apache. Przejdź do następnego artykułu, aby poznać podstawy zarządzania i monitorowania topologii Apache Storm.

> [!div class="nextstepaction"]
>[Wdrażanie topologii usługi Apache Storm i zarządzanie nimi w usłudze Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)