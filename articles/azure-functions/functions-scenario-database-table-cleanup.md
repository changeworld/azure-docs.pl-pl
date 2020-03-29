---
title: Wykonywanie zadania oczyszczania bazy danych za pomocą usługi Azure Functions
description: Użyj usługi Azure Functions, aby zaplanować zadanie, które łączy się z usługą Azure SQL Database, aby okresowo czyścić wiersze.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 2e3f53943d45e90b8aff8e386ce8d0e28670673f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366817"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Łączenie się z bazą danych SQL usługi Azure za pomocą funkcji azure

W tym artykule pokazano, jak używać usługi Azure Functions do tworzenia zaplanowanego zadania, które łączy się z bazą danych SQL platformy Azure lub wystąpieniem zarządzanym sql platformy Azure. Kod funkcji czyści wiersze w tabeli w bazie danych. Nowa funkcja Języka C# jest tworzona na podstawie wstępnie zdefiniowanego szablonu wyzwalacza czasomierza w programie Visual Studio 2019. Aby obsługiwać ten scenariusz, należy również ustawić ciąg połączenia bazy danych jako ustawienie aplikacji w aplikacji funkcji. W przypadku wystąpienia zarządzanego sql platformy Azure należy [włączyć publiczny punkt końcowy,](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) aby móc łączyć się z usługą Azure Functions. W tym scenariuszu używa operacji zbiorczej w bazie danych. 

Jeśli jest to pierwsze środowisko pracy z funkcjami języka C#, należy przeczytać [odwołanie dewelopera usługi Azure Functions C#.](functions-dotnet-class-library.md)

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj kroki opisane w artykule [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](functions-create-your-first-function-visual-studio.md) w celu utworzenia aplikacji funkcji lokalnej przeznaczonej dla wersji 2.x lub nowszej wersji środowiska wykonawczego. Musisz również opublikować swój projekt do aplikacji funkcji na platformie Azure.

+ W tym artykule pokazano polecenie Transact-SQL, które wykonuje operację oczyszczania zbiorczego w tabeli **SalesOrderHeader** w przykładowej bazie danych AdventureWorksLT. Aby utworzyć przykładową bazę danych AdventureWorksLT, wykonaj kroki opisane w artykule [Tworzenie bazy danych SQL platformy Azure w portalu Azure.](../sql-database/sql-database-get-started-portal.md)

+ Należy dodać [regułę zapory na poziomie serwera](../sql-database/sql-database-get-started-portal-firewall.md) dla publicznego adresu IP komputera używanego do tego przewodnika Szybki start. Ta reguła jest wymagana, aby mieć dostęp do wystąpienia bazy danych SQL z komputera lokalnego.  

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Musisz uzyskać parametry połączenia dla bazy danych utworzonej po zakończeniu [tworzenia bazy danych SQL platformy Azure w witrynie Azure portal](../sql-database/sql-database-get-started-portal.md).

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Wybierz **bazy danych SQL** z menu po lewej stronie i wybierz bazę danych na stronie bazy danych **SQL.**

