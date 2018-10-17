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
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 024958d8a548313b53fc24ade5805de036a89afb
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351919"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Łączenie z bazą danych SQL Azure za pomocą usługi Azure Functions
W tym temacie dowiesz się, jak używać usługi Azure Functions, aby utworzyć zaplanowane zadanie, które utraciły wierszy w tabeli w bazie danych SQL Azure. Nowa funkcja skryptu języka C# jest tworzony na podstawie szablonu wyzwalacza czasomierza wstępnie zdefiniowanych w witrynie Azure portal. Aby zapewnić obsługę tego scenariusza, należy ustawić parametry połączenia bazy danych jako ustawienia aplikacji w aplikacji funkcji. W tym scenariuszu operacja zbiorcza w bazie danych. 

Zapewnienie swojego osobistego procesu funkcji tworzenia, odczytu, aktualizacji i operacje usuwania (CRUD) w tabeli funkcji Mobile Apps, należy w zamian użyć [powiązania funkcji Mobile Apps](functions-bindings-mobile-apps.md).

> [!IMPORTANT]
> Przykłady w tym dokumencie mają zastosowanie do środowisko uruchomieniowe 1.x. Informacje na temat tworzenia aplikacji funkcji w wersji 1.x [można znaleźć tutaj](./functions-versions.md#creating-1x-apps).

## <a name="prerequisites"></a>Wymagania wstępne

+ Ten temat używa funkcji wyzwalanej czasomierzem. Wykonaj kroki opisane w temacie [Tworzenie funkcji na platformie Azure, który jest wyzwalany przez czasomierz](functions-create-scheduled-function.md) tworzenia wersji języka C# tej funkcji.   

+ W tym temacie przedstawiono polecenie języka Transact-SQL, które wykonuje operację czyszczenia zbiorczego w **SalesOrderHeader** tabeli w bazie danych AdventureWorksLT. Aby utworzyć przykładowej bazy danych AdventureWorksLT, wykonaj kroki opisane w temacie [utworzyć bazę danych Azure SQL database w witrynie Azure portal](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Musisz pobrać parametry połączenia dla bazy danych utworzonej po zakończeniu [utworzyć bazę danych Azure SQL database w witrynie Azure portal](../sql-database/sql-database-get-started-portal.md).

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
 
3. Wybierz **baz danych SQL** z menu po lewej stronie i wybierz swoją bazę danych na **baz danych SQL** strony.

4. Wybierz **Pokaż parametry połączenia bazy danych** i skopiować pełną **ADO.NET** parametry połączenia. 

    ![Skopiuj parametry połączenia ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Ustawianie parametrów połączenia 

Aplikacja funkcji obsługuje wykonywanie funkcji na platformie Azure. Jest najlepszym rozwiązaniem do przechowywania parametrów połączeń i innych wpisów tajnych w ustawieniach aplikacji funkcji. Używanie ustawień aplikacji uniemożliwia przypadkowego ujawnienia parametry połączenia w kodzie. 

1. Przejdź do usługi utworzonej aplikacji funkcji [Tworzenie funkcji na platformie Azure, który jest wyzwalany przez czasomierz](functions-create-scheduled-function.md).

2. Wybierz **funkcje platformy** > **ustawienia aplikacji**.
   
    ![Ustawienia aplikacji dla aplikacji funkcji.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Przewiń w dół do **parametry połączenia** i dodaj ciąg połączenia przy użyciu ustawień określonych w tabeli.
   
    ![Dodaj parametry połączenia do ustawień aplikacji funkcji.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Ustawienie       | Sugerowana wartość | Opis             | 
    | ------------ | ------------------ | --------------------- | 
    | **Nazwa**  |  sqldb_connection  | Umożliwia dostęp do parametrów połączenia przechowywanych w kodzie funkcji.    |
    | **Wartość** | Ciąg skopiowany  | Wklej parametry połączenia skopiowaną w poprzedniej sekcji i Zastąp `{your_username}` i `{your_password}` symbole zastępcze wartości rzeczywistych. |
    | **Typ** | SQL Database | Użyj domyślnego połączenia bazy danych SQL. |   

3. Kliknij pozycję **Zapisz**.

Teraz możesz dodać kod funkcji języka C#, który nawiązuje połączenie z bazą danych SQL.

## <a name="update-your-function-code"></a>Aktualizacja kodu funkcji

1. W aplikacji funkcji w portalu wybierz funkcji wyzwalanej przez czasomierz.
 
3. Dodaj następujące odwołania zestawów w górnej części istniejącego kodu C# script funkcji:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >Kod w tych przykładach przedstawiono skrypt języka C# z poziomu portalu. Podczas tworzenia prekompilowanego funkcji C# lokalnie, należy zamiast tego dodać odwołania do tych składa się w projekcie lokalnym.  

3. Dodaj następujący kod `using` instrukcje do funkcji:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Zastąp istniejące `Run` funkcji następującym kodem:
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    Następujące przykładowe polecenie aktualizuje `Status` kolumny według daty dostawy. Należy go zaktualizować 32 wiersze danych.

5. Kliknij przycisk **Zapisz**, obejrzyj **dzienniki** systemu windows dla następnej funkcji wykonywania, a następnie zanotuj liczbę wiersz zaktualizowany w **SalesOrderHeader** tabeli.

    ![Wyświetlanie dzienników funkcji.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak za pomocą funkcji Logic Apps do integracji z innymi usługami.

> [!div class="nextstepaction"] 
> [Tworzenie funkcji integrującej się z usługą Logic Apps](functions-twitter-email.md)

Aby uzyskać więcej informacji na temat funkcji zobacz następujące tematy:

* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)  
  Dokumentacja dla programistów dotycząca kodowania funkcji oraz definiowania wyzwalaczy i powiązań.
* [Testowanie usługi Azure Functions](functions-test-a-function.md)  
  Opis różnych narzędzi i technik testowania funkcji.  
