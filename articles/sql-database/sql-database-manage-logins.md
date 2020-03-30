---
title: Autoryzowanie dostępu do serwera i bazy danych przy użyciu loginów i kont użytkowników
description: Dowiedz się, jak usługa Azure SQL Database i usługa Azure Synapse Analytics uwierzytelniają użytkowników w celu uzyskania dostępu przy użyciu loginów i kont użytkowników. Dowiedz się również, jak role bazy danych i jawne uprawnienia do autoryzowania logowania i użytkowników do wykonywania akcji i danych zapytań.
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
ms.date: 03/23/2020
ms.openlocfilehash: 98c15fe11b64e8c177e60a2ea1eb7c50eaf69353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124801"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Autoryzowanie dostępu do bazy danych uwierzytelnionym użytkownikom do bazy danych SQL i usługi Azure Synapse Analytics przy użyciu loginów i kont użytkowników

W tym artykule dowiesz się o:

- Opcje konfigurowania usługi Azure SQL Database i usługi Azure Synapse Analytics (dawniej Usługi Azure SQL Data Warehouse), aby umożliwić użytkownikom wykonywanie zadań administracyjnych i dostęp do danych przechowywanych w tych bazach danych.
- Konfiguracja dostępu i autoryzacji po początkowym utworzeniu nowej bazy danych SQL Azure
- Jak dodać loginy i konta użytkowników w głównej bazie danych i kontach użytkowników, a następnie udzielić tych kont uprawnień administracyjnych
- Jak dodać konta użytkowników w bazach danych użytkowników, skojarzone z loginami lub jako zamknięte konta użytkowników
- Konfigurowanie kont użytkowników z uprawnieniami w bazach danych użytkowników przy użyciu ról bazy danych i uprawnień jawnych

> [!IMPORTANT]
> Bazy danych w usłudze Azure SQL Database i usłudze Azure Synapse Analytics (dawniej Usługa Azure SQL Data Warehouse) są określane łącznie w pozostałej części tego artykułu jako bazy danych lub usługi Azure SQL (dla uproszczenia).

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

