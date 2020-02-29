---
title: Co to jest zabezpieczenia na poziomie kolumny w usłudze Azure Synapse?
description: Zabezpieczenia na poziomie kolumny umożliwiają klientom kontrolowanie dostępu do kolumn tabeli bazy danych na podstawie kontekstu wykonywania użytkownika lub członkostwa w grupie, upraszczając projektowanie i kodowanie zabezpieczeń w aplikacji oraz pozwalające na implementację ograniczeń w kolumnie niego.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: aa9791f019436cc5c7effc9bce197d89131a6557
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199975"
---
# <a name="column-level-security"></a>Zabezpieczenia na poziomie kolumny

Zabezpieczenia na poziomie kolumny umożliwiają klientom kontrolowanie dostępu do kolumn tabeli na podstawie kontekstu wykonywania użytkownika lub członkostwa w grupie.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Ponieważ to wideo zostało ogłoszone, [zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) stały się dostępne dla usługi Azure Synapse. 

Zabezpieczenia na poziomie kolumny upraszczają projektowanie i kodowanie zabezpieczeń w aplikacji, co pozwala ograniczyć dostęp do kolumn w celu ochrony poufnych danych. Na przykład w celu zapewnienia, że określeni użytkownicy będą mieli dostęp tylko do niektórych kolumn tabeli odnoszących się do ich działu. Logika ograniczeń dostępu znajduje się w warstwie bazy danych, a nie na danych w innej warstwie aplikacji. Baza danych stosuje ograniczenia dostępu przy każdym próbie dostępu do danych z dowolnej warstwy. To ograniczenie sprawia, że zabezpieczenia są bardziej niezawodne i niezawodne przez zredukowanie obszaru ogólnego systemu zabezpieczeń. Ponadto zabezpieczenia na poziomie kolumny eliminują również konieczność wprowadzenia widoków do filtrowania kolumn w celu nakładania ograniczeń dostępu dla użytkowników.

Zabezpieczenia na poziomie kolumny można zaimplementować przy użyciu instrukcji [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) języka T-SQL. Dzięki temu mechanizmowi obsługiwane jest uwierzytelnianie SQL i Azure Active Directory (AAD).

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
Poniższy przykład pokazuje, jak ograniczyć dostęp `TestUser` do kolumny `SSN` tabeli `Membership`:

Utwórz tabelę `Membership` z kolumną SSN służącą do przechowywania numerów PESEL:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Zezwalaj `TestUser` na dostęp do wszystkich kolumn z wyjątkiem kolumny SSN, która ma poufne dane:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Zapytania wykonane jako `TestUser` zakończą się niepowodzeniem, jeśli zawierają kolumnę SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Przypadki użycia

Przykłady sposobu używania zabezpieczeń na poziomie kolumny:

- Firma Financial Services zezwala tylko menedżerom kont na dostęp do numerów PESEL (SSN) klienta, numerów telefonów i innych informacji umożliwiających identyfikację użytkownika.
- Dostawca opieki zdrowotnej umożliwia tylko lekarzy i pielęgniarkom dostęp do poufnych rekordów medycznych, jednocześnie uniemożliwiając członkom działu rozliczania wyświetlanie tych danych.
