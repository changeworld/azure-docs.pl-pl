---
title: C#, Apache Hive & Apache Pig on Apache Hadoop - Azure HDInsight
description: Dowiedz się, jak używać funkcji zdefiniowanych przez użytkownika języka C# (UDF) z przesyłaną strumieniowo apache hive i apache pig w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9ef9eada9b9aec50642a8bf357edab0677868817
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74949393"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Użyj funkcji zdefiniowanych przez użytkownika języka C# z Apache Hive i Apache Pig na Apache Hadoop w hdinsight

Dowiedz się, jak używać funkcji zdefiniowanych przez użytkownika języka C# (UDF) z [apache hive](https://hive.apache.org) i [Apache Pig](https://pig.apache.org) w programie HDInsight.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie działają z klastrami HDInsight opartymi na systemie Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [PRZECHOWYWANIE wersji składników HDInsight](../hdinsight-component-versioning.md).

Zarówno Hive, jak i Pig mogą przekazywać dane do aplikacji zewnętrznych do przetwarzania. Ten proces jest znany jako _przesyłania strumieniowego_. Podczas korzystania z aplikacji .NET, dane są przekazywane do aplikacji na STDIN i aplikacja zwraca wyniki na STDOUT. Aby odczytywać i zapisywać z STDIN `Console.ReadLine()` `Console.WriteLine()` i STDOUT, można użyć i z aplikacji konsoli.

## <a name="prerequisites"></a>Wymagania wstępne

