---
title: Środowiska obliczeniowe obsługiwane przez usługę Azure Data Factory
description: Dowiedz się więcej o środowiskach obliczeniowych, których można używać w potokach usługi Azure Data Factory (takich jak Usługa Azure HDInsight) do przekształcania lub przetwarzania danych.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0cc7c3b7d8b364e0bcca671efaff2cf324695428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281550"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Środowiska obliczeniowe obsługiwane przez usługę Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Obliczynie połączonych usług w obszarze](../compute-linked-services.md).

W tym artykule opisano środowiska obliczeniowe, których można używać do przetwarzania lub przekształcania danych. Zawiera również szczegółowe informacje o różnych konfiguracjach (na żądanie w porównaniu do bring-your-own), które usługa Data Factory obsługuje podczas konfigurowania połączonych usług łączących te środowiska obliczeniowe z fabryką danych platformy Azure.

Poniższa tabela zawiera listę środowisk obliczeniowych, które są obsługiwane przez fabrykę danych, oraz działania, które można na nich uruchomić. 

| Środowisko obliczeniowe                      | Działania                               |
| ---------------------------------------- | ---------------------------------------- |
| [Klaster usługi Azure HDInsight na żądanie](#azure-hdinsight-on-demand-linked-service) lub [własny klaster usługi HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Ul](data-factory-hive-activity.md), [Świnia](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [Dotnet](data-factory-use-custom-activities.md) |
| [Uczenie maszynowe platformy Azure](#azure-machine-learning-linked-service) | [Działania usługi Machine Learning: wykonywanie wsadowe i aktualizacja zasobów](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Usługa Azure SQL](#azure-sql-linked-service), [usługa Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), sql [server](#sql-server-linked-service) | [Działania procedur składowanych](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>Wersje HDInsight obsługiwane w układzie danych
Usługa Azure HDInsight obsługuje wiele wersji klastra Hadoop, które można wdrożyć w dowolnym momencie. Każda obsługiwana wersja tworzy określoną wersję dystrybucji Hortonworks Data Platform (HDP) i zestaw składników w dystrybucji. 

Firma Microsoft aktualizuje listę obsługiwanych wersji HDInsight za pomocą najnowszych składników ekosystemu Hadoop i poprawek. Aby uzyskać szczegółowe informacje, zobacz [Obsługiwane wersje programu HDInsight](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linux hdinsight wersja 3.3 został wycofany 31 lipca 2017. Klienci usług połączonych usługi HDInsight w wersji 1 na żądanie firmy Data Factory były podawane do 15 grudnia 2017 r., aby przetestować i uaktualnić do nowszej wersji usługi HDInsight. Usługa HDInsight oparta na systemie Windows zostanie wycofana 31 lipca 2018 r.
>
> 

### <a name="after-the-retirement-date"></a>Po dacie przejścia na emeryturę 

Po 15 grudnia 2017 r.:

- Nie można już tworzyć klastrów HDInsight w wersji 3.3 (lub wcześniejszych wersji) opartych na systemie Linux przy użyciu połączonej usługi HDInsight na żądanie w wersji 1 usługi data factory. 
- Jeśli [właściwości **osType** i **Version** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) nie są jawnie określone w definicji JSON dla istniejącej usługi połączonej HDInsight w wersji 1 na żądanie, wartość domyślna zostanie zmieniona z **Version=3.1, osType=Windows** **to Version=\<najnowsza wersja domyślna\>HDI (https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType=Linux**.

Po 31 lipca 2018 r.:

- Nie można już tworzyć żadnej wersji klastrów HDInsight opartych na systemie Windows przy użyciu połączonej usługi HDInsight na żądanie w usłudze Data Factory w wersji 1. 

### <a name="recommended-actions"></a>Zalecane działania 

- Aby upewnić się, że można używać najnowszych składników ekosystemu Hadoop i poprawek, należy zaktualizować właściwości [ **systemu operacyjnego** i **wersji,** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) których dotyczy problem Data Factory w wersji 1 na żądanie HDInsight połączone definicje usług hdinsight do nowszych wersji HDInsight opartych na systemie Linux (HDInsight 3.6). 
- Przed 15 grudnia 2017 r. przetestuj działania przesyłania strumieniowego data factory w wersji 1, świnia, mapreduce i usługi przesyłania strumieniowego Hadoop, które odwołują się do połączonej usługi, których dotyczy problem. Upewnij się, że są one zgodne z nowymi **wartościami domyślnymi systemu operacyjnego i** **Version** (**Version=3.6**, **osType=Linux**) lub jawną wersją HDInsight i typem systemu operacyjnego, do którego uaktualniasz. 
  Aby dowiedzieć się więcej o zgodności, zobacz [Migrowanie z klastra HDInsight opartego na systemie Windows do klastra opartego na systemie Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) oraz [Jakie składniki i wersje usługi Hadoop są dostępne w programie HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Aby kontynuować korzystanie z usługi połączonej HDInsight usługi HDInsight w wersji 1 na żądanie w celu utworzenia klastrów HDInsight opartych na systemie Windows, należy jawnie ustawić **system operacyjny** na **system Windows** przed 15 grudnia 2017 r. Zaleca się migrację do klastrów HDInsight opartych na systemie Linux przed 31 lipca 2018 r. 
- Jeśli używasz usługi połączonej HDInsight na żądanie do wykonywania działania niestandardowego Usługi Data Factory w wersji 1 DotNet, zaktualizuj definicję JSON działania niestandardowego DotNet, aby zamiast tego użyć połączonej usługi Azure Batch. Aby uzyskać więcej informacji, zobacz [Używanie działań niestandardowych w potoku usługi Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Jeśli używasz istniejącego, bring-your-own klastra HDInsight połączone urządzenie w fabryce danych w wersji 1 lub bring-your-own i na żądanie HDInsight połączone usługi w usłudze Azure Data Factory, nie jest wymagane żadne działanie. W tych scenariuszach zasady obsługi najnowszej wersji klastrów HDInsight są już wymuszane. 
>
> 


## <a name="on-demand-compute-environment"></a>Środowisko obliczeniowe na żądanie
W konfiguracji na żądanie fabryka danych w pełni zarządza środowiskiem obliczeniowym. Fabryka danych automatycznie tworzy środowisko obliczeniowe przed przesłaniem zadania do przetwarzania danych. Po zakończeniu zadania usługa Data Factory usuwa środowisko obliczeniowe. 

Można utworzyć połączony serwis dla środowiska obliczeniowego na żądanie. Za pomocą połączonej usługi można skonfigurować środowisko obliczeniowe i kontrolować szczegółowe ustawienia wykonywania zadań, zarządzania klastrami i akcji boottrappingu.

> [!NOTE]
> Obecnie konfiguracja na żądanie jest obsługiwana tylko dla klastrów HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Połączona usługa Azure HDInsight na żądanie
Usługa Data Factory może automatycznie tworzyć klaster HDInsight oparty na systemie Windows lub Linux na żądanie do przetwarzania danych. Klaster jest tworzony w tym samym regionie co konto magazynu skojarzone z klastrem. Użyj właściwości JSON **linkedServiceName,** aby utworzyć klaster.

Zwróć uwagę na następujące *kluczowe* punkty dotyczące usługi połączonej HDInsight na żądanie:

* Klaster HDInsight na żądanie nie jest wyświetlany w subskrypcji platformy Azure. Usługa Data Factory zarządza klastrem HDInsight na żądanie w Twoim imieniu.
* Dzienniki dla zadań, które są uruchamiane w klastrze HDInsight na żądanie są kopiowane do konta magazynu skojarzonego z klastrem HDInsight. Aby uzyskać dostęp do tych dzienników, w witrynie Azure portal przejdź do okienka **Szczegóły przebiegu działania.** Aby uzyskać więcej informacji, zobacz [Monitorowanie potoków i zarządzanie nimi](data-factory-monitor-manage-pipelines.md).
* Opłata jest naliczana tylko za czas, w której klaster HDInsight działa zadania.

> [!IMPORTANT]
> Zazwyczaj trwa *20 minut* lub więcej do aprowizowania klastra HDInsight na żądanie.
>
> 

### <a name="example"></a>Przykład
Następujący JSON definiuje usługi połączone HDInsight oparte na systemie Linux na żądanie. Usługa Data Factory automatycznie tworzy klaster HDInsight *oparty na systemie Linux* podczas przetwarzania plasterka danych. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> Klaster HDInsight tworzy *domyślny kontener* w magazynie obiektów blob platformy Azure, który określisz we właściwości JSON **linkedServiceName.** Zgodnie z projektem usługa HDInsight nie usuwa tego kontenera po usunięciu klastra. W usłudze połączonej HDInsight na żądanie klaster HDInsight jest tworzony za każdym razem, gdy plasterek musi zostać przetworzony, chyba że istnieje istniejący klaster na żywo **(timeToLive).** Klaster jest usuwany po zakończeniu przetwarzania. 
>
> Jak więcej plasterków są przetwarzane, widać wiele kontenerów w magazynie obiektów Blob. Jeśli nie potrzebujesz kontenerów do rozwiązywania zadań, możesz usunąć kontenery, aby zmniejszyć koszt magazynowania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adf<your Data Factory name>-<linked service name>-<date and time>`. Za pomocą narzędzia, takiego jak [Eksplorator magazynu firmy Microsoft,](https://storageexplorer.com/) można usunąć kontenery w magazynie obiektów Blob.
>
> 

### <a name="properties"></a>Właściwości
| Właściwość                     | Opis                              | Wymagany |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Ustaw właściwość typu na **HDInsightOnDemand**. | Tak      |
| clusterSize                  | Liczba węzłów roboczych i danych w klastrze. Klaster HDInsight jest tworzony z 2 węzłami głównymi, oprócz liczby węzłów procesu roboczego, które można określić dla tej właściwości. Węzły mają rozmiar Standard_D3, który ma 4 rdzenie. Klaster węzłów 4-procesowych przyjmuje 24 rdzenie (4\*4 = 16 rdzeni dla węzłów procesu roboczego oraz 2\*4 = 8 rdzeni dla węzłów głównych). Aby uzyskać szczegółowe informacje na temat Standard_D3 warstwy, zobacz [Tworzenie klastrów Hadoop opartych na systemie Linux w umiaśnik HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Tak      |
| timeToLive                   | Dozwolony czas bezczynności dla klastra HDInsight na żądanie. Określa, jak długo klaster HDInsight na żądanie pozostaje aktywny po zakończeniu wykonywania działania, jeśli w klastrze nie ma innych aktywnych zadań.<br /><br />Na przykład jeśli uruchomienie działania trwa 6 minut, a **timeToLive** jest ustawiona na 5 minut, klaster pozostaje przy życiu przez 5 minut po 6 minutach przetwarzania przebiegu działania. Jeśli w oknie 6-minutowym zostanie wykonane inne działanie, jest ono przetwarzane przez ten sam klaster.<br /><br />Tworzenie klastra HDInsight na żądanie jest kosztowną operacją (może to trochę potrwać). Użyj tego ustawienia w razie potrzeby, aby poprawić wydajność fabryki danych, ponownie korzystając z klastra HDInsight na żądanie.<br /><br />Jeśli ustawisz **timeToLive** wartość **0**, klaster jest usuwany po zakończeniu uruchamiania działania. Jednak jeśli ustawisz wysoką wartość, klaster może pozostać bezczynny, niepotrzebnie powodując wysokie koszty. Ważne jest, aby ustawić odpowiednią wartość w zależności od potrzeb.<br /><br />Jeśli **timeToLive** wartość jest odpowiednio ustawiona, wiele potoków może współużytkować wystąpienie klastra HDInsight na żądanie. | Tak      |
| version                      | Wersja klastra HDInsight. Aby zapoznać się z dozwolonymi wersjami hdinsight, zobacz [Obsługiwane wersje HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Jeśli ta wartość nie jest określona, używana jest [najnowsza domyślna wersja HDI.](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) | Nie       |
| linkedServiceName            | Usługa połączona usługi Azure Storage, która ma być używana przez klaster na żądanie do przechowywania i przetwarzania danych. Klaster HDInsight jest tworzony w tym samym regionie co to konto magazynu.<p>Obecnie nie można utworzyć klastra HDInsight na żądanie, który używa usługi Azure Data Lake Store jako magazynu. Jeśli chcesz przechowywać dane wynikowe z przetwarzania HDInsight w magazynie Usługi Data Lake, użyj copy activity, aby skopiować dane z magazynu obiektów Blob do magazynu usługi Data Lake Store. </p> | Tak      |
| additionalLinkedServiceNames | Określa dodatkowe konta magazynu dla usługi połączonej HDInsight. Fabryka danych rejestruje konta magazynu w Twoim imieniu. Te konta magazynu muszą znajdować się w tym samym regionie co klaster HDInsight. Klaster HDInsight jest tworzony w tym samym regionie co konto magazynu, które jest określone przez **właściwość linkedServiceName.** | Nie       |
| osType                       | Typ systemu operacyjnego. Dozwolone wartości to **Linux** i **Windows**. Jeśli ta wartość nie **Linux** jest określona, linux jest używany.  <br /><br />Zdecydowanie zalecamy używanie klastrów HDInsight opartych na systemie Linux. Data wycofania usługi HDInsight w systemie Windows to 31 lipca 2018 r. | Nie       |
| hcatalogLinkedServiceName    | Nazwa usługi połączonej sql platformy Azure, która wskazuje na bazę danych HCatalog. Klaster HDInsight na żądanie jest tworzony przy użyciu bazy danych SQL jako magazynu metastore. | Nie       |

#### <a name="example-linkedservicenames-json"></a>Przykład: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Właściwości zaawansowane
W przypadku szczegółowej konfiguracji klastra HDInsight na żądanie można określić następujące właściwości:

| Właściwość               | Opis                              | Wymagany |
| :--------------------- | :--------------------------------------- | :------- |
| coreKonfiguracja      | Określa podstawowe parametry konfiguracji (core-site.xml) dla klastra HDInsight, który ma zostać utworzony. | Nie       |
| konfiguracja hBaseConfiguracja     | Określa parametry konfiguracji bazy danych HBase (hbase-site.xml) dla klastra HDInsight. | Nie       |
| konfiguracja hdfs      | Określa parametry konfiguracji hdfs (hdfs-site.xml) dla klastra HDInsight. | Nie       |
| konfiguracja hiveConfiguration      | Określa parametry konfiguracji gałęzi (hive-site.xml) dla klastra HDInsight. | Nie       |
| mapReduceConfiguration | Określa parametry konfiguracji MapReduce (mapred-site.xml) dla klastra HDInsight. | Nie       |
| oozieKonfiguracja     | Określa parametry konfiguracji Oozie (oozie-site.xml) dla klastra HDInsight. | Nie       |
| stormKonfiguracja     | Określa parametry konfiguracji burzy (storm-site.xml) dla klastra HDInsight. | Nie       |
| konfiguracja przędzy      | Określa parametry konfiguracji YARN (yarn-site.xml) dla klastra HDInsight. | Nie       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Przykład: Konfiguracja klastra HDInsight na żądanie z zaawansowanymi właściwościami

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Rozmiary węzłów
Aby określić rozmiar węzłów head, data i ZooKeeper, należy użyć następujących właściwości: 

| Właściwość          | Opis                              | Wymagany |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Ustawia rozmiar węzła głównego. Wartość domyślna to **Standard_D3**. Aby uzyskać szczegółowe informacje, zobacz [Określanie rozmiarów węzłów](#specify-node-sizes). | Nie       |
| dataNodeSize      | Ustawia rozmiar węzła danych. Wartość domyślna to **Standard_D3**. | Nie       |
| zookeeperNodeSize | Ustawia rozmiar węzła ZooKeeper. Wartość domyślna to **Standard_D3**. | Nie       |

#### <a name="specify-node-sizes"></a>Określanie rozmiarów węzłów
W przypadku wartości ciągów, które należy określić dla właściwości opisanych w poprzedniej sekcji, zobacz [Rozmiary maszyn wirtualnych](../../virtual-machines/linux/sizes.md). Wartości muszą być zgodne z poleceniami cmdlet i interfejsami API, do których odwołują się [rozmiary maszyn wirtualnych.](../../virtual-machines/linux/sizes.md) Duży (domyślny) rozmiar węzła danych ma 7 GB pamięci. Może to nie być wystarczające dla twojego scenariusza. 

Jeśli chcesz utworzyć węzły głównych i węzły robocze o rozmiarze D4, określ **Standard_D4** jako wartość właściwości **headNodeSize** i **dataNodeSize:** 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Jeśli ustawisz niepoprawną wartość dla tych właściwości, może zostać wyświetlony następujący komunikat:

  Nie można utworzyć klastra. Wyjątek: nie można ukończyć operacji tworzenia klastra. Operacja zakończona niepowodzeniem z kodem „400”. Końcowy stan klastra: „Błąd”. Komunikat: "PreClusterCreationValidationFailure". 
  
Jeśli widzisz ten komunikat, upewnij się, że używasz nazw poleceń cmdlet i interfejsu API z tabeli w [rozmiarach maszyn wirtualnych](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Obecnie usługa Data Factory nie obsługuje klastrów HDInsight, które używają magazynu Usługi Data Lake Store jako magazynu podstawowego. Użyj usługi Azure Storage jako magazynu podstawowego dla klastrów HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Przynieś własne środowisko obliczeniowe
Istniejące środowisko obliczeniowe można zarejestrować jako usługę połączony w fabryce danych. Zarządzasz środowiskiem obliczeniowym. Usługa Fabryka danych używa środowiska obliczeniowego do wykonywania działań.

Ten typ konfiguracji jest obsługiwany dla następujących środowisk obliczeniowych:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL Database, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Usługa połączona usługi Azure HDInsight
Można utworzyć usługę połączona HDInsight, aby zarejestrować własny klaster HDInsight w fabryce danych.

### <a name="example"></a>Przykład

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Właściwości
| Właściwość          | Opis                              | Wymagany |
| ----------------- | ---------------------------------------- | -------- |
| type              | Ustaw właściwość typu na **HDInsight**. | Tak      |
| clusterUri        | Identyfikator URI klastra HDInsight.        | Tak      |
| nazwa użytkownika          | Nazwa konta użytkownika, które ma być używane do łączenia się z istniejącym klastrem HDInsight. | Tak      |
| hasło          | Hasło do konta użytkownika.   | Tak      |
| linkedServiceName | Nazwa usługi połączonej magazynu, która odwołuje się do magazynu obiektów Blob używanego przez klaster HDInsight. <p>Obecnie nie można określić usługi połączonej z magazynem Danych Lake dla tej właściwości. Jeśli klaster HDInsight ma dostęp do magazynu Usługi Data Lake Store, można uzyskać dostęp do danych w magazynie usługi Data Lake store ze skryptów Hive lub Pig. </p> | Tak      |

## <a name="azure-batch-linked-service"></a>Usługa połączona z usługą Azure Batch
Można utworzyć usługę połączony batch, aby zarejestrować pulę partii maszyn wirtualnych (VM) w fabryce danych. Działania niestandardowe platformy Microsoft .NET można uruchamiać przy użyciu usługi Batch lub HDInsight.

Jeśli użytkownik jest nowy w korzystaniu z usługi Batch:

* Dowiedz się więcej o [podstawach usługi Azure Batch](../../batch/batch-technical-overview.md).
* Dowiedz się więcej o policzce cmdlet [New-AzureBatchAccount.](https://msdn.microsoft.com/library/mt125880.aspx) To polecenie cmdlet służy do tworzenia konta usługi Batch. Można też utworzyć konto usługi Batch przy użyciu [portalu Azure.](../../batch/batch-account-create-portal.md) Aby uzyskać szczegółowe informacje dotyczące używania polecenia cmdlet, zobacz [Zarządzanie kontem usługi Batch za pomocą programu PowerShell](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Dowiedz się więcej o powiększe [nowej usługi AzureBatchPool.](https://msdn.microsoft.com/library/mt125936.aspx) Ta polecenie cmdlet służy do tworzenia puli partii.

### <a name="example"></a>Przykład

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Dla **właściwości accountName** dołącz **\< plik . \> nazwę regionu** do nazwy konta wsadowego. Przykład:

```json
"accountName": "mybatchaccount.eastus"
```

Inną opcją jest zapewnienie punktu końcowego **batchUri.** Przykład:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Właściwości
| Właściwość          | Opis                              | Wymagany |
| ----------------- | ---------------------------------------- | -------- |
| type              | Ustaw właściwość typu na **AzureBatch**. | Tak      |
| accountName       | Nazwa konta usługi Batch.         | Tak      |
| Accesskey         | Klucz dostępu dla konta Batch.  | Tak      |
| nazwa puli          | Nazwa puli maszyn wirtualnych.    | Tak      |
| linkedServiceName | Nazwa połączonej usługi magazynu, która jest skojarzona z tą usługą połączona przez partię. Ta połączona usługa jest używana dla plików przemieszczania, które są wymagane do uruchomienia działania i do przechowywania dzienników wykonywania działań. | Tak      |

## <a name="azure-machine-learning-linked-service"></a>Usługa połączona usługi usługi Azure Machine Learning
Można utworzyć usługę połączony uczenia maszynowego, aby zarejestrować punkt końcowy oceniania wsadowego uczenia maszynowego w fabryce danych.

### <a name="example"></a>Przykład

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Właściwości
| Właściwość   | Opis                              | Wymagany |
| ---------- | ---------------------------------------- | -------- |
| Typ       | Ustaw właściwość typu na **AzureML**. | Tak      |
| mlPunkt | Adres URL oceniania partii.                   | Tak      |
| apiKey (klawisz apiKey)     | Interfejs API opublikowanego modelu obszaru roboczego.     | Tak      |

## <a name="azure-data-lake-analytics-linked-service"></a>Usługa połączona z usługą Azure Data Lake Analytics
Usługę połączony z usługą Data Lake Analytics można utworzyć, aby połączyć usługę obliczeniową Usługi Data Lake Analytics z fabryką danych platformy Azure. Działanie U-SQL usługi Data Lake Analytics w potoku odnosi się do tej połączonej usługi. 

W poniższej tabeli opisano właściwości ogólne, które są używane w definicji JSON:

| Właściwość                 | Opis                              | Wymagany                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Ustaw właściwość typu na **AzureDataLakeAnalytics**. | Tak                                      |
| accountName          | Nazwa konta Usługi Data Lake Analytics.  | Tak                                      |
| dataLakeAnalyticsUri | Identyfikator URI analizy usługi Data Lake Analytics.           | Nie                                       |
| subscriptionId       | Identyfikator subskrypcji platformy Azure.                    | Nie<br /><br />(Jeśli nie zostanie określona, używana jest subskrypcja fabryki danych). |
| resourceGroupName    | Nazwa grupy zasobów platformy Azure.                | Nie<br /><br /> (Jeśli nie zostanie określona, używana jest grupa zasobów fabryki danych). |

### <a name="authentication-options"></a>Opcje uwierzytelniania
W przypadku usługi połączonej usługi Usługi Usługi Data Lake Analytics można wybrać między uwierzytelnianiem przy użyciu jednostki usługi lub poświadczeń użytkownika.

#### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie jednostkowe usługi (zalecane)
Aby użyć uwierzytelniania jednostkowego usługi, zarejestruj jednostkę aplikacji w usłudze Azure Active Directory (Azure AD). Następnie przyznaj dostęp usługi Azure AD do magazynu usługi Data Lake. Aby uzyskać szczegółowe kroki, zobacz [Uwierzytelnianie usługi do usługi](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Użyj uwierzytelniania głównego usługi, określając następujące właściwości:

| Właściwość                | Opis                              | Wymagany |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Identyfikator klienta aplikacji.     | Tak      |
| servicePrincipalKey | Klucz aplikacji.           | Tak      |
| Dzierżawy              | Informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Aby uzyskać te informacje, umieść wskaźnik myszy w prawym górnym rogu witryny Azure portal. | Tak      |

**Przykład: uwierzytelnianie jednostki usługi**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Uwierzytelnianie poświadczeń użytkownika
W przypadku uwierzytelniania poświadczeń użytkownika w usłudze Data Lake Analytics należy określić następujące właściwości:

| Właściwość          | Opis                              | Wymagany |
| :---------------- | :--------------------------------------- | :------- |
| autoryzacja | W Edytorze fabryki danych wybierz przycisk **Autoryzuj.** Wprowadź poświadczenie, które przypisuje automatyczniegenerowany adres URL autoryzacji do tej właściwości. | Tak      |
| Sessionid     | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być używany tylko raz. To ustawienie jest generowane automatycznie podczas korzystania z Edytora fabryki danych. | Tak      |

**Przykład: uwierzytelnianie poświadczeń użytkownika**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Wygaśnięcie tokenu
Kod autoryzacji wygenerowany przez wybranie przycisku **Autoryzuj** wygasa po ustawionym interwale. 

Po wygaśnięciu tokenu uwierzytelniania może zostać wyświetlony następujący komunikat o błędzie: 

  Błąd operacji poświadczeń: invalid_grant — AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS70008: Udzielona dotacja dostępu wygasła lub została odwołana. Identyfikator śledzenia: d18629e8-af88-43c5-88e3-d8419eb1fca1 Identyfikator korelacji: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Sygnatura czasowa: 2015-12-15 21:09:31Z

W poniższej tabeli przedstawiono wygaśnięcia według typu konta użytkownika: 

| Typ użytkownika                                | Wygasa po                            |
| :--------------------------------------- | :--------------------------------------- |
| Konta użytkowników, które *nie* są zarządzane przez usługę Azure AD (Hotmail, Live itd.) | 12 godzin.                                 |
| Konta użytkowników *zarządzane* przez usługę Azure AD | 14 dni po ostatnim uruchomieniu plasterka. <br /><br />90 dni, jeśli plasterek oparty na połączonej usłudze opartej na UAuth działa co najmniej raz na 14 dni. |

Aby uniknąć lub rozwiązać ten błąd, ponownie autoryzuj, wybierając przycisk **Autore** po wygaśnięciu tokenu. Następnie ponownie wdrożyć usługę połączone. Można również programowo wygenerować wartości właściwości **sessionId** i **authorization** przy użyciu następującego kodu:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Aby uzyskać szczegółowe informacje na temat klas fabryki danych, które są używane w tym przykładzie kodu, zobacz:
* [Klasa AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [Klasa AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [Klasa AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Dodaj odwołanie do pliku Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll dla klasy **WindowsFormsWebAuthenticationDialog.** 

## <a name="azure-sql-linked-service"></a>Połączona usługa Azure SQL
Można utworzyć usługę połączony SQL i używać jej z [działaniem procedury składowanej](data-factory-stored-proc-activity.md) do wywoływania procedury składowanej z potoku fabryki danych. Aby uzyskać więcej informacji, zobacz [łącznik SQL platformy Azure](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Usługa połączona z usługą Azure SQL Data Warehouse
Można utworzyć usługę połączony z magazynem danych SQL i użyć jej z [działaniem procedury składowanej](data-factory-stored-proc-activity.md) do wywołania procedury składowanej z potoku fabryki danych. Aby uzyskać więcej informacji, zobacz [łącznik usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Usługa połączona z programem SQL Server
Można utworzyć usługę połączony z programem SQL Server i używać jej z [działaniem procedury składowanej](data-factory-stored-proc-activity.md) do wywoływania procedury składowanej z potoku fabryki danych. Aby uzyskać więcej informacji, zobacz [łącznik programu SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

