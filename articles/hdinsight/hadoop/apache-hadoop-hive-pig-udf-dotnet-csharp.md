---
title: Korzystanie C# z usług Apache Hive i Apache chlewnej na Apache Hadoop w usłudze HDInsight — Azure
description: Dowiedz się, C# jak używać funkcji zdefiniowanych przez użytkownika (UDF) z Apache Hive i Apache chlewnej streaming w usłudze Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: c05e72cd28c78b26b7c23a123b133d10147a4421
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810786"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Korzystanie C# z funkcji zdefiniowanych przez użytkownika z usługami Apache Hive i Apache chlewnej na Apache Hadoop w usłudze HDInsight

Dowiedz się, C# jak używać funkcji zdefiniowanych przez użytkownika (UDF) z usługami Apache Hive i Apache chlewnej w usłudze HDInsight.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie działają zarówno w przypadku klastrów usługi HDInsight opartych na systemie Linux, jak i Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składników usługi HDInsight](../hdinsight-component-versioning.md).

Zarówno Hive, jak i świnie mogą przekazywać dane do aplikacji zewnętrznych do przetworzenia. Ten proces jest nazywany _przesyłaniem strumieniowym_. W przypadku korzystania z aplikacji .NET dane są przesyłane do aplikacji w STDIN, a aplikacja zwraca wyniki na STDOUT. Aby odczytać i zapisać dane z stdin i stdout, można użyć `Console.ReadLine()` i `Console.WriteLine()` z aplikacji konsolowej.

## <a name="prerequisites"></a>Wymagania wstępne

