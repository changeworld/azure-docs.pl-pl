---
title: Uruchamiać zadania Apache Sqoop przy użyciu platformy .NET i HDInsight — Azure
description: Dowiedz się, jak używać zestawu SDK .NET HDInsight do uruchamiania narzędzia Apache Sqoop importu i eksportu między klastrem usługi Apache Hadoop a bazą danych Azure SQL database.
keywords: zadania narzędzia sqoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 1bdf1318f93697cd7b479d404f44b7617ad875dc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450149"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Uruchamiać zadania Apache Sqoop przy użyciu zestawu SDK platformy .NET dla usługi Apache Hadoop w HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak uruchamiać zadania Apache Sqoop w HDInsight w celu importowania i eksportowania między klastra usługi HDInsight i Azure SQL database lub SQL Server za pomocą zestawu .NET SDK usługi Azure HDInsight.

> [!NOTE]
> Chociaż procedury opisane w tym artykule można używać z oboma klastra HDInsight z systemem Windows lub opartych na systemie Linux, działają tylko z klienta Windows. Aby wybrać inne metody, selektor karty w górnej części tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonywania w tym artykule, konieczne jest posiadanie następujących elementów:

* Klaster platformy Apache Hadoop w HDInsight. Aby uzyskać więcej informacji, zobacz [utworzenia klastra i bazy danych SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>W klastrach HDInsight przy użyciu zestawu SDK platformy .NET przy użyciu narzędzia Sqoop
Zestaw .NET SDK HDInsight zawiera biblioteki klienckie programu .NET, tak, aby łatwiej pracować z klastrami HDInsight za pomocą platformy .NET. W tej sekcji utworzysz C# konsoli aplikacji, aby wyeksportować tabeli hivesampletable do tabeli usługi Azure SQL Database, który został utworzony we wcześniejszej części tego artykułu.

## <a name="submit-a-sqoop-job"></a>Prześlij zadanie narzędzia Sqoop

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio.

2. Z poziomu konsoli Menedżera pakietów Visual Studio należy zaimportować pakiet, uruchamiając następujące polecenie NuGet:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Użyj poniższego kodu w pliku Program.cs:
   
        using System.Collections.Generic;
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
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Aby uruchomić program, zaznacz **F5** klucza. 

## <a name="limitations"></a>Ograniczenia
HDInsight opartych na systemie Linux przedstawia następujące ograniczenia:

* Zbiorczy Eksport: Łącznik Sqoop, który jest używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database nie obsługuje obecnie zbiorcze operacje wstawiania.

* Przetwarzanie wsadowe: Za pomocą `-batch` przełącznika, Sqoop wykonuje wiele operacji wstawiania zamiast przetwarzanie wsadowe operacji wstawiania.

## <a name="next-steps"></a>Kolejne kroki
Teraz masz pokazaliśmy, jak przy użyciu narzędzia Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Za pomocą usług Apache Oozie HDInsight](../hdinsight-use-oozie-linux-mac.md): Użyj narzędzia Sqoop akcji w przepływ pracy programu Oozie.
* [Przekazywanie danych do HDInsight](../hdinsight-upload-data.md): Znajdź innych metod do przekazywania danych do usługi HDInsight lub Azure Blob storage.

