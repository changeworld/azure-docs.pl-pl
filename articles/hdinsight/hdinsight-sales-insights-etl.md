---
title: 'Samouczek: Tworzenie kompleksowego potoku ETL w celu uzyskania szczegółowych informacji o sprzedaży w usłudze Azure HDInsight'
description: Dowiedz się, jak używać tworzenia potoków ETL za pomocą usługi Azure HDInsight w celu uzyskiwania szczegółowych informacji z danych sprzedaży przy użyciu klastrów platformy Spark na żądanie i usługi Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/24/2020
ms.openlocfilehash: a4df99c45b27ad662133010422cae2e30e36e584
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247269"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Samouczek: Tworzenie kompleksowego potoku danych w celu uzyskania szczegółowych informacji o sprzedaży w usłudze Azure HDInsight

W tym samouczku skompilujesz kompleksowy potok danych, który wykonuje operacje wyodrębniania, przekształcania i ładowania (ETL). Potok [użyje apache Spark](./spark/apache-spark-overview.md) i Apache hive klastrów uruchomionych w usłudze Azure HDInsight do wykonywania zapytań i manipulowania danymi. Będziesz również używać technologii, takich jak Usługa Azure Data Lake Storage Gen2 do przechowywania danych i usługa Power BI do wizualizacji.

Ten potok danych łączy dane z różnych magazynów, usuwa niechciane dane, dołącza nowe dane i ładuje to wszystko z powrotem do magazynu, aby wizualizować szczegółowe informacje biznesowe. Dowiedz się więcej o potokach ETL w [zakresie wyodrębniania, przekształcania i ładowania (ETL) na dużą skalę](./hadoop/apache-hadoop-etl-at-scale.md).

