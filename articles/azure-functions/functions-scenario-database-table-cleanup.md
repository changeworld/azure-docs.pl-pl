---
title: Użyj usługi Azure Functions do wykonania zadania oczyszczania bazy danych | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Functions można zaplanować zadanie, które łączy się z bazą danych SQL Azure, aby okresowo wyczyszczenia wierszy.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 2947fc6da0c4559e81cf97255b8375b020e0b657
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Nawiązywanie połączenia z bazy danych SQL Azure za pomocą usługi Azure Functions
W tym temacie przedstawiono sposób użycia usługi Azure Functions można utworzyć zaplanowane zadanie, które utraciły wierszy w tabeli w bazie danych SQL Azure. Nowa funkcja skryptu C# jest tworzony na podstawie szablonu wyzwalacza czasomierza wstępnie zdefiniowane w portalu Azure. Aby zapewnić obsługę tego scenariusza, należy także ustawić parametry połączenia bazy danych jako ustawienie aplikacji w aplikacji funkcji. W tym scenariuszu operacja zbiorcza w bazie danych. 

Aby z funkcji poszczególnych procesu tworzenia, odczytu, aktualizacji i operacji usuwania (CRUD) w tabeli Mobile Apps, zamiast tego należy używać [powiązania Mobile Apps](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Wymagania wstępne

+ W tym temacie funkcja czasomierza wyzwolone. Wykonaj kroki w temacie [Tworzenie funkcji platformy Azure, która jest wyzwalany przez czasomierz](functions-create-scheduled-function.md) tworzenia wersji języka C# tej funkcji.   

+ W tym temacie przedstawiono polecenie języka Transact-SQL, które wykonuje operację czyszczenia zbiorczego w **SalesOrderHeader** tabeli w bazie danych AdventureWorksLT. Aby utworzyć przykładową bazę danych AdventureWorksLT, wykonaj czynności opisane w temacie [tworzenie bazy danych Azure SQL w portalu Azure](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Należy pobrać parametry połączenia dla bazy danych utworzonej po wykonaniu [tworzenie bazy danych Azure SQL w portalu Azure](../sql-database/sql-database-get-started-portal.md).

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
 
3. Wybierz **baz danych SQL** z menu po lewej stronie i wybierz bazę danych na **baz danych SQL** strony.

4. Wybierz **Pokaż parametry połączenia bazy danych** i skopiować pełną **ADO.NET** parametry połączenia. 

    ![Skopiuj parametry połączenia ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Ustawianie parametrów połączenia 

Aplikacja funkcji obsługuje wykonywanie funkcji na platformie Azure. Jest najlepszym rozwiązaniem jest przechowywanie parametrów połączenia i innych informacji poufnych w ustawieniach funkcji aplikacji. Używanie ustawień aplikacji uniemożliwia przypadkowego ujawnienia parametrów połączenia w kodzie. 

1. Przejdź do tworzenia aplikacji funkcji [Tworzenie funkcji platformy Azure, która jest wyzwalany przez czasomierz](functions-create-scheduled-function.md).

2. Wybierz **funkcji platformy** > **ustawienia aplikacji**.
   
    ![Ustawienia aplikacji dla aplikacji funkcja.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Przewiń w dół do **parametry połączenia** i dodaj ciąg połączenia przy użyciu ustawień określonych w tabeli.
   
    ![Dodaj parametry połączenia w ustawieniach aplikacji funkcji.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Ustawienie       | Sugerowana wartość | Opis             | 
    | ------------ | ------------------ | --------------------- | 
    | **Nazwa**  |  sqldb_connection  | Umożliwia dostęp do parametrów połączenia przechowywanych w kodzie funkcji.    |
    | **Wartość** | Ciąg skopiowany  | Wklej parametry połączenia skopiowane z poprzedniej sekcji i Zastąp `{your_username}` i `{your_password}` symbole zastępcze wartości rzeczywistych. |
    | **Typ** | SQL Database | Użyj domyślnego połączenia bazy danych SQL. |   

3. Kliknij pozycję **Zapisz**.

Teraz można dodać kod funkcji języka C#, który nawiązuje połączenie z bazą danych SQL.

## <a name="update-your-function-code"></a>Zaktualizuj kod — funkcja

1. W funkcji aplikacji w portalu wybierz funkcję wyzwalany przez czasomierz.
 
3. Dodaj następujące odwołania do zestawów w górnej części istniejący kod C# skrypt funkcji:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >Kod w tym przykładzie są skryptu C# z portalu. Podczas tworzenia prekompilowanego C# funkcję lokalnie, należy zamiast tego dodać odwołania do tych składana w projekcie lokalnego.  

3. Dodaj następujące `using` instrukcje funkcji:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Zastąp istniejące `Run` funkcja następującym kodem:
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

    To przykładowe polecenie aktualizuje `Status` kolumna jest oparta na daty. Należy ją zaktualizować 32 wierszy danych.

5. Kliknij przycisk **zapisać**, obejrzyj **dzienniki** windows Następna funkcja wykonywania, a następnie zanotuj liczbę wierszy zaktualizowane w **SalesOrderHeader** tabeli.

    ![Wyświetlać dzienniki funkcji.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Kolejne kroki

Następnie Dowiedz się, jak za pomocą funkcji aplikacji logiki do integracji z innymi usługami.

> [!div class="nextstepaction"] 
> [Tworzy funkcję, która integruje się z usługą Logic Apps](functions-twitter-email.md)

Aby uzyskać więcej informacji na temat funkcji zobacz następujące tematy:

* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)  
  Dokumentacja dla programistów dotycząca kodowania funkcji oraz definiowania wyzwalaczy i powiązań.
* [Testowanie usługi Azure Functions](functions-test-a-function.md)  
  Opis różnych narzędzi i technik testowania funkcji.  
