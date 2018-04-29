---
title: Tworzenie aplikacji Scala do uruchamiania na klastry Spark - Azure HDInsight | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji Spark napisane w języku Scala z Apache Maven jako system kompilacji i istniejące archetype Maven dla Scala podał IntelliJ IDEA.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: d64d60112641992a5a56ee5d129e7cbe754bde9d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-scala-maven-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Tworzenie aplikacji Scala Maven, aby uruchomić w klastrze Apache Spark w usłudze HDInsight

Informacje o sposobie tworzenia aplikacji Spark napisanych w języku Scala za pomocą programu Maven z ROZWIĄZANIEM IntelliJ. Artykuł używa Apache Maven jako system kompilacji i rozpoczyna się od istniejącej archetype Maven dla Scala podał IntelliJ IDEA.  Tworzenie aplikacji Scala w IntelliJ IDEA obejmuje następujące kroki:

* Używanie programu Maven jako system kompilacji.
* Zaktualizuj plik projektu Object Model (POM) można rozpoznać zależności modułu Spark.
* Pisanie aplikacji w języku Scala.
* Generuj plik jar, który może być przesyłany do klastrów HDInsight Spark.
* Uruchom aplikację w klastrze Spark przy użyciu programu Livy.

> [!NOTE]
> Usługa HDInsight zapewnia również narzędzie wtyczkę IntelliJ IDEA do jej obsługi ułatwiają proces tworzenia i przesyłanie aplikacji do klastra Spark w usłudze HDInsight w systemie Linux. Aby uzyskać więcej informacji, zobacz [Użyj HDInsight Tools Plugin for IntelliJ IDEA tworzenie i przesyłanie aplikacji Spark](apache-spark-intellij-tool-plugin.md).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development kit. Możesz zainstalować ją z [tutaj](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Java IDE. W tym artykule wykorzystano IntelliJ IDEA 15.0.1. Możesz zainstalować ją z [tutaj](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalowanie wtyczki Scala for IntelliJ IDEA
Jeśli instalacja IntelliJ IDEA nie Monituj o włączenie wtyczki Scala, uruchom IntelliJ IDEA i kolejnych kroków, aby zainstalować dodatek plug-in:

1. Uruchom IntelliJ IDEA i na ekranie powitalnym kliknij **Konfiguruj** , a następnie kliknij przycisk **wtyczek**.
   
    ![Włączanie wtyczki scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
2. Na następnym ekranie kliknij **JetBrains zainstalować dodatek** z lewym dolnym rogu. W **Przeglądaj wtyczek JetBrains** okno dialogowe zostanie otwarte, wyszukaj Scala, a następnie kliknij przycisk **zainstalować**.
   
    ![Instalowanie wtyczki scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
3. Po zainstalowaniu dodatku plug-in pomyślnie, kliknij przycisk **przycisk Uruchom ponownie IntelliJ IDEA** ponowne uruchomienie IDE.

## <a name="create-a-standalone-scala-project"></a>Tworzenie projektu Scala autonomiczny
1. Uruchom IntelliJ IDEA i Utwórz nowy projekt. W oknie dialogowym projektu nowe wybierz następujące opcje, a następnie kliknij przycisk **dalej**.
   
    ![Utwórz projekt Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Wybierz **Maven** jako typ projektu.
   * Określ **projekt zestawu SDK**. Kliknij przycisk Nowy i przejdź do katalogu instalacyjnego programu Java zwykle `C:\Program Files\Java\jdk1.8.0_66`.
   * Wybierz **Utwórz z archetype** opcji.
   * Wybierz z listy archetypes **org.scala tools.archetypes:scala-archetype — prosty**. Spowoduje to utworzenie struktury katalogów prawo i pobrać zależności wymagane domyślne można zapisać Scala program.
2. Podaj odpowiednie wartości dla **GroupId**, **ArtifactId**, i **wersji**. Kliknij przycisk **Dalej**.
3. W oknie dialogowym dalej, której określić katalog macierzysty Maven i inne ustawienia użytkownika, zaakceptuj ustawienia domyślne, a następnie kliknij przycisk **dalej**.
4. W oknie dialogowym ostatnich, określ nazwę projektu i lokalizację, a następnie kliknij przycisk **Zakończ**.
5. Usuń **MySpec.Scala** o plików **src\test\scala\com\microsoft\spark\example**. Nie trzeba to dla aplikacji.
6. W razie potrzeby zmień nazwę domyślnych plików źródła i testowania. W lewym okienku w IntelliJ IDEA, przejdź do **src\main\scala\com.microsoft.spark.example**. Kliknij prawym przyciskiem myszy **App.scala**, kliknij przycisk **Refaktoryzuj**, kliknij przycisk Zmień nazwę pliku, a w oknie dialogowym podaj nową nazwę dla aplikacji, a następnie kliknij przycisk **Refaktoryzuj**.
   
    ![Zmień nazwy plików](./media/apache-spark-create-standalone-application/rename-scala-files.png)  
7. W kolejnych krokach spowoduje zaktualizowanie pom.xml do Definiowanie zależności dla aplikacji Spark Scala. Te zależności do pobrania i rozwiązany automatycznie należy odpowiednio skonfigurować Maven.
   
    ![Skonfigurować automatyczne pobieranie Maven](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. Z **pliku** menu, kliknij przycisk **ustawienia**.
   2. W **ustawienia** okna dialogowego wybierz opcję **, wykonanie, wdrożenie kompilacji** > **Build Tools** > **Maven** > **importowanie**.
   3. Wybierz opcję **Import Maven projektów automatycznie**.
   4. Kliknij przycisk **Zastosuj**, a następnie kliknij przycisk **OK**.
8. Zaktualizuj Scala pliku źródłowego, aby uwzględnić kod aplikacji. Otwórz i Zastąp istniejące przykładowy kod następujący kod i Zapisz zmiany. Ten kod odczytuje dane z HVAC.csv (dostępne na wszystkich klastrach HDInsight Spark), pobiera wiersze z tylko jedną cyfrę w kolumnie szóstego i zapisuje dane wyjściowe do **/HVACOut** w obszarze domyślnego kontenera magazynu dla klastra.
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
9. Zaktualizuj pom.xml.
   
   1. W ramach `<project>\<properties>` Dodaj następujący kod:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. W ramach `<project>\<dependencies>` Dodaj następujący kod:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Zapisz zmiany w pom.xml.
10. Utwórz plik JAR. IntelliJ IDEA umożliwia tworzenie JAR jako artefaktu projektu. Wykonaj poniższe kroki.
    
    1. Z **pliku** menu, kliknij przycisk **struktury projektu**.
    2. W **struktury projektu** okno dialogowe, kliknij przycisk **artefakty** , a następnie kliknij znak plus. W wyświetlonym oknie dialogowym, kliknij polecenie **JAR**, a następnie kliknij przycisk **z modułów z zależnościami**.
       
        ![Utwórz JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. W **utworzyć JAR z modułów** okna dialogowego, kliknij przycisk wielokropka (![wielokropka](./media/apache-spark-create-standalone-application/ellipsis.png) ) przed **klasy głównym**.
    4. W **Wybierz klasy głównym** okno dialogowe, wybierz klasę, która jest wyświetlany domyślnie, a następnie kliknij przycisk **OK**.
       
        ![Utwórz JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. W **utworzyć JAR z modułów** okna dialogowego upewnij się, że opcja **Wyodrębnij do docelowego JAR** jest zaznaczone, a następnie kliknij przycisk **OK**. Spowoduje to utworzenie jednego JAR z wszystkie zależności.
       
        ![Utwórz JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. Karta Układ danych wyjściowych zawiera listę wszystkich słoików, które są częścią projektu Maven. Można wybrać i usunąć te, na którym aplikacja Scala nie ma żadnych zależności bezpośrednich. Dla aplikacji jest tworzone w tym miejscu, należy usunąć wszystkie oprócz ostatnią (**danych wyjściowych kompilacji SparkSimpleApp**). Wybierz słoików usunąć, a następnie kliknij przycisk **usunąć** ikony.
       
        ![Utwórz JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Upewnij się, że **kompilacji w upewnij** pole jest zaznaczone, co zapewnia, że jar jest tworzony za każdym razem, gdy projekt jest utworzony lub zaktualizowany. Kliknij przycisk **zastosować** , a następnie **OK**.
    7. Na pasku menu kliknij **kompilacji**, a następnie kliknij przycisk **projektu należy**. Możesz również kliknąć **artefaktów kompilacji** utworzyć jar. Dane wyjściowe jar utworzony na podstawie **\out\artifacts**.
       
        ![Utwórz JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Uruchom aplikację w klastrze Spark
Aby uruchomić aplikację w klastrze, wykonaj następujące czynności:

* **Skopiuj jar aplikacji do obiektu blob magazynu Azure** skojarzony z klastrem. Można użyć [ **AzCopy**](../../storage/common/storage-use-azcopy.md), narzędzie wiersza polecenia, aby to zrobić. Istnieje wiele innych klientów, jak również służy do przekazywania danych. Można znaleźć więcej informacji na temat je pod adresem [przekazywanie danych dotyczących zadań Hadoop w usłudze HDInsight](../hdinsight-upload-data.md).
* **Użyj programu Livy można przesłać zadania aplikacji zdalnie** do klastra Spark. Klastry Spark w usłudze HDInsight obejmuje Livy ujawniającym punkty końcowe REST do zdalnego przesyłania zadań Spark. Aby uzyskać więcej informacji, zobacz [zadania przesyłania Spark klastrów zdalnie przy użyciu programu Livy z platformy Spark w usłudze HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Następny krok

W tym artykule przedstawiono sposób tworzenia aplikacji Spark scala. Przejdź do następnego artykuł, aby dowiedzieć się, jak uruchomić tę aplikację w klastrze Spark w usłudze HDInsight przy użyciu programu Livy.

> [!div class="nextstepaction"]
>[Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

