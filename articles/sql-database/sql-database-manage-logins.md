---
title: Identyfikatory logowania i użytkownicy bazy danych SQL platformy Azure | Microsoft Docs
description: Więcej informacji na temat zarządzania zabezpieczeniami bazy danych SQL, w szczególności o zarządzaniu zabezpieczeniami dostępu i logowania za pomocą konta głównego poziomu serwera.
keywords: zabezpieczenia bazy danych sql, zarządzanie zabezpieczeniami bazy danych, zabezpieczenia logowania, zabezpieczenia bazy danych, dostęp do bazy danych
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/16/2018
ms.author: carlrab
ms.openlocfilehash: 54bf692f35e2529fe7d0b14684c9acc7d66b9903
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="controlling-and-granting-database-access"></a>Kontrolowanie i udzielanie dostępu do bazy danych

Po skonfigurowaniu reguł zapory osób może nawiązać połączenia bazy danych SQL jako jednego z kont administratorów, jako właściciel bazy danych lub jako użytkownika bazy danych w bazie danych.  

>  [!NOTE]  
>  Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database. 
>

> [!TIP]
> Samouczek, zobacz [zabezpieczenia bazy danych SQL Azure](sql-database-security-tutorial.md).
>


## <a name="unrestricted-administrative-accounts"></a>Konta z uprawnieniami administracyjnymi bez ograniczeń
Istnieją dwa konta z uprawnieniami administracyjnymi (**Administrator serwera** i **Administrator usługi Active Directory**), które funkcjonują jako administratorzy. Aby zidentyfikować te konta administratora dla używanego serwera SQL, otwórz witrynę Azure Portal i przejdź do właściwości serwera SQL.

![Administratorzy serwera SQL](./media/sql-database-manage-logins/sql-admins.png)

- **Administrator serwera**   
Podczas tworzenia serwera Azure SQL musi zostać podany **identyfikator logowania administratora serwera**. Serwer SQL tworzy to konto jako identyfikator logowania w bazie danych master. To konto używa do połączenia uwierzytelnienia programu SQL Server (nazwy użytkownika i hasła). Może istnieć tylko jedno z tych kont.   
- **Administrator usługi Azure Active Directory**   
Jako konto administratora można również skonfigurować jedno konto usługi Azure Active Directory (indywidualne lub grupy zabezpieczeń). Skonfigurowanie administratora usługi Azure AD jest opcjonalne, ale aby można było połączyć się z usługą SQL Database za pomocą kont usługi Azure AD, musi być on skonfigurowany. Aby uzyskać więcej informacji na temat konfigurowania dostępu do usługi Azure Active Directory, zobacz artykuły [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Łączenie się z usługą SQL Database lub SQL Data Warehouse przy użyciu uwierzytelnienia usługi Azure Active Directory) i [SSMS support for Azure AD MFA with SQL Database and SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) (Obsługa programu SSMS w usłudze Azure AD MFA przy użyciu usługi SQL Database i SQL Data Warehouse).
 

Konta **Administrator serwera** i **Administrator usługi Azure AD** mają następujące cechy:
- Są to jedyne konta, które mogą się automatycznie łączyć z dowolną bazą danych SQL Database na serwerze. (Aby połączyć się z bazą danych użytkownika, inne konta muszą być właścicielem bazy danych lub mieć konto użytkownika w bazie danych użytkownika).
- Te konta korzystają z baz danych użytkowników jako użytkownik `dbo` i mają wszystkie uprawnienia w bazach danych użytkowników. (Właściciel bazy danych użytkownika również korzysta z bazy danych jako użytkownik `dbo`). 
- Te konta nie korzystają z bazy danych `master` jako użytkownik `dbo` i mają ograniczone uprawnienia w tej bazie danych. 
- Te konta nie są członkami standardowej stałej roli `sysadmin` programu SQL Server, która nie jest dostępna w bazie danych SQL.  
- Te konta mogą tworzyć, modyfikować i usuwać bazy danych, identyfikatory logowania, użytkowników w bazie master oraz reguły zapory na poziomie serwera.
- Te konta mogą dodawać członków do ról `dbmanager` i `loginmanager` oraz usuwać ich z nich.
- Te konta mogą wyświetlać tabelę systemową `sys.sql_logins`.