* Znajomość pisania i tworzenia kodu Języka C#, który jest przeznaczony dla platformy .NET Framework 4.5.

    Użyj dowolnego ide, który chcesz. Firma Microsoft zaleca [Visual Studio](https://www.visualstudio.com/vs) lub Visual [Studio Code](https://code.visualstudio.com/). Kroki opisane w tym dokumencie używają programu Visual Studio 2019.

* Sposób przekazywania plików exe do klastra i uruchamiania zadań Pig i Hive. Zalecamy [narzędzia Usługi Data Lake Tools dla programu Visual Studio,](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md)programu Azure [PowerShell](/powershell/azure)i [interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli?view=azure-cli-latest) Kroki opisane w tym dokumencie używają narzędzia usługi Data Lake Tools for Visual Studio do przekazywania plików i uruchamiania przykładowej kwerendy hive.

    Aby uzyskać informacje na temat innych sposobów uruchamiania zapytań hive, zobacz [Co to jest Gałąź apache i hiveQl w usłudze Azure HDInsight?](hdinsight-use-hive.md).

* Hadoop w klastrze HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [Tworzenie klastrów HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET na HDInsight

*Klastry HDInsight oparte na systemie Linux* używają mono [https://mono-project.com) (](https://mono-project.com) do uruchamiania aplikacji .NET. Mono w wersji 4.2.1 jest dołączony do hdinsight w wersji 3.6.

Aby uzyskać więcej informacji na temat zgodności mono z wersjami programu .NET Framework, zobacz [Zgodność mono](https://www.mono-project.com/docs/about-mono/compatibility/).

Aby uzyskać więcej informacji na temat wersji programu .NET Framework i mono dołączonej do wersji HDInsight, zobacz [wersje składników HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Tworzenie projektów\# C

W poniższych sekcjach opisano sposób tworzenia projektu języka C# w programie Visual Studio dla UDF hive Apache i Apache Pig UDF.

### <a name="apache-hive-udf"></a>Ul Apache UDF

Aby utworzyć projekt języka C# dla interfejsu UDF gałęzi Apache:

1. Uruchom program Visual Studio.

2. Wybierz **pozycję Utwórz nowy projekt**.

3. W oknie **Utwórz nowy projekt** wybierz szablon **Aplikacji konsoli (.NET Framework)** (wersja C#). Następnie wybierz **przycisk Dalej**.

4. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu** *HiveCSharp*i przejdź do lokalizacji lub utwórz **ją** w celu zapisania nowego projektu. Następnie wybierz pozycję **Utwórz**.

5. W programie Visual Studio IDE zastąp zawartość *Program.cs* następującym kodem:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

6. Na pasku menu wybierz pozycję **Zbuduj** > **rozwiązanie kompilacji,** aby utworzyć projekt.

7. Zamknij rozwiązanie.

### <a name="apache-pig-udf"></a>Świnia Apache UDF

Aby utworzyć projekt języka C# dla interfejsu UDF gałęzi Apache:

1. Otwórz program Visual Studio.

2. W oknie **Start** wybierz pozycję **Utwórz nowy projekt**.

3. W oknie **Utwórz nowy projekt** wybierz szablon **Aplikacji konsoli (.NET Framework)** (wersja C#). Następnie wybierz **przycisk Dalej**.

4. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu** *PigUDF*i przejdź do lub utwórz **lokalizację,** w którym zostanie zapisane nowy projekt. Następnie wybierz pozycję **Utwórz**.

5. W programie Visual Studio IDE zastąp zawartość *Program.cs* następującym kodem:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Ten kod analizuje wiersze wysyłane z wierszy Świni `java.lang.Exception`i sformatuje, które zaczynają się od .

6. Na pasku menu wybierz pozycję Build Build Solution to build the project .From the menu bar, choose **Build** > **Build Solution** to build the project.

7. Pozostaw roztwór otwartym.

## <a name="upload-to-storage"></a>Przekazywanie do magazynu

Następnie przekaż aplikacje Hive i Pig UDF do magazynu w klastrze HDInsight.

1. W programie Visual Studio przejdź do **pozycji Widok** > **Eksploratora serwera**.

1. Z **Eksploratora serwera**, prawym przyciskiem myszy **Azure**, wybierz pozycję **Połącz z subskrypcją platformy Microsoft Azure**i ukończ proces logowania.

1. Rozwiń klaster HDInsight, do którego chcesz wdrożyć tę aplikację. Na liście znajduje się wpis z **tekstem (Domyślne konto magazynu).**

    ![Domyślne konto magazynu, klaster HDInsight, Eksplorator serwerów](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Jeśli ten wpis można rozwinąć, używasz **konta usługi Azure Storage** jako domyślnego magazynu dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, rozwiń wpis, a następnie kliknij dwukrotnie **(Kontener domyślny).**

    * Jeśli nie można rozwinąć tego wpisu, używasz **usługi Azure Data Lake Storage** jako domyślnego magazynu dla klastra. Aby wyświetlić pliki w magazynie domyślnym dla klastra, kliknij dwukrotnie wpis **(Domyślne konto magazynu).**

1. Aby przekazać pliki exe, użyj jednej z następujących metod:

    * Jeśli używasz **konta usługi Azure Storage,** wybierz ikonę Przekaż obiekt **blob.**

        ![Ikona przekazywania programu HDInsight dla nowego projektu](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        W oknie dialogowym **Przekazywanie nowego pliku** w obszarze **Nazwa pliku**wybierz pozycję **Przeglądaj**. W oknie dialogowym **Przekazywanie obiektów blob** przejdź do folderu *bin\debug* dla projektu *HiveCSharp,* a następnie wybierz plik *HiveCSharp.exe.* Na koniec wybierz pozycję **Otwórz,** a następnie **przycisk OK,** aby zakończyć przesyłanie.

    * Jeśli korzystasz z **usługi Azure Data Lake Storage,** kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz polecenie **Przekaż**. Na koniec wybierz plik *HiveCSharp.exe* i wybierz pozycję **Otwórz**.

    Po zakończeniu *przekazywania hiveCSharp.exe* powtórz proces przekazywania pliku *PigUDF.exe.*

## <a name="run-an-apache-hive-query"></a>Uruchamianie kwerendy gałęzi Apache

Teraz można uruchomić kwerendę hive, która używa aplikacji Hive UDF.

1. W programie Visual Studio przejdź do **pozycji Widok** > **Eksploratora serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Kliknij prawym przyciskiem myszy klaster wdrożony w aplikacji *HiveCSharp,* a następnie wybierz polecenie **Napisz kwerendę gałęzi**.

4. Użyj następującego tekstu dla kwerendy hive:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Odkomentuj instrukcję, `add file` która pasuje do typu domyślnego magazynu używanego dla klastra.

    Ta kwerenda `clientid`wybiera `devicemake`, `devicemodel` i `hivesampletable`pola z , a następnie przekazuje pola do *aplikacji HiveCSharp.exe.* Kwerenda oczekuje, że aplikacja zwróci trzy pola, `phoneLabel`które `phoneHash`są przechowywane jako `clientid`, i . Kwerenda oczekuje również, aby znaleźć *HiveCSharp.exe* w katalogu głównym domyślnego kontenera magazynu.

5. Przełącz domyślną **interakcję** na **partię**, a następnie wybierz **pozycję Prześlij,** aby przesłać zadanie do klastra HDInsight. Zostanie otwarte okno **Podsumowanie zadania gałęzi.**

6. Wybierz **odśwież,** aby odświeżyć podsumowanie, dopóki **stan zadania** nie zmieni się na **Zakończone**. Aby wyświetlić dane wyjściowe zadania, wybierz pozycję **Wyjście zadania**.

## <a name="run-an-apache-pig-job"></a>Uruchamianie zadania Świni Apache

Można również uruchomić zadanie Pig, który używa aplikacji Świnka UDF.

1. Użyj SSH, aby połączyć się z klastrem HDInsight. (Na przykład uruchom `ssh sshuser@<clustername>-ssh.azurehdinsight.net`polecenie .) Aby uzyskać więcej informacji, zobacz [Korzystanie z funkcji SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj następującego polecenia, aby uruchomić wiersz polecenia Świnia:

    ```shell
    pig
    ```

    Zostanie `grunt>` wyświetlony monit.

3. Wprowadź następujące, aby uruchomić zadanie Pig, które używa aplikacji .NET Framework:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    Instrukcja `DEFINE` tworzy alias `streamer` dla aplikacji *PigUDF.exe* i `CACHE` ładuje go z domyślnego magazynu dla klastra. Później `streamer` jest używany `STREAM` z operatorem do przetwarzania `LOG` pojedynczych wierszy zawartych w i zwracać dane jako serię kolumn.

    > [!NOTE]
    > Nazwa aplikacji używana do przesyłania strumieniowego musi \` być otoczona znakiem (backtick) podczas aliasowania i znakiem `SHIP`' (pojedynczy cudzysłów) używanym z programem .

4. Po wprowadzeniu ostatniego wiersza zadanie powinno się rozpocząć. Zwraca dane wyjściowe podobne do następującego tekstu:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Użyj, `exit` aby wyjść świni.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak używać aplikacji .NET Framework z hive i pig na HDInsight. Jeśli chcesz dowiedzieć się, jak używać Pythona z Hive i Pig, zobacz [Korzystanie z Pythona z Ulem Apache i Apache Pig w HDInsight](python-udf-hdinsight.md).

Aby uzyskać inne sposoby używania gałęzi i dowiedzenia się więcej o korzystaniu z mapReduce, zobacz następujące artykuły:

* [Korzystanie z programu Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z mapReduce z HDInsight](hdinsight-use-mapreduce.md)
* [Podstawy łaciny wieprzowej](https://pig.apache.org/docs/latest/basic.html)