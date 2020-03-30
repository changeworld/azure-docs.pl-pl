---
title: Konfigurowanie zabezpieczeń do odzyskiwania po awarii
description: Zapoznaj się z zagadnieniami związanymi z zabezpieczeniami związanymi z konfigurowaniem zabezpieczeń i zarządzaniem nimi po przywróceniu bazy danych lub przekształceniu w niego w błąd serwera pomocniczego.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 9d628583168883276e67d9e2f2fcafdce292769e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73807492"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurowanie zabezpieczeń usługi Azure SQL Database i zarządzanie nimi w celu przywracania geograficznego lub pracy awaryjnej

W tym artykule opisano wymagania uwierzytelniania dotyczące konfigurowania i kontrolowania aktywnych grup [replikacji geograficznej](sql-database-active-geo-replication.md) i [automatycznego trybu failover.](sql-database-auto-failover-group.md) Zawiera również kroki wymagane do skonfigurowania dostępu użytkownika do pomocniczej bazy danych. Na koniec opisano również, jak włączyć dostęp do odzyskanej bazy danych po użyciu [przywracania geograficznego](sql-database-recovery-using-backups.md#geo-restore). Aby uzyskać więcej informacji na temat opcji odzyskiwania, zobacz [Omówienie ciągłości biznesowej](sql-database-business-continuity.md).

## <a name="disaster-recovery-with-contained-users"></a>Odzyskiwanie po awarii z użytkownikami zawartymi w po awarii

W przeciwieństwie do tradycyjnych użytkowników, które muszą być mapowane do logowania w głównej bazie danych, zawarte użytkownika jest całkowicie zarządzany przez samą bazę danych. Ma to dwie zalety. W scenariuszu odzyskiwania po awarii użytkownicy mogą nadal łączyć się z nową podstawową bazą danych lub bazą danych odzyskaną przy użyciu przywracania geograficznego bez dodatkowej konfiguracji, ponieważ baza danych zarządza użytkownikami. Istnieją również potencjalne korzyści skalowalności i wydajności z tej konfiguracji z punktu widzenia logowania. Aby uzyskać więcej informacji, zobacz artykuł [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych).

Głównym kompromisem jest to, że zarządzanie procesem odzyskiwania po awarii na dużą skalę jest trudniejsze. Jeśli masz wiele baz danych, które używają tego samego logowania, utrzymanie poświadczeń przy użyciu zawartych użytkowników w wielu bazach danych może zanegować korzyści zawartych użytkowników. Na przykład zasady rotacji haseł wymagają spójnego ww. w wielu bazach danych, a nie zmiany hasła logowania raz w głównej bazie danych. Z tego powodu jeśli masz wiele baz danych, które używają tej samej nazwy użytkownika i hasła, przy użyciu użytkowników zawartych nie jest zalecane.

## <a name="how-to-configure-logins-and-users"></a>Jak skonfigurować loginy i użytkowników

Jeśli używasz loginów i użytkowników (a nie użytkowników zawartych w nich), należy podjąć dodatkowe kroki, aby upewnić się, że te same logowania istnieją w głównej bazie danych. W poniższych sekcjach opisano kroki i dodatkowe zagadnienia.

  >[!NOTE]
  > Istnieje również możliwość korzystania z logowania usługi Azure Active Directory (AAD) do zarządzania bazami danych. Aby uzyskać więcej informacji, zobacz [Azure SQL loginy i użytkowników](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Konfigurowanie dostępu użytkownika do pomocniczej lub odzyskanej bazy danych

Aby pomocnicza baza danych mogła być użyteczna jako pomocnicza baza danych tylko do odczytu, a także aby zapewnić odpowiedni dostęp do nowej podstawowej bazy danych lub bazy danych odzyskanej przy użyciu przywracania geograficznego, główna baza danych serwera docelowego musi mieć odpowiednie zabezpieczenia konfiguracji przed odzyskiem.

Określone uprawnienia dla każdego kroku są opisane w dalszej części tego tematu.

Przygotowanie dostępu użytkownika do pomocniczej replikacji geograficznej powinno być wykonywane jako część konfigurująca replikację geograficzną. Przygotowanie dostępu użytkownika do baz danych przywróconych geograficznie powinno być wykonywane w dowolnym momencie, gdy oryginalny serwer jest w trybie online (np. jako część drążenia odzyskiwania po awarii).

> [!NOTE]
> Jeśli serwer, który nie ma poprawnie skonfigurowanych loginów, przejdzie w tryb fail over lub geo-restore na serwerze, który nie ma poprawnie skonfigurowanych loginów, dostęp do niego będzie ograniczony do konta administratora serwera.

Konfigurowanie loginów na serwerze docelowym obejmuje trzy kroki opisane poniżej:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Określanie loginów z dostępem do podstawowej bazy danych

Pierwszym krokiem procesu jest określenie, które logowania muszą być duplikowane na serwerze docelowym. Jest to realizowane za pomocą pary instrukcji SELECT, jednej w logicznej głównej bazie danych na serwerze źródłowym i jednej w samej podstawowej bazie danych.

Tylko administrator serwera lub członek roli serwera **LoginManager** może określić logowania na serwerze źródłowym za pomocą następującej instrukcji SELECT.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Tylko członek roli db_owner bazy danych, użytkownik dbo lub administrator serwera, można określić wszystkie podmioty użytkownika bazy danych w podstawowej bazie danych.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Znajdź identyfikator SID dla loginów zidentyfikowanych w kroku 1

Porównując dane wyjściowe zapytań z poprzedniej sekcji i dopasowując identyfikatory SID, można mapować logowania serwera do użytkownika bazy danych. Logowania, które mają użytkownika bazy danych z pasującym identyfikatorem SID mają dostęp użytkownika do tej bazy danych jako tego podmiotu użytkownika bazy danych.

Poniższa kwerenda może służyć do zobaczyć wszystkie podmioty użytkownika i ich identyfikatory SID w bazie danych. Tylko członek roli db_owner bazy danych lub administrator serwera może uruchomić tę kwerendę.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> Użytkownicy **INFORMATION_SCHEMA** i **sys** mają *identyfikatory NULL,* a identyfikator SID **gościa** to **0x00**. **Dbo** SID może zaczynać się od *0x0106000000001648000000000048454*, jeśli twórca bazy danych był administratorem serwera zamiast członkiem **DbManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Tworzenie loginów na serwerze docelowym

Ostatnim krokiem jest, aby przejść do serwera docelowego lub serwerów i wygenerować logowania z odpowiednich identyfikatorów SID. Podstawowa składnia jest następująca.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Jeśli chcesz udzielić użytkownikowi dostępu do pomocniczego, ale nie do podstawowego, można to zrobić, zmieniając logowania użytkownika na serwerze podstawowym przy użyciu następującej składni.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> DISABLE nie zmienia hasła, więc zawsze możesz je włączyć w razie potrzeby.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat zarządzania dostępem do bazy danych i logowaniem, zobacz [Zabezpieczenia bazy danych SQL: Zarządzanie dostępem do bazy danych i zabezpieczeniami logowania](sql-database-manage-logins.md).
* Aby uzyskać więcej informacji na temat użytkowników zawartej bazy danych, zobacz [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx).
* Aby dowiedzieć się więcej o aktywnej replikacji geograficznej, zobacz [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md).
* Aby dowiedzieć się więcej o grupach automatycznego trybu failover, zobacz [Grupy auto-trybu failover](sql-database-auto-failover-group.md).
* Aby uzyskać informacje na temat korzystania z przywracania [geograficznego, zobacz przywracanie geograficzne](sql-database-recovery-using-backups.md#geo-restore)