1. Wybierz **pozycję Parametry połączenia** w obszarze **Ustawienia** i skopiuj pełny **ADO.NET** parametry połączenia. Dla usługi Azure SQL wystąpienie zarządzane wystąpienie skopiować ciąg połączenia dla publicznego punktu końcowego.

    ![Skopiuj parametry połączenia ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Ustawianie parametrów połączenia

Aplikacja funkcji obsługuje wykonywanie funkcji na platformie Azure. Najlepszym rozwiązaniem w zakresie zabezpieczeń jest przechowywanie ciągów połączeń i innych wpisów tajnych w ustawieniach aplikacji funkcji. Za pomocą ustawień aplikacji zapobiega przypadkowemu ujawnieniu ciągu połączenia z kodem. Dostęp do ustawień aplikacji dla aplikacji funkcji można uzyskać bezpośrednio z programu Visual Studio.

Aplikacja musi zostać wcześniej opublikowana na platformie Azure. Jeśli jeszcze tego nie zrobiono, [opublikuj swoją aplikację funkcji na platformie Azure](functions-develop-vs.md#publish-to-azure).

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji funkcji i wybierz polecenie **Publikuj** > **ustawienia usługi Azure App Service**. Wybierz **pozycję Dodaj ustawienie**, w obszarze Nowa nazwa ustawienia **aplikacji**, wpisz `sqldb_connection`i wybierz przycisk **OK**.

    ![Ustawienia aplikacji dla aplikacji funkcyjnej.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. W nowym ustawieniu **sqldb_connection** wklej skopiowany w poprzedniej sekcji ciąg połączenia do `{your_username}` pola `{your_password}` **Lokalny** i zastąp i symbole zastępcze wartościami rzeczywistymi. Wybierz **pozycję Wstaw wartość z lokalnego,** aby skopiować zaktualizowaną wartość do pola **Zdalne,** a następnie wybierz przycisk **OK**.

    ![Dodaj ustawienie ciągu połączenia SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Parametry połączenia są przechowywane zaszyfrowane na platformie Azure **(Remote).** Aby zapobiec wyciekowi wpisów tajnych, plik projektu local.settings.json **(Lokalny)** powinien zostać wykluczony z kontroli źródła, na przykład przy użyciu pliku gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Dodawanie pakietu SqlClient do projektu

Należy dodać pakiet NuGet, który zawiera bibliotekę SqlClient. Ta biblioteka dostępu do danych jest potrzebna do łączenia się z bazą danych SQL.

1. Otwórz projekt aplikacji funkcji lokalnej w programie Visual Studio 2019.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji funkcji i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. Na karcie **Przeglądaj** wyszukaj pozycję ```System.Data.SqlClient``` i po znalezieniu wybierz ją.

1. Na stronie **System.Data.SqlClient** wybierz `4.5.1` wersję, a następnie kliknij przycisk **Zainstaluj**.

1. Po zakończeniu instalacji przejrzyj zmiany, a następnie kliknij przycisk **OK**, aby zamknąć okno **Podgląd**.

1. W przypadku wyświetlenia okna **Akceptacja licencji** kliknij przycisk **Akceptuję**.

Teraz można dodać kod funkcji Języka C#, który łączy się z bazą danych SQL.

## <a name="add-a-timer-triggered-function"></a>Dodawanie funkcji wyzwalanej czasomierzem

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji funkcji i wybierz polecenie **Dodaj** > **nową funkcję platformy Azure**.

1. Po wybraniu szablonu **Usługi platformy** Azure `DatabaseCleanup.cs` nazwij nowy element czymś w stylu i wybierz pozycję **Dodaj**.

1. W oknie dialogowym **Nowa funkcja Platformy Azure** wybierz pozycję **Wyzwalacz czasowy,** a następnie **przycisk OK**. To okno dialogowe tworzy plik kodu dla funkcji wyzwalanego czasomierza.

1. Otwórz nowy plik kodu i dodaj następujące instrukcje w górnej części pliku:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Zastąp istniejącą `Run` funkcję następującym kodem:

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

    Ta funkcja jest uruchamiana co `Status` 15 sekund w celu zaktualizowania kolumny na podstawie daty wysyłki. Aby dowiedzieć się więcej o wyzwalaczu czasomierza, zobacz [Wyzwalacz czasomierza dla usługi Azure Functions](functions-bindings-timer.md).

1. Naciśnij **klawisz F5,** aby uruchomić aplikację funkcyjną. Okno wykonywania [podstawowych narzędzi azure funkcje](functions-develop-local.md) otwiera się za visual studio.

1. Po 15 sekundach po uruchomieniu funkcja jest uruchamiana. Obejrzyj dane wyjściowe i zanotuj liczbę wierszy zaktualizowanych w tabeli **SalesOrderHeader.**

    ![Wyświetlanie dzienników funkcji.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Przy pierwszym wykonaniu należy zaktualizować 32 wiersze danych. Następujące przebiegi nie aktualizują żadnych wierszy danych, chyba że wprowadzono zmiany w danych `UPDATE` tabeli SalesOrderHeader, tak aby więcej wierszy zostało wybranych przez instrukcję.

Jeśli planujesz [opublikować tę funkcję,](functions-develop-vs.md#publish-to-azure)pamiętaj, aby zmienić atrybut na `TimerTrigger` bardziej rozsądny harmonogram [cron](functions-bindings-timer.md#ncrontab-expressions) niż co 15 sekund.

## <a name="next-steps"></a>Następne kroki

Następnie dowiedz się, jak używać. Funkcje z aplikacjami logiki do integracji z innymi usługami.

> [!div class="nextstepaction"]
> [Tworzenie funkcji integranych z aplikacjami logiki](functions-twitter-email.md)

Aby uzyskać więcej informacji na temat funkcji, zobacz następujące artykuły:

+ [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)  
  Dokumentacja dla programistów dotycząca kodowania funkcji oraz definiowania wyzwalaczy i powiązań.
+ [Testowanie usługi Azure Functions](functions-test-a-function.md)  
  Opis różnych narzędzi i technik testowania funkcji.  
