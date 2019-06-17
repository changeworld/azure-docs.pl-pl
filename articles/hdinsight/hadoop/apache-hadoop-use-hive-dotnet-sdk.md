---
title: Uruchamianie zapytania usługi Apache Hive przy użyciu HDInsight platformy .NET SDK — platformy Azure
description: Dowiedz się, jak można przesłać zadania technologii Apache Hadoop do usługi Azure HDInsight Apache Hadoop przy użyciu zestawu .NET SDK HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 947e797842000e4da2f9e22077bc32c24d6c6a74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718838"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Uruchamianie zapytania usługi Apache Hive przy użyciu zestawu .NET SDK HDInsight
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak przesyłać zapytania usługi Apache Hive przy użyciu zestawu .NET SDK HDInsight. Napisz program C#, można przesłać zapytania programu Hive w taki sposób, aby uzyskać listę tabel programu Hive i wyświetlić wyniki.

> [!NOTE]  
> Kroki opisane w tym artykule, należy wykonać w kliencie Windows. Instrukcje dotyczące używania systemu Linux, OS X lub klienta systemu Unix do pracy z programu Hive selektor karty wyświetlane w górnej części tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonywania w tym artykule, musi mieć następujące elementy:

* **Klaster platformy Apache Hadoop w HDInsight**. Zobacz [rozpoczęcie korzystania z opartą na systemie Linux platformą Hadoop w HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]  
    > Od 15 września 2017 r. zestawu .NET SDK HDInsight obsługuje tylko zwracanie wyników zapytania programu Hive z konta usługi Azure Storage. Jeśli używasz w tym przykładzie z klastrem usługi HDInsight, który używa usługi Azure Data Lake Storage jako magazynu podstawowego, nie można pobrać wyników wyszukiwania za pomocą zestawu .NET SDK.

* **Visual Studio 2013/2015/2017**.

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive
Zestaw .NET SDK HDInsight zawiera biblioteki klienckie programu .NET, dzięki czemu łatwiej jest pracować z klastrami HDInsight za pomocą platformy .NET. 

**Aby przesłać zadania**

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio.
2. Z poziomu konsoli Menedżera pakietów Nuget uruchom następujące polecenie:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Użyj następującego kodu:

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
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
                
                // Only Azure Storage accounts are supported by the SDK
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
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
4. Naciśnij klawisz **F5**, aby uruchomić aplikację.

Dane wyjściowe aplikacji będą podobne do:

![Dane wyjściowe zadania programu Hive HDInsight Hadoop](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Kolejne kroki
W tym artykule mają przedstawiono kilka sposobów, aby utworzyć klaster usługi HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Rozpoczynanie pracy z usługą Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Tworzenie klastrów usługi Apache Hadoop w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Odwołanie do zestawu SDK .NET HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Apache Pig za pomocą HDInsight](hdinsight-use-pig.md)
* [Za pomocą HDInsight przy użyciu narzędzia Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md)
* [Tworzenie aplikacji usługi HDInsight platformy .NET z uwierzytelnianiem nieinterakcyjnym](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


