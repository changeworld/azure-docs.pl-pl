---
title: Rozwiązywanie problemów przy użyciu klastra Apache Spark w usłudze Azure HDInsight
description: Więcej informacji na temat problemów związanych z klastrami Apache Spark w usłudze Azure HDInsight oraz sposób obejścia tych.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 51a0ee6f2d928d79e60ca9976d7651c70867a41f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717597"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Znane problemy dotyczące klastra Apache Spark w HDInsight

W tym dokumencie przechowuje informacje o znanych problemów w publicznej wersji zapoznawczej platformy HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache, usługi Livy przecieków interaktywnej sesji
Gdy [Apache, usługi Livy](https://livy.incubator.apache.org/) powoduje ponowne uruchomienie (z [Apache Ambari](https://ambari.apache.org/) lub z powodu ponownego uruchomienia maszyny wirtualnej węzłem głównym 0) przy użyciu interaktywnej sesji wciąż jest aktywny, następuje przeciek sesję zadania interaktywnego. Co w efekcie nowe zadania mogą zostać zatrzymane w stanie zaakceptowane.

**Środki zaradcze:**

Aby obejść ten problem, należy użyć następującej procedury:

1. SSH do węzła głównego. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom następujące polecenie, aby znaleźć identyfikatory aplikacji interaktywnych zadania pracy za pomocą usługi Livy. 
   
        yarn application –list
   
    Jeśli zadania zostały uruchomione przy użyciu usługi Livy interaktywnych sesji z nie jawnej nazwy określone domyślne nazwy zadania zostaną usługi Livy. W sesji usługi Livy uruchomione przez [notesu programu Jupyter](https://jupyter.org/), rozpoczyna się remotesparkmagics_ * Nazwa zadania. 
3. Uruchom następujące polecenie, aby skasować te zadania. 
   
        yarn application –kill <Application ID>

Nowe zadania uruchomione. 

## <a name="spark-history-server-not-started"></a>Nie jest uruchomiony serwer historii platformy Spark
Serwer historii platformy Spark nie jest uruchomiane automatycznie po utworzeniu klastra.  

**Środki zaradcze:** 

Ręcznie uruchom serwer historii z systemu Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problem z uprawnieniami w katalogu dzienników platformy Spark
hdiuser pobiera następujący błąd podczas przesyłania zadania za pomocą polecenia skryptu spark-submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
I nie sterownik dziennik jest zapisywany. 

**Środki zaradcze:**

1. Dodaj hdiuser do grupy usługi Hadoop. 
2. Podaj uprawnienia 777 na /var/log/spark po utworzeniu klastra. 
3. Zaktualizuj lokalizację dziennika spark przy użyciu narzędzia Ambari do katalogu przy użyciu 777 uprawnień.  
4. Uruchom skryptu spark-submit jako "sudo".  

## <a name="spark-phoenix-connector-is-not-supported"></a>Łącznik Spark Phoenix nie jest obsługiwany

Klastry HDInsight Spark nie obsługują łącznik Spark Phoenix.

**Środki zaradcze:**

Zamiast tego należy użyć łącznika Spark HBase. Aby uzyskać instrukcje, zobacz [sposób korzystania z łącznika Spark HBase](https://web.archive.org/web/20190112153146/ https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemy związane z notesów programu Jupyter
Poniżej przedstawiono niektóre znane problemy związane z notesów programu Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notesy znaki spoza zestawu ASCII w nazwach plików
Nie używaj znaki spoza zestawu ASCII w nazwach plików notesu programu Jupyter. Jeśli zostanie podjęta próba przekazania pliku przy użyciu interfejsu użytkownika programu Jupyter, która ma parametr filename spoza zestawu ASCII, nie jest on bez żadnych błędów. Jupyter nie zezwala na przekazywanie pliku, ale nie zostanie zgłoszony błąd widoczne albo.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Wystąpił błąd podczas ładowania notesy o większych rozmiarach
Możesz napotkać błąd **`Error loading notebook`** podczas ładowania notesów, które są większe.  

**Środki zaradcze:**

Jeśli ten błąd nie oznacza to, że Twoje dane są uszkodzone lub zostały utracone.  Notesy nadal znajdują się na dysku w `/var/lib/jupyter`, i możesz SSH w klastrze do nich dostęp. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Po połączeniu się z klastrem przy użyciu protokołu SSH, można skopiować z notesów z klastra usługi na komputerze lokalnym (przy użyciu protokołu SCP lub WinSCP) do przechowywania kopii zapasowych, aby zapobiec utracie wszystkie ważne dane w notesie. Możesz następnie tunel SSH do Twojej węzeł główny na port 8001 uzyskiwać dostęp do aplikacji Jupyter bez przechodzenia przez bramy.  Z tego miejsca możesz wyczyścić dane wyjściowe notesu i ponownie zapisać go, aby zminimalizować rozmiar notesu.

Aby uniknąć tego błędu zachowaniom w przyszłości, należy wykonać kilka najlepszych zasad:

* Należy zachować mały rozmiar notesu. Wszelkie dane wyjściowe z zadań platformy Spark są wysyłane z powrotem do aplikacji Jupyter są utrwalane w notesie.  Ogólnie rzecz biorąc jest najlepszym rozwiązaniem w programie Jupyter w celu uniknięcia uruchomiona `.collect()` w dużych RDD firmy lub elementy dataframe; zamiast tego, jeśli chcesz obejrzeć zawartość RDD, należy wziąć pod uwagę uruchomiona `.take()` lub `.sample()` , tak aby dane wyjściowe nie uzyskać zbyt duży.
* Ponadto podczas zapisywania notesu wyczyść wszystkie dane wyjściowe komórek, aby zmniejszyć rozmiar.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Trwa dłużej niż oczekiwano, wstępnego uruchamiania notesu
Pierwsza instrukcja kodu w notesie Jupyter przy użyciu funkcji magic Spark może potrwać ponad minutę.  

**Explanation:**

Dzieje się tak, ponieważ po uruchomieniu pierwszej komórki kodu. W tle spowoduje to zainicjowanie konfiguracji sesji i platformy Spark, SQL i konteksty Hive są ustawiane. Po tych kontekstach są ustawione, pierwsza instrukcja jest uruchamiany i temu wrażenie instrukcji zajęło dużo czasu.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Podczas tworzenia sesji, limit czasu notesu programu Jupyter
Gdy klaster Spark jest za mało zasobów, Spark i PySpark jądra w notesie Jupyter będzie limit czasu próby utworzenia sesji. 

**Środki zaradcze:** 

1. Zwolnij niektórych zasobów w klastrze Spark przez:
   
   * Zatrzymywanie innych notesy platformy Spark, przechodząc do menu Close and Halt lub klikając zamknięcia w Eksploratorze notesu.
   * Zatrzymywanie inne aplikacje platformy Spark z usługi YARN.
2. Uruchom ponownie Notes, którą chcesz uruchomić. Za mało zasobów powinien być dostępny w celu utworzenia sesji teraz.

## <a name="see-also"></a>Zobacz także
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
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

