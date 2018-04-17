---
title: Uruchamianie zapytań Hive przy użyciu zestawu SDK programu .NET HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak umożliwiają przesyłanie zadań Hadoop do usługi Azure HDInsight Hadoop przy użyciu zestawu SDK .NET usługi HDInsight.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 4e291890-f8b4-426c-b5e8-d4fd512ff042
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 558ea75a74b89776be32095a7230f9c6e97dcea2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Uruchamianie zapytań Hive przy użyciu zestawu .NET SDK usługi HDInsight
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak wysyłanie zapytań programu Hive za pomocą zestawu SDK .NET usługi HDInsight. Napisz program C# do przesyłania zapytań programu Hive, wyświetlania list tabele programu Hive i wyświetlić wyniki.

> [!NOTE]
> W kliencie systemu Windows należy wykonać czynności opisane w tym artykule. Informacji o korzystaniu z systemem Linux, OS X lub klienta systemu Unix do pracy z gałęzi selektor karty wyświetlany w górnej części tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego artykułu, musi mieć następujące elementy:

* **Klastra usługi Hadoop w usłudze HDInsight**. Zobacz [Rozpoczynanie pracy z opartą na systemie Linux platformą Hadoop w usłudze HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]
    > Począwszy od 15 września 2017 zestawu .NET SDK HDInsight obsługuje tylko zwracanie wyników zapytania Hive z konta usługi Azure Storage. Jeśli w tym przykładzie użytkownik korzysta z klastra usługi HDInsight, korzystającą z usługi Azure Data Lake Store jako podstawowy, nie można pobrać wyników wyszukiwania za pomocą zestawu .NET SDK.

* **Visual Studio 2013/2015/2017**.

## <a name="run-a-hive-query"></a>Uruchamianie zapytań Hive
Zestawu .NET SDK HDInsight udostępnia biblioteki klienta .NET, co ułatwia do pracy z klastrami HDInsight ze środowiska .NET. 

**Do przesyłania zadań**

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio.
2. Za pomocą konsoli Menedżera pakietów Nuget uruchom następujące polecenie:
   
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

Dane wyjściowe aplikacji jest podobny do:

![Dane wyjściowe zadania HDInsight Hadoop Hive](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Kolejne kroki
W tym artykule uzyskanych kilka sposobów tworzenia klastra usługi HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Rozpoczynanie pracy z usługą Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Tworzenie klastrów Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Zarządzanie klastrami Hadoop w usłudze HDInsight przy użyciu portalu Azure](../hdinsight-administer-use-management-portal.md)
* [Odwołanie do zestawu SDK .NET usługi HDInsight](https://msdn.microsoft.com/library/mt271028.aspx)
* [Korzystanie z języka Pig z usługą HDInsight](hdinsight-use-pig.md)
* [Korzystanie z usługą HDInsight Sqoop](apache-hadoop-use-sqoop-mac-linux.md)
* [Tworzenie aplikacji usługi HDInsight platformy .NET z uwierzytelnianiem nieinterakcyjnym](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


