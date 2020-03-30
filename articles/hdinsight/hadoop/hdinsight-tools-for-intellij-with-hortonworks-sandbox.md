---
title: Użyj zestawu narzędzi Platformy Azure dla technologii IntelliJ z piaskownicą Hortonworks
description: Dowiedz się, jak korzystać z narzędzi HDInsight w usłudze Azure Toolkit dla technologii IntelliJ z piaskownicą Hortonworks.
keywords: narzędzia hadoop,zapytanie gałęzi,intellij,piaskownica hortonworks,zestaw narzędzi platformy Azure dla intellij
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 65a15a8506b88e95e14af8c87bcbe33087301519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647836"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Korzystanie z narzędzi HDInsight tools dla intellij z piaskownicą Hortonworks

Dowiedz się, jak używać narzędzi HDInsight Tools for IntelliJ do tworzenia aplikacji Apache Scala, a następnie testować aplikacje w [piaskownicy Hortonworks działającej](https://hortonworks.com/products/sandbox/) na komputerze. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) to zintegrowane środowisko programistyczne Java (IDE) do tworzenia oprogramowania komputerowego. Po opracowaniu i przetestowaniu aplikacji w aplikacji Hortonworks Sandbox można przenieść aplikacje do [usługi Azure HDInsight.](apache-hadoop-introduction.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu musisz mieć następujące elementy:

- Hortonworks Data Platform (HDP) 2.4 na Hortonworks Sandbox działa na komputerze lokalnym. Aby skonfigurować hdp, zobacz [Wprowadzenie do ekosystemu Apache Hadoop z piaskownicą Hadoop na maszynie wirtualnej](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > HdInsight Tools for IntelliJ został przetestowany tylko z HDP 2.4. Aby uzyskać HDP 2.4, rozwiń **Hortonworks Sandbox Archive** na [stronie pobierania piaskownicy Hortonworks.](https://hortonworks.com/downloads/#sandbox)

- [Zestaw Java Developer Kit (JDK) w wersji 1.8 lub nowszej](https://aka.ms/azure-jdks). Zestaw narzędzi platformy Azure dla technologii IntelliJ wymaga zestawu JDK.

- [IntelliJ IDEA community edition](https://www.jetbrains.com/idea/download) z wtyczką [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) i [zestawem narzędzi Azure dla intellij](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) plug-in. Narzędzia HDInsight dla technologii IntelliJ są dostępne w ramach zestawu narzędzi platformy Azure dla technologii IntelliJ. 

Aby zainstalować wtyczki:

  1. Otwórz środowisko IntelliJ IDEA.
  2. Na stronie **Powitanie** wybierz pozycję **Konfiguruj**, a następnie wybierz pozycję **Wtyczki**.
  3. W lewym dolnym rogu wybierz opcję **Zainstaluj wtyczkę JetBrains**.
  4. Użyj funkcji wyszukiwania, aby wyszukać **Scala**, a następnie wybierz pozycję **Zainstaluj**.
  5. Aby zakończyć instalację, wybierz **opcję Uruchom ponownie intellij idea**.
  6. Powtórz kroki 4 i 5, aby zainstalować **zestaw narzędzi Platformy Azure dla intellij**. Aby uzyskać więcej informacji, zobacz [Instalowanie zestawu narzędzi platformy Azure dla technologii IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Tworzenie aplikacji Apache Spark Scala

W tej sekcji można utworzyć przykładowy projekt Scala przy użyciu IntelliJ IDEA. W następnej sekcji należy połączyć IntelliJ IDEA z piaskownicą Hortonworks (emulatorem) przed przesłaniem projektu.

1. Otwórz IntelliJ IDEA na swoim komputerze. W oknie dialogowym **Nowy projekt** wykonaj następujące czynności:

   1. Wybierz **HDInsight** > **Spark na HDInsight (Scala)**.
   2. Na liście **narzędzia Kompilacja** wybierz jedną z następujących opcji na podstawie scenariusza:

      * **Maven**: Dla scala tworzenia projektu obsługi kreatora.
      * **SBT**: Do zarządzania zależnościami i tworzenia dla projektu Scala.

   ![Intellij tworzy nowy projekt scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Wybierz **pozycję Dalej**.
3. W następnym oknie dialogowym **Nowy projekt** wykonaj następujące czynności:

   1. W polu **Nazwa projektu** wprowadź nazwę projektu.
   2. W polu **Lokalizacja projektu** wprowadź lokalizację projektu.
   3. Obok listy rozwijanej **SDK projektu** wybierz pozycję **Nowy**, wybierz **JDK**, a następnie określ folder dla java JDK w wersji 1.7 lub nowszej. Wybierz **opcję Java 1.8** dla klastra Spark 2.x. Wybierz **opcję Java 1.7** dla klastra Spark 1.x. Domyślną lokalizacją jest C:\Program Files\Java\jdk1.8.x_xxx.
   4. Na liście rozwijanej **wersji platformy Spark** kreator tworzenia projektów Scala integruje poprawną wersję dla zestawie Spark SDK i Scala SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie użyto platformy Spark 1.6.2 (Scala 2.10.5). Upewnij się, że używasz repozytorium oznaczonego **jako Scala 2.10.x**. Nie należy używać repozytorium oznaczonego jako Scala 2.11.x.
    
      ![Tworzenie właściwości projektu IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Wybierz **pozycję Zakończ**.
5. Jeśli widok **projektu** nie jest jeszcze otwarty, naciśnij **klawisze Alt+1,** aby go otworzyć.
6. W **Eksploratorze projektu**rozwiń projekt, a następnie wybierz **opcję src**.
7. Kliknij prawym przyciskiem **myszy polecenie src**, wskaż polecenie **Nowy**, a następnie wybierz **klasę Scala**.
8. W polu **Nazwa** wprowadź nazwę. W polu **Rodzaj** wybierz pozycję **Obiekt**. Następnie wybierz przycisk **OK**.

    ![Okno dialogowe Tworzenie nowej klasy Scali](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. W pliku .scala wklej następujący kod:

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

10. W menu **Kompilacja** wybierz polecenie **Zbuduj projekt**. Upewnij się, że kompilacja kończy się pomyślnie.


## <a name="link-to-the-hortonworks-sandbox"></a>Link do piaskownicy Hortonworks

Aby można było utworzyć łącze do piaskownicy Hortonworks (emulatora), musisz mieć istniejącą aplikację IntelliJ.

Aby utworzyć łącze do emulatora:

1. Otwórz projekt w IntelliJ.
2. W menu **Widok** wybierz polecenie **Narzędzia systemu Windows**, a następnie wybierz pozycję **Eksplorator Azure**.
3. Rozwiń **pozycję Azure**, kliknij prawym przyciskiem myszy pozycję **HDInsight**, a następnie wybierz polecenie **Połącz emulator**.
4. W oknie dialogowym **Link A New Emulator** wprowadź hasło ustawione dla konta głównego piaskownicy Hortonworks. Następnie wprowadź wartości podobne do tych używanych na poniższym zrzucie ekranu. Następnie wybierz przycisk **OK**. 

   ![Okno dialogowe Łączenie nowego emulatora](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Aby skonfigurować emulator, wybierz opcję **Tak**.

Po pomyślnym podłączeniu emulatora emulator (Hortonworks Sandbox) jest wyświetlany w węźle HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Prześlij aplikację Spark Scala do piaskownicy Hortonworks

Po połączeniu IntelliJ IDEA z emulatorem możesz przesłać swój projekt.

Aby przesłać projekt do emulatora:

1. W **Eksploratorze projektu**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Prześlij aplikację Spark do programu HDInsight**.
2. Wykonaj następujące czynności:

    1. Na liście rozwijanej **Klastra Platformy Spark (tylko Linux)** wybierz lokalną piaskownicę Hortonworks.
    2. W polu **Nazwa klasy głównej** wybierz lub wprowadź nazwę klasy głównej. W tym artykule nazwa to **GroupByTest**.

3. Wybierz pozycję **Prześlij**. Dzienniki przesyłania zadań są wyświetlane w oknie narzędzia przesyłania platformy Spark.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [używać narzędzi HDInsight Tools w usłudze Azure Toolkit for IntelliJ do tworzenia aplikacji Apache Spark dla klastra systemu HDInsight Spark Linux.](../spark/apache-spark-intellij-tool-plugin.md)

- Aby uzyskać film o narzędziach HDInsight tools for IntelliJ, zobacz [Wprowadzenie narzędzi HDInsight tools for IntelliJ for Apache Spark development](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Dowiedz się, jak [zdalnie debugować aplikacje Apache Spark w klastrze HDInsight za pomocą zestawu narzędzi Azure Toolkit dla technologii IntelliJ za pośrednictwem programu SSH.](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)

- Dowiedz się, jak [używać narzędzi HDInsight w usłudze Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark w klastrze systemu HDInsight Spark Linux.](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

- Dowiedz się, jak [tworzyć aplikacje Apache Spark za pomocą narzędzi HDInsight Tools w usłudze Azure Toolkit for Eclipse.](../spark/apache-spark-eclipse-tool-plugin.md)