[**Uwierzytelnianie**](sql-database-security-overview.md#authentication) jest procesem udowodnienia, że użytkownik jest tym, za kogo się podają. Użytkownik łączy się z bazą danych przy użyciu konta użytkownika.
Gdy użytkownik próbuje połączyć się z bazą danych, podaje konto użytkownika i informacje uwierzytelniania. Użytkownik jest uwierzytelniony przy użyciu jednej z następujących dwóch metod uwierzytelniania:

- [Uwierzytelnianie SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Za pomocą tej metody uwierzytelniania użytkownik przesyła nazwę konta użytkownika i skojarzone hasło w celu nawiązania połączenia. To hasło jest przechowywane w głównej bazie danych dla kont użytkowników połączonych z logowaniem lub przechowywanych w bazie danych zawierającej konto użytkownika dla kont użytkowników niepowiązanych z logowaniem.
- [Uwierzytelnianie usługi Azure Active Directory](sql-database-aad-authentication.md)

  Za pomocą tej metody uwierzytelniania użytkownik przesyła nazwę konta użytkownika i żąda, aby usługa używała informacji o poświadczeniach przechowywanych w usłudze Azure Active Directory.

**Logowania i użytkowników:** W usłudze Azure SQL konto użytkownika w bazie danych może być skojarzone z logowaniem, który jest przechowywany w głównej bazie danych lub może być nazwą użytkownika, która jest przechowywana w pojedynczej bazie danych.

- **Logowanie** to indywidualne konto w głównej bazie danych, z którym można połączyć konto użytkownika w jednej lub kilku bazach danych. Za pomocą logowania informacje o poświadczeniach dla konta użytkownika są przechowywane przy użyciu logowania.
- **Konto użytkownika** jest kontem indywidualnym w dowolnej bazie danych, które może być, ale nie musi być połączone z loginem. W za pomocą konta użytkownika, które nie jest połączone z logowaniem, informacje o poświadczeniach są przechowywane przy użyciu konta użytkownika.

[**Autoryzacja**](sql-database-security-overview.md#authorization) dostępu do danych i wykonywania różnych akcji są zarządzane przy użyciu ról bazy danych i uprawnień jawnych. Autoryzacja odnosi się do uprawnień przypisanych do użytkownika i określa, co ten użytkownik może zrobić. Autoryzacja jest kontrolowana przez [członkostwo w roli](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) bazy danych konta użytkownika i [uprawnienia na poziomie obiektu.](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Istniejące loginy i konta użytkowników po utworzeniu nowej bazy danych

Podczas tworzenia pierwszego wdrożenia sql platformy Azure, należy określić logowania administratora i skojarzone hasło dla tego logowania. To konto administracyjne nosi nazwę **Administrator serwera**. Podczas wdrażania występuje następująca konfiguracja logowania i użytkowników w bazach danych głównych i użytkowników:

- Logowanie SQL z uprawnieniami administracyjnymi jest tworzone przy użyciu określonej nazwy logowania. [Logowanie](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) to indywidualne konta użytkowników do logowania się do bazy danych SQL.
- Ten login otrzymuje pełne uprawnienia administracyjne do wszystkich baz danych jako [podmiot na poziomie serwera.](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) Ten login ma wszystkie dostępne uprawnienia w bazie danych SQL i nie może być ograniczony. W wystąpieniu zarządzanym ten identyfikator jest dodawany do [roli serwera stałego sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (ta rola nie istnieje w pojedynczych lub pooled baz danych).
- [Konto użytkownika](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) `dbo` wywoływane jest tworzony dla tego logowania w każdej bazie danych użytkownika. Użytkownik [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) ma wszystkie uprawnienia bazy danych w bazie `db_owner` danych i jest mapowany do roli stałej bazy danych. Dodatkowe role stałej bazy danych są omówione w dalszej części tego artykułu.

Aby zidentyfikować konta administratora bazy danych, otwórz witrynę Azure portal i przejdź do karty **Właściwości** serwera lub wystąpienia zarządzanego.

![Administratorzy serwera SQL](media/sql-database-manage-logins/sql-admins.png)

![Administratorzy serwera SQL](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> Nie można zmienić nazwy logowania administratora po jej utworzeniu. Aby zresetować hasło administratora serwera logicznego, przejdź do [witryny Azure Portal](https://portal.azure.com), kliknij pozycję **Serwery SQL**, wybierz serwer z listy, a następnie kliknij przycisk **Resetuj hasło**. Aby zresetować hasło serwera wystąpienia zarządzanego, przejdź do witryny Azure Portal, kliknij wystąpienie i kliknij pozycję **Resetuj hasło**. Można również użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Tworzenie dodatkowych loginów i użytkowników z uprawnieniami administracyjnymi

W tym momencie wystąpienie sql platformy Azure jest skonfigurowany tylko do dostępu przy użyciu jednego logowania SQL i konta użytkownika. Aby utworzyć dodatkowe logowania z pełnymi lub częściowymi uprawnieniami administracyjnymi, dostępne są następujące opcje (w zależności od trybu wdrażania):

- **Tworzenie konta administratora usługi Azure Active Directory z pełnymi uprawnieniami administracyjnymi**

  Włącz uwierzytelnianie usługi Azure Active Directory i utwórz login administratora usługi Azure AD. Jedno konto usługi Azure Active Directory można skonfigurować jako administratora wdrożenia bazy danych SQL z pełnymi uprawnieniami administracyjnymi. To konto może być kontem indywidualnym lub grupy zabezpieczeń. Administrator usługi Azure AD **musi** być skonfigurowany, jeśli chcesz używać kont usługi Azure AD do łączenia się z bazą danych SQL. Aby uzyskać szczegółowe informacje na temat włączania uwierzytelniania usługi Azure AD dla wszystkich typów wdrożeń bazy danych SQL, zobacz następujące artykuły:

  - [Używanie uwierzytelniania usługi Azure Active Directory do uwierzytelniania za pomocą języka SQL](sql-database-aad-authentication.md)
  - [Configure and manage Azure Active Directory authentication with SQL (Konfigurowanie uwierzytelniania w usłudze Azure Active Directory i zarządzanie nim przy użyciu języka SQL)](sql-database-aad-authentication-configure.md)

- **We wdrożeniu wystąpienia zarządzanego utwórz logowania SQL z pełnymi uprawnieniami administracyjnymi**

  - Tworzenie dodatkowego logowania programu SQL Server w wystąpieniu zarządzanym
  - Dodaj logowanie do [roli serwera stałego sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) za pomocą instrukcji [ALTER SERVER ROLE.](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) Ten login będzie miał pełne uprawnienia administracyjne.
  - Alternatywnie należy utworzyć [logowania usługi Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) przy użyciu create <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">logowania</a> składni.

- **W przypadku pojedynczego lub zbiorczego wdrożenia utwórz logowania SQL o ograniczonych uprawnieniach administracyjnych**

  - Tworzenie dodatkowego logowania SQL w głównej bazie danych dla wdrożenia pojedynczej lub puli bazy danych lub wdrożenia wystąpienia zarządzanego
  - Tworzenie konta użytkownika w głównej bazie danych skojarzonej z tym nowym loginem
  - Dodaj konto użytkownika `dbmanager`do `loginmanager` , roli lub `master` obu w bazie danych przy użyciu instrukcji [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (dla usługi Azure Synapse Analytics użyj instrukcji [sp_addrolemember).](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

  > [!NOTE]
  > `dbmanager`i `loginmanager` role **nie** odnoszą się do wdrożeń wystąpień zarządzanych.

  Członkowie tych [specjalnych ról głównej bazy danych](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) dla pojedynczych lub pulchowanych baz danych umożliwiają użytkownikom tworzenie baz danych i zarządzanie nimi oraz tworzenie loginów i zarządzanie nimi. W bazach danych utworzonych przez użytkownika, który jest członkiem `dbmanager` `db_owner` roli, element członkowski jest mapowany `dbo` do roli stałej bazy danych i może zalogować się do tej bazy danych i zarządzać nią przy użyciu konta użytkownika. Te role nie mają żadnych jawnych uprawnień poza główną bazą danych.

  > [!IMPORTANT]
  > Nie można utworzyć dodatkowego logowania SQL z pełnymi uprawnieniami administracyjnymi w pojedynczej lub puli bazy danych.

## <a name="create-accounts-for-non-administrator-users"></a>Tworzenie kont dla użytkowników niebędących administratorami

Konta dla użytkowników nieadawisowych można utworzyć przy użyciu jednej z dwóch metod:

- **Utwórz logowanie**

  Utwórz logowanie SQL w głównej bazie danych. Następnie utwórz konto użytkownika w każdej bazie danych, do której ten użytkownik potrzebuje dostępu i skojarzyć konto użytkownika z tym loginem. Takie podejście jest preferowane, gdy użytkownik musi uzyskać dostęp do wielu baz danych i chcesz zachować hasła zsynchronizowane. Jednak to podejście ma złożoność, gdy jest używana z replikacją geograficzną, ponieważ login musi być utworzony zarówno na serwerze podstawowym, jak i na serwerach pomocniczych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zabezpieczeń bazy danych SQL i zarządzanie nią w celu przywracania geograficznego lub pracy awaryjnej](sql-database-geo-replication-security-config.md).
- **Tworzenie konta użytkownika**

  Utwórz konto użytkownika w bazie danych, do której użytkownik potrzebuje dostępu (nazywany także [użytkownikiem zawierającym).](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)

  - Za pomocą jednej lub puli bazy danych zawsze można utworzyć tego typu konto użytkownika.
  - W bazie danych wystąpienia zarządzanego, która nie obsługuje [podmiotów serwera usługi Azure AD,](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)można utworzyć tylko tego typu konto użytkownika w [zawartej bazie danych](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). W przypadku wystąpienia zarządzanego obsługującego [podmioty serwera usługi Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)można utworzyć konta użytkowników w celu uwierzytelnienia w wystąpieniu zarządzanym bez konieczności tworzenia użytkowników bazy danych jako użytkownika zawartej bazy danych.

  Dzięki takiemu podejściu informacje o uwierzytelnianiu użytkownika są przechowywane w każdej bazie danych i automatycznie replikowane do replikowanych geograficznie baz danych. Jeśli jednak to samo konto istnieje w wielu bazach danych i używasz uwierzytelniania SQL, należy zachować hasła zsynchronizowane ręcznie. Ponadto jeśli użytkownik ma konto w różnych bazach danych z różnymi hasłami, zapamiętywanie tych haseł może stać się problemem.

> [!IMPORTANT]
> Aby utworzyć zawartych użytkowników mapowanych do tożsamości usługi Azure AD, musisz być zalogowany przy użyciu konta usługi Azure AD, który jest administratorem w bazie danych SQL. W wystąpieniu zarządzanym `sysadmin` logowania SQL z uprawnieniami można również utworzyć logowania usługi Azure AD lub użytkownika.

Przykłady przedstawiające sposób tworzenia loginów i użytkowników można znaleźć w:

- [Tworzenie logowania dla pojedynczych lub pulowanych baz danych](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Tworzenie logowania dla bazy danych wystąpienia zarządzanego](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Tworzenie logowania do bazy danych usługi Azure Synapse Analytics](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Tworzenie użytkownika](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Tworzenie użytkowników zawierających usługę Azure AD](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Aby zapoznać się z samouczkiem zabezpieczeń obejmującym tworzenie programu SQL Server zawartych użytkowników w pojedynczej lub puli bazy danych, zobacz [Samouczek: Zabezpieczanie pojedynczej lub puli bazy danych](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Korzystanie z ról stałej i niestandardowej bazy danych

Po utworzeniu konta użytkownika w bazie danych, na podstawie logowania lub jako użytkownik zawarty, można autoryzować tego użytkownika do wykonywania różnych akcji i dostępu do danych w określonej bazie danych. Do autoryzacji dostępu można użyć następujących metod:

- **Stałe role bazy danych**

  Dodaj konto użytkownika do [roli stałej bazy danych](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). Istnieje 9 ról stałej bazy danych, z których każda ma zdefiniowany zestaw uprawnień. Najczęstsze role stałej bazy danych to: **db_owner,** **db_ddladmin,** **db_datawriter,** **db_datareader,** **db_denydatawriter**i **db_denydatareader.** Rola **db_owner** jest najczęściej używana do udzielenia pełnych uprawnień jedynie niewielkiej liczbie użytkowników. Inne ustalone role bazy danych ułatwiają szybkie tworzenie prostej bazy danych, ale nie zaleca się ich używania w większości przypadków tworzenia produkcyjnych baz danych. Na przykład **rola db_datareader** stałej bazy danych udziela dostępu do odczytu do każdej tabeli w bazie danych, co jest więcej niż jest to absolutnie konieczne.

  - Aby dodać użytkownika do roli stałej bazy danych:

    - W usłudze Azure SQL Database użyj instrukcji [ALTER ROLE.](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) Zobacz też Przykłady [alter role](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Usługa Azure Synapse Analytics, użyj [instrukcji sp_addrolemember.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) Przykłady można znaleźć [sp_addrolemember przykładach](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Rola niestandardowej bazy danych**

  Utwórz rolę niestandardowej bazy danych przy użyciu instrukcji [CREATE ROLE.](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) Rola niestandardowa umożliwia tworzenie własnych ról bazy danych zdefiniowanych przez użytkownika i staranne przyznawanie każdej roli najmniejszych uprawnień niezbędnych do potrzeb biznesowych. Następnie można dodać użytkowników do roli niestandardowej. Gdy użytkownik jest członkiem wielu ról, łączą one uprawnienia ich wszystkich.
- **Udzielanie uprawnień bezpośrednio**

  Udzielaj [uprawnień](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) konta użytkownika bezpośrednio. Istnieje ponad 100 uprawnień, których można indywidualnie udzielić lub odmówić w usłudze SQL Database. Wiele z tych uprawnień jest zagnieżdżonych. Na przykład uprawnienie `UPDATE` na schemacie obejmuje `UPDATE` uprawnienie dla każdej tabeli na tym schemacie. Podobnie jak w przypadku większości systemów, odmowa przyznania uprawnienia kasuje przyznanie. Ze względu na zagnieżdżoną naturę uprawnień oraz ich liczbę zaprojektowanie systemu zabezpieczającego bazę danych w prawidłowy sposób może wymagać starannej analizy. Rozpocznij od listy uprawnień [Uprawnienia (aparat bazy danych)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) i przejrzyj [obszerny wykaz](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) uprawnień.

## <a name="using-groups"></a>Korzystanie z grup

Zarządzanie efektywnym dostępem używa uprawnień przypisanych do grup zabezpieczeń usługi Active Directory i ról stałych lub niestandardowych zamiast do poszczególnych użytkowników.

- Korzystając z uwierzytelniania usługi Azure Active Directory, umieść użytkowników usługi Azure Active Directory w grupie zabezpieczeń usługi Azure Active Directory. Utwórz użytkownika zawartej bazy danych dla tej grupy. Umieść jednego lub więcej użytkowników bazy danych w roli niestandardowej bazy danych z określonymi uprawnieniami odpowiednimi dla tej grupy użytkowników.

- Podczas korzystania z uwierzytelniania SQL, tworzenie zamkniętych użytkowników bazy danych w bazie danych. Umieść jednego lub więcej użytkowników bazy danych w roli niestandardowej bazy danych z określonymi uprawnieniami odpowiednimi dla tej grupy użytkowników.

  > [!NOTE]
  > Można również użyć grup dla użytkowników nieuwiernych baz danych.

Należy zapoznać się z następującymi funkcjami, których można użyć do ograniczania lub podnoszenia uprawnień:

- [Personifikacji](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) i [podpisywania modułów](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) można używać do bezpiecznego tymczasowego podnoszenia poziomu uprawnień.
- [Zabezpieczeń na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) można używać do ograniczania zbioru wierszy, do których użytkownik może uzyskać dostęp.
- [Maskowania danych](sql-database-dynamic-data-masking-get-started.md) można używać do ograniczania ujawniania danych wrażliwych.
- [Procedury składowane](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) umożliwiają ograniczenie czynności wykonywanych w bazie danych.

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć przegląd wszystkich funkcji zabezpieczeń usługi SQL Database, zobacz [omówienie zabezpieczeń usługi SQL](sql-database-security-overview.md).
