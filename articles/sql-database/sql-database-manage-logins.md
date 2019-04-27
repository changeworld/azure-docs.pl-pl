---
title: Identyfikatory logowania i użytkownicy bazy danych SQL platformy Azure | Microsoft Docs
description: Dowiedz się więcej o zarządzanie zabezpieczeniami bazy danych SQL Database i SQL Data Warehouse, w szczególności, jak zarządzać zabezpieczeniami dostępu i logowania za pomocą konta głównego poziomu serwera bazy danych.
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
manager: craigg
ms.date: 03/26/2019
ms.openlocfilehash: b1e952d9af474e2318ef91a6bdcc2605a3c30018
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702342"
---
# <a name="controlling-and-granting-database-access-to-sql-database-and-sql-data-warehouse"></a>Kontrolowanie i udzielanie dostępu do bazy danych SQL Database i SQL Data Warehouse

Po przeprowadzeniu konfiguracji reguły zapory, możesz nawiązać połączenie Azure [bazy danych SQL](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) jako jeden z konta administratora, jako właściciel bazy danych lub jako użytkownika bazy danych w bazie danych.  

> [!NOTE]  
> Ten temat dotyczy serwera Azure SQL i baz danych SQL Database i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database. 
> [!TIP]
> Aby zapoznać się z samouczkiem, zobacz [Zabezpieczanie usługi Azure SQL Database](sql-database-security-tutorial.md). W tym samouczku nie ma zastosowania do **wystąpienia zarządzanego Azure SQL Database**.

## <a name="unrestricted-administrative-accounts"></a>Konta z uprawnieniami administracyjnymi bez ograniczeń

Istnieją dwa konta z uprawnieniami administracyjnymi (**Administrator serwera** i **Administrator usługi Active Directory**), które funkcjonują jako administratorzy. Aby zidentyfikować te konta administratora serwera SQL, otwórz witrynę Azure portal i przejdź do karty właściwości serwera SQL lub bazy danych SQL.

![Administratorzy serwera SQL](media/sql-database-manage-logins/sql-admins.png)

