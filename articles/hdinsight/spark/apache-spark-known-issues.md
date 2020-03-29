---
title: Rozwiązywanie problemów z klastrem Apache Spark w usłudze Azure HDInsight
description: Dowiedz się więcej o problemach związanych z klastrami Platformy Spark apache w usłudze Azure HDInsight i o tym, jak je obejść.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.author: hrasheed
ms.openlocfilehash: 2c153d818136c5d8804dae72004dfaf17fd1bf7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494533"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Znane problemy dla klastra Apache Spark w programie HDInsight

Ten dokument śledzi wszystkie znane problemy dla publicznej wersji zapoznawczej platformy HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy wycieki interaktywnej sesji
Po ponownym uruchomieniu [Apache Livy](https://livy.incubator.apache.org/) (z [Apache Ambari](https://ambari.apache.org/) lub z powodu restartu maszyny wirtualnej headnode 0) z interakcyjną sesją wciąż żywą, wycieka interaktywna sesja zadania. W rezultacie nowe zadania mogą zostać zablokowane w stanie Zaakceptowane.

**Łagodzenia:**

Aby obejść ten problem, użyj następującej procedury:

1. Ssh w headnode. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom następujące polecenie, aby znaleźć identyfikatory aplikacji interaktywnych zadań uruchomionych za pośrednictwem livy.

        yarn application –list

    Domyślne nazwy zadań będą Livy, jeśli zadania zostały uruchomione z livy sesji interaktywnej bez jawnych nazw określonych. W przypadku sesji Livy rozpoczętej przez [Jupyter](https://jupyter.org/) `remotesparkmagics_*`Notebook nazwa zadania zaczyna się od .

3. Uruchom następujące polecenie, aby zabić te zadania.

        yarn application –kill <Application ID>

Nowe zadania zaczynają działać.

## <a name="spark-history-server-not-started"></a>Nie uruchomiono serwera spark History Server
Serwer spark History Server nie jest uruchamiany automatycznie po utworzeniu klastra.  

**Łagodzenia:**

Ręcznie uruchom serwer historii z Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problem z uprawnieniami w katalogu dziennika platformy Spark
hdiuser otrzymuje następujący błąd podczas przesyłania zadania za pomocą spark-submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

I nie jest zapisywany żaden dziennik sterownika.

**Łagodzenia:**

1. Dodaj hdiuser do grupy Hadoop.
2. Podaj 777 uprawnień do /var/log/spark po utworzeniu klastra.
3. Zaktualizuj lokalizację dziennika iskry przy użyciu Ambari, aby był katalogiem z uprawnieniami 777.  
4. Uruchom iskrę-submit jako sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Złącze Spark-Phoenix nie jest obsługiwane

Klastry platformy HDInsight Spark nie obsługują łącznika Spark-Phoenix.

**Łagodzenia:**

Zamiast tego należy użyć łącznika Spark-HBase. Aby uzyskać instrukcje, zobacz [Jak używać łącznika Spark-HBase](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemy związane z notebookami Jupyter

Oto kilka znanych problemów związanych z notebookami Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notesy ze znakami innymi niż ASCII w nazwach plików

Nie należy używać znaków innych niż ASCII w nazwach plików notesu Jupyter. Jeśli spróbujesz przekazać plik za pośrednictwem interfejsu użytkownika Jupyter, który ma nazwę pliku innego niż ASCII, nie powiedzie się bez żadnego komunikatu o błędzie. Jupyter nie pozwala przesłać pliku, ale nie zgłasza widoczny błąd albo.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Błąd podczas ładowania notesów o większych rozmiarach

Podczas ładowania notesów o większym rozmiarze może pojawić się błąd. **`Error loading notebook`**  

**Łagodzenia:**

Jeśli ten błąd zostanie wyświetlony, nie oznacza to, że dane są uszkodzone lub utracone.  Notesy są nadal na `/var/lib/jupyter`dysku w , i można SSH do klastra, aby uzyskać do nich dostęp. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Po nawiązaniu połączenia z klastrem przy użyciu protokołu SSH można skopiować notesy z klastra na komputer lokalny (przy użyciu protokołu SCP lub WinSCP) jako kopię zapasową, aby zapobiec utracie ważnych danych w notesie. Następnie możesz tunel SSH do headnode w porcie 8001, aby uzyskać dostęp do Jupyter bez przechodzenia przez bramę.  W tym miejscu można wyczyścić dane wyjściowe notesu i ponownie je zasłonąć, aby zminimalizować rozmiar notebooka.

Aby zapobiec wystąpieniu tego błędu w przyszłości, należy postępować zgodnie z najlepszymi rozwiązaniami:

* Ważne jest, aby zachować mały rozmiar notebooka. Wszelkie dane wyjściowe z zadań platformy Spark, które są wysyłane z powrotem do Jupyter jest zachowywany w notesie.  Jest to najlepsza praktyka z Jupyter `.collect()` w ogóle, aby uniknąć uruchamiania na dużych RDD lub dataframes; Zamiast tego, jeśli chcesz zajrzeć do zawartości RDD, `.take()` `.sample()` należy rozważyć uruchomienie lub tak, aby dane wyjściowe nie stają się zbyt duże.
* Ponadto podczas zapisywania notesu wyczyść wszystkie komórki wyjściowe, aby zmniejszyć rozmiar.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Początkowe uruchamianie notesu trwa dłużej niż oczekiwano

Pierwsze instrukcje kodu w notatniku Jupyter przy użyciu magii Spark może potrwać ponad minutę.  

**Wyjaśnienie:**

Dzieje się tak, ponieważ po uruchomieniu pierwszej komórki kodu. W tle inicjuje konfigurację sesji i spark, SQL i hive konteksty są ustawione. Po ustawieniu tych kontekstów, pierwsza instrukcja jest uruchamiana i daje wrażenie, że instrukcja miała dużo czasu, aby zakończyć.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Limit czasu notesu Jupyter podczas tworzenia sesji

Gdy klaster platformy Spark nie ma zasobów, jądra Platformy Spark i PySpark w notesie Jupytera przestają czas na próbę utworzenia sesji.

**Czynniki:**

1. Zwolnić niektóre zasoby w klastrze platformy Spark przez:

   * Zatrzymywanie innych notesów platformy Spark przez przejście do menu Zamknij i Wstrzymanie lub kliknięcie przycisku Zamknij w Eksploratorze notesu.
   * Zatrzymywanie innych aplikacji Spark z YARN.

2. Uruchom ponownie notes, który próbował uruchomić. Wystarczającej ilości zasobów powinny być dostępne, aby utworzyć sesję teraz.

## <a name="see-also"></a>Zobacz też

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki HDInsight Tools dla IntelliJ IDEA do zdalnego debugowania aplikacji Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)