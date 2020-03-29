---
title: Jądra notesu jupytera w klastrach platformy Spark w usłudze Azure HDInsight
description: Dowiedz się więcej o jądrach PySpark, PySpark3 i Spark dla notesu Jupyter dostępnego w klastrach platformy Spark w usłudze Azure HDInsight.
keywords: jupyter notebook na iskrze, jupyter iskra
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/20/2020
ms.openlocfilehash: a04b8fee31ffa5280bc8ad0fca35495bb87e0e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064462"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Jądra notesu jupytera w klastrach Platformy Apache Spark w usłudze Azure HDInsight

Klastry platformy Spark usługi HDInsight zapewniają jądra, których można używać z notebookiem Jupyter w [programie Apache Spark](./apache-spark-overview.md) do testowania aplikacji. Jądro to program, który uruchamia i interpretuje kod. Trzy jądra to:

- **PySpark** - dla aplikacji napisanych w Pythonie2.
- **PySpark3** - dla aplikacji napisanych w Python3.
- **Spark** - dla aplikacji napisanych w Scali.

W tym artykule dowiesz się, jak korzystać z tych jąder i jakie są korzyści płynące z ich używania.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Platformy Spark Apache w pliku HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Tworzenie notebooka Jupyter na spark HDInsight

1. Z [witryny Azure portal](https://portal.azure.com/)wybierz klaster platformy Spark.  Aby uzyskać instrukcje, zobacz [Lista i pokazyj klastry.](../hdinsight-administer-use-portal-linux.md#showClusters) Zostanie otwarty widok **Przegląd.**

2. W widoku **Przegląd** w polu **Pulpity nawigacyjne klastra** wybierz pozycję **Notes jupyter**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

    ![Notebook Jupyter na Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Notebook Jupyter na Spark")
  
   > [!NOTE]  
   > Możesz również dotrzeć do notesu Jupyter w klastrze Spark, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Wybierz **pozycję Nowy**, a następnie wybierz pozycję **Pyspark**, **PySpark3**lub **Spark,** aby utworzyć notes. Użyj jądra Spark dla aplikacji Scala, jądra PySpark dla aplikacji Python2 i jądra PySpark3 dla aplikacji Python3.

    ![Jądra dla notebooka Jupyter na Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Jądra dla notebooka Jupyter na Spark")

4. Zostanie otwarty notes z wybranym jądrem.

## <a name="benefits-of-using-the-kernels"></a>Korzyści z używania jąder

Oto kilka zalet korzystania z nowych jąder z notebookiem Jupyter w klastrach Spark HDInsight.

- **Wstępnie ustawione konteksty**. W **przypadku pyspark,** **PySpark3**lub jądra **Spark** nie trzeba wyraźnie ustawiać kontekstów Spark lub Hive przed rozpoczęciem pracy z aplikacjami. Są one domyślnie dostępne. Konteksty te są następujące:

  - **sc** - dla kontekstu Spark
  - **sqlContext** - dla kontekstu hive

    Tak więc **nie** trzeba uruchamiać instrukcji, takich jak następujące, aby ustawić konteksty:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    Zamiast tego można bezpośrednio użyć wstępnie ustawionych kontekstów w aplikacji.

- **Magia komórek**. Jądro PySpark zawiera kilka wstępnie zdefiniowanych "magii", które są specjalnymi poleceniami, `%%` `%%MAGIC` `<args>`z którymi można dzwonić (na przykład). Magiczne polecenie musi być pierwszym słowem w komórce kodu i umożliwiać wiele wierszy zawartości. Magiczne słowo powinno być pierwszym słowem w komórce. Dodanie czegokolwiek przed magią, a nawet komentarze, powoduje błąd.     Aby uzyskać więcej informacji na temat magii, zobacz [tutaj](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    W poniższej tabeli wymieniono różne magie dostępne za pośrednictwem jąder.

   | Magii | Przykład | Opis |
   | --- | --- | --- |
   | Pomoc |`%%help` |Generuje tabelę wszystkich dostępnych magii z przykładem i opisem |
   | informacje o |`%%info` |Dane wyjściowe informacje o sesji dla bieżącego punktu końcowego Livy |
   | Skonfigurować |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfiguruje parametry tworzenia sesji. Flaga siły (-f) jest obowiązkowa, jeśli sesja została już utworzona, co zapewnia, że sesja zostanie porzucona i odtworzona. Spójrz na [Livy POST / sesje Treść żądania](https://github.com/cloudera/livy#request-body) listy prawidłowych parametrów. Parametry muszą być przekazywane jako ciąg JSON i musi znajdować się w następnym wierszu po magii, jak pokazano w przykładowej kolumnie. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Wykonuje kwerendę hive względem sqlContext. Jeśli `-o` parametr jest przekazywany, wynik kwerendy jest zachowywany w kontekście %%local Python jako ramka danych [Pandas.](https://pandas.pydata.org/) |
   | local |`%%local`<br>`a=1` |Cały kod w kolejnych wierszach jest wykonywany lokalnie. Kod musi być prawidłowy kod Python2, nawet niezależnie od jądra, którego używasz. Tak więc, nawet jeśli podczas tworzenia notesu wybrano jądra **PySpark3** lub **Spark,** jeśli używasz `%%local` magii w komórce, komórka ta musi mieć tylko prawidłowy kod Python2. |
   | dzienniki |`%%logs` |Wyprowadza dzienniki dla bieżącej sesji Livy. |
   | delete |`%%delete -f -s <session number>` |Usuwa określoną sesję bieżącego punktu końcowego Livy. Nie można usunąć sesji zainicjowanej dla samego jądra. |
   | oczyszczanie |`%%cleanup -f` |Usuwa wszystkie sesje dla bieżącego punktu końcowego Livy, w tym sesji tego notesu. Flaga siły -f jest obowiązkowa. |

   > [!NOTE]  
   > Oprócz magii dodawanych przez jądro PySpark, możesz również użyć [wbudowanej magii IPython, w](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)tym. `%%sh` Za pomocą `%%sh` magii można uruchamiać skrypty i blokować kod na headnode klastra.

- **Automatyczna wizualizacja**. Jądro Pyspark automatycznie wizualizuje dane wyjściowe zapytań hive i SQL. Można wybrać jeden z kilku różnych typów wizualizacji, w tym Tabela, Koł, Linia, Obszar, Pasek.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametry obsługiwane za pomocą programu %%sql magic

Magia `%%sql` obsługuje różne parametry, których można użyć do kontrolowania rodzaju danych wyjściowych, które są odbierane podczas uruchamiania kwerend. W poniższej tabeli wymieniono dane wyjściowe.

| Parametr | Przykład | Opis |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Ten parametr służy do utrwalania wyniku kwerendy w kontekście %%local Python jako ramki danych [Pandas.](https://pandas.pydata.org/) Nazwa zmiennej dataframe jest określoną nazwą zmiennej. |
| -q |`-q` |Użyj tej funkcji, aby wyłączyć wizualizacje dla komórki. Jeśli nie chcesz automatycznie rewizualizować zawartości komórki i po prostu chcesz ją przechwycić jako ramkę danych, użyj programu `-q -o <VARIABLE>`. Jeśli chcesz wyłączyć wizualizacje bez przechwytywania wyników (na przykład do uruchamiania `CREATE TABLE` kwerendy `-q` SQL, takiej `-o` jak instrukcja), użyj bez określania argumentu. |
| -m |`-m <METHOD>` |W przypadku **gdy metoda** jest **albo pobrać** lub **próbki** (domyślnie jest **wziąć).** Jeśli metoda jest **podejmowana,** jądro wybiera elementy z góry zestawu danych wynikowych określonego przez MAXROWS (opisanego w dalszej części tej tabeli). Jeśli metodą jest **próbka,** jądro losowo pobiera próbki `-r` elementów zestawu danych zgodnie z parametrem, opisanymi dalej w tej tabeli. |
| -r |`-r <FRACTION>` |Tutaj **FRACTION** jest liczbą zmiennoprzecinkową między 0,0 a 1,0. Jeśli przykładową metodą kwerendy SQL jest `sample`, to jądro losowo próbki określony ułamek elementów zestawu wyników dla Ciebie. Na przykład po uruchomieniu kwerendy SQL `-m sample -r 0.01`z argumentami, a następnie 1% wierszy wyników są losowo próbkowane. |
| -n |`-n <MAXROWS>` |**MAXROWS** jest wartością całkowitą. Jądro ogranicza liczbę wierszy wyjściowych do **MAXROWS**. Jeśli **MAXROWS** jest liczbą ujemną, taką jak **-1,** liczba wierszy w zestawie wyników nie jest ograniczona. |

**Przykład:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Powyższe oświadczenie wykonuje następujące czynności:

- Wybiera wszystkie rekordy z **pouci.**
- Ponieważ używamy -q, wyłącza autowizualizację.
- Ponieważ używamy `-m sample -r 0.1 -n 500` go losowo próbki 10% wierszy w powierachwzdnych i ogranicza rozmiar wynik ustawiony do 500 wierszy.
- Na koniec, ponieważ `-o query2` użyliśmy go również zapisuje dane wyjściowe w ramce danych o nazwie **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Uwagi podczas korzystania z nowych jąder

Niezależnie od tego, którego jądra użyjesz, pozostawienie uruchomionych notesów zużywa zasoby klastra.  Z tych jąder, ponieważ konteksty są wstępnie ustawione, po prostu zamykanie notesów nie zabija kontekstu i dlatego zasoby klastra nadal są w użyciu. Dobrą praktyką jest użycie opcji **Zamknij i Zatrzymaj** z menu **Plik** notesu po zakończeniu korzystania z notesu, który zabija kontekst, a następnie kończy pracę notesu.

## <a name="where-are-the-notebooks-stored"></a>Gdzie są przechowywane notesy?

Jeśli klaster używa usługi Azure Storage jako domyślnego konta magazynu, notesy Jupyter są zapisywane na koncie magazynu w folderze **/HdiNotebooks.**  Notesy, pliki tekstowe i foldery utworzone z poziomu programu Jupyter są dostępne z konta magazynu.  Na przykład, jeśli używasz Jupyter do utworzenia **folderu myfolder** i folderu notesu/mynotebook.ipynb, możesz uzyskać dostęp do tego notesu na **myfolder/mynotebook.ipynb** `/HdiNotebooks/myfolder/mynotebook.ipynb` koncie magazynu.  Odwrotnie jest również prawdą, to znaczy, jeśli prześlesz `/HdiNotebooks/mynotebook1.ipynb`notebooka bezpośrednio do konta pamięci masowej w , notebook jest widoczny z Jupyter, jak również.  Notesy pozostają na koncie magazynu nawet po usunięciu klastra.

> [!NOTE]  
> Klastry usługi HDInsight z usługą Azure Data Lake Storage jako magazyn domyślny nie przechowują notesów w skojarzonym magazynie.

Sposób zapisywania notebooków na koncie pamięci masowej jest zgodny z [systemem Apache Hadoop HDFS.](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) Tak więc, jeśli SSH do klastra można użyć poleceń zarządzania plikami, jak pokazano w następującym urywek:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it's visible from Jupyter

Niezależnie od tego, czy klaster używa usługi Azure Storage lub usługi Azure Data Lake Storage jako `/var/lib/jupyter`domyślnego konta magazynu, notesy są również zapisywane w pliku headnode klastra w programie .

## <a name="supported-browser"></a>Obsługiwana przeglądarka

Notebooki Jupyter w klastrach Spark HDInsight są obsługiwane tylko w Google Chrome.

## <a name="feedback"></a>Opinia

Nowe jądra są w fazie ewoluującej i będą dojrzewać w czasie. Może to również oznaczać, że interfejsy API mogą ulec zmianie w miarę dojrzewania tych jąder. Będziemy wdzięczni za wszelkie opinie, które masz podczas korzystania z tych nowych jąder. Jest to przydatne w kształtowaniu ostatecznego wydania tych jąder. Komentarze/opinie można zostawić w sekcji **Opinie** u dołu tego artykułu.

## <a name="see-also"></a>Zobacz też

- [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

- [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
- [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
- [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

- [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
- [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

- [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
- [Użyj wtyczki HDInsight Tools dla IntelliJ IDEA do zdalnego debugowania aplikacji Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](apache-spark-zeppelin-notebook.md)
- [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
- [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

- [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
- [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