- **Administrator serwera**

  Podczas tworzenia serwera Azure SQL musi zostać podany **identyfikator logowania administratora serwera**. Serwer SQL tworzy to konto jako identyfikator logowania w bazie danych master. To konto używa do połączenia uwierzytelnienia programu SQL Server (nazwy użytkownika i hasła). Może istnieć tylko jedno z tych kont.

  > [!NOTE]
  > Aby zresetować hasło administratora serwera, przejdź do [witryny Azure portal](https://portal.azure.com), kliknij przycisk **serwerów SQL**, wybierz serwer z listy, a następnie kliknij przycisk **resetowania hasła**.

- **Administrator usługi Active Directory systemu Azure**

  Jako konto administratora można również skonfigurować jedno konto usługi Azure Active Directory (indywidualne lub grupy zabezpieczeń). Opcjonalnie można skonfigurować, administrator usługi Azure AD, ale administrator usługi Azure AD **musi** skonfigurowane, jeśli chcesz nawiązać połączenie z bazą danych SQL przy użyciu konta usługi Azure AD. Aby uzyskać więcej informacji na temat konfigurowania dostępu do usługi Azure Active Directory, zobacz artykuły [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Łączenie się z usługą SQL Database lub SQL Data Warehouse przy użyciu uwierzytelnienia usługi Azure Active Directory) i [SSMS support for Azure AD MFA with SQL Database and SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) (Obsługa programu SSMS w usłudze Azure AD MFA przy użyciu usługi SQL Database i SQL Data Warehouse).

Konta **Administrator serwera** i **Administrator usługi Azure AD** mają następujące cechy:

- Są to jedyne konta, które mogą automatycznie łączyć się z dowolną bazą danych SQL na serwerze. (Aby połączyć się z bazą danych użytkownika, inne konta muszą być właścicielem bazy danych lub mieć konto użytkownika w bazie danych użytkownika).
- Te konta korzystają z baz danych użytkowników jako użytkownik `dbo` i mają wszystkie uprawnienia w bazach danych użytkowników. (Właściciel bazy danych użytkownika również korzysta z bazy danych jako użytkownik `dbo`). 
- Nie należy wprowadzać `master` bazy danych jako `dbo` użytkownika i mają ograniczone uprawnienia w części głównej. 
- Czy **nie** członkowie standard programu SQL Server `sysadmin` stałej roli serwera, który nie jest dostępny w usłudze SQL database.  
- Można tworzyć, alter i drop baz danych, identyfikatory logowania i użytkowników w bazie master oraz poziom serwera reguły zapory IP.
- Można dodawać i usuwać elementy członkowskie do `dbmanager` i `loginmanager` ról.
- Można wyświetlić `sys.sql_logins` tabeli systemowej.

### <a name="configuring-the-firewall"></a>Konfigurowanie zapory

W przypadku skonfigurowania zapory na poziomie serwera za pomocą pojedynczego adresu IP lub zakresu adresów konta **Administrator serwera SQL** i **Administrator usługi Azure Active Directory** mogą łączyć się z bazą danych master i wszystkimi bazami danych użytkowników. Początkowo zaporę na poziomie serwera można skonfigurować za pomocą [witryny Azure Portal](sql-database-single-database-get-started.md), programu [PowerShell](sql-database-powershell-samples.md) lub [interfejsu API REST](https://msdn.microsoft.com/library/azure/dn505712.aspx). Po nawiązaniu połączenia, dodatkowe reguły zapory IP w poziomie serwera można również skonfigurować za pomocą [języka Transact-SQL](sql-database-configure-firewall-settings.md).

### <a name="administrator-access-path"></a>Ścieżka dostępu administratora

Po poprawnym skonfigurowaniu zapory na poziomie serwera konta **Administrator serwera SQL** i **Administrator usługi Azure Active Directory** mogą łączyć się przy użyciu narzędzi klienckich, takich jak SQL Server Management Studio lub SQL Server Data Tools. Tylko najnowsze narzędzia oferują wszystkie funkcje i możliwości. Na poniższym diagramie przedstawiono typową konfigurację dla tych dwóch kont administracyjnych.

![Ścieżka dostępu administratora](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Używając otwartego portu w zaporze na poziomie serwera, administratorzy mogą połączyć się z dowolną bazą danych SQL.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Łączenie się z bazą danych przy użyciu programu SQL Server Management Studio

Aby uzyskać szczegółowe omówienie tworzenia serwera, bazy danych i reguły zapory IP poziomu serwera i zapytanie dotyczące bazy danych przy użyciu programu SQL Server Management Studio, zobacz [wprowadzenie do serwerów, baz danych i reguł zapory usługi Azure SQL Database przy użyciu witryny Azure portal i SQL Server Management Studio](sql-database-single-database-get-started.md).

> [!IMPORTANT]
> Zalecane jest używanie najnowszej wersji programu Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="additional-server-level-administrative-roles"></a>Dodatkowe role administracyjne na poziomie serwera

>[!IMPORTANT]
>Ta sekcja nie ma zastosowania do **wystąpienia zarządzanego Azure SQL Database** te role są specyficzne dla **usługi Azure SQL Database**.

Oprócz ról administracyjnych na poziomie serwera omówionych wcześniej usługa SQL Database zawiera dwie ograniczone role administracyjne w bazie danych master, do których można dodać konta użytkowników, aby udzielić im uprawnień do tworzenia baz danych lub zarządzania identyfikatorami logowania.

### <a name="database-creators"></a>Kreatory bazy danych

Jedna z tych ról administracyjnych to rola **dbmanager**. Członkowie tej roli mogą tworzyć nowe bazy danych. Aby użyć tej roli, należy utworzyć użytkownika w bazie danych `master` i dodać go do roli bazy danych **dbmanager**. Aby utworzyć bazę danych, użytkownik musi być użytkownikiem opartym na identyfikatorze logowania programu SQL Server, w `master` bazy danych lub użytkownika zawartej bazy danych na podstawie usługi Azure Active Directory użytkownika.

1. Przy użyciu konta administratora, połączyć się z `master` bazy danych.
2. Utwórz identyfikator logowania uwierzytelniania programu SQL Server przy użyciu [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) instrukcji. Przykładowa instrukcja:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Podczas tworzenia nazwy logowania lub użytkownika zawartej bazy danych należy używać silnego hasła. Aby uzyskać więcej informacji, zobacz artykuł [Silne hasła](https://msdn.microsoft.com/library/ms161962.aspx).

   W celu poprawy wydajności nazwy logowania (nazwy główne na poziomie serwera) są tymczasowo przechowywane w pamięci podręcznej na poziomie bazy danych. Aby odświeżyć pamięć podręczną uwierzytelniania, zobacz artykuł [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

3. W `master` bazy danych, Utwórz użytkownika za pomocą [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) instrukcji. Użytkownik może być użytkownikiem uwierzytelnienia zawartej bazy danych usługi Azure Active Directory (jeśli skonfigurowano środowisko dla uwierzytelniania usługi Azure AD), użytkownikiem uwierzytelnienia zawartej bazy danych programu SQL Server lub użytkownikiem uwierzytelniania programu SQL Server w oparciu o nazwę logowania uwierzytelniania programu SQL Server (utworzonym w poprzednim kroku). Przykładowe instrukcje:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Dodawanie nowego użytkownika do **dbmanager** roli bazy danych w `master` przy użyciu [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) instrukcji. Przykładowe instrukcje:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > Dbmanager jest rolą bazy danych w bazie danych master, więc do roli dbmanager można dodać tylko użytkownika bazy danych. Do roli na poziomie bazy danych nie można dodać nazwy logowania na poziomie serwera.

5. W razie potrzeby skonfiguruj regułę zapory, aby umożliwić połączenie się nowemu użytkownikowi. (Nowy użytkownik może być objęty istniejącą regułą zapory).

Teraz użytkownik może łączyć się `master` bazy danych i może tworzyć nowe bazy danych. Konto tworzące bazę danych staje się właścicielem bazy danych.

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

Aby przydzielić dodatkowym użytkownikom pełną kontrolę nad bazy danych, należy wprowadzić je jako członka **db_owner** stałej roli bazy danych.

W przypadku użycia usługi Azure SQL Database `ALTER ROLE` instrukcji.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Azure SQL Data Warehouse używany [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).
```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```


> [!NOTE]
> Jest jednym z typowych powodów Utwórz użytkownika bazy danych, w oparciu o identyfikator logowania do bazy danych programu SQL server dla użytkowników, którzy muszą mieć dostęp do wielu baz danych. Ponieważ zawartych użytkowników bazy danych są poszczególnymi jednostkami, każda baza danych przechowuje własny użytkownika i własne hasło. Może to powodować obciążenie, użytkownik musi następnie pamiętam każdego dla każdej bazy danych i może stać się trudną w przypadku konieczności zmiany wiele haseł dla wielu baz danych. Jednak gdy przy użyciu nazwy logowania programu SQL Server i wysokiej dostępności (aktywna replikacja geograficzna i grupy trybu failover), dane logowania programu SQL Server musi mieć wartość ręcznie na każdym serwerze. W przeciwnym razie użytkownika bazy danych będzie już można zamapować na nazwy logowania serwera, po przejściu w tryb failover występuje i nie będą mogli korzystać z trybu failover wpis w bazie danych. Aby uzyskać więcej informacji na temat konfigurowania logowania dla replikacji geograficznej, zobacz [Konfigurowanie i zarządzanie zabezpieczeniami usługi Azure SQL Database, przywracanie geograficzne lub pracy awaryjnej](sql-database-geo-replication-security-config.md).

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
  
  Zamiast tego należy użyć następujących instrukcji języka Transact-SQL:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- Podczas wykonywania instrukcji `CREATE USER` z opcją `FOR/FROM LOGIN` musi to być jedyna instrukcja w zadaniu wsadowym języka Transact-SQL.
- Podczas wykonywania instrukcji `ALTER USER` z opcją `WITH LOGIN` musi to być jedyna instrukcja w zadaniu wsadowym języka Transact-SQL.
- Aby wykonać instrukcję `CREATE/ALTER/DROP`, użytkownik musi mieć uprawnienia `ALTER ANY USER` dla bazy danych.
- Gdy właściciel roli database próbuje dodać lub usunąć innego użytkownik bazy danych z tej roli bazy danych, może wystąpić następujący błąd: **Użytkownik lub rola "Name" nie istnieje w tej bazie danych.** Ten błąd występuje, ponieważ użytkownik nie jest widoczny dla właściciela. Aby rozwiązać ten problem, należy udzielić właścicielowi roli uprawnień `VIEW DEFINITION` do użytkownika. 


## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o regułach zapory, zobacz artykuł dotyczący [zapory usługi Azure SQL Database](sql-database-firewall-configure.md).
- Aby zobaczyć przegląd wszystkich funkcji zabezpieczeń usługi SQL Database, zobacz [omówienie zabezpieczeń usługi SQL](sql-database-security-overview.md).
- Aby zapoznać się z samouczkiem, zobacz [Zabezpieczanie usługi Azure SQL Database](sql-database-security-tutorial.md).
- Aby uzyskać informacje o widokach i procedurach składowanych, zobacz artykuł dotyczący [tworzenia widoków i procedur składowanych](https://msdn.microsoft.com/library/ms365311.aspx)
- Aby uzyskać informacje o udzielaniu dostępu do obiektu bazy danych, zobacz artykuł dotyczący [udzielania dostępu do obiektu bazy danych](https://msdn.microsoft.com/library/ms365327.aspx)
