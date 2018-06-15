---
title: Uruchamianie zadań Sqoop przy użyciu platformy .NET i HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zestawu .NET SDK HDInsight Uruchom Sqoop importowania i eksportowania między klastrem usługi Hadoop i bazy danych Azure SQL.
keywords: zadanie sqoop
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 818e4aca63249c7a1543abe146e0691e993e9e80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200303"
---
# <a name="run-sqoop-jobs-by-using-net-sdk-for-hadoop-in-hdinsight"></a>Uruchamianie zadań Sqoop przy użyciu zestawu .NET SDK dla platformy Hadoop w usłudze HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać zestawu .NET SDK usługi Azure HDInsight do uruchomienia zadań Sqoop w usłudze HDInsight umożliwia importowanie i eksportowanie między klastrem usługi HDInsight i bazy danych Azure SQL lub bazy danych SQL Server.

> [!NOTE]
> Mimo że można użyć procedur w tym artykule z jednego klastra usługi HDInsight opartych na systemie Windows lub opartych na systemie Linux, działają tylko w kliencie systemu Windows. Aby wybrać inne metody, selektor karty w górnej części tego artykułu.
> 

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka, musi mieć następującego elementu:

* Klaster usługi Hadoop w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra i bazy danych SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Użyj Sqoop w klastrach HDInsight przy użyciu zestawu .NET SDK
Zestawu .NET SDK usługi HDInsight udostępnia biblioteki klienta .NET, dzięki czemu możliwe jest łatwiejsze do pracy z klastrami HDInsight ze środowiska .NET. W tej sekcji utworzysz aplikację konsoli języka C# można wyeksportować hivesampletable do tabeli bazy danych SQL Azure, który został utworzony we wcześniejszej części tego samouczka.

## <a name="submit-a-sqoop-job"></a>Prześlij zadanie Sqoop

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio.

2. Za pomocą konsoli Menedżera pakietów programu Visual Studio należy zaimportować pakiet za pomocą następującego polecenia NuGet:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Użyj następującego kodu w pliku Program.cs:
   
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

* Zbiorcze eksportu: Sqoop łącznik, który jest używany do eksportowania danych do programu Microsoft SQL Server lub bazy danych SQL Azure nie obsługuje obecnie zbiorcze operacje wstawiania.

* Przetwarzanie wsadowe: przy użyciu `-batch` przełącznika podczas wykonywania operacji wstawienia, Sqoop wykonuje wiele operacji wstawienia zamiast przetwarzanie wsadowe operacji insert.

## <a name="next-steps"></a>Kolejne kroki
Teraz ma przedstawiono sposób używania Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Korzystanie z usługą HDInsight Oozie](../hdinsight-use-oozie.md): Użyj Sqoop działań w przepływie pracy Oozie.
* [Analizowanie danych opóźnienie transmitowane przy użyciu usługi HDInsight](../hdinsight-analyze-flight-delay-data.md): Użyj gałąź rejestru, aby transmitowane analizować opóźnienie danych, a następnie użyj Sqoop eksportować dane do bazy danych Azure SQL.
* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md): znajdowanie innych metod do przekazywania danych do magazynu usługi HDInsight lub obiektów Blob platformy Azure.

