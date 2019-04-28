---
title: Zabezpieczenia na poziomie kolumny usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zabezpieczenia na poziomie kolumny (CLS) umożliwiają klientom kontrolowanie dostępu do kolumny tabeli bazy danych na podstawie kontekstu wykonania przez użytkownika lub członkostwa w grupach. Zgodny ze specyfikacją upraszcza projektowanie i kodowanie zabezpieczeń w aplikacji. Zgodny ze specyfikacją umożliwia Implementowanie ograniczeń dla dostępu do kolumny.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: aa91bd586e064239d0e05c754427947963c9ee3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082810"
---
# <a name="column-level-security"></a>Zabezpieczenia na poziomie kolumny
Zabezpieczenia na poziomie kolumny (CLS) umożliwiają klientom kontrolowanie dostępu do kolumny tabeli bazy danych na podstawie kontekstu wykonania przez użytkownika lub członkostwa w grupach.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

Zgodny ze specyfikacją upraszcza projektowanie i kodowanie zabezpieczeń w aplikacji. Zgodny ze specyfikacją umożliwia zaimplementowanie ograniczeń w kolumnie dostępu w celu ochrony poufnych danych. Na przykład zapewnienie, że konkretni użytkownicy mogą uzyskać dostęp tylko niektóre kolumny tabeli, które są odpowiednie do ich działu. Logika ograniczeń dostępu jest znajduje się w warstwie bazy danych, a nie od danych w innej warstwie aplikacji. Mają zastosowanie ograniczenia dostępu do bazy danych, za każdym razem, gdy podejmowana jest próba tego dostępu do danych z dowolnej warstwy. To ograniczenie sprawia, że system zabezpieczeń bardziej niezawodnych i niezawodną przez zmniejszenie obszaru powierzchni ogólną systemu zabezpieczeń. Ponadto zgodne ze specyfikacją również eliminuje potrzebę wprowadzenie do widoków odfiltrować kolumny dla ograniczeń dostępu użytkowników.

Można zaimplementować ze specyfikacją CLS przy użyciu [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) instrukcję języka T-SQL. Z tego mechanizmu uwierzytelniania SQL i Azure Active Directory (AAD) są obsługiwane.

![ze specyfikacją CLS](./media/column-level-security/cls.png)

## <a name="syntax"></a>Składnia

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Przykład
Poniższy przykład pokazuje, jak uniemożliwić dostęp do kolumny "SSN" tabeli "Członkostwa" w "TestUser":

Utwórz tabelę "Członkostwa" z kolumny numer PESEL, używany do przechowywania numery ubezpieczenia społecznego:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

"TestUser" Zezwalaj na dostęp do wszystkich kolumn z wyjątkiem SSN zawierające poufne dane:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Zapytania wykonywane zgodnie z "TestUser" zakończy się niepowodzeniem, jeśli zawierają one kolumny SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Przypadki użycia
Kilka przykładów sposobu użycia ze specyfikacją CLS jest on już dzisiaj:
- Dostawca usług finansowych umożliwia jedyne konto menedżerom na dostęp do numerów klienta ubezpieczenia społecznego (SSN), numerów telefonów i inne identyfikowalne dane osobowe (PII).
- Dostawca opieki zdrowotnej umożliwia tylko lekarzy, jak i pielęgniarek mają mieć dostęp do poufnych rekordami medycznymi nie zezwalając działu rozliczeń do wyświetlenia tych danych.
