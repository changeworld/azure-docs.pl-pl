---
title: Instalowanie i używanie systemu Giraph w usłudze Azure HDInsight
description: Dowiedz się, jak zainstalować system Giraph w klastrach HDInsight za pomocą akcji skryptu. System Giraph umożliwia czy wykres przetwarzania na platformie Apache Hadoop w chmurze platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: aa13d8dfc65f020f3f27183423913933cd0b9404
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64697600"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Zainstaluj Apache Giraph w klastrach usługi HDInsight Hadoop, a przetwarzanie dużych wykresów za pomocą systemu Giraph

Dowiedz się, jak zainstalować system Apache Giraph w klastrze usługi HDInsight. Funkcja akcji skryptu HDInsight umożliwia dostosowanie klastra, uruchamiając skrypt powłoki bash. Skrypty można dostosowywać klastry, podczas i po utworzeniu klastra.

## <a name="whatis"></a>Co to jest system Giraph

[Apache Giraph](https://giraph.apache.org/) umożliwia wykonywanie wykres przetwarzania za pomocą usługi Hadoop i mogą być używane z usługi Azure HDInsight. Wykresy modelowania relacji między obiektami. Na przykład połączenia między routerami w dużej sieci, takich jak Internet lub relacji między osobami w sieciach społecznościowych. Przetwarzanie wykresów umożliwia przeglądanie informacji o relacji między obiektami w grafie, takich jak:

* Identyfikowanie potencjalnych znajomych, na podstawie Twojej bieżącej relacji.

* Identyfikowanie najkrótszej trasy między dwoma komputerami w sieci.

* Obliczanie rangę strony stron sieci Web.

> [!WARNING]  
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane — Microsoft Support pomaga wyizolować i rozwiązać problemy związane z tych składników.
>
> Składniki niestandardowe, takie jak system Giraph otrzymują uzasadnioną komercyjnie pomoc techniczną, aby pomóc rozwiązać ten problem. Microsoft Support może mieć do rozwiązania problemu. W przeciwnym razie należy zapoznać się z społeczności "open source", gdzie znajduje się specjalistyczna dla tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [Forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Projektów Apache mieć witryny projektu na [ https://apache.org ](https://apache.org), na przykład: [Hadoop](https://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Działanie skryptu

Ten skrypt wykonuje następujące czynności:

* Instaluje system Giraph do `/usr/hdp/current/giraph`

* Kopiuje `giraph-examples.jar` plik do magazynu domyślnego (WASB) dla klastra: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Zainstalować system Giraph za pomocą akcji skryptu

Przykładowy skrypt, aby zainstalować system Giraph w klastrze usługi HDInsight jest dostępna w następującej lokalizacji:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Ta sekcja zawiera instrukcje dotyczące sposobu używania przykładowy skrypt podczas tworzenia klastra przy użyciu witryny Azure portal.

> [!NOTE]  
> Akcja skryptu można zastosować przy użyciu dowolnej z następujących metod:
> * Azure PowerShell
> * Interfejs wiersza polecenia platformy Azure
> * HDInsight SDK platformy .NET
> * Szablony usługi Azure Resource Manager
> 
> Można również zastosować akcji skryptu na już działające klastry. Aby uzyskać więcej informacji, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md).

1. Rozpoczęcie tworzenia klastra wykonując kroki opisane w [klastry HDInsight opartych na systemie Linux z tworzenia](hdinsight-hadoop-create-linux-clusters-portal.md), kroków tworzenia.

2. W **opcjonalna konfiguracja** zaznacz **akcji skryptu**i podaj następujące informacje:

   * **NAZWA**: Wprowadź przyjazną nazwę dla akcji skryptu.

   * **IDENTYFIKATOR URI SKRYPTU**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: Sprawdź ten wpis.

   * **PROCES ROBOCZY**: Ten wpis należy pozostawić puste.

   * **ZOOKEEPER**: Ten wpis należy pozostawić puste.

   * **PARAMETRY**: Pozostaw to pole puste.

3. W dolnej części **akcji skryptu**, użyj **wybierz** przycisk, aby zapisać konfigurację. Na koniec użyj **wybierz** znajdujący się u dołu **opcjonalna konfiguracja** sekcji, aby zapisać informacje o konfiguracji opcjonalnej.

4. Kontynuuj tworzenie klastra zgodnie z opisem w [klastry HDInsight opartych na systemie Linux z tworzenia](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Jak korzystać z systemu Giraph w HDInsight?

Po utworzeniu klastra, wykonaj następujące kroki, aby uruchomić przykład SimpleShortestPathsComputation dołączone do systemu Giraph. W tym przykładzie użyto podstawowa [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) wdrażania służące do znajdowania najkrótsze ścieżki między obiektami w grafie.

1. Nawiąż połączenie z klastrem usługi HDInsight przy użyciu protokołu SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj następującego polecenia, aby utworzyć plik o nazwie **tiny_graph.txt**:

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

    Te dane w tym artykule opisano relację między obiektami na grafie skierowanym przy użyciu formatu `[source_id, source_value,[[dest_id], [edge_value],...]]`. Każdy wiersz reprezentuje relację między `source_id` obiektu i co najmniej jeden `dest_id` obiektów. `edge_value` Mogą być uważane za siłę lub odległości połączenie między `source_id` i `dest\_id`.

    Rysowania, i jako odległość między obiektami za pomocą wartości (lub waga), dane może wyglądać podobnie jak na poniższym diagramie:

    ![tiny_graph.txt rysowana w formie okręgów linie różnych odległość między](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Aby zapisać plik, użyj **Ctrl + X**, następnie **Y**, a na koniec **Enter** zaakceptować nazwę pliku.

4. Przechowywanie danych na podstawowy magazyn dla klastra usługi HDInsight, należy wykonać następujące kroki:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Uruchom przykład SimpleShortestPathsComputation, używając następującego polecenia:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    W poniższej tabeli opisano parametry używane przy użyciu następującego polecenia:

   | Parametr | Wyniki działania |
   | --- | --- |
   | `jar` |Plik jar zawierający przykładów. |
   | `org.apache.giraph.GiraphRunner` |Klasa, używane do uruchamiania przykładów. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Przykład, który jest używany. W tym przykładzie oblicza najkrótsze ścieżki między ID 1 i inne identyfikatory na wykresie. |
   | `-ca mapred.job.tracker` |Węzeł główny dla klastra. |
   | `-vif` |Format wejściowy do użycia dla danych wejściowych. |
   | `-vip` |Plik danych wejściowych. |
   | `-vof` |Format danych wyjściowych. W tym przykładzie, Identyfikatora i wartość w postaci zwykłego tekstu. |
   | `-op` |Lokalizacja danych wyjściowych. |
   | `-w 2` |Liczba procesów roboczych do użycia. W tym przykładzie 2. |

    Aby uzyskać więcej informacji na temat tych i innych parametrów używane z systemu Giraph przykładów, zobacz [Szybki Start Giraph](https://giraph.apache.org/quick_start.html).

6. Po zakończeniu zadania, wyniki są przechowywane w **/example/out/shortestpaths** katalogu. Nazw plików wyjściowych zaczynają się od **część-m -** oraz kończyć się liczbę określającą pierwszy, drugi, plików itp. Aby wyświetlić dane wyjściowe, użyj następującego polecenia:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Dane wyjściowe wyglądają podobnie do następującego tekstu:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation, jest przykład twardych kodowanego zaczynać obiekt o identyfikatorze 1 i najkrótsze ścieżki do innych obiektów. Dane wyjściowe są w formacie `destination_id` i `distance`. `distance` Wartość (lub waga) krawędzi przemieścić się między tymi obiekt ID 1 i identyfikatora celu.

    Zaprezentowania tych danych, można sprawdzić wyniki, podróżowanie najkrótsze ścieżki między 1 identyfikator i innych obiektów. Najkrótsze ścieżki między ID 1 i 4 identyfikator wynosi 5. Ta wartość jest całkowita odległość między <span style="color:orange">ID 1 i 3</span>, a następnie <span style="color:red">Identyfikatorem 3 i 4</span>.

    ![Rysowanie obiektów jako kółka, przy użyciu najkrótsze ścieżki między](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Kolejne kroki

* [Instalowanie i korzystanie z rozwiązania Hue w klastrach HDInsight](hdinsight-hadoop-hue-linux.md).
