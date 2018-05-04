---
title: Obliczeniowe środowiskach obsługiwanych przez usługi fabryka danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat środowiska obliczeniowego, w których można użyć w potokach fabryki danych Azure (np. Azure HDInsight) do przechowywania danych transformacji lub proces.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
ms.openlocfilehash: 6f9f0f9a9bab7e6865ae5a48552ac702ae2bf6fb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Obliczenia bazy danych środowiskach obsługiwanych przez usługi fabryka danych Azure
W tym artykule opisano różne środowiska obliczeniowe, w których można użyć do procesu lub przekształcenia danych. Podano również szczegółowe informacje o różnych konfiguracjach (na żądanie i użycie własnego) obsługiwane przez fabrykę danych podczas konfigurowania usług połączonych łączenia tych obliczeniowe środowisk do fabryki danych Azure.

Poniższa tabela zawiera listę środowiska obliczeniowe obsługiwane przez fabryki danych i działań, które można uruchomić na nich. 

| Środowisko obliczeniowe                      | activities                               |
| ---------------------------------------- | ---------------------------------------- |
| [Klaster usługi HDInsight na żądanie](#azure-hdinsight-on-demand-linked-service) lub [klastrem usługi HDInsight](#azure-hdinsight-linked-service) | [Gałąź rejestru](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service) | [Custom](transform-data-using-dotnet-custom-activity.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Działania usługi Machine Learning: wykonywanie wsadowe i aktualizacja zasobów](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Język U-SQL usługi Data Lake Analytics](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [magazyn danych Azure SQL](#azure-sql-data-warehouse-linked-service), [programu SQL Server](#sql-server-linked-service) | [Procedura składowana](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>Środowiska obliczeniowe na żądanie
W tym typie konfiguracji środowiska komputerowego pełni zarządza usługi fabryka danych Azure. Została ona utworzona automatycznie przez usługi fabryka danych przed zadania jest przesłane do przetwarzania danych i usuwane po zakończeniu zadania. Można utworzyć połączonej usługi dla środowiska obliczeń na żądanie, jest skonfigurowana i sterowanie ustawieniami szczegółowego do wykonywania zadań zarządzania klastrem i uruchamianie akcji.

> [!NOTE]
> Konfiguracja na żądanie jest obecnie obsługiwane tylko w przypadku klastrów usługi HDInsight Azure.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight na żądanie połączonej usługi
Usługi fabryka danych Azure, można automatycznie utworzyć klaster usługi HDInsight na żądanie do przetwarzania danych. Klaster jest tworzony w tym samym regionie co skojarzone z klastrem konta magazynu (właściwość linkedServiceName w formacie JSON). Konta magazynu musi być kontem magazynu ogólnego przeznaczenia Azure standard. 

Należy pamiętać, że **ważne** punktów o HDInsight na żądanie połączonej usługi:

* W ramach Twojej subskrypcji platformy Azure jest tworzenie klastra usługi HDInsight na żądanie. Jest widoczna klastra w portalu Azure, gdy klaster jest uruchomiony i działa. 
* Dzienniki dla zadań, które są uruchamiane w klastrze usługi HDInsight na żądanie są kopiowane do konta magazynu skojarzone z klastrem usługi HDInsight. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword określone w definicji połączonej usługi są używane do logowania do klastra na potrzeby dogłębne Rozwiązywanie problemów podczas cyklu życia klastra. 
* Naliczane są opłaty tylko do czasu, gdy klaster usługi HDInsight jest uruchomiony i uruchomionych zadań.

> [!IMPORTANT]
> Zwykle trwa **20 minut** lub więcej, aby udostępnić klaster Azure HDInsight na żądanie.
>
> 

### <a name="example"></a>Przykład
Następujące JSON definiuje opartych na systemie Linux usługi HDInsight połączony na żądanie. Usługi fabryka danych automatycznie tworzy **opartych na systemie Linux** klastra usługi HDInsight można przetworzyć wymagane działania. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> Klaster usługi HDInsight tworzy **kontener domyślny** w magazynie obiektów blob określonym w kodzie JSON (**linkedServiceName**). Usługa HDInsight nie powoduje usunięcia tego kontenera w przypadku usunięcia klastra. To zachowanie jest celowe. W przypadku połączonej usługi HDInsight na żądanie klaster usługi HDInsight jest tworzony za każdym razem, gdy trzeba przetworzyć wycinek — o ile w tym momencie nie istnieje aktywny klaster (**timeToLive**) — i zostaje usunięty po zakończeniu przetwarzania. 
>
> Jak więcej działanie zostanie uruchomione, pojawi się wiele kontenerów w magazynie obiektów blob platformy Azure. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Aby usunąć kontenery z usługi Azure Blob Storage, użyj takich narzędzi, jak [Microsoft Storage Explorer](http://storageexplorer.com/).
>
> 

### <a name="properties"></a>Właściwości
| Właściwość                     | Opis                              | Wymagane |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Powinien mieć ustawioną właściwość type **HDInsightOnDemand**. | Yes      |
| clusterSize                  | Liczba węzłów procesu roboczego/danych w klastrze. Klaster usługi HDInsight jest tworzony z głównymi węzłami 2 wraz z liczbą węzłów procesu roboczego, które określisz dla tej właściwości. Węzły mają rozmiar Standard_D3, który ma 4 rdzenie, więc klastra z węzłem procesu roboczego 4 przyjmuje 24 rdzenie (4\*4 = 16 rdzenie dla węzłów procesu roboczego, a także 2\*rdzenie 4 = 8 dla węzłów głównych). Zobacz [Ustawianie klastrów w usłudze HDInsight Hadoop, Spark, Kafka i](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) szczegółowe informacje. | Yes      |
| linkedServiceName            | Azure połączonej usługi magazynu do użycia przez klaster na żądanie do przechowywania i przetwarzania danych. W tym samym regionie co konto usługi Azure Storage jest tworzenie klastra usługi HDInsight. Usługa Azure HDInsight ma ograniczenia całkowitej liczby rdzeni, których możesz użyć w każdym obsługiwanym przez nią regionie platformy Azure. Upewnij się, że masz wystarczająco dużo przydziały core w tym regionie Azure, aby spełnić wymagana wartość clusterSize. Aby uzyskać więcej informacji, zapoznaj się [Ustawianie klastrów w usłudze HDInsight Hadoop, Spark, Kafka i](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Obecnie nie można utworzyć klastra usługi HDInsight na żądanie, która używa usługi Azure Data Lake Store jako magazynu. Jeśli chcesz przechowywać dane wynikowe z HDInsight przetwarzania w usłudze Azure Data Lake Store, umożliwia działanie kopiowania skopiować dane z magazynu obiektów Blob Azure do usługi Azure Data Lake Store. </p> | Yes      |
| clusterResourceGroup         | Klaster usługi HDInsight jest tworzony w tej grupie zasobów. | Yes      |
| wartość TimeToLive                   | Limit czasu bezczynności klastra usługi HDInsight na żądanie. Określa, jak długo klastra usługi HDInsight na żądanie pozostaje aktywne po zakończeniu działania uruchamiania, jeśli w klastrze nie ma żadnych aktywnych działań. Minimalne dozwolone wartości to 5 minut (00: 05:00).<br/><br/>Na przykład jeśli uruchomienia działania trwa 6 minut i timetolive jest ustawiony na 5 minut, klaster pozostanie aktywności 5 minut po uruchomieniu 6 minut przetwarzania działania. Jeśli inny uruchamiania działania jest wykonywane z okna 6 minut, jednak jest przetwarzany przez tego samego klastra.<br/><br/>Tworzenie klastra usługi HDInsight na żądanie jest kosztowna operacja (może to potrwać pewien czas), użyj tak, to ustawienie jako potrzebne do zwiększenia wydajności fabryki danych przez ponowne użycie klastra usługi HDInsight na żądanie.<br/><br/>Jeśli wartość timetolive jest ustawiona na 0, klastra jest usuwany natychmiast po zakończeniu wykonywania działania. Natomiast jeśli ustawisz wysokiej wartości, klaster może pozostać bezczynny logowania na rozwiązywanie niektórych problemów z celem, ale może spowodować wysokich kosztów. Dlatego jest ważne, aby ustawić odpowiednią wartość, na podstawie Twoich potrzeb.<br/><br/>Jeśli skonfigurowana wartość timetolive właściwości wielu potoki można udostępniać wystąpienia klastra usługi HDInsight na żądanie. | Yes      |
| clusterType                  | Typ klastra usługi HDInsight, który ma zostać utworzony. Dozwolone wartości to "hadoop" i "spark". Jeśli nie zostanie określony, wartością domyślną jest hadoop. | Nie       |
| wersja                      | Wersja klastra usługi HDInsight. Jeśli nie zostanie określony, jest przy użyciu bieżącej wersji usługi HDInsight w zdefiniowanej wartości domyślnej. | Nie       |
| hostSubscriptionId           | Identyfikator subskrypcji platformy Azure, używany do tworzenia klastra usługi HDInsight. Jeśli nie zostanie określony, używany identyfikator subskrypcji kontekst logowania do systemu Azure. | Nie       |
| clusterNamePrefix           | Prefiks nazwy klastra HDI, sygnatura czasowa zostaną automatycznie dodane na końcu nazwy klastra| Nie       |
| sparkVersion                 | Wersja platformy Spark, jeśli typ klastra jest "Spark" | Nie       |
| additionalLinkedServiceNames | Określa, że dodatkowe konta magazynu dla usługi HDInsight połączonej usługi, dzięki czemu usługi fabryka danych można zarejestrować je w Twoim imieniu. Te konta magazynu musi być w tym samym regionie co klaster usługi HDInsight, który jest tworzony w tym samym regionie co konto magazynu, określony przez linkedServiceName. | Nie       |
| osType                       | Typ systemu operacyjnego. Dozwolone wartości to: Linux i Windows (w przypadku tylko 3.3 HDInsight). Domyślnie jest systemu Linux. | Nie       |
| hcatalogLinkedServiceName    | Nazwa programu Azure SQL połączonej usługi, które HCatalog bazy danych. Klaster usługi HDInsight na żądanie jest tworzona przy użyciu bazy danych Azure SQL jako potrzeby magazynu metadanych. | Nie       |
| connectVia                   | Środowisko uruchomieniowe integracji ma być używany do wysyłania działania tej usługi HDInsight połączone. Dla usługi HDInsight połączony na żądanie obsługuje on tylko środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Nie       |
| clusterUserName                   | Nazwa użytkownika, dostęp do klastra. | Nie       |
| clusterPassword                   | Hasło w typie ciąg bezpieczny dostęp do klastra. | Nie       |
| clusterSshUserName         | Nazwa użytkownika do SSH zdalne połączenia do węzła klastra (dla systemu Linux). | Nie       |
| clusterSshPassword         | Hasło w typie bezpieczny ciąg do SSH połączenie zdalne węzła klastra (dla systemu Linux). | Nie       |


> [!IMPORTANT]
> HDInsight obsługuje wielu wersjach klastra Hadoop, które można wdrożyć. Każdy wybór wersji tworzy określonej wersji programu dystrybucji Hortonworks Data Platform (HDP) i zestaw składników, które są zawarte w tej dystrybucji. Lista obsługiwanych wersji usługi HDInsight przechowuje zaktualizowano w celu najnowsze składniki ekosystemu Hadoop i poprawki. Upewnij się, że zawsze odwołuje się do najnowsze informacje [HDInsight obsługiwanych wersji i typ systemu operacyjnego](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) zapewnienie używasz obsługiwanej wersji usługi hdinsight. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>przykład JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Usługi HDInsight na żądanie połączone wymaga uwierzytelniania głównej usługi, do tworzenia klastrów usługi HDInsight w Twoim imieniu. Uwierzytelnianie głównej usługi, Zarejestruj podmiot aplikacji w usłudze Azure Active Directory (Azure AD) i przyznać jej **współautora** roli subskrypcji lub grupy zasobów, w której jest tworzony klaster usługi HDInsight. Aby uzyskać szczegółowe instrukcje, zobacz [użycia portalu do tworzenia aplikacji i usług podmiot zabezpieczeń, który ma dostęp do zasobów usługi Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Zwróć uwagę na następujące wartości, które służą do definiowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji 
- Identyfikator dzierżawy

Uwierzytelnianie usługi głównej przez określenie następujących właściwości:

| Właściwość                | Opis                              | Wymagane |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Określ identyfikator aplikacji klienta.     | Yes      |
| **servicePrincipalKey** | Określ klucz aplikacji.           | Yes      |
| **Dzierżawy**              | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można go pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. | Yes      |

### <a name="advanced-properties"></a>Zaawansowane właściwości

Można również określić następujące właściwości do szczegółowej konfiguracji klastra usługi HDInsight na żądanie.

| Właściwość               | Opis                              | Wymagane |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Określa parametry konfiguracji podstawowej (jak core-site.xml) do klastra usługi HDInsight, który ma zostać utworzony. | Nie       |
| hBaseConfiguration     | Określa parametry konfiguracji bazy danych HBase (hbase-site.xml) dla klastra usługi HDInsight. | Nie       |
| hdfsConfiguration      | Określa parametry konfiguracji systemu plików HDFS (systemu plików hdfs-site.xml) dla klastra usługi HDInsight. | Nie       |
| hiveConfiguration      | Określa parametry konfiguracji gałąź (gałąź site.xml) dla klastra usługi HDInsight. | Nie       |
| mapReduceConfiguration | Określa parametry konfiguracji MapReduce (mapred-site.xml) dla klastra usługi HDInsight. | Nie       |
| oozieConfiguration     | Określa parametry konfiguracji Oozie (oozie-site.xml) dla klastra usługi HDInsight. | Nie       |
| stormConfiguration     | Określa parametry konfiguracji Storm (storm-site.xml) dla klastra usługi HDInsight. | Nie       |
| yarnConfiguration      | Określa parametry konfiguracji Yarn (yarn-site.xml) dla klastra usługi HDInsight. | Nie       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Przykład — konfiguracji klastra usługi HDInsight na żądanie z właściwości zaawansowane

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
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
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>Rozmiary węzła
Można określić head, danych i węzły dozorcy z następującymi właściwościami: 

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Określa rozmiar węzła głównego. Wartość domyślna to: Standard_D3. Zobacz **określania rozmiarów węzła** sekcji, aby uzyskać szczegółowe informacje. | Nie       |
| dataNodeSize      | Określa rozmiar węzeł danych. Wartość domyślna to: Standard_D3. | Nie       |
| zookeeperNodeSize | Określa rozmiar dozorca Zoo węzła. Wartość domyślna to: Standard_D3. | Nie       |

#### <a name="specifying-node-sizes"></a>Określanie rozmiary węzła
Zobacz [rozmiary maszyn wirtualnych](../virtual-machines/linux/sizes.md) artykułu wartości ciągu, należy określić dla właściwości, o których wspomniano w poprzedniej sekcji. Wartości muszą być zgodna z **poleceń cmdlet i interfejsów API** przywoływany w artykule. Jak widać w artykule, węzeł danych o rozmiarze duży (ustawienie domyślne) zawiera 7 GB pamięci, która nie może być dobrym dla danego scenariusza. 

Jeśli chcesz utworzyć D4 o rozmiarze węzłów głównych i węzłów procesu roboczego, określ **Standard_D4** jako wartość właściwości headNodeSize i dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Jeśli określono nieprawidłową wartość dla tych właściwości, może pojawić się następujące **błąd:** nie można utworzyć klastra. Wyjątek: nie można ukończyć operacji tworzenia klastra. Operacja zakończona niepowodzeniem z kodem „400”. Końcowy stan klastra: „Błąd”. Komunikat o błędzie: "PreClusterCreationValidationFailure". Gdy zostanie wyświetlony ten błąd, upewnij się, że używasz **polecenia CMDLET i interfejsów API** nazwa z tabeli w [rozmiary maszyn wirtualnych](../virtual-machines/linux/sizes.md) artykułu.        

## <a name="bring-your-own-compute-environment"></a>Przełącz środowiska obliczeniowe
W tym typie konfiguracji użytkownicy mogą zarejestrować już istniejącego środowiska komputerowego jako połączonej usługi z fabryki danych. Środowiska komputerowego jest zarządzana przez użytkownika i używa jej do wykonania działania usługi fabryka danych.

Ten typ konfiguracji jest obsługiwane w następujących środowiskach obliczeniowych:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Bazy danych Azure SQL, magazynu danych Azure SQL, programu SQL Server

## <a name="azure-hdinsight-linked-service"></a>Usługa Azure HDInsight połączone
Można utworzyć usługi Azure HDInsight połączony do zarejestrowania klastrem usługi HDInsight przy użyciu fabryki danych.

### <a name="example"></a>Przykład

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Właściwości
| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| type              | Powinien mieć ustawioną właściwość type **HDInsight**. | Yes      |
| clusterUri        | Identyfikator URI klastra usługi HDInsight.        | Yes      |
| nazwa użytkownika          | Określ nazwę użytkownika, który ma być używany do nawiązania połączenia z istniejącym klastrze usługi HDInsight. | Yes      |
| hasło          | Określ hasło dla konta użytkownika.   | Yes      |
| linkedServiceName | Nazwa usługi Azure Storage połączone usługi, która odwołuje się do magazynu obiektów blob platformy Azure, używane przez klaster usługi HDInsight. <p>Obecnie nie można określić, czy usługa Azure Data Lake Store połączonej usługi dla tej właściwości. Jeśli klaster usługi HDInsight ma dostęp do usługi Data Lake Store, może uzyskać dostępu do danych w usłudze Azure Data Lake Store, ze skryptów usługi Hive/Pig. </p> | Yes      |
| connectVia        | Środowisko uruchomieniowe integracji ma być używany do wysyłania działania do tej połączonej usługi. Możesz użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Nie       |

> [!IMPORTANT]
> HDInsight obsługuje wielu wersjach klastra Hadoop, które można wdrożyć. Każdy wybór wersji tworzy określonej wersji programu dystrybucji Hortonworks Data Platform (HDP) i zestaw składników, które są zawarte w tej dystrybucji. Lista obsługiwanych wersji usługi HDInsight przechowuje zaktualizowano w celu najnowsze składniki ekosystemu Hadoop i poprawki. Upewnij się, że zawsze odwołuje się do najnowsze informacje [HDInsight obsługiwanych wersji i typ systemu operacyjnego](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) zapewnienie używasz obsługiwanej wersji usługi hdinsight. 
>

## <a name="azure-batch-linked-service"></a>Usługa Azure partii połączone

Można utworzyć usługi partia zadań Azure połączony do zarejestrowania puli partii maszynach wirtualnych (VM) do fabryki danych. Możesz uruchomić niestandardowe działanie przy użyciu partii zadań Azure.

Zobacz następujące tematy, jeśli jesteś nowym użytkownikiem usługi partia zadań Azure:

* [Podstawy usługi partia zadań Azure](../batch/batch-technical-overview.md) Omówienie usługi partia zadań Azure.
* [Nowy AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) polecenia cmdlet, aby utworzyć konto partii zadań Azure (lub) [portalu Azure](../batch/batch-account-create-portal.md) Aby utworzyć konto partii zadań Azure za pomocą portalu Azure. Zobacz [przy użyciu programu PowerShell do zarządzania konta usługi partia zadań Azure](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) tematu, aby uzyskać szczegółowe instrukcje na temat używania polecenia cmdlet.
* [Nowy AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) polecenia cmdlet, aby utworzyć pulę partii zadań Azure.

### <a name="example"></a>Przykład

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Właściwości
| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| type              | Powinien mieć ustawioną właściwość type **AzureBatch**. | Yes      |
| accountName       | Nazwa konta partii zadań Azure.         | Yes      |
| accessKey         | Klucz dostępu dla konta usługi partia zadań Azure.  | Yes      |
| batchUri          | Adres URL do konta partii zadań Azure w formacie https://*batchaccountname.region*. batch.azure.com. | Yes      |
| poolName          | Nazwa puli maszyn wirtualnych.    | Yes      |
| linkedServiceName | Nazwa usługi Azure Storage połączonej usługi skojarzone z tą usługą partii zadań Azure połączone. Tej połączonej usługi jest używany na potrzeby przemieszczania plików wymaganych do uruchomienia wybranego działania. | Yes      |
| connectVia        | Środowisko uruchomieniowe integracji ma być używany do wysyłania działania do tej połączonej usługi. Możesz użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Nie       |

## <a name="azure-machine-learning-linked-service"></a>Usługa Azure Machine Learning połączone
Można utworzyć usługi Azure Machine Learning połączony do zarejestrowania partii uczenia maszynowego punktu końcowego oceniania z fabryką danych.

### <a name="example"></a>Przykład

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Właściwości
| Właściwość               | Opis                              | Wymagane                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Powinien mieć ustawioną właściwość type: **uczenie maszynowe Azure**. | Yes                                      |
| mlEndpoint             | Adres URL wsadowego oceniania.                   | Yes                                      |
| apiKey                 | Interfejs API modelu opublikowanych obszaru roboczego.     | Yes                                      |
| updateResourceEndpoint | Adres URL aktualizowania zasobu dla punktu końcowego usługi sieci Web uczenie Maszynowe Azure używana do aktualizowania predykcyjnej usługi sieci Web z plikiem uczonego modelu | Nie                                       |
| servicePrincipalId     | Określ identyfikator aplikacji klienta.     | Wymagane, jeśli jest określony updateResourceEndpoint |
| servicePrincipalKey    | Określ klucz aplikacji.           | Wymagane, jeśli jest określony updateResourceEndpoint |
| dzierżawa                 | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można go pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. | Wymagane, jeśli jest określony updateResourceEndpoint |
| connectVia             | Środowisko uruchomieniowe integracji ma być używany do wysyłania działania do tej połączonej usługi. Możesz użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Nie                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Usługa Azure Data Lake Analytics połączone usługi
Możesz utworzyć **Azure Data Lake Analytics** połączonej usługi, aby połączyć z usługą Azure Data Lake Analytics obliczeniowe usługi fabryka danych Azure. Data Lake Analytics U-SQL działania w potoku odwołuje się do tej połączonej usługi. 

### <a name="example"></a>Przykład

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Właściwości

| Właściwość             | Opis                              | Wymagane                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | Powinien mieć ustawioną właściwość type: **AzureDataLakeAnalytics**. | Yes                                      |
| accountName          | Nazwa konta usługi Azure Data Lake Analytics.  | Yes                                      |
| dataLakeAnalyticsUri | Identyfikator URI, usługi Azure Data Lake Analytics.           | Nie                                       |
| subscriptionId       | Identyfikator subskrypcji platformy Azure                    | Nie (Jeśli nie zostanie określony, używany subskrypcji fabryki danych). |
| resourceGroupName    | Nazwa grupy zasobów platformy Azure                | Nie (Jeśli nie zostanie określony, używana grupa zasobów z fabryką danych). |
| servicePrincipalId   | Określ identyfikator aplikacji klienta.     | Yes                                      |
| servicePrincipalKey  | Określ klucz aplikacji.           | Yes                                      |
| dzierżawa               | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można go pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. | Yes                                      |
| connectVia           | Środowisko uruchomieniowe integracji ma być używany do wysyłania działania do tej połączonej usługi. Możesz użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. | Nie                                       |



## <a name="azure-databricks-linked-service"></a>Usługa Azure Databricks połączone
Można utworzyć **Azure Databricks połączona usługa** zarejestrować Databricks obszaru roboczego, który zostanie użyty do uruchomienia Databricks workloads(notebooks).

### <a name="example---using-new-job-cluster-in-databricks"></a>Przykład — przy użyciu nowego klastra zadania w Databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Przykład — przy użyciu istniejącego klastra interakcyjnego w Databricks

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Właściwości

| Właściwość             | Opis                              | Wymagane                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | Nazwa połączonej usługi               | Yes   |
| type                 | Powinien mieć ustawioną właściwość type: **AzureDatabricks**. | Yes                                      |
| domena               | Określ Region platformy Azure, odpowiednio oparte na obszaru roboczego Databricks. Przykład: https://eastus.azuredatabricks.net | Yes                                 |
| accessToken          | Token dostępu jest wymagane dla fabryki danych do uwierzytelniania Azure Databricks. Token dostępu musi być uzyskane z obszaru roboczego databricks. Szczegółowe kroki, aby znaleźć tokenu dostępu można znaleźć [tutaj](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Yes                                       |
| existingClusterId    | Identyfikator klastra z istniejącego klastra do uruchamiania wszystkich zadań na tym. Powinno to być już utworzonego klastra interaktywnego. Może być konieczne ręczne ponowne uruchomienie klastra, jeśli przestaje odpowiadać. Databricks sugeruje uruchomionych zadań na nowych klastrów zwiększa niezawodność. Identyfikator klastra można znaleźć interakcyjne klastra na Databricks -> obszar roboczy klastrów -> interaktywnego nazwa klastra -> Konfiguracja -> tagów. [więcej informacji](https://docs.databricks.com/user-guide/clusters/tags.html) | Nie 
| newClusterVersion    | Wersja klastra Spark. Klaster zadania zostanie utworzony w databricks. | Nie  |
| newClusterNumOfWorker| Liczba węzłów procesu roboczego, które powinny mieć tego klastra. Klaster ma jeden sterownika Spark i num_workers z modułów wykonujących dla danych całkowitych num_workers + 1 węzłów Spark. Ciąg sformatowany Int32, jak jest "1" oznacza numOfWorker 1 lub "1:10" oznacza automatycznego skalowania z 1 min i 10 jako max.  | Nie                |
| newClusterNodeType   | To pole koduje za pośrednictwem pojedynczej wartości zasoby dostępne dla każdego z węzłów Spark, w tym klastrze. Na przykład Spark węzłów może być udostępniane i zoptymalizowane pod kątem pamięci lub obliczeniowych intensywnych obciążeń to pole jest wymagane dla nowego klastra                | Nie               |
| newClusterSparkConf  | zestaw par klucz wartość konfiguracji Spark opcjonalne, określone przez użytkownika. Użytkownicy mogą również przenosić w ciągu dodatkowe opcje JVM do sterownika i modułów za pośrednictwem spark.driver.extraJavaOptions i spark.executor.extraJavaOptions odpowiednio. | Nie  |


## <a name="azure-sql-database-linked-service"></a>Połączona usługa Azure SQL Database
Tworzenie Azure połączoną usługą SQL i użyj go przy użyciu [działania dotyczącego procedury składowanej](transform-data-using-stored-procedure.md) aby wywołać procedurę składowaną z potoku fabryki danych. Zobacz [Łącznik usług SQL Azure](connector-azure-sql-database.md#linked-service-properties) artykułu, aby uzyskać szczegółowe informacje o tej połączonej usługi.

## <a name="azure-sql-data-warehouse-linked-service"></a>Usługa Azure SQL Data Warehouse połączone
Tworzenie usługi Azure SQL Data Warehouse połączone i używać go z [działania dotyczącego procedury składowanej](transform-data-using-stored-procedure.md) aby wywołać procedurę składowaną z potoku fabryki danych. Zobacz [łącznika magazynu danych SQL Azure](connector-azure-sql-data-warehouse.md#linked-service-properties) artykułu, aby uzyskać szczegółowe informacje o tej połączonej usługi.

## <a name="sql-server-linked-service"></a>Usługi SQL Server połączone
Tworzenie usługi SQL Server połączone i używać go z [działania dotyczącego procedury składowanej](transform-data-using-stored-procedure.md) aby wywołać procedurę składowaną z potoku fabryki danych. Zobacz [łącznika programu SQL Server](connector-sql-server.md#linked-service-properties) artykułu, aby uzyskać szczegółowe informacje o tej połączonej usługi.

## <a name="azure-data-factory---naming-rules"></a>Fabryka danych Azure - reguły nazewnictwa
W poniższej tabeli przedstawiono reguły nazewnictwa artefaktów fabryki danych.

| Name (Nazwa)                             | Name Uniqueness                          | Sprawdzanie poprawności                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| Fabryka danych                     | Unikatowe w obrębie platformy Microsoft Azure. Nazwy jest rozróżniana wielkość liter, czyli `MyDF` i `mydf` odwoływać się do tej samej fabryki danych. | <ul><li>Każdej fabryki danych jest powiązany dokładnie jedną subskrypcją platformy Azure.</li><li>Nazwy obiektów muszą zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).</li><li>Każdy znak kreski (-) musi być od razu poprzedzone i następuje literą lub cyfrą. Następujących po sobie kresek nie są dozwolone w nazwach kontenerów.</li><li>Nazwa może być 3 do 63 znaków.</li></ul> |
| Połączonej usługi/tabel/potoki | Unikatowy o w fabryce danych. Nazwy jest rozróżniana wielkość liter. | <ul><li>Maksymalna liczba znaków w nazwie tabeli: 260.</li><li>Nazwa obiektu musi rozpoczynać się od litery, liczby lub znaku podkreślenia (_).</li><li>Następujące znaki nie są dozwolone: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul> |
| Grupa zasobów                   | Unikatowe w obrębie platformy Microsoft Azure. Nazwy jest rozróżniana wielkość liter. | <ul><li>Maksymalna liczba znaków: 1000.</li><li>Nazwa może zawierać litery, cyfry i następujące znaki: "-", "_",","i"."</li></ul> |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę działań przekształcania obsługiwane przez usługi fabryka danych Azure, zobacz [przekształcania danych](transform-data.md).
