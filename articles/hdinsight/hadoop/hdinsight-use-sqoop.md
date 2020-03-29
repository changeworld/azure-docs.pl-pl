---
title: Uruchamianie zadań Apache Sqoop za pomocą usługi Azure HDInsight (Apache Hadoop)
description: Dowiedz się, jak używać programu Azure PowerShell ze stacji roboczej do uruchamiania importu i eksportowania sqoop między klastrem Hadoop a bazą danych SQL platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951857"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Korzystanie z programu Apache Sqoop za pomocą platformy Hadoop w usłudze HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać apache Sqoop w programie HDInsight do importowania i eksportowania danych między klastrem HDInsight a bazą danych SQL platformy Azure.

Chociaż Apache Hadoop jest naturalnym wyborem do przetwarzania danych niestrukturalnych i częściowo ustrukturyzowanych, takich jak dzienniki i pliki, może istnieć również potrzeba przetwarzania danych strukturalnych, które są przechowywane w relacyjnych bazach danych.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) to narzędzie przeznaczone do przesyłania danych między klastrami Hadoop a relacyjnych baz danych. Można go używać do importowania danych z relacyjnego systemu zarządzania bazami danych (RDBMS), takiego jak SQL Server, MySQL lub Oracle do rozproszonego systemu plików Hadoop (HDFS), przekształcania danych w Hadoop za pomocą MapReduce lub Apache Hive, a następnie eksportowania danych z powrotem do RDBMS . W tym artykule używasz bazy danych programu SQL Server dla relacyjnej bazy danych.

> [!IMPORTANT]  
> W tym artykule konfiguruje środowisko testowe do wykonywania transferu danych. Następnie wybierz metodę transferu danych dla tego środowiska z jednej z metod w sekcji [Uruchom zadania Sqoop](#run-sqoop-jobs), poniżej.

W przypadku wersji Sqoop, które są obsługiwane w klastrach HDInsight, zobacz [Co nowego w wersjach klastra dostarczonych przez HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Omówienie scenariusza

Klaster HDInsight zawiera kilka przykładowych danych. Można użyć następujących dwóch przykładów:

* Plik dziennika Apache Log4j, który `/example/data/sample.log`znajduje się w . Z pliku wyodrębniane są następujące dzienniki:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Tabela gałęzi `hivesampletable`o nazwie , która odwołuje `/hive/warehouse/hivesampletable`się do pliku danych znajdującego się w pliku . Tabela zawiera dane urządzenia przenośnego.
  
  | Pole | Typ danych |
  | --- | --- |
  | clientid |ciąg |
  | czas zapytania |ciąg |
  | rynek |ciąg |
  | deviceplatform (platforma urządzeń) |ciąg |
  | urządzeniemake |ciąg |
  | model urządzenia |ciąg |
  | state |ciąg |
  | country |ciąg |
  | querydwelltime |double |
  | Sessionid |bigint |
  | sessionpagevieworder |bigint |

W tym artykule użyjesz tych dwóch zestawów danych do testowania importu i eksportu sqoop.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Konfigurowanie środowiska testowego

Klaster, baza danych SQL i inne obiekty są tworzone za pośrednictwem witryny Azure Portal przy użyciu szablonu usługi Azure Resource Manager. Szablon można znaleźć w [szablonach szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Szablon Menedżera zasobów wywołuje pakiet bacpac, aby wdrożyć schematy tabeli w bazie danych SQL.  Pakiet bacpac znajduje się w publicznym https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpackontenerze obiektów blob, . Jeśli chcesz użyć prywatnego kontenera dla plików bacpac, użyj następujących wartości w szablonie:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importowanie przy użyciu szablonu lub witryny Azure portal obsługuje tylko importowanie pliku BACPAC z magazynu obiektów blob platformy Azure.

1. Wybierz poniższy obraz, aby otworzyć szablon Menedżera zasobów w witrynie Azure portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Wprowadź następujące właściwości:

    |Pole |Wartość |
    |---|---|
    |Subskrypcja |Wybierz subskrypcję platformy Azure z listy rozwijanej.|
    |Grupa zasobów |Wybierz grupę zasobów z listy rozwijanej lub utwórz nową|
    |Lokalizacja |Wybierz region z listy rozwijanej.|
    |Nazwa klastra |Wprowadź nazwę klastra usługi Hadoop. Używaj tylko małych liter.|
    |Nazwa użytkownika logowania klastra |Zachowaj wstępnie wypełnioną wartość `admin`.|
    |Hasło logowania klastra |Wprowadź hasło.|
    |Nazwa użytkownika Ssh |Zachowaj wstępnie wypełnioną wartość `sshuser`.|
    |Hasło Ssh |Wprowadź hasło.|
    |Logowanie administratora sql |Zachowaj wstępnie wypełnioną wartość `sqluser`.|
    |Hasło administratora języka SQL |Wprowadź hasło.|
    |lokalizacja _artifacts | Użyj wartości domyślnej, chyba że chcesz użyć własnego pliku bacpac w innej lokalizacji.|
    |_artifacts Token Sas lokalizacji |Pozostaw to pole puste.|
    |Nazwa pliku Bacpac |Użyj wartości domyślnej, chyba że chcesz użyć własnego pliku bacpac.|
    |Lokalizacja |Użyj wartości domyślnej.|

    Nazwa programu Azure SQL `<ClusterName>dbserver`Server będzie . Nazwa bazy danych `<ClusterName>db`będzie . Domyślna nazwa konta `e6qhezrh2pdqu`magazynu to .

3. Wybierz **zgadzam się z warunkami podanymi powyżej.**

4. Wybierz pozycję **Kup**. Zostanie wyświetlony nowy kafelek o nazwie Przesyłanie wdrożenia do wdrożenia szablonu. Utworzenie klastra i bazy danych SQL trwa około 20 minut.

## <a name="run-sqoop-jobs"></a>Uruchamianie zadań Sqoop

HDInsight można uruchomić sqoop zadań przy użyciu różnych metod. Użyj poniższej tabeli, aby zdecydować, która metoda jest odpowiednia dla Ciebie, a następnie kliknij łącze dla instruktażu.

| **Użyj tego,** jeśli chcesz... | ... **interaktywna** powłoka | ... przetwarzanie **wsadowe** | ... z tego **systemu operacyjnego klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [Ssh](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X lub Windows |
| [Zestaw SDK dla platformy .NET usługi Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (na razie) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Ograniczenia

* Eksport zbiorczy — w przypadku usługi HDInsight opartej na systemie Linux łącznik Sqoop używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database nie obsługuje obecnie wstawiaków zbiorczych.
* Batching — z systemem LINUX HDInsight, Podczas korzystania z `-batch` przełącznika podczas wykonywania wstawia, Sqoop wykonuje wiele wstawia zamiast wsadowego operacji wstawiania.

## <a name="next-steps"></a>Następne kroki

Teraz nauczyłeś się, jak korzystać z Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Korzystanie z programu Apache Hive z usługą HDInsight](../hdinsight-use-hive.md)
* [Przekaż dane do usługi HDInsight](../hdinsight-upload-data.md): Znajdź inne metody przekazywania danych do magazynu obiektów BLOB USŁUGI HDInsight/Azure.
* [Importowanie i eksportowanie danych między narzędziem Apache Hadoop w usłudze HDInsight i usługą SQL Database przy użyciu narzędzia Apache Sqoop](./apache-hadoop-use-sqoop-mac-linux.md)