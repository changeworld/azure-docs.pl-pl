---
title: Jądra dla notesu Jupyter w klastrze Spark w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Więcej informacji na temat jądra PySpark, PySpark3 i Spark dla notesu Jupyter dostępne z klastrami Spark w usłudze Azure HDInsight.
keywords: notesu jupyter na spark, jupyter spark
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0719e503-ee6d-41ac-b37e-3d77db8b121b
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: nitinme
ms.openlocfilehash: 58a0bf27109af3131bd102fd43e9367d267525f3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31521538"
---
# <a name="kernels-for-jupyter-notebook-on-spark-clusters-in-azure-hdinsight"></a>Jądra dla notesu Jupyter w klastrze Spark w usłudze Azure HDInsight 

Klastry HDInsight Spark zapewniają jądra, których można używać do testowania aplikacji z notesu Jupyter w Spark. Jądra to program, który uruchamia i interpretuje kodu. Są trzy jądra:

- **PySpark** — dla aplikacji napisanych w Python2
- **PySpark3** — dla aplikacji napisanych w Python3
- **Platforma Spark** — dla aplikacji napisanych w języku Scala

W tym artykule Dowiedz się jak używać tych jądra i korzystanie z nich.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Tworzenie notesu Jupyter w usłudze HDInsight Spark

