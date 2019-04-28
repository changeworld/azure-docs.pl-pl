---
title: Wykonywanie zadań Apache Sqoop z usługą Azure HDInsight (Apache Hadoop)
description: Dowiedz się, jak używać programu Azure PowerShell na stacji roboczej do uruchamiania narzędzia Sqoop importu i eksportu między klastrem Hadoop a bazą danych Azure SQL database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129403"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Z usługą Hadoop w HDInsight przy użyciu narzędzia Apache Sqoop
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak do importowania i eksportowania danych między klastrem usługi HDInsight a bazą danych Azure SQL database przy użyciu narzędzia Apache Sqoop w HDInsight.

Mimo że Apache Hadoop to naturalny wybór dotyczący przetwarzania danych bez struktury i o połowicznej strukturze, takie jak dzienniki i pliki, mogą również być potrzebne do przetwarzania danych strukturalnych, które są przechowywane w relacyjnych baz danych.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) to narzędzie do transferu danych między klastrami Hadoop a relacyjnymi bazami danych. Służy do importowania danych z systemu zarządzania relacyjnymi bazami danych (RDBMS), takie jak SQL Server, MySQL lub Oracle do rozproszonego systemu plików Hadoop (HDFS), przekształcania danych na platformie Hadoop MapReduce lub Apache Hive i następnie eksportować dane z powrotem do RDBMS . W tym artykule używasz bazy danych programu SQL Server Twoja relacyjna baza danych.

> [!IMPORTANT]  
> W tym artykule konfiguruje środowiska testowego do transferu danych. Następnie wybierz metodę transferu danych dla tego środowiska z jednej z metod opisanych w sekcji [zadania uruchamiania narzędzia Sqoop](#run-sqoop-jobs), poniżej.

Dla wersji Sqoop, które są obsługiwane w klastrach HDInsight, zobacz [nowości w wersjach klastra, dostarczone przez HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Omówienie scenariusza

Klaster HDInsight jest dostarczany z pewnymi przykładowymi danymi. Możesz użyć następujących dwóch próbek:

* Pliku dziennika Apache Log4j, który znajduje się w folderze `/example/data/sample.log`. Następujące dzienniki są wyodrębniane z pliku:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Tabela programu Hive o nazwie `hivesampletable`, które odwołują się do pliku danych znajdującym się w `/hive/warehouse/hivesampletable`. Tabela zawiera niektóre dane o urządzeniach mobilnych.
  
  | Pole | Typ danych |
  | --- | --- |
  | clientid |string |
  | querytime |string |
  | na rynku |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | Kraj |string |
  | querydwelltime |double |
  | Identyfikator sesji |bigint |
  | sessionpagevieworder |bigint |

W tym artykule używasz tych dwóch zestawów danych do testowania Sqoop importu i eksportu.

## <a name="create-cluster-and-sql-database"></a>Konfigurowanie środowiska testowego
Klaster bazy danych SQL i inne obiekty są tworzone w witrynie Azure portal przy użyciu szablonu usługi Azure Resource Manager. Szablon można znaleźć w [szablony szybkiego startu platformy](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Szablon usługi Resource Manager wywołuje pakiet bacpac do wdrożenia schematów tabel do usługi SQL database.  Pakiet bacpac znajduje się w publicznym kontenerze obiektów blob, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Jeśli chcesz użyć kontenera prywatnych dla plików bacpac, użyj następujących wartości w szablonie:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importowanie za pomocą szablonu lub witryny Azure portal obsługuje tylko importowanie pliku BACPAC z magazynu obiektów blob platformy Azure.

1. Wybierz poniższy obraz, aby otworzyć szablon usługi Resource Manager w witrynie Azure portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Wprowadź następujące właściwości:

    |Pole |Wartość |
    |---|---|
    |Subskrypcja |Wybierz subskrypcję platformy Azure z listy rozwijanej.|
    |Grupa zasobów |Wybierz grupę zasobów z listy rozwijanej lub Utwórz nową|
    |Lokalizacja |Wybierz region z listy rozwijanej.|
    |Nazwa klastra |Wprowadź nazwę klastra usługi Hadoop. Użyj tylko małą literą.|
    |Nazwa użytkownika logowania klastra |Należy zachować wartość wstępnie wypełnionych `admin`.|
    |Hasło logowania klastra |Wprowadź hasło.|
    |Nazwa użytkownika SSH |Należy zachować wartość wstępnie wypełnionych `sshuser`.|
    |SSH hasła |Wprowadź hasło.|
    |Identyfikator logowania administratora SQL |Należy zachować wartość wstępnie wypełnionych `sqluser`.|
    |Hasło administratora SQL |Wprowadź hasło.|
    |Lokalizacja _artifacts | Użyj wartości domyślnej, chyba że chcesz użyć własnego pliku bacpac w innej lokalizacji.|
    |Token sygnatury dostępu współdzielonego lokalizacji _artifacts |Pozostaw to pole puste.|
    |Nazwa pliku Bacpac |Użyj wartości domyślnej, chyba że chcesz użyć własnego pliku bacpac.|
    |Lokalizacja |Użyj wartości domyślnej.|

    Nazwa serwera SQL Azure będzie `<ClusterName>dbserver`. Nazwa bazy danych będzie `<ClusterName>db`. Domyślna nazwa konta magazynu będzie `e6qhezrh2pdqu`.

3. Wybierz **zgodę na warunki i postanowienia, o których wspomniano**.

4. Wybierz pozycję **Kup**. Zostanie wyświetlony nowy Kafelek zatytułowany przesyłanie wdrożenia dla wdrożenia szablonu. Utworzenie klastra i bazy danych SQL trwa około 20 minut.

## <a name="run-sqoop-jobs"></a>Uruchamianie zadań narzędzia Sqoop

HDInsight można uruchomić zadania Sqoop przy użyciu różnych metod. Skorzystaj z poniższej tabeli do określania, która metoda jest odpowiedni dla Ciebie, a następnie kliknij link, aby uzyskać wskazówki.

| **Użyj tej** Jeśli chcesz... | ...an **interakcyjne** powłoki | ...**partii** przetwarzania | ...from to **system operacyjny klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X lub Windows |
| [Zestaw SDK dla platformy .NET usługi Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (na razie) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Ograniczenia

* Zbiorcze export - HDInsight opartych na systemie Linux za pomocą, łącznik Sqoop, używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database nie obsługuje obecnie zbiorcze operacje wstawiania.
* Przetwarzanie wsadowe — za pomocą HDInsight opartych na systemie Linux, korzystając z `-batch` przełącznika podczas wykonywania operacji wstawienia, Sqoop wykonuje wiele operacji wstawiania zamiast przetwarzanie wsadowe operacji wstawiania.

## <a name="next-steps"></a>Kolejne kroki
Teraz masz pokazaliśmy, jak przy użyciu narzędzia Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Use Apache Hive z HDInsight](../hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](../hdinsight-use-pig.md)
* [Przekazywanie danych do HDInsight](../hdinsight-upload-data.md): Znajdź inne metody przekazywania danych do usługi HDInsight/Azure Blob storage.
