---
title: Możliwe jest debugowanie kodu usługi Azure Data Lake Analytics lokalnie | Dokumentacja firmy Microsoft
description: Dowiedz się, jak debugowanie zadań U-SQL na lokalnej stacji roboczej przy użyciu usługi Azure Data Lake Tools for Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889593"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Możliwe jest debugowanie kodu usługi Azure Data Lake Analytics lokalnie

Azure Data Lake Tools for Visual Studio służy do uruchamiania i debugowania kodu Azure Data Lake Analytics na stacji roboczej, podobnie jak w usłudze Azure Data Lake.

Dowiedz się, [sposobu uruchamiania skryptu U-SQL na maszynie lokalnej](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Debugowanie skryptów i zestawów języka C# lokalnie

Można debugować zestawy języka C# bez przesyłania i rejestrowania ich, aby usługa Azure Data Lake Analytics. Można ustawić punkty przerwania w kodzie pliku oraz w projekcie języka C#, do którego się odwołujesz.

### <a name="to-debug-local-code-in-code-behind-file"></a>Aby debugować kod lokalny w kodzie pliku

1. Ustaw punkty przerwania w kodzie pliku.
2. Naciśnij klawisz F5, aby debugować skrypt lokalnie.

> [!NOTE]
   > Poniższa procedura dotyczy tylko programu Visual Studio 2015. W starszych wersjach programu Visual Studio może być konieczne ręczne dodanie plików pdb.  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Aby debugować kod lokalny w występującym w odwołaniu projekcie C#

1. Utwórz projekt zestawu języka C# i skompiluj go, aby wygenerować wyjściowy plik dll.
2. Zarejestruj plik dll za pomocą instrukcji U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Ustaw punkty przerwania w kodzie C#.
4. Naciśnij klawisz F5, aby debugować skrypt z odwołujące się do języka C# biblioteki dll lokalnie.


## <a name="next-steps"></a>Kolejne kroki

- Aby wyświetlić bardziej złożonego zapytania, zobacz [analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Aby wyświetlić szczegóły zadania, zobacz [korzystanie z przeglądarki zadań i widoku zadań dla zadań usługi Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Aby użyć widoku wykonania wierzchołka, zobacz [korzystanie z widoku wykonania wierzchołka w narzędziach Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
