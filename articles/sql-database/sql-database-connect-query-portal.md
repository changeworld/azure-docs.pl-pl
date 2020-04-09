---
title: Kwerenda bazy danych SQL przy użyciu edytora zapytań w witrynie Azure portal
description: Dowiedz się, jak używać Edytora zapytań do uruchamiania kwerend Transact-SQL (T-SQL) w bazie danych SQL azure.
keywords: łączenie się z bazą danych SQL,zapytanie bazy danych SQL, azure portal, portal, edytor zapytań
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: c072161db7a477b7973571a18d4f686b1b9c6202
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985681"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-a-sql-database"></a>Szybki start: używanie edytora zapytań portalu Azure do wykonywania zapytań o bazę danych SQL

Edytor zapytań jest narzędziem w portalu Azure do uruchamiania zapytań SQL względem bazy danych SQL platformy Azure lub usługi Azure SQL Data Warehouse. 

W tym przewodniku Szybki start użyjesz edytora zapytań do uruchamiania zapytań Transact-SQL (T-SQL) względem bazy danych SQL platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne

Ukończenie tego przewodnika Szybki start wymaga przykładowej bazy danych AdventureWorksLT. Jeśli nie masz kopii roboczej bazy danych SQL AdventureWorksLT, następujący szybki start szybko tworzy jedną z następujących:

- [Szybki start: tworzenie pojedynczej bazy danych SQL platformy Azure przy użyciu portalu Azure, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](sql-database-single-database-get-started.md) 

### <a name="configure-network-settings"></a>Konfigurowanie ustawień sieciowych

Jeśli w edytorze kwerend pojawi się jeden z następujących błędów: *Ustawienia sieci lokalnej mogą uniemożliwiać Edytorowi zapytań wystawianie zapytań. Kliknij tutaj, aby uzyskać instrukcje dotyczące konfigurowania ustawień sieciowych*lub *Nie można ustanowić połączenia z serwerem. Może to wskazywać na problem z konfiguracją zapory lokalnej lub ustawieniami serwera proxy sieciowego,* następujące ważne informacje powinny pomóc w rozwiązaniu problemu:

> [!IMPORTANT]
> Edytor zapytań używa portów 443 i 1443 do komunikowania się. Upewnij się, że na tych portach włączono wychodzący ruch HTTPS. Należy również [dodać wychodzący adres IP do reguł zapory dozwolonych serwera,](sql-database-server-level-firewall-rule.md) aby uzyskać dostęp do baz danych i magazynów danych.


## <a name="open-the-sql-database-query-editor"></a>Otwórz Edytor zapytań bazy danych SQL

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i wybierz bazę danych SQL, którą chcesz zbadać.

