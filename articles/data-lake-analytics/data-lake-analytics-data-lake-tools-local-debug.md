---
title: Możliwe jest debugowanie kodu usługi Azure Data Lake Analytics lokalnie
description: Dowiedz się, jak debugowanie zadań U-SQL na lokalnej stacji roboczej przy użyciu usługi Azure Data Lake Tools for Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61472995"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Możliwe jest debugowanie kodu usługi Azure Data Lake Analytics lokalnie

Azure Data Lake Tools for Visual Studio służy do uruchamiania i debugowania kodu Azure Data Lake Analytics na lokalnej stacji roboczej, podobnie jak w usłudze Azure Data Lake Analytics.

Dowiedz się, jak [uruchamianie skryptu U-SQL na maszynie lokalnej](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Debugowanie skryptów i zestawów języka C# lokalnie

Można debugować zestawy języka C# bez przesyłania i rejestrowania ich w usłudze Azure Data Lake Analytics. Możesz ustawić punkty przerwania w pliku związanym z kodem i występującym w odwołaniu projekcie C#.

### <a name="debug-local-code-in-a-code-behind-file"></a>Debugować kod lokalny w pliku związanym z kodem

1. Ustaw punkty przerwania w pliku związanym z kodem.
2. Wybierz **F5** Aby debugować skrypt lokalnie.

> [!NOTE]
   > Poniższa procedura ma zastosowanie tylko w programie Visual Studio 2015. W starszych wersjach programu Visual Studio, może być konieczne ręczne dodawanie **PDB** plików.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Debugować kod lokalny w występującym w odwołaniu projekcie C#

1. Utwórz projekt zestawu języka C# i skompiluj go, aby wygenerować dane wyjściowe **DLL** pliku.
2. Zarejestruj **DLL** plików za pomocą instrukcji U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Ustaw punkty przerwania w kodzie C#.
4. Wybierz **F5** debugowanie skryptu, odwołując się do języka C# **DLL** plik lokalnie.


## <a name="next-steps"></a>Kolejne kroki

- Na przykład bardziej złożonego zapytania zobacz [analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Aby wyświetlić szczegóły zadania, zobacz [korzystanie z przeglądarki zadań i widoku zadań dla zadań usługi Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Aby użyć widoku wykonania wierzchołka, zobacz [korzystanie z widoku wykonania wierzchołka w narzędziach Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
