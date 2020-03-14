---
title: Nazwy logowania i użytkownicy
description: Dowiedz się, w jaki sposób Azure SQL Database i usługa Azure Synapse Analytics uwierzytelniają użytkowników w celu uzyskania dostępu przy użyciu nazw logowania i kont użytkowników, a także są używane role i jawne uprawnienia do autoryzacji logowań i użytkowników w celu wykonywania działań w bazach danych oraz na poziomie serwera.
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
ms.date: 03/12/2020
ms.openlocfilehash: 7c70d5dd19ec0495fe09152b5653363ad369347c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268914"
---
# <a name="granting-database-access-and-authorization-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Udzielanie dostępu do bazy danych i autoryzacji do SQL Database i usługi Azure Synapse Analytics przy użyciu nazw logowania i kont użytkowników

Uwierzytelniony dostęp do baz danych w Azure SQL Database i Azure Synapse Analytics (dawniej Azure SQL Data Warehouse) jest zarządzany przy użyciu nazw logowania i kont użytkowników. [**Uwierzytelnianie**](sql-database-security-overview.md#authentication) to proces potwierdzania tożsamości użytkownika.

- Logowanie jest kontem pojedynczym w bazie danych Master
- Konto użytkownika to pojedyncze konto w dowolnej bazie danych i nie musi być skojarzone z identyfikatorem logowania

> [!IMPORTANT]
> Bazy danych w Azure SQL Database i Azure Synapse Analytics (dawniej Azure SQL Data Warehouse) są określane zbiorczo w pozostałej części artykułu jako Azure SQL Database (dla uproszczenia).

Użytkownik bazy danych nawiązuje połączenie z bazą danych Azure SQL Database przy użyciu konta użytkownika i jest uwierzytelniany przy użyciu jednej z następujących dwóch metod:

- [Uwierzytelnianie SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication), które składa się z nazwy logowania lub nazwy konta użytkownika i powiązanego hasła przechowywanego w Azure SQL Database.
- [Uwierzytelnianie Azure Active Directory](sql-database-aad-authentication.md), które używa poświadczeń logowania przechowywanych w Azure Active Directory

Autoryzacja dostępu do danych i wykonywanie różnych akcji w ramach usługi Azure SQL Database są zarządzane przy użyciu ról bazy danych i jawnych uprawnień. [**Autoryzacja**](sql-database-security-overview.md#authorization) odnosi się do uprawnień przypisanych do użytkownika w ramach Azure SQL Database i określa, co użytkownik może zrobić. Autoryzacja jest kontrolowana za pomocą [członkostw roli](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) bazy danych konta użytkownika i [uprawnień na poziomie obiektów](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień.

Z tego artykułu dowiesz się:

- Konfiguracja dostępu i autoryzacji po pierwszym utworzeniu nowej Azure SQL Database
- Jak dodać nazwy logowania i konta użytkowników w bazie danych Master i konta użytkowników, a następnie przyznać im uprawnienia administracyjne
- Jak dodać konta użytkowników w bazach danych użytkowników, skojarzone z nazwami logowania lub kontami zawartych użytkowników
- Konfigurowanie kont użytkowników z uprawnieniami w bazach danych użytkowników przy użyciu ról bazy danych i jawnych uprawnień

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Istniejące dane logowania i konta użytkowników po utworzeniu nowej bazy danych

Podczas tworzenia pierwszego wdrożenia Azure SQL Database należy określić nazwę logowania administratora i skojarzone hasło dla tej nazwy logowania. To konto administracyjne nosi nazwę **administrator serwera**. Następująca konfiguracja nazw logowania i użytkowników w bazach danych Master i User odbywa się podczas wdrażania:

- Logowanie SQL z uprawnieniami administracyjnymi jest tworzone przy użyciu podanej nazwy logowania. [Logowanie](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) do usługi SQL Database odbywa się przy użyciu poszczególnych kont użytkowników.
- Ta nazwa logowania ma przyznane pełne uprawnienia administracyjne do wszystkich baz danych jako [podmiot zabezpieczeń na poziomie serwera](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Ta nazwa logowania ma wszystkie dostępne uprawnienia w ramach SQL Database i nie może być ograniczona. W wystąpieniu zarządzanym ta nazwa logowania jest dodawana do [stałej roli serwera sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (Ta rola nie istnieje z bazami danych w jednej lub w puli).
- [Konto użytkownika](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) o nazwie `dbo` jest tworzone dla tej nazwy logowania w każdej bazie danych użytkownika. Użytkownik [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) ma wszystkie uprawnienia baz danych w bazie danych i jest mapowany do `db_owner` stałej roli bazy danych. Dodatkowe role stałych baz danych zostały omówione w dalszej części tego artykułu.

Aby zidentyfikować konta administratorów dla programu SQL Server, Otwórz Azure Portal i przejdź do karty **Właściwości** serwera sql lub SQL Database.

![Administratorzy serwera SQL](media/sql-database-manage-logins/sql-admins.png)

> [!IMPORTANT]
> Nazwy logowania administratora nie można zmienić po jej utworzeniu. Aby zresetować hasło administratora serwera logicznego, przejdź do [Azure Portal](https://portal.azure.com), kliknij pozycję **serwery SQL**, wybierz serwer z listy, a następnie kliknij przycisk **Resetuj hasło**. Aby zresetować hasło dla serwera wystąpienia zarządzanego, przejdź do Azure Portal, kliknij wystąpienie, a następnie kliknij przycisk **Resetuj hasło**. Możesz również użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Tworzenie dodatkowych logowań i użytkowników z uprawnieniami administracyjnymi

W tym momencie SQL Database jest skonfigurowany tylko do dostępu przy użyciu jednego identyfikatora logowania SQL i konta użytkownika. Aby utworzyć dodatkowe nazwy logowania z pełnymi lub częściowymi uprawnieniami administracyjnymi, dostępne są następujące opcje (w zależności od trybu wdrożenia):

- **Tworzenie konta administratora Azure Active Directory z pełnymi uprawnieniami administracyjnymi**

  Włącz uwierzytelnianie Azure Active Directory i Utwórz identyfikator logowania administratora usługi Azure AD. Jedno konto Azure Active Directory można skonfigurować jako administrator wdrożenia SQL Database z pełnymi uprawnieniami administracyjnymi. To konto może być kontem użytkownika lub grupy zabezpieczeń. Aby nawiązać połączenie z usługą SQL Database, **należy** skonfigurować administratora usługi Azure AD. Aby uzyskać szczegółowe informacje na temat włączania uwierzytelniania usługi Azure AD dla wszystkich typów wdrożeń SQL Database, zobacz następujące artykuły:

  - [Używanie uwierzytelniania Azure Active Directory na potrzeby uwierzytelniania przy użyciu programu SQL Server](sql-database-aad-authentication.md)
  - [Configure and manage Azure Active Directory authentication with SQL (Konfigurowanie uwierzytelniania w usłudze Azure Active Directory i zarządzanie nim przy użyciu języka SQL)](sql-database-aad-authentication-configure.md)

- **W ramach wdrożenia wystąpienia zarządzanego utwórz identyfikatory logowania SQL z pełnymi uprawnieniami administracyjnymi**

  - Utwórz dodatkową SQL Serverą nazwę logowania w wystąpieniu zarządzanym
  - Dodaj nazwę logowania do [stałej roli serwera sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) przy użyciu instrukcji [ALTER Server role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) . Ta nazwa logowania będzie miała pełne uprawnienia administracyjne.
  - Alternatywnie możesz utworzyć [Identyfikator logowania usługi Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) przy użyciu składni <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">tworzenia nazwy logowania</a> .

- **W przypadku wdrożenia pojedynczego lub w puli Utwórz nazwy logowania SQL mające ograniczone uprawnienia administracyjne**

  - Utwórz dodatkową nazwę logowania SQL w bazie danych Master dla pojedynczego lub puli wdrożenia bazy danych lub wdrożenia wystąpienia zarządzanego
  - Utwórz konto użytkownika w bazie danych Master skojarzone z nowym logowaniem
  - Dodaj konto użytkownika do `dbmanager`, roli `loginmanager` lub obu w bazie danych `master` przy użyciu instrukcji [ALTER Server role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (w przypadku usługi Azure Synapse Analytics użyj instrukcji [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ).

  > [!NOTE]
  > role `dbmanager` i **`loginmanager` nie odnoszą się** do wdrożeń wystąpienia zarządzanego.

  Członkowie tych [specjalnych ról głównych baz danych](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) dla jednej lub puli baz danych umożliwiają użytkownikom posiadanie uprawnień do tworzenia baz danych i zarządzania nimi oraz do tworzenia nazw logowania i zarządzania nimi. W bazach danych utworzonych przez użytkownika, który jest członkiem roli `dbmanager`, członek jest mapowany na stałą rolę bazy danych `db_owner` i może logować się do tej bazy danych i zarządzać nią przy użyciu konta użytkownika `dbo`. Te role nie mają jawnych uprawnień poza główną bazą danych.

  > [!IMPORTANT]
  > Nie można utworzyć dodatkowego identyfikatora logowania SQL z pełnymi uprawnieniami administracyjnymi w jednej lub puli baz danych.

## <a name="create-accounts-for-non-administrator-users"></a>Tworzenie kont dla użytkowników niebędących administratorami

Można utworzyć konta dla użytkowników niebędących administratorami przy użyciu jednej z dwóch metod:

- **Utwórz nazwę logowania**

  Utwórz identyfikator logowania SQL w bazie danych Master. Następnie utwórz konto użytkownika w każdej bazie danych, do której użytkownik będzie potrzebował dostępu, i skojarz konto użytkownika z tym logowaniem. Ta metoda jest preferowana, gdy użytkownik musi uzyskać dostęp do wielu baz danych i chcesz zachować synchronizację haseł. Jednak takie podejście ma złożoność w przypadku używania z replikacją geograficzną, ponieważ nazwa logowania musi być utworzona zarówno na serwerze podstawowym, jak i na serwerze pomocniczym. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zabezpieczeń Azure SQL Database i zarządzanie nimi na potrzeby przywracania geograficznego lub przełączania do trybu failover](sql-database-geo-replication-security-config.md).
- **Utwórz konto użytkownika**

  Utwórz konto użytkownika w bazie danych, do której użytkownik potrzebuje dostępu (nazywany również [zawartym użytkownikiem](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - W przypadku bazy danych z jedną lub pulą można zawsze utworzyć ten typ konta użytkownika.
  - W przypadku bazy danych wystąpienia zarządzanego, która nie obsługuje [podmiotów zabezpieczeń serwera usługi Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), można utworzyć tylko ten typ konta użytkownika w [zawartej bazie danych](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Korzystając z wystąpienia zarządzanego obsługującego [podmioty zabezpieczeń serwera usługi Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), można utworzyć konta użytkowników, aby uwierzytelniać się w zarządzanym wystąpieniu bez konieczności tworzenia użytkowników bazy danych jako zawartych użytkowników bazy danych.

  W ramach tego podejścia informacje o uwierzytelnianiu użytkownika są przechowywane w poszczególnych bazach danych i automatycznie replikowane do baz danych replikowanych geograficznie. Jeśli jednak to samo konto istnieje w wielu bazach danych i używasz uwierzytelniania SQL, należy zachować ręczne Synchronizowanie haseł. Ponadto, jeśli użytkownik ma konto w różnych bazach danych z różnymi hasłami, zapamiętanie tych haseł może stać się problemem.

> [!IMPORTANT]
> Aby utworzyć zawartych użytkowników mapowanych na tożsamości usługi Azure AD, musisz zalogować się przy użyciu konta usługi Azure AD, które jest administratorem w SQL Database. W wystąpieniu zarządzanym, identyfikator logowania SQL z uprawnieniami `sysadmin` można również utworzyć identyfikator logowania lub użytkownika usługi Azure AD.

Przykłady pokazujące sposób tworzenia logowań i użytkowników znajdują się w temacie:

- [Utwórz dane logowania dla jednej lub puli baz danych](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Utwórz nazwę logowania dla bazy danych wystąpienia zarządzanego](https://docs.microsoft.com/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Utwórz nazwę logowania dla bazy danych usługi Azure Synapse Analytics](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Utwórz użytkownika](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Tworzenie użytkowników zawartych w usłudze Azure AD](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Aby zapoznać się z samouczkiem dotyczącym zabezpieczeń, który obejmuje tworzenie SQL Server zawartych użytkowników w jednej lub puli baz danych, zobacz [Samouczek: Zabezpieczanie pojedynczej lub puli baz danych](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Korzystanie z stałych i niestandardowych ról baz danych

Po utworzeniu konta użytkownika w bazie danych, na podstawie nazwy logowania lub jako zawartego użytkownika, można autoryzować tego użytkownika do wykonywania różnych akcji i uzyskiwania dostępu do danych w określonej bazie danych. Aby autoryzować dostęp, można użyć następujących metod:

- **Stałe role bazy danych**

  Dodaj konto użytkownika do [stałej roli bazy danych](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). Dostępne są 9 stałych ról bazy danych, z których każda ma zdefiniowany zestaw uprawnień. Najbardziej popularne role bazy danych to: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**i **db_denydatareader**. Rola **db_owner** jest najczęściej używana do udzielenia pełnych uprawnień jedynie niewielkiej liczbie użytkowników. Inne ustalone role bazy danych ułatwiają szybkie tworzenie prostej bazy danych, ale nie zaleca się ich używania w większości przypadków tworzenia produkcyjnych baz danych. Na przykład rola stałej bazy danych **db_datareader** przyznaje dostęp do odczytu każdej tabeli w bazie danych, która jest większa niż jest absolutnie konieczna.

  - Aby dodać użytkownika do stałej roli bazy danych:

    - W Azure SQL Database Użyj instrukcji [ALTER role](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) . Aby zapoznać się z przykładami, zobacz [ALTER role przykłady](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse Analytics, użyj instrukcji [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) . Przykłady zawierają [przykłady sp_addrolemember](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Niestandardowa rola bazy danych**

  Utwórz niestandardową rolę bazy danych przy użyciu instrukcji [Create role](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) . Rola niestandardowa umożliwia tworzenie własnych zdefiniowanych przez użytkownika ról baz danych i staranne przyznawanie każdej roli najniższych uprawnień niezbędnych dla potrzeb firmy. Następnie można dodać użytkowników do roli niestandardowej. Gdy użytkownik jest członkiem wielu ról, łączą one uprawnienia ich wszystkich.
- **Udziel uprawnień bezpośrednio**

  Przyznaj bezpośrednio [uprawnienia](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) kontu użytkownika. Istnieje ponad 100 uprawnień, których można indywidualnie udzielić lub odmówić w usłudze SQL Database. Wiele z tych uprawnień jest zagnieżdżonych. Na przykład uprawnienie `UPDATE` na schemacie obejmuje `UPDATE` uprawnienie dla każdej tabeli na tym schemacie. Podobnie jak w przypadku większości systemów, odmowa przyznania uprawnienia kasuje przyznanie. Ze względu na zagnieżdżoną naturę uprawnień oraz ich liczbę zaprojektowanie systemu zabezpieczającego bazę danych w prawidłowy sposób może wymagać starannej analizy. Rozpocznij od listy uprawnień [Uprawnienia (aparat bazy danych)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) i przejrzyj [obszerny wykaz](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) uprawnień.

## <a name="using-groups"></a>Korzystanie z grup

Wydajne zarządzanie dostępem korzysta z uprawnień przypisanych do Active Directory grup zabezpieczeń i stałych lub niestandardowych ról zamiast do poszczególnych użytkowników.

- W przypadku korzystania z uwierzytelniania Azure Active Directory należy umieścić Azure Active Directory użytkowników w Azure Active Directory grupie zabezpieczeń. Utwórz użytkownika zawartej bazy danych dla tej grupy. Umieść co najmniej jednego użytkownika bazy danych w niestandardowej roli bazy danych z określonymi uprawnieniami, które są odpowiednie dla tej grupy użytkowników.

- W przypadku korzystania z uwierzytelniania SQL należy utworzyć użytkowników zawartej bazy danych w bazie danych programu. Umieść co najmniej jednego użytkownika bazy danych w niestandardowej roli bazy danych z określonymi uprawnieniami, które są odpowiednie dla tej grupy użytkowników.

  > [!NOTE]
  > Można również użyć grup dla użytkowników nienależących do bazy danych.

Należy zapoznać się z następującymi funkcjami, których można użyć do ograniczania lub podnoszenia uprawnień:

- [Personifikacji](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) i [podpisywania modułów](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) można używać do bezpiecznego tymczasowego podnoszenia poziomu uprawnień.
- [Zabezpieczeń na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) można używać do ograniczania zbioru wierszy, do których użytkownik może uzyskać dostęp.
- [Maskowania danych](sql-database-dynamic-data-masking-get-started.md) można używać do ograniczania ujawniania danych wrażliwych.
- [Procedury składowane](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) umożliwiają ograniczenie czynności wykonywanych w bazie danych.

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć przegląd wszystkich funkcji zabezpieczeń usługi SQL Database, zobacz [omówienie zabezpieczeń usługi SQL](sql-database-security-overview.md).
