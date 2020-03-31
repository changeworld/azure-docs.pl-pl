---
title: Debugowanie kodu usługi Azure Data Lake Analytics lokalnie
description: Dowiedz się, jak używać narzędzi usługi Azure Data Lake Tools for Visual Studio do debugowania zadań U-SQL na lokalnej stacji roboczej.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61472995"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Debugowanie kodu usługi Azure Data Lake Analytics lokalnie

Narzędzia usługi Azure Data Lake Tools dla programu Visual Studio umożliwiają uruchamianie i debugowanie kodu usługi Azure Data Lake Analytics na lokalnej stacji roboczej, tak samo jak w usłudze Azure Data Lake Analytics.

Dowiedz się, jak [uruchomić skrypt U-SQL na komputerze lokalnym.](data-lake-analytics-data-lake-tools-local-run.md)

## <a name="debug-scripts-and-c-assemblies-locally"></a>Debugowanie skryptów i zestawów języka C# lokalnie

Można debugować zestawy C# bez przesyłania i rejestrowania ich do usługi Azure Data Lake Analytics. Punkty przerwania można ustawić zarówno w pliku związanym z kodem, jak i w projekcie, do którego istnieje odwołanie.

### <a name="debug-local-code-in-a-code-behind-file"></a>Debugowanie kodu lokalnego w pliku związanym z kodem

1. Ustaw punkty przerwania w pliku związanym z kodem.
2. Wybierz **klawisz F5,** aby debugować skrypt lokalnie.

> [!NOTE]
   > Poniższa procedura działa tylko w programie Visual Studio 2015. W starszych wersjach programu Visual Studio może być konieczne ręczne dodanie plików **PDB.**  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Debugowanie kodu lokalnego w projekcie w języku C# przywołytym do wiadomości

1. Utwórz projekt zestawu języka C# i skompiluj go do wygenerowania wyjściowego pliku **DLL.**
2. Zarejestruj plik **DLL** przy użyciu instrukcji U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Ustaw punkty przerwania w kodzie C#.
4. Wybierz **klawisz F5,** aby debugować skrypt, odwołując się lokalnie do pliku **DLL** języka C#.


## <a name="next-steps"></a>Następne kroki

- Na przykład bardziej złożonej kwerendy zobacz [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Aby wyświetlić szczegóły zadania, zobacz [Korzystanie z funkcji Przeglądarka zadań i Widok zadań dla usługi Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Aby użyć widoku wykonywania wierzchołków, zobacz [Użyj widoku wykonania wierzchołka w narzędziach usługi Data Lake dla programu Visual Studio.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
