---
title: Przesyłanie zadań MapReduce przy użyciu zestawu SDK programu .NET HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przesłać zadania MapReduce do usługi Azure HDInsight Hadoop przy użyciu zestawu SDK .NET usługi HDInsight.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 068d63b7c96dd6729ebc46fb92a136be43b84385
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Uruchamianie zadań MapReduce przy użyciu zestawu SDK .NET usługi HDInsight
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Dowiedz się, jak można przesłać zadania MapReduce przy użyciu zestawu SDK .NET usługi HDInsight. HDInsight klastrów pochodzą z pliku jar z niektóre przykłady MapReduce. Plik jar jest */example/jars/hadoop-mapreduce-examples.jar*.  Jedną z próbek jest *wordcount*. Opracowywania aplikacji konsolowej C# można przesłać zadania wordcount.  Odczytuje zadanie */example/data/gutenberg/davinci.txt* pliku i umieszcza wyniki w */example/data/davinciwordcount*.  Jeśli chcesz ponownie uruchomić aplikację, należy wyczyścić folder wyjściowy.

> [!NOTE]
> W kliencie systemu Windows należy wykonać czynności opisane w tym artykule. Informacji o korzystaniu z systemem Linux, OS X lub klienta systemu Unix do pracy z gałęzi selektor karty wyświetlany w górnej części tego artykułu.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego artykułu, musi mieć następujące elementy:

* **Klastra usługi Hadoop w usłudze HDInsight**. Zobacz [Rozpoczynanie pracy z opartą na systemie Linux platformą Hadoop w usłudze HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Przesyłanie zadań MapReduce przy użyciu zestawu SDK .NET usługi HDInsight
Zestawu .NET SDK HDInsight udostępnia biblioteki klienta .NET, co ułatwia do pracy z klastrami HDInsight ze środowiska .NET. 

**Do przesyłania zadań**

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio.
2. Za pomocą konsoli Menedżera pakietów NuGet uruchom następujące polecenie:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. Użyj następującego kodu:

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string existingClusterUsername = "<Cluster Username>";
            private const string existingClusterPassword = "<Cluster User Password>";

            private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";

            private const string sourceFile = "/example/data/gutenberg/davinci.txt";  
            private const string outputFolder = "/example/data/davinciwordcount";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);

                SubmitMRJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };

                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + 
                        defaultStorageAccountName + 
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); 
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }
    ```

4. Naciśnij klawisz **F5**, aby uruchomić aplikację.

Aby ponownie uruchomić zadanie, należy zmienić nazwę folderu danych wyjściowych zadania w przykładzie jest "/ przykład/data/davinciwordcount".

Gdy zadanie zostało ukończone pomyślnie, aplikacja wyświetla zawartość pliku wyjściowego "części r-00000".

## <a name="next-steps"></a>Kolejne kroki
W tym artykule uzyskanych kilka sposobów tworzenia klastra usługi HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* Do przesyłania zadań Hive, zobacz [uruchamianie zapytań Hive przy użyciu zestawu SDK .NET usługi HDInsight](apache-hadoop-use-hive-dotnet-sdk.md).
* Do tworzenia klastrów usługi HDInsight, zobacz [utworzyć Linux opartych klastrów Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Zarządzanie klastrami usługi HDInsight, zobacz [klastrów zarządzania Hadoop w HDInsight](../hdinsight-administer-use-portal-linux.md).
* Do uczenia zestawu .NET SDK usługi HDInsight, zobacz [odwołania do zestawu SDK .NET usługi HDInsight](https://msdn.microsoft.com/library/mt271028.aspx).
* Dla podejścia nieinterakcyjnego uwierzytelniać na platformie Azure, zobacz [tworzenie aplikacji usługi HDInsight .NET uwierzytelnianie nieinteraktywne](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

