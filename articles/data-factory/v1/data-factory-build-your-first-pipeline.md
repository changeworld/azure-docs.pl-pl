---
title: 'Data Factory samouczek: pierwszy potok danych | Dokumentacja firmy Microsoft'
description: W tym samouczku fabryki danych Azure przedstawiono sposób tworzenia i planowania fabryki danych, który przetwarza dane za pomocą skryptu Hive na klastra usługi Hadoop.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 951756d57441d175ccf8bab44bf00c3cb542f1b9
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050050"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Samouczek: Tworzenie swój pierwszy potok do przekształcania danych przy użyciu klastra usługi Hadoop
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Program Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [Program PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Szablon usługi Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [Szybki Start: tworzenie fabryki danych przy użyciu fabryki danych Azure](../quickstart-create-data-factory-dot-net.md).

W tym samouczku należy utworzyć pierwszy fabrykę danych Azure z potokiem danych. Potok przekształca danych wejściowych przez uruchomienie skryptu Hive w usłudze Azure HDInsight (Hadoop) klastrze wygenerowało danych wyjściowych.  

Ten artykuł zawiera omówienie i wymagania wstępne dotyczące samouczka. Po ukończeniu wymagań wstępnych, możesz zrobić samouczek przy użyciu jednej z następujących narzędzi/zestawów SDK: portal Azure, programu Visual Studio, programu PowerShell, szablon usługi Resource Manager, interfejsu API REST. Wybierz jedną z opcji na liście rozwijanej na początku (lub) łącza na końcu tego artykułu, w celu tego samouczka przy użyciu jednej z tych opcji.    

## <a name="tutorial-overview"></a>Omówienie samouczka
Ten samouczek obejmuje wykonanie następujących kroków:

1. Utwórz **fabryki danych**. Fabryka danych może zawierać potoki danych, które Przenieś i przekształcania danych. 

    W tym samouczku utworzysz jeden potok w fabryce danych. 
2. Utwórz **potoku**. Potok może mieć co najmniej jedno działanie (przykłady: działanie kopiowania, działania Hive HDInsight). W przykładzie użyto działania HDInsight Hive, które uruchamia skrypt Hive w klastrze usługi HDInsight Hadoop. Skrypt najpierw tworzy tabelę, która odwołuje się do danych dziennika raw sieci web przechowywany w magazynie obiektów blob platformy Azure, a następnie partycje nieprzetworzone dane przez rok i miesiąc.

    W tym samouczku potoku używa działania gałęzi do przekształcania danych przez uruchomienie zapytania programu Hive w klastrze usługi Azure HDInsight Hadoop. 
3. Utwórz **połączone usługi**. Można utworzyć połączonej usługi, aby połączyć magazyn danych lub usługi obliczeniowe z fabryką danych. Magazyn danych, takie jak magazyn Azure przechowuje dane wejścia/wyjścia działań w potoku. Usługi obliczeniowe, takich jak klaster usługi HDInsight Hadoop procesów/przekształcenia danych.

    W tym samouczku, Utwórz dwie połączonej usługi: **usługi Azure Storage** i **Azure HDInsight**. Magazyn Azure połączone usługi łączy konto magazynu Azure, która przechowuje dane We/Wy z fabryką danych. Usługa Azure HDInsight połączone łącza usługi klastra usługi Azure HDInsight, który jest używany do transformacji danych z fabryką danych. 
3. Utwórz dane wejściowe i wyjściowe **zestawów danych**. Zestaw danych wejściowych reprezentuje dane wejściowe dla działania w potoku, a zestaw danych wyjściowych reprezentuje dane wyjściowe dla działania.

    W tym samouczku wejściowe i wyjściowe zestawy danych Określ lokalizacje danych wejściowych i wyjściowych w magazynie obiektów Blob Azure. Określa połączoną usługą magazynu Azure, co konto magazynu Azure jest używana. Wejściowy zestaw danych określa, gdzie znajdują się pliki wejściowy i wyjściowy zestaw danych określa, gdzie są umieszczone pliki wyjściowe. 


Zobacz [wprowadzenie do fabryki danych Azure](data-factory-introduction.md) artykułu szczegółowe omówienie fabryki danych Azure.
  
Oto **widok diagramu** fabryki danych przykładowych kompilacji w tym samouczku. **MyFirstPipeline** ma jedno działanie typu Hive, który wykorzystuje **AzureBlobInput** zestawu danych jako dane wejściowe i tworzy **AzureBlobOutput** zestawu danych jako dane wyjściowe. 

![Widok diagramu w samouczku fabryki danych](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


W tym samouczku **inputdata** folderu **adfgetstarted** kontenera obiektów blob platformy Azure zawiera jeden plik o nazwie input.log. Ten plik dziennika zawiera wpisy z trzech miesięcy: stycznia, lutego i marca 2016 r. Poniżej przedstawiono przykładowe wiersze dla każdego miesiąca w pliku wejściowym. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Gdy plik jest przetwarzany przez potok wraz z działania Hive HDInsight, działanie uruchamia skrypt Hive w klastrze usługi HDInsight że partycje wprowadzanie danych przez rok i miesiąc. Skrypt tworzy trzech plików zawierających plik z wpisów z każdego miesiąca.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Z wierszy przykładowych pokazano powyżej, pierwsza z nich (z 2016-01-01) jest zapisywany do pliku 000000_0 w miesiącu = 1 folder. Podobnie, drugi są zapisywane do pliku w miesiącu = 2 folderu, a trzeci jedną są zapisywane do pliku w miesiącu = 3 folder.  

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka wymagane są następujące wymagania wstępne:

1. **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Zobacz [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) artykuł, w jaki sposób można uzyskać bezpłatne konto próbne.
2. **Usługa Azure Storage** — używasz konta magazynu Azure do przechowywania danych w tym samouczku. Jeśli nie masz konta magazynu platformy Azure, zobacz [Utwórz konto magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account) artykułu. Po utworzeniu konta magazynu należy zanotować **nazwa konta** i **klucz dostępu**. Zobacz [widoku, kopiowania i regenerate magazynu klucze dostępu](../../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).
3. Pobierz i przejrzyj plik zapytania Hive (**HQL**) w lokalizacji: [ https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql ](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). To zapytanie przy użyciu danych wejściowych, które wygenerowało danych wyjściowych. 
4. Pobierz i przejrzyj przykładowy plik wejściowy (**input.log**) w lokalizacji: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Tworzenie kontenera obiektów blob o nazwie **adfgetstarted** w magazynie obiektów Blob Azure. 
6. Przekaż **partitionweblogs.hql** pliku **skryptu** folderu w **adfgetstarted** kontenera. Użyj narzędzia takiego jak [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com/). 
7. Przekaż **input.log** pliku **inputdata** folderu w **adfgetstarted** kontenera. 

Po ukończeniu wymagania wstępne, wybierz jedną z następujących narzędzi/zestawów SDK do samouczka: 

- [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Program Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Program PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Szablon usługi Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Portalu Azure i programu Visual Studio umożliwiają graficznego interfejsu użytkownika w budynku z fabryki danych. Natomiast opcje środowiska PowerShell, szablon usługi Resource Manager i interfejsu API REST zawiera skryptów programowania sposób tworzenia z fabryki danych.

> [!NOTE]
> Potok danych przedstawiony w tym samouczku przekształca dane wejściowe w celu wygenerowania danych wyjściowych. Nie kopiuje on danych ze źródłowego do docelowego magazynu danych. Aby zapoznać się z samouczkiem dotyczącym kopiowania danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: Kopiowanie danych z usługi Blob Storage do usługi SQL Database).
> 
> Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Szczegółowe informacje znajdują się w artykule [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Planowanie i wykonywanie w usłudze Data Factory). 





  
