---
title: Zabezpieczenia na poziomie kolumny
description: Zabezpieczenia na poziomie kolumny (CLS) umożliwiają klientom kontrolowanie dostępu do kolumn tabeli bazy danych na podstawie kontekstu wykonywania użytkownika lub ich przynależności do grupy. CLS upraszcza projektowanie i kodowanie zabezpieczeń w aplikacji. Specyfikacja CLS umożliwia implementowanie ograniczeń dostępu do kolumn.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 34172e313b537d59287a424683ea1555cbba9b99
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049157"
---
# <a name="column-level-security"></a>Zabezpieczenia na poziomie kolumny
Zabezpieczenia na poziomie kolumny (CLS) umożliwiają klientom kontrolowanie dostępu do kolumn tabeli bazy danych na podstawie kontekstu wykonywania użytkownika lub ich przynależności do grupy.
Zaktualizuj do filmu wideo poniżej — ponieważ to wideo zostało ogłoszone, [zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/en-us/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) są również dostępne w SQL Data Warehouse. 
> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS upraszcza projektowanie i kodowanie zabezpieczeń w aplikacji. Specyfikacja CLS umożliwia implementowanie ograniczeń dostępu do kolumn w celu ochrony poufnych danych. Na przykład w celu zapewnienia, że określeni użytkownicy będą mieli dostęp tylko do niektórych kolumn tabeli odnoszących się do ich działu. Logika ograniczeń dostępu znajduje się w warstwie bazy danych, a nie na danych w innej warstwie aplikacji. Baza danych stosuje ograniczenia dostępu przy każdej próbie dostępu do danych z dowolnej warstwy. To ograniczenie sprawia, że system zabezpieczeń jest bardziej niezawodny i niezawodny poprzez zmniejszenie powierzchni ogólnego systemu zabezpieczeń. Ponadto CLS eliminuje również potrzebę wprowadzenia widoków do filtrowania kolumn w celu nakładania ograniczeń dostępu dla użytkowników.

Specyfikację CLS można zaimplementować przy użyciu instrukcji [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) języka T-SQL. Dzięki temu mechanizmowi obsługiwane jest uwierzytelnianie SQL i Azure Active Directory (AAD).

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
Poniższy przykład pokazuje, jak ograniczyć dostęp "Użytkownik testowy" do kolumny "SSN" tabeli "Membership":

Utwórz tabelę "Membership" z kolumną SSN służącą do przechowywania numerów ubezpieczenia społecznego:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Zezwalaj Użytkownik testowy ' na dostęp do wszystkich kolumn z wyjątkiem kolumny SSN zawierającej poufne dane:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Zapytania wykonane jako "Użytkownik testowy" zakończą się niepowodzeniem, jeśli zawierają kolumnę SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Przypadki użycia
Kilka przykładów użycia CLS jest dzisiaj:
- Firma Financial Services zezwala tylko menedżerom kont na dostęp do numerów PESEL (SSN) klienta, numerów telefonów i innych informacji umożliwiających identyfikację użytkownika.
- Dostawca opieki zdrowotnej umożliwia tylko lekarzy i pielęgniarkom dostęp do poufnych rekordów medycznych bez zezwalania członkom działu rozliczeniowego na wyświetlanie tych danych.