### <a name="configuring-the-firewall"></a>Konfigurowanie zapory
W przypadku skonfigurowania zapory na poziomie serwera za pomocą pojedynczego adresu IP lub zakresu adresów konta **Administrator serwera SQL** i **Administrator usługi Azure Active Directory** mogą łączyć się z bazą danych master i wszystkimi bazami danych użytkowników. Początkowo zaporę na poziomie serwera można skonfigurować za pomocą [witryny Azure Portal](sql-database-get-started-portal.md), programu [PowerShell](sql-database-get-started-powershell.md) lub [interfejsu API REST](https://msdn.microsoft.com/library/azure/dn505712.aspx). Po nawiązaniu połączenia można również skonfigurować dodatkowe reguły zapory na poziomie serwera za pomocą [języka Transact-SQL](sql-database-configure-firewall-settings.md).

### <a name="administrator-access-path"></a>Ścieżka dostępu administratora
Po poprawnym skonfigurowaniu zapory na poziomie serwera konta **Administrator serwera SQL** i **Administrator usługi Azure Active Directory** mogą łączyć się przy użyciu narzędzi klienckich, takich jak SQL Server Management Studio lub SQL Server Data Tools. Tylko najnowsze narzędzia oferują wszystkie funkcje i możliwości. Na poniższym diagramie przedstawiono typową konfigurację dla tych dwóch kont administracyjnych.

![Ścieżka dostępu administratora](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Używając otwartego portu w zaporze na poziomie serwera, administratorzy mogą połączyć się z dowolną bazą danych SQL.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Łączenie się z bazą danych przy użyciu programu SQL Server Management Studio
Aby zapoznać się z omówieniem tworzenia serwera, bazy danych i reguł zapory na poziomie serwera oraz używania programu SQL Server Management Studio do odpytywania bazy danych, zobacz [Wprowadzenie do serwerów, baz danych i reguł zapory usługi Azure SQL Database przy użyciu witryny Azure Portal i programu SQL Server Management Studio](sql-database-get-started-portal.md).

> [!IMPORTANT]
> Zalecane jest używanie najnowszej wersji programu Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="additional-server-level-administrative-roles"></a>Dodatkowe role administracyjne na poziomie serwera
Oprócz ról administracyjnych na poziomie serwera omówionych wcześniej usługa SQL Database zawiera dwie ograniczone role administracyjne w bazie danych master, do których można dodać konta użytkowników, aby udzielić im uprawnień do tworzenia baz danych lub zarządzania identyfikatorami logowania.

### <a name="database-creators"></a>Kreatory bazy danych
Jedna z tych ról administracyjnych to rola **dbmanager**. Członkowie tej roli mogą tworzyć nowe bazy danych. Aby użyć tej roli, należy utworzyć użytkownika w bazie danych `master` i dodać go do roli bazy danych **dbmanager**. Aby utworzyć bazę danych, użytkownik musi być użytkownikiem bazującym na identyfikatorze logowania do serwera SQL Server w bazie danych master lub użytkownikiem zawartej bazy danych bazującym na użytkowniku usługi Azure Active Directory.

1. Połącz się z bazą danych master przy użyciu konta administratora.
2. Krok opcjonalny: Utwórz nazwę logowania do uwierzytelniania w programie SQL Server przy użyciu instrukcji [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx). Przykładowa instrukcja:
   
   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```
   
   > [!NOTE]
   > Podczas tworzenia nazwy logowania lub użytkownika zawartej bazy danych należy używać silnego hasła. Aby uzyskać więcej informacji, zobacz artykuł [Silne hasła](https://msdn.microsoft.com/library/ms161962.aspx).
    
   W celu poprawy wydajności nazwy logowania (nazwy główne na poziomie serwera) są tymczasowo przechowywane w pamięci podręcznej na poziomie bazy danych. Aby odświeżyć pamięć podręczną uwierzytelniania, zobacz artykuł [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

3. W bazie danych master utwórz użytkownika za pomocą instrukcji [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx). Użytkownik może być użytkownikiem uwierzytelnienia zawartej bazy danych usługi Azure Active Directory (jeśli skonfigurowano środowisko dla uwierzytelniania usługi Azure AD), użytkownikiem uwierzytelnienia zawartej bazy danych programu SQL Server lub użytkownikiem uwierzytelniania programu SQL Server w oparciu o nazwę logowania uwierzytelniania programu SQL Server (utworzonym w poprzednim kroku). Przykładowe instrukcje:
   
   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Tran WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Dodaj nowego użytkownika do roli bazy danych **dbmanager** przy użyciu instrukcji [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx). Przykładowe instrukcje:
   
   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```
   
   > [!NOTE]
   > Dbmanager jest rolą bazy danych w bazie danych master, więc do roli dbmanager można dodać tylko użytkownika bazy danych. Do roli na poziomie bazy danych nie można dodać nazwy logowania na poziomie serwera.
    
5. W razie potrzeby skonfiguruj regułę zapory, aby umożliwić połączenie się nowemu użytkownikowi. (Nowy użytkownik może być objęty istniejącą regułą zapory).

Teraz użytkownik może łączyć się z bazą danych master i tworzyć nowe bazy danych. Konto tworzące bazę danych staje się właścicielem bazy danych.

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

Aby przydzielić dodatkowym użytkownikom pełną kontrolę nad bazą danych, należy uczynić ich członkami ustalonej roli bazy danych **db_owner** za pomocą instrukcji `ALTER ROLE`.

```sql
ALTER ROLE db_owner ADD MEMBER Mary; 
```

> [!NOTE]
> Najczęściej zdarza się utworzyć użytkownika bazy danych, w oparciu logowania serwera logicznego jest przeznaczony dla użytkowników wymagających dostępu do wielu baz danych. Ponieważ zawarte bazy danych użytkowników są poszczególnych jednostek, każda baza danych zachowuje własną i własnego hasła. Może to spowodować obciążenie, użytkownik musi należy pamiętać, aby każdy hasła dla każdej bazy danych, a jego może stać się trudną w przypadku konieczności zmiany wiele haseł dla wielu baz danych. Jednak gdy przy użyciu logowania do programu SQL Server i wysokiej dostępności (aktywna replikacja geograficzna i trybu failover grupy), logowania do programu SQL Server musi mieć wartość ręcznie na każdym serwerze. W przeciwnym razie użytkownika bazy danych będą już mapowane na nazwę logowania serwera po przejściu w tryb failover występuje i nie będzie mieć możliwość dostępu trybu failover post bazy danych. Aby uzyskać więcej informacji na temat konfigurowania nazwy logowania dla — replikacja geograficzna, zobacz [Konfigurowanie i zarządzanie nimi zabezpieczenia bazy danych SQL Azure geograficzne lub pracy awaryjnej](sql-database-geo-replication-security-config.md).

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

* Podczas wykonywania instrukcji `CREATE/ALTER/DROP DATABASE` musisz mieć połączenie z bazą danych **master**.   
* Użytkownik bazy danych odpowiadający identyfikatorowi logowania **Administrator serwera** nie może zostać zmieniony ani usunięty. 
* Domyślnym językiem identyfikatora logowania **Administrator serwera** jest angielski (Stany Zjednoczone).
* Tylko administratorzy (identyfikator logowania **Administrator serwera** lub Administrator usługi Azure AD) i członkowie roli bazy danych **dbmanager** w bazie danych **master** mają uprawnienia do wykonywania instrukcji `CREATE DATABASE` i `DROP DATABASE`.
* Podczas wykonywania instrukcji `CREATE/ALTER/DROP LOGIN` musisz mieć połączenie z bazą danych master. Nie zaleca się jednak używania nazw logowania. Zamiast tego korzystaj z użytkowników zawartej bazy danych.
* Aby połączyć się z bazą danych użytkownika, podaj nazwę bazy danych w parametrach połączenia.
* Tylko główna nazwa logowania na poziomie serwera i członkowie roli bazy danych **loginmanager** w bazie danych **master** mają uprawnienia do wykonywania instrukcji `CREATE LOGIN`, `ALTER LOGIN` i `DROP LOGIN`.
* Podczas wykonywania instrukcji `CREATE/ALTER/DROP LOGIN` i `CREATE/ALTER/DROP DATABASE` w aplikacji ADO.NET niedozwolone jest używanie poleceń sparametryzowanych. Aby uzyskać więcej informacji, zobacz artykuł [Polecenia i parametry](https://msdn.microsoft.com/library/ms254953.aspx).
* Podczas wykonywania instrukcji `CREATE/ALTER/DROP DATABASE` i `CREATE/ALTER/DROP LOGIN` każda z tych instrukcji musi być jedyną instrukcją w zadaniu wsadowym języka Transact-SQL. W przeciwnym razie wystąpi błąd. Na przykład następująca instrukcja języka Transact-SQL sprawdza, czy baza danych istnieje. Jeśli baza istnieje, jest wywoływana instrukcja `DROP DATABASE` w celu jej usunięcia. Ponieważ instrukcja `DROP DATABASE` nie jest jedyną instrukcją w zadaniu wsadowym, wykonywanie następującej instrukcji języka Transact-SQL spowoduje błąd.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```

* Podczas wykonywania instrukcji `CREATE USER` z opcją `FOR/FROM LOGIN` musi to być jedyna instrukcja w zadaniu wsadowym języka Transact-SQL.
* Podczas wykonywania instrukcji `ALTER USER` z opcją `WITH LOGIN` musi to być jedyna instrukcja w zadaniu wsadowym języka Transact-SQL.
* Aby wykonać instrukcję `CREATE/ALTER/DROP`, użytkownik musi mieć uprawnienia `ALTER ANY USER` dla bazy danych.
* Gdy właściciel roli database próbuje dodać lub usunąć innego użytkownik bazy danych z roli database, może wystąpić następujący błąd: **User or role 'Name' does not exist in this database** (Użytkownik lub rola „Nazwa” nie istnieje w tej bazie danych). Ten błąd występuje, ponieważ użytkownik nie jest widoczny dla właściciela. Aby rozwiązać ten problem, należy udzielić właścicielowi roli uprawnień `VIEW DEFINITION` do użytkownika. 


## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o regułach zapory, zobacz artykuł dotyczący [zapory usługi Azure SQL Database](sql-database-firewall-configure.md).
- Aby zobaczyć przegląd wszystkich funkcji zabezpieczeń usługi SQL Database, zobacz [omówienie zabezpieczeń usługi SQL](sql-database-security-overview.md).
- Samouczek, zobacz [zabezpieczenia bazy danych SQL Azure](sql-database-security-tutorial.md).
- Aby uzyskać informacje o widokach i procedurach składowanych, zobacz artykuł dotyczący [tworzenia widoków i procedur składowanych](https://msdn.microsoft.com/library/ms365311.aspx)
- Aby uzyskać informacje o udzielaniu dostępu do obiektu bazy danych, zobacz artykuł dotyczący [udzielania dostępu do obiektu bazy danych](https://msdn.microsoft.com/library/ms365327.aspx)
