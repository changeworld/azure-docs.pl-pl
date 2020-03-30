---
title: Kod debugowania języka C# dla zadań U-SQL usługi Azure Data Lake
description: W tym artykule opisano sposób debugowania wierzchołka U-SQL nie powiodło się przy użyciu narzędzia usługi Azure Data Lake dla programu Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315816"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Debugowanie kodu C# zdefiniowanego przez użytkownika dla nieudanych zadań U-SQL

U-SQL zapewnia model rozszerzalności przy użyciu języka C#. W skryptach U-SQL jest łatwe do wywołania funkcji Języka C# i wykonywania funkcji analitycznych, które sql-jak język deklaratywny nie obsługuje. Aby dowiedzieć się więcej na temat rozszerzalności języka U-SQL, zobacz [Przewodnik po programowalności języka U-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

W praktyce każdy kod może wymagać debugowania, ale trudno jest debugować zadanie rozproszone z kodem niestandardowym w chmurze z ograniczonymi plikami dziennika. [Narzędzia usługi Azure Data Lake tools dla programu Visual Studio](https://aka.ms/adltoolsvs) udostępnia funkcję o nazwie **Failed Vertex Debug**, która ułatwia debugowanie błędów występujących w kodzie niestandardowym. Gdy zadanie U-SQL nie powiedzie się, usługa zachowuje stan awarii, a narzędzie pomaga pobrać środowisko awarii chmury do komputera lokalnego do debugowania. Pobieranie lokalne przechwytuje całe środowisko chmury, w tym wszelkie dane wejściowe i kod użytkownika.

W poniższym klipie wideo przedstawiono nie powodzenie debugowania wierzchołka w narzędziach usługi Azure Data Lake Tools dla programu Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Program Visual Studio wymaga następujących dwóch aktualizacji do korzystania z tej funkcji: [Microsoft Visual C++ 2015 Redystrybucjowa aktualizacja 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) i [uniwersalny czas pracy C dla systemu Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Pobieranie nieudanego wierzchołka do komputera lokalnego

Po otwarciu zadania nie powiodło się w usłudze Azure Data Lake Tools dla programu Visual Studio, zobaczysz żółty pasek alertów ze szczegółowymi komunikatami o błędach na karcie błędów.

1. Kliknij **przycisk Pobierz,** aby pobrać wszystkie wymagane zasoby i strumienie wejściowe. Jeśli pobieranie nie zostanie zakończone, kliknij przycisk **Ponów próbę**.

2. Kliknij **przycisk Otwórz** po zakończeniu pobierania, aby wygenerować lokalne środowisko debugowania. Zostanie otwarte nowe rozwiązanie do debugowania, a jeśli w programie Visual Studio zostanie otwarte istniejące rozwiązanie, należy je zapisać i zamknąć przed debugowaniem.

![Usługa Azure Data Lake Analytics U-SQL debug visual studio download wierzchołek](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurowanie środowiska debugowania

> [!NOTE]
> Przed debugowaniem należy zaznaczyć **wyjątki środowiska uruchomieniowego języka wspólnego** w oknie Ustawienia wyjątków (**Ctrl + Alt + E**).

![Ustawienie programu visual studio debugowania usługi Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

W nowym wystąpieniu uruchomionego programu Visual Studio może lub nie może znaleźć zdefiniowany przez użytkownika kod źródłowy języka C#:

1. [Kod źródłowy można znaleźć w rozwiązaniu](#source-code-is-included-in-debugging-solution)

2. [Nie mogę znaleźć kodu źródłowego w rozwiązaniu](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Kod źródłowy znajduje się w rozwiązaniu debugowania

Istnieją dwa przypadki, że kod źródłowy języka C# jest przechwytywany:

1. Kod użytkownika jest zdefiniowany w pliku związanym z kodem (zazwyczaj nazwany `Script.usql.cs` w projekcie U-SQL).

2. Kod użytkownika jest zdefiniowany w projekcie biblioteki klas C# dla aplikacji U-SQL i zarejestrowany jako zestaw z **informacjami debugowania**.

Jeśli kod źródłowy jest importowany do rozwiązania, można użyć narzędzi debugowania programu Visual Studio (zegarek, zmienne itp.), aby rozwiązać problem:

1. Naciśnij klawisz **F5**, aby uruchomić debugowanie. Kod jest uruchamiany, dopóki nie zostanie zatrzymany przez wyjątek.

2. Otwórz plik kodu źródłowego i ustaw punkty przerwania, a następnie naciśnij **klawisz F5,** aby debugować kod krok po kroku.

    ![Wyjątek debugowania usługi Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Kod źródłowy nie jest uwzględniony w rozwiązaniu debugowania

Jeśli kod użytkownika nie jest uwzględniony w pliku związanym z kodem lub nie zarejestrowano zestawu z **informacjami debugowania,** kod źródłowy nie jest automatycznie uwzględniany w rozwiązaniu debugowania. W takim przypadku należy wykonać dodatkowe kroki, aby dodać kod źródłowy:

1. Kliknij prawym przyciskiem myszy **rozwiązanie "VertexDebug" > Dodaj > istniejącego projektu...** aby znaleźć kod źródłowy zestawu i dodać projekt do rozwiązania debugowania.

    ![Dodawanie projektu dodawania debugowania usługi Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Pobierz ścieżkę folderu projektu dla projektu **FailedVertexDebugHost.** 

3. Kliknij prawym przyciskiem myszy **dodany projekt kodu źródłowego zestawu > właściwości**, zaznacz kartę **Kompilacja** po lewej stronie i wklej skopiowaną ścieżkę kończącą się \bin\debug jako **ścieżka wyjście > wyjście**. Końcowa ścieżka wyjściowa jest jak `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`.

    ![Ścieżka debugowania usługi PDB usługi Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Po tych ustawieniach rozpocznij debugowanie za pomocą **klawisza F5** i punktów przerwania. Można również użyć narzędzi debugowania programu Visual Studio (zegarek, zmienne itp.), aby rozwiązać problem.

> [!NOTE]
> Odbuduj projekt kodu źródłowego zestawu za każdym razem po zmodyfikowaniu kodu w celu wygenerowania zaktualizowanych plików pdb.

## <a name="resubmit-the-job"></a>Ponownie złożyć zadanie

Po debugowaniu, jeśli projekt zakończy się pomyślnie, okno danych wyjściowych zawiera następujący komunikat:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Debugowanie U-SQL usługi Azure Data Lake Analytics powiodło się](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Aby ponownie przesłać zadanie, które nie powiodło się:

1. W przypadku zadań z rozwiązaniami związanymi z kodem należy skopiować `Script.usql.cs`kod C# do pliku źródłowego zawierającego kod (zazwyczaj).

2. W przypadku zadań z zestawami kliknij prawym przyciskiem myszy projekt kodu źródłowego zestawu w rozwiązaniu debugowania i zarejestruj zaktualizowane zestawy dll w wykazie usługi Azure Data Lake.

3. Ponownie prześlij zadanie U-SQL.

## <a name="next-steps"></a>Następne kroki

- [Przewodnik programowania U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Tworzenie operatorów zdefiniowanych przez użytkownika u-SQL dla zadań usługi Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testowanie i debugowanie zadań U-SQL przy użyciu uruchamiania lokalnego i zestawu SDK U-SQL usługi Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
- [Jak rozwiązać nieprawidłowe zadanie cykliczne](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
