---
title: Wykonaj zadania oczyszczania bazy danych za pomocą usługi Azure Functions | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Functions, aby zaplanować zadanie, który nawiązuje połączenie z usługi Azure SQL Database, aby okresowo oczyszczać wierszy.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: glenga
ms.openlocfilehash: 4ec2e9b931e6405aca5b4237bc044647af3b8bb3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120674"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Łączenie z bazą danych SQL Azure za pomocą usługi Azure Functions

W tym artykule pokazano, jak utworzyć zaplanowane zadanie, które łączy się z wystąpieniem usługi Azure SQL Database za pomocą usługi Azure Functions. Kod funkcji czyści wierszy w tabeli w bazie danych. Nowy C# funkcji jest tworzony na podstawie szablonu wyzwalacza czasomierza wstępnie zdefiniowanych w programie Visual Studio 2017. Aby zapewnić obsługę tego scenariusza, należy ustawić parametry połączenia bazy danych jako ustawienia aplikacji w aplikacji funkcji. W tym scenariuszu operacja zbiorcza w bazie danych. 

Jeśli jest to Twój pierwszy kontakt, Praca z C# funkcji, należy przeczytać [usługi Azure Functions C# dokumentacja dla deweloperów](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj kroki opisane w artykule [tworzenie pierwszej funkcji przy użyciu programu Visual Studio](functions-create-your-first-function-visual-studio.md) umożliwiające utworzenie aplikacji funkcji lokalnej, który jest przeznaczony dla środowiska uruchomieniowego w wersji 2.x. Należy również opublikowaniu projektu w aplikacji funkcji na platformie Azure.

+ W tym artykule przedstawiono polecenie języka Transact-SQL, które wykonuje operację czyszczenia zbiorczego w **SalesOrderHeader** tabeli w bazie danych AdventureWorksLT. Aby utworzyć przykładowej bazy danych AdventureWorksLT, wykonaj kroki opisane w artykule [utworzyć bazę danych Azure SQL database w witrynie Azure portal](../sql-database/sql-database-get-started-portal.md).

