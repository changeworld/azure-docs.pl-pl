---
title: Rozwiązywanie problemów przy użyciu klastra Apache Spark w usłudze Azure HDInsight
description: Więcej informacji na temat problemów związanych z klastrami Apache Spark w usłudze Azure HDInsight oraz sposób obejścia tych.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: a2cb862102462d00822686de7a273c013a164bde
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39616959"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Znane problemy dotyczące klastra Apache Spark w HDInsight

W tym dokumencie przechowuje informacje o znanych problemów w publicznej wersji zapoznawczej platformy HDInsight Spark.  

## <a name="livy-leaks-interactive-session"></a>Usługi Livy przecieków interaktywnej sesji
Gdy usługi Livy zostanie uruchomiony ponownie (od Ambari lub z powodu ponownego uruchomienia maszyny wirtualnej węzłem głównym 0) przy użyciu interaktywnej sesji wciąż jest aktywny, następuje przeciek sesję zadania interaktywnego. Co w efekcie nowe zadania mogą zostać zatrzymane w stanie zaakceptowane.

**Środki zaradcze:**

Aby obejść ten problem, należy użyć następującej procedury:

1. SSH do węzła głównego. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom następujące polecenie, aby znaleźć identyfikatory aplikacji interaktywnych zadania pracy za pomocą usługi Livy. 
   
        yarn application –list
   
    Jeśli zadania zostały uruchomione przy użyciu usługi Livy interaktywnych sesji z nie jawnej nazwy określone domyślne nazwy zadania zostaną usługi Livy. Dla tej sesji usługi Livy uruchomione przez notesu programu Jupyter Nazwa zadania rozpoczyna się od remotesparkmagics_ *. 
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

Zamiast tego należy użyć łącznika Spark HBase. Aby uzyskać instrukcje, zobacz [sposób korzystania z łącznika Spark HBase](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

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

**Objaśnienie:**

Dzieje się tak, ponieważ po uruchomieniu pierwszej komórki kodu. W tle spowoduje to zainicjowanie konfiguracji sesji i platformy Spark, SQL i konteksty Hive są ustawiane. Po tych kontekstach są ustawione, pierwsza instrukcja jest uruchamiany i temu wrażenie instrukcji zajęło dużo czasu.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Podczas tworzenia sesji, limit czasu notesu programu Jupyter
Gdy klaster Spark jest za mało zasobów, Spark i PySpark jądra w notesie Jupyter będzie limit czasu próby utworzenia sesji. 

**Środki zaradcze:** 

1. Zwolnij niektórych zasobów w klastrze Spark przez:
   
   * Zatrzymywanie innych notesy platformy Spark, przechodząc do menu Close and Halt lub klikając zamknięcia w Eksploratorze notesu.
   * Zatrzymywanie inne aplikacje platformy Spark z usługi YARN.
2. Uruchom ponownie Notes, którą chcesz uruchomić. Za mało zasobów powinien być dostępny w celu utworzenia sesji teraz.

## <a name="see-also"></a>Zobacz także
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
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

