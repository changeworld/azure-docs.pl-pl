---
title: Obliczenia środowisk obsługiwanych przez usługę Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat środowisk obliczeniowych, w których można użyć w potokach usługi Azure Data Factory (takich jak Azure HDInsight) do przekształcania lub przetwarzania danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0e0a249c53c90d3d8d03dcdb5fbb4f11f31c54df
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565722"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Obliczenia środowisk obsługiwanych przez usługę Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [połączonych usług obliczeniowych w](../compute-linked-services.md).

W tym artykule opisano środowiska obliczeniowego, w których można użyć, aby przetwarzać i przekształcać dane. Udostępnia również szczegółowe informacje o różnych konfiguracjach (na żądanie a bring your own), Data Factory obsługuje podczas konfigurowania połączonych usług, które łączą te obliczenia środowisk na potrzeby usługi Azure data factory.

Poniższa tabela zawiera listę środowisk obliczeniowych, które są obsługiwane przez usługi Data Factory i działania, które można uruchomić na nich. 

| Środowisko obliczeniowe                      | Działania                               |
| ---------------------------------------- | ---------------------------------------- |
| [Klaster usługi Azure HDInsight na żądanie](#azure-hdinsight-on-demand-linked-service) lub [klaster HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning działania: Wykonywanie wsadowe i aktualizacja zasobów](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Język U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md) |
| [Usługi Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [programu SQL Server](#sql-server-linked-service) | [Działania procedur składowanych](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Obsługiwane w usłudze Data Factory wersje HDInsight
Usługa Azure HDInsight obsługuje wielu wersjach klastrów Hadoop, które można wdrożyć w dowolnym momencie. Wszystkie obsługiwane wersje tworzy określoną wersję dystrybucji Hortonworks Data Platform (HDP) i zestaw składników w dystrybucji. 

Firma Microsoft aktualizuje listę obsługiwanych wersji HDInsight przy użyciu najnowszych składników ekosystemu Hadoop i poprawki. Aby uzyskać szczegółowe informacje, zobacz [HDInsight obsługiwane wersje](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> HDInsight opartych na systemie Linux w wersji 3.3 została wycofana 31 lipca 2017 r. Data Factory w wersji 1 HDInsight na żądanie połączone usługi, których klienci wystarczają do 15 grudnia 2017 r do testowania i przeprowadzić uaktualnienie do nowszej wersji HDInsight. HDInsight z systemem Windows zostanie wycofana 31 lipca 2018 r.
>
> 

### <a name="after-the-retirement-date"></a>Po dacie wycofania 

Po 15 grudnia 2017 r.:

- Nie można utworzyć HDInsight opartych na systemie Linux w wersji 3.3 (i starszych) klastrów za pomocą HDInsight na żądanie połączonej usługi w usłudze Data Factory w wersji 1. 
- Jeśli [ **osType** i **wersji** właściwości](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) nie są jawnie określone w definicji JSON dla istniejącej usługi połączonej HDInsight na żądanie w wersji 1 usługi Data Factory , wartością domyślną jest zmieniła się z **wersja 3.1, osType = = Windows** do **wersji =\<najnowszej wersji domyślnej usługi HDI\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType = Linux**.

Po 31 lipca 2018 r.:

- Można już utworzyć dowolną wersję klastry HDInsight oparte na Windows za pomocą usługi połączonej HDInsight na żądanie w usłudze Data Factory w wersji 1. 

### <a name="recommended-actions"></a>Zalecane akcje 

- Aby upewnić się, czy można użyć w najnowszych składników ekosystemu Hadoop i poprawki, należy zaktualizować [ **osType** i **wersji** właściwości](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) dotyczy usługi Data Factory w wersji 1 na żądanie Definicje usługi połączonej HDInsight do nowszej wersji HDInsight opartych na systemie Linux (HDInsight 3.6). 
- 15 grudnia 2017 r. test Data Factory w wersji 1 programu Hive, Pig, MapReduce i Hadoop działania przesyłania strumieniowego, odwołujące się do zainfekowanego połączonej usługi. Upewnij się, że są one zgodne z nowymi **osType** i **wersji** wartości domyślne (**wersji = 3.6**, **osType = Linux**) lub jawne HDInsight w wersji i systemu operacyjnego wpisz w przypadku uaktualniania do. 
  Aby dowiedzieć się więcej na temat zgodności, zobacz [migracji z klastra HDInsight z systemem Windows w klastrze opartych na systemie Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) i [jakie są składniki i wersje dostępne z HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Aby nadal korzystać z usługi połączonej HDInsight na żądanie w wersji 1 usługi Data Factory do tworzenia klastrów HDInsight z systemem Windows, należy jawnie ustawić **osType** do **Windows** przed 15 grudnia 2017 r. Zaleca się migrację z klastrami HDInsight opartych na systemie Linux przed 31 lipca 2018 r. 
- Jeśli używasz usługi połączonej HDInsight na żądanie można wykonać Data Factory w wersji 1 DotNet niestandardowe działanie, zaktualizuj definicję DotNet niestandardowe działania w formacie JSON, aby zamiast tego użyć usługi Azure Batch połączone usługi. Aby uzyskać więcej informacji, zobacz [używanie niestandardowych działań w potoku usługi Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Jeśli używasz istniejących, bring your own klaster HDInsight połączone urządzenia w usługi Data Factory w wersji 1 lub bring your own i na żądanie usługa HDInsight połączonej usługi Azure Data Factory, żadna akcja jest wymagana. W tych scenariuszach najnowsze zasady pomocy technicznej wersji klastrów HDInsight już jest wymuszana. 
>
> 


## <a name="on-demand-compute-environment"></a>Środowisko obliczeniowe na żądanie
W konfiguracji na żądanie usługi Data Factory w pełni zarządza każdym środowiska obliczeniowego. Przed przesłaniem zadania związane z przetwarzaniem danych Data Factory automatycznie utworzy środowiska obliczeniowego. Po zakończeniu zadania usługi Data Factory usuwa środowiska obliczeniowego. 

Można utworzyć połączonej usługi dla środowiska obliczeniowe na żądanie. Użyj połączonej usługi, aby skonfigurować środowisko obliczeniowe oraz do sterowania szczegółowe ustawienia wykonywania zadań, zarządzanie klastrami i uruchamianie akcji.

> [!NOTE]
> Obecnie konfiguracja na żądanie jest obsługiwana tylko w przypadku klastrów HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Usługa Azure HDInsight na żądanie połączonej usługi
Fabryka danych może automatycznie tworzyć klaster HDInsight na żądanie oparte na Windows lub opartych na systemie Linux do przetwarzania danych. Klaster jest tworzony w tym samym regionie co konto magazynu, który jest skojarzony z klastrem. Przy użyciu formatu JSON **linkedServiceName** właściwości do utworzenia klastra.

Należy pamiętać o następujących *klucz* punkty o HDInsight na żądanie połączoną usługę:

* Klaster HDInsight na żądanie nie jest wyświetlana w subskrypcji platformy Azure. Usługa Data Factory zarządza klastra HDInsight na żądanie w Twoim imieniu.
* Dzienniki zadań uruchamianych w klastrze usługi HDInsight na żądanie są kopiowane do konta magazynu, który jest skojarzony z klastrem usługi HDInsight. Aby dostęp do tych dzienników, w witrynie Azure portal przejdź do **szczegóły uruchamiania działania** okienka. Aby uzyskać więcej informacji, zobacz [monitorowanie potoków i zarządzanie nimi](data-factory-monitor-manage-pipelines.md).
* Opłaty są naliczane tylko za czas, który klaster HDInsight działa i uruchomionych zadań.

> [!IMPORTANT]
> Trwa zwykle *20 minut* co najmniej do inicjowania obsługi klastra HDInsight na żądanie.
>
> 

### <a name="example"></a>Przykład
Następujący kod JSON definiuje opartych na systemie Linux usługi połączonej HDInsight na żądanie. Usługa Data Factory automatycznie tworzy *opartych na systemie Linux* klastra HDInsight podczas przetwarzania wycinka danych. 

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
> Klaster HDInsight tworzy *domyślny kontener* w usłudze Azure Blob storage, który określisz w kodzie JSON **linkedServiceName** właściwości. Zgodnie z projektem HDInsight nie powoduje usunięcia tego kontenera, gdy klaster jest usuwany. W usługi połączonej HDInsight na żądanie klaster usługi HDInsight jest tworzony przy każdym przetwarzaniu wycinka wymaga przetworzenia, chyba że istnieje istniejącego klastra na żywo (**timeToLive**). Klaster jest usuwany po zakończeniu przetwarzania. 
>
> Przetworzeniu większej liczby wycinków w usłudze Blob storage będzie widocznych wiele kontenerów. Kontenery nie są potrzebne do rozwiązywania problemów z zadaniami, można usunąć kontener, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adf<your Data Factory name>-<linked service name>-<date and time>`. Można użyć narzędzia, takiego jak [Microsoft Storage Explorer](https://storageexplorer.com/) Aby usunąć kontenery z magazynu obiektów Blob.
>
> 

### <a name="properties"></a>Właściwości
| Właściwość                     | Opis                              | Wymagane |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Ustaw właściwość typu **HDInsightOnDemand**. | Tak      |
| clusterSize                  | Liczba węzłów procesu roboczego i dane w klastrze. Klaster HDInsight jest tworzony z 2 węzłami głównymi, oprócz liczby węzłów procesu roboczego, które określisz dla tej właściwości. Węzły mają rozmiar maszyna wirtualna Standard_D3, która ma 4 rdzenie. Klaster z węzłami procesu roboczego 4 przyjmuje 24 rdzenie (4\*4 = 16 rdzeni dla węzłów procesu roboczego oraz 2\*4 = 8 rdzeni dla węzłów głównych). Aby uzyskać szczegółowe informacje o warstwie maszyna wirtualna Standard_D3, zobacz [opartych na systemie Linux z Tworzenie klastrów usługi Hadoop w HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Yes      |
| timeToLive                   | Dozwolony czas bezczynności, po dla klastra HDInsight na żądanie. Określa, jak długo klastra HDInsight na żądanie pozostaje aktywne po zakończeniu wykonywania działań w przypadku żadnych aktywnych działań w klastrze.<br /><br />Na przykład, jeśli działanie uruchamiania ma 6 minut i **timeToLive** jest ustawiony na 5 minut, pozostaje klaster aktywny przez 5 minut, po upływie 6 minut operacji przetwarzania uruchomienia działania. Jeśli uruchomienie innego działania jest wykonywane w tym 6-minutowym oknie, jest on przetwarzany przez tego samego klastra.<br /><br />Tworzenie klastra usługi HDInsight na żądanie jest kosztowną operacją (może upłynąć trochę czasu). Użyj tego ustawienia, zgodnie z potrzebami, aby poprawić wydajność usługi data Factory dzięki ponownemu wykorzystaniu klastra usługi HDInsight na żądanie.<br /><br />Jeśli ustawisz **timeToLive** wartość **0**, klaster jest usuwany, zaraz po zakończeniu uruchamiania działania. Jednak jeśli ustawisz o wysokiej wartości, klastra mogą pozostać bezczynny, co niepotrzebnie wysokich kosztów. Jest to ważne, aby ustawić odpowiednią wartość zgodnie z potrzebami.<br /><br />Jeśli **timeToLive** odpowiednio wartość, wiele potoków można udostępniać wystąpienia klastra HDInsight na żądanie. | Yes      |
| version                      | Wersja klastra HDInsight. Dla dozwolonych wersji HDInsight, zobacz [HDInsight obsługiwane wersje](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Jeśli ta wartość nie jest określona, [najnowszej wersji domyślnej usługi HDI](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) jest używany. | Nie       |
| linkedServiceName            | Połączona usługa Azure Storage używanego przez klaster na żądanie do przechowywania i przetwarzania danych. Klaster HDInsight jest tworzony w tym samym regionie co konto magazynu.<p>Obecnie nie można utworzyć klastra HDInsight na żądanie, który używa usługi Azure Data Lake Store jako magazyn. Jeśli chcesz przechowywać dane wynikowe z HDInsight przetwarzanie w Data Lake Store, należy użyć działania kopiowania do skopiowania danych z magazynu obiektów Blob do Data Lake Store. </p> | Yes      |
| additionalLinkedServiceNames | Określa dodatkowe konta magazynu dla usługi HDInsight połączone. Data Factory rejestruje kont magazynu w Twoim imieniu. Te konta magazynu musi być w tym samym regionie co klaster HDInsight. Klaster HDInsight jest tworzony w tym samym regionie co konto magazynu, który jest określony przez **linkedServiceName** właściwości. | Nie       |
| osType                       | Typ systemu operacyjnego. Dozwolone wartości to **Linux** i **Windows**. Jeśli ta wartość nie jest określona, **Linux** jest używany.  <br /><br />Zdecydowanie zaleca się przy użyciu klastrów usługi HDInsight opartych na systemie Linux. Data wycofania HDInsight na Windows jest 31 lipca 2018 r. | Nie       |
| hcatalogLinkedServiceName    | Nazwa połączonej usługi SQL Azure, które wskazują na bazę danych HCatalog. Klaster HDInsight na żądanie jest tworzony przy użyciu bazy danych SQL jako magazynu metadanych. | Nie       |

#### <a name="example-linkedservicenames-json"></a>Przykład: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Właściwości zaawansowane
W przypadku szczegółowej konfiguracji klastra HDInsight na żądanie można określić następujące właściwości:

| Właściwość               | Opis                              | Wymagane |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Określa parametry konfiguracji podstawowej (core-site.xml) dla klastra HDInsight, który ma zostać utworzony. | Nie       |
| hBaseConfiguration     | Określa parametry konfiguracji bazy danych HBase (hbase-site.xml) dla klastra HDInsight. | Nie       |
| hdfsConfiguration      | Określa parametry konfiguracji systemu plików HDFS (systemu plików hdfs-site.xml) dla klastra HDInsight. | Nie       |
| hiveConfiguration      | Określa parametry konfiguracji gałęzi (gałęzi site.xml) dla klastra HDInsight. | Nie       |
| mapReduceConfiguration | Określa parametry konfiguracji MapReduce (mapred-site.xml) dla klastra HDInsight. | Nie       |
| oozieConfiguration     | Określa parametry konfiguracji Oozie (oozie-site.xml) dla klastra HDInsight. | Nie       |
| stormConfiguration     | Określa parametry konfiguracji system Storm (storm-site.xml) dla klastra HDInsight. | Nie       |
| yarnConfiguration      | Określa parametry konfiguracji usługi YARN (yarn-site.xml) dla klastra HDInsight. | Nie       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Przykład: Konfiguracja klastra HDInsight na żądanie przy użyciu właściwości zaawansowane

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
Aby określić rozmiar head, danych i węzły dozorcy, należy użyć następujących właściwości: 

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Określa rozmiar węzła głównego. Wartość domyślna to **maszyna wirtualna Standard_D3**. Aby uzyskać więcej informacji, zobacz [Określ rozmiary węzła](#specify-node-sizes). | Nie       |
| dataNodeSize      | Określa rozmiar węzła danych. Wartość domyślna to **maszyna wirtualna Standard_D3**. | Nie       |
| zookeeperNodeSize | Określa rozmiar węzła dozorcy. Wartość domyślna to **maszyna wirtualna Standard_D3**. | Nie       |

#### <a name="specify-node-sizes"></a>Określ rozmiary węzła
Wartości parametrów, które należy określić dla właściwości opisane w poprzedniej sekcji, zobacz [rozmiarów maszyn wirtualnych](../../virtual-machines/linux/sizes.md). Wartości muszą być zgodne z poleceniami cmdlet i interfejsów API do którego odwołuje się [rozmiarów maszyn wirtualnych](../../virtual-machines/linux/sizes.md). Rozmiar węzła danych duże (ustawienie domyślne) ma 7 GB pamięci. To może nie być wystarczające dla danego scenariusza. 

Jeśli chcesz utworzyć D4 rozmiar węzłów głównych i węzłów procesu roboczego, należy określić **maszyna wirtualna Standard_D4** jako wartość pozycji **właściwości headNodeSize** i **dataNodeSize** właściwości: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Jeśli ustawisz nieprawidłowej wartości tych właściwości, może zostać wyświetlony następujący komunikat:

  Nie można utworzyć klastra. Wystąpił wyjątek: Nie można ukończyć operacji tworzenia klastra. Operacja zakończona niepowodzeniem z kodem „400”. Końcowy stan klastra: "Error". Komunikat: "PreClusterCreationValidationFailure". 
  
Jeśli widzisz ten komunikat, upewnij się, że używasz polecenia cmdlet i nazw interfejsu API z tabeli w [rozmiarów maszyn wirtualnych](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Obecnie usługa Data Factory nie obsługuje klastrów HDInsight, które używają Data Lake Store jako magazynu podstawowego. Użyj usługi Azure Storage jako magazynu podstawowego w przypadku klastrów HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Bring your own środowiska obliczeniowego
Istniejące środowisko obliczeniowe mogą zarejestrować jako połączonej usługi w usłudze Data Factory. Możesz zarządzać środowiska obliczeniowego. Usługa Data Factory używa środowiska obliczeniowego do wykonywania działań.

Ten typ konfiguracji jest obsługiwana dla następujących środowisk obliczeniowych:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Usługi Azure SQL Database, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Usługa Azure HDInsight połączone
Można utworzyć usługi połączonej HDInsight, aby zarejestrować swój własny klaster HDInsight z usługą Data Factory.

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
| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| type              | Ustaw właściwość typu **HDInsight**. | Tak      |
| clusterUri        | Identyfikator URI klastra HDInsight.        | Tak      |
| username          | Nazwa konta użytkownika do nawiązywania połączenia z istniejącym klastrem HDInsight. | Yes      |
| password          | Hasło dla konta użytkownika.   | Tak      |
| linkedServiceName | Nazwa połączonej usługi storage odwołująca się do usługi Blob storage używane przez klaster usługi HDInsight. <p>Obecnie nie można określić, że Data Lake Store połączonej usługi dla tej właściwości. Jeśli klaster HDInsight ma dostęp do programu Data Lake Store, mogą uzyskiwać dostęp do danych w Data Lake Store, przez skrypty Hive i Pig. </p> | Yes      |

## <a name="azure-batch-linked-service"></a>Usługa Azure Batch połączone
Utworzysz usługi połączone usługi Batch do rejestrowania maszyn wirtualnych (VM) do usługi data factory puli usługi Batch. Za pomocą usługi Batch lub HDInsight, można uruchomić działania niestandardowe programu Microsoft .NET.

Jeśli jesteś nowym użytkownikiem korzystania z usługi Batch:

* Dowiedz się więcej o [podstawy usługi Azure Batch](../../batch/batch-technical-overview.md).
* Dowiedz się więcej o [New AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) polecenia cmdlet. Użyj tego polecenia cmdlet, aby utworzyć konto usługi Batch. Możesz utworzyć konto usługi Batch za pomocą [witryny Azure portal](../../batch/batch-account-create-portal.md). Aby uzyskać szczegółowe informacje o użyciu polecenia cmdlet, zobacz [przy użyciu programu PowerShell do zarządzania kontem usługi Batch](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Dowiedz się więcej o [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) polecenia cmdlet. Użyj tego polecenia cmdlet, aby utworzyć pulę usługi Batch.

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

Aby uzyskać **accountName** właściwości, dołącz **.\< Nazwa regionu\>**  na nazwę swojego konta usługi batch. Na przykład:

```json
"accountName": "mybatchaccount.eastus"
```

Innym rozwiązaniem jest zapewnienie **batchUri** punktu końcowego. Na przykład:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Właściwości
| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| type              | Ustaw właściwość typu **AzureBatch**. | Yes      |
| accountName       | Nazwa konta usługi Batch.         | Tak      |
| accessKey         | Klucz dostępu dla konta usługi Batch.  | Yes      |
| poolName          | Nazwa puli maszyn wirtualnych.    | Tak      |
| linkedServiceName | Nazwa połączonej usługi storage skojarzonego z tej partii połączoną usługę. Ta połączona usługa jest używana dla tymczasowych plików, które są wymagane do uruchomienia wybranego działania i przechowywać dzienniki wykonywania działań. | Tak      |

## <a name="azure-machine-learning-linked-service"></a>Usługa Azure Machine Learning połączone
Można utworzyć usługi Machine Learning połączone do zarejestrowania punktu końcowego oceniania do usługi data factory partii usługi Machine Learning.

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
| Właściwość   | Opis                              | Wymagane |
| ---------- | ---------------------------------------- | -------- |
| Type       | Ustaw właściwość typu **AzureML**. | Tak      |
| mlEndpoint | Adres URL wsadowego oceniania.                   | Tak      |
| ApiKey     | Interfejs API opublikowanego modelu obszaru roboczego firmy.     | Yes      |

## <a name="azure-data-lake-analytics-linked-service"></a>Usługa Azure Data Lake Analytics, połączone usługi
Można utworzyć usługę połączoną Data Lake Analytics, aby połączyć usługi obliczeniowej usługi Data Lake Analytics z usługi Azure data factory. Działanie U-SQL usługi Data Lake Analytics w potoku odnosi się do tej połączonej usługi. 

W poniższej tabeli opisano ogólne właściwości, które są używane w definicji JSON:

| Właściwość                 | Opis                              | Wymagane                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Ustaw właściwość typu **AzureDataLakeAnalytics**. | Tak                                      |
| accountName          | Nazwa konta usługi Data Lake Analytics.  | Yes                                      |
| dataLakeAnalyticsUri | Identyfikator URI Data Lake Analytics.           | Nie                                       |
| subscriptionId       | Identyfikator subskrypcji platformy Azure.                    | Nie<br /><br />(Jeśli nie zostanie określony, subskrypcję fabryki danych jest używany). |
| resourceGroupName    | Nazwa grupy zasobów platformy Azure.                | Nie<br /><br /> (Jeśli nie zostanie określony, grupy zasobów fabryki danych jest używany). |

### <a name="authentication-options"></a>Opcje uwierzytelniania
Usługi połączone usługi Data Lake Analytics można wybrać uwierzytelnianie przy użyciu nazwy głównej usługi lub poświadczenia użytkownika.

#### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie jednostki usługi (zalecane)
Aby używać uwierzytelniania jednostki usługi, należy zarejestrować jednostki aplikacji w usłudze Azure Active Directory (Azure AD). Następnie udzielić dostępu do usługi Azure AD Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [Service-to-service authentication](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Użyj uwierzytelniania jednostki usługi, określając następujące właściwości:

| Właściwość                | Opis                              | Wymagane |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Identyfikator klienta aplikacji.     | Tak      |
| servicePrincipalKey | Klucz aplikacji.           | Yes      |
| tenant              | Informacji o dzierżawie (identyfikator nazwy lub dzierżawy domeny) gdzie znajduje się Twoja aplikacja. Aby uzyskać te informacje, umieść kursor myszy w prawym górnym rogu witryny Azure portal. | Tak      |

**Przykład: Uwierzytelnianie jednostki usługi**
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

#### <a name="user-credential-authentication"></a>Uwierzytelnienia poświadczeń użytkownika
Do uwierzytelniania poświadczeń użytkownika usługi Data Lake Analytics należy określić następujące właściwości:

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| authorization | W edytorze fabryki danych wybierz **Autoryzuj** przycisku. Wprowadź poświadczenia, które przypisuje adres URL autoryzacji wygenerowany automatycznie do tej właściwości. | Yes      |
| sessionId     | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i mogą być użyte tylko raz. To ustawienie jest generowany automatycznie, korzystając z edytora fabryki danych. | Tak      |

**Przykład: Uwierzytelnienia poświadczeń użytkownika**
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
Kod autoryzacji, który został wygenerowany przez wybranie **Autoryzuj** przycisk wygasa po upływie interwału zestawu. 

Po wygaśnięciu ważności tokenu uwierzytelniania może zobaczysz następujący komunikat o błędzie: 

  Błąd operacji dotyczącej poświadczeń: invalid_grant - AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS70008: Udzielenie dostępu podany jest wygasnąć lub zostać odwołane. Identyfikator śledzenia: Identyfikator korelacji d18629e8-af88-43c5-88e3-d8419eb1fca1: sygnatura czasowa fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21:09:31Z

W poniższej tabeli przedstawiono wygaśnięcia przez typ konta dla użytkownika: 

| Typ użytkownika                                | Wygasa po                            |
| :--------------------------------------- | :--------------------------------------- |
| Konta użytkowników, które są *nie* zarządzane przez usługę Azure AD (Hotmail, na żywo i tak dalej) | 12 godzin.                                 |
| Konta użytkowników *są* zarządzane przez usługę Azure AD | Uruchom 14 dni od ostatniego wycinka. <br /><br />90 dni, jeśli wycinek, który jest oparty na połączonej usługi na podstawie uwierzytelniania OAuth jest uruchamiane co najmniej raz na 14 dni. |

Aby uniknąć lub rozwiązać ten problem, ponownie autoryzować zaznaczając **Autoryzuj** przycisk po wygaśnięciu ważności tokenu. Następnie należy ponownie wdrożyć połączoną usługę. Możesz również generować wartości **sessionId** i **autoryzacji** właściwości programowo, używając następującego kodu:

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

Aby uzyskać szczegółowe informacje o klasach fabryki danych, które są używane w tym przykładzie kodu zobacz:
* [AzureDataLakeStoreLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [Klasa AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Dodaj odwołanie do Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll dla **WindowsFormsWebAuthenticationDialog** klasy. 

## <a name="azure-sql-linked-service"></a>Połączona usługa Azure SQL
Można utworzyć połączonej usługi SQL i używać ich z [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) aby wywołać procedurę składowaną z potoku usługi fabryka danych. Aby uzyskać więcej informacji, zobacz [Łącznik usług Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Usługa Azure SQL Data Warehouse połączone
Można utworzyć usługi SQL Data Warehouse połączone i używać ich z [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) aby wywołać procedurę składowaną z potoku usługi fabryka danych. Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Połączonej usługi SQL Server
Można utworzyć połączonej usługi SQL Server i używać ich z [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) aby wywołać procedurę składowaną z potoku usługi fabryka danych. Aby uzyskać więcej informacji, zobacz [łącznik programu SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

