---
title: Rozwiązywanie problemów z klastra Apache Spark w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Więcej informacji na temat związane z klastrami Apache Spark w usłudze Azure HDInsight i sposobu obejścia tych problemów.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 664c97117de793209007843fa23c98f52c2b079d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Znane problemy dotyczące klastra Apache Spark w usłudze HDInsight

Ten dokument przechowuje informacje o znanych problemów w publicznej wersji zapoznawczej HDInsight Spark.  

## <a name="livy-leaks-interactive-session"></a>Livy przecieków sesja interaktywna
Gdy Livy jest uruchamiany (od systemu Ambari lub z powodu ponownego uruchomienia maszyny wirtualnej headnode 0) z nadal aktywna sesja interakcyjna, sesji interaktywne zadania jest przecieku. W rezultacie nowych miejsc pracy może być zablokowany w stanie zaakceptowane.

**Środki zaradcze:**

Aby obejść ten problem, należy użyć następującej procedury:

1. SSH do headnode. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom następujące polecenie, aby znaleźć identyfikatory aplikacji interaktywnych zadania uruchamiane za pośrednictwem programu Livy. 
   
        yarn application –list
   
    Domyślne nazwy zadania będzie Livy zadań została uruchomiona przy użyciu programu Livy interakcyjne sesji z nie określono jawnej nazwy. W sesji programu Livy uruchomione przez notesu Jupyter Nazwa zadania rozpoczyna się od remotesparkmagics_ *. 
3. Uruchom następujące polecenie, aby kill te zadania. 
   
        yarn application –kill <Application ID>

Nowe zadania uruchomione. 

## <a name="spark-history-server-not-started"></a>Platforma Spark historii serwer nie został uruchomiony
Platforma Spark historii serwera nie jest uruchamiana automatycznie po utworzeniu klastra.  

**Środki zaradcze:** 

Ręcznie uruchom serwer historii z narzędzia Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problem uprawnień w katalogu dzienników Spark
hdiuser pobiera następujący błąd podczas przesyłania zadania przy użyciu spark składać:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
I żaden sterownik dziennik jest zapisywany. 

**Środki zaradcze:**

1. Dodaj hdiuser do grupy usługi Hadoop. 
2. Podaj 777 uprawnienia na /var/log/spark po utworzeniu klastra. 
3. Zaktualizuj lokalizację dziennika spark przy użyciu narzędzia Ambari katalogiem 777 uprawnienia.  
4. Uruchom spark — przedstawia jako sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark Phoenix łącznik nie jest obsługiwany.

Klastry w iskrowym HDInsight nie obsługują złącza Spark Phoenix.

**Środki zaradcze:**

Zamiast tego należy użyć łącznika Spark HBase. Aby uzyskać instrukcje, zobacz [sposobu korzystania z łącznika Spark HBase](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemy związane z notesów Jupyter
Poniżej przedstawiono niektóre znane problemy związane z notesów Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notesów znaków innych niż ASCII w nazwach plików
Nie należy używać znaków spoza zestawu ASCII w nazwach plików notebook Jupyter. Jeśli podczas próby przekazania pliku za pośrednictwem interfejsu użytkownika Jupyter, który ma nazwę pliku spoza zestawu ASCII, nie powiedzie się bez żadnego komunikatu o błędzie. Jupyter nie zezwala na przekazywanie pliku, ale go nie zgłosiła błąd widoczne albo.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Wystąpił błąd podczas ładowania notesów o większych rozmiarach
Można napotkać błąd **`Error loading notebook`** podczas ładowania notebooki, które są większe.  

**Środki zaradcze:**

Jeśli ten błąd nie oznacza to, że dane są uszkodzone lub zostały utracone.  Notesy nadal znajdują się na dysku w `/var/lib/jupyter`, i może SSH w klastrze, aby uzyskiwać do nich dostęp. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Po połączeniu się z klastrem przy użyciu protokołu SSH, możesz skopiować notesy z klastra na komputerze lokalnym (przy użyciu protokołu SCP lub WinSCP) jako kopię zapasową, aby uniknąć utraty ważnych danych w notesie. Można następnie tunelu SSH w Twojej headnode 8001 dostęp bez pośrednictwa bramy Jupyter do portu.  Z tego miejsca możesz wyczyścić dane wyjściowe notesu i ponownie zapisać go, aby zminimalizować rozmiar notesu.

Aby uniknąć tego błędu w przyszłości w przyszłości, należy wykonać najlepsze rozwiązania:

* Należy zachować mały rozmiar notesu. Żadnych danych wyjściowych z zadaniami Spark, które są wysyłane do aplikacji Jupyter jest trwały notesu.  Ogólnie jest najlepszym rozwiązaniem z Jupyter w celu uniknięcia uruchomiona `.collect()` w dużych RDD firmy lub dataframes; zamiast tego, jeśli chcesz uzyskać wgląd w zawartość RDD, należy wziąć pod uwagę uruchomiona `.take()` lub `.sample()` , tak aby dane wyjściowe nie uzyskać zbyt duży.
* Ponadto podczas zapisywania Notes wyczyść wszystkie dane wyjściowe komórek, aby zmniejszyć rozmiar.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Notesu uruchamiania początkowego trwa dłużej, niż oczekiwano
Pierwsza instrukcja kodu w notesu Jupyter przy użyciu Spark magic może zająć więcej niż kilka minut.  

**Wyjaśnienie:**

Dzieje się tak, ponieważ po uruchomieniu pierwszej komórki kodu. W tle to inicjuje konfigurację sesji, Spark SQL, i są ustawione kontekstów Hive. Po kontekstów te są ustawione, pierwsza instrukcja jest uruchamiane, co powoduje wrażenie który instrukcji przez długi czas do ukończenia.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Limit czasu notesu Jupyter w tworzenia sesji
Gdy klaster Spark jest brak zasobów, limit czasu próby utworzenia sesji będzie jądra Spark i PySpark w notesie Jupyter. 

**Środki zaradcze:** 

1. Zwolnij na nim w klastrze Spark przez niektóre zasoby:
   
   * Zatrzymywanie innych notesów Spark, przechodząc do menu Close and Halt lub kliknąć przycisk zamykania w Eksploratorze notesu.
   * Zatrzymywanie innych aplikacji Spark z YARN.
2. Uruchom ponownie notesu, który chcesz uruchomić. Za mało zasobów powinny być dostępne dla Ciebie do utworzenia sesji teraz.

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