![Architektura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejsu wiersza polecenia platformy Azure. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Członek [wbudowanej roli platformy Azure — właściciel](../role-based-access-control/built-in-roles.md).

* [Program Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) do wizualizacji szczegółowych informacji biznesowych na końcu tego samouczka.

## <a name="create-resources"></a>Tworzenie zasobów

### <a name="clone-the-repository-with-scripts-and-data"></a>Klonowanie repozytorium za pomocą skryptów i danych

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Otwórz usługę Azure Cloud Shell na górnym pasku menu. Wybierz subskrypcję do utworzenia udziału plików, jeśli usługa Cloud Shell wyświetli monit.

   ![Otwieranie usługi Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)

1. Z menu rozwijanego **Wybierz środowisko** wybierz polecenie **Bash**.

1. Upewnij się, że jesteś członkiem [właściciela](../role-based-access-control/built-in-roles.md)roli platformy Azure. Zamień `user@contoso.com` na swoje konto, a następnie wprowadź polecenie:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Jeśli nie zostanie zwrócony żaden rekord, nie jesteś członkiem i nie będzie można ukończyć tego samouczka.

1. Wyświetl listę subskrypcji wprowadzających polecenie:

    ```azurecli
    az account list --output table
    ```

    Należy zwrócić uwagę na identyfikator subskrypcji, która będzie używana dla tego projektu.

1. Ustaw subskrypcję, która będzie używana dla tego projektu. Zamień `SUBSCRIPTIONID` na wartość rzeczywistą, a następnie wprowadź polecenie.

    ```azurecli
    subscriptionID="SUBSCRIPTIONID"
    az account set --subscription $subscriptionID
    ```

1. Utwórz nową grupę zasobów dla projektu. Zamień `RESOURCEGROUP` na żądaną nazwę, a następnie wprowadź polecenie.

    ```azurecli
    resourceGroup="RESOURCEGROUP"
    az group create --name $resourceGroup --location westus
    ```

1. Pobierz dane i skrypty dla tego samouczka z [repozytorium ETL analiz sprzedaży HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl).  Wprowadź następujące polecenie:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Upewnij `salesdata scripts templates` się, że zostały utworzone. Sprawdź za pomocą następującego polecenia:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Wdrażanie zasobów platformy Azure potrzebnych do potoku

1. Dodaj uprawnienia wykonania dla wszystkich skryptów, wprowadzając:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Uruchom skrypt. Zastąp `RESOURCE_GROUP_NAME` i `LOCATION` odpowiednimi wartościami, a następnie wprowadź polecenie:

    ```bash
    ./scripts/resources.sh RESOURCE_GROUP_NAME LOCATION
    ```

    Polecenie wdroży następujące zasoby:

    * Konto magazynu obiektów Blob platformy Azure. To konto będzie zawierać dane sprzedaży firmy.
    * Konto usługi Azure Data Lake Storage Gen2. To konto będzie służyć jako konto magazynu dla obu klastrów HDInsight. Dowiedz się więcej o rozwiązaniach HDInsight i data lake storage gen2 w [integracji usługi Azure HDInsight z usługą Data Lake Storage Gen2.](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
    * Tożsamość zarządzana przypisana przez użytkownika. To konto daje klastrom HDInsight dostęp do konta Data Lake Storage Gen2.
    * Klaster Platformy Spark Apache. Ten klaster będzie używany do czyszczenia i przekształcania nieprzetworzonych danych.
    * Klaster [interaktywnych zapytań](./interactive-query/apache-interactive-query-get-started.md) gałęzi Apache. Ten klaster umożliwi wykonywanie zapytań dotyczących danych sprzedaży i ich wizualizację za pomocą usługi Power BI.
    * Sieć wirtualna platformy Azure obsługiwana przez reguły sieciowej grupy zabezpieczeń (NSG). Ta sieć wirtualna umożliwia klastrom komunikowanie się i zabezpiecza ich komunikację.

Tworzenie klastra może potrwać około 20 minut.

Skrypt `resources.sh` zawiera następujące polecenia. Nie jest wymagane, aby uruchomić te polecenia, jeśli skrypt został już wykonany w poprzednim kroku.

* `az group deployment create`- To polecenie używa szablonu`resourcestemplate.json`usługi Azure Resource Manager ( ) do tworzenia określonych zasobów z żądaną konfiguracją.

    ```azurecli
    az group deployment create --name ResourcesDeployment \
        --resource-group $resourceGroup \
        --template-file resourcestemplate.json \
        --parameters "@resourceparameters.json"
    ```

* `az storage blob upload-batch`- To polecenie przesyła pliki csv danych sprzedaży do nowo utworzonego konta magazynu obiektów Blob za pomocą tego polecenia:

    ```azurecli
    az storage blob upload-batch -d rawdata \
        --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
    ```

Domyślnym hasłem dostępu SSH do `Thisisapassword1`klastrów jest . Jeśli chcesz zmienić hasło, przejdź `resourcesparameters.json` do pliku i `sparksshPassword`zmień `sparkClusterLoginPassword` `llapClusterLoginPassword`hasło `llapsshPassword` dla , , i parametrów.

### <a name="verify-deployment-and-collect-resource-information"></a>Weryfikowanie wdrażania i zbieranie informacji o zasobach

1. Jeśli chcesz sprawdzić stan wdrożenia, przejdź do grupy zasobów w witrynie Azure portal. Wybierz **wdrożeń** w obszarze **Ustawienia**. Wybierz nazwę wdrożenia, `ResourcesDeployment`. W tym miejscu można zobaczyć zasoby, które zostały pomyślnie wdrożone i zasoby, które są nadal w toku.

1. Aby wyświetlić nazwy klastrów, wprowadź następujące polecenie:

    ```azurecli
    sparkCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'spark')].{clusterName:name}" -o tsv)

    llapCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'llap')].{clusterName:name}" -o tsv)

    echo $sparkCluster
    echo $llapCluster
    ```

1. Aby wyświetlić konto magazynu platformy Azure i klucz dostępu, wprowadź następujące polecenie:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Aby wyświetlić konto Data Lake Storage Gen2 i klucz dostępu, wprowadź następujące polecenie:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Usługa Azure Data Factory to narzędzie ułatwiace automatyzację potoków platformy Azure. Nie jest to jedyny sposób na wykonanie tych zadań, ale jest to świetny sposób na automatyzację procesów. Aby uzyskać więcej informacji na temat usługi Azure Data Factory, zobacz [dokumentację usługi Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Ta fabryka danych będzie miała jeden potok z dwoma działaniami:

* Pierwsze działanie skopiuje dane z magazynu obiektów Blob platformy Azure do konta magazynu usługi Data Lake Storage gen 2, aby naśladować pozyskiwania danych.
* Drugie działanie spowoduje przekształcenie danych w klastrze platformy Spark. Skrypt przekształca dane, usuwając niechciane kolumny. Dołącza również nową kolumnę, która oblicza przychody generowane przez pojedynczą transakcję.

Aby skonfigurować potok usługi Azure Data Factory, wykonaj następujące polecenie:

```bash
./scripts/adf.sh
```

Ten skrypt wykonuje następujące czynności:

1. Tworzy jednostkę `Storage Blob Data Contributor` usługi z uprawnieniami na koncie magazynu Usługi Data Lake Storage Gen2.
1. Uzyskuje token uwierzytelniania w celu autoryzowania żądań POST do [interfejsu API REST systemu danych usługi Data Lake Storage Gen2.](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)
1. Wypełnia rzeczywistą nazwę konta magazynu Data Lake Storage `sparktransform.py` Gen2 w plikach i. `query.hql`
1. Uzyskuje klucze magazynu dla kont magazynu usługi Data Lake Gen2 i blob magazynu.
1. Tworzy inne wdrożenie zasobów w celu utworzenia potoku usługi Azure Data Factory z skojarzonymi połączonymi usługami i działaniami. Przekazuje klucze magazynu jako parametry do pliku szablonu, dzięki czemu połączone usługi mogą uzyskać dostęp do kont magazynu poprawnie.

Potok fabryki danych jest wdrażany za pomocą następującego polecenia:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Uruchamianie potoku danych

### <a name="trigger-the-data-factory-activities"></a>Wyzwalanie działań usługi Data Factory

Pierwsze działanie w potoku fabryki danych, który został utworzony przenosi dane z magazynu obiektów Blob do usługi Data Lake Storage Gen2. Drugie działanie stosuje transformacje platformy Spark na danych i zapisuje przekształcone pliki csv w nowej lokalizacji. Zakończenie całego potoku może potrwać kilka minut.

Aby wyzwolić potoki, można:

* Wyzwalanie potoków fabryki danych w programie PowerShell. Zamień `DataFactoryName` na rzeczywistą nazwę fabryki danych, a następnie uruchom następujące polecenia:

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "CopyPipeline_k8z"
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "sparkTransformPipeline"
    ```

    Lub

* Otwórz fabrykę danych i wybierz **pozycję Autor & Monitor**. Wyzwolić potoku kopiowania, a następnie potoku platformy Spark z portalu. Aby uzyskać informacje na temat wyzwalania potoków za pośrednictwem portalu, zobacz [Tworzenie klastrów Apache Hadoop na żądanie w usłudze HDInsight przy użyciu usługi Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Aby sprawdzić, czy potoki zostały uruchomione, można wykonać jedną z następujących czynności:

* Przejdź do sekcji **Monitor** w fabryce danych za pośrednictwem portalu.
* W Eksploratorze usługi Azure Storage przejdź do konta magazynu usługi Data Lake Storage gen 2. Przejdź do `files` systemu plików, a `transformed` następnie przejdź do folderu i sprawdź jego zawartość, aby sprawdzić, czy potok zakończył się pomyślnie.

Aby uzyskać inne sposoby przekształcania danych za pomocą programu HDInsight, zobacz [ten artykuł dotyczący korzystania z notebooka Jupyter](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Tworzenie tabeli w klastrze zapytań interaktywnych w celu wyświetlania danych w usłudze Power BI

1. Skopiuj `query.hql` plik do klastra LLAP przy użyciu protokołu SCP. Zamień `LLAPCLUSTERNAME` na rzeczywistą nazwę, a następnie wprowadź polecenie:

    ```bash
    scp scripts/query.hql sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Użyj protokołu SSH, aby uzyskać dostęp do klastra LLAP. Zamień `LLAPCLUSTERNAME` na rzeczywistą nazwę, a następnie wprowadź polecenie. Jeśli `resourcesparameters.json` plik nie został zmieniony, hasło `Thisisapassword1`to .

    ```bash
    ssh sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Użyj następującego polecenia, aby uruchomić skrypt:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Ten skrypt utworzy tabelę zarządzaną w klastrze zapytań interaktywnych, do której można uzyskać dostęp z usługi Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Tworzenie pulpitu nawigacyjnego usługi Power BI na podstawie danych sprzedaży

1. Otwórz program Power BI Desktop.
1. Wybierz pozycję **Pobieranie danych**.
1. Wyszukaj **klaster zapytań interaktywnych USŁUGI HDInsight**.
1. Wklej identyfikator URI dla klastra. Powinna ona mieć format `https://LLAPCLUSTERNAME.azurehdinsight.net`.

   Wprowadź `default` bazę danych.
1. Wprowadź nazwę użytkownika i hasło używane do uzyskiwania dostępu do klastra.

Po załadowaniu danych można eksperymentować z pulpitem nawigacyjnym, który chcesz utworzyć. Aby rozpocząć korzystanie z pulpitów nawigacyjnych usługi Power BI, zobacz następujące łącza:

* [Wprowadzenie do pulpitów nawigacyjnych dla projektantów usługi Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Samouczek: Wprowadzenie do usługi Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal używać tej aplikacji, usuń wszystkie zasoby za pomocą następującego polecenia, aby nie były za nie obciążane.

```azurecli-interactive
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę](./hadoop/apache-hadoop-etl-at-scale.md)
