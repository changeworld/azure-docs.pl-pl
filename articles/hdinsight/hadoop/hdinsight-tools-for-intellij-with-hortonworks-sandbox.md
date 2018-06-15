---
title: Użyj narzędzi Azure for IntelliJ z piaskownicy Hortonworks | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą narzędzi HDInsight w zestawie narzędzi Azure for IntelliJ z Hortonworks piaskownicy.
keywords: narzędzia hadoop hive zapytania, intellij, hortonworks piaskownicy, narzędzi platformy azure dla intellij
services: HDInsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 59251287f7597955fe22700cc96527b9476753c1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202547"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Użyj narzędzia HDInsight Tools for IntelliJ z Hortonworks piaskownicy

Dowiedz się, jak używać narzędzia HDInsight Tools for IntelliJ do opracowywania aplikacji Apache Scala, a następnie testować aplikacje na [piaskownicy Hortonworks](http://hortonworks.com/products/sandbox/) działania na tym komputerze. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) jest Java zintegrowane środowisko programistyczne (IDE) do tworzenia oprogramowania komputera. Po opracowywania i testowania aplikacji na Hortonworks piaskownicy, można przenieść aplikacje [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

- Hortonworks Data Platform (HDP) 2.4 dotyczącej piaskownicy Hortonworks uruchomiony na komputerze lokalnym. Aby skonfigurować HDP, zobacz [wprowadzenie w ekosystemie Hadoop z piaskownicą Hadoop na maszynie wirtualnej](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Narzędzia HDInsight Tools for IntelliJ przetestowano tylko z HDP 2.4. Aby uzyskać HDP 2.4, rozwiń węzeł **Hortonworks piaskownicy archiwum** na [lokacji pobiera piaskownicy Hortonworks](http://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) w wersji 1.8 lub nowszej](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Azure Toolkit for IntelliJ wymaga JDK.

- [Wersja community IntelliJ IDEA](https://www.jetbrains.com/idea/download) z [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) wtyczki i [narzędzi Azure dla IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) wtyczki. Narzędzia HDInsight Tools for IntelliJ jest dostępny jako część zestawu narzędzi Azure for IntelliJ. 

Aby zainstalować dodatki plug-in:

  1. Otwórz środowisko IntelliJ IDEA.
  2. Na **powitalnej** wybierz pozycję **Konfiguruj**, a następnie wybierz **wtyczek**.
  3. W lewym dolnym rogu, wybierz **JetBrains zainstalować dodatek**.
  4. Użyj funkcji wyszukiwania w celu wyszukania **Scala**, a następnie wybierz **zainstalować**.
  5. Aby ukończyć instalację, wybierz **ponowne uruchomienie IntelliJ IDEA**.
  6. Powtórz kroki 4 i 5, aby zainstalować **narzędzi Azure dla IntelliJ**. Aby uzyskać więcej informacji, zobacz [zainstalować zestaw narzędzi platformy Azure dla IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-a-spark-scala-application"></a>Tworzenie aplikacji Spark Scala

W tej sekcji utworzysz przykładowy projekt Scala przy użyciu IntelliJ IDEA. W następnej sekcji możesz połączyć IntelliJ IDEA piaskownicy Hortonworks (emulatora), przed przesłaniem projektu.

1. Otwórz środowisko IntelliJ IDEA na komputerze. W **nowy projekt** okno dialogowe, wykonaj następujące kroki:

   1. Wybierz **HDInsight** > **Spark w usłudze HDInsight (Scala)**.
   2. W **narzędzia kompilacji** listy, wybierz jedną z następujących czynności, oparte na danego scenariusza:

    * **Maven**: Obsługa kreatora dla Scala tworzenia projektu.
    * **SBT**: Zarządzanie zależności i budowania projektu Scala.

   ![Okno dialogowe nowego projektu](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Wybierz opcję **Dalej**.
3. W następnej **nowy projekt** okna dialogowego należy wykonać następujące czynności:

    1. W **Nazwa projektu** wprowadź nazwę projektu.
    2. W **lokalizacja projektu** wprowadź lokalizację projektu.
    3. Obok pozycji **SDK projektu** listy rozwijanej wybierz **nowy**, wybierz pozycję **JDK**, a następnie określ folder dla języka Java JDK wersji 1,7 lub nowszej. Wybierz **Java 1.8** dla klastra Spark 2.x. Wybierz **Java 1.7** 1.x klastra Spark. Lokalizacja domyślna to C:\Program Files\Java\jdk1.8.x_xxx.
    4. W **wersji Spark** listy rozwijanej, Kreator tworzenia projektu Scala integruje się poprawną wersję zestawu SDK platformy Spark i Scala zestawu SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie użyto Spark 1.6.2 (Scala 2.10.5). Upewnij się, czy korzystają z repozytorium oznaczone **Scala 2.10.x**. Nie używaj repozytorium oznaczone Scala 2.11.x.
    
    ![Utwórz IntelliJ Scala właściwości projektu](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Wybierz pozycję **Finish** (Zakończ).
5. Jeśli **projektu** widoku nie jest jeszcze otwarty, naciśnij klawisz **Alt + 1** go otworzyć.
6. W **Eksplorator projektów**, rozwiń węzeł projektu, a następnie wybierz **src**.
7. Kliknij prawym przyciskiem myszy **src**, wskaż polecenie **nowy**, a następnie wybierz **klasy Scala**.
8. W **nazwa** wprowadź nazwę. W **rodzaj** wybierz opcję **obiektu**. Następnie wybierz przycisk **OK**.

    ![Okno dialogowe Tworzenie nowej klasy Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

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

10. Na **kompilacji** menu, wybierz opcję **kompilacji projektu**. Upewnij się, że kompilacja kończy się pomyślnie.


## <a name="link-to-the-hortonworks-sandbox"></a>Łącze do piaskownicy Hortonworks

Przed połączeniem się piaskownicy Hortonworks (emulatora), musi korzystać z istniejących aplikacji IntelliJ.

Aby utworzyć link do emulatora:

1. Otwórz projekt w IntelliJ.
2. Na **widoku** menu, wybierz opcję **narzędzi systemu Windows**, a następnie wybierz **Eksploratora Azure**.
3. Rozwiń węzeł **Azure**, kliknij prawym przyciskiem myszy **HDInsight**, a następnie wybierz **Link Emulator**.
4. W **emulatora nowe łącze A** okna dialogowego wprowadź hasło, które ustawiono dla konta głównego piaskownicy Hortonworks. Następnie wprowadź wartości podobnie jak w poniższym zrzucie ekranu. Następnie wybierz przycisk **OK**. 

   ![Okno dialogowe Nowy Emulator Link](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Aby skonfigurować emulator, wybierz **tak**.

Po połączył emulator emulatora (Hortonworks piaskownicy) zostanie wyświetlona w węźle usługi HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Przesyłanie aplikacji Spark Scala do piaskownicy Hortonworks

Po połączeniu IntelliJ IDEA emulator, możesz przesłać projektu.

Aby przesłać projektu na emulatorze:

1. W **Eksplorator projektów**, kliknij prawym przyciskiem myszy projekt, a następnie wybierz **Spark przesyłania aplikacji do usługi HDInsight**.
2. Wykonaj następujące czynności:

    1. W **klastra Spark (tylko w systemie Linux)** listy rozwijanej wybierz użytkownika lokalnego Hortonworks piaskownicy.
    2. W **Nazwa klasy głównym** wybierz lub wprowadź nazwę klasy głównym. W tym samouczku jest nazwa **GroupByTest**.

3. Wybierz **przesłać**. Dzienniki przesyłania zadania są wyświetlane w oknie narzędzia przesyłanie Spark.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [umożliwia narzędzi HDInsight Tools w zestawie narzędzi Azure for IntelliJ tworzenie aplikacji Spark dla klastra usługi HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Film o narzędzia HDInsight Tools for IntelliJ, zobacz [wprowadzenie narzędzi HDInsight Tools for IntelliJ rozwoju Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Dowiedz się, jak [zdalne debugowanie aplikacji Spark w klastrze usługi HDInsight narzędzi Azure for IntelliJ za pośrednictwem protokołu SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Dowiedz się, jak [debugowanie aplikacji Spark zdalnie w klastrze HDInsight Spark Linux przy użyciu narzędzia HDInsight Tools w zestawie narzędzi Azure for IntelliJ](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Dowiedz się, jak [użycia narzędzi HDInsight w zestawie narzędzi Azure dla programu Eclipse tworzenie aplikacji Spark](../spark/apache-spark-eclipse-tool-plugin.md).

- Film o narzędzi HDInsight Tools dla programu Eclipse, zobacz [użycia narzędzi HDInsight Tools dla programu Eclipse tworzenie aplikacji Spark](https://mix.office.com/watch/1rau2mopb6fha).
