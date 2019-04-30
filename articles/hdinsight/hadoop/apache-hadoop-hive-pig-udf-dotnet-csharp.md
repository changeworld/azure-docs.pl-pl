---
title: Użyj C# za pomocą programu Apache Hive i Apache Pig na technologii Apache Hadoop w HDInsight — Azure
description: Dowiedz się, jak używać C# funkcje zdefiniowane przez użytkownika (UDF) przy użyciu Apache Hive i Apache Pig przesyłania strumieniowego w usłudze Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 31738c43756da14ba6c2c92afbcb2882561c8001
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121954"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-streaming-on-apache-hadoop-in-hdinsight"></a>Użyj C# funkcje zdefiniowane przez użytkownika przy użyciu Apache Hive i Apache Pig, przesyłanie strumieniowe na technologii Apache Hadoop w HDInsight

Dowiedz się, jak używać C# funkcje zdefiniowane przez użytkownika (UDF) przy użyciu Apache Hive i Pig Apache na HDInsight.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie działają z klastrami HDInsight opartych na systemie Linux i systemem Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składnika HDInsight](../hdinsight-component-versioning.md).

Zarówno Hive i Pig można przekazać danych do aplikacji zewnętrznych do przetworzenia. Ten proces jest nazywany _przesyłania strumieniowego_. Korzystając z aplikacji platformy .NET, dane są przekazywane do aplikacji na STDIN, a aplikacja zwraca wyniki ze strumienia STDOUT. Odczyt i zapis z STDIN i STDOUT, umożliwia `Console.ReadLine()` i `Console.WriteLine()` z aplikacji konsoli.

## <a name="prerequisites"></a>Wymagania wstępne