1. Z [portalu Azure](https://portal.azure.com/), otwórz klastra.  Zobacz [klastrów listy i Pokaż](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters) dla instrukcji. Klaster jest otwarty w nowym bloku portalu.

2. Z **szybkie linki** kliknij **klastra pulpity nawigacyjne** otworzyć **klastra pulpity nawigacyjne** bloku.  Jeśli nie widzisz **szybkie linki**, kliknij przycisk **omówienie** z menu po lewej stronie w bloku.

    ![Notesu Jupyter na Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "notesu Jupyter na Spark") 

3. Kliknij przycisk **notesu Jupyter**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.
   
   > [!NOTE]
   > Można również przejść notesu Jupyter w klastrze Spark, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

3. Kliknij przycisk **nowy**, a następnie kliknij przycisk **Pyspark**, **PySpark3**, lub **Spark** do tworzenie notesu. Użyj jądra Spark Scala aplikacji jądra PySpark Python2 aplikacji i PySpark3 jądra dla Python3 aplikacji.
   
    ![Jądra dla notesu Jupyter na Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "jądra dla notesu Jupyter na Spark") 

4. Otwiera notes o jądrze wybranych.

## <a name="benefits-of-using-the-kernels"></a>Zalety używania jądra

Poniżej przedstawiono kilka korzyści z notesu Jupyter w klastrze Spark w usłudze HDInsight przy użyciu nowego jądra.

- **Ustawienie wstępne kontekstów**. Z **PySpark**, **PySpark3**, lub **Spark** jądra, nie należy jawnie ustawić konteksty Spark i Hive, przed rozpoczęciem pracy z aplikacjami. Są one dostępne domyślnie. Konteksty te są:
   
   * **sc** — Spark kontekstu
   * **Element sqlContext** — dla kontekstu gałęzi
   
   Tak nie trzeba uruchomić instrukcje, takie jak następujące polecenie, aby ustawić kontekstów:
   
          sc = SparkContext('yarn-client')
          sqlContext = HiveContext(sc)
   
   Zamiast tego możesz bezpośrednio użyć predefiniowanych kontekstach w aplikacji.

- **Komórki poleceń magicznych**. Jądro PySpark zawiera kilka wstępnie zdefiniowanych "poleceń magicznych", które są specjalne polecenia, które można wywoływać z `%%` (na przykład `%%MAGIC` <args>). Polecenie magic musi być pierwsze słowo w komórce kodu i mogą zostać w wielu wierszach zawartości. Magic word powinna być pierwsze słowo w komórce. Dodawanie czegokolwiek przed magic, nawet komentarzy powoduje błąd.     Aby uzyskać więcej informacji dotyczących poleceń magicznych, zobacz [tutaj](http://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    W poniższej tabeli wymieniono różne poleceń magicznych dostępnych za pośrednictwem jądra.

   | Magiczna | Przykład | Opis |
   | --- | --- | --- |
   | pomoc |`%%help` |Generuje spis wszystkich dostępnych poleceń magicznych przykład i opis |
   | Informacje o |`%%info` |Dane wyjściowe informacji o sesji dla bieżącej punktu końcowego programu Livy |
   | konfiguruj |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfiguruje parametry w celu utworzenia sesji. Flagi force (-f) jest wymagane, jeśli sesja została już utworzona, który zapewnia, że sesja jest porzucona i utworzona ponownie. Przyjrzyj się [/sessions POST Livy w treści żądania](https://github.com/cloudera/livy#request-body) listę prawidłowych parametrów. Parametry muszą być przekazywane w postaci ciągu JSON i musi być w następnym wierszu po magic, jak pokazano w przykładzie kolumny. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Wykonuje zapytanie Hive względem element sqlContext. Jeśli `-o` parametr jest przekazywany, w wyniku zapytania jest trwały %% lokalny kontekst Python jako [Pandas](http://pandas.pydata.org/) dataframe. |
   | lokalne |`%%local`<br>`a=1` |Cały kod w kolejnych wierszy jest wykonywana lokalnie. Kod musi być prawidłowym kodem Python2 nawet niezależnie od jądra, którego używasz. Tak, nawet w przypadku wybrania **PySpark3** lub **Spark** jądra podczas tworzenia notesu, jeśli używasz `%%local` magic w komórce, tej komórki musi mieć tylko prawidłowy kod Python2... |
   | dzienniki |`%%logs` |Dane wyjściowe dzienników dla bieżącej sesji programu Livy. |
   | usuwanie |`%%delete -f -s <session number>` |Usuwa określonej sesji bieżącego Livy punktu końcowego. Nie można usunąć sesji, która jest inicjowane dla samej siebie jądra. |
   | oczyszczanie |`%%cleanup -f` |Usuwa wszystkich sesji dla bieżącej Livy punktu końcowego tego notesu sesji. Flagi force -f jest obowiązkowe. |

   > [!NOTE]
   > Oprócz poleceń magicznych dodane przez jądro PySpark, można również użyć [wbudowanych IPython poleceń magicznych](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), takie jak `%%sh`. Można użyć `%%sh` magic do uruchamiania skryptów i blok kodu na headnode klastra.
   >
   >
2. **Automatyczna wizualizacja**. **Pyspark** jądra automatycznie wizualizuje dane wyjściowe zapytań Hive i SQL. Można wybrać kilka różnych typów wizualizacji, łącznie z tabeli, kołowego, wiersza, obszar, paska.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametry są obsługiwane z %% sql magic
`%%sql` Magic obsługuje parametry, których można użyć do kontrolowania rodzaj wyjścia, który jest wyświetlany podczas uruchamiania kwerend. W poniższej tabeli przedstawiono dane wyjściowe.

| Parametr | Przykład | Opis |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Użyj tego parametru, aby trwale w wyniku zapytania, %% lokalny kontekst Python, jako [Pandas](http://pandas.pydata.org/) dataframe. Nazwa zmiennej dataframe jest nazwę zmiennej, które określisz. |
| -q |`-q` |Umożliwia wyłączanie wizualizacje komórki. Jeśli nie chcesz automatycznie wizualizacji zawartość komórki i po prostu chcesz przechwyceniem go jako dataframe, a następnie użyj `-q -o <VARIABLE>`. Jeśli chcesz wyłączyć wizualizacje bez Przechwytywanie wyników (na przykład do uruchomienia zapytania SQL, tak samo, jak `CREATE TABLE` instrukcji), użyj `-q` bez określania `-o` argumentu. |
| -m |`-m <METHOD>` |Gdzie **— metoda** jest **zająć** lub **próbki** (domyślnie jest **zająć**). Jeśli metoda jest **zająć**, jądra wybiera elementy znajdujące się u góry zestawu danych wyników określony przez maksymalna liczba wierszy (opisane w dalszej części tej tabeli). Jeśli metoda jest **próbki**, jądra losowo przykłady elementy zestawu danych zgodnie z `-r` parametru opisane dalej w tej tabeli. |
| -r |`-r <FRACTION>` |W tym miejscu **UŁAMEK** jest liczba zmiennoprzecinkowa od 0,0 do 1,0. Jeśli metoda próbki dla zapytania SQL jest `sample`, a następnie jądra losowo przykłady określona część elementy zestawu wyników dla należy. Na przykład, jeśli uruchomienie zapytania SQL z argumentami `-m sample -r 0.01`, następnie losowo próbkowanych 1% wierszy wyniku. |
| -n |`-n <MAXROWS>` |**Maksymalna liczba wierszy** jest wartością całkowitą. Jądro ogranicza liczbę wierszy danych wyjściowych do **maksymalna liczba wierszy**. Jeśli **maksymalna liczba wierszy** jest liczbą ujemną, takich jak **-1**, a następnie nie jest ograniczona liczba wierszy w zestawie wyników. |

**Przykład:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Instrukcja powyżej wykonuje następujące czynności:

* Wybiera wszystkie rekordy z **hivesampletable**.
* Ponieważ używamy - q, wyłącza automatyczne wizualizacji.
* Ponieważ używamy `-m sample -r 0.1 -n 500` losowo przykłady 10% wierszy w hivesampletable i ogranicza rozmiar zestawu wyników do 500 wierszy.
* Ponadto ponieważ użyliśmy `-o query2` zapisuje dane wyjściowe do dataframe, nazywany **kwerenda2**.

## <a name="considerations-while-using-the-new-kernels"></a>Zagadnienia dotyczące podczas korzystania z nowego jądra

Niezależnie od jądra używasz, pozostawiając notesów systemem wykorzystuje zasoby klastra.  Z tych jądra ponieważ kontekstów są zdefiniowane, po prostu Kończenie notebooki nie kill kontekstu i dlatego zasobów klastra nadal będzie używana. Dobrym rozwiązaniem jest użycie **zamknąć i zatrzymuje** opcji z notesu **pliku** menu po zakończeniu za pomocą notesu, co kasuje kontekstu i kończy działanie notesu.     

## <a name="show-me-some-examples"></a>Pokaż niektóre przykłady

Po otwarciu notesu Jupyter wyświetlany dwa foldery dostępna na poziomie głównym.

* **PySpark** znajduje się w nim notesów próbki, korzystających z nowej **Python** jądra.
* **Scala** znajduje się w nim notesów próbki, korzystających z nowej **Spark** jądra.

Możesz otworzyć **00 — [odczytu w pierwszej kolejności] funkcje jądra Magic Spark** notesu z **PySpark** lub **Spark** folderu, aby dowiedzieć się więcej o różnych poleceń magicznych dostępnych. Umożliwia także dostępne notesów próbki w dwóch folderach informacje na temat osiągnięcia różnych scenariuszy za pomocą notesów Jupyter z klastrami HDInsight Spark.

## <a name="where-are-the-notebooks-stored"></a>Gdzie są przechowywane notebooki?

Jeśli klaster używa magazynu Azure jako domyślne konto magazynu, notesy Jupyter są zapisywane do konta magazynu w ramach **/HdiNotebooks** folderu.  Notesów, pliki tekstowe i folderów tworzonych z wewnątrz Jupyter są dostępne z konta magazynu.  Na przykład, jeśli używasz Jupyter do utworzenia folderu **MójFolder** i notebook **myfolder/mynotebook.ipynb**, można uzyskać dostępu do tego notesu w `/HdiNotebooks/myfolder/mynotebook.ipynb` w ramach konta magazynu.  Występuje również sytuacja odwrotna ma wartość true, to znaczy bezpośrednio do swojego konta magazynu w przypadku przekazywania Notes `/HdiNotebooks/mynotebook1.ipynb`, notesu jest również widoczna w aplikacji Jupyter.  Notesów pozostają na koncie magazynu, nawet po usunięciu klastra.

> [!NOTE]
> Klastry HDInsight z usługi Azure Data Lake Store jako domyślny magazyn nie należy przechowywać notesów w powiązanym magazynie.
>

Sposób notesów są zapisywane na koncie magazynu jest zgodny z systemem plików HDFS. Tak więc, jeśli użytkownik SSH do klastra, których można użyć pliku polecenia zarządzania jak pokazano w poniższy fragment kodu:

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Niezależnie od tego, czy klaster używa magazynu Azure lub usługi Azure Data Lake Store jako domyślne konto magazynu, notebooki zostały także zapisane w headnode klastra na `/var/lib/jupyter`.

## <a name="supported-browser"></a>Obsługiwana przeglądarka

Notesów Jupyter w klastrze Spark w usłudze HDInsight są obsługiwane tylko w Google Chrome.

## <a name="feedback"></a>Opinia
Nowe jądra znajdują się w rozwijającymi etap i będzie dojrzałych wraz z upływem czasu. Może to również oznaczać, że interfejsy API można zmienić, ponieważ te jądra dla dorosłych. Czy Dziękujemy za opinię, czy masz podczas korzystania z tych nowych jądra. Jest to przydatne w tworzeniu ostatecznej wersji tych jądra. Możesz pozostawić komentarzy/opinii w obszarze **komentarze** sekcji w dolnej części tego artykułu.

## <a name="seealso"></a>Zobacz też
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
