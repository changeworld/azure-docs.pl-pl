---
title: Jądra dla notesu Jupyter w klastrach Spark w usłudze Azure HDInsight
description: Więcej informacji na temat jądra PySpark, PySpark3 i platformy Spark dla notesu programu Jupyter dostępne klastry Spark w usłudze Azure HDInsight.
keywords: Notes jupyter na platformie spark, jupyter, platformy spark
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: b2ae24c0449b009db6fcecdd8a1366ea5154629a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257820"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Jądra dla notesu Jupyter w klastrach platformy Apache Spark w usłudze Azure HDInsight 

Klastry HDInsight Spark zapewniają jądra, których można użyć z notesem Jupyter, na [platformy Apache Spark](https://spark.apache.org/) do testowania aplikacji. Jądro jest program, który jest uruchamiany i interpretuje słowa kluczowe w kodzie. Dostępne są następujące trzy jądra:

- **PySpark** — dla aplikacji napisanych w Python2.
- **PySpark3** — dla aplikacji napisanych w języku Python3.
- **Platforma Spark** — dla aplikacji napisanych w języku Scala.

W tym artykule dowiesz się, jak używać tych tych jąder i zalet korzystania z nich.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Spark w HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Tworzenie notesu Jupyter w HDInsight Spark

1. Z [witryny Azure portal](https://portal.azure.com/), wybierz klaster Spark.  Zobacz [listy i wyświetlaniu klastrów](../hdinsight-administer-use-portal-linux.md#showClusters) Aby uzyskać instrukcje. **Przegląd** wyświetlić zostanie otwarta.

2. Z **Przegląd** widoku **pulpity nawigacyjne klastra** wybierz opcję **notesu programu Jupyter**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

    ![Notes Jupyter na platformie Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter notebook na platformie Spark") 
  
   > [!NOTE]  
   > Można również osiągnąć notesu Jupyter w klastrze Spark, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Wybierz **New**, a następnie wybierz opcję **Pyspark**, **PySpark3**, lub **Spark** do utworzenia notesu. Na użytek Spark jądra dla aplikacji w języku Scala, jądra PySpark dla aplikacji Python2 i jądra PySpark3 środowiska python3 jako aplikacji.
   
    ![Jądra dla notesu Jupyter na platformie Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "jądra dla notesu Jupyter na platformie Spark") 

4. Otwiera notesu za pośrednictwem jądra wybrane.

## <a name="benefits-of-using-the-kernels"></a>Korzyści z używania tych jąder

Poniżej przedstawiono kilka korzyści wynikające ze stosowania nowych jądra za pomocą notesu Jupyter w klastrach HDInsight Spark.

- **Ustawienie wstępne kontekstów**. Za pomocą **PySpark**, **PySpark3**, lub **Spark** jądra, nie trzeba jawnie ustawić konteksty Spark i Hive, przed rozpoczęciem pracy z aplikacjami. Są one dostępne domyślnie. Tych kontekstach są następujące:
   
  * **sc** — w przypadku kontekstu aparatu Spark
  * **sqlContext** — w kontekście programu Hive
   
    Nie trzeba więc uruchomienia instrukcji, podobnie do poniższego, do ustawiania kontekstów:
   
         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)
   
    Wstępnie ustawionych kontekstów można zamiast tego należy korzystać bezpośrednio w aplikacji.

- **Komórka poleceń magicznych**. Jądra PySpark zawiera kilka wstępnie zdefiniowanych "poleceń magicznych", które są specjalne polecenia, które można wywoływać za pomocą `%%` (na przykład `%%MAGIC` `<args>`). Magiczne polecenie musi być pierwszy wyraz w komórce kodu i umożliwić wiele wierszy w zawartości. Magic word powinien być pierwszy wyraz w komórce. Dodawanie czegokolwiek przed magic, a nawet komentarzy powoduje błąd.     Aby uzyskać więcej informacji na temat poleceń magicznych, zobacz [tutaj](https://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    W poniższej tabeli wymieniono różne poleceń magicznych dostępnych za pośrednictwem jądra.

   | Magic | Przykład | Opis |
   | --- | --- | --- |
   | pomoc |`%%help` |Generuje spis wszystkich dostępnych poleceń magicznych przy użyciu przykładu i opis |
   | Informacje o |`%%info` |Informacje o sesji danych wyjściowych dla bieżącego punktu końcowego usługi Livy |
   | Konfigurowanie |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Umożliwia skonfigurowanie parametrów do utworzenia sesji. Flagi force (-f) jest wymagane, jeśli sesja została już utworzona, który zapewnia, że sesja jest porzucona i utworzona ponownie. Przyjrzyj się [/sessions WPIS usługi Livy firmy treść żądania](https://github.com/cloudera/livy#request-body) listę prawidłowych parametrów. Parametry muszą być przekazywane w postaci ciągu JSON i musi być w następnym wierszu po magic, jak pokazano w przykładzie kolumna. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Wykonuje zapytanie programu Hive względem sqlContext. Jeśli `-o` parametr jest przekazywany, wynik kwerendy są utrwalane w %% kontekstu Python lokalnego jako [Pandas](https://pandas.pydata.org/) ramki danych. |
   | lokalne |`%%local`<br>`a=1` |Cały kod w kolejnych wierszy jest wykonywana lokalnie. Kod musi być prawidłowym kodem Python2 nawet niezależnie od tego, jądra, którego używasz. Tak, nawet jeśli wybrano **PySpark3** lub **Spark** jądra podczas tworzenia notesu, jeśli używasz `%%local` magic w komórce, ta komórka może mieć tylko prawidłowy kod Python2. |
   | logs |`%%logs` |Dane wyjściowe dzienników dla bieżącej sesji usługi Livy. |
   | delete |`%%delete -f -s <session number>` |Usuwa sesję określonych bieżący punkt końcowy usługi Livy. Nie można usunąć sesji, która jest inicjowane dla samego jądra. |
   | oczyszczanie |`%%cleanup -f` |Usuwa wszystkie sesje dla bieżący punkt końcowy usługi Livy, łącznie z sesją ten notes. Wymuś flagi -f. jest obowiązkowy. |

   > [!NOTE]  
   > Oprócz poleceń magicznych, dodane przez jądro PySpark, można również użyć [wbudowanych poleceń magicznych IPython](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), w tym `%%sh`. Możesz użyć `%%sh` Magic Quadrant na uruchamianie skryptów i bloku kodu na głównym węzłem klastra.

- **Automatyczna wizualizacja**. Jądra Pyspark automatycznie wizualizuje dane wyjściowe zapytań SQL i Hive. Możesz wybrać między kilka różnych typów wizualizacji, łącznie z tabeli, kołowego, wiersz, obszaru, pasek.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametry są obsługiwane za pomocą %% magicznym wyrażeniem sql
`%%sql` Magic obsługuje parametry, których można użyć do kontrolowania rodzaj danych wyjściowych, który jest wyświetlany podczas uruchamiania kwerend. W poniższej tabeli wymieniono dane wyjściowe.

| Parametr | Przykład | Opis |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Użyj tego parametru, aby utrwalić wynik zapytania, w %% kontekstu Python lokalnego jako [Pandas](https://pandas.pydata.org/) ramki danych. Nazwa zmiennej ramka danych jest nazwa zmiennej, którą określisz. |
| -q |`-q` |Umożliwia wyłączanie wizualizacji komórki. Jeśli nie chcesz autovisualize zawartość komórki i po prostu chcesz przechwytują ją jako ramkę danych, należy użyć `-q -o <VARIABLE>`. Jeśli chcesz wyłączyć wizualizacji bez przechwytywania wyników (na przykład w przypadku uruchomienia zapytania SQL, takie jak `CREATE TABLE` instrukcji), użyj `-q` bez określania `-o` argumentu. |
| -m |`-m <METHOD>` |Gdzie **metoda** jest **zająć** lub **przykładowe** (wartość domyślna to **zająć**). Jeśli metoda jest **zająć**, jądra wybiera elementy od początku zestawu danych wyników określony przez maksymalna liczba wierszy (opisane dalej w tej tabeli). Jeśli metoda jest **przykładowe**, jądra losowo przykłady elementy zestawu danych zgodnie z opisem w `-r` parametru, opisane w dalszej części w tej tabeli. |
| -r |`-r <FRACTION>` |W tym miejscu **UŁAMEK** jest liczbą zmiennoprzecinkową z zakresu od od 0,0 do 1,0. Jeśli przykładowa metoda dla zapytania SQL jest `sample`, a następnie jądra losowo przykłady określonym ułamkiem elementy zestawu dla Ciebie wyników. Na przykład uruchomić zapytanie SQL z argumentami `-m sample -r 0.01`, a następnie 1% wiersze wynikowe są losowo próbkowane. |
| -n |`-n <MAXROWS>` |**Maksymalna liczba wierszy** jest liczbą całkowitą. Jądro ogranicza liczbę wierszy danych wyjściowych do **MAXROWS**. Jeśli **MAXROWS** jest liczbą ujemną, takich jak **-1**, wówczas nie jest ograniczona liczba wierszy w zestawie wyników. |

**Przykład:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Instrukcja powyżej wykonuje następujące czynności:

* Wybiera wszystkie rekordy z **hivesampletable**.
* Ponieważ używamy - q, wyłącza autovisualization.
* Ponieważ używamy `-m sample -r 0.1 -n 500` losowo 10% wierszy w tabeli hivesampletable z przykładami i ogranicza rozmiar zestawu wyników do 500 wierszy.
* Na koniec ponieważ użyto `-o query2` zapisuje dane wyjściowe do elementów dataframe o nazwie **kwerenda2**.

## <a name="considerations-while-using-the-new-kernels"></a>Zagadnienia dotyczące podczas korzystania z nowego jądra

Niezależnie od jądra, należy użyć, pozostawiając notesów systemem wykorzystuje zasoby klastra.  Za pomocą tych jądra ponieważ konteksty są ustawione wstępnie tak, po prostu zamykania notesów nie zabije kontekstu i dlatego zasobów klastra w dalszym ciągu będzie używana. Dobrym rozwiązaniem jest użycie **Zamknij i zatrzymanie** opcji z notesu **pliku** menu, po zakończeniu korzystania z notesu, który kasuje kontekstu, a następnie kończy działanie notesu.

## <a name="where-are-the-notebooks-stored"></a>Gdzie są przechowywane notesów

Jeśli klaster używa usługi Azure Storage jako domyślne konto magazynu, notesów programu Jupyter są zapisywane do konta magazynu w ramach **/HdiNotebooks** folderu.  Notesów, pliki tekstowe i foldery, które tworzysz z w ramach programu Jupyter są dostępne z konta magazynu.  Na przykład, jeśli używasz programu Jupyter do utworzenia folderu **MójFolder** i notebook **myfolder/mynotebook.ipynb**, możesz uzyskać dostęp tego notesu w `/HdiNotebooks/myfolder/mynotebook.ipynb` w ramach konta magazynu.  Występuje również sytuacja odwrotna ma wartość true, to znaczy, jeśli przekazywanie notesu za pomocą bezpośrednio do konta magazynu na poziomie `/HdiNotebooks/mynotebook1.ipynb`, Notes jest również widoczne w aplikacji Jupyter.  Notesy pozostawać na koncie magazynu, nawet po tym klaster jest usuwany.

> [!NOTE]  
> Klastry HDInsight przy użyciu usługi Azure Data Lake Storage jako magazynu domyślnego nie należy przechowywać notesów w skojarzonego magazynu.

Sposób notesów są zapisywane na koncie magazynu jest zgodny z [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Jeśli więc użytkownik SSH w klastrze, w których można użyć pliku poleceń zarządzania, jak pokazano w poniższym fragmencie kodu:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Niezależnie od tego, czy klaster używa usługi Azure Storage lub Azure Data Lake Storage jako domyślne konto magazynu, notesów są także zapisywane na głównym węzłem klastra na `/var/lib/jupyter`.

## <a name="supported-browser"></a>Obsługiwana przeglądarka

Notesy Jupyter w klastrach HDInsight Spark są obsługiwane tylko w przeglądarce Google Chrome.

## <a name="feedback"></a>Opinia

Nowe jądra są dostępne w zmieniające się etap i będzie dla dorosłych, wraz z upływem czasu. Może to także oznaczać, że interfejsów API można zmienić tych jądra dla dorosłych. Będziemy wdzięczni za opinię, która się podczas korzystania z tych nowych jądra. Jest to przydatne w kształtowaniu ostatecznej wersji tych jądra. Możesz pozostawić komentarzy/opinii w ramach **opinii** sekcji w dolnej części tego artykułu.

## <a name="seealso"></a>Zobacz też
* [Omówienie: Platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Apache Spark przy użyciu Power BI: Interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika witryny sieci Web przy użyciu platformy Apache Spark w HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji platformy Apache Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](apache-spark-zeppelin-notebook.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
