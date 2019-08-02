---
title: Identyfikatory logowania i użytkownicy bazy danych SQL platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat SQL Database i SQL Data Warehouse zarządzania zabezpieczeniami, w tym, jak zarządzać dostępem do bazy danych i zabezpieczeniami logowania za pomocą konta głównego na poziomie serwera.
keywords: zabezpieczenia bazy danych sql, zarządzanie zabezpieczeniami bazy danych, zabezpieczenia logowania, zabezpieczenia bazy danych, dostęp do bazy danych
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/26/2019
ms.openlocfilehash: 3a59e276c3ec9717634c8f0f3634b7337ebc47d8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567710"
---
# <a name="controlling-and-granting-database-access-to-sql-database-and-sql-data-warehouse"></a>Kontrolowanie i udzielanie dostępu do bazy danych do SQL Database i SQL Data Warehouse

Po skonfigurowaniu reguł zapory można nawiązać połączenie z usługą Azure [SQL Database](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) jako jeden z kont administratora jako właściciel bazy danych lub jako użytkownik bazy danych w bazie danych programu.  

> [!NOTE]  
> Ten temat ma zastosowanie do programu Azure SQL Server oraz do SQL Database i SQL Data Warehouse baz danych utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database. 
> [!TIP]
> Aby zapoznać się z samouczkiem, zobacz temat [zabezpieczanie Azure SQL Database](sql-database-security-tutorial.md). Ten samouczek nie ma zastosowania do **Azure SQL Database wystąpienia zarządzanego**.

## <a name="unrestricted-administrative-accounts"></a>Konta z uprawnieniami administracyjnymi bez ograniczeń

Istnieją dwa konta z uprawnieniami administracyjnymi (**Administrator serwera** i **Administrator usługi Active Directory**), które funkcjonują jako administratorzy. Aby zidentyfikować te konta administratorów dla programu SQL Server, Otwórz Azure Portal i przejdź do karty właściwości serwera SQL lub SQL Database.

![Administratorzy serwera SQL](media/sql-database-manage-logins/sql-admins.png)

