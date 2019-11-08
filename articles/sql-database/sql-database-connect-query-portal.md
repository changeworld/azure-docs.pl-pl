---
title: 'Azure Portal: zapytanie przy użyciu edytora zapytań'
description: Dowiedz się, jak nawiązać połączenie z bazą danych SQL w witrynie Azure Portal za pomocą edytora zapytań SQL. Następnie uruchom instrukcje Transact-SQL (T-SQL), aby wykonać zapytanie i edytować dane.
keywords: połącz z bazą danych sql, azure portal, portal, edytor zapytań
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 10/24/2019
ms.openlocfilehash: 3990d7ec63c312d38168fe76269e1a920f1a6817
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827110"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Szybki Start: używanie Edytora zapytań SQL Azure Portal do łączenia i wykonywania zapytań dotyczących danych

Edytor zapytań SQL jest narzędziem przeglądarki portalu Azure, które ułatwia wykonywanie zapytań SQL w bazie danych Azure SQL Database lub usłudze Azure SQL Data Warehouse. W tym przewodniku skorzystasz z edytora zapytań w celu nawiązania połączenia z bazą danych SQL, a następnie uruchomisz instrukcje języka Transact-SQL, aby wykonywać zapytania o dane oraz wstawiać, aktualizować i usuwać dane.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Baza danych Azure SQL Database. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

  || Pojedyncza baza danych |
  |:--- |:--- |
  | Tworzenie| [Portal](sql-database-single-database-get-started.md) |
  || [Interfejs wiersza polecenia](scripts/sql-database-create-and-configure-database-cli.md) |
  || [Program PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurowanie | [Reguła zapory bazująca na adresach IP na poziomie serwera](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> Edytor zapytań używa portów 443 i 1443 do komunikacji.  Upewnij się, że włączono ruch wychodzący HTTPS na tych portach. Należy również dodać wychodzący adres IP do reguł zapory dozwolonych na serwerze, aby uzyskać dostęp do baz danych i magazynów.

## <a name="sign-in-the-azure-portal"></a>Logowanie w witrynie Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>Nawiązywanie połączenia przy użyciu uwierzytelniania SQL

1. Przejdź do Azure Portal, aby nawiązać połączenie z bazą danych SQL. Wyszukaj i wybierz pozycję **bazy danych SQL**.

    ![Przejdź do listy SQL Database, Azure Portal](./media/sql-database-connect-query-portal/search-for-sql-databases.png)

2. Wybierz bazę danych SQL.

    ![Wybierz bazę danych SQL, Azure Portal](./media/sql-database-connect-query-portal/select-a-sql-database.png)

3. W menu **bazy danych SQL** wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** .

    ![znajdowanie edytora zapytań](./media/sql-database-connect-query-portal/find-query-editor.PNG)

4. Na stronie **logowania** w obszarze Etykieta **uwierzytelniania programu SQL Server** wprowadź identyfikator **logowania** i **hasło** do konta administratora serwera użytego do utworzenia bazy danych. Następnie wybierz przycisk **OK**.

    ![logowanie](./media/sql-database-connect-query-portal/login-menu.png)

## <a name="connect-using-azure-active-directory"></a>Nawiązywanie połączenia przy użyciu usługi Azure Active Directory

Skonfigurowanie administratora usługi Azure Active Directory (Azure AD) umożliwia użycie pojedynczej tożsamości do logowania się do Azure Portal i bazy danych SQL. Wykonaj poniższe kroki, aby skonfigurować administratora usługi Azure AD dla programu SQL Server.

> [!NOTE]
> * Konta e-mail (na przykład outlook.com, gmail.com, yahoo.com itd.) nie są jeszcze obsługiwane jako Administratorzy usługi Azure AD. Wybierz użytkownika utworzonego natywnie w usłudze Azure AD lub sfederowanego z usługą Azure AD.
> * Logowanie administratora usługi Azure AD nie działa w przypadku kont, dla których włączono uwierzytelnianie 2-etapowe.

1. W menu Azure Portal lub na stronie **głównej** wybierz pozycję **wszystkie zasoby**.

2. Wybierz serwer SQL.

3. W menu **programu SQL Server** w obszarze **Ustawienia**wybierz pozycję **administrator Active Directory**.

4. Na pasku narzędzi strony **administratora** programu SQL Server Active Directory wybierz pozycję **Ustaw administratora** , a następnie wybierz użytkownika lub grupę jako administratora usługi Azure AD.

    ![wybierz usługę active directory](./media/sql-database-connect-query-portal/select-active-directory.png)

5. Na stronie **Dodawanie administratora** w polu wyszukiwania wprowadź użytkownika lub grupę do znalezienia, wybierz ją jako administratora, a następnie wybierz przycisk **Wybierz** .

6. Z powrotem na pasku narzędzi strony administratora programu SQL Server **Active Directory** wybierz pozycję **Zapisz**.

7. W menu **programu SQL Server** wybierz opcję **bazy danych SQL**, a następnie wybierz bazę danych SQL.

8. W menu **bazy danych SQL** wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** . Na stronie **logowania** na etykiecie **uwierzytelniania Active Directory** zostanie wyświetlony komunikat informujący, że użytkownik został zalogowany, jeśli jesteś administratorem usługi Azure AD. Następnie wybierz przycisk **Kontynuuj jako** *\<identyfikator użytkownika lub grupy >* .

## <a name="view-data"></a>Wyświetlanie danych

1. Po uwierzytelnieniu wklej następujący kod SQL w edytorze zapytań, aby uzyskać 20 najważniejszych produktów według kategorii.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Na pasku narzędzi wybierz pozycję **Uruchom**, a następnie przejrzyj dane wyjściowe w okienku **Wyniki**.

   ![wyniki edytora zapytań](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>Wstawianie danych

Uruchom następującą instrukcję [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) języka Transact-SQL, aby dodać nowy produkt do tabeli `SalesLT.Product`.

1. Zastąp poprzednie zapytanie tym.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Wybierz polecenie **Uruchom**, aby wstawić nowy wiersz do tabeli `Product`. W okienku **komunikaty** zostanie wyświetlone **zapytanie zakończone powodzeniem: uwzględnione wiersze: 1**.


## <a name="update-data"></a>Aktualizowanie danych

Uruchom następującą instrukcję [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) języka Transact-SQL, aby zmodyfikować nowy produkt.

1. Zastąp poprzednie zapytanie tym.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Uruchom**, aby zaktualizować określony wiersz w tabeli `Product`. W okienku **komunikaty** zostanie wyświetlone **zapytanie zakończone powodzeniem: uwzględnione wiersze: 1**.

## <a name="delete-data"></a>Usuwanie danych

Uruchom następującą instrukcję [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) języka Transact-SQL, aby usunąć nowy produkt.

1. Zastąp poprzednie zapytanie następującym:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Uruchom**, aby usunąć wiersz z tabeli `Product`. W okienku **komunikaty** zostanie wyświetlone **zapytanie zakończone powodzeniem: uwzględnione wiersze: 1**.


## <a name="query-editor-considerations"></a>Zagadnienia dotyczące edytora zapytań

Jest kilka rzeczy, o których trzeba wiedzieć podczas pracy z edytorem zapytań.

* Edytor zapytań używa portów 443 i 1443 do komunikacji.  Upewnij się, że włączono ruch wychodzący HTTPS na tych portach. Należy również dodać wychodzący adres IP do reguł zapory dozwolonych na serwerze, aby uzyskać dostęp do baz danych i magazynów.

* Naciśnięcie klawisza F5 powoduje odświeżenie strony edytora i utratę tworzonego zapytania.

* Edytor zapytań nie obsługuje nawiązywania połączeń z bazą danych `master`.

* Istnieje limit czasu na wykonanie zapytania wynoszący 5 minut.

* Edytor zapytań obsługuje tylko cylindryczne projekcje dla typów danych geograficznych.

* Tabele i widoki bazy danych nie obsługują funkcji IntelliSense. Jednak edytor obsługuje funkcję automatycznego uzupełniania dla nazw, które zostały już wpisane.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o języku Transact-SQL obsługiwanym w bazach danych Azure SQL Database, zobacz [Resolving Transact-SQL differences during migration to SQL Database (Rozwiązywanie różnic w języku Transact-SQL podczas migracji do usługi SQL Database)](sql-database-transact-sql-information.md).
