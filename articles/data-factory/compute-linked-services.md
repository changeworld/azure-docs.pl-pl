---
title: Obliczenia środowisk obsługiwanych przez usługę Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat środowisk obliczeniowych, w których można użyć w potokach usługi Azure Data Factory (takich jak Azure HDInsight) do przekształcania lub przetwarzania danych.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 0e7405e48307091ff5df12096d49a00c011e2de3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480433"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Obliczenia środowisk obsługiwanych przez usługę Azure Data Factory
W tym artykule opisano różnych środowiskach obliczeniowych, które służą do procesu lub przekształcania danych. Udostępniają one także szczegółowe informacje o różnych konfiguracjach (na żądanie i skorzystaj z własnych) obsługiwane przez usługę Data Factory, podczas konfigurowania usługi połączone, łączenia tych obliczeń środowisk na potrzeby usługi Azure data factory.

Poniższa tabela zawiera listę środowisk obliczeniowych obsługiwanych przez usługi Data Factory i działania, które można uruchomić na nich. 

| Środowisko obliczeniowe                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Klaster HDInsight na żądanie](#azure-hdinsight-on-demand-linked-service) lub [klaster HDInsight](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Custom](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning działania: Wykonywanie wsadowe i aktualizacja zasobów](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Język U-SQL usługi Data Lake Analytics](transform-data-using-data-lake-analytics.md) |
| [Usługi Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [programu SQL Server](#sql-server-linked-service) | [Procedura składowana](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notes](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [języka Python](transform-data-databricks-python.md) |

>  

## <a name="on-demand-hdinsight-compute-environment"></a>Środowisko obliczeniowe HDInsight na żądanie
W tym typie konfiguracji środowiska komputerowego jest w pełni zarządzana przez usługę Azure Data Factory. Zostanie automatycznie utworzony przez usługę Data Factory przed zadanie jest przesyłane do przetwarzania danych i usuwane, gdy zadanie jest ukończone. Można tworzenie połączonej usługi dla środowiska obliczeniowego na żądanie, jest skonfigurowana i kontrolować szczegółowe ustawienia wykonywania zadań, zarządzanie klastrami i uruchamianie akcji.

> [!NOTE]
> Konfiguracja na żądanie jest obecnie obsługiwane tylko w przypadku klastrów Azure HDInsight. Usługa Azure Databricks obsługuje również zadania na żądanie przy użyciu zadania klastrów, zapoznaj się [usługę połączoną usługi Azure databricks](#azure-databricks-linked-service) Aby uzyskać więcej informacji.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Usługa Azure HDInsight na żądanie połączonej usługi
Usługa Azure Data Factory może automatycznie tworzyć klaster HDInsight na żądanie do przetwarzania danych. Klaster jest tworzony w tym samym regionie co konto magazynu (właściwość linkedServiceName w formacie JSON) skojarzonego z klastrem. Konto magazynu musi być kontem ogólnego przeznaczenia standardowego magazynu platformy Azure. 

Należy pamiętać o następujących **ważne** punkty o HDInsight na żądanie połączoną usługę:

* Klaster HDInsight na żądanie jest tworzony w ramach Twojej subskrypcji platformy Azure. Jesteś mogli zobaczyć klastra w portalu Azure, gdy klaster działa i jest uruchomiona. 
* Dzienniki zadań uruchamianych w klastrze usługi HDInsight na żądanie są kopiowane do konta magazynu skojarzonego z klastrem HDInsight. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword zdefiniowane w definicji połączonej usługi są używane do logowania do klastra na potrzeby szczegółowego rozwiązywania problemów podczas cyklu życia klastra. 
* Opłaty są naliczane tylko za czas, gdy klaster HDInsight działa i uruchomionych zadań.
* Możesz użyć **akcji skryptu** za pomocą usługi Azure HDInsight na żądanie połączoną usługę.  

> [!IMPORTANT]
> Trwa zwykle **20 minut** co najmniej do inicjowania obsługi klastra Azure HDInsight na żądanie.

### <a name="example"></a>Przykład
Następujący kod JSON definiuje opartych na systemie Linux usługi połączonej HDInsight na żądanie. Usługa Data Factory automatycznie tworzy **opartych na systemie Linux** klastra HDInsight do przetwarzania działań. 

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
> Po uruchomieniu więcej aktywności w usłudze Azure blob storage będzie widocznych wiele kontenerów. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Aby usunąć kontenery z usługi Azure Blob Storage, użyj takich narzędzi, jak [Microsoft Storage Explorer](https://storageexplorer.com/).
>
> 

### <a name="properties"></a>Właściwości
| Właściwość                     | Opis                              | Wymagane |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Właściwość type powinna być równa **HDInsightOnDemand**. | Tak      |
| clusterSize                  | Liczba węzłów procesu roboczego/danych w klastrze. Klaster HDInsight jest tworzony z 2 węzłami głównymi wraz z liczbą węzłów procesów roboczych, które określisz dla tej właściwości. Węzły są o rozmiarze maszyna wirtualna Standard_D3, który ma 4 rdzenie, dzięki czemu klaster z węzłami procesu roboczego 4 przyjmuje 24 rdzenie (4\*4 = 16 rdzeni dla węzłów procesu roboczego oraz 2\*4 = 8 rdzeni dla węzłów głównych). Zobacz [konfigurowanie klastrów w HDInsight przy użyciu usługi Hadoop, Spark, Kafka i](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) Aby uzyskać szczegółowe informacje. | Tak      |
| linkedServiceName            | Połączona usługa Azure Storage używanego przez klaster na żądanie do przechowywania i przetwarzania danych. Klaster HDInsight jest tworzony w tym samym regionie, co to konto usługi Azure Storage. Usługa Azure HDInsight ma ograniczenia całkowitej liczby rdzeni, których możesz użyć w każdym obsługiwanym przez nią regionie platformy Azure. Upewnij się, że masz wystarczająco duże limity przydziału w danym regionie platformy Azure w celu spełnienia wymagana wartość clusterSize. Aby uzyskać szczegółowe informacje, zapoznaj się [konfigurowanie klastrów w HDInsight przy użyciu usługi Hadoop, Spark, Kafka i więcej](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Obecnie nie można utworzyć klastra HDInsight na żądanie, która używa usługi Azure Data Lake Store jako magazyn. Jeśli chcesz przechowywać dane wynikowe z HDInsight przetwarzania w usłudze Azure Data Lake Store umożliwia działanie kopiowania skopiuj dane z usługi Azure Blob Storage do usługi Azure Data Lake Store. </p> | Yes      |
| clusterResourceGroup         | Klaster HDInsight jest tworzony w tej grupie zasobów. | Tak      |
| timetolive                   | Dozwolony czas bezczynności, po dla klastra HDInsight na żądanie. Określa, jak długo klastra HDInsight na żądanie pozostanie aktywny po zakończeniu działania uruchamiania w przypadku żadnych aktywnych działań w klastrze. Minimalne dozwolone wartości to 5 minut (00: 05:00).<br/><br/>Na przykład po uruchomienia działania trwa 6 minut i timetolive jest ustawiony na 5 minut, klaster pozostanie aktywny przez 5 minut po uruchomieniu 6 minut operacji przetwarzania działania. Jeśli uruchomienie innego działania jest wykonywane przy użyciu okna 6 minut, jest on przetwarzany przez tego samego klastra.<br/><br/>Tworzenie klastra usługi HDInsight na żądanie jest kosztowną operacją (może to trochę potrwać), użyj tak, to ustawienie jako potrzebnych do zwiększenia wydajności usługi data factory dzięki ponownemu wykorzystaniu klastra usługi HDInsight na żądanie.<br/><br/>Jeśli wartość timetolive jest ustawiona na wartość 0, klaster jest usuwany natychmiast po zakończeniu przebiegu działania. Natomiast jeśli ustawisz o wysokiej wartości, klaster może pozostać bezczynny zalogować się do rozwiązywania niektórych problemów cel, ale może spowodować wysokie koszty. Dlatego ważne jest, ustaw odpowiednią wartość zgodnie z potrzebami.<br/><br/>Jeśli skonfigurowana wartość właściwości timetolive wiele potoków można udostępniać wystąpienia klastra HDInsight na żądanie. | Tak      |
| clusterType                  | Typ klastra HDInsight, który ma zostać utworzony. Dozwolone wartości to "usługi hadoop" i "spark". Jeśli nie zostanie określony, wartością domyślną jest hadoop. Pakiet Enterprise Security włączone klastra nie można utworzyć na żądanie, zamiast tego użyć [istniejący klaster / Przenieś własnych obliczeń](#azure-hdinsight-linked-service). | Nie       |
| version                      | Wersja klastra HDInsight. Jeśli nie zostanie określony, to przy użyciu bieżącej wersji zdefiniowanego domyślnego HDInsight. | Nie       |
| hostSubscriptionId           | Identyfikator subskrypcji platformy Azure, użyte do utworzenia klastra HDInsight. Jeśli nie zostanie określony, używa Identyfikatora subskrypcji z kontekstu logowania do platformy Azure. | Nie       |
| clusterNamePrefix           | Prefiks nazwy klastra usługi HDI sygnaturę czasową będzie automatycznie dołączany na końcu nazwy klastra| Nie       |
| Parametrami                 | Wersja platformy spark, jeśli typ klastra jest "Spark" | Nie       |
| additionalLinkedServiceNames | Określa, że dodatkowe konta magazynu dla HDInsight połączonej usługi, tak aby usługa Data Factory można zarejestrować je w Twoim imieniu. Te konta magazynu musi być w tym samym regionie co klaster HDInsight, który jest tworzony w tym samym regionie co konto magazynu określone przez linkedServiceName. | Nie       |
| osType                       | Typ systemu operacyjnego. Dozwolone wartości to: Linux i Windows (w przypadku HDInsight 3.3 tylko). Wartość domyślna to Linux. | Nie       |
| hcatalogLinkedServiceName    | Nazwa programu SQL Server, Azure połączoną usługę, wskazujące HCatalog bazy danych. Klaster HDInsight na żądanie jest tworzony przy użyciu bazy danych Azure SQL jako magazynu metadanych. | Nie       |
| connectVia                   | Integration Runtime, który ma być używany do wysyłania działania do tej usługi połączonej HDInsight. Usługi połączonej HDInsight na żądanie obsługuje on tylko Azure Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie       |
| clusterUserName                   | Nazwa użytkownika, dostęp do klastra. | Nie       |
| clusterPassword                   | Hasło w polu Typ bezpieczny ciąg, aby uzyskać dostęp do klastra. | Nie       |
| clusterSshUserName         | Nazwa użytkownika SSH połączenie zdalne do węzła klastra (dla systemu Linux). | Nie       |
| clusterSshPassword         | Hasło w polu Typ bezpieczny ciąg SSH połączenie zdalne (dla systemu Linux) w węźle klastra. | Nie       |
| scriptActions | Określ skrypt [dostosowania klastra HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) podczas tworzenia klastra na żądanie. <br />Obecnie narzędzie do tworzenia interfejsu użytkownika usługi Azure Data Factory obsługuje określanie akcji skryptu tylko 1, ale możesz uzyskać za pośrednictwem tego ograniczenia w kodzie JSON (Określ wiele akcji skryptów w kodzie JSON). | Nie |


> [!IMPORTANT]
> HDInsight obsługuje wielu wersjach klastrów Hadoop, które mogą zostać wdrożone. Każdy wybór wersji tworzy określoną wersję dystrybucji Hortonworks Data Platform (HDP) i zestaw składników, które są zawarte w tej dystrybucji. Listę obsługiwanych wersji HDInsight przechowuje zaktualizowany tak, aby najnowszych składników ekosystemu Hadoop i poprawki. Upewnij się, że zawsze odwołuje się do najnowszych informacji o [HDInsight obsługiwana wersja i typ systemu operacyjnego](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) zapewnienie używasz obsługiwanej wersji HDInsight. 
>
> [!IMPORTANT]
> Obecnie usługa HDInsight połączonych usług nie obsługuje bazy danych HBase, zapytania interakcyjnego (LLAP programu Hive), platformy Storm. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>przykład kodu JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Usługa HDInsight na żądanie, połączone wymaga uwierzytelniania jednostki usługi w celu tworzenia klastrów HDInsight w Twoim imieniu. Do używania uwierzytelniania jednostki usługi, Zarejestruj aplikację jednostki w usłudze Azure Active Directory (Azure AD) i przyznania **Współautor** roli subskrypcji lub grupy zasobów, w której został utworzony klaster HDInsight. Aby uzyskać szczegółowe instrukcje, zobacz [w obsłudze portalu do tworzenia aplikacji i usługi jednostki, które mogą uzyskiwać dostęp do zasobów usługi Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji 
- Identyfikator dzierżawy

Użyj uwierzytelniania jednostki usługi, określając następujące właściwości:

| Właściwość                | Opis                              | Wymagane |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Określ identyfikator klienta aplikacji.     | Tak      |
| **servicePrincipalKey** | Określ klucz aplikacji.           | Tak      |
| **dzierżawy**              | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure portal. | Tak      |

### <a name="advanced-properties"></a>Właściwości zaawansowane

Można również określić następujące właściwości w przypadku szczegółowej konfiguracji klastra HDInsight na żądanie.

| Właściwość               | Opis                              | Wymagane |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Określa parametry konfiguracji podstawowej (jak core-site.xml) dla klastra HDInsight, który ma zostać utworzony. | Nie       |
| hBaseConfiguration     | Określa parametry konfiguracji bazy danych HBase (hbase-site.xml) dla klastra HDInsight. | Nie       |
| hdfsConfiguration      | Określa parametry konfiguracji systemu plików HDFS (systemu plików hdfs-site.xml) dla klastra HDInsight. | Nie       |
| hiveConfiguration      | Określa parametry konfiguracji gałęzi (gałęzi site.xml) dla klastra HDInsight. | Nie       |
| mapReduceConfiguration | Określa parametry konfiguracji MapReduce (mapred-site.xml) dla klastra HDInsight. | Nie       |
| oozieConfiguration     | Określa parametry konfiguracji Oozie (oozie-site.xml) dla klastra HDInsight. | Nie       |
| stormConfiguration     | Określa parametry konfiguracji system Storm (storm-site.xml) dla klastra HDInsight. | Nie       |
| yarnConfiguration      | Określa parametry konfiguracji usługi Yarn (yarn-site.xml) dla klastra HDInsight. | Nie       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Przykład — konfiguracja klastra HDInsight na żądanie przy użyciu właściwości zaawansowane

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

### <a name="node-sizes"></a>Rozmiary węzłów
Można określić wielkości head, danych i węzły dozorcy z następującymi właściwościami: 

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Określa rozmiar węzła głównego. Wartość domyślna to: Maszyna wirtualna Standard_D3. Zobacz **Określanie rozmiary węzłów** sekcji, aby uzyskać szczegółowe informacje. | Nie       |
| dataNodeSize      | Określa rozmiar węzła danych. Wartość domyślna to: Maszyna wirtualna Standard_D3. | Nie       |
| zookeeperNodeSize | Określa rozmiar węzła dozorca Zoo. Wartość domyślna to: Maszyna wirtualna Standard_D3. | Nie       |

#### <a name="specifying-node-sizes"></a>Określanie rozmiary węzła
Zobacz [rozmiarów maszyn wirtualnych](../virtual-machines/linux/sizes.md) artykuł dotyczący wartości ciągu, należy określić właściwości, o których wspomniano w poprzedniej sekcji. Wartości muszą być zgodna z **poleceń cmdlet i interfejsów API** przywoływany w artykule. Jak widać w artykule węzeł danych o rozmiarze duże (ustawienie domyślne), będzie miał 7 GB pamięci, co może nie być wystarczające dla danego scenariusza. 

Jeśli chcesz utworzyć D4 rozmiar węzłów głównych i węzłów procesu roboczego, należy określić **maszyna wirtualna Standard_D4** jako wartość właściwości headNodeSize i dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Jeśli określono nieprawidłową wartość dla tych właściwości, może pojawić się następujące **błąd:** Nie można utworzyć klastra. Wystąpił wyjątek: Nie można ukończyć operacji tworzenia klastra. Operacja zakończona niepowodzeniem z kodem „400”. Końcowy stan klastra: "Error". Komunikat: "PreClusterCreationValidationFailure". Gdy zostanie wyświetlony ten błąd, upewnij się, że używasz **polecenia CMDLET i interfejsów API** nazwę z tabeli w [rozmiarów maszyn wirtualnych](../virtual-machines/linux/sizes.md) artykułu.        

## <a name="bring-your-own-compute-environment"></a>Przełącz środowiska obliczeniowego
W tym typie konfiguracji użytkownicy mogą rejestrować już istniejące środowisko obliczeniowe jako połączonej usługi w usłudze Data Factory. Środowiska komputerowego odbywa się przez użytkownika, a usługa Data Factory używa go do wykonywania działań.

Ten typ konfiguracji jest obsługiwana dla następujących środowisk obliczeniowych:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Baza danych Azure SQL, Magazyn danych Azure SQL, programu SQL Server

## <a name="azure-hdinsight-linked-service"></a>Usługa Azure HDInsight połączone
Można utworzyć usługi Azure HDInsight połączone do zarejestrowania własnego klastra HDInsight z usługą Data Factory.

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
| Właściwość          | Opis                                                  | Wymagane |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | Właściwość type powinna być równa **HDInsight**.            | Tak      |
| clusterUri        | Identyfikator URI klastra HDInsight.                            | Tak      |
| username          | Podaj nazwę użytkownika, który ma być używany do łączenia z do istniejącego klastra HDInsight. | Tak      |
| password          | Określ hasło dla konta użytkownika.                       | Tak      |
| linkedServiceName | Nazwa połączonej usługi Azure Storage odwołująca się do usługi Azure blob storage używane przez klaster usługi HDInsight. <p>Obecnie nie można określić, że usługi Azure Data Lake Store połączonej usługi dla tej właściwości. Jeśli klaster HDInsight ma dostęp do Data Lake Store, mogą uzyskiwać dostęp do danych w usłudze Azure Data Lake Store, ze skryptów usługi Hive/Pig. </p> | Tak      |
| isEspEnabled      | Określ "*true*" w przypadku klastra HDInsight [pakiet Enterprise Security](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction) włączone. Wartość domyślna to "*false*". | Nie       |
| connectVia        | Integration Runtime, który ma być używany do wysyłania działań z tą usługą połączoną. Można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. <br />Pakiet zabezpieczeń przedsiębiorstwa (ESP) włączone użycia w klastrze HDInsight własnego środowiska integration runtime mającej linii wzroku do klastra lub powinny być wdrażane w tej samej sieci wirtualnej co klaster HDInsight ESP. | Nie       |

> [!IMPORTANT]
> HDInsight obsługuje wielu wersjach klastrów Hadoop, które mogą zostać wdrożone. Każdy wybór wersji tworzy określoną wersję dystrybucji Hortonworks Data Platform (HDP) i zestaw składników, które są zawarte w tej dystrybucji. Listę obsługiwanych wersji HDInsight przechowuje zaktualizowany tak, aby najnowszych składników ekosystemu Hadoop i poprawki. Upewnij się, że zawsze odwołuje się do najnowszych informacji o [HDInsight obsługiwana wersja i typ systemu operacyjnego](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) zapewnienie używasz obsługiwanej wersji HDInsight. 
>
> [!IMPORTANT]
> Obecnie usługa HDInsight połączonych usług nie obsługuje bazy danych HBase, zapytania interakcyjnego (LLAP programu Hive), platformy Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Usługa Azure Batch połączone

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można utworzyć usługi Azure Batch połączone, aby zarejestrować puli usługi Batch maszyn wirtualnych (VM) do usługi data factory. Można uruchomić działania niestandardowego przy użyciu usługi Azure Batch.

Zobacz następujące tematy, jeśli jesteś nowym użytkownikiem usługi Azure Batch:

* [Podstawy usługi Azure Batch](../batch/batch-technical-overview.md) z omówieniem usługi Azure Batch.
* [Nowe AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) polecenia cmdlet, aby utworzyć konto usługi Azure Batch (lub) [witryny Azure portal](../batch/batch-account-create-portal.md) do utworzenia konta usługi Azure Batch przy użyciu witryny Azure portal. Zobacz [przy użyciu programu PowerShell do zarządzania kontem usługi Batch Azure](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) tematu, aby uzyskać szczegółowe instrukcje na temat korzystania z polecenia cmdlet.
* [Nowe AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) polecenie cmdlet do tworzenia puli usługi Azure Batch.

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
| type              | Właściwość type powinna być równa **AzureBatch**. | Tak      |
| accountName       | Nazwa konta usługi Azure Batch.         | Tak      |
| accessKey         | Klucz dostępu dla konta usługi Azure Batch.  | Tak      |
| batchUri          | Adres URL do swojego konta usługi Azure Batch, w formacie https://*batchaccountname.region*. batch.azure.com. | Tak      |
| poolName          | Nazwa puli maszyn wirtualnych.    | Tak      |
| linkedServiceName | Nazwa usługi Azure Storage połączone skojarzonego z tą usługą Azure Batch połączonej usługi. Ta połączona usługa jest używana do przemieszczania plików wymaganych do uruchomienia wybranego działania. | Tak      |
| connectVia        | Integration Runtime, który ma być używany do wysyłania działań z tą usługą połączoną. Można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie       |

## <a name="azure-machine-learning-linked-service"></a>Usługa Azure Machine Learning połączone
Utworzysz usługę Azure Machine Learning połączone do zarejestrowania punktu końcowego oceniania do usługi data factory partii usługi Machine Learning.

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
| Type                   | Właściwość type powinna być równa: **AzureML**. | Tak                                      |
| mlEndpoint             | Adres URL wsadowego oceniania.                   | Tak                                      |
| ApiKey                 | Interfejs API opublikowanego modelu obszaru roboczego firmy.     | Tak                                      |
| updateResourceEndpoint | Adres URL aktualizowania zasobu dla punktu końcowego usługi Azure ML Web Service, używane do aktualizowania predykcyjne usługi sieci Web przy użyciu pliku uczonego modelu | Nie                                       |
| servicePrincipalId     | Określ identyfikator klienta aplikacji.     | Wymagane, jeśli określono updateResourceEndpoint |
| servicePrincipalKey    | Określ klucz aplikacji.           | Wymagane, jeśli określono updateResourceEndpoint |
| tenant                 | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure portal. | Wymagane, jeśli określono updateResourceEndpoint |
| connectVia             | Integration Runtime, który ma być używany do wysyłania działań z tą usługą połączoną. Można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Usługa Azure Data Lake Analytics, połączone usługi
Możesz utworzyć **Azure Data Lake Analytics** połączonej usługi, aby połączyć usługi Azure Data Lake Analytics obliczeń Usługa do usługi Azure data factory. Działanie U-SQL usługi Data Lake Analytics w potoku odnosi się do tej połączonej usługi. 

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
| type                 | Właściwość type powinna być równa: **AzureDataLakeAnalytics**. | Tak                                      |
| accountName          | Nazwa konta usługi Azure Data Lake Analytics.  | Tak                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics URI.           | Nie                                       |
| subscriptionId       | Identyfikator subskrypcji platformy Azure                    | Nie                                       |
| resourceGroupName    | Nazwa grupy zasobów platformy Azure                | Nie                                       |
| servicePrincipalId   | Określ identyfikator klienta aplikacji.     | Yes                                      |
| servicePrincipalKey  | Określ klucz aplikacji.           | Yes                                      |
| tenant               | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure portal. | Tak                                      |
| connectVia           | Integration Runtime, który ma być używany do wysyłania działań z tą usługą połączoną. Można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie                                       |



## <a name="azure-databricks-linked-service"></a>Usługa Azure Databricks połączone
Możesz utworzyć **usługę połączoną usługi Azure Databricks** do zarejestrowania obszaru roboczego usługi Databricks, które będzie używane do uruchamiania workloads(notebooks) usługi Databricks.

### <a name="example---using-new-job-cluster-in-databricks"></a>Przykład — przy użyciu nowego klastra zadania w usłudze Databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Przykład — przy użyciu istniejącego klastra interakcyjne w usłudze Databricks

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
| name                 | Nazwa połączonej usługi               | Tak   |
| — typ                 | Właściwość type powinna być równa: **AzureDatabricks**. | Yes                                      |
| Domeny               | Określ Region platformy Azure, w związku z tym na podstawie tego obszaru roboczego usługi Databricks. Przykład: https://eastus.azuredatabricks.net | Tak                                 |
| accessToken          | Token dostępu jest wymagany dla usługi Data Factory do uwierzytelniania w usłudze Azure Databricks. Token dostępu musi zostać wygenerowany na podstawie obszaru roboczego usługi databricks. Szczegółowe kroki, aby znaleźć token dostępu można znaleźć [tutaj](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Tak                                       |
| existingClusterId    | Identyfikator klastra z istniejącego klastra do uruchamiania wszystkich zadań w tym. Powinna to być już utworzonego klastra interaktywne. Może być konieczne ręczne ponowne uruchomienie klastra, jeśli przestanie odpowiadać. Usługi Databricks sugerują, uruchamia wszystkie zadania w nowych klastrów większej niezawodności. Identyfikator klastra można znaleźć interaktywne klastra w usłudze Databricks -> obszar roboczy klastrów -> interaktywnego nazwa klastra -> Konfiguracja -> tagów. [Więcej szczegółów](https://docs.databricks.com/user-guide/clusters/tags.html) | Nie 
| newClusterVersion    | Wersja platformy Spark klastra. Zostanie utworzony klaster zadania w usłudze databricks. | Nie  |
| newClusterNumOfWorker| Liczba węzłów procesu roboczego, którzy powinni je posiadać tego klastra. Klaster ma jeden sterownik Spark i num_workers Executors łącznie num_workers + 1 węzły platformy Spark. Ciąg sformatowany Int32, takie jak jest "1" oznacza, że numOfWorker 1 lub "1:10" oznacza, że automatyczne skalowanie z 1 jako minimalna i 10 jako maksymalna liczba.  | Nie                |
| newClusterNodeType   | W tym polu koduje za pośrednictwem pojedynczej wartości, a zasoby dostępne dla każdego z węzłów platformy Spark w tym klastrze. Na przykład Spark węzłów można ustanowić i zoptymalizowane pod kątem obciążeń intensywnie korzystających z pamięci lub obliczeniowych to pole jest wymagane dla nowego klastra                | Nie               |
| newClusterSparkConf  | zestaw par klucz wartość konfiguracji aparatu Spark opcjonalne, określonych przez użytkownika. Użytkownicy mogą również przekazać w ciągu dodatkowe opcje maszyny JVM do sterownika i executors za pośrednictwem spark.driver.extraJavaOptions i spark.executor.extraJavaOptions odpowiednio. | Nie  |
| newClusterInitScripts| zestaw skryptów inicjowania opcjonalny, zdefiniowany przez użytkownika dla nowego klastra. Określanie ścieżki DBFS do skryptów init. | Nie  |


## <a name="azure-sql-database-linked-service"></a>Połączona usługa Azure SQL Database
Tworzenie połączonej usługi Azure SQL i używać ich z [działania dotyczącego procedury składowanej](transform-data-using-stored-procedure.md) aby wywołać procedurę składowaną z potoku usługi fabryka danych. Zobacz [Łącznik usług SQL Azure](connector-azure-sql-database.md#linked-service-properties) szczegółowe informacje dotyczące tej połączonej usługi.

## <a name="azure-sql-data-warehouse-linked-service"></a>Usługa Azure SQL Data Warehouse połączone
Tworzenie usługi Azure SQL Data Warehouse połączone i używać ich z [działania dotyczącego procedury składowanej](transform-data-using-stored-procedure.md) aby wywołać procedurę składowaną z potoku usługi fabryka danych. Zobacz [łącznik usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#linked-service-properties) szczegółowe informacje dotyczące tej połączonej usługi.

## <a name="sql-server-linked-service"></a>Połączonej usługi SQL Server
Tworzenie usługi połączonej programu SQL Server i używać ich z [działania dotyczącego procedury składowanej](transform-data-using-stored-procedure.md) aby wywołać procedurę składowaną z potoku usługi fabryka danych. Zobacz [łącznik programu SQL Server](connector-sql-server.md#linked-service-properties) szczegółowe informacje dotyczące tej połączonej usługi.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę działań przekształcania obsługiwane przez usługę Azure Data Factory, zobacz [przekształcania danych](transform-data.md).
