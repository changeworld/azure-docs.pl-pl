---
title: Tworzenie klastrów Hadoop na żądanie przy użyciu fabryki danych - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć na żądanie klastrów platformy Hadoop w usłudze HDInsight przy użyciu fabryki danych Azure.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: 6344b9a50f182a2b9ab05562c29099c9d6976f0b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Tworzenie na żądanie klastrów platformy Hadoop w usłudze HDInsight przy użyciu fabryki danych Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Fabryka danych Azure](../data-factory/introduction.md) to usługa integracji danych opartych na chmurze, która organizuje i automatyzuje przepływu i przekształcania danych. Można go utworzyć HDInsight Hadoop klastra just-in-time do przetwarzania wycinka danych wejściowych i usunąć klaster, po zakończeniu przetwarzania. Korzyści wynikające ze stosowania klastra usługi Hadoop w HDInsight na żądanie, należą:

- Tylko płatności dla zadania czasu jest zasilany z klastra usługi HDInsight Hadoop (oraz krótki czas bezczynności można konfigurować). Rozliczenia dla klastrów usługi HDInsight jest proporcjonalnie za minutę, czy są używane lub nie. Gdy używasz usługi HDInsight połączony na żądanie w fabryce danych klastry są tworzone na żądanie. I klastrów są automatycznie usuwane po zakończeniu zadania. W związku z tym płacisz tylko za uruchomione czasu i krótki czas bezczynności (ustawienie time-to-live) zadanie.
- Można utworzyć przepływu pracy za pomocą potoku fabryki danych. Na przykład można mieć potoku, aby skopiować dane z lokalnego programu SQL Server do magazynu obiektów blob platformy Azure, przetwarzania danych przez uruchomienie skryptu Hive i Pig skrypt w klastrze usługi HDInsight Hadoop na żądanie. Następnie skopiuj dane wynikowe do magazyn danych SQL Azure dla aplikacji korzystających ze BI.
- Można zaplanować uruchamianie okresowo (co godzinę, codziennie, co tydzień, co miesiąc, itp.) przepływu pracy.

W fabryce danych Azure fabryki danych może mieć co najmniej jeden potoki danych. Potoku danych ma co najmniej jednego działania. Istnieją dwa typy działań: [działań przepływu danych](../data-factory/copy-activity-overview.md) i [działań przekształcania danych](../data-factory/transform-data.md). Działania przepływu danych (obecnie tylko działanie kopiowania) służy do przenoszenia danych z magazynu danych źródłowych w magazynie danych docelowym. Działania przekształcania danych służy do procesu przekształcenia danych. Działanie Hive HDInsight jest jednym z działania przekształcania obsługiwane przez fabryki danych. Działanie przekształcania Hive jest służy w tym samouczku.

Można skonfigurować działanie gałęzi do używania klastra usługi HDInsight Hadoop lub klastra usługi Hadoop w HDInsight na żądanie. W tym samouczku Hive działania w potoku fabryki danych jest skonfigurowany do używania klastra usługi HDInsight na żądanie. W związku z tym po uruchomieniu działania do przetwarzania wycinka danych, Oto co się stanie:

1. Klastra usługi HDInsight Hadoop jest tworzona automatycznie dla możesz just-in-time przetwarzania wycinka.  
2. Dane wejściowe są przetwarzane przez uruchomienie skryptu HiveQL w klastrze.
3. Klaster usługi HDInsight Hadoop jest usuwany po zakończeniu przetwarzania i klastra jest w stanie bezczynności skonfigurowanych ilość czasu (ustawienie timeToLive). Jeśli dalej wycinek danych jest dostępne do przetwarzania z tego czasu bezczynności timeToLive, tego samego klastra służy do przetwarzania wycinka.  

W tym samouczku skrypt HiveQL skojarzone z działaniem hive wykonuje następujące czynności:

