---
title: Zainstalować i używać Giraph w usłudze Azure HDInsight (Hadoop) - | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować Giraph w klastrach usługi HDInsight opartej na systemie Linux za pomocą akcji skryptu. Akcje skryptu umożliwiają dostosowanie klastra podczas tworzenia, zmieniając konfigurację klastra lub instalowania usług i narzędzi.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9fcac906-8f06-4002-9fe8-473e42f8fd0f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 03e72c29bedf6a3125a5ae0272e93cdf58632bc6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177037"
---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Zainstaluj Giraph w klastrach HDInsight Hadoop i używać Giraph do przetwarzania dużych wykresów

Dowiedz się, jak zainstalować Apache Giraph w klastrze usługi HDInsight. Funkcja Akcja skryptu HDInsight umożliwia dostosowanie klastra przez uruchomienie skryptu bash. Skrypty można dostosować klastry, podczas i po utworzeniu klastra.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight, który używa systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="whatis"></a>Co to jest Giraph

[Apache Giraph](http://giraph.apache.org/) umożliwia wykonywanie wykresu przetwarzanie przy użyciu platformy Hadoop i mogą być używane z usługi Azure HDInsight. Wykresy modelu relacje między obiektami. Na przykład połączeń między routerami w dużych sieci, takich jak Internet lub relacje między osób w sieciach społecznościowych. Wykres przetwarzania umożliwia przeglądanie informacji o relacje między obiektami na wykresie, takich jak:

* Identyfikuje potencjalne znajomych oparte na bieżącej relacji.

* Identyfikowanie najkrótszy trasy między dwoma komputerami w sieci.

* Obliczanie rangę strony stron sieci Web.

> [!WARNING]
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane - Microsoft Support pomaga w celu odizolowania i rozwiązać problemy związane z tych składników.
>
> Niestandardowe składniki, takie jak Giraph, otrzymywanie pomocy uzasadnione ekonomicznie ułatwiające aby dalej rozwiązywać ten problem. Microsoft Support może być możliwe do rozwiązania problemu. Jeśli nie, należy zapoznać się z Wspólnot typu open source wykryto głębokie doświadczenia z tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [forum MSDN dla usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Projekty Apache mieć witryny projektu na [ http://apache.org ](http://apache.org), na przykład: [Hadoop](http://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Działanie skryptu

Ten skrypt wykonuje następujące czynności:

* Instaluje Giraph do `/usr/hdp/current/giraph`

* Kopie `giraph-examples.jar` pliku do magazynu domyślnego (WASB) dla klastra: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Zainstaluj Giraph za pomocą akcji skryptu

Przykładowy skrypt do zainstalowania Giraph w klastrze usługi HDInsight jest dostępna w następującej lokalizacji:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Ta sekcja zawiera instrukcje dotyczące sposobu używania przykładowy skrypt podczas tworzenia klastra przy użyciu portalu Azure.

> [!NOTE]
> Akcja skryptu można zastosować za pomocą jednej z następujących metod:
> * Azure PowerShell
> * Azure CLI
> * Zestaw .NET SDK usługi HDInsight
> * Szablony usługi Azure Resource Manager
> 
> Akcje skryptu można również dotyczą już działające klastry. Aby uzyskać więcej informacji, zobacz [Dostosowywanie klastrów usługi HDInsight z akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

1. Rozpocznij tworzenie klastra przy użyciu kroków w [klastrów usługi HDInsight opartych na tworzenie Linux](hdinsight-hadoop-create-linux-clusters-portal.md), kroków tworzenia.

2. W **konfiguracji opcjonalnej** zaznacz **akcji skryptu**i podaj następujące informacje:

   * **Nazwa**: Wprowadź przyjazną nazwę dla akcji skryptu.

   * **IDENTYFIKATOR URI SKRYPTU**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: Sprawdź tego wpisu

   * **Proces ROBOCZY**: ten wpis należy pozostawić niezaznaczone

   * **DOZORCY**: ten wpis należy pozostawić niezaznaczone

   * **Parametry**: pozostaw to pole puste

3. W dolnej części **akcji skryptu**, użyj **wybierz** przycisk, aby zapisać konfigurację. Na koniec użyj **wybierz** przycisk w dolnej części **konfiguracji opcjonalnej** sekcji w celu zapisania informacji o konfiguracji opcjonalnej.

4. Kontynuuj tworzenie klastra, zgodnie z opisem w [klastrów usługi HDInsight opartych na tworzenie Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Jak używać Giraph w usłudze HDInsight?

Po utworzeniu klastra, wykonaj następujące kroki, aby uruchomić przykład SimpleShortestPathsComputation dołączonego Giraph. W tym przykładzie użyto podstawowego [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) implementacji odnajdywania najkrótszy ścieżki między obiektami na wykresie.

1. Nawiąż połączenie z klastrem usługi HDInsight przy użyciu protokołu SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj następującego polecenia, aby utworzyć plik o nazwie **tiny_graph.txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Zawartość tego pliku, należy użyć następującego tekstu:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Te dane w tym artykule opisano relacje między obiektami w ukierunkowanego wykresu, w formacie `[source_id, source_value,[[dest_id], [edge_value],...]]`. Każdy wiersz zawiera relację między `source_id` obiektu i co najmniej jeden `dest_id` obiektów. `edge_value` Można traktować jako siłę lub odległość połączenie między `source_id` i `dest\_id`.

    Rysowane, i przy użyciu wartości (lub wagi) jako odległość między obiektami, danych może wyglądać podobnie jak na poniższym diagramie:

    ![tiny_graph.txt rysowane jako okręgi wiersze z różnymi odległość między](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Aby zapisać plik, użyj **Ctrl + X**, następnie **Y**, a na końcu **Enter** aby zaakceptować nazwę pliku.

4. Użyj następujących funkcji do przechowywania danych na podstawowy magazyn dla klastra usługi HDInsight:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Uruchom przykład SimpleShortestPathsComputation za pomocą następującego polecenia:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Parametry używane w tym poleceniu są opisane w poniższej tabeli:

   | Parametr | Wyniki działania |
   | --- | --- |
   | `jar` |Plik jar zawierający przykłady. |
   | `org.apache.giraph.GiraphRunner` |Klasa używane do uruchamiania przykładów. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Przykład, w którym jest używany. W tym przykładzie oblicza najkrótszy ścieżki między 1 Identyfikatora i inne identyfikatory na wykresie. |
   | `-ca mapred.job.tracker` |Headnode dla klastra. |
   | `-vif` |Format wejściowy do użycia dla danych wejściowych. |
   | `-vip` |Plik danych wejściowych. |
   | `-vof` |Format danych wyjściowych. W tym przykładzie, Identyfikatora i wartości w postaci zwykłego tekstu. |
   | `-op` |Lokalizacja danych wyjściowych. |
   | `-w 2` |Liczba procesów roboczych do użycia. W tym przykładzie 2. |

    Aby uzyskać więcej informacji na temat tych i innych parametrów używane z próbek Giraph, zobacz [szybkiego startu Giraph](http://giraph.apache.org/quick_start.html).

6. Po zakończeniu zadania, wyniki są przechowywane w **/example/out/shotestpaths** katalogu. Nazwy pliku wyjściowego zaczynają się od **części-m -** i kończyć się liczbę określającą pierwszy, drugi, itp. plik. Aby wyświetlić dane wyjściowe, użyj następującego polecenia:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Wynik jest podobny do następującego tekstu:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation, który przykład jest trudna do uruchomienia z kodowanego obiektu ID 1 i znaleźć najkrótszy ścieżki do innych obiektów. Dane wyjściowe są w formacie `destination_id` i `distance`. `distance` Wartości (lub wagi) krawędzi przemieścić się między obiektu ID 1 i identyfikatora docelowego.

    Wizualizacja danych, można sprawdzić wyniki za podróży najkrótszy ścieżek między 1 Identyfikatora i wszystkie inne obiekty. Najkrótszy ścieżki między ID 1 i 4 identyfikator wynosi 5. Ta wartość jest całkowita odległość między <span style="color:orange">ID 1 i 3</span>, a następnie <span style="color:red">identyfikator 3 i 4</span>.

    ![Rysowanie obiektów jako kółka za pomocą najmniejszej ścieżek między](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Kolejne kroki

* [Zainstalować i używać Hue w klastrach HDInsight](hdinsight-hadoop-hue-linux.md).

* [Zainstaluj Solr w klastrach HDInsight](hdinsight-hadoop-solr-install-linux.md).
