---
title: 'Samouczek: tworzenie aplikacji Scala Maven dla platformy Spark w usłudze Azure HDInsight przy użyciu środowiska IntelliJ'
description: Samouczek — tworzenie aplikacji Spark napisane w języku Scala przy użyciu narzędzia Apache Maven systemu kompilacji oraz istniejących archetypu narzędzia Maven, dla Scala dostarczone przez IntelliJ IDEA.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/28/2019
ms.openlocfilehash: cf1ada88cda77091e303e67b9652b9c6a372e2d1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295374"
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

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie aplikacji Scala Maven przy użyciu środowiska IntelliJ

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* [Zestaw Oracle Java Development](https://www.azul.com/downloads/azure-only/zulu/).  W tym samouczku jest używany język Java w wersji 8.0.202.
* Środowisko projektowe Java. W tym artykule jest używane środowisko [IntelliJ IDEA Community w wersji  2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Zobacz [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable) (Instalowanie zestawu Azure Toolkit for IntelliJ).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalowanie wtyczki Scala dla środowiska IntelliJ IDEA
Wykonaj następujące kroki, aby zainstalować wtyczkę Scala:

1. Otwórz środowisko IntelliJ IDEA.

2. Na ekranie powitalnym przejdź do pozycji **Configure (Konfiguruj)**  > **Plugins (Wtyczki)** , aby otworzyć okno **Plugins (Wtyczki)** .
   
    ![Włączanie wtyczki Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)

3. Wybierz pozycję **Install** (Instaluj) dla wtyczki Scala, która zostanie wyświetlona w nowym oknie.  
 
    ![Instalowanie wtyczki Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Po pomyślnym zainstalowaniu wtyczki musisz ponownie uruchomić środowisko IDE.


## <a name="use-intellij-to-create-application"></a>Tworzenie aplikacji przy użyciu środowiska IntelliJ

1. Uruchom środowisko IntelliJ IDEA i wybierz pozycję **Create New Project** (Utwórz nowy projekt), aby otworzyć okno **New Project** (Nowy projekt).

2. Wybierz pozycję **Azure Spark/HDInsight** w okienku po lewej stronie.

3. Wybierz pozycję **Spark Project (Scala)** w głównym oknie.

4. Z listy rozwijanej **Build tool** (Narzędzie kompilacji), wybierz jedną z następujących pozycji:
      * **Maven** — w celu obsługi kreatora tworzenia projektu Scala.
      * **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala.

   ![Okno dialogowe Nowy projekt](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

5. Wybierz opcję **Dalej**.

6. W oknie **New Project** (Nowy projekt) podaj następujące informacje:  

  	|  Właściwość   | Opis   |  
  	| ----- | ----- |  
  	|Project name (Nazwa projektu)| Wprowadź nazwę.|  
  	|Lokalizacja&nbsp;projektu| Wprowadź żądaną lokalizację do zapisania projektu.|
  	|Zestaw SDK projektu| To pole będzie puste przy pierwszym użyciu środowiska IDEA.  Wybierz pozycję **New...** (Nowy...) i przejdź do swojego zestawu JDK.|
  	|Wersja platformy Spark|Kreator tworzenia integruje poprawną wersję dla zestawów Spark SDK i Scala SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie używana jest wersja **Spark 2.3.0 (Scala 2.11.8)** .|

    ![Wybieranie zestawu SDK platformy Spark](./media/apache-spark-create-standalone-application/hdi-new-project.png)

7. Wybierz pozycję **Finish** (Zakończ).

## <a name="create-a-standalone-scala-project"></a>Tworzenie autonomicznego projektu Scala

1. Uruchom środowisko IntelliJ IDEA i wybierz pozycję **Create New Project** (Utwórz nowy projekt), aby otworzyć okno **New Project** (Nowy projekt).

2. Wybierz pozycję **Maven** w okienku po lewej stronie.

3. Wskaż **zestaw SDK projektu**. Jeśli jest pusty, wybierz pozycję **New...** (Nowy...) i przejdź do katalogu instalacyjnego Java.

4. Zaznacz pole wyboru **Create from archetype** (Utwórz z archetypu).  

5. Z listy archetypów wybierz pozycję **org.scala-tools.archetypes:scala-archetype-simple**. Ten archetyp pozwala utworzyć prawidłową strukturę katalogów i pobrać wymagane zależności domyślne, umożliwiające napisanie programu w języku Scala.

    ![Tworzenie projektu Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)

6. Wybierz opcję **Dalej**.

7. Podaj odpowiednie wartości w polach **GroupId**, **ArtifactId** i **Version**. W tym samouczku są one następujące:

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

8. Wybierz opcję **Dalej**.

9. Sprawdź ustawienia, a następnie wybierz pozycję **Next** (Dalej).

10. Sprawdź nazwę i lokalizację projektu, a następnie wybierz pozycję **Finish** (Zakończ).  Zaimportowanie projektu potrwa kilka minut.

11. Po zaimportowaniu projektu w lewym okienku przejdź do pozycji **SparkSimpleApp** > **src** > **test**  >  **scala** > **com** > **microsoft** > **spark**  >  **example**.  Kliknij prawym przyciskiem myszy pozycję **MySpec**, a następnie wybierz pozycję **Delete...** (Usuń...). Ten plik nie jest potrzebny w aplikacji.  Wybierz przycisk **OK** w oknie dialogowym.
  
12. W kolejnych krokach zaktualizujemy plik **pom.xml**, definiując zależności dla aplikacji Spark Scala. Aby automatycznie pobrać i rozpoznać te zależności, musisz odpowiednio skonfigurować narzędzie Maven.

13. W menu **File** (Plik) wybierz polecenie **Settings** (Ustawienia), aby otworzyć okno **Settings** (Ustawienia).

14. W oknie **Settings** (Ustawienia) wybierz pozycję **Build, Execution, Deployment (Kompilacja, wykonywanie, wdrażanie)**  > **Build Tools (Narzędzia kompilacji)**  > **Maven** > **Importing (Importowanie)** .

15. Zaznacz pole wyboru **Import Maven projects automatically** (Importuj projekty Maven automatycznie).

16. Wybierz **Zastosuj**, a następnie wybierz pozycję **OK**.  Następnie nastąpi powrót do okna projektu.
   
    ![Konfigurowanie automatycznego pobierania w narzędziu Maven](./media/apache-spark-create-standalone-application/configure-maven.png)
   

17. W okienku po lewej stronie przejdź do pozycji **src** > **main** > **scala** > **com.microsoft.spark.example**, a następnie kliknij dwukrotnie pozycję **App**, aby otworzyć plik App.scala.

18. Zastąp przykładowy kod następującym kodem i zapisz zmiany. Ten kod odczytuje dane z pliku HVAC.csv (dostępnego we wszystkich klastrach HDInsight Spark), pobiera wiersze, które w szóstej kolumnie zawierają tylko jedną cyfrę, i zapisuje dane wyjściowe w lokalizacji **/HVACOut** w domyślnym kontenerze magazynu klastra.

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
19. W lewym okienku kliknij dwukrotnie plik **pom.xml**.  
   
20. W sekcji `<project>\<properties>` dodaj następujące segmenty:
      
          <scala.version>2.11.8</scala.version>
          <scala.compat.version>2.11.8</scala.compat.version>
          <scala.binary.version>2.11</scala.binary.version>

21. W sekcji `<project>\<dependencies>` dodaj następujące segmenty:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>2.3.0</version>
           </dependency>
      
    Zapisz zmiany w pliku pom.xml.

22. Utwórz plik jar. Środowisko IntelliJ IDEA umożliwia tworzenie plików JAR jako artefaktów projektu. Wykonaj poniższe kroki.
    
    1. W menu **File** (Plik) wybierz pozycję **Project Structure...** (Struktura projektu...).

    2. W oknie **Project Structure** (Struktura projektu) przejdź do pozycji **Artifacts** (Artefakty) > **symbol znaku plus +**  > **JAR**  >  **From modules with dependencies...**  (Z modułów z zależnościami).
       
        ![Tworzenie pliku JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)

    3. W oknie **Create JAR from Modules** (Tworzenie pliku JAR z modułów) wybierz ikonę folderu w polu tekstowym **Main Class** (Klasa główna).

    4. W oknie **Select Main Class** (Wybieranie klasy głównej) wybierz domyślną klasę, a następnie wybierz przycisk **OK**.
       
        ![Tworzenie pliku JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)

    5. W oknie **Create JAR from Modules** (Tworzenie pliku JAR z modułów) sprawdź, czy jest wybrana opcja **extract to the target JAR** (Wyodrębnij do docelowego pliku JAR), a następnie wybierz przycisk **OK**.  Wybranie tego ustawienia powoduje utworzenie pojedynczego pliku JAR zawierającego wszystkie zależności.
       
        ![Tworzenie pliku JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)

    6. Karta **Output Layout** (Układ danych wyjściowych) zawiera listę wszystkich plików JAR, które są częścią projektu Maven. Możesz usunąć pliki, z którymi aplikacja Scala nie ma bezpośrednich zależności. W przypadku aplikacji, którą tworzysz, możesz usunąć wszystkie pliki oprócz ostatniego (**SparkSimpleApp compile output**). Wybierz pliki JAR do usunięcia, a następnie wybierz symbol znaku minus **-** .
       
        ![Tworzenie pliku JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Upewnij się, że jest zaznaczone pole wyboru **Include in project build** (Uwzględnij w kompilacji projektu). Dzięki temu plik JAR będzie tworzony przy każdej kompilacji lub aktualizacji projektu. Wybierz przycisk **Apply** (Zastosuj), a następnie przycisk **OK**.

    7. Aby utworzyć plik jar, przejdź do pozycji **Build** (Kompilacja) > **Build Artifacts** (Artefakty kompilacji) > **Build** (Kompilacja). Projekt zostanie skompilowany w ciągu około 30 sekund.  Wyjściowy plik JAR jest tworzony w lokalizacji **\out\artifacts**.
       
        ![Tworzenie pliku JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Uruchamianie aplikacji w klastrze Apache Spark
Aplikację można uruchomić w klastrze przy użyciu następujących metod:

* **Skopiuj plik JAR aplikacji do obiektu blob magazynu Azure** skojarzonego z klastrem. Możesz to zrobić za pomocą narzędzia wiersza polecenia [**AzCopy**](../../storage/common/storage-use-azcopy.md). Dane możesz przesłać również przy użyciu wielu innych klientów. Więcej informacji na ten temat można znaleźć w artykule [Przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight](../hdinsight-upload-data.md).

* **Użyj usługi Apache Livy, aby zdalnie przesłać zadanie aplikacji** do klastra Spark. Klastry Spark w usłudze HDInsight zawierają usługę Livy, która udostępnia punkty końcowe REST umożliwiające zdalne przesyłanie zadań Spark. Aby uzyskać więcej informacji, zobacz [Submit Apache Spark jobs remotely using Apache Livy with Spark clusters on HDInsight](apache-spark-livy-rest-interface.md) (Zdalne przesyłanie zadań Apache Spark przy użyciu usługi Apache Livy udostępnianej w klastrach Spark w usłudze HDInsight).

## <a name="next-step"></a>Następny krok

W tym artykule przedstawiono sposób tworzenia aplikacji Apache Spark Scala. Przejdź do następnego artykułu, aby dowiedzieć się, jak uruchomić tę aplikację w klastrze HDInsight Spark przy użyciu usługi Livy.

> [!div class="nextstepaction"]
>[Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](./apache-spark-livy-rest-interface.md)
