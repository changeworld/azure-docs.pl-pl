---
title: Uruchamiać zadania Apache Pig z zestawem SDK .NET dla usługi Hadoop — Azure HDInsight
description: Dowiedz się, jak przesyłać zadania Pig z usługą Hadoop w HDInsight przy użyciu zestawu SDK .NET dla usługi Hadoop.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: hrasheed
ms.openlocfilehash: ebf1f2806a6606294c61860a24fb2f02033a4bf4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688399"
---
# <a name="run-apache-pig-jobs-using-the-net-sdk-for-apache-hadoop-in-hdinsight"></a>Uruchamiać zadania Apache Pig, przy użyciu zestawu .NET SDK dla usługi Apache Hadoop w HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Dowiedz się, jak przesyłać zadania Apache Pig z usługą Hadoop w usłudze Azure HDInsight przy użyciu zestawu SDK .NET dla usługi Apache Hadoop.

Zestaw .NET SDK HDInsight zawiera biblioteki klienckie programu .NET, które sprawia, że łatwiej pracować z klastrami HDInsight za pomocą platformy .NET. Pig służy do tworzenia działania MapReduce, modelowanie szereg przekształcenia danych. W tym dokumencie dowiesz się, jak użyć Podstawowa aplikacja języka C# można przesłać zadania technologii Pig w klastrze usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące elementy.

* Klaster usługi Azure HDInsight (Hadoop w HDInsight) (albo Windows lub opartych na systemie Linux).

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Program Visual Studio 2012, 2013, 2015 lub 2017.

## <a name="create-the-application"></a>Tworzenie aplikacji

Zestaw .NET SDK HDInsight zawiera biblioteki klienckie programu .NET, dzięki czemu łatwiej jest pracować z klastrami HDInsight za pomocą platformy .NET.

1. Z **pliku** menu w programie Visual Studio, wybierz **New** , a następnie wybierz **projektu**.

2. Dla nowego projektu wpisz lub wybierz następujące wartości:

   | Właściwość | Wartość |
   | ------ | ------ |
   | Category | Szablony/Visual C#/Windows |
   | Szablon | Aplikacja konsolowa |
   | Name (Nazwa) | SubmitPigJob |

3. Kliknij przycisk **OK**, aby utworzyć projekt.

4. Z **narzędzia** menu, wybierz opcję **Menedżer pakietów biblioteki** lub **Menedżera pakietów NuGet**, a następnie wybierz pozycję **Konsola Menedżera pakietów**.

5. Aby zainstalować pakiety zestawu SDK platformy .NET, użyj następującego polecenia:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. W Eksploratorze rozwiązań kliknij dwukrotnie **Program.cs** aby go otworzyć. Zastąp istniejący kod poniżej.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. Aby uruchomić aplikację, naciśnij klawisz **F5**.

8. Aby zakończyć aplikację, naciśnij klawisz **ENTER**.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacji na temat technologii Pig w HDInsight, zobacz [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md).

Aby uzyskać więcej informacji na temat korzystania z usługi Hadoop w HDInsight zobacz następujące dokumenty:

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)
* [Korzystanie z technologii MapReduce z platformą Apache Hadoop w HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
