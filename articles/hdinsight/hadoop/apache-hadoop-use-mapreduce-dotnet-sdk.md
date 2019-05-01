---
title: Przesyłanie zadań MapReduce, przy użyciu zestawu SDK usługi .NET HDInsight — Azure
description: Dowiedz się, jak przesyłać zadania MapReduce do usługi Azure HDInsight Apache Hadoop przy użyciu zestawu .NET SDK HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 1ac2dda20ba1219c9f62e834b5cd2cfba8a50086
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718954"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Uruchomienie zadania MapReduce, przy użyciu zestawu .NET SDK HDInsight
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Dowiedz się, jak przesyłać zadania MapReduce, przy użyciu zestawu .NET SDK HDInsight. HDInsight klastrów pochodzą z pliku jar z niektórych przykładów technologii MapReduce. Plik jar jest */example/jars/hadoop-mapreduce-examples.jar*.  Jednym z przykładów jest *wordcount*. Utworzysz aplikację konsolową C#, w celu przesłania zadania wordcount.  Zadanie odczytuje */example/data/gutenberg/davinci.txt* pliku i generuje wyjściowe wyniki do */example/data/davinciwordcount*.  Jeśli chcesz ponownie uruchomić aplikację, należy wyczyścić folder wyjściowy.

> [!NOTE]  
> Kroki opisane w tym artykule, należy wykonać w kliencie Windows. Instrukcje dotyczące używania systemu Linux, OS X lub klienta systemu Unix do pracy z programu Hive selektor karty wyświetlane w górnej części tego artykułu.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonywania w tym artykule, musi mieć następujące elementy:

* **Klaster Hadoop w HDInsight**. Zobacz [rozpoczęcie korzystania z opartą na systemie Linux Apache Hadoop w HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Przesyłanie zadań MapReduce, przy użyciu zestawu .NET SDK HDInsight
Zestaw .NET SDK HDInsight zawiera biblioteki klienckie programu .NET, dzięki czemu łatwiej jest pracować z klastrami HDInsight za pomocą platformy .NET. 

**Aby przesłać zadania**

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio.
2. Z poziomu konsoli Menedżera pakietów NuGet uruchom następujące polecenie:

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

Aby ponownie uruchomić zadanie, należy zmienić nazwę folderu danych wyjściowych zadania, w tym przykładzie jest "/ przykład/data/davinciwordcount".

Jeśli zadanie zostało ukończone pomyślnie, aplikacja wyświetla zawartość pliku wyjściowego "część r-00000".

## <a name="next-steps"></a>Kolejne kroki
W tym artykule mają przedstawiono kilka sposobów, aby utworzyć klaster usługi HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* Aby przesyłanie zadania Hive, zobacz [uruchamianie Apache zapytań Hive przy użyciu zestawu .NET SDK HDInsight](apache-hadoop-use-hive-dotnet-sdk.md).
* W celu tworzenia klastrów HDInsight, zobacz [klastrów opartych na systemie Linux z tworzenia Apache Hadoop w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Aby zarządzać klastrami HDInsight, zobacz [klastrów zarządzania Apache Hadoop w HDInsight](../hdinsight-administer-use-portal-linux.md).
* Do nauki zestawu .NET SDK HDInsight, zobacz [dokumentacji zestawu .NET SDK HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Dla nieinterakcyjnych uwierzytelniania na platformie Azure, zobacz [tworzenie aplikacji HDInsight platformy .NET z uwierzytelnianiem nieinterakcyjnym](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

