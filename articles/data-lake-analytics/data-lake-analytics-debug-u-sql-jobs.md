---
title: Debugowanie zdefiniowanych przez użytkownika kodu C#, w przypadku nieudanych zadań usługi Azure Data Lake U-SQL
description: W tym artykule opisano, jak można debugować wierzchołka nie powiodło się U-SQL przy użyciu narzędzi Azure Data Lake Tools for Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 5417f66696191cebadc2af9c6d634419a0eb8e5b
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526456"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Debugowanie zdefiniowanych przez użytkownika kodu C#, w przypadku nieudanych zadań U-SQL

U-SQL zapewnia model rozszerzeń przy użyciu języka C#. Skryptów U-SQL jest łatwe do wywołania funkcji języka C# i wykonują funkcje analityczne, które nie obsługuje języka deklaratywnego podobnego do SQL. Aby dowiedzieć się więcej rozszerzeń U-SQL, zobacz [Podręcznik programowania U-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

W praktyce debugowanie kodu może być konieczne, ale trudno debugować rozproszone zadania z niestandardowym kodem w chmurze za pomocą plików dziennika ograniczone. [Usługa Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) udostępnia funkcję o nazwie **nie można debugować wierzchołka**, która pomaga łatwiej debugowania błędów, które występują w kodzie niestandardowym. W przypadku niepowodzenia zadania U-SQL usługi śledzi stan niepowodzenia i narzędzie ułatwia do pobrania w środowisku błąd chmury na komputer lokalny do debugowania. Pobieranie lokalne przechwytuje całego środowiska chmury, w tym wszystkie dane wejściowe i kod użytkownika.

Poniższy klip wideo pokazuje, nie powiodło się debugowania wierzchołka w usłudze Azure Data Lake Tools for Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Program Visual Studio wymaga dwóch następujących aktualizacji dla tej funkcji: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) i [uniwersalne środowisko uruchomieniowe C dla Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Pobierz wierzchołka nie powiodło się na komputerze lokalnym

Po otwarciu zakończonego niepowodzeniem zadania w usłudze Azure Data Lake Tools dla programu Visual Studio widać żółty pasek alertu z szczegółowe komunikaty o błędach na karcie błąd.

1. Kliknij przycisk **Pobierz** można pobrać wszystkie wymagane zasoby oraz strumieni danych wejściowych. Jeśli pobieranie nie zostało ukończone, kliknij przycisk **ponów**.

2. Kliknij przycisk **Otwórz** po ukończeniu pobierania, aby wygenerować lokalnego środowiska debugowania. Zostanie otwarte nowe rozwiązanie debugowania, a jeśli masz istniejące rozwiązanie otwarte w programie Visual Studio, upewnij się, że Zapisz i zamknij go przed debugowaniem.

![Azure Data Lake Analytics U-SQL debugowania programu visual studio pobierania wierzchołka](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurowanie środowiska debugowania

> [!NOTE]
> Przed debugowaniem, należy koniecznie sprawdzić **wyjątki środowiska uruchomieniowego języka wspólnego** w oknie Ustawienia wyjątków (**Ctrl + Alt + E**).

![Usługa Azure Data Lake Analytics U-SQL debugowania programu visual studio ustawienie](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

W nowych uruchomionego wystąpienia programu Visual Studio może lub nie może odnaleźć zdefiniowanych przez użytkownika języka C# kodu źródłowego:

1. [Mogę znaleźć mój kod źródłowy w rozwiązaniu](#source-code-is-included-in-debugging-solution)

2. [Nie mogę znaleźć mój kod źródłowy w rozwiązaniu](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Kod źródłowy znajduje się podczas debugowania rozwiązania

Istnieją dwa przypadki, że kod źródłowy języka C# są przechwytywane:

1. Kod użytkownika jest zdefiniowana w pliku związanym z kodem (przeważnie zwaną `Script.usql.cs` w projekcie języka U-SQL).

2. Kod użytkownika jest zdefiniowany w języku C# projekt biblioteki klas dla aplikacji w języku U-SQL i zarejestrowana jako zestaw za pomocą **informacje debugowania**.

Jeśli kod źródłowy jest importowany do rozwiązania, można użyć narzędzia debugowania programu Visual Studio (Czujka, zmienne itp.) do rozwiązania problemu:

1. Naciśnij klawisz **F5**, aby uruchomić debugowanie. Kod działa, dopóki nie zostanie zatrzymana przez wyjątek.

2. Otwórz plik kodu źródłowego i ustaw punkty przerwania, naciśnij klawisz **F5** do debugowania kodu krok po kroku.

    ![Usługa Azure Data Lake Analytics U-SQL debugowania wyjątek](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Kod źródłowy jest niedostępna podczas debugowania rozwiązania

Jeśli kod użytkownika nie znajduje się w pliku związanym z kodem lub nie zarejestrowała zestaw za pomocą **informacje debugowania**, kod źródłowy nie jest automatycznie uwzględnione w rozwiązania debugowania. W takim przypadku potrzebne są dodatkowe czynności, aby dodać kod źródłowy:

1. Kliknij prawym przyciskiem myszy **rozwiązania "VertexDebug" > Dodaj > istniejący projekt...**  znaleźć kod źródłowy zestawu i Dodaj projekt do rozwiązania debugowania.

    ![Usługa Azure Data Lake Analytics U-SQL debugowania, Dodaj projekt](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Pobrać ścieżki do folderu projektu do **FailedVertexDebugHost** projektu. 

3. Kliknij prawym przyciskiem myszy **projektu kodu źródłowego dodano zestaw > właściwości**, wybierz opcję **kompilacji** po lewej stronie, a następnie wklej skopiowany ścieżkę, kończąc \bin\debug jako **dane wyjściowe > Ścieżka wyjściowa**. Ścieżka pliku wyjściowego jest jak `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`.

    ![Usługa Azure Data Lake Analytics U-SQL debugowania Ustaw ścieżkę pliku pdb](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Po tych ustawień, rozpocząć debugowanie za pomocą **F5** i punktów przerwania. Można również użyć narzędzia (Czujka, zmienne itp.) do rozwiązywania problemów debugowania programu Visual Studio.

> [!NOTE]
> Ponownie skompiluj projekt kodu źródłowego zestawu każdorazowo po modyfikacji kod, aby wygenerować pliki .pdb zaktualizowane.

## <a name="resubmit-the-job"></a>Prześlij ponownie to zadanie

Jeśli projektu zakończy się pomyślnie po debugowaniu, w oknie danych wyjściowych pokazuje następujący komunikat:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Usługa Azure Data Lake Analytics U-SQL debugowania powiodło się](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Prześlij zadanie zakończone niepowodzeniem:

1. Dla zadań z rozwiązaniami związanymi z kodem, skopiuj kod C# do pliku źródłowego związane z kodem (zazwyczaj `Script.usql.cs`).

2. W przypadku zadań za pomocą zestawów kliknij prawym przyciskiem myszy projekt kodu źródłowego zestawu w rozwiązaniu do debugowania i zarejestruj zestawy zaktualizowany plik .dll w katalogu usługi Azure Data Lake.

3. Ponowne przesyłanie zadania U-SQL.

## <a name="next-steps"></a>Kolejne kroki

- [Podręcznik programowania U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Opracowywanie operatorów zdefiniowanych przez użytkownika języka U-SQL na potrzeby zadań usługi Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testowanie i debugowanie zadań U-SQL przy użyciu uruchamiania lokalnego i zestawu SDK U-SQL usługi Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
- [Jak rozwiązywać problemy z nietypowym zadaniem cyklicznym](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
