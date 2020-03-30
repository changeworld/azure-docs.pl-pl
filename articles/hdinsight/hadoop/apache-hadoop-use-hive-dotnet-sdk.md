---
title: Uruchamianie zapytań gałęzi Apache przy użyciu sdk .NET usługi HDInsight — Azure
description: Dowiedz się, jak przesłać zadania Apache Hadoop do usługi Azure HDInsight Apache Hadoop przy użyciu funkcji HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: a9d71c8aebb9cc4a0adbd461aead6e2612bd13bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552495"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Uruchamianie zapytań gałęzi Apache przy użyciu sdk HDInsight .NET

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak przesłać zapytania gałęzi Apache przy użyciu sdk .NET usługi HDInsight. Piszesz program C# przesłać zapytanie hive do wyświetlania tabel hive i wyświetlić wyniki.

> [!NOTE]  
> Kroki opisane w tym artykule muszą być wykonywane z klienta systemu Windows. Aby uzyskać informacje na temat korzystania z klienta Systemu Linux, OS X lub Unix do pracy z hive, użyj selektora kart pokazanego u góry artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu musisz mieć następujące elementy:

* Klaster Apache Hadoop w hdinsight. Zobacz [Wprowadzenie do korzystania z systemu Linux Hadoop w systemie HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > Od 15 września 2017 r. zestaw SDK .NET usługi HDInsight obsługuje tylko zwracanie wyników zapytań hive z kont usługi Azure Storage. Jeśli w tym przykładzie używasz klastra HDInsight, który używa usługi Azure Data Lake Storage jako magazynu podstawowego, nie można pobrać wyników wyszukiwania przy użyciu zestawu SDK .NET.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 i nie tylko. Należy zainstalować co najmniej obciążenie **programistyczne dla komputerów .NET.**

## <a name="run-a-hive-query"></a>Uruchamianie kwerendy gałęzi

Zestawu HDInsight .NET SDK udostępnia biblioteki klienckie platformy .NET, co ułatwia pracę z klastrami HDInsight z platformy .NET.

1. Tworzenie aplikacji konsoli języka C# w programie Visual Studio.

1. W konsoli Menedżera pakietów Nuget uruchom następujące polecenie:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

1. Edytuj poniższy kod, aby zainicjować `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName`wartości zmiennych: . Następnie użyj poprawionego kodu jako całej zawartości **Program.cs** w programie Visual Studio.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. Naciśnij **klawisz F5,** aby uruchomić aplikację.

Dane wyjściowe aplikacji powinny być podobne do:

![Wyjście zadania HDInsight Hadoop Hive](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak przesyłać zapytania apache hive przy użyciu pliku HDInsight .NET SDK. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wprowadzenie do usługi Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Tworzenie klastrów Apache Hadoop w udziale HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Odwołanie do SDK .NET w formacie HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Korzystanie z programu Apache Sqoop z usługą HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Tworzenie aplikacji usługi HDInsight platformy .NET z uwierzytelnianiem nieinterakcyjnym](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
