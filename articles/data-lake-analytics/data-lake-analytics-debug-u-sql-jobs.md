---
title: Debugowanie zdefiniowane przez użytkownika kodu C#, zadań usługi Azure Data Lake U-SQL nie powiodło się
description: W tym artykule opisano sposób debugowania wierzchołek nie powiodło się U-SQL przy użyciu usługi Azure Data Lake Tools dla programu Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
manager: kfile
editor: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: d8926f0defdfcc0e045ae87de6fd82755a5a35eb
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736851"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Debugowanie zdefiniowane przez użytkownika kodu C#, dla zadań U-SQL nie powiodło się

U-SQL zapewnia modelu rozszerzalności przy użyciu języka C#. W skryptów U-SQL jest łatwe wywoływać funkcje C# i wykonywać funkcje analityczne, które nie obsługują deklaratywne języka przypominającego SQL. Aby dowiedzieć się więcej na wprowadzenie U-SQL, zobacz [Podręcznik programowania U-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

W praktyce debugowanie kodu może być konieczne, ale jest trudne do debugowania rozproszonej zadania z kodu niestandardowego w chmurze z plików dziennika ograniczone. [Azure Data Lake Tools dla programu Visual Studio](http://aka.ms/adltoolsvs) udostępnia funkcję **nie można debugować wierzchołków**, co pomaga łatwiej debugowania błędów, które występują w niestandardowym kodem. Gdy zadanie U-SQL nie powiodło się, usługa śledzi stan niepowodzenia i narzędzie pomaga Pobierz środowisko chmury błąd na komputerze lokalnym do debugowania. Pobieranie lokalne przechwytuje środowiska chmury całego, w tym wszystkie dane wejściowe i kod użytkownika.

Poniżej film wideo przedstawia nie powiodło się wierzchołków debugowania w Azure Data Lake Tools dla programu Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Program Visual Studio wymaga następujących dwóch aktualizacji dla tej funkcji: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) i [Universal C środowiska uruchomieniowego systemu Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Pobieranie nie powiodło się wierzchołków do komputera lokalnego

Po otwarciu zadanie zakończone niepowodzeniem w Azure Data Lake Tools dla programu Visual Studio jest wyświetlany żółty pasek alertów o szczegółowe komunikaty o błędach na karcie błąd.

1. Kliknij przycisk **Pobierz** do pobrania wszystkich wymaganych zasobów i strumienie wejściowe. Pobieranie nie zostało ukończone, kliknij przycisk **ponów**.

2. Kliknij przycisk **Otwórz** po zakończeniu pobierania do generowania środowisku debugowania lokalnego. Nowe rozwiązanie debugowania zostanie otwarty, a jeśli masz istniejące rozwiązania otwarty w programie Visual Studio, sprawdź upewnij się, że Zapisz i zamknij je przed dokonaniem debugowania.

![Azure Data Lake Analytics U-SQL debugowania programu visual studio pobierania wierzchołków](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Skonfiguruj środowisko debugowania

> [!NOTE]
> Przed debugowania, należy sprawdzić **wspólnego języka środowiska uruchomieniowego wyjątki** w oknie Ustawienia wyjątków (**Ctrl + Alt + E**).

![Ustawienia programu visual studio debugowania w usłudze Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

W tym nowym uruchomionemu wystąpieniu programu Visual Studio może lub nie może znaleźć użytkownika C# kodu źródłowego:

1. [W rozwiązaniu można znaleźć mojego kodu źródłowego](#source-code-is-included-in-debugging-solution)

2. [Nie można znaleźć mojego kodu źródłowego w rozwiązaniu](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Kod źródłowy jest uwzględniona w debugowanie rozwiązania

Istnieją dwa przypadki przechwytywania kodu źródłowego C#:

1. Kod użytkownika jest zdefiniowana w pliku CodeBehind (zwykle o nazwie `Script.usql.cs` projektu U-SQL).

2. Kod użytkownika jest zdefiniowany w języku C# projektu biblioteki klas dla aplikacji U-SQL i zarejestrowany jako zestaw o **informacje debugowania**.

Jeśli kod źródłowy jest importowany do rozwiązania, można użyć narzędzia debugowania programu Visual Studio (czujki, zmienne, itp.) do rozwiązania problemu:

1. Naciśnij klawisz **F5** można rozpocząć debugowania. Kod jest uruchomiony, dopóki nie zostanie zatrzymana przez wyjątek.

2. Otwórz plik kodu źródłowego i ustaw punkty przerwania, naciśnij klawisz **F5** do debugowania kodu krok po kroku.

    ![Wyjątek debugowania w usłudze Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Kod źródłowy nie znajduje się w debugowaniu rozwiązania

Jeśli kod użytkownika nie jest uwzględniona w pliku CodeBehind lub nie może zarejestrować zestaw z **informacje debugowania**, kod źródłowy nie jest automatycznie uwzględnione w rozwiązaniu do debugowania. W takim przypadku potrzebne dodatkowe kroki, aby dodać kod źródłowy:

1. Kliknij prawym przyciskiem myszy **rozwiązania "VertexDebug" > Dodaj > istniejący projekt...**  można znaleźć zestawu kodu źródłowego i dodać projektu do rozwiązania debugowania.

    ![Dodawanie usługi Azure Data Lake Analytics U-SQL debugowania projektu](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Pobierania ścieżki folderu projektu dla **FailedVertexDebugHost** projektu. 

3. Kliknij prawym przyciskiem myszy **projektu kodu źródłowego dodano zestawu > właściwości**, wybierz pozycję **kompilacji** po lewej stronie, a następnie wklej skopiowany ścieżkę kończąc \bin\debug jako **wyjściowy > Ścieżka wyjściowa**. Ścieżka pliku wyjściowego jest takie jak "<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\".

    ![Ścieżka pliku pdb zestawu Azure Data Lake Analytics U-SQL debugowania](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Po tych ustawień, należy rozpocząć debugowanie przy użyciu **F5** i punktów przerwania. Umożliwia także Visual Studio debugowanie narzędzia (czujki, zmienne, itp.) do rozwiązania problemu.

> [!NOTE]
> Odbuduj projekt kodu źródłowego zestawu każdorazowo po modyfikacji kod służący do generowania plików PDB zaktualizowane.

## <a name="resubmit-the-job"></a>Prześlij ponownie zadania

Po debugowaniu, jeśli projekt zakończy się pomyślnie w oknie danych wyjściowych zawiera następujący komunikat:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL debugowania powiodło się](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Prześlij zadanie z błędem:

1. W przypadku zadań z rozwiązaniami związane z kodem, skopiuj kod C# do pliku źródła kodu powiązanego (zazwyczaj `Script.usql.cs`).

2. Dla zadań z zestawów kliknij prawym przyciskiem myszy projekt kodu źródłowego zestawu w rozwiązaniu debugowania i zarejestruj zestawy zaktualizowanych .dll w katalogu usługi Azure Data Lake.

3. Prześlij ponownie zadania skryptu U-SQL.

## <a name="next-steps"></a>Kolejne kroki

- [Podręcznik programowania U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Opracowywanie operatorów zdefiniowanych przez użytkownika U-SQL do zadania usługi Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testowanie i debugowanie zadań U-SQL przy użyciu uruchamiania lokalnego i zestawu SDK U-SQL usługi Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
- [Jak rozwiązywać problemy z nietypowe cyklicznych zadań](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
