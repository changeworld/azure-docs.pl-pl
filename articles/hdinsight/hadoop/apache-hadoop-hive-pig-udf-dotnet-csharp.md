---
title: Za pomocą języka C# Hive i Pig na platformie Hadoop w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak funkcji języka C# zdefiniowane przez użytkownika (UDF) z Hive i Pig przesyłania strumieniowego w usłudze Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: b776b214b9d46293a3ab97ad226dffd3107f3430
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>C# zdefiniowane przez użytkownika funkcji za pomocą technologii Hive i Pig przesyłania strumieniowego na platformie Hadoop w usłudze HDInsight

Dowiedz się, jak funkcji języka C# zdefiniowane przez użytkownika (UDF) z Apache Hive i Pig w usłudze HDInsight.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie pracy z klastrami usługi HDInsight opartych na systemie Linux, a także z systemem Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składnika usługi HDInsight](../hdinsight-component-versioning.md).

Zarówno Hive i Pig można przekazać dane do aplikacji zewnętrznych do przetwarzania. Ten proces jest nazywany _przesyłania strumieniowego_. Podczas korzystania z aplikacji .NET, dane są przekazywane do aplikacji na STDIN, a aplikacja zwraca wyniki ze strumienia STDOUT. Odczyt i zapis z STDIN i STDOUT, można użyć `Console.ReadLine()` i `Console.WriteLine()` z aplikacji konsoli.

## <a name="prerequisites"></a>Wymagania wstępne