* Znajomość pisanie i kompilowanie kodu C#, który jest przeznaczony dla .NET Framework 4.5.

    * Użyj dowolnego środowiska IDE ma. Firma Microsoft zaleca [programu Visual Studio](https://www.visualstudio.com/vs) 2015, 2017, lub [programu Visual Studio Code](https://code.visualstudio.com/). W krokach w tym dokumencie używane programu Visual Studio 2017.

* Sposób przekazywania plików .exe do klastra i uruchamianie zadań programów Pig i Hive. Firma Microsoft zaleca narzędzi Data Lake Tools for Visual Studio, programu Azure PowerShell i klasycznego wiersza polecenia platformy Azure. Kroki opisane w tym dokumencie używane narzędzia Data Lake Tools for Visual Studio, aby przekazać pliki i uruchamiać w przykładzie zapytanie programu Hive.

    Aby uzyskać informacji na temat innych sposobów, aby uruchomić gałąź rejestru, kwerendy i zadania Pig, zobacz następujące dokumenty:

    * [Use Apache Hive z HDInsight](hdinsight-use-hive.md)

    * [Apache Pig za pomocą HDInsight](hdinsight-use-pig.md)

* Usługi Hadoop w klastrze HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [Tworzenie klastra usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET na HDInsight

* __HDInsight opartych na systemie Linux__ klastrów przy użyciu [Mono (https://mono-project.com) ](https://mono-project.com) do uruchamiania aplikacji .NET. Wersja platformy mono 4.2.1 jest dołączony do HDInsight w wersji 3.6.

    Aby uzyskać więcej informacji na temat zgodności platformy Mono z wersji systemu .NET Framework, zobacz [zgodności platformy Mono](https://www.mono-project.com/docs/about-mono/compatibility/).

* __HDInsight oparte na Windows__ klastry używają programu Microsoft .NET CLR do uruchamiania aplikacji .NET.

Aby uzyskać więcej informacji na temat wersji programu .NET framework i Mono dołączone wersji HDInsight, zobacz [wersje składników HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Utwórz C\# projektów

### <a name="apache-hive-udf"></a>Apache Hive funkcji zdefiniowanej przez użytkownika

1. Otwórz program Visual Studio i utworzyć rozwiązanie. Dla typów projektów wybierz **Aplikacja konsoli (.NET Framework)** i nadaj nazwę nowego projektu **HiveCSharp**.

    > [!IMPORTANT]
    > Wybierz __.NET Framework 4.5__ korzystania z klastrów HDInsight opartych na systemie Linux. Aby uzyskać więcej informacji na temat zgodności platformy Mono z wersji systemu .NET Framework, zobacz [zgodności platformy Mono](https://www.mono-project.com/docs/about-mono/compatibility/).

2. Zastąp zawartość **Program.cs** następującym kodem:

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

3. Skompiluj projekt.

### <a name="apache-pig-udf"></a>Apache Pig UDF

1. Otwórz program Visual Studio i utworzyć rozwiązanie. Dla typów projektów wybierz **aplikację Konsolową**i nadaj nazwę nowego projektu **PigUDF**.

2. Zastąp zawartość **Program.cs** pliku następującym kodem:

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

    Ten kod analizuje wiersze wysyłane z języka Pig i formatuje wierszy, które zaczynają się od `java.lang.Exception`.

3. Zapisz **Program.cs**, a następnie Skompiluj projekt.

## <a name="upload-to-storage"></a>Przekazywanie do magazynu

1. W programie Visual Studio, otwórz **Eksploratora serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Po wyświetleniu monitu wprowadź swoje poświadczenia subskrypcji platformy Azure, a następnie kliknij przycisk **Sign In**.

4. Rozwiń węzeł klastra HDInsight, który chcesz wdrożyć tę aplikację. Wpis z tekstem __(domyślne konto magazynu)__ znajduje się na liście.

    ![Konto magazynu dla klastra Eksplorator serwera](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Jeśli ten wpis można rozszerzać, używasz __konta usługi Azure Storage__ jako magazynem domyślnym dla klastra. Aby wyświetlić pliki na domyślny magazyn dla klastra, rozwiń pozycję, a następnie kliknij dwukrotnie __(domyślny kontener)__.

    * Jeśli tego wpisu nie może zostać rozszerzona, którego używasz __usługi Azure Data Lake Storage__ jako magazynem domyślnym dla klastra. Aby wyświetlić pliki na domyślny magazyn dla klastra, kliknij dwukrotnie __(domyślne konto magazynu)__ wpisu.

6. Aby przekazać pliki .exe, użyj jednej z następujących metod:

   * Jeśli przy użyciu __konta usługi Azure Storage__, kliknij ikonę przekazywania, a następnie przejdź do **bin\debug** folder **HiveCSharp** projektu. Na koniec wybierz pozycję **HiveCSharp.exe** plik i kliknij przycisk **Ok**.

       ![Przekaż ikonę](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
   * Jeśli przy użyciu __usługi Azure Data Lake Storage__, kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz __przekazywanie__. Na koniec wybierz pozycję **HiveCSharp.exe** plik i kliknij przycisk **Otwórz**.

     Gdy __HiveCSharp.exe__ przekazywanie zostało zakończone, powtórz proces przekazywania __PigUDF.exe__ pliku.

## <a name="run-an-apache-hive-query"></a>Uruchom zapytanie, Apache Hive

1. W programie Visual Studio, otwórz **Eksploratora serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Kliknij prawym przyciskiem myszy klaster, która została wdrożona **HiveCSharp** aplikacji do, a następnie wybierz **Napisz zapytanie Hive**.

4. Skorzystaj z poniższego tekstu dla zapytania programu Hive:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
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
    > Usuń znaczniki komentarza `add file` instrukcję, która jest zgodny z typem domyślnego magazynu używane dla klastra.

    To zapytanie wybiera `clientid`, `devicemake`, i `devicemodel` pola z `hivesampletable`i przekazuje pola do aplikacji HiveCSharp.exe. Zapytanie oczekuje, że aplikacja do zwrócenia trzy pola, które są przechowywane jako `clientid`, `phoneLabel`, i `phoneHash`. Zapytanie jest również spodziewa się znaleźć HiveCSharp.exe w katalogu głównym domyślnego kontenera magazynu.

5. Kliknij przycisk **przesyłania** można przesłać zadania do klastra HDInsight. **Podsumowanie zadania Hive** zostanie otwarte okno.

6. Kliknij przycisk **Odśwież** odświeżyć podsumowanie aż do **stan zadania** zmieni się na **Ukończono**. Aby wyświetlić dane wyjściowe zadania, kliknij **dane wyjściowe zadania**.

## <a name="run-an-apache-pig-job"></a>Uruchom zadanie Apache Pig

1. Za pomocą protokołu SSH Połącz się z klastrem usługi HDInsight. Na przykład `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Aby uzyskać więcej informacji, zobacz [withHDInsight używanie protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Użyj jednej polecenie można uruchomić z wiersza polecenia Pig:

        pig

    > [!IMPORTANT]
    > Jeśli używasz klastra z systemem Windows, należy użyć następujących poleceń:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    A `grunt>` zostanie wyświetlony monit.

3. Wprowadź następujące polecenie, aby uruchomić zadania Pig, który używa aplikacji .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    `DEFINE` Instrukcja tworzy alias `streamer` dla aplikacji pigudf.exe i `CACHE` ładuje go z domyślnego magazynu klastra. Później `streamer` jest używana z `STREAM` operatora pojedynczych wierszy zawartych w dzienniku przetwarzania i zwrócić dane jako serię kolumn.

    > [!NOTE]
    > Nazwa aplikacji, który służy do przesyłania strumieniowego musi być ujęte w \` (początkowych) znaków, kiedy aliasem, i "(pojedynczy cudzysłów) gdy jest używane z `SHIP`.

4. Po wprowadzeniu ostatni wiersz, zadanie powinno zostać uruchomione. Zwraca dane wyjściowe podobne do następującego tekstu:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie mają przedstawiono sposób użycia aplikacji programu .NET Framework z programów Hive i Pig na HDInsight. Jeśli chcesz dowiedzieć się, jak używać języka Python z technologiami Hive i Pig, zobacz [używać języka Python przy użyciu Apache Hive i Apache Pig w HDInsight](python-udf-hdinsight.md).

Inne sposoby używania programów Pig i Hive i Dowiedz się więcej o korzystaniu z MapReduce na ten temat można znaleźć w następujących dokumentach:

* [Use Apache Hive z HDInsight](hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hdinsight-use-mapreduce.md)