- **Administrator serwera**

  Podczas tworzenia serwera Azure SQL musi zostać podany **identyfikator logowania administratora serwera**. Serwer SQL tworzy to konto jako identyfikator logowania w bazie danych master. To konto używa do połączenia uwierzytelnienia programu SQL Server (nazwy użytkownika i hasła). Może istnieć tylko jedno z tych kont.

  > [!NOTE]
  > Aby zresetować hasło administratora serwera, przejdź do [Azure Portal](https://portal.azure.com), kliknij pozycję **serwery SQL**, wybierz serwer z listy, a następnie kliknij przycisk **Resetuj hasło**.

- **Azure Active Directory administrator**

  Jako konto administratora można również skonfigurować jedno konto usługi Azure Active Directory (indywidualne lub grupy zabezpieczeń). Skonfigurowanie administratora usługi Azure AD jest opcjonalne, ale **należy** skonfigurować administratora usługi Azure AD, jeśli chcesz używać kont usługi Azure AD do nawiązywania połączenia z usługą SQL Database. Aby uzyskać więcej informacji na temat konfigurowania dostępu do usługi Azure Active Directory, zobacz artykuły [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Łączenie się z usługą SQL Database lub SQL Data Warehouse przy użyciu uwierzytelnienia usługi Azure Active Directory) i [SSMS support for Azure AD MFA with SQL Database and SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) (Obsługa programu SSMS w usłudze Azure AD MFA przy użyciu usługi SQL Database i SQL Data Warehouse).

Konta **Administrator serwera** i **Administrator usługi Azure AD** mają następujące cechy:

- Są jedynymi kontami, które mogą automatycznie łączyć się z dowolnymi SQL Database na serwerze. (Aby połączyć się z bazą danych użytkownika, inne konta muszą być właścicielem bazy danych lub mieć konto użytkownika w bazie danych użytkownika).
- Te konta korzystają z baz danych użytkowników jako użytkownik `dbo` i mają wszystkie uprawnienia w bazach danych użytkowników. (Właściciel bazy danych użytkownika również korzysta z bazy danych jako użytkownik `dbo`). 
- Nie należy wprowadzać `master` bazy danych `dbo` jako użytkownik i mieć ograniczone uprawnienia w obszarze głównym. 
- **Nie** są członkami standardowej SQL Server `sysadmin` stałej roli serwera, która nie jest dostępna w usłudze SQL Database.  
- Może tworzyć, zmieniać i usuwać bazy danych, nazwy logowania, użytkowników z wzorców i reguły zapory adresów IP na poziomie serwera.
- Może dodawać i usuwać członków do `dbmanager` ról i. `loginmanager`
- Może wyświetlać `sys.sql_logins` tabelę systemową.

### <a name="configuring-the-firewall"></a>Konfigurowanie zapory

W przypadku skonfigurowania zapory na poziomie serwera za pomocą pojedynczego adresu IP lub zakresu adresów konta **Administrator serwera SQL** i **Administrator usługi Azure Active Directory** mogą łączyć się z bazą danych master i wszystkimi bazami danych użytkowników. Początkowo zaporę na poziomie serwera można skonfigurować za pomocą [witryny Azure Portal](sql-database-single-database-get-started.md), programu [PowerShell](sql-database-powershell-samples.md) lub [interfejsu API REST](https://msdn.microsoft.com/library/azure/dn505712.aspx). Po nawiązaniu połączenia można również skonfigurować dodatkowe reguły zapory adresów IP na poziomie serwera za pomocą [języka Transact-SQL](sql-database-configure-firewall-settings.md).

### <a name="administrator-access-path"></a>Ścieżka dostępu administratora

Po poprawnym skonfigurowaniu zapory na poziomie serwera konta **Administrator serwera SQL** i **Administrator usługi Azure Active Directory** mogą łączyć się przy użyciu narzędzi klienckich, takich jak SQL Server Management Studio lub SQL Server Data Tools. Tylko najnowsze narzędzia oferują wszystkie funkcje i możliwości. Na poniższym diagramie przedstawiono typową konfigurację dla tych dwóch kont administracyjnych.

![Konfiguracja dwóch kont administracyjnych](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Używając otwartego portu w zaporze na poziomie serwera, administratorzy mogą połączyć się z dowolną bazą danych SQL.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Łączenie się z bazą danych przy użyciu programu SQL Server Management Studio

Aby dowiedzieć się, jak utworzyć serwer, bazę danych, reguły zapory adresów IP na poziomie serwera i używać SQL Server Management Studio do wykonywania zapytań dotyczących bazy danych, zobacz Rozpoczynanie [pracy z serwerami Azure SQL Database, bazami danych i regułami zapory przy użyciu Azure Portal i SQL Management Studio serwera](sql-database-single-database-get-started.md).

> [!IMPORTANT]
> Zalecane jest używanie najnowszej wersji programu Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="additional-server-level-administrative-roles"></a>Dodatkowe role administracyjne na poziomie serwera

>[!IMPORTANT]
>Ta sekcja nie ma zastosowania do **Azure SQL Database wystąpienia zarządzanego** , ponieważ te role są specyficzne dla **Azure SQL Database**.

Oprócz ról administracyjnych na poziomie serwera omówionych wcześniej usługa SQL Database zawiera dwie ograniczone role administracyjne w bazie danych master, do których można dodać konta użytkowników, aby udzielić im uprawnień do tworzenia baz danych lub zarządzania identyfikatorami logowania.

### <a name="database-creators"></a>Kreatory bazy danych

Jedna z tych ról administracyjnych to rola **dbmanager**. Członkowie tej roli mogą tworzyć nowe bazy danych. Aby użyć tej roli, należy utworzyć użytkownika w bazie danych `master` i dodać go do roli bazy danych **dbmanager**. Aby utworzyć bazę danych, użytkownik musi być użytkownikiem opartym na SQL Server logowaniu `master` do bazy danych lub użytkownika zawartej bazy danych na podstawie Azure Active Directory użytkownika.

1. Aby nawiązać połączenie z `master` bazą danych, użyj konta administratora.
2. Utwórz nazwę logowania SQL Server uwierzytelniania przy użyciu instrukcji [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) . Przykładowa instrukcja:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Podczas tworzenia nazwy logowania lub użytkownika zawartej bazy danych należy używać silnego hasła. Aby uzyskać więcej informacji, zobacz artykuł [Silne hasła](https://msdn.microsoft.com/library/ms161962.aspx).

   W celu poprawy wydajności nazwy logowania (nazwy główne na poziomie serwera) są tymczasowo przechowywane w pamięci podręcznej na poziomie bazy danych. Aby odświeżyć pamięć podręczną uwierzytelniania, zobacz artykuł [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

3. W bazie danych Utwórz użytkownika przy użyciu instrukcji [Create User.](https://msdn.microsoft.com/library/ms173463.aspx) `master` Użytkownik może być użytkownikiem uwierzytelnienia zawartej bazy danych usługi Azure Active Directory (jeśli skonfigurowano środowisko dla uwierzytelniania usługi Azure AD), użytkownikiem uwierzytelnienia zawartej bazy danych programu SQL Server lub użytkownikiem uwierzytelniania programu SQL Server w oparciu o nazwę logowania uwierzytelniania programu SQL Server (utworzonym w poprzednim kroku). Przykładowe instrukcje:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Dodaj nowego użytkownika do roli bazy danych **DBManager** w programie `master` przy użyciu instrukcji [ALTER role](https://msdn.microsoft.com/library/ms189775.aspx) . Przykładowe instrukcje:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > Dbmanager jest rolą bazy danych w bazie danych master, więc do roli dbmanager można dodać tylko użytkownika bazy danych. Do roli na poziomie bazy danych nie można dodać nazwy logowania na poziomie serwera.

5. W razie potrzeby skonfiguruj regułę zapory, aby umożliwić połączenie się nowemu użytkownikowi. (Nowy użytkownik może być objęty istniejącą regułą zapory).

Teraz użytkownik może połączyć `master` się z bazą danych i może tworzyć nowe bazy danych. Konto tworzące bazę danych staje się właścicielem bazy danych.

### <a name="login-managers"></a>Menedżerowie logowania

Druga rola administracyjna to rola menedżera logowania. Członkowie tej roli mogą tworzyć nowe nazwy logowania w bazie danych master. Jeśli chcesz, możesz wykonać te same kroki (utworzenie identyfikatora logowania i użytkownika, a następnie dodanie użytkownika do roli **loginmanager**), aby umożliwić użytkownikowi tworzenie nowych identyfikatorów logowania w bazie danych master. Zazwyczaj identyfikatory logowania nie są konieczne, ponieważ firma Microsoft zaleca korzystanie z użytkowników zawartej bazy danych, którzy przeprowadzają uwierzytelnianie na poziomie bazy danych zamiast użytkowników przeprowadzających uwierzytelnianie w oparciu o identyfikator logowania. Aby uzyskać więcej informacji, zobacz artykuł [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych).

## <a name="non-administrator-users"></a>Użytkownicy niebędący administratorami

Ogólnie rzecz biorąc, konta inne niż administracyjne nie potrzebują dostępu do bazy danych master. Tworzenie użytkowników zawartej bazy danych na poziomie bazy danych przy użyciu instrukcji [CREATE USER (język Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). Użytkownik może być użytkownikiem uwierzytelnienia zawartej bazy danych usługi Azure Active Directory (jeśli skonfigurowano środowisko dla uwierzytelniania usługi Azure AD), użytkownikiem uwierzytelnienia zawartej bazy danych programu SQL Server lub użytkownikiem uwierzytelniania programu SQL Server w oparciu o nazwę logowania uwierzytelniania programu SQL Server (utworzonym w poprzednim kroku). Aby uzyskać więcej informacji, zobacz artykuł [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych). 

Aby utworzyć użytkowników, połącz się z bazą danych i wykonaj instrukcje podobne do następujących:

```sql
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Początkowo tylko jeden z administratorów lub jeden z właścicieli bazy danych mogą tworzyć użytkowników. Aby autoryzować dodatkowych użytkowników do tworzenia nowych użytkowników, należy udzielić im uprawnienia `ALTER ANY USER` przy użyciu instrukcji, takiej jak:

```sql
GRANT ALTER ANY USER TO Mary;
```

Aby zapewnić dodatkowym użytkownikom pełną kontrolę nad bazą danych, nadaj im członkom stałej roli bazy danych **db_owner** .

W Azure SQL Database Użyj `ALTER ROLE` instrukcji.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

W Azure SQL Data Warehouse Użyj [Sp_addrolemember exec](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).
```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```


> [!NOTE]
> Jednym z typowych przyczyn tworzenia użytkownika bazy danych na podstawie nazwy logowania SQL Database serwera jest użytkownicy, którzy muszą mieć dostęp do wielu baz danych. Ponieważ użytkownicy zawartej bazy danych są poszczególnymi jednostkami, każda baza danych utrzymuje własnego użytkownika i jego własne hasło. Może to spowodować obciążenie, ponieważ użytkownik musi pamiętać każde hasło dla każdej bazy danych i może stać się untenable w przypadku konieczności zmiany wielu haseł dla wielu baz danych. Jednak w przypadku korzystania z SQL Server logowań i wysokiej dostępności (aktywnej replikacji geograficznej i grup trybu failover) SQL Server logowania należy ustawić ręcznie na każdym serwerze. W przeciwnym razie użytkownik bazy danych nie będzie już mapowany na logowanie do serwera po przejściu w tryb failover i nie będzie mógł uzyskać dostępu do przełączenia do trybu failover dla bazy danych. Aby uzyskać więcej informacji na temat konfigurowania nazw logowania dla replikacji geograficznej, zobacz [Konfigurowanie i zarządzanie zabezpieczeniami Azure SQL Database na potrzeby przywracania geograficznego lub przełączania do trybu failover](sql-database-geo-replication-security-config.md).

### <a name="configuring-the-database-level-firewall"></a>Konfigurowanie zapory na poziomie bazy danych

Najlepszym rozwiązaniem jest sytuacja, gdy użytkownicy niebędący administratorami mają dostęp do używanych baz danych tylko poprzez zaporę. Zamiast autoryzowania ich adresów IP poprzez zaporę na poziomie serwera i przydzielania dostępu do wszystkich baz danych użyj instrukcji [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx), aby skonfigurować zaporę na poziomie bazy danych. Nie można skonfigurować zapory na poziomie bazy danych za pomocą portalu.

### <a name="non-administrator-access-path"></a>Ścieżka dostępu użytkownika niebędącego administratorem

Gdy zapora na poziomie bazy danych jest odpowiednio skonfigurowana, użytkownicy bazy danych mogą łączyć się za pomocą narzędzia klienckiego, takiego jak SQL Server Management Studio lub SQL Server Data Tools. Tylko najnowsze narzędzia oferują wszystkie funkcje i możliwości. Na poniższym diagramie przedstawiono typowe ścieżki dostępu użytkowników niebędących administratorami.

![Ścieżka dostępu użytkownika niebędącego administratorem](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupy i role

Wydajne zarządzanie dostępem obejmuje korzystanie z uprawnień przypisanych do grup i ról, a nie do poszczególnych użytkowników. 

- Korzystając z uwierzytelniania usługi Azure Active Directory, umieść użytkowników usługi Azure Active Directory w grupie usługi Azure Active Directory. Utwórz użytkownika zawartej bazy danych dla tej grupy. Umieść co najmniej jednego użytkownika bazy danych w [roli bazy danych](https://msdn.microsoft.com/library/ms189121), a następnie przypisz [uprawnienia](https://msdn.microsoft.com/library/ms191291.aspx) do roli bazy danych.

- W przypadku uwierzytelniania programu SQL Server utwórz zawartych użytkowników bazy danych w bazie danych. Umieść co najmniej jednego użytkownika bazy danych w [roli bazy danych](https://msdn.microsoft.com/library/ms189121), a następnie przypisz [uprawnienia](https://msdn.microsoft.com/library/ms191291.aspx) do roli bazy danych.

Role bazy danych mogą być rolami wbudowanymi, takimi jak **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** i **db_denydatareader**. Rola **db_owner** jest najczęściej używana do udzielenia pełnych uprawnień jedynie niewielkiej liczbie użytkowników. Inne ustalone role bazy danych ułatwiają szybkie tworzenie prostej bazy danych, ale nie zaleca się ich używania w większości przypadków tworzenia produkcyjnych baz danych. Na przykład ustalona rola bazy danych **db_datareader** pozwala na odczyt każdej tabeli w bazie danych, co nie zawsze jest niezbędne. Znacznie lepiej jest używać instrukcji [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx), aby tworzyć własne role użytkownika bazy danych i rozważnie udzielać każdej roli możliwie najniższych uprawnień niezbędnych do zaspokojenia potrzeb biznesowych. Gdy użytkownik jest członkiem wielu ról, łączą one uprawnienia ich wszystkich.

## <a name="permissions"></a>Uprawnienia

Istnieje ponad 100 uprawnień, których można indywidualnie udzielić lub odmówić w usłudze SQL Database. Wiele z tych uprawnień jest zagnieżdżonych. Na przykład uprawnienie `UPDATE` na schemacie obejmuje `UPDATE` uprawnienie dla każdej tabeli na tym schemacie. Podobnie jak w przypadku większości systemów, odmowa przyznania uprawnienia kasuje przyznanie. Ze względu na zagnieżdżoną naturę uprawnień oraz ich liczbę zaprojektowanie systemu zabezpieczającego bazę danych w prawidłowy sposób może wymagać starannej analizy. Rozpocznij od listy uprawnień [Uprawnienia (aparat bazy danych)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) i przejrzyj [obszerny wykaz](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) uprawnień.


### <a name="considerations-and-restrictions"></a>Uwagi i ograniczenia

Podczas zarządzania nazwami logowania i użytkownikami w usłudze SQL Database należy uwzględnić następujące fakty:

- Podczas wykonywania instrukcji `CREATE/ALTER/DROP DATABASE` musisz mieć połączenie z bazą danych **master**.   
- Użytkownik bazy danych odpowiadający identyfikatorowi logowania **Administrator serwera** nie może zostać zmieniony ani usunięty. 
- Domyślnym językiem identyfikatora logowania **Administrator serwera** jest angielski (Stany Zjednoczone).
- Tylko administratorzy (identyfikator logowania **Administrator serwera** lub Administrator usługi Azure AD) i członkowie roli bazy danych **dbmanager** w bazie danych **master** mają uprawnienia do wykonywania instrukcji `CREATE DATABASE` i `DROP DATABASE`.
- Podczas wykonywania instrukcji `CREATE/ALTER/DROP LOGIN` musisz mieć połączenie z bazą danych master. Nie zaleca się jednak używania nazw logowania. Zamiast tego korzystaj z użytkowników zawartej bazy danych.
- Aby połączyć się z bazą danych użytkownika, podaj nazwę bazy danych w parametrach połączenia.
- Tylko główna nazwa logowania na poziomie serwera i członkowie roli bazy danych **loginmanager** w bazie danych **master** mają uprawnienia do wykonywania instrukcji `CREATE LOGIN`, `ALTER LOGIN` i `DROP LOGIN`.
- Podczas wykonywania instrukcji `CREATE/ALTER/DROP LOGIN` i `CREATE/ALTER/DROP DATABASE` w aplikacji ADO.NET niedozwolone jest używanie poleceń sparametryzowanych. Aby uzyskać więcej informacji, zobacz artykuł [Polecenia i parametry](https://msdn.microsoft.com/library/ms254953.aspx).
- Podczas wykonywania instrukcji `CREATE/ALTER/DROP DATABASE` i `CREATE/ALTER/DROP LOGIN` każda z tych instrukcji musi być jedyną instrukcją w zadaniu wsadowym języka Transact-SQL. W przeciwnym razie wystąpi błąd. Na przykład następująca instrukcja języka Transact-SQL sprawdza, czy baza danych istnieje. Jeśli baza istnieje, jest wywoływana instrukcja `DROP DATABASE` w celu jej usunięcia. Ponieważ instrukcja `DROP DATABASE` nie jest jedyną instrukcją w zadaniu wsadowym, wykonywanie następującej instrukcji języka Transact-SQL spowoduje błąd.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Zamiast tego należy użyć następującej instrukcji języka Transact-SQL:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- Podczas wykonywania instrukcji `CREATE USER` z opcją `FOR/FROM LOGIN` musi to być jedyna instrukcja w zadaniu wsadowym języka Transact-SQL.
- Podczas wykonywania instrukcji `ALTER USER` z opcją `WITH LOGIN` musi to być jedyna instrukcja w zadaniu wsadowym języka Transact-SQL.
- Aby wykonać instrukcję `CREATE/ALTER/DROP`, użytkownik musi mieć uprawnienia `ALTER ANY USER` dla bazy danych.
- Gdy właściciel roli bazy danych próbuje dodać lub usunąć innego użytkownika bazy danych do lub z tej roli bazy danych, może wystąpić następujący błąd: **Użytkownik lub rola "name" nie istnieje w tej bazie danych.** Ten błąd występuje, ponieważ użytkownik nie jest widoczny dla właściciela. Aby rozwiązać ten problem, należy udzielić właścicielowi roli uprawnień `VIEW DEFINITION` do użytkownika. 


## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o regułach zapory, zobacz artykuł dotyczący [zapory usługi Azure SQL Database](sql-database-firewall-configure.md).
- Aby zobaczyć przegląd wszystkich funkcji zabezpieczeń usługi SQL Database, zobacz [omówienie zabezpieczeń usługi SQL](sql-database-security-overview.md).
- Aby zapoznać się z samouczkiem, zobacz temat [zabezpieczanie Azure SQL Database](sql-database-security-tutorial.md).
- Aby uzyskać informacje o widokach i procedurach składowanych, zobacz artykuł dotyczący [tworzenia widoków i procedur składowanych](https://msdn.microsoft.com/library/ms365311.aspx)
- Aby uzyskać informacje o udzielaniu dostępu do obiektu bazy danych, zobacz artykuł dotyczący [udzielania dostępu do obiektu bazy danych](https://msdn.microsoft.com/library/ms365327.aspx)
