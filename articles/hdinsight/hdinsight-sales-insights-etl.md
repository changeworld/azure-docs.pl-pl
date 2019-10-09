---
title: 'Samouczek: Tworzenie kompleksowego potoku ETL w celu uzyskania szczegółowych informacji o sprzedaży'
description: Dowiedz się, jak tworzyć potoki ETL za pomocą usługi Azure HDInsight, aby uzyskiwać wgląd w dane sprzedaży przy użyciu klastrów na żądanie i Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: b9bcaf4b7497e8beba377eb7e47a44a6eb061299
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178008"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Samouczek: Tworzenie kompleksowego potoku danych w celu uzyskania szczegółowych informacji o sprzedaży

W tym samouczku utworzysz kompleksowy Potok danych, który wykonuje operacje wyodrębniania, przekształcania i ładowania (ETL). Potok będzie używać klastrów Apache Spark i Apache Hive działających w usłudze Azure HDInsight na potrzeby wykonywania zapytań i manipulowania danymi. Będziesz również używać takich technologii jak Azure Data Lake Storage Gen2 do przechowywania danych i Power BI do wizualizacji.

Ten potok danych łączy dane z różnych magazynów, usuwa wszelkie niechciane dane, dołącza nowe dane i ładuje je z powrotem do magazynu w celu wizualizacji szczegółowych informacji o firmie. Więcej informacji na temat potoków ETL można znaleźć w części [wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę](./hadoop/apache-hadoop-etl-at-scale.md).