* Znajomość pisania i kompilowania kodu C#, przeznaczonego dla programu .NET Framework 4.5.

    * Użyj dowolnej IDE ma. Firma Microsoft zaleca [programu Visual Studio](https://www.visualstudio.com/vs) 2015, 2017, lub [Visual Studio Code](https://code.visualstudio.com/). Kroki opisane w tym dokumencie, użyj programu Visual Studio 2017 r.

* Sposób, aby przekazać pliki .exe do wykonywania zadań Pig i Hive i klastra. Zalecamy narzędzi Data Lake Tools dla programu Visual Studio, programu Azure PowerShell i interfejsu wiersza polecenia Azure. Kroki opisane w tym dokumencie narzędzi Data Lake Tools dla programu Visual Studio przekazać pliki, a następnie uruchomić przykład Hive zapytania.

    Aby uzyskać informacje na inne sposoby uruchamiania gałąź rejestru, kwerendy i zadań Pig, można znaleźć w następujących dokumentach:

    * [Use Apache Hive z usługą HDInsight](hdinsight-use-hive.md)

    * [Use Apache Pig z usługą HDInsight](hdinsight-use-pig.md)

* Hadoop w klastrze usługi HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [tworzenia klastra usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET w usłudze HDInsight

* __HDInsight opartych na systemie Linux__ klastrów za pomocą [Mono (https://mono-project.com) ](https://mono-project.com) na uruchamianie aplikacji .NET. Wersja mono 4.2.1 jest uwzględniona w usłudze HDInsight w wersji 3,6.

    Aby uzyskać więcej informacji na Mono zgodność z wersji systemu .NET Framework, zobacz [Mono zgodności](http://www.mono-project.com/docs/about-mono/compatibility/).

    Aby użyć określonej wersji Mono, zobacz [instalacji lub aktualizacji Mono](../hdinsight-hadoop-install-mono.md) dokumentu.

* __HDInsight opartych na systemie Windows__ klastrów umożliwia uruchamianie aplikacji .NET programu Microsoft .NET CLR.

Aby uzyskać więcej informacji o wersji programu .NET framework i Mono dołączone do wersji usługi HDInsight, zobacz [wersji składnika usługi HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Utwórz C\# projektów

### <a name="hive-udf"></a>Hive funkcji zdefiniowanej przez użytkownika

1. Otwórz program Visual Studio i utworzyć rozwiązanie. Dla typu projektu, zaznacz **aplikacji konsoli (.NET Framework)**, a nazwa nowego projektu **HiveCSharp**.

    > [!IMPORTANT]
    > Wybierz __.NET Framework 4.5__ korzystania z klastra usługi HDInsight opartej na systemie Linux. Aby uzyskać więcej informacji na Mono zgodność z wersji systemu .NET Framework, zobacz [Mono zgodności](http://www.mono-project.com/docs/about-mono/compatibility/).

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

### <a name="pig-udf"></a>Pig funkcji zdefiniowanej przez użytkownika

1. Otwórz program Visual Studio i utworzyć rozwiązanie. Dla typu projektu, zaznacz **aplikacji konsoli**, a nazwa nowego projektu **PigUDF**.

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

    Ten kod analizuje wysłanych z Pig wierszy i formatuje wiersze rozpoczynające się od `java.lang.Exception`.

3. Zapisz **Program.cs**, a następnie skompilować projekt.

## <a name="upload-to-storage"></a>Przekaż do magazynu

1. W programie Visual Studio Otwórz **Eksploratora serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Jeśli zostanie wyświetlony monit, wprowadź swoje poświadczenia subskrypcji platformy Azure, a następnie kliknij przycisk **logowania**.

4. Rozwiń węzeł klastra usługi HDInsight, którą chcesz wdrożyć tę aplikację. Wpis z tekstem __(domyślne konto magazynu)__ ma na liście.

    ![Konta magazynu dla klastra przedstawiający Eksploratora serwera](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Jeśli tego wpisu można rozszerzać, używasz __konta magazynu Azure__ jako domyślny magazyn dla klastra. Aby wyświetlić pliki na domyślny magazyn dla klastra, rozwiń pozycję, a następnie kliknij dwukrotnie __(domyślny kontener)__.

    * Jeśli tego wpisu nie można rozwijać, używasz __Azure Data Lake Store__ jako domyślny magazyn dla klastra. Aby wyświetlić pliki na domyślny magazyn dla klastra, kliknij dwukrotnie __(domyślne konto magazynu)__ wpisu.

6. Aby przekazać pliki .exe, użyj jednej z następujących metod:

    * Jeśli przy użyciu __konta magazynu Azure__, kliknij ikonę przekazywania, a następnie przejdź do **bin\debug** folder **HiveCSharp** projektu. Na koniec wybierz **HiveCSharp.exe** plik i kliknij przycisk **Ok**.

        ![Przekaż ikonę](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Jeśli przy użyciu __Azure Data Lake Store__, kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz __przekazać__. Na koniec wybierz **HiveCSharp.exe** plik i kliknij przycisk **Otwórz**.

    Raz __HiveCSharp.exe__ przekazywania została ukończona, powtórz proces przekazywania __PigUDF.exe__ pliku.

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

1. W programie Visual Studio Otwórz **Eksploratora serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Kliknij prawym przyciskiem myszy klaster, do którego wdrożono **HiveCSharp** do aplikacji, a następnie wybierz **Napisz zapytanie Hive**.

4. Dla zapytania Hive, należy użyć następującego tekstu:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Store
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Usuń znaczniki komentarza `add file` instrukcji, która jest zgodny z typem magazyny domyślne używane dla klastra.

    To zapytanie wybiera `clientid`, `devicemake`, i `devicemodel` pola z `hivesampletable`i przekazuje do aplikacji HiveCSharp.exe pola. Zapytanie oczekuje aplikacji, aby powrócić trzy pola, które są przechowywane jako `clientid`, `phoneLabel`, i `phoneHash`. Zapytanie oczekuje znaleźć HiveCSharp.exe w folderze głównym domyślnego kontenera magazynu.

5. Kliknij przycisk **przesyłania** można przesłać zadania w klastrze usługi HDInsight. **Podsumowanie zadania Hive** zostanie otwarte okno.

6. Kliknij przycisk **Odśwież** odświeżyć podsumowanie do **stan zadania** zmienia się na **Ukończono**. Aby wyświetlić dane wyjściowe zadania, kliknij przycisk **dane wyjściowe zadania**.

## <a name="run-a-pig-job"></a>Uruchom zadanie Pig

1. Aby nawiązać połączenie z klastrem usługi HDInsight, użyj jednej z następujących metod:

    * Jeśli używasz __opartych na systemie Linux__ HDInsight klastra, należy użyć protokołu SSH. Na przykład `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Aby uzyskać więcej informacji, zobacz [withHDInsight używanie protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * Jeśli używasz __opartych na systemie Windows__ klastra usługi HDInsight [Połącz z klastrem przy użyciu pulpitu zdalnego](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)

2. Użyj jednym następującego polecenia można uruchomić z wiersza polecenia Pig:

        pig

    > [!IMPORTANT]
    > Jeśli korzystasz z klastra z systemem Windows, należy użyć następujących poleceń:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    A `grunt>` zostanie wyświetlony monit.

3. Wprowadź następujące polecenie, aby uruchomić zadanie Pig, który korzysta z aplikacji .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    `DEFINE` Instrukcja tworzy alias `streamer` dla aplikacji pigudf.exe i `CACHE` ładuje go z domyślny magazyn dla klastra. Później `streamer` jest używany z `STREAM` operatora, aby przetworzyć pojedynczych wierszy zawartych w dzienniku i zwraca dane jako serię kolumn.

    > [!NOTE]
    > Nazwa aplikacji, która jest używana do przesyłania strumieniowego musi być ujęte w \` (backtick) gdy znak, ponieważ i ' (pojedynczy cudzysłów) w przypadku użycia z `SHIP`.

4. Po wprowadzeniu ostatni wiersz, należy uruchomić zadanie. Zwraca dane wyjściowe podobne do następującego tekstu:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie mają pokazaliśmy, jak korzystać z aplikacji .NET Framework z Hive i Pig w usłudze HDInsight. Jeśli chcesz dowiedzieć się, jak używać języka Python z Hive i Pig, zobacz [Użyj Python z Hive i Pig w usłudze HDInsight](python-udf-hdinsight.md).

Inne sposoby używania Pig i Hive i informacje o używaniu MapReduce można znaleźć w następujących dokumentach:

* [Korzystanie z programu Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hdinsight-use-pig.md)
* [Korzystać z usługi MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
