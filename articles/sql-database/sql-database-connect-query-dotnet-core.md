---
title: Korzystanie z platformy .NET Core do wykonywania zapytań w usłudze Azure SQL Database | Microsoft Docs
description: W tym temacie przedstawiono sposób wykorzystania platformy .NET Core do utworzenia programu, który nawiązuje połączenie z bazą danych Azure SQL Database i wykonuje zapytania za pomocą instrukcji języka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: 471d2b0b8d98651d4b9ef4e88df0e863715b0c88
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341783"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Szybki start: Korzystanie z platformy .NET Core (C#) do wykonywania zapytań w bazie danych Azure SQL Database

W tym przewodniku Szybki start pokazano, jak używać platformy [.NET Core](https://www.microsoft.com/net/) i kodu C# w celu nawiązania połączenia z usługą Azure SQL Database i jak uruchamiać instrukcję Transact-SQL, aby wysyłać zapytania o dane.

## <a name="prerequisites"></a>Wymagania wstępne

W celu skorzystania z tego samouczka potrzebne są następujące elementy:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Reguła zapory na poziomie serwera](sql-database-get-started-portal-firewall.md) dla publicznego adresu IP komputera.

- Zainstalowana platforma [.NET Core dla Twojego systemu operacyjnego](https://www.microsoft.com/net/core). 

> [!NOTE]
> W tym przewodniku Szybki start używana jest baza danych *mySampleDatabase*. Jeśli chcesz użyć innej bazy danych, musisz zmienić odwołania do bazy danych i zmodyfikować zapytanie `SELECT` w kodzie C#.


## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="get-adonet-connection-information-optional"></a>Uzyskiwanie informacji o połączeniu z platformą ADO.NET (opcjonalnie)

1. Przejdź na stronę bazy danych **mySampleDatabase** i w obszarze **Ustawienia** wybierz opcję **Parametry połączenia**.

2. Sprawdź pełne parametry połączenia sterownika **ADO.NET**.

    ![Parametry połączenia sterownika ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Skopiuj parametry połączenia sterownika **ADO.NET**, jeśli zamierzasz go używać.
  
## <a name="create-a-new-net-core-project"></a>Tworzenie nowego projektu platformy .NET Core

1. Otwórz wiersz polecenia i utwórz folder o nazwie **sqltest**. Przejdź do tego folderu i uruchom następujące polecenie.

    ```cmd
    dotnet new console
    ```
    Spowoduje to utworzenie nowych plików projektu aplikacji, w tym początkowego pliku kodu C# (**Program.cs**), pliku konfiguracyjnego XML (**sqltest.csproj**) i potrzebnych plików binarnych.

2. W edytorze tekstów otwórz plik **sqltest.csproj** i wklej następujący kod XML między tagami `<Project>`. Dodaje to obiekt `System.Data.SqlClient` jako zależność.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Wstawianie kodu zapytania bazy danych SQL

1. W edytorze tekstów otwórz plik **Program.cs**.

2. Zastąp jego zawartość następującym kodem i dodaj odpowiednie wartości dla serwera, bazy danych, nazwy użytkownika i hasła.

> [!NOTE]
> Aby użyć parametrów połączenia sterownika ADO.NET, zastąp cztery wiersze kodu, które ustawiają serwer, bazę danych, nazwę użytkownika i hasło, za pomocą poniższego wiersza. W parametrach ustaw swoją nazwę użytkownika i hasło.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom następujące polecenia.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Upewnij się, że zwracanych jest 20 pierwszych wierszy.

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. Naciśnij klawisz **Enter**, aby zamknąć okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczynanie pracy z platformą .NET Core w systemie Windows/Linux/macOS przy użyciu wiersza polecenia](/dotnet/core/tutorials/using-with-xplat-cli).
- Dowiedz się, jak [uzyskać połączenie i wykonywać zapytania bazy danych Azure SQL przy użyciu platformy .NET i programu Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Dowiedz się, jak [zaprojektować swoją pierwszą bazę danych Azure SQL przy użyciu narzędzia SSMS](sql-database-design-first-database.md) lub [zaprojektować bazę danych Azure SQL i nawiązać połączenie przy użyciu języka C# i sterownika ADO.NET](sql-database-design-first-database-csharp.md).
- Aby uzyskać więcej informacji na temat platformy .NET, zobacz [.NET documentation](https://docs.microsoft.com/dotnet/) (Dokumentacja platformy .NET).