![Architektura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

Pobierz [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) , aby wizualizować szczegółowe informacje biznesowe na końcu tego samouczka.

## <a name="create-resources"></a>Tworzenie zasobów

### <a name="clone-the-repository-with-scripts-and-data"></a>Klonowanie repozytorium za pomocą skryptów i danych

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Otwórz Azure Cloud Shell z górnego paska menu. Wybierz swoją subskrypcję, aby utworzyć udział plików, jeśli Cloud Shell zostanie wyświetlony komunikat.

   ![Otwórz Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. W menu rozwijanym **Wybierz środowisko** wybierz pozycję **bash**.
1. Zaloguj się do konta platformy Azure i ustaw subskrypcję. 
1. Skonfiguruj grupę zasobów dla projektu.
   1. Wybierz unikatową nazwę grupy zasobów.
   1. Uruchom Poniższy fragment kodu w Cloud Shell, aby ustawić zmienne, które będą używane w kolejnych krokach:

       ```azurecli-interactive 
       resourceGroup="<RESOURCE GROUP NAME>"
       subscriptionID="<SUBSCRIPTION ID>"
        
       az account set --subscription $subscriptionID
       az group create --name $resourceGroup --location westus
       ```

1. Pobierz dane i skrypty dla tego samouczka z [REPOZYTORIUM ETL usługi HDInsight Sales Insights](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) , wprowadzając następujące polecenia w Cloud Shell:

    ```azurecli-interactive 
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Wprowadź `ls` w wierszu powłoki, aby zobaczyć, że zostały utworzone następujące pliki i katalogi:

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Wdrażanie zasobów platformy Azure wymaganych dla potoku 

1. Dodaj uprawnienia wykonywania dla skryptu `chmod +x scripts/*.sh`.
1. Użyj polecenia `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>`, aby uruchomić skrypt w celu wdrożenia następujących zasobów na platformie Azure:

   1. Konto usługi Azure Blob Storage. To konto będzie zawierać dane sprzedaży firmowej.
   2. Konto Azure Data Lake Storage Gen2. To konto będzie działać jako konto magazynu dla obu klastrów usługi HDInsight. Przeczytaj więcej na temat usługi HDInsight i Data Lake Storage Gen2 w [usłudze Azure HDInsight Integration z Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
   3. Tożsamość zarządzana przypisana przez użytkownika. To konto umożliwia Klastrom usługi HDInsight dostęp do konta Data Lake Storage Gen2.
   4. Klaster Apache Spark. Ten klaster zostanie użyty do oczyszczenia i przekształcenia danych pierwotnych.
   5. Apache Hive interaktywny klaster zapytań. Ten klaster umożliwi wykonywanie zapytań dotyczących danych sprzedaży i wizualizację go przy użyciu Power BI.
   6. Sieć wirtualna platformy Azure obsługiwana przez reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Ta sieć wirtualna umożliwia Klastrom komunikowanie się i zabezpieczenie komunikacji. 

Tworzenie klastra może trwać około 20 minut.

Skrypt `resources.sh` zawiera następujące polecenie. To polecenie używa szablonu Azure Resource Manager (`resourcestemplate.json`) do utworzenia określonych zasobów z odpowiednią konfiguracją.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

Skrypt `resources.sh` przekazuje także pliki CSV danych Sales do nowo utworzonego konta usługi BLOB Storage za pomocą tego polecenia:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

Domyślne hasło dostępu SSH do klastrów jest `Thisisapassword1`. Jeśli chcesz zmienić hasło, przejdź do pliku `resourcesparameters.json` i Zmień hasło dla `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` i `llapsshPassword` parametrów.

### <a name="verify-deployment-and-collect-resource-information"></a>Weryfikowanie wdrożenia i zbieranie informacji o zasobach

1. Jeśli chcesz sprawdzić stan wdrożenia, przejdź do grupy zasobów na Azure Portal. Wybierz pozycję **wdrożenia** w obszarze **Ustawienia**. Wybierz nazwę wdrożenia, `ResourcesDeployment`. Tutaj można zobaczyć zasoby, które zostały pomyślnie wdrożone, oraz zasoby, które są nadal w toku.
1. Po zakończeniu wdrażania przejdź do Azure Portal > **grup zasobów** > < RESOURCE_GROUP_NAME >.
1. Znajdź nowe konto usługi Azure Storage, które zostało utworzone na potrzeby przechowywania plików sprzedaży. Nazwa konta magazynu rozpoczyna się od `blob`, a następnie zawiera ciąg losowy. Wykonaj następujące czynności:
   1. Zanotuj nazwę konta magazynu do późniejszego użycia.
   1. Wybierz nazwę konta magazynu obiektów BLOB.
   1. Po lewej stronie portalu w obszarze **Ustawienia**wybierz pozycję **klucze dostępu**.
   1. Skopiuj ciąg w polu **Klucz1** i Zapisz go do późniejszego użycia.
1. Znajdź konto Data Lake Storage Gen2, które zostało utworzone jako magazyn dla klastrów usługi HDInsight. To konto znajduje się w tej samej grupie zasobów co konto magazynu obiektów blob, ale rozpoczyna się od `adlsgen2`. Wykonaj następujące czynności:
   1. Zanotuj nazwę konta Data Lake Storage Gen2.
   1. Wybierz nazwę konta Data Lake Storage Gen2.
   1. Po lewej stronie portalu w obszarze **Ustawienia**wybierz pozycję **klucze dostępu**.
   1. Skopiuj ciąg w polu **Klucz1** i Zapisz go do późniejszego użycia.

> [!Note]
> Po uzyskaniu informacji o nazwach kont magazynu można uzyskać klucze kont za pomocą następującego polecenia w wierszu Azure Cloud Shell:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Azure Data Factory jest narzędziem ułatwiającym automatyzację potoków platformy Azure. Nie jest to jedyna metoda wykonywania tych zadań, ale jest to świetny sposób automatyzacji procesów. Aby uzyskać więcej informacji na temat Azure Data Factory, zobacz [dokumentację Azure Data Factory](https://azure.microsoft.com/en-us/services/data-factory/). 

Ta Fabryka danych będzie mieć jeden potok z dwoma działaniami: 

- Pierwsze działanie spowoduje skopiowanie danych z usługi Azure Blob Storage do konta magazynu Data Lake Storage generacji 2, aby naśladować pozyskiwanie danych.
- Drugie działanie spowoduje przekształcenie danych w klastrze Spark. Skrypt przekształca dane przez usunięcie niechcianych kolumn. Dodaje również nową kolumnę, która oblicza przychód generowany przez pojedynczą transakcję.

Aby skonfigurować potok Azure Data Factory, uruchom skrypt `adf.sh`:

1. Aby dodać uprawnienia EXECUTE do pliku, użyj `chmod +x adf.sh`.
1. Użyj `./adf.sh`, aby uruchomić skrypt. 

Ten skrypt wykonuje następujące czynności:

1. Tworzy jednostkę usługi z uprawnieniami `Storage Blob Data Contributor` na koncie magazynu Data Lake Storage Gen2.
1. Uzyskuje token uwierzytelniania w celu autoryzowania żądań POST do [interfejsu API REST systemu plików Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Wypełnia rzeczywistą nazwę konta magazynu Data Lake Storage Gen2 w plikach `sparktransform.py` i `query.hql`.
1. Uzyskuje klucze magazynu dla Data Lake Storage Gen2 i kont usługi BLOB Storage.
1. Tworzy inne wdrożenie zasobów w celu utworzenia potoku Azure Data Factory przy użyciu skojarzonych z nim usług i działań. Przekazuje klucze magazynu jako parametry do pliku szablonu, dzięki czemu połączone usługi mogą prawidłowo uzyskiwać dostęp do kont magazynu.

Potok Data Factory jest wdrażany za pomocą następującego polecenia:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Uruchamianie potoku danych

### <a name="trigger-the-data-factory-activities"></a>Wyzwól działania Data Factory

Pierwsze działanie w utworzonym potoku Data Factory przenosi dane z magazynu obiektów BLOB do Data Lake Storage Gen2. Drugie działanie stosuje przekształcenia Spark na danych i zapisuje przekształcone pliki CSV do nowej lokalizacji. Ukończenie całego potoku może potrwać kilka minut.

Aby wyzwolić potoki, można wykonać jedną z:

- Uruchom następujące polecenia, aby wyzwolić potoki Data Factory w programie PowerShell: 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- Otwórz fabrykę danych i wybierz pozycję **utwórz & monitor**. Wyzwól potok kopiowania, a następnie potok Spark z portalu. Aby uzyskać informacje dotyczące wyzwalania potoków za pośrednictwem portalu, zobacz [Tworzenie klastrów Apache Hadoop na żądanie w usłudze HDInsight przy użyciu Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Aby sprawdzić, czy potoki zostały uruchomione, można wykonać jedną z następujących czynności:

- Przejdź do sekcji **monitorowanie** w fabryce danych za pomocą portalu.
- W Eksplorator usługi Azure Storage przejdź do konta magazynu Data Lake Storage generacji 2. Przejdź do systemu plików `files`, a następnie przejdź do folderu `transformed` i sprawdź jego zawartość, aby sprawdzić, czy potok zakończył się pomyślnie.

Aby uzyskać inne metody przekształcania danych za pomocą usługi HDInsight, zobacz [ten artykuł na temat korzystania z Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Utwórz tabelę w klastrze zapytań interaktywnych, aby wyświetlić dane na Power BI

1. Skopiuj plik `query.hql` do klastra LLAP przy użyciu punktu połączenia usługi:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Użyj protokołu SSH, aby uzyskać dostęp do klastra LLAP przy użyciu następującego polecenia, a następnie wprowadź hasło. Jeśli plik `resourcesparameters.json` nie został zmodyfikowany, hasło jest `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Użyj następującego polecenia, aby uruchomić skrypt:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Ten skrypt spowoduje utworzenie tabeli zarządzanej w klastrze interakcyjnych zapytań, do której można uzyskać dostęp z Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Tworzenie pulpitu nawigacyjnego Power BI na podstawie danych sprzedaży

1. Otwórz Power BI Desktop.
1. Wybierz pozycję **Pobierz dane**.
1. Wyszukaj **klaster interakcyjnych zapytań usługi HDInsight**.
1. Wklej w tym miejscu identyfikator URI klastra. Powinien być w formacie `https://<LLAP CLUSTER NAME>.azurehdinsight.net`.

   Wprowadź `default` dla bazy danych.
1. Wprowadź nazwę użytkownika i hasło, które są używane w celu uzyskania dostępu do klastra.

Po załadowaniu danych można eksperymentować z pulpitem nawigacyjnym, który ma zostać utworzony. Aby rozpocząć pracę z pulpitami nawigacyjnymi Power BI, zobacz następujące linki:

* [Wprowadzenie do pulpitów nawigacyjnych dla projektantów Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Samouczek: Rozpoczynanie pracy z usługa Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń wszystkie zasoby przy użyciu następującego polecenia, aby nie były naliczone opłaty.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę](./hadoop/apache-hadoop-etl-at-scale.md)
