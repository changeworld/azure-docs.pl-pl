---
title: Prześlij zadania MapReduce przy użyciu sdk .NET usługi HDInsight — Azure
description: Dowiedz się, jak przesłać zadania MapReduce do usługi Azure HDInsight Apache Hadoop przy użyciu sdk .NET usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157055"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Uruchamianie zadania MapReduce przy użyciu zestawu SDK dla platformy .NET usługi HDInsight

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Dowiedz się, jak przesłać zadania MapReduce przy użyciu SDK .NET usługi HDInsight. Klastry HDInsight są wyposażone w plik jar z niektórymi próbkami MapReduce. Plik jar `/example/jars/hadoop-mapreduce-examples.jar`jest .  Jednym z przykładów jest **wordcount**. Tworzenie aplikacji konsoli języka C# do przesłania zadania wordcount.  Zadanie odczytuje `/example/data/gutenberg/davinci.txt` plik i wyprowadza wyniki `/example/data/davinciwordcount`do .  Jeśli chcesz ponownie uruchomić aplikację, należy wyczyścić folder wyjściowy.

> [!NOTE]  
> Kroki opisane w tym artykule muszą być wykonywane z klienta systemu Windows. Aby uzyskać informacje na temat korzystania z klienta Systemu Linux, OS X lub Unix do pracy z hive, użyj selektora kart pokazanego u góry artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Prześlij zadania MapReduce przy użyciu sdk HDInsight .NET

Zestawu HDInsight .NET SDK udostępnia biblioteki klienckie platformy .NET, które ułatwiają pracę z klastrami HDInsight z platformy .NET.

1. Uruchom program Visual Studio i utwórz aplikację konsoli języka C#.

1. Przejdź do**konsoli Menedżera pakietów** **Menedżera** >  **pakietów Narzędzia** > NuGet i wprowadź następujące polecenie:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Skopiuj poniższy kod do **Program.cs**. Następnie edytuj kod, ustawiając `existingClusterName`wartości `existingClusterPassword` `defaultStorageAccountName`dla: , , , `defaultStorageAccountKey`i `defaultStorageContainerName`.

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
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
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

1. Naciśnij **klawisz F5,** aby uruchomić aplikację.

Aby ponownie uruchomić zadanie, należy zmienić nazwę folderu wyjściowego `/example/data/davinciwordcount`zadania, w próbce jest to .

Po pomyślnym zakończeniu zadania aplikacja drukuje zawartość pliku `part-r-00000`wyjściowego .

## <a name="next-steps"></a>Następne kroki

W tym artykule poznaliśmy kilka sposobów tworzenia klastra HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* Aby przesłać zadanie gałęzi, zobacz [Uruchamianie zapytań gałęzi apache przy użyciu pliku HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Aby utworzyć klastry HDInsight, zobacz [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w pliku HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Aby zarządzać klastrami HDInsight, zobacz [Zarządzanie klastrami Apache Hadoop w udziale w udziale HDInsight](../hdinsight-administer-use-portal-linux.md).
* Aby zapoznać się z uczeniem się sdk HDInsight .NET, zobacz [informacje o sdku .NET .](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* Aby uzyskać uwierzytelnianie nieinterakcyjne na platformie Azure, zobacz Tworzenie aplikacji net [hdinsight uwierzytelniania nieinterakcyjnego](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).