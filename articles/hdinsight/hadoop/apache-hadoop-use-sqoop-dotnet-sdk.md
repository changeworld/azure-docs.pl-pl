---
title: Uruchamianie zadań Apache Sqoop przy użyciu platformy .NET i HDInsight — Azure
description: Dowiedz się, jak używać zestawu SDK .NET usługi HDInsight do uruchamiania importu i eksportowania apache Sqoop między klastrem Apache Hadoop a bazą danych SQL Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157070"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Uruchamianie zadań Apache Sqoop przy użyciu pliku .NET SDK dla apache Hadoop w funkcji HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać zestawu Azure HDInsight .NET SDK do uruchamiania zadań Apache Sqoop w programie HDInsight w celu importowania i eksportowania między klastrem USŁUGI HDInsight a bazą danych usługi Azure SQL Database lub bazą danych PROGRAMU SQL Server.

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania środowiska testowego](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [Użyj Apache Sqoop z Hadoop w HDInsight](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Znajomość Sqoop. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Używanie sqoop w klastrach HDInsight z zestawu .NET SDK

Zestawu HDInsight .NET SDK udostępnia biblioteki klienckie platformy .NET, dzięki czemu łatwiej jest pracować z klastrami HDInsight z platformy .NET. W tej sekcji utworzysz aplikację konsoli `hivesampletable` języka C#, aby wyeksportować do tabeli bazy danych SQL platformy Azure, która została utworzona na podstawie wymagań wstępnych.

## <a name="set-up"></a>Konfiguruj

1. Uruchom program Visual Studio i utwórz aplikację konsoli języka C#.

1. Przejdź do**konsoli Menedżera pakietów** **Menedżera** >  **pakietów Narzędzia** > NuGet i uruchom następujące polecenie:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Eksport sqoop

Od gałęzi do programu SQL Server.  W tym przykładzie eksportuje dane `mobiledata` z tabeli Gałąź `hivesampletable` do tabeli w bazie danych SQL.

1. Użyj następującego kodu w pliku Program.cs. Edytuj kod, aby ustawić `ExistingClusterName`wartości `ExistingClusterPassword`dla , i .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
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
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Aby uruchomić program, wybierz klawisz **F5.**

## <a name="sqoop-import"></a>Import sqoop

Od programu SQL Server do usługi Azure Storage. Ten przykład zależy od powyższego eksportu, który został wykonany.  W tym przykładzie `mobiledata` importuje dane z `wasb:///tutorials/usesqoop/importeddata` tabeli w bazie danych SQL do katalogu na domyślnym koncie magazynu klastra.

1. Zastąp powyższy kod w `//sqoop start //sqoop end` bloku następującym kodem:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Aby uruchomić program, wybierz klawisz **F5.**

## <a name="limitations"></a>Ograniczenia

HdInsight oparty na systemie Linux przedstawia następujące ograniczenia:

* Eksport zbiorczy: łącznik Sqoop, który jest używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database, obecnie nie obsługuje wstawia zbiorczych.

* Przetwarzanie wsadowe: `-batch` Za pomocą przełącznika Sqoop wykonuje wiele wstawia zamiast wsadowania operacji wstawiania.

## <a name="next-steps"></a>Następne kroki

Teraz nauczyłeś się, jak korzystać z Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Użyj Apache Oozie z HDInsight](../hdinsight-use-oozie-linux-mac.md): Użyj sqoop akcji w oozie przepływu pracy.
* [Przekaż dane do usługi HDInsight](../hdinsight-upload-data.md): Znajdź inne metody przekazywania danych do magazynu obiektów BLOB usługi HDInsight lub Azure.
