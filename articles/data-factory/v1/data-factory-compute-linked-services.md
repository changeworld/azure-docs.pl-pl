---
title: Obliczeniowe środowiskach obsługiwanych przez usługi fabryka danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat środowiska obliczeniowego, w których można użyć w potokach fabryki danych Azure (np. Azure HDInsight) do przechowywania danych transformacji lub proces.
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
ms.openlocfilehash: 51a0f43587b9d34a3693eb4a2927d10c71bd95d1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621755"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Obliczenia bazy danych środowiskach obsługiwanych przez usługi fabryka danych Azure
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [obliczeniowe połączonych usług w wersji 2](../compute-linked-services.md).

W tym artykule opisano środowiska obliczeniowego, w których można użyć do procesu lub przekształcenia danych. Zapewnia także szczegółowe informacje o różnych konfiguracjach (na żądanie i bring your own) czy obsługuje fabryki danych podczas konfigurowania połączone usługi, które łączą te obliczeniowe środowisk do fabryki danych Azure.

Poniższa tabela zawiera listę środowiskach obliczeniowych, które są obsługiwane przez fabryki danych i działań, które można uruchomić na nich. 

| Środowisko obliczeniowe                      | Działania                               |
| ---------------------------------------- | ---------------------------------------- |
| [Klaster Azure HDInsight na żądanie](#azure-hdinsight-on-demand-linked-service) lub [klastrem usługi HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Działania usługi Machine Learning: wykonywanie wsadowe i aktualizacja zasobów](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Język U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [magazyn danych Azure SQL](#azure-sql-data-warehouse-linked-service), [programu SQL Server](#sql-server-linked-service) | [Działania procedur składowanych](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Obsługiwane w fabryce danych wersji usługi HDInsight
Usługa Azure HDInsight obsługuje wielu wersjach klastra Hadoop, które można wdrożyć w dowolnym momencie. Wszystkie obsługiwane wersje tworzy określonej wersji programu dystrybucji Hortonworks Data Platform (HDP) i zestaw składników w dystrybucji. 

Microsoft aktualizuje listę obsługiwanych wersji usługi HDInsight najnowsze składniki ekosystemu Hadoop i poprawki. Aby uzyskać szczegółowe informacje, zobacz [HDInsight obsługiwane wersje](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> HDInsight opartych na systemie Linux wersji 3.3 została wycofana 31 lipca 2017 r. Fabryki danych w wersji 1 usługi HDInsight na żądanie połączone usługi, które otrzymały klientów do 15 grudnia 2017 do testowania i uaktualnienia do nowszej wersji usługi hdinsight. 31 lipca 2018 zostaną wycofane HDInsight opartych na systemie Windows.
>
> 

### <a name="after-the-retirement-date"></a>Po dacie wycofania 

Po 15 grudnia 2017 r.:

- Nie można utworzyć wersji 3.3 HDInsight opartych na systemie Linux (i starszych) klastrów za pomocą usługi HDInsight na żądanie połączonej usługi z fabryki danych w wersji 1. 
- Jeśli [ **osType** i **wersji** właściwości](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) nie są jawnie określone w definicji JSON dla istniejącej usługi HDInsight połączone na żądanie w wersji 1 usługi fabryka danych , wartością domyślną jest zmieniana z **wersji = 3.1, osType = Windows** do **wersji =\<najnowszej wersji domyślnej HDI\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions), osType = Linux**.

Po 31 lipca 2018:

- Nie można utworzyć dowolnej wersji systemu Windows w usłudze hdinsight, za pomocą usługi HDInsight połączony na żądanie w fabryce danych w wersji 1. 

### <a name="recommended-actions"></a>Zalecane akcje 

- Aby upewnić się, że można użyć najnowsze składniki ekosystemu Hadoop i poprawki, należy zaktualizować [ **osType** i **wersji** właściwości](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) z odpowiednim fabryki danych w wersji 1 na żądanie HDInsight połączone definicji usługi do nowszej wersji usługi HDInsight opartej na systemie Linux (3,6 HDInsight). 
- Przed 15 grudnia 2017 fabryki danych w wersji 1 Hive, Pig, MapReduce i Hadoop przesyłania strumieniowego działań testów odwołujące się do odpowiednich połączonej usługi. Upewnij się, że są one zgodne z nowym **osType** i **wersji** wartości domyślnych (**wersji = 3,6**, **osType = Linux**) lub jawne wersji usługi HDInsight i systemu operacyjnego wpisz w przypadku uaktualniania do. 
  Aby dowiedzieć się więcej na temat zgodności, zobacz [migracji z klastra usługi HDInsight opartej na systemie Windows do klastra z systemem Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) i [jakie są składniki platformy Hadoop i wersje dostępne w usłudze HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Aby kontynuować korzystanie z usługi HDInsight połączone fabryki danych w wersji 1 na żądanie do tworzenia klastrów usługi HDInsight opartej na systemie Windows, należy jawnie ustawić **osType** do **Windows** przed 15 grudnia 2017 r. Zaleca się przeprowadzenie migracji do klastrów usługi HDInsight opartych na systemie Linux przed 31 lipca 2018. 
- Jeśli używasz usługi HDInsight połączony na żądanie można wykonać fabryki danych w wersji 1 DotNet niestandardowe działania, aktualizację definicji DotNet niestandardowe działania w formacie JSON, aby zamiast tego użyć partii zadań Azure połączone usługi. Aby uzyskać więcej informacji, zobacz [skorzystać z działań niestandardowych w potoku fabryki danych](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Jeśli używasz istniejącą, bring your own klastra usługi HDInsight połączone urządzenia w fabryce danych w wersji 1 lub bring your own i na żądanie usługi HDInsight połączone w fabryce danych Azure w wersji 2, akcja nie jest wymagana. W tych scenariuszach najnowszych zasad pomocy technicznej wersji klastrów HDInsight już jest wymuszana. 
>
> 


## <a name="on-demand-compute-environment"></a>Środowiska obliczeniowe na żądanie
W konfiguracji na żądanie fabryki danych w pełni zarządza środowiska obliczeniowego. Fabryka danych automatycznie tworzy środowiska obliczeniowego przed przesłaniem do przetwarzania danych zadania. Po zakończeniu zadania fabryki danych usuwa środowiska obliczeniowego. 

Można utworzyć połączonej usługi dla środowiska obliczeń na żądanie. Użyj połączonej usługi, do konfigurowania środowiska obliczeniowe oraz do sterowania szczegółowe ustawienia wykonywania zadań zarządzania klastrem i uruchamianie akcji.

> [!NOTE]
> Obecnie konfiguracja na żądanie jest obsługiwana tylko w przypadku klastrów usługi HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight na żądanie połączonej usługi
Fabryka danych może automatycznie tworzyć klastra usługi HDInsight na żądanie Windows lub Linux do przetwarzania danych. Klaster jest tworzony w tym samym regionie co konto magazynu, który został skojarzony z klastrem. Użyj JSON **linkedServiceName** właściwości do utworzenia klastra.

Należy pamiętać, że *klucza* punktów o HDInsight na żądanie połączonej usługi:

* Klaster usługi HDInsight na żądanie nie jest wyświetlane w Twojej subskrypcji platformy Azure. Usługi fabryka danych zarządza klastrem usługi HDInsight na żądanie w Twoim imieniu.
* Dzienniki dla zadań, które są uruchamiane w klastrze usługi HDInsight na żądanie są kopiowane do konta magazynu, który został skojarzony z klastrem usługi HDInsight. Dostępu do tych dzienników, w portalu Azure, przejdź do **szczegóły uruchomienia działania** okienka. Aby uzyskać więcej informacji, zobacz [monitorowanie i zarządzanie nimi potoki](data-factory-monitor-manage-pipelines.md).
* Naliczane są opłaty tylko w przypadku czas, który klastra usługi HDInsight jest włączony i uruchomionych zadań.

> [!IMPORTANT]
> Zwykle trwa *20 minut* lub więcej, aby udostępnić klastra usługi HDInsight na żądanie.
>
> 

### <a name="example"></a>Przykład
Następujące JSON definiuje opartych na systemie Linux usługi HDInsight połączony na żądanie. Fabryka danych automatycznie tworzy *opartych na systemie Linux* klastra usługi HDInsight podczas przetwarzania wycinka danych. 

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
> Tworzy klaster usługi HDInsight *domyślny kontener* w magazynie obiektów Blob platformy Azure, w formacie JSON **linkedServiceName** właściwości. Zgodnie z projektem HDInsight nie powoduje usunięcia tego kontenera, po usunięciu klastra. W usłudze HDInsight połączony na żądanie klastra usługi HDInsight jest tworzony za każdym razem, gdy wycinek ma zostać przetworzony, chyba że istnieje istniejącego klastra na żywo (**timeToLive**). Klaster jest usuwany po zakończeniu przetwarzania. 
>
> Jak są przetwarzane więcej wycinków, zobaczysz wiele kontenerów w magazynie obiektów Blob. Jeśli nie potrzebujesz kontenerów do rozwiązywania problemów z zadaniami, można usunąć kontener, aby zmniejszyć koszt magazynowania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adf<your Data Factory name>-<linked service name>-<date and time>`. Można użyć narzędzia, takiego jak [Eksploratora magazynu](http://storageexplorer.com/) usunąć kontenerów w magazynie obiektów Blob.
>
> 

### <a name="properties"></a>Właściwości
| Właściwość                     | Opis                              | Wymagane |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Ustaw dla właściwości typu **HDInsightOnDemand**. | Yes      |
| clusterSize                  | Liczba węzłów procesu roboczego i danych w klastrze. Klaster usługi HDInsight jest tworzony z głównymi węzłami 2 oprócz liczba węzłów procesu roboczego, które określają dla tej właściwości. Węzły mają rozmiar Standard_D3, który ma 4 rdzenie. Klaster z węzłami procesu roboczego 4 przyjmuje 24 rdzenie (4\*4 = 16 rdzenie dla węzłów procesu roboczego, a także 2\*rdzenie 4 = 8 dla węzłów głównych). Aby uzyskać więcej informacji o warstwie Standard_D3, zobacz [utworzyć Linux opartych klastrów Hadoop w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Yes      |
| timeToLive                   | Limit czasu bezczynności klastra usługi HDInsight na żądanie. Określa, jak długo klastra usługi HDInsight na żądanie pozostaje aktywne po zakończeniu wykonywania działania, jeśli w klastrze nie ma żadnych aktywnych działań.<br /><br />Na przykład, jeśli działanie Uruchom ma 6 minut i **timeToLive** jest ustawiony na 5 minut, pozostaje klastra aktywności 5 minut po 6 minut przetwarzania uruchamiania działania. Jeśli inny uruchamiania działania jest wykonywane w oknie 6-minutowych, są przetwarzane przez tego samego klastra.<br /><br />Tworzenie klastra usługi HDInsight na żądanie jest kosztowna operacja (może potrwać kilka minut). Użyj tego ustawienia, zgodnie z potrzebami, aby zwiększyć wydajność fabryki danych przez ponowne użycie klastra usługi HDInsight na żądanie.<br /><br />Jeśli ustawisz **timeToLive** do wartości **0**, natychmiast po zakończeniu uruchom działania jest usunąć klastra. Jednak jeśli ustawisz wysokiej wartości klastra mogą pozostać bezczynny, co niepotrzebnie wysokich kosztów. Jest ważne, aby ustawić odpowiednią wartość, na podstawie Twoich potrzeb.<br /><br />Jeśli **timeToLive** odpowiednio jest ustawiona, wiele potoki mogą udostępniać wystąpienia klastra usługi HDInsight na żądanie. | Yes      |
| wersja                      | Wersja klastra usługi HDInsight. Dla dozwolonych wersji usługi HDInsight, zobacz [HDInsight obsługiwane wersje](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Jeśli ta wartość nie jest określona, [najnowszej wersji domyślnej HDI](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions) jest używany. | Nie       |
| linkedServiceName            | Połączoną usługą magazynu Azure do użycia przez klaster na żądanie do przechowywania i przetwarzania danych. W tym samym regionie co konto magazynu jest tworzenie klastra usługi HDInsight.<p>Obecnie nie można utworzyć klastra usługi HDInsight na żądanie, która używa usługi Azure Data Lake Store jako magazynu. Jeśli chcesz przechowywać dane wynikowe z HDInsight przetwarzania w usłudze Data Lake Store, umożliwia działanie kopiowania skopiować dane z magazynu obiektów Blob do usługi Data Lake Store. </p> | Yes      |
| additionalLinkedServiceNames | Określa dodatkowe konta magazynu dla usługi HDInsight połączone. Fabryka danych rejestruje kont magazynu w Twoim imieniu. Te konta magazynu musi być w tym samym regionie co klaster usługi HDInsight. Klaster usługi HDInsight jest tworzony w tym samym regionie co konto magazynu, który jest określony przez **linkedServiceName** właściwości. | Nie       |
| osType                       | Typ systemu operacyjnego. Dozwolone wartości to **Linux** i **Windows**. Jeśli ta wartość nie jest określona, **Linux** jest używany.  <br /><br />Zdecydowanie zaleca się używania klastrów usługi HDInsight opartej na systemie Linux. Data wycofania dla usługi HDInsight w systemie Windows jest 31 lipca 2018. | Nie       |
| hcatalogLinkedServiceName    | Nazwa usługi SQL Azure połączone, która wskazuje HCatalog bazy danych. Klaster usługi HDInsight na żądanie jest tworzona przy użyciu bazy danych SQL jako potrzeby magazynu metadanych. | Nie       |

#### <a name="example-linkedservicenames-json"></a>Przykład: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Zaawansowane właściwości
Do szczegółowej konfiguracji klastra usługi HDInsight na żądanie można określić następujące właściwości:

| Właściwość               | Opis                              | Wymagane |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Określa parametry konfiguracji podstawowej (core-site.xml) do klastra usługi HDInsight, który ma zostać utworzony. | Nie       |
| hBaseConfiguration     | Określa parametry konfiguracji bazy danych HBase (hbase-site.xml) dla klastra usługi HDInsight. | Nie       |
| hdfsConfiguration      | Określa parametry konfiguracji systemu plików HDFS (systemu plików hdfs-site.xml) dla klastra usługi HDInsight. | Nie       |
| hiveConfiguration      | Określa parametry konfiguracji gałąź (gałąź site.xml) dla klastra usługi HDInsight. | Nie       |
| mapReduceConfiguration | Określa parametry konfiguracji MapReduce (mapred-site.xml) dla klastra usługi HDInsight. | Nie       |
| oozieConfiguration     | Określa parametry konfiguracji Oozie (oozie-site.xml) dla klastra usługi HDInsight. | Nie       |
| stormConfiguration     | Określa parametry konfiguracji Storm (storm-site.xml) dla klastra usługi HDInsight. | Nie       |
| yarnConfiguration      | Określa parametry konfiguracji YARN (yarn-site.xml) dla klastra usługi HDInsight. | Nie       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Przykład: Konfiguracja klastra usługi HDInsight na żądanie z właściwości zaawansowane

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

### <a name="node-sizes"></a>Rozmiary węzła
Aby określić rozmiar, head, danych i węzły dozorcy, należy użyć następujących właściwości: 

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Umożliwia ustawienie rozmiaru węzła głównego. Wartość domyślna to **Standard_D3**. Aby uzyskać więcej informacji, zobacz [Określ rozmiary węzła](#specify-node-sizes). | Nie       |
| dataNodeSize      | Określa rozmiar węzeł danych. Wartość domyślna to **Standard_D3**. | Nie       |
| zookeeperNodeSize | Umożliwia ustawienie rozmiaru węzła dozorcy. Wartość domyślna to **Standard_D3**. | Nie       |

#### <a name="specify-node-sizes"></a>Określ rozmiary węzła
Wartości ciągów, które należy określić dla właściwości opisane w poprzedniej sekcji, zobacz [rozmiarów maszyn wirtualnych](../../virtual-machines/linux/sizes.md). Wartości muszą być zgodne z poleceń cmdlet i interfejsów API przywoływany w [rozmiarów maszyn wirtualnych](../../virtual-machines/linux/sizes.md). Rozmiaru węzła danych duży (ustawienie domyślne) zawiera 7 GB pamięci. To może nie być wystarczający do danego scenariusza. 

Jeśli chcesz utworzyć D4 rozmiar węzłów głównych i węzłów procesu roboczego, określ **Standard_D4** jako wartość **headNodeSize** i **dataNodeSize** właściwości: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Jeśli ustawisz nieprawidłową wartość dla tych właściwości, może zostać wyświetlony następujący komunikat:

  Nie można utworzyć klastra. Wyjątek: nie można ukończyć operacji tworzenia klastra. Operacja zakończona niepowodzeniem z kodem „400”. Końcowy stan klastra: „Błąd”. Komunikat o błędzie: "PreClusterCreationValidationFailure". 
  
Jeśli ten komunikat zostanie wyświetlony, upewnij się, że używane są polecenia cmdlet i nazwy interfejsu API z tabelą w [rozmiarów maszyn wirtualnych](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Obecnie usługa fabryki danych nie obsługuje klastrów usługi HDInsight, które używają usługi Data Lake Store jako podstawowy magazynu. Użyj magazynu Azure jako podstawowy magazynu dla klastrów usługi HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Środowiska obliczeniowe bring your own
Możesz zarejestrować istniejącego środowiska obliczeniowego jako połączonej usługi z fabryki danych. Możesz zarządzać środowiska obliczeniowego. Usługi fabryka danych wykorzystuje środowisko obliczeń do wykonania działań.

Ten typ konfiguracji jest obsługiwane w następujących środowiskach obliczeniowych:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL bazy danych, Magazyn danych Azure SQL, programu SQL Server

## <a name="azure-hdinsight-linked-service"></a>Usługa Azure HDInsight połączone
Można utworzyć usługi HDInsight połączony do zarejestrowania klastrem usługi HDInsight przy użyciu fabryki danych.

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
| type              | Ustaw dla właściwości typu **HDInsight**. | Yes      |
| clusterUri        | Identyfikator URI klastra usługi HDInsight.        | Yes      |
| nazwa użytkownika          | Nazwa konta użytkownika na potrzeby połączenia z istniejącym klastrem usługi HDInsight. | Yes      |
| hasło          | Hasło dla konta użytkownika.   | Yes      |
| linkedServiceName | Nazwa połączonej usługi magazynu odnoszący się do magazynu obiektów Blob używane przez klaster usługi HDInsight. <p>Obecnie nie można określić, że usługi Data Lake Store połączonej usługi dla tej właściwości. Jeśli klaster usługi HDInsight ma dostęp do usługi Data Lake Store, mogą uzyskiwać dostęp do danych w usłudze Data Lake Store, Hive i Pig skryptów. </p> | Yes      |

## <a name="azure-batch-linked-service"></a>Usługa Azure partii połączone
Można utworzyć usługi partii połączone zarejestrować puli partii maszynach wirtualnych (VM) do fabryki danych. Za pomocą partii lub HDInsight, możesz uruchomić niestandardowych działań programu Microsoft .NET.

Jeśli jesteś nowym użytkownikiem usługi partii:

* Dowiedz się więcej o [podstawowe informacje o partii zadań Azure](../../batch/batch-technical-overview.md).
* Dowiedz się więcej o [AzureBatchAccount nowy](https://msdn.microsoft.com/library/mt125880.aspx) polecenia cmdlet. Użyj tego polecenia cmdlet, aby utworzyć konto usługi partia zadań. Można utworzyć konta usługi partia zadań za pomocą [portalu Azure](../../batch/batch-account-create-portal.md). Aby uzyskać szczegółowe informacje na temat używania polecenia cmdlet, zobacz [przy użyciu programu PowerShell, aby zarządzać kontem usługi partia zadań](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Dowiedz się więcej o [AzureBatchPool nowy](https://msdn.microsoft.com/library/mt125936.aspx) polecenia cmdlet. To polecenie cmdlet umożliwia utworzenie puli partii.

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

Aby uzyskać **accountName** właściwości, dołącz **.\< Nazwa regionu\>**  do nazwy konta partii zadań. Na przykład:

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
| type              | Ustaw dla właściwości typu **AzureBatch**. | Yes      |
| accountName       | Nazwa konta usługi partia zadań.         | Yes      |
| accessKey         | Klucz dostępu dla konta usługi partia zadań.  | Yes      |
| poolName          | Nazwa puli maszyn wirtualnych.    | Yes      |
| linkedServiceName | Nazwa połączonej usługi magazynu skojarzoną z tej partii połączonej usługi. Tej połączonej usługi jest używany dla tymczasowych plików, które są wymagane, aby uruchomić działanie, a do przechowywania dzienniki wykonywania działania. | Yes      |

## <a name="azure-machine-learning-linked-service"></a>Usługa Azure Machine Learning połączone
Można utworzyć usługi Machine Learning połączone zarejestrować partii uczenia maszynowego punktu końcowego oceniania z fabryką danych.

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
| Typ       | Ustaw dla właściwości typu **uczenie maszynowe Azure**. | Yes      |
| mlEndpoint | Adres URL wsadowego oceniania.                   | Yes      |
| apiKey     | Interfejs API modelu opublikowanych obszaru roboczego.     | Yes      |

## <a name="azure-data-lake-analytics-linked-service"></a>Usługa Azure Data Lake Analytics połączone usługi
Można utworzyć usługi Data Lake Analytics połączony do łączenia usługi Data Lake Analytics obliczeń do fabryki danych Azure. Data Lake Analytics U-SQL działania w potoku odwołuje się do tej połączonej usługi. 

W poniższej tabeli opisano właściwości ogólnych, które są używane w definicji JSON:

| Właściwość                 | Opis                              | Wymagane                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Ustaw dla właściwości typu **AzureDataLakeAnalytics**. | Yes                                      |
| accountName          | Nazwa konta usługi Data Lake Analytics.  | Yes                                      |
| dataLakeAnalyticsUri | Identyfikator URI Data Lake Analytics.           | Nie                                       |
| subscriptionId       | Identyfikator subskrypcji platformy Azure.                    | Nie<br /><br />(Jeśli nie zostanie określony, subskrypcja fabryki danych jest używany). |
| resourceGroupName    | Nazwa grupy zasobów platformy Azure.                | Nie<br /><br /> (Jeśli nie zostanie określony, grupy zasobów fabryki danych jest używany). |

### <a name="authentication-options"></a>Opcje uwierzytelniania
Dla usługi Data Lake Analytics połączone można wybrać uwierzytelnianie przy użyciu nazwy głównej usługi lub poświadczenia użytkownika.

#### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie główna usługi (zalecane)
Aby używać uwierzytelniania głównej usługi, należy zarejestrować jednostki aplikacji w usłudze Azure Active Directory (Azure AD). Następnie przyznać dostęp do usługi Azure AD do usługi Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [do usługi uwierzytelniania](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zwróć uwagę na następujące wartości, które służą do definiowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Uwierzytelnianie usługi głównej przez określenie następujących właściwości:

| Właściwość                | Opis                              | Wymagane |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Identyfikator klienta aplikacji.     | Yes      |
| servicePrincipalKey | Klucz aplikacji.           | Yes      |
| dzierżawa              | Dzierżawy informacji (identyfikator nazwy lub dzierżawy domeny) którym znajduje się aplikacja. Aby uzyskać informacje o tym, umieść kursor myszy w prawym górnym rogu portalu Azure. | Yes      |

**Przykład: Usługa podmiotu zabezpieczeń uwierzytelniania**
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
Do uwierzytelniania poświadczeń użytkownika dla usługi Data Lake Analytics określ następujące właściwości:

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| Autoryzacji | Wybierz w Edytor fabryki danych **autoryzacji** przycisku. Wprowadź poświadczenia, który przypisuje do tej właściwości adresu URL autoryzacji wygenerowana automatycznie. | Yes      |
| Identyfikator sesji     | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i mogą być użyte tylko raz. To ustawienie jest generowane automatycznie, gdy używasz Edytor fabryki danych. | Yes      |

**Przykład: Użytkownik poświadczeń uwierzytelniania**
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

#### <a name="token-expiration"></a>Wygaśnięcia tokenu
Kod autoryzacji, generowany przez wybranie **autoryzacji** przycisk wygasa po upływie czasu zestawu. 

Po wygaśnięciu tokenu uwierzytelniania może pojawić następujący komunikat o błędzie: 

  Poświadczeń błąd operacji: invalid_grant - AADSTS70002: błąd podczas sprawdzania poprawności poświadczeń. AADSTS70008: Udzielone prawa dostępu jest wygasnąć lub zostać odwołane. Identyfikator śledzenia: Identyfikator korelacji d18629e8-af88-43c5-88e3-d8419eb1fca1: sygnatura czasowa fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21:09:31Z

W poniższej tabeli przedstawiono wygasanie według typu konta użytkownika: 

| Typ użytkownika                                | Wygasa po                            |
| :--------------------------------------- | :--------------------------------------- |
| Konta użytkowników, które są *nie* zarządzane przez usługę Azure AD (Hotmail, Live itd.) | 12 godzin.                                 |
| Konta użytkowników *są* zarządzane przez usługę Azure AD | Uruchom 14 dni od ostatniego wycinka. <br /><br />90 dni, jeśli wycinek, który jest oparty na usługa OAuth w połączonych działa co najmniej raz na 14 dni. |

Aby uniknąć lub rozwiązać ten problem, ponownie autoryzować wybierając **autoryzacji** przycisku po wygaśnięciu tokenu. Następnie należy ponownie wdrożyć połączonej usługi. Można również tworzyć wartości **sessionId** i **autoryzacji** właściwości programowo przy użyciu następującego kodu:

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

## <a name="azure-sql-linked-service"></a>Azure połączoną usługą SQL
Można utworzyć połączoną usługą SQL i użyć go z [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) aby wywołać procedurę składowaną z potoku fabryki danych. Aby uzyskać więcej informacji, zobacz [Łącznik usług Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Usługa Azure SQL Data Warehouse połączone
Można utworzyć usługi SQL Data Warehouse połączone i użyć go z [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) aby wywołać procedurę składowaną z potoku fabryki danych. Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Usługi SQL Server połączone
Można utworzyć usługi SQL Server połączone i użyć go z [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) aby wywołać procedurę składowaną z potoku fabryki danych. Aby uzyskać więcej informacji, zobacz [łącznika programu SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