1. Tworzy tabelę zewnętrzną, który odwołuje się do danych dziennika raw sieci web przechowywany w magazynie obiektów Blob platformy Azure.
2. Partycje nieprzetworzone dane przez rok i miesiąc.
3. Przechowuje dane podzielone na partycje w magazynie obiektów blob Azure.

W tym samouczku skrypt HiveQL skojarzone z działaniem hive tworzy tabelę zewnętrzną, który odwołuje się do danych dziennika raw sieci web przechowywany w magazynie obiektów Blob Azure. Poniżej przedstawiono przykładowe wiersze dla każdego miesiąca w pliku wejściowym.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Skrypt HiveQL partycje nieprzetworzone dane przez rok i miesiąc. Tworzy trzech plików oparte na poprzednie dane wejściowe. Każdy folder zawiera plik z wpisów z każdego miesiąca.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Aby uzyskać listę działań przekształcania danych fabryki danych oprócz działania Hive, zobacz [transformacji i analizy przy użyciu fabryki danych Azure](../data-factory/transform-data.md).

> [!NOTE]
> Obecnie można tworzyć tylko klastra usługi HDInsight w wersji 3.2 z fabryki danych Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem instrukcje w tym artykule, musi mieć następujące elementy:

* [Subskrypcja platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>Przygotowanie konta magazynu
W tym scenariuszu można użyć do trzech kont magazynu:

- domyślne konto magazynu dla klastra usługi HDInsight
- Konto magazynu dla danych wejściowych
- Konto magazynu dla danych wyjściowych

Aby uprościć samouczek, służy do służą do celów trzy jedno konto magazynu. Przykładowy skrypt programu PowerShell systemu Azure w tej sekcji wykonuje następujące zadania:

1. Loguje się do platformy Azure.
2. Utwórz grupę zasobów platformy Azure.
3. Tworzenie konta usługi Azure Storage.
4. Tworzenie kontenera obiektów Blob na koncie magazynu
5. Skopiuj następujące dwa pliki do kontenera obiektów Blob:

   * Plik danych wejściowych: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * Skrypt HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     Oba pliki są przechowywane w publicznego kontenera obiektów Blob.


**Aby przygotować magazyn i skopiować pliki przy użyciu programu Azure PowerShell:**
> [!IMPORTANT]
> Określ nazwy grupy zasobów platformy Azure i konto magazynu Azure, który zostanie utworzony przez skrypt.
> Zapisz **Nazwa grupy zasobów**, **nazwy konta magazynu**, i **klucz konta magazynu** wyjściowych przez skrypt. Należy je w następnej sekcji.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

Jeśli potrzebujesz pomocy przy użyciu skryptu programu PowerShell, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md). Jeśli chcesz użyć wiersza polecenia platformy Azure, zobacz [dodatku](#appendix) sekcji skryptu wiersza polecenia platformy Azure.

**Aby sprawdzić konto magazynu i zawartość**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **grup zasobów** w lewym okienku.
3. Kliknij dwukrotnie nazwę grupy zasobów utworzonej za pomocą skryptu programu PowerShell. Jeśli masz zbyt wiele grup zasobów na liście, użyj filtru.
4. Na **zasobów** kafelka, powinna mieć jeden zasób z listy, chyba że współużytkować grupy zasobów z innymi projektami. Ten zasób jest konto magazynu o nazwie określone wcześniej. Kliknij nazwę konta magazynu.
5. Kliknij przycisk **obiekty BLOB** Kafelki.
6. Kliknij przycisk **adfgetstarted** kontenera. Zobacz dwa foldery: **inputdata** i **skryptu**.
7. Otwórz folder, a następnie sprawdź pliki w folderach. Inputdata zawiera plik input.log z danych wejściowych i folder skryptu zawiera plik skrypt HiveQL.

## <a name="create-a-data-factory-using-resource-manager-template"></a>Tworzenie fabryki danych przy użyciu szablonu usługi Resource Manager
Konto magazynu, dane wejściowe i skrypt HiveQL przygotowany można przystąpić do utworzenia fabryki danych Azure. Istnieje kilka metod tworzenia fabryki danych. Przez wdrożenie szablonu usługi Azure Resource Manager przy użyciu portalu Azure, w tym samouczku tworzenie fabryki danych. Można także wdrożyć przy użyciu szablonu usługi Resource Manager [interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) i [programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template). Dla innych metod tworzenia fabryki danych, zobacz [samouczek: Tworzenie pierwszego fabrykę danych](../data-factory/quickstart-create-data-factory-dot-net.md).

1. Kliknij poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon usługi Resource Manager w witrynie Azure Portal. Szablon znajduje się pod adresem https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. Zobacz [jednostek fabryki danych w szablonie](#data-factory-entities-in-the-template) sekcji, aby uzyskać szczegółowe informacje na temat jednostek zdefiniowanych w szablonie. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Wybierz **Użyj istniejącego** opcja dla **grupy zasobów** ustawienie i wybierz nazwę grupy zasobów utworzonej w poprzednim kroku (przy użyciu skryptu programu PowerShell).
3. Wprowadź nazwę dla fabryki danych (**nazwa fabryki danych**). Ta nazwa musi być globalnie unikatowa.
4. Wprowadź **nazwy konta magazynu** i **klucz konta magazynu** zapisanej w poprzednim kroku.
5. Wybierz **akceptuję warunki i postanowienia** powyższych po odczytaniu za pośrednictwem **warunków i postanowień**.
6. Wybierz **Przypnij do pulpitu nawigacyjnego** opcji.
6. Kliknij przycisk **zakupu/utworzyć**. Zostanie wyświetlony na pulpicie nawigacyjnym kafelka o nazwie **wdrażanie szablonu wdrożenia**. Poczekaj na **grupy zasobów** zostanie otwarty blok grupy zasobów. Możesz również kliknąć Kafelek zatytułowany jako nazwę grupy zasobów można otworzyć bloku grupy zasobów.
6. Kliknij Kafelek, aby otworzyć grupę zasobów, jeśli bloku grupy zasobów nie jest już otwarty. Teraz zostanie wyświetlona jeden zasób fabryki więcej danych na liście oprócz zasobów konta magazynu.
7. Kliknij nazwę fabrykę danych (wartość podana dla **nazwa fabryki danych** parametru).
8. W bloku fabryki danych, kliknij przycisk **Diagram** kafelka. Na diagramie przedstawiono jedno działanie z zestawem danych wejściowych i wyjściowy zestaw danych:

    ![Azure diagram potoku działania Hive HDInsight fabryki danych na żądanie](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    Nazwy są definiowane w szablonie usługi Resource Manager.
9. Kliknij dwukrotnie **AzureBlobOutput**.
10. Na **ostatnie zaktualizowane wycinków**, powinien zostać wyświetlony jeden wycinek typu. Jeśli stan jest **w toku**, poczekaj, aż zostanie zmieniona na **gotowe**. Zwykle trwa około **20 minut** do tworzenia klastra usługi HDInsight.

### <a name="check-the-data-factory-output"></a>Sprawdź dane wyjściowe z fabryki danych

1. Użyj tej samej procedury podczas ostatniej sesji, aby sprawdzić kontenery adfgetstarted kontenera. Istnieją dwa nowe kontenery oprócz **adfgetsarted**:

   * Kontener o nazwie, który jest zgodny ze wzorcem: `adf<yourdatafactoryname>-linkedservicename-datetimestamp`. Ten kontener jest domyślnym kontenerem dla klastra usługi HDInsight.
   * adfjobs: ten kontener jest kontenerem dla dzienników zadania ADF.

     Fabryka danych wyjściowych jest przechowywany w **afgetstarted** zgodnie z konfiguracją w szablonie usługi Resource Manager.
2. Kliknij przycisk **adfgetstarted**.
3. Kliknij dwukrotnie **partitioneddata**. Zostanie wyświetlony **roku = 2014** folderu, ponieważ wszystkie dzienniki sieci web są dniu 2014 roku.

    ![Azure HDInsight fabryki danych na żądanie Hive potoku dane wyjściowe działania](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Jeśli możesz przejść do szczegółów listy, zostanie wyświetlona trzy foldery stycznia, lutego i marca. I ma dziennika dla każdego miesiąca.

    ![Azure HDInsight fabryki danych na żądanie Hive potoku dane wyjściowe działania](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>Jednostki usługi Data Factory w szablonie
Oto, jak szablon Menedżera zasobów najwyższego poziomu dla fabryki danych wygląda następująco:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>Definiowanie fabryki danych
Fabrykę danych definiuje się w szablonie usługi Resource Manager jak pokazano w następującym przykładzie:  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
DataFactoryName to nazwa fabryki danych, które można określić podczas wdrażania szablonu. Fabryka danych jest obecnie obsługiwane tylko w regionach wschodnie stany USA, zachodnie stany USA i Europa Północna, Europa.

### <a name="defining-entities-within-the-data-factory"></a>Definiowanie jednostek w fabryce danych
Następujące jednostki usługi Data Factory są zdefiniowane w szablonie JSON:

* [Połączona usługa Azure Storage](#azure-storage-linked-service)
* [Połączona usługa HDInsight na żądanie](#hdinsight-on-demand-linked-service)
* [Wejściowy zestaw danych obiektów blob platformy Azure](#azure-blob-input-dataset)
* [Wyjściowy zestaw danych obiektów blob platformy Azure](#azure-blob-output-dataset)
* [Potok danych z działaniem kopiowania](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Połączona usługa Azure Storage łączy konto magazynu Azure z fabryką danych. W tym samouczku tego samego konta magazynu jest używany jako domyślne konto magazynu usługi HDInsight, Magazyn danych wejściowych i przechowywania danych wyjściowych. W związku z tym można zdefiniować tylko jednego magazynu Azure połączonej usługi. W definicji połączonej usługi Określ nazwę i klucza konta magazynu Azure. Szczegóły dotyczące właściwości JSON używanych do definiowania połączonej usługi Azure Storage zawiera temat [Połączona usługa Azure Storage](../data-factory/connector-azure-blob-storage.md).

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
Parametr **connectionString** używa parametrów storageAccountName i storageAccountKey. Możesz określić wartości dla parametrów podczas wdrażania szablonu.  

#### <a name="hdinsight-on-demand-linked-service"></a>Połączona usługa HDInsight na żądanie
W definicji usługi HDInsight połączony na żądanie można określić wartości parametrów konfiguracyjnych, które są używane przez usługi fabryka danych do tworzenia klastra usługi HDInsight Hadoop w czasie wykonywania. Szczegółowe informacje o właściwościach JSON używanych do definiowania połączonej usługi HDInsight na żądanie zawiera temat [Usługi połączone usługi Compute](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service).  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
Pamiętaj o następujących kwestiach:

* Tworzy fabrykę danych **opartych na systemie Linux** klastra usługi HDInsight dla Ciebie.
* Klaster usługi HDInsight Hadoop jest tworzony w tym samym regionie co konto magazynu.
* Powiadomienie *timeToLive* ustawienie. Fabryka danych automatycznie usuwa klastra, po bezczynności klastra przez 30 minut.
* Klaster usługi HDInsight tworzy **kontener domyślny** w magazynie obiektów blob określonym w kodzie JSON (**linkedServiceName**). Usługa HDInsight nie powoduje usunięcia tego kontenera w przypadku usunięcia klastra. To zachowanie jest celowe. W przypadku połączonej usługi HDInsight na żądanie klaster usługi HDInsight jest tworzony za każdym razem, gdy trzeba przetworzyć wycinek — o ile w tym momencie nie istnieje aktywny klaster (**timeToLive**) — i zostaje usunięty po zakończeniu przetwarzania.

Szczegółowe informacje znajdują się w artykule [On-demand HDInsight Linked Service](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Połączona usługa HDInsight na żądanie).

> [!IMPORTANT]
> Po przetworzeniu większej liczby wycinków w usłudze Azure Blob Storage będzie widocznych wiele kontenerów. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne ze wzorcem: „adf**twojanazwafabrykidanych**-**nazwapołączonejusługi**-znacznikdatygodziny”. Aby usunąć kontenery z usługi Azure Blob Storage, użyj takich narzędzi, jak [Microsoft Storage Explorer](http://storageexplorer.com/).

#### <a name="azure-blob-input-dataset"></a>Wejściowy zestaw danych obiektów blob platformy Azure
W definicji zestawu danych wejściowych należy określić nazwy kontenera obiektów blob, folderu i pliku zawierającego dane wejściowe. Szczegóły dotyczące właściwości JSON używanych do definiowania zestawu danych obiektów blob platformy Azure zawiera temat [Azure Blob dataset properties](../data-factory/connector-azure-blob-storage.md) (Właściwości zestawu danych obiektów blob plaformy Azure).

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

Zwróć uwagę poniższe ustawienia określone w definicji JSON:

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Wyjściowy zestaw danych obiektów blob platformy Azure
W definicji zestawu danych wyjściowych należy określić nazwy kontenera obiektów blob i folder, który przechowuje danych wyjściowych. Szczegóły dotyczące właściwości JSON używanych do definiowania zestawu danych obiektów blob platformy Azure zawiera temat [Azure Blob dataset properties](../data-factory/connector-azure-blob-storage.md) (Właściwości zestawu danych obiektów blob plaformy Azure).  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

FolderPath Określa ścieżkę do folderu, który przechowuje danych wyjściowych:

```json
"folderPath": "adfgetstarted/partitioneddata",
```

[Dostępności zestawu danych](../data-factory/concepts-datasets-linked-services.md) ustawienie wygląda następująco:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

W fabryce danych Azure dostępności zestawu danych wyjściowych dysków potoku. W tym przykładzie wycinek jest tworzony co miesiąc ostatniego dnia miesiąca (EndOfInterval). 

#### <a name="data-pipeline"></a>Potok danych
Należy zdefiniować potok, który przekształca danych przez uruchomienie skryptu Hive w klastrze usługi Azure HDInsight na żądanie. Opisy elementów JSON używanych do definiowania potoku w tym przykładzie zawiera temat [Pipeline JSON](../data-factory/concepts-pipelines-activities.md) (Kod JSON potoku).

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

Potok zawiera jedno działanie, HDInsightHive działania. Jak zarówno rozpoczęcia i zakończenia daty są styczeń 2016 r., dane tylko jeden miesiąc (wycinek) jest przetwarzane. Zarówno *start* i *zakończenia* działania mają datę przeszłą, więc fabryki danych przetwarza dane bezpośrednio w miesiącu. Jeśli punkt końcowy jest datą przyszłą, fabryki danych tworzy wycinek innym czasie. Aby uzyskać więcej informacji, zobacz [planowania fabryki danych i wykonywania](../data-factory/v1/data-factory-scheduling-and-execution.md).

## <a name="clean-up-the-tutorial"></a>Czyszczenie na koniec samouczka

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>Usuń kontenerów obiektów blob utworzoną przez klaster usługi HDInsight na żądanie
Z usługą HDInsight połączony na żądanie klaster usługi HDInsight jest tworzony za każdym razem, gdy wycinek ma zostać przetworzony, chyba że istnieje istniejącego klastra na żywo (timeToLive); i klastra jest usuwany po zakończeniu przetwarzania. Dla każdego klastra fabryki danych Azure tworzy kontener obiektów blob w magazynie obiektów blob platformy Azure, używane jako konto domyślne stroage dla klastra. Nawet po usunięciu klastra usługi HDInsight domyślnego kontenera magazynu obiektów blob i skojarzonego konta magazynu nie zostaną usunięte. To zachowanie jest celowe. Po przetworzeniu większej liczby wycinków w usłudze Azure Blob Storage będzie widocznych wiele kontenerów. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adfyourdatafactoryname-linkedservicename-datetimestamp`.

Usuń **adfjobs** i **adfyourdatafactoryname-linkedservicename-datetimestamp** folderów. Kontener adfjobs zawiera z fabryki danych Azure z dziennikami zadań.

### <a name="delete-the-resource-group"></a>Usuń grupę zasobów
[Usługa Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) służy do wdrażania, zarządzania i monitorowania rozwiązanie w formie grupy.  Usunięcie grupy zasobów powoduje usunięcie wszystkich składników w grupie.  

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **grup zasobów** w lewym okienku.
3. Kliknij nazwę grupy zasobów utworzonej za pomocą skryptu programu PowerShell. Jeśli masz zbyt wiele grup zasobów na liście, użyj filtru. Nazwa grupy zasobów zostanie otwarty w nowym bloku.
4. Na **zasobów** kafelka, użytkownik ma domyślne konto magazynu i fabryki danych, chyba że współużytkować grupy zasobów z innymi projektami na liście.
5. Kliknij przycisk **usunąć** górnej części bloku. To spowoduje usunięcie konta magazynu i dane przechowywane na koncie magazynu.
6. Wprowadź nazwę grupy zasobów, aby potwierdzić usunięcie, a następnie kliknij przycisk **usunąć**.

W przypadku, gdy nie chcesz usunąć konto magazynu podczas usuwania grupy zasobów, należy wziąć pod uwagę następujące architektury, oddzielając danych biznesowych z domyślnego konta magazynu. W takim przypadku jedna grupa zasobów dla konta magazynu danych biznesowych, i inne grupy zasobów dla domyślnego konta magazynu dla usługi HDInsight połączone usługi i fabryki danych. Po usunięciu drugiej grupy zasobów nie ma wpływu na koncie magazynu danych biznesowych. W tym celu:

* Dodaj następujący element do grupy zasobów najwyższego poziomu, wraz z zasobów Microsoft.DataFactory/datafactories w szablonie usługi Resource Manager. Tworzy konto magazynu:

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* Dodaj nowy punkt połączonej usługi do nowego konta magazynu:

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* Skonfiguruj usługą usługi HDInsight na żądanie, połączony z dodatkowych dependsOn i additionalLinkedServiceNames:

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>Kolejne kroki
W tym artykule ma przedstawiono sposób tworzenia klastra usługi HDInsight na żądanie do przetworzenia zadań Hive za pomocą fabryki danych Azure. Aby dowiedzieć się więcej:

* [Samouczek Hadoop: rozpoczynanie pracy z opartą na systemie Linux platformą Hadoop w usłudze HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Tworzenie klastrów opartych na systemie Linux platformą Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Dokumentacja dotycząca usługi HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Dokumentacja fabryki danych](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>Dodatek

### <a name="azure-cli-script"></a>Skryptu interfejsu wiersza polecenia platformy Azure
Zamiast tego samouczka należy za pomocą programu Azure PowerShell można użyć wiersza polecenia platformy Azure. Aby użyć wiersza polecenia platformy Azure, należy najpierw zainstalować wiersza polecenia platformy Azure, zgodnie z poniższych instrukcji:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Przygotuj magazyn i skopiować pliki za pomocą wiersza polecenia platformy Azure

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

Nazwa kontenera jest *adfgetstarted*. Zachować, ponieważ jest on. W przeciwnym razie należy zaktualizować szablonu usługi Resource Manager. Aby uzyskać pomoc dotyczącą tego skryptu interfejsu wiersza polecenia, zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage](../storage/common/storage-azure-cli.md).
