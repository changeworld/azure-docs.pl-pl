---
title: 'Szybki start: tworzenie klastra Spark w usłudze HDInsight przy użyciu programu Azure PowerShell'
description: W tym przewodniku Szybki start pokazano, jak za pomocą programu Azure PowerShell utworzyć klaster Apache Spark w usłudze Azure HDInsight i uruchomić proste zapytanie Spark SQL.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 41311dce20237a300ae57f21bcc969c91da617b1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097278"
---
# <a name="quickstart-create-an-apache-spark-cluster-in-hdinsight-using-powershell"></a>Szybki start: tworzenie klastra Apache Spark w usłudze HDInsight przy użyciu programu PowerShell
Dowiedz się, jak utworzyć klaster [Apache Spark](https://spark.apache.org/) w usłudze Azure HDInsight, a następnie uruchamiać zapytania Spark SQL dla tabel programu [Apache Hive](https://hive.apache.org/). Platforma Apache Spark umożliwia szybką analizę danych i używanie klastrów obliczeniowych korzystających z funkcji przetwarzania w pamięci. Aby uzyskać informacje na temat platformy Apache Spark w usłudze HDInsight, zobacz [Omówienie: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md).

W tym przewodniku Szybki start użyjesz programu Azure PowerShell do utworzenia klastra Spark w usłudze HDInsight. Klaster używa usług Azure Storage Blob jako magazynu klastra. Aby uzyskać więcej informacji na temat korzystania z usługi Data Lake Storage Gen2, zobacz [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]  
> Opłaty za klastry usługi HDInsight są naliczane proporcjonalnie za minutę, niezależnie od ich użycia. Pamiętaj o usunięciu klastra po zakończeniu korzystania z niego. Aby uzyskać więcej informacji, zobacz sekcję [Czyszczenie zasobów](#clean-up-resources) w tym artykule.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-hdinsight-spark-cluster"></a>Tworzenie klastra Spark w usłudze HDInsight

Tworzenie klastra usługi HDInsight obejmuje tworzenie następujących obiektów i zasobów platformy Azure:

- Grupa zasobów platformy Azure. Grupa zasobów platformy Azure to kontener dla zasobów platformy Azure. 
- Konto magazynu platformy Azure lub usługa Azure Data Lake Storage.  Każdy klaster usługi HDInsight wymaga zależnego magazynu danych. W tym przewodniku Szybki start utworzysz konto magazynu.
- Klaster usługi HDInsight odpowiedniego typu.  W tym przewodniku Szybki start utworzysz klaster Spark w wersji 2.3.

Te zasoby zostaną utworzone za pomocą skryptu programu PowerShell.  Po uruchomieniu skryptu zostanie wyświetlony monit o wprowadzenie następujących wartości:

|Parametr|Wartość|
|------|------|
|Nazwa grupy zasobów platformy Azure | Podaj unikatową nazwę grupy zasobów.|
|Lokalizacja| Określ region świadczenia usługi Azure, na przykład „Środkowe stany USA”. |
|Domyślna nazwa konta magazynu | Podaj unikatową nazwę konta magazynu. |
|Nazwa klastra | Podaj unikatową nazwę klastra Spark w usłudze HDInsight.|
|Poświadczenia logowania klastra | To konto służy do nawiązania połączenia z pulpitem nawigacyjnym klastra w dalszej części przewodnika Szybki start.|
|Poświadczenia użytkownika protokołu SSH | Klienci protokołu SSH mogą służyć do tworzenia zdalnej sesji wiersza polecenia z klastrami usługi HDInsight.|



1. Kliknij przycisk **Wypróbuj to** w prawym górnym rogu następującego bloku kodu w celu otworzenia usługi [Azure Cloud Shell](../../cloud-shell/overview.md) i postępuj zgodnie z instrukcjami, aby połączyć się z platformą Azure.
2. Skopiuj i wklej poniższy skrypt programu PowerShell w usłudze Cloud Shell. 

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight
        
    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"
    
    # Create an Azure storae account and container
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_LRS `
        -Location $location
    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    
    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"
    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"
    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"
    
    # Default cluster size (# of worker nodes), version, type, and OS
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"
    $clusterOS = "Linux"
    
    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName
    
    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName -Context $defaultStorageContext 
    
    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")
    
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType $clusterOS `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials 
    
    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    ```
   Utworzenie klastra trwa około 20 minut. Przed przejściem do następnej sesji należy utworzyć klaster.

Problemy podczas tworzenia klastrów usługi HDInsight mogą świadczyć o braku odpowiednich uprawnień. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące kontroli dostępu](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

[Jupyter Notebook](https://jupyter.org/) to interakcyjne środowisko notesu, które obsługuje różne języki programowania. Notes pozwala na interakcję z danymi, łączenie kodu z tekstem markdown i wykonywanie prostych wizualizacji. 

1. Otwórz [portal Azure](https://portal.azure.com).
2. Wybierz pozycję **Klastry usługi HDInsight**, a następnie wybierz utworzony klaster.

    ![Otwieranie klastra usługi HDInsight w witrynie Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. W portalu wybierz pozycję **Pulpity nawigacyjne klastra**, a następnie pozycję **Notes Jupyter**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla klastra.

   ![Otwieranie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otwieranie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL")

4. Aby utworzyć notes, wybierz pozycję **Nowy** > **PySpark**. 

   ![Tworzenie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Tworzenie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL")

   Zostanie utworzony i otwarty nowy notes o nazwie Untitled (Untitled.pynb).


## <a name="run-spark-sql-statements"></a>Uruchamianie instrukcji Spark SQL

SQL (Structured Query Language) to najczęściej używany język służący do definiowania danych i wykonywania zapytań na tych danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

1. Sprawdź, czy jądro jest gotowe. Gotowość jądra jest sygnalizowana pustym okręgiem obok nazwy jądra w notesie. Pełne kółko oznacza, że jądro jest zajęte.

    ![Zapytanie programu Hive na platformie HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Zapytanie programu Hive na platformie HDInsight Spark")

    Podczas pierwszego uruchamiania notesu jądro wykonuje pewne zadania w tle. Poczekaj, aż jądro będzie gotowe. 
2. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**, aby go uruchomić. Polecenie wyświetla listę tabel Hive w klastrze:

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    Gdy używasz notesu Jupyter na potrzeby klastra platformy HDInsight Spark, możesz pobrać ustawienie wstępne `sqlContext` służące do uruchamiania zapytań programu Hive przy użyciu modułu Spark SQL. Wyrażenie `%%sql` informuje notes Jupyter o konieczności użycia ustawienia wstępnego `sqlContext` do uruchomienia zapytania programu Hive. Zapytanie pobiera pierwszych 10 wierszy z tabeli programu Hive (**hivesampletable**), która jest dostępna domyślnie na wszystkich klastrach usługi HDInsight. Uzyskanie wyników zajmuje około 30 sekund. Dane wyjściowe wyglądają następująco: 

    ![Zapytanie programu Hive na platformie HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Zapytanie programu Hive na platformie HDInsight Spark")

    Podczas każdego uruchomienia zapytania w programie Jupyter w tytule okna przeglądarki internetowej wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu.
    
2. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    Ekran zostanie odświeżony w celu wyświetlenia wyników zapytania.

    ![Wyniki zapytania programu Hive na platformie HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Wyniki zapytania programu Hive na platformie HDInsight Spark")

2. W menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Zamknięcie notesu spowoduje zwolnienie zasobów klastra.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Usługa HDInsight zapisuje Twoje dane w usłudze Azure Storage lub Azure Data Lake Storage, więc możesz bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Jeśli planujesz natychmiastowe rozpoczęcie pracy z samouczkiem z listy [Następne kroki](#next-steps), warto zachować klaster.

Przejdź z powrotem do witryny Azure Portal, a następnie wybierz pozycję **Usuń**.

![Usuwanie klastra usługi HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usunięcie grupy zasobów powoduje usunięcie zarówno klastra Spark w usłudze HDInsight, jak i domyślnego konta magazynu.

## <a name="next-steps"></a>Następne kroki 

W tym przewodniku Szybki start omówiono sposób tworzenia klastra Spark w usłudze HDInsight i uruchamiania podstawowego zapytania Spark SQL. Przejdź do następnego samouczka, aby dowiedzieć się, jak uruchamiać interakcyjne zapytania dotyczące przykładowych danych za pomocą klastra Spark w usłudze HDInsight.

> [!div class="nextstepaction"]
>[Uruchamianie interakcyjnych zapytań na platformie Apache Spark](./apache-spark-load-data-run-query.md)
