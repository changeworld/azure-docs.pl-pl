---
title: 'Samouczek fabryki danych: Pierwszy potok danych '
description: Ten samouczek usługi Azure Data Factory pokazuje, jak utworzyć i zaplanować fabrykę danych, która przetwarza dane przy użyciu skryptu hive w klastrze Hadoop.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 80644ed2d655544fa176a7be92aec3c01aa3bf14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966074"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Samouczek: Tworzenie pierwszego potoku w celu przekształcania danych przy użyciu klastra Hadoop
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [Powershell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Szablon Menedżera zasobów](data-factory-build-your-first-pipeline-using-arm.md)
> * [INTERFEJS API ODPOCZYNKU](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Szybki start: tworzenie fabryki danych przy użyciu usługi Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

W tym samouczku skompilujesz pierwszą fabrykę danych platformy Azure za pomocą potoku danych. Potok przekształca dane wejściowe, uruchamiając skrypt hive w klastrze usługi Azure HDInsight (Hadoop) w celu uzyskania danych wyjściowych.  

Ten artykuł zawiera omówienie i wymagania wstępne dla samouczka. Po zakończeniu wymagań wstępnych można wykonać samouczek przy użyciu jednego z następujących narzędzi/zestawu SDK: Visual Studio, PowerShell, szablon Menedżera zasobów, interfejs API REST. Wybierz jedną z opcji na liście rozwijanej na początku (lub) łącza na końcu tego artykułu, aby wykonać samouczek przy użyciu jednej z tych opcji.    

## <a name="tutorial-overview"></a>Omówienie samouczka
Ten samouczek obejmuje wykonanie następujących kroków:

1. Tworzenie **fabryki danych**. Fabryka danych może zawierać jeden lub więcej potoków danych, które przenoszą i przekształcają dane.

    W tym samouczku utworzysz jeden potok w fabryce danych.
2. Utwórz **potok**. Potok może mieć co najmniej jedno działanie (przykłady: Działanie kopiowania, działanie gałęzi hdinsight). W tym przykładzie użyto działania hive hdinsight, który uruchamia skrypt hive w klastrze HDInsight Hadoop. Skrypt najpierw tworzy tabelę, która odwołuje się do nieprzetworzonych danych dziennika sieci web przechowywanych w magazynie obiektów blob platformy Azure, a następnie partycjonuje nieprzetworzone dane według roku i miesiąca.

    W tym samouczku potoku używa działania gałęzi do przekształcania danych przez uruchomienie zapytania hive w klastrze usługi Azure HDInsight Hadoop.
3. Tworzenie **połączonych usług**. Utwórz usługę połączony, aby połączyć magazyn danych lub usługę obliczeniową z fabryką danych. Magazyn danych, takich jak usługa Azure Storage przechowuje dane wejściowe/wyjściowe działań w potoku. Usługa obliczeniowa, taka jak PROCESY KLASTROWE HDInsight Hadoop/przekształca dane.

    W tym samouczku utworzysz dwie połączone usługi: **Azure Storage** i **Azure HDInsight**. Usługa połączona usługi Azure Storage łączy konto usługi Azure Storage, które przechowuje dane wejściowe/wyjściowe do fabryki danych. Usługa połączona usługi Azure HDInsight łączy klaster usługi Azure HDInsight, który jest używany do przekształcania danych w fabryce danych.
3. Tworzenie wejściowych i **wyjściowych zestawów danych**. Zestaw danych wejściowych reprezentuje dane wejściowe dla działania w potoku, a zestaw danych wyjściowych reprezentuje dane wyjściowe dla działania.

    W tym samouczku wejściowe i wyjściowe zestawy danych określają lokalizacje danych wejściowych i wyjściowych w usłudze Azure Blob Storage. Usługa połączona usługi Azure Storage określa, jakie konto usługi Azure Storage jest używane. Wejściowy zestaw danych określa, gdzie znajdują się pliki wejściowe, a wyjściowy zestaw danych określa, gdzie są umieszczane pliki wyjściowe.


Zobacz [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) artykuł szczegółowe omówienie usługi Azure Data Factory.

Oto **widok diagramu** przykładowej fabryki danych, którą tworzysz w tym samouczku. **MyFirstPipeline** ma jedno działanie typu Gałąź, który zużywa **azureblobinput** zestawu danych jako dane wejściowe i produkuje **AzureBlobOutput** zestaw danych jako dane wyjściowe.

![Widok diagramu w samouczku Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


W tym samouczku folder **danych wejściowych kontenera** obiektów blob platformy Azure **adfgetstarted** zawiera jeden plik o nazwie input.log. Ten plik dziennika zawiera wpisy z trzech miesięcy: styczeń, luty i marzec 2016. Oto przykładowe wiersze dla każdego miesiąca w pliku wejściowym.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Gdy plik jest przetwarzany przez potok z hdinsight działania hive, działanie uruchamia skrypt hive w klastrze HDInsight, który partycjonuje dane wejściowe według roku i miesiąca. Skrypt tworzy trzy foldery wyjściowe, które zawierają plik z wpisami z każdego miesiąca.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Z linii przykładowych pokazanych powyżej pierwszy (z 2016-01-01) jest zapisywany w pliku 000000_0 w folderze month=1. Podobnie drugi jest zapisywany w pliku w folderze month=2, a trzeci jest zapisywany w pliku w folderze month=3.  

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka należy mieć następujące wymagania wstępne:

1. **Subskrypcja platformy Azure** — jeśli nie masz subskrypcji platformy Azure, możesz utworzyć bezpłatne konto próbne w ciągu zaledwie kilku minut. Zobacz artykuł [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/) o tym, jak uzyskać bezpłatne konto próbne.
2. **Usługa Azure Storage** — używasz konta magazynu platformy Azure do przechowywania danych w tym samouczku. Jeśli nie masz konta magazynu platformy Azure, zobacz artykuł [Tworzenie konta magazynu.](../../storage/common/storage-account-create.md) Po utworzeniu konta magazynu zanotuj **nazwę konta** i **klucz dostępu**. Aby uzyskać informacje dotyczące pobierania kluczy dostępu do konta magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
3. Pobierz i przejrzyj plik zapytania Hive [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql)**(HQL)** znajdujący się pod adresem: . Ta kwerenda przekształca dane wejściowe do tworzenia danych wyjściowych.
4. Pobierz i przejrzyj przykładowy plik wejściowy **(input.log)** znajdujący się pod adresem:[https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Utwórz kontener obiektów blob o nazwie **adfgetstarted** w usłudze Azure Blob Storage.
6. Przekaż plik **partitionweblogs.hql** do folderu **skryptów** w kontenerze **adfgetstarted.** Użyj narzędzi, takich jak [Microsoft Azure Storage Explorer](https://storageexplorer.com/).
7. Przekaż plik **input.log** do folderu **inputdata** w kontenerze **adfgetstarted.**

Po zakończeniu wymagań wstępnych wybierz jedno z następujących narzędzi/zestawów SDK, aby wykonać samouczek:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Powershell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Szablon Menedżera zasobów](data-factory-build-your-first-pipeline-using-arm.md)
- [INTERFEJS API ODPOCZYNKU](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio udostępnia sposób tworzenia w yjaków danych w postaci graficznego interfejsu użytkownika. Natomiast opcje powershell, resource manager i interfejs API REST zapewniają skrypty/programowanie tworzenia fabryk danych.

> [!NOTE]
> Potok danych przedstawiony w tym samouczku przekształca dane wejściowe w celu wygenerowania danych wyjściowych. Nie kopiuje on danych ze źródłowego do docelowego magazynu danych. Aby zapoznać się z samouczkiem dotyczącym kopiowania danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: Kopiowanie danych z usługi Blob Storage do usługi SQL Database).
>
> Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Szczegółowe informacje znajdują się w artykule [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Planowanie i wykonywanie w usłudze Data Factory).