* Znajomość pisania i kompilowania C# kodu, który jest przeznaczony dla .NET Framework 4,5.

    * Używaj dowolnego środowiska IDE. Zalecamy korzystanie z [programu Visual Studio](https://www.visualstudio.com/vs) 2015, 2017 lub [Visual Studio Code](https://code.visualstudio.com/). W procedurach przedstawionych w tym dokumencie użyto programu Visual Studio 2017.

* Sposób przekazywania plików exe do klastra i uruchamiania zadań dotyczących trzody chlewnej i Hive. Zalecamy korzystanie z Data Lake narzędzi dla programu Visual Studio, Azure PowerShell i klasycznego interfejsu wiersza polecenia platformy Azure. Kroki opisane w tym dokumencie wykorzystują Data Lake Tools for Visual Studio do przekazywania plików i uruchamiania przykładowego zapytania Hive.

    Aby uzyskać informacje na temat innych sposobów uruchamiania zapytań Hive i zadań związanych z świnią, zobacz następujące dokumenty:

    * [Korzystanie z Apache Hive z usługą HDInsight](hdinsight-use-hive.md)

    * [Korzystanie z Apache świni z usługą HDInsight](hdinsight-use-pig.md)

* Usługa Hadoop w klastrze usługi HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [Tworzenie klastra usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>Platforma .NET w usłudze HDInsight

* Klastry __HDInsight oparte na systemie Linux__ używające systemu [https://mono-project.com) mono (](https://mono-project.com) do uruchamiania aplikacji .NET. W usłudze HDInsight w wersji 3,6 jest dołączony system mono w wersji.

    Aby uzyskać więcej informacji na temat zgodności z programem mono z wersjami .NET Framework, zobacz Zgodność z usługą [mono](https://www.mono-project.com/docs/about-mono/compatibility/).

* Klastry __HDInsight oparte na systemie Windows__ używają środowiska CLR Microsoft .NET do uruchamiania aplikacji platformy .NET.

Aby uzyskać więcej informacji na temat wersji programu .NET Framework i narzędzia mono dołączonego do wersji usługi HDInsight, zobacz [wersje składników usługi HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Tworzenie projektów C\#

### <a name="apache-hive-udf"></a>Apache Hive UDF

1. Otwórz program Visual Studio i Utwórz rozwiązanie. W polu Typ projektu wybierz pozycję **aplikacja konsoli (.NET Framework)** , a następnie nadaj nazwę nowemu projektowi **HiveCSharp**.

    > [!IMPORTANT]
    > Wybierz __.NET Framework 4,5__ , jeśli używasz klastra usługi HDInsight opartego na systemie Linux. Aby uzyskać więcej informacji na temat zgodności z programem mono z wersjami .NET Framework, zobacz Zgodność z usługą [mono](https://www.mono-project.com/docs/about-mono/compatibility/).

2. Zastąp zawartość **program.cs** następującym kodem:

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

### <a name="apache-pig-udf"></a>UDF świni Apache

1. Otwórz program Visual Studio i Utwórz rozwiązanie. Dla typu projektu wybierz pozycję **Aplikacja konsolowa**i nadaj nazwę nowemu projektowi **PigUDF**.

2. Zastąp zawartość pliku **program.cs** następującym kodem:

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

    Ten kod analizuje wiersze wysyłane z świni i ponownie formatuje linie, które zaczynają `java.lang.Exception`się od.

3. Zapisz **program.cs**, a następnie Skompiluj projekt.

## <a name="upload-to-storage"></a>Przekazywanie do magazynu

1. W programie Visual Studio Otwórz **Eksplorator serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Jeśli zostanie wyświetlony monit, wprowadź swoje poświadczenia subskrypcji platformy Azure, a następnie kliknij przycisk **Zaloguj**.

4. Rozwiń klaster usługi HDInsight, do którego chcesz wdrożyć tę aplikację. Zostanie wyświetlona pozycja z tekstem __(domyślne konto magazynu)__ .

    ![Eksplorator serwera wyświetlania konta magazynu dla klastra](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Jeśli ten wpis można rozszerzyć, używasz __konta usługi Azure Storage__ jako magazynu domyślnego dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, rozwiń wpis, a następnie kliknij dwukrotnie __(kontener domyślny)__ .

    * Jeśli tego wpisu nie można rozszerzyć, używany jest __Azure Data Lake Storage__ jako magazyn domyślny dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, kliknij dwukrotnie wpis __(domyślne konto magazynu)__ .

6. Aby przekazać pliki. exe, należy użyć jednej z następujących metod:

   * Jeśli używasz __konta usługi Azure Storage__, kliknij ikonę Przekaż, a następnie przejdź do folderu **Bin\debug** dla projektu **HiveCSharp** . Na koniec wybierz plik **HiveCSharp. exe** , a następnie kliknij przycisk **OK**.

       ![ikona przekazywania](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
   * Jeśli używasz __Azure Data Lake Storage__, kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz polecenie __Przekaż__. Na koniec wybierz plik **HiveCSharp. exe** , a następnie kliknij przycisk **Otwórz**.

     Po zakończeniu przekazywania programu __HiveCSharp. exe__ Powtórz proces przekazywania dla pliku __PigUDF. exe__ .

## <a name="run-an-apache-hive-query"></a>Uruchamianie zapytania Apache Hive

1. W programie Visual Studio Otwórz **Eksplorator serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Kliknij prawym przyciskiem myszy klaster, w którym wdrożono aplikację **HiveCSharp** , a następnie wybierz polecenie **Napisz zapytanie Hive**.

4. Użyj następującego tekstu zapytania programu Hive:

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
    > Usuń komentarz z `add file` instrukcji, która jest zgodna z typem domyślnego magazynu używanego w klastrze.

    To zapytanie wybiera `clientid`pola, `devicemake`, i `devicemodel` z `hivesampletable`i przekazuje pola do aplikacji HiveCSharp. exe. Zapytanie oczekuje, że aplikacja zwróci trzy pola, które są przechowywane jako `clientid`, `phoneLabel`i `phoneHash`. Zapytanie oczekuje również znalezienia HiveCSharp. exe w katalogu głównym domyślnego kontenera magazynu.

5. Kliknij pozycję **Prześlij** , aby przesłać zadanie do klastra usługi HDInsight. Zostanie otwarte okno **podsumowania zadania Hive** .

6. Kliknij przycisk **Odśwież** , aby odświeżyć podsumowanie do czasu **zakończenia**zmiany **stanu zadania** . Aby wyświetlić dane wyjściowe zadania, kliknij pozycję **dane wyjściowe zadania**.

## <a name="run-an-apache-pig-job"></a>Uruchamianie zadania Apache świni

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Na przykład `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Aby uzyskać więcej informacji, zobacz Korzystanie z protokołu [SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Użyj jednego z następujących poleceń, aby uruchomić wiersz polecenia świni:

        pig

    > [!IMPORTANT]
    > Jeśli używasz klastra opartego na systemie Windows, zamiast tego użyj następujących poleceń:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Zostanie `grunt>` wyświetlony monit.

3. Wprowadź następujące, aby uruchomić zadanie trzody chlewnej używające aplikacji .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    Instrukcja tworzy alias dla aplikacji pigudf. exe i `CACHE` ładuje go z magazynu domyślnego dla klastra. `streamer` `DEFINE` Później jest używany `STREAM` z operatorem do przetwarzania pojedynczych wierszy zawartych w dzienniku i zwracania danych jako serii kolumn. `streamer`

    > [!NOTE]
    > Nazwa aplikacji, która jest używana na potrzeby przesyłania strumieniowego, musi być \` otoczona znakiem (odwróconym znacznikiem), gdy jest on używany w połączeniu z `SHIP`.

4. Po wprowadzeniu ostatniego wiersza zadanie powinno zostać uruchomione. Zwraca dane wyjściowe podobne do następującego tekstu:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korzystania z aplikacji .NET Framework z usługi Hive i trzody chlewnej w usłudze HDInsight. Jeśli chcesz dowiedzieć się, jak używać języka Python z usługą Hive i świnią, zobacz [Używanie języka Python z usługami Apache Hive i Apache chlewnej w usłudze HDInsight](python-udf-hdinsight.md).

Aby zapoznać się z innymi sposobami korzystania z funkcji świni i Hive oraz uzyskać informacje na temat korzystania z usługi MapReduce, zobacz następujące dokumenty:

* [Korzystanie z Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z Apache świni z usługą HDInsight](hdinsight-use-pig.md)
* [Korzystanie z MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