+ Należy dodać [reguły zapory na poziomie serwera](../sql-database/sql-database-get-started-portal-firewall.md) za publiczny adres IP komputera, na potrzeby tego przewodnika Szybki Start używasz. Ta reguła jest wymagana do można uzyskać dostęp do wystąpienia SQL database z komputera lokalnego.  

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Musisz pobrać parametry połączenia dla bazy danych utworzonej po zakończeniu [utworzyć bazę danych Azure SQL database w witrynie Azure portal](../sql-database/sql-database-get-started-portal.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz **baz danych SQL** z menu po lewej stronie i wybierz swoją bazę danych na **baz danych SQL** strony.

1. Wybierz **parametry połączenia** w obszarze **ustawienia** i skopiować pełną **ADO.NET** parametry połączenia.

    ![Skopiuj parametry połączenia ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Ustawianie parametrów połączenia

Aplikacja funkcji obsługuje wykonywanie funkcji na platformie Azure. Najlepszym rozwiązaniem bezpieczeństwa przechowywać parametry połączenia i inne wpisy tajne w ustawieniach aplikacji funkcji. Używanie ustawień aplikacji uniemożliwia przypadkowego ujawnienia parametry połączenia w kodzie. Możesz uzyskać dostęp ustawienia aplikacji dla aplikacji funkcji bezpośrednio w programie Visual Studio.

Użytkownik musi wcześniej opublikowaniu aplikacji na platformie Azure. Jeśli użytkownik jeszcze tego nie zrobiono, [opublikuj swoją aplikację funkcji na platformie Azure](functions-develop-vs.md#publish-to-azure).

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji funkcji, a następnie wybierz **Publikuj** > **Zarządzanie ustawieniami aplikacji...** . Wybierz **Dodaj ustawienie**w **Nowa nazwa ustawienia aplikacji**, typ `sqldb_connection`i wybierz **OK**.

    ![Ustawienia aplikacji dla aplikacji funkcji.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. W nowym **sqldb_connection** ustawienie, Wklej parametry połączenia skopiowaną w poprzedniej sekcji do **lokalnego** pola, a następnie zastąp `{your_username}` i `{your_password}` symbole zastępcze przy użyciu rzeczywistego wartości. Wybierz **wstawienia wartości na podstawie danych lokalnych** Aby skopiować zaktualizowane wartości do **zdalnego** , a następnie wybierz opcję **OK**.

    ![Dodaj ustawienie parametrów połączenia SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Parametry połączenia są przechowywane w postaci zaszyfrowanej na platformie Azure (**zdalnego**). Aby zapobiec przeciek wpisów tajnych oraz ich pliku local.settings.json w projekcie (**lokalnego**) powinny być wykluczone z kontroli źródła, takich jak przy użyciu pliku .gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Dodaj pakiet SqlClient do projektu

Należy dodać pakiet NuGet, który zawiera biblioteki SqlClient. Ta biblioteka dostępu do danych jest wymagane do łączenia z bazą danych SQL.

1. Otwórz swój projekt aplikacji funkcji lokalnej w programie Visual Studio 2017.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji funkcji, a następnie wybierz **Zarządzaj pakietami NuGet**.

1. Na karcie **Przeglądaj** wyszukaj pozycję ```System.Data.SqlClient``` i po znalezieniu wybierz ją.

1. W **System.Data.SqlClient** strony, wybierz wersję `4.5.1` a następnie kliknij przycisk **zainstalować**.

1. Po zakończeniu instalacji przejrzyj zmiany, a następnie kliknij przycisk **OK**, aby zamknąć okno **Podgląd**.

1. W przypadku wyświetlenia okna **Akceptacja licencji** kliknij przycisk **Akceptuję**.

Teraz możesz dodać kod funkcji języka C#, który nawiązuje połączenie z bazą danych SQL.

## <a name="add-a-timer-triggered-function"></a>Dodawanie funkcji wyzwalanej czasomierzem

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji funkcji, a następnie wybierz **Dodaj** > **funkcji platformy Azure w nowych**.

1. Za pomocą **usługi Azure Functions** szablonu wybranego, nadaj nazwę nowego elementu podobny `DatabaseCleanup.cs` i wybierz **Dodaj**.

1. W **funkcji platformy Azure w nowych** okna dialogowego wybierz **wyzwalacza czasomierza** i następnie **OK**. To okno dialogowe tworzy plik kodu dla funkcji wyzwalanej czasomierzem.

1. Otwórz nowy plik kodu i dodaj następujące instrukcje using na początku pliku:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Zastąp istniejące `Run` funkcji następującym kodem:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Ta funkcja jest uruchamiany co 15 sekund, aby zaktualizować `Status` kolumny według daty dostawy. Aby dowiedzieć się więcej na temat wyzwalaczy czasomierza, zobacz [wyzwalacza czasomierza dla usługi Azure Functions](functions-bindings-timer.md).

1. Naciśnij klawisz **F5** można uruchomić aplikacji funkcji. [Podstawowych narzędzi usługi Azure Functions](functions-develop-local.md) za program Visual Studio zostanie otwarte okno wykonywania.

1. Po 15 sekund po uruchomieniu funkcja działa. Obejrzyj dane wyjściowe i zanotuj liczbę wierszy, aktualizowane w **SalesOrderHeader** tabeli.

    ![Wyświetlanie dzienników funkcji.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Przy pierwszym wykonaniu należy zaktualizować 32 wiersze danych. Następujące przebiegi aktualizacji żadne wiersze danych, o ile nie wprowadzisz zmiany danych tabeli SalesOrderHeader tak, aby więcej wierszy są wybierane przez `UPDATE` instrukcji.

Jeśli planujesz [opublikować tę funkcję,](functions-develop-vs.md#publish-to-azure), pamiętaj, aby zmienić `TimerTrigger` atrybutu do bardziej uzasadnionego [harmonogramu wyrażenia cron](functions-bindings-timer.md#cron-expressions) niż co 15 sekund.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać. Funkcje z aplikacjami logiki w celu integracji z innymi usługami.

> [!div class="nextstepaction"]
> [Tworzenie funkcji integrującej się z usługą Logic Apps](functions-twitter-email.md)

Aby uzyskać więcej informacji na temat funkcji zobacz następujące artykuły:

+ [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)  
  Dokumentacja dla programistów dotycząca kodowania funkcji oraz definiowania wyzwalaczy i powiązań.
+ [Testowanie usługi Azure Functions](functions-test-a-function.md)  
  Opis różnych narzędzi i technik testowania funkcji.  
