---
title: 'Samouczek: tworzenie aplikacji Scala Maven dla platformy Spark w usłudze Azure HDInsight przy użyciu środowiska IntelliJ'
description: Tworzenie aplikacji Spark napisanej w języku Scala przy użyciu systemu kompilacji Apache Maven oraz istniejącego archetypu Maven dla języka Scala, udostępnionego przez środowisko IntelliJ IDEA.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/07/2018
ms.openlocfilehash: d83c04946b67dd25bae306c2fa41a0864287bfc8
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499321"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Samouczek: tworzenie aplikacji Scala Maven dla platformy Apache Spark w usłudze HDInsight przy użyciu środowiska IntelliJ

W tym samouczku dowiesz się, jak utworzyć aplikację [Apache Spark](https://spark.apache.org/) napisaną w języku [Scala](https://www.scala-lang.org/) za pomocą narzędzia [Apache Maven](https://maven.apache.org/) i środowiska IntelliJ IDEA. W tym artykule skorzystamy z systemu kompilacji Apache Maven. Na początku użyjemy istniejącego archetypu Maven dla języka Scala, udostępnionego przez środowisko IntelliJ IDEA.  Tworzenie aplikacji Scala w środowisku IntelliJ IDEA obejmuje następujące kroki:

* Używanie narzędzia Maven jako systemu kompilacji.
* Aktualizowanie pliku POM (Project Object Model) umożliwiającego rozpoznawanie zależności modułu Spark.
* Pisanie aplikacji w języku Scala.
* Generowanie pliku jar, który można przesłać do klastrów HDInsight Spark.
* Uruchamianie aplikacji w klastrze Spark przy użyciu usługi Livy.

> [!NOTE]
> Usługa HDInsight udostępnia również wtyczkę IntelliJ IDEA, która ułatwia tworzenie aplikacji i przesyłanie ich do klastra HDInsight Spark w systemie Linux. Aby uzyskać więcej informacji, zobacz [Tworzenie i przesyłanie aplikacji Apache Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md).
> 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie aplikacji Scala Maven przy użyciu środowiska IntelliJ

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Zestaw Oracle Java Development. Możesz go zainstalować, klikając [tutaj](https://aka.ms/azure-jdks).
* Środowisko projektowe Java. W tym artykule jest używane środowisko IntelliJ IDEA 18.1.1. Możesz je zainstalować, klikając [tutaj](https://www.jetbrains.com/idea/download/).

## <a name="use-intellij-to-create-application"></a>Tworzenie aplikacji przy użyciu środowiska IntelliJ

1. Uruchom środowisko IntelliJ IDEA, a następnie utwórz projekt. W oknie dialogowym **Nowy projekt** wykonaj następujące czynności: 

   a. Wybierz pozycję **HDInsight** > **Spark w usłudze HDInsight (Scala)**.

   b. Z listy **Narzędzie kompilacji** wybierz jedną z następujących pozycji odpowiednio do potrzeb:

      * **Maven** — w celu obsługi kreatora tworzenia projektu Scala
      * **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala

   ![Okno dialogowe Nowy projekt](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

1. Wybierz opcję **Dalej**.

1. Kreator tworzenia projektu Scala automatycznie wykrywa, czy wtyczka Scala została zainstalowana. Wybierz pozycję **Zainstaluj**.

   ![Sprawdzanie wtyczki Scala](./media/apache-spark-create-standalone-application/Scala-Plugin-check-Reminder.PNG) 

1. Aby pobrać wtyczkę Scala, wybierz przycisk **OK**. Postępuj zgodnie z instrukcjami, aby ponownie uruchomić środowisko IntelliJ. 

   ![Okno dialogowe instalacji wtyczki Scala](./media/apache-spark-create-standalone-application/Choose-Scala-Plugin.PNG)

1. W oknie **Nowy projekt** wykonaj następujące czynności:  

    ![Wybieranie zestawu SDK platformy Spark](./media/apache-spark-create-standalone-application/hdi-new-project.png)

   a. Wprowadź nazwę i lokalizację projektu.

   b. Z listy rozwijanej **Zestaw SDK projektu** wybierz pozycję **Java 1.8** dla klastra Spark 2.x lub pozycję **Java 1.7** dla klastra Spark 1.x.

   d. Na liście rozwijanej **Wersja Spark** kreator tworzenia projektu Scala integruje poprawną wersję dla zestawu SDK platformy Spark i zestawu SDK Scala. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie używana jest wersja **Spark 2.0.2 (Scala 2.11.8)**.

1. Wybierz pozycję **Finish** (Zakończ).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalowanie wtyczki Scala dla środowiska IntelliJ IDEA
Aby zainstalować wtyczkę Scala, wykonaj następujące kroki:

1. Otwórz środowisko IntelliJ IDEA.
1. Na ekranie powitalnym wybierz pozycję **Configure** (Konfiguruj), a następnie pozycję **Plugins** (Wtyczki).
   
    ![Włączanie wtyczki Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
1. Wybierz pozycję **Install JetBrains plugin** (Zainstaluj wtyczkę JetBrains) w lewym dolnym rogu. 
1. W oknie dialogowym **Browse JetBrains Plugins** (Przeglądanie wtyczek JetBrains) wyszukaj pozycję **Scala**, a następnie wybierz pozycję **Install** (Zainstaluj).
   
    ![Instalowanie wtyczki Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
1. Po pomyślnym zainstalowaniu wtyczki musisz ponownie uruchomić środowisko IDE.

## <a name="create-a-standalone-scala-project"></a>Tworzenie autonomicznego projektu Scala
1. Otwórz środowisko IntelliJ IDEA.
1. W menu **File** (Plik) wybierz pozycję **New > Project** (Nowy > Projekt), aby utworzyć nowy projekt.
1. W oknie dialogowym nowego projektu wybierz następujące opcje:
   
    ![Tworzenie projektu Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Wybierz opcję **Maven** jako typ projektu.
   * Wskaż **zestaw SDK projektu**. Wybierz pozycję **New** (Nowy) i przejdź do katalogu instalacyjnego Java, zwykle `C:\Program Files\Java\jdk1.8.0_66`.
   * Wybierz opcję **Create from archetype** (Utwórz z archetypu).
   * Z listy archetypów wybierz pozycję **org.scala-tools.archetypes:scala-archetype-simple**. Ten archetyp pozwala utworzyć prawidłową strukturę katalogów i pobrać wymagane zależności domyślne, umożliwiające napisanie programu w języku Scala.
1. Wybierz opcję **Dalej**.
1. Podaj odpowiednie wartości w polach **GroupId**, **ArtifactId** i **Version**. W tym samouczku są one następujące:

    - GroupId: com.microsoft.spark.example
    - ArtifactId: SparkSimpleApp
1. Wybierz opcję **Dalej**.
1. Sprawdź ustawienia, a następnie wybierz pozycję **Next** (Dalej).
1. Sprawdź nazwę i lokalizację projektu, a następnie wybierz pozycję **Finish** (Zakończ).
1. W okienku po lewej stronie wybierz pozycję **src > test > scala > com > microsoft > spark > example**, kliknij prawym przyciskiem myszy pozycję **MySpec**, a następnie wybierz pozycję **Delete** (Usuń). Ten plik nie jest potrzebny w aplikacji.
  
1. W kolejnych krokach zaktualizujemy plik pom.xml, definiując zależności dla aplikacji Spark Scala. Aby automatycznie pobrać i rozpoznać te zależności, musisz odpowiednio skonfigurować narzędzie Maven.
   
    ![Konfigurowanie automatycznego pobierania w narzędziu Maven](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. W menu **File** (Plik) wybierz polecenie **Settings** (Ustawienia).
   1. W oknie dialogowym **Settings** (Ustawienia) wybierz pozycję **Build, Execution, Deployment** > **Build Tools** > **Maven** > **Importing** (Kompilacja, wykonywanie, wdrażanie > Narzędzia kompilacji > Maven > Importowanie).
   1. Wybierz opcję **Import Maven projects automatically** (Importuj projekty Maven automatycznie).
   1. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.
1. W okienku po lewej stronie wybierz pozycję **src > main > scala > com.microsoft.spark.example**, a następnie kliknij dwukrotnie pozycję **App**, aby otworzyć element App.scala.

1. Zastąp przykładowy kod następującym kodem i zapisz zmiany. Ten kod odczytuje dane z pliku HVAC.csv (dostępnego we wszystkich klastrach HDInsight Spark), pobiera wiersze, które w szóstej kolumnie zawierają tylko jedną cyfrę, i zapisuje dane wyjściowe w lokalizacji **/HVACOut** w domyślnym kontenerze magazynu klastra.

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
1. W lewym okienku kliknij dwukrotnie plik **pom.xml**.
   
   1. W sekcji `<project>\<properties>` dodaj następujące segmenty:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   1. W sekcji `<project>\<dependencies>` dodaj następujące segmenty:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Zapisz zmiany w pliku pom.xml.
1. Utwórz plik jar. Środowisko IntelliJ IDEA umożliwia tworzenie plików JAR jako artefaktów projektu. Wykonaj poniższe kroki.
    
    1. W menu **File** (Plik) wybierz pozycję **Project Structure** (Struktura projektu).
    1. W oknie dialogowym **Project Structure** (Struktura projektu) wybierz pozycję **Artifacts** (Artefakty), a następnie wybierz znak plus. W wyświetlonym podręcznym oknie dialogowym wybierz pozycję **JAR**, a następnie wybierz pozycję **From modules with dependencies** (Z modułów z zależnościami).
       
        ![Tworzenie pliku JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)
    1. W oknie dialogowym **Create JAR from Modules** (Tworzenie pliku JAR z modułów) wybierz wielokropek (![wielokropek](./media/apache-spark-create-standalone-application/ellipsis.png)) obok pozycji **Main Class** (Klasa główna).
    1. W oknie dialogowym **Select Main Class** (Wybieranie klasy głównej) wybierz domyślną klasę, a następnie wybierz przycisk **OK**.
       
        ![Tworzenie pliku JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)
    1. W oknie dialogowym **Create JAR from Modules** (Tworzenie pliku JAR z modułów) sprawdź, czy jest wybrana opcja **extract to the target JAR** (Wyodrębnij do docelowego pliku JAR), a następnie wybierz przycisk **OK**.  Wybranie tego ustawienia powoduje utworzenie pojedynczego pliku JAR zawierającego wszystkie zależności.
       
        ![Tworzenie pliku JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)
    1. Karta układu danych wyjściowych zawiera listę wszystkich plików JAR, które są częścią projektu Maven. Możesz usunąć pliki, z którymi aplikacja Scala nie ma bezpośrednich zależności. W przypadku aplikacji, którą tworzysz, możesz usunąć wszystkie pliki oprócz ostatniego (**SparkSimpleApp compile output**). Wybierz pliki JAR do usunięcia, a następnie wybierz ikonę **Delete** (Usuń).
       
        ![Tworzenie pliku JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Upewnij się, że jest zaznaczone pole **Include in project build** (Uwzględnij w kompilacji projektu). Dzięki temu plik JAR będzie tworzony przy każdej kompilacji lub aktualizacji projektu. Wybierz przycisk **Apply** (Zastosuj), a następnie przycisk **OK**.
    1. W menu **Build** (Kompilacja) wybierz pozycję **Build Artifacts** (Kompiluj artefakty), aby utworzyć plik JAR. Wyjściowy plik JAR jest tworzony w lokalizacji **\out\artifacts**.
       
        ![Tworzenie pliku JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Uruchamianie aplikacji w klastrze Apache Spark
Aplikację można uruchomić w klastrze przy użyciu następujących metod:

* **Skopiuj plik JAR aplikacji do obiektu blob magazynu Azure** skojarzonego z klastrem. Możesz to zrobić za pomocą narzędzia wiersza polecenia [**AzCopy**](../../storage/common/storage-use-azcopy.md). Dane możesz przesłać również przy użyciu wielu innych klientów. Więcej informacji na ten temat można znaleźć w artykule [Przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight](../hdinsight-upload-data.md).
* **Użyj usługi Apache Livy, aby zdalnie przesłać zadanie aplikacji** do klastra Spark. Klastry Spark w usłudze HDInsight zawierają usługę Livy, która udostępnia punkty końcowe REST umożliwiające zdalne przesyłanie zadań Spark. Aby uzyskać więcej informacji, zobacz [Submit Apache Spark jobs remotely using Apache Livy with Spark clusters on HDInsight](apache-spark-livy-rest-interface.md) (Zdalne przesyłanie zadań Apache Spark przy użyciu usługi Apache Livy udostępnianej w klastrach Spark w usłudze HDInsight).

## <a name="next-step"></a>Następny krok

W tym artykule przedstawiono sposób tworzenia aplikacji Apache Spark Scala. Przejdź do następnego artykułu, aby dowiedzieć się, jak uruchomić tę aplikację w klastrze HDInsight Spark przy użyciu usługi Livy.

> [!div class="nextstepaction"]
>[Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](./apache-spark-livy-rest-interface.md)

