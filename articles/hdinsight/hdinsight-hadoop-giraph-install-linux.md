---
title: Instalowanie i używanie Giraph w usłudze Azure HDInsight
description: Dowiedz się, jak zainstalować Giraph w klastrach usługi HDInsight przy użyciu akcji skryptu. Możesz użyć Giraph, aby przetwarzać wykresy w Apache Hadoop w chmurze platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/26/2019
ms.openlocfilehash: 1f6fd88ec492f26f6819dff099ec8fe53364ba0b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552257"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Instalowanie platformy Apache Giraph w klastrach usługi HDInsight Hadoop i używanie Giraph do przetwarzania wykresów o dużej skali

Dowiedz się, jak zainstalować Apache Giraph w klastrze usługi HDInsight. Funkcja akcja skryptu usługi HDInsight umożliwia dostosowanie klastra przez uruchomienie skryptu bash. Skrypty mogą służyć do dostosowywania klastrów podczas tworzenia klastra i po nim.

## <a name="what-is-giraph"></a>Co to jest Giraph

Usługa [Apache Giraph](https://giraph.apache.org/) umożliwia przetwarzanie grafów przy użyciu usługi Hadoop i może być używana z usługą Azure HDInsight. Grafuje relacje modelu między obiektami. Na przykład połączenia między routerami w dużej sieci, takie jak Internet, lub relacje między osobami w sieciach społecznościowych. Przetwarzanie grafu umożliwia określenie relacji między obiektami w grafie, na przykład:

* Identyfikowanie potencjalnych przyjaciół w oparciu o bieżące relacje.

* Zidentyfikowanie najkrótszej trasy między dwoma komputerami w sieci.

* Obliczanie rangi stron sieci Web.

> [!WARNING]  
> Składniki dostarczane z klastrem usługi HDInsight są w pełni obsługiwane — pomoc techniczna firmy Microsoft ułatwiają izolowanie i rozwiązywanie problemów związanych z tymi składnikami.
>
> Niestandardowe składniki, takie jak Giraph, otrzymują komercyjnie uzasadnioną pomoc techniczną, która ułatwia dalsze Rozwiązywanie problemu. Pomoc techniczna firmy Microsoft może być w stanie rozwiązać problem. Jeśli nie, musisz skonsultować się z społecznościami Open Source, w których znajduje się Szczegółowa wiedza dotycząca tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takich jak: [forum MSDN dla usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Również projekty Apache mają witryny projektu na [https://apache.org](https://apache.org), na przykład: [Hadoop](https://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Co robi skrypt

Ten skrypt wykonuje następujące czynności:

* Instaluje Giraph do `/usr/hdp/current/giraph`.

* Kopiuje plik `giraph-examples.jar` do magazynu domyślnego (WASB) dla klastra: `/example/jars/giraph-examples.jar`.

## <a name="install-giraph-using-script-actions"></a>Instalowanie Giraph za pomocą akcji skryptu

Przykładowy skrypt służący do instalowania Giraph w klastrze usługi HDInsight jest dostępny pod adresem `https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`

Ta sekcja zawiera instrukcje dotyczące korzystania z przykładowego skryptu podczas tworzenia klastra przy użyciu Azure Portal.

> [!NOTE]  
> Akcję skryptu można zastosować przy użyciu dowolnej z następujących metod:
> * Program Azure PowerShell
> * Interfejs wiersza polecenia platformy Azure
> * Zestaw SDK usługi HDInsight dla platformy .NET
> * Szablony usługi Azure Resource Manager
> 
> Można również zastosować akcje skryptu do już uruchomionych klastrów. Aby uzyskać więcej informacji, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

1. Rozpocznij tworzenie klastra, wykonując kroki opisane w temacie [Tworzenie klastrów usługi HDInsight opartych](hdinsight-hadoop-create-linux-clusters-portal.md)na systemie Linux, ale nie Dokończ tworzenia. Musisz użyć **klasycznego środowiska tworzenia** i **niestandardowego (rozmiaru, ustawień, aplikacji)** .

1. W sekcji **rozmiar klastra** upewnij się, że **Liczba węzłów procesu roboczego** jest równa co najmniej 2.

1. W sekcji **Akcje skryptu** podaj następujące informacje:

    |Właściwość |Wartość |
    |---|---|
    |Typ skryptu|-Niestandardowe|
    |Nazwa|Zainstaluj Giraph|
    |Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`|
    |Typy węzłów|Główny|
    |Parametry|Pozostaw puste|

    Aby uzyskać więcej informacji, zobacz [Używanie akcji skryptu podczas tworzenia klastra](./hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation).

1. Kontynuuj tworzenie klastra zgodnie z opisem w temacie [Tworzenie klastrów usługi HDInsight opartych](hdinsight-hadoop-create-linux-clusters-portal.md)na systemie Linux.

## <a name="how-do-i-use-giraph-in-hdinsight"></a>Jak mogę używać Giraph w usłudze HDInsight?

Po utworzeniu klastra wykonaj następujące kroki, aby uruchomić przykład SimpleShortestPathsComputation zawarty w Giraph. W tym przykładzie użyto podstawowej implementacji [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) do znajdowania najkrótszej ścieżki między obiektami w grafie.

1. Użyj [polecenia SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Użyj następującego polecenia, aby utworzyć plik o nazwie **tiny_graph. txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Użyj następującego tekstu jako zawartości tego pliku:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Te dane opisują relację między obiektami na wykresie kierowanym przy użyciu formatu `[source_id, source_value,[[dest_id], [edge_value],...]]`. Każdy wiersz reprezentuje relację między obiektem `source_id` i co najmniej jednym obiektem `dest_id`. `edge_value` można traktować jako siłę lub odległość połączenia między `source_id` i `dest\_id`.

    Wystawione i przy użyciu wartości (lub wagi) jako odległości między obiektami dane mogą wyglądać podobnie jak na poniższym diagramie:

    ![tiny_graph. txt rysowany jako okręgi z wierszami o różnej odległości między](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. Aby zapisać plik, użyj **kombinacji klawiszy Ctrl + X**, a następnie **Y**, a następnie klawisz **Enter** , aby zaakceptować nazwę pliku.

4. Poniższe dane służą do przechowywania danych w magazynie podstawowym dla klastra usługi HDInsight:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Uruchom przykład SimpleShortestPathsComputation za pomocą następującego polecenia:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    > [!IMPORTANT]
    > Wartość przeniesiona do `-w` musi być mniejsza lub równa rzeczywistej liczbie węzłów procesu roboczego.

    Parametry używane z tym poleceniem są opisane w poniższej tabeli:

   | Parametr | Wyniki działania |
   | --- | --- |
   | Słoik |Plik JAR zawierający przykłady. |
   | org. Apache. Giraph. GiraphRunner |Klasa używana do uruchamiania przykładów. |
   | org. Apache. Giraph. przykłady. SimpleShortestPathsComputation |Przykład, który jest używany. W tym przykładzie oblicza najkrótszą ścieżkę między IDENTYFIKATORem 1 i wszystkimi innymi identyfikatorami na wykresie. |
   | -CA mapred. job. Monitor |Węzła głównego klastra. |
   | -vif |Format wejściowy, który ma być używany dla danych wejściowych. |
   | -VIP |Plik danych wejściowych. |
   | -vof |Format danych wyjściowych. W tym przykładzie identyfikator i wartość jako zwykły tekst. |
   | -op |Lokalizacja wyjściowa. |
   | -w 2 |Liczba procesów roboczych, które mają być używane. W tym przykładzie 2. |

    Aby uzyskać więcej informacji na temat tych i innych parametrów używanych z przykładami Giraph, zobacz [Przewodnik Szybki Start Giraph](https://giraph.apache.org/quick_start.html).

6. Po zakończeniu zadania wyniki są przechowywane w katalogu **/example/Output/shortestpaths** . Nazwy plików wyjściowych zaczynają się od **części-m-** i kończą się liczbą wskazującą pierwszy, drugi itd., plik. Użyj następującego polecenia, aby wyświetlić dane wyjściowe:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Dane wyjściowe wyglądają podobnie do następującego tekstu:

    ```output
    0    1.0
    4    5.0
    2    2.0
    1    0.0
    3    1.0
    ```

    SimpleShortestPathComputation przykład jest trudno zakodowany, aby rozpocząć od obiektu o IDENTYFIKATORze 1 i znaleźć najkrótszą ścieżkę do innych obiektów. Dane wyjściowe są w formacie `destination_id` i `distance`. `distance` to wartość (lub waga) krawędzi, która jest przenoszona między obiekt ID 1 i IDENTYFIKATORem docelowym.

    Wizualizowanie tych danych możesz sprawdzić wyniki, najkrótsze ścieżki między identyfikatorem 1 i wszystkimi innymi obiektami. Najkrótsza ścieżka między IDENTYFIKATORem 1 i 4 to 5. Ta wartość to łączna odległość między IDENTYFIKATORami 1 i 3, a następnie identyfikator 3 i 4.

    ![Rysowanie obiektów jako okręgów z najkrótszymi ścieżkami rysowanymi między](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Następne kroki

[Instalowanie i używanie odcienia w klastrach usługi HDInsight](hdinsight-hadoop-hue-linux.md).