2. W menu **bazy danych SQL** wybierz pozycję Edytor **zapytań (wersja zapoznawcza)**.

    ![znajdowanie edytora zapytań](./media/sql-database-connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Ustanawianie połączenia z bazą danych

Mimo że zalogowano się do portalu, nadal trzeba podać poświadczenia, aby uzyskać dostęp do bazy danych SQL. Aby połączyć się z bazą danych, można połączyć się przy użyciu uwierzytelniania SQL lub usługi Azure Active Directory.

### <a name="connect-using-sql-authentication"></a>Nawiązywanie połączenia przy użyciu uwierzytelniania SQL

1. Na stronie **Logowania** w obszarze **Uwierzytelnianie serwera SQL**wprowadź **login** i **hasło** dla użytkownika, który ma dostęp do bazy danych. Jeśli nie masz pewności, użyj loginu i hasła dla administratora serwera bazy danych.

    ![logowanie](./media/sql-database-connect-query-portal/login-menu.png)

2. Kliknij przycisk **OK**.


### <a name="connect-using-azure-active-directory"></a>Nawiązywanie połączenia przy użyciu usługi Azure Active Directory

Konfigurowanie administratora usługi Azure Active Directory (Azure AD) umożliwia używanie jednej tożsamości do logowania się do witryny Azure portal i bazy danych SQL. Aby połączyć się z bazą danych przy użyciu usługi Azure AD, wykonaj poniższe czynności, aby skonfigurować administratora usługi Azure AD dla serwera SQL.

> [!NOTE]
> * Konta e-mail (na przykład outlook.com, gmail.com, yahoo.com itd.) nie są jeszcze obsługiwane jako administratorzy usługi Azure AD. Wybierz użytkownika utworzonego natywnie w usłudze Azure AD lub sfederowanego z usługą Azure AD.
> * Logowanie administratora usługi Azure AD nie działa w przypadku kont, dla których włączono uwierzytelnianie 2-etapowe.

#### <a name="set-an-active-directory-admin-for-the-database-server"></a>Ustawianie administratora usługi Active Directory dla serwera bazy danych

1. W witrynie Azure portal wybierz serwer SQL.

2. W menu **serwera SQL** wybierz polecenie Administrator usługi **Active Directory**.

3. Na pasku narzędzi **administratora usługi Active Directory** na serwerze SQL wybierz pozycję **Ustaw administratora** i wybierz użytkownika lub grupę jako administratora usługi Azure AD.

    ![wybierz usługę active directory](./media/sql-database-connect-query-portal/select-active-directory.png)

4. Na stronie **Dodawanie administratora** w polu wyszukiwania wprowadź użytkownika lub grupę do znalezienia, wybierz go jako administratora, a następnie wybierz przycisk **Wybierz.**

5. Z powrotem na pasku narzędzi **administratora usługi Active Directory** programu SQL wybierz pozycję **Zapisz**.

### <a name="connect-to-the-database"></a>Łączenie z bazą danych

6. W menu **serwera SQL** wybierz bazy **danych SQL**, a następnie wybierz bazę danych SQL.

7. W menu **bazy danych SQL** wybierz pozycję Edytor **zapytań (wersja zapoznawcza)**. Na stronie **Logowania** pod etykietą **uwierzytelniania usługi Active Directory** zostanie wyświetlony komunikat informujący, że użytkownik został zalogowany, jeśli jesteś administratorem usługi Azure AD. Następnie wybierz przycisk **Kontynuuj jako** * \<identyfikator użytkownika lub grupy>.* Jeśli strona wskazuje, że nie zalogowano się pomyślnie, może być konieczne odświeżenie strony.

## <a name="query-a-sql-database"></a>Wykonywanie zapytań z bazy danych SQL

Poniższe przykładowe kwerendy powinny zostać pomyślnie uruchomione względem przykładowej bazy danych AdventureWorksLT.

### <a name="run-a-select-query"></a>Uruchamianie kwerendy SELECT

1. Wklej następującą kwerendę do edytora zapytań:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Wybierz **pozycję Uruchom,** a następnie przejrzyj dane wyjściowe w okienku **Wyniki.**

   ![wyniki edytora zapytań](./media/sql-database-connect-query-portal/query-editor-results.png)

3. Opcjonalnie można zapisać kwerendę jako plik sql lub wyeksportować zwrócone dane jako plik .json, csv lub xml.

### <a name="run-an-insert-query"></a>Uruchamianie kwerendy INSERT

Uruchom następującą instrukcję [INSERT](/sql/t-sql/statements/insert-transact-sql/) T-SQL, `SalesLT.Product` aby dodać nowy produkt w tabeli.

1. Wpisz to zapytanie w miejsce poprzedniego.

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


2. Wybierz polecenie **Uruchom**, aby wstawić nowy wiersz do tabeli `Product`. W okienku **Wiadomości** zostanie wyświetlony **komunikat Kwerenda powiodła się: wiersze, których dotyczy problem: 1**.


### <a name="run-an-update-query"></a>Uruchamianie kwerendy UPDATE

Uruchom następującą [instrukcję UPDATE](/sql/t-sql/queries/update-transact-sql/) T-SQL, aby zmodyfikować nowy produkt.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Uruchom**, aby zaktualizować określony wiersz w tabeli `Product`. W okienku **Wiadomości** zostanie wyświetlony **komunikat Kwerenda powiodła się: wiersze, których dotyczy problem: 1**.

### <a name="run-a-delete-query"></a>Uruchamianie kwerendy DELETE

Uruchom następującą instrukcję [DELETE](/sql/t-sql/statements/delete-transact-sql/) T-SQL, aby usunąć nowy produkt.

1. Zastąp poprzednie zapytanie następującym:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Uruchom**, aby usunąć wiersz z tabeli `Product`. W okienku **Wiadomości** zostanie wyświetlony **komunikat Kwerenda powiodła się: wiersze, których dotyczy problem: 1**.


## <a name="query-editor-considerations"></a>Zagadnienia dotyczące edytora zapytań

Jest kilka rzeczy, o których trzeba wiedzieć podczas pracy z edytorem zapytań.

* Edytor zapytań używa portów 443 i 1443 do komunikowania się. Upewnij się, że na tych portach włączono wychodzący ruch HTTPS. Należy również dodać wychodzący adres IP do reguł zapory dozwolonych serwera, aby uzyskać dostęp do baz danych i magazynów danych.

* Jeśli masz połączenie Link prywatny, Edytor zapytań działa bez konieczności dodawania adresu IP klienta do zapory bazy danych SQL

* Naciśnięcie **klawisza F5** powoduje odświeżenie strony edytora zapytań, a wszelkie kwerendy, nad którymi trwają prace, zostaną utracone.

* Edytor zapytań nie obsługuje nawiązywania połączeń z bazą danych `master`.

* Istnieje limit czasu na wykonanie zapytania wynoszący 5 minut.

* Edytor zapytań obsługuje tylko cylindryczne projekcje dla typów danych geograficznych.

* Nie ma obsługi intellisense dla tabel bazy danych i widoków, ale edytor obsługuje autouzupełnianie nazw, które zostały już wpisane.




## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o transact-SQL (T-SQL) obsługiwanych w bazach danych SQL platformy Azure, zobacz [Rozwiązywanie różnic transact-SQL podczas migracji do bazy danych SQL](sql-database-transact-sql-information.md).
