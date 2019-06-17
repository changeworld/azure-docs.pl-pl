---
title: 'Samouczek fabryki danych: Pierwszy potok danych | Dokumentacja firmy Microsoft'
description: Ten samouczek usługi Azure Data Factory dowiesz się, jak utworzyć i zaplanować fabryki danych, która przetwarza dane przy użyciu skryptu programu Hive w klastrze platformy Hadoop.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d9d9e68b7e74ba7725e97162d01e1a35314fdd0f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60564601"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Samouczek: Tworzenie pierwszego potoku do przekształcania danych przy użyciu klastra Hadoop
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Program Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [Program PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Szablon usługi Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [Szybki start: tworzenie fabryki danych w usłudze Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

W tym samouczku utworzysz pierwszej fabryki danych platformy Azure przy użyciu potoku danych. Potok przekształca dane wejściowe, uruchamiając skrypt Hive w klastrze usługi Azure HDInsight (Hadoop) w celu wygenerowania danych wyjściowych.  

Ten artykuł zawiera przegląd i wymagania wstępne dla samouczka. Po wykonaniu wymagań wstępnych, można wykonać tego samouczka przy użyciu jednej z następujących narzędzi/zestawów SDK: Witrynę Azure portal, programu Visual Studio, PowerShell i szablonu usługi Resource Manager i interfejsu API REST. Wybierz jedną z opcji na liście rozwijanej na początku (lub) linki na końcu tego artykułu, aby wykonać instrukcje z samouczka przy użyciu jednej z tych opcji.    

## <a name="tutorial-overview"></a>Omówienie samouczka
Ten samouczek obejmuje wykonanie następujących kroków:

1. Tworzenie **usługi data factory**. Fabryka danych może zawierać jeden lub wiele potoków danych służące do przenoszenia i przekształcania danych. 

    W tym samouczku utworzysz jeden potok w fabryce danych. 
2. Tworzenie **potoku**. Potok może obejmować jedno lub więcej działań (przykłady: Działanie kopiowania, działanie HDInsight Hive). Ta próbka używa działanie HDInsight Hive, które uruchamia skrypt Hive w klastrze usługi HDInsight Hadoop. Skrypt najpierw tworzy tabelę, która odwołuje się do danych dziennika raw sieci web, przechowywane w usłudze Azure blob storage, a następnie partycji danych pierwotnych według roku i miesiąca.

    W tym samouczku potok używa działania programu Hive do przekształcania danych, uruchamiając zapytanie programu Hive w klastrze usługi Azure HDInsight Hadoop. 
3. Tworzenie **połączonych usług**. Utworzysz połączoną usługę służącą do połączyć magazyn danych lub usługi obliczeniowe z fabryką danych. Magazyn danych, takich jak Azure Storage przechowuje dane wejściowe i wyjściowe działań w potoku. Usługi obliczeniowe, takie jak klaster usługi HDInsight Hadoop procesów/przekształcenia danych.

    W tym samouczku utworzysz dwie połączone usługi: **Usługa Azure Storage** i **Azure HDInsight**. Usługi Azure Storage połączona usługa łączy konto usługi Azure Storage przechowujący dane wejściowe/wyjściowe, z fabryką danych. Usługa Azure HDInsight połączona usługa łączy klastra usługi Azure HDInsight, która jest używana do przekształcania danych w usłudze data factory. 
3. Utworzenie wejściowych i wyjściowych **zestawów danych**. Zestaw danych wejściowych reprezentuje dane wejściowe dla działania w potoku, a zestaw danych wyjściowych reprezentuje dane wyjściowe dla działania.

    W tym samouczku danych wejściowych i wyjściowych zestawów danych, określ lokalizacje, danych wejściowych i wyjściowych w usłudze Azure Blob Storage. Połączona usługa Azure Storage Określa, co to jest konto usługi Azure Storage używany. Wejściowy zestaw danych określa, gdzie znajdują się pliki wejściowe i wyjściowy zestaw danych określa rozmieszczenie plików wyjściowych. 


Zobacz [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) artykułu szczegółowe omówienie usługi Azure Data Factory.
  
Oto **widok diagramu** fabryki danych przykładowych kompilacji w ramach tego samouczka. **MyFirstPipeline** ma jedno działanie typu Hive, który wykorzystuje **AzureBlobInput** zestawu danych jako dane wejściowe i tworzy **AzureBlobOutput** zestawu danych jako dane wyjściowe. 

![Widok diagramu w samouczku usługi Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


W tym samouczku **inputdata** folderu **adfgetstarted** kontener obiektów blob platformy Azure zawiera jeden plik o nazwie input.log. Ten plik dziennika zawiera wpisy z trzech miesięcy: Stycznia, lutego i marca 2016 r. Poniżej przedstawiono przykładowe wiersze w każdym miesiącu, w pliku wejściowym. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Gdy plik jest przetwarzany przez potok z działaniem usługi HDInsight Hive, działanie uruchamia skrypt Hive w klastrze HDInsight, partycje dane wejściowe według roku i miesiąca. Skrypt tworzy trzy foldery wynikowe, które zawierają plik z wpisy z każdego miesiąca.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Z wierszy przykładowych pokazano powyżej, pierwszy z nich (z 2016-01-01) są zapisywane do pliku 000000_0 w miesiącu = 1 folder. Podobnie, drugi z nich są zapisywane do pliku w miesiącu = 2 folder, a trzeci jednego są zapisywane do pliku w miesiącu = 3 folder.  

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka, musisz mieć następujące wymagania wstępne:

1. **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, można utworzyć bezpłatne konto próbne w zaledwie kilka minut. Zobacz [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/) artykuł, w jaki sposób można uzyskać bezpłatne konto próbne.
2. **Usługa Azure Storage** — Użyj konta usługi Azure storage do przechowywania danych w ramach tego samouczka. Jeśli nie masz konta usługi Azure storage, zobacz [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md) artykułu. Po utworzeniu konta magazynu, zanotuj **nazwa konta** i **klucz dostępu**. Zobacz [wyświetlanie, kopiowanie i ponowne generowanie magazynu klucze dostępu](../../storage/common/storage-account-manage.md#access-keys).
3. Pobierać i przeglądać plik zapytania programu Hive (**HQL**) znajdujący się w: [ https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql ](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). To zapytanie przekształca dane wejściowe w celu wygenerowania danych wyjściowych. 
4. Pobrać i przejrzeć przykładowy plik danych wejściowych (**input.log**) znajdujący się na: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Utwórz kontener obiektów blob o nazwie **adfgetstarted** w usłudze Azure Blob Storage. 
6. Przekaż **partitionweblogs.hql** plik **skryptu** folderu w **adfgetstarted** kontenera. Użyj narzędzi takich jak [Microsoft Azure Storage Explorer](https://storageexplorer.com/). 
7. Przekaż **input.log** plik **inputdata** folderu w **adfgetstarted** kontenera. 

Po wykonaniu wymagania wstępne, wybierz jedną z następujących narzędzi/zestawów SDK celu tego samouczka: 

- [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Program Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Program PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Szablon usługi Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Witryna Azure portal i programu Visual Studio umożliwiają graficznego interfejsu użytkownika tworzenia fabryk danych. Natomiast opcji programu PowerShell, szablon usługi Resource Manager i interfejsu API REST umożliwia wykonywanie skryptów programowania tworzenia fabryk danych.

> [!NOTE]
> Potok danych przedstawiony w tym samouczku przekształca dane wejściowe w celu wygenerowania danych wyjściowych. Nie kopiuje on danych ze źródłowego do docelowego magazynu danych. Aby zapoznać się z samouczkiem dotyczącym kopiowania danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: kopiowanie danych z usługi Blob Storage do usługi SQL Database).
> 
> Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Szczegółowe informacje znajdują się w artykule [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Planowanie i wykonywanie w usłudze Data Factory). 





  
