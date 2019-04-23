---
title: Zapis zaawansowane funkcje języka R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dowiedz się, jak napisać funkcję języka R do zaawansowanych obliczeń statystycznych w usłudze Azure SQL Database przy użyciu usług Machine Learning (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014785"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Zapisywanie zaawansowane funkcje języka R w usłudze Azure SQL Database przy użyciu usług Machine Learning (wersja zapoznawcza)

W tym artykule opisano sposób osadzania R matematyczne i procedury składowanej funkcje narzędziowe w języku SQL. Zaawansowane funkcje statystyczne, są skomplikowane implementacji języka T-SQL, które można zrobić w języku R z jednego wiersza kodu.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

- Aby uruchomić przykładowy kod w tych ćwiczeń, najpierw musisz mieć usługi Azure SQL database przy użyciu usług Machine Learning (przy użyciu języka R) włączone. W okresie publicznej wersji zapoznawczej firma Microsoft dołączy Cię i włączy usługę Machine Learning dla Twojej istniejącej lub nowej bazy danych. Postępuj zgodnie z instrukcjami w części [Tworzenie konta na potrzeby korzystania z wersji zapoznawczej](sql-database-machine-learning-services-overview.md#signup).

- Upewnij się, że została zainstalowana najnowsza wersja [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Można uruchomić skrypty języka R przy użyciu innych Zarządzanie bazą danych lub narzędzi do obsługi zapytań, ale w tym przewodniku Szybki Start użyjesz programu SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Utwórz procedurę przechowywaną, aby generować liczby losowe

Dla uproszczenia Użyjmy R `stats` pakiet, który został zainstalowany i domyślnie jest ładowany z usługi Azure SQL Database przy użyciu usług Machine Learning (wersja zapoznawcza). Pakiet zawiera setki funkcje dla typowych zadań statystycznych, między nimi `rnorm` funkcji. Ta funkcja generuje liczby losowe przy użyciu rozkładu normalnego, rozpoczynając od podanej odchylenie standardowe oznacza określoną liczbę.

Na przykład poniższy kod R zwraca liczby 100 na mean 50, biorąc pod uwagę odchylenie standardowe w 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Aby wywołać ten wiersz języka R z języka T-SQL, należy uruchomić `sp_execute_external_script` i Dodaj funkcję języka R w parametrze skrypt języka R następująco:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Co zrobić, jeśli chcesz ułatwić Generowanie inny zbiór liczb losowych?

To łatwe w połączeniu z językiem SQL. Należy zdefiniować procedury przechowywanej, która pobiera argumenty od użytkownika, a następnie przekazać te argumenty do skryptu języka R jako zmienne.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- Pierwszy wiersz definiuje każdego z parametrów wejściowych SQL, które są wymagane podczas wykonywania procedury składowanej.

- Wiersz zaczynający się od `@params` definiuje wszystkie zmienne używane przez kod R i odpowiadające im typy danych SQL.

- Wiersze, które następują bezpośrednio po mapowania nazw parametrów SQL do odpowiedniej nazwy zmiennych R.

Teraz, gdy funkcja R zostały opakowane w procedurze składowanej, można łatwo wywołanie funkcji i przekazać różne wartości w następujący sposób:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Korzystanie z rozwiązań R funkcji narzędzia do rozwiązywania problemów

`utils` Zainstalowany domyślny pakiet oferuje różne funkcje narzędziowe badania bieżące środowisko R. Te funkcje mogą być przydatne, jeśli niezgodności w taki sposób, który wykonuje kodu języka R, SQL i w środowiskach poza programem. Na przykład, może użyć języka R `memory.limit()` funkcję, aby pobrać pamięci dla bieżącego środowiska języka R.

Ponieważ `utils` pakiet jest zainstalowany, ale nie załadowano domyślnie, należy użyć `library()` funkcji najpierw go załadować.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Wielu użytkowników, takich jak używać funkcji czasu systemu w języku R, takich jak `system.time` i `proc.time`czas wykorzystywane przez procesy R przechwytywania i analizowania problemów z wydajnością.
