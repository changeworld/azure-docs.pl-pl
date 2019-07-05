---
title: Użyj zestaw narzędzi platformy Azure dla środowiska IntelliJ z Piaskownicą Hortonworks
description: Dowiedz się, jak używać narzędzi HDInsight w zestaw narzędzi platformy Azure dla środowiska IntelliJ z Piaskownicą Hortonworks.
keywords: narzędzia hadoop hive zapytania, intellij, piaskownicą hortonworks, zestaw narzędzi platformy azure dla środowiska intellij
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 5234835f5fbfd5617e6b0440ff38fa900051470a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450121"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Korzystaj z narzędzi HDInsight dla programu IntelliJ z Piaskownicą Hortonworks

Dowiedz się, jak narzędzia HDInsight Tools for IntelliJ umożliwia tworzenie aplikacji Apache Scala, a następnie przetestować aplikacje na [Piaskownicą Hortonworks](https://hortonworks.com/products/sandbox/) uruchomionych na danym komputerze. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) jest Java zintegrowanego środowiska programistycznego (IDE) do tworzenia oprogramowania. Po tworzenia i testowania swoich aplikacji na Piaskownicą Hortonworks, można przenieść aplikacje [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania w tym artykule, musi mieć następujące elementy:

- Hortonworks Data Platform (HDP) 2.4 na Piaskownicą Hortonworks uruchomiony na komputerze lokalnym. Aby skonfigurować HDP, zobacz [wprowadzenie do ekosystemu Apache Hadoop z piaskownicy usługi Hadoop na maszynie wirtualnej](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Narzędzia HDInsight Tools for IntelliJ zostało przetestowane tylko w przypadku HDP 2.4. Aby uzyskać HDP 2.4, rozwiń węzeł **archiwum piaskownica Hortonworks** na [witryny pobierania plików Piaskownicą Hortonworks](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) w wersji 1.8 lub nowszej](https://aka.ms/azure-jdks). Zestaw narzędzi platformy Azure dla środowiska IntelliJ wymaga zestawu JDK.

- [Wersja community środowiska IntelliJ IDEA](https://www.jetbrains.com/idea/download) z [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) wtyczki i [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) wtyczki. Narzędzia HDInsight Tools for IntelliJ jest dostępna w ramach zestawu narzędzi platformy Azure dla środowiska IntelliJ. 

Aby zainstalować wtyczkę:

  1. Otwórz środowisko IntelliJ IDEA.
  2. Na **powitalnej** wybierz opcję **Konfiguruj**, a następnie wybierz pozycję **wtyczek**.
  3. W lewym dolnym rogu, wybierz **JetBrains Zainstaluj wtyczkę**.
  4. Użyj funkcji wyszukiwania, aby wyszukać **Scala**, a następnie wybierz pozycję **zainstalować**.
  5. Aby ukończyć instalację, wybierz pozycję **ponownego uruchomienia rozwiązania IntelliJ IDEA**.
  6. Powtórz kroki 4 i 5, aby zainstalować **Azure Toolkit for IntelliJ**. Aby uzyskać więcej informacji, zobacz [zainstalować Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Tworzenie aplikacji Apache Spark Scala

W tej sekcji utworzysz przykładowy projekt Scala przy użyciu rozwiązania IntelliJ IDEA. W następnej sekcji opisano łączenie IntelliJ IDEA z Piaskownicą Hortonworks (emulatora) przed przesłaniem projektu.

1. Otwórz IntelliJ IDEA na komputerze. W **nowy projekt** okna dialogowego pole, wykonaj następujące kroki:

   1. Wybierz pozycję **HDInsight** > **Spark w usłudze HDInsight (Scala)** .
   2. W **narzędzia do kompilowania** listy, wybierz jedną z poniższych pozycji w oparciu o Twojego scenariusza:

      * **Narzędzie maven**: Kreator tworzenia projektu Scala pomocy technicznej.
      * **SBT**: Zarządzanie zależnościami oraz kompilowanie projektu Scala.

   ![Okno dialogowe Nowy projekt](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Wybierz opcję **Dalej**.
3. W ciągu następnych **nowy projekt** okna dialogowego pole, wykonaj następujące czynności:

   1. W **Nazwa projektu** wprowadź nazwę projektu.
   2. W **lokalizacja projektu** wprowadź lokalizację projektu.
   3. Obok pozycji **SDK projektu** listy rozwijanej wybierz **nowy**, wybierz opcję **JDK**, a następnie określ folder dla języka Java JDK w wersji 1.7 lub nowszej. Wybierz **Java 1.8** klastra Spark 2.x. Wybierz **środowiska Java 1.7** 1.x klastra Spark. Domyślna lokalizacja to C:\Program Files\Java\jdk1.8.x_xxx.
   4. W **wersji platformy Spark** listy rozwijanej, Kreator tworzenia projektu w języku Scala integruje się poprawna wersja zestawu SDK platformy Spark i Scala zestawu SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie użyto platformy Spark 1.6.2 (Scala 2.10.5). Upewnij się, że korzystają repozytorium oznaczone **Scala 2.10.x**. Nie należy używać repozytorium oznaczone Scala 2.11.x.
    
      ![Tworzenie środowiska IntelliJ Scala właściwości projektu](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Wybierz pozycję **Finish** (Zakończ).
5. Jeśli **projektu** widoku nie jest jeszcze otwarty, naciśnij **Alt + 1** aby go otworzyć.
6. W **Eksplorator projektów**, rozwiń projekt, a następnie wybierz **src**.
7. Kliknij prawym przyciskiem myszy **src**, wskaż polecenie **New**, a następnie wybierz pozycję **klasy Scala**.
8. W **nazwa** wprowadź nazwę. W **rodzaj** wybierz opcję **obiektu**. Następnie wybierz przycisk **OK**.

    ![Okno dialogowe Utwórz nową klasę Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. W pliku .scala Wklej następujący kod:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. Na **kompilacji** menu, wybierz opcję **kompilacji projektu**. Upewnij się, że kompilacja zakończy się pomyślnie.


## <a name="link-to-the-hortonworks-sandbox"></a>Połącz z Piaskownicą Hortonworks

Zanim możesz połączyć z Piaskownicą Hortonworks (emulatora), konieczne jest posiadanie istniejącej aplikacji IntelliJ.

Aby połączyć się z emulatora:

1. Otwórz projekt w programie IntelliJ.
2. Na **widoku** menu, wybierz opcję **narzędzia Windows**, a następnie wybierz pozycję **Eksploratora usługi Azure**.
3. Rozwiń **Azure**, kliknij prawym przyciskiem myszy **HDInsight**, a następnie wybierz pozycję **Link Emulator**.
4. W **łącze Nowy Emulator** okna dialogowego wprowadź hasło, ustawionego dla konta głównego z Piaskownicą Hortonworks. Następnie wprowadź wartości podobne do tych na następującym zrzucie ekranu. Następnie wybierz przycisk **OK**. 

   ![Okno dialogowe Nowy Emulator Link](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Aby skonfigurować emulator, wybierz **tak**.

Gdy nawiązano emulator emulator (Piaskownicą Hortonworks) znajduje się w węźle HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Przesyłanie aplikacji Spark Scala Piaskownicą Hortonworks

Po połączeniu IntelliJ IDEA w emulatorze mogą przesyłać projektu.

Aby przesłać projekt w emulatorze:

1. W **Eksplorator projektów**, kliknij prawym przyciskiem myszy projekt, a następnie wybierz **przesłać aplikację aparatu Spark na HDInsight**.
2. Wykonaj następujące czynności:

    1. W **klastra Spark (tylko system Linux)** listy rozwijanej wybierz swoje lokalne Piaskownicą Hortonworks.
    2. W **Nazwa klasy Main** wybierz lub wprowadź nazwę klasy głównego. W tym artykule jest nazwa **GroupByTest**.

3. Wybierz **przesłać**. Dzienniki przesyłania zadań są wyświetlane w oknie narzędzia przesyłania platformy Spark.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [używać narzędzi HDInsight w Azure Toolkit for IntelliJ do tworzenia aplikacji platformy Apache Spark dla klastra usługi HDInsight Spark systemu Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Wideo dotyczące narzędzi HDInsight dla programu IntelliJ, zobacz [wprowadzenia narzędzi HDInsight dla programu IntelliJ do tworzenia aplikacji platformy Apache Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Dowiedz się, jak [zdalne debugowanie aplikacji platformy Apache Spark w klastrze usługi HDInsight przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ za pośrednictwem protokołu SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Dowiedz się, jak [debugowanie aplikacji platformy Apache Spark zdalnie w klastrze usługi HDInsight Spark systemu Linux przy użyciu narzędzi HDInsight w usłudze Azure Toolkit for IntelliJ](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Dowiedz się, jak [korzystaj z narzędzi HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Apache Spark](../spark/apache-spark-eclipse-tool-plugin.md).

- Wideo dotyczące narzędzi HDInsight dla programu Eclipse, zobacz [korzystaj z narzędzi HDInsight dla programu Eclipse, tworzenie aplikacji Spark](https://mix.office.com/watch/1rau2mopb6fha).
