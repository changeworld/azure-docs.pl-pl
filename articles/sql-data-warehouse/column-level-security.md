---
title: Zabezpieczenia na poziomie kolumny Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zabezpieczenia na poziomie kolumny (CLS) służy do kontrolowania dostępu do kolumny tabeli bazy danych na podstawie kontekstu wykonywania użytkownika lub członkostwa w grupach. Ze specyfikacją CLS upraszcza projektu i kodowania zabezpieczeń w aplikacji. Ze specyfikacją CLS można zaimplementować ograniczenia dostępu do kolumny.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 06/15/2018
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: 5a916132f705f3c517ee6789b61a3972b2445b62
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938467"
---
# <a name="column-level-security"></a>Zabezpieczenia na poziomie kolumny 
Zabezpieczenia na poziomie kolumny (CLS) służy do kontrolowania dostępu do kolumny tabeli bazy danych na podstawie kontekstu wykonywania użytkownika lub członkostwa w grupach.  

Ze specyfikacją CLS upraszcza projektu i kodowania zabezpieczeń w aplikacji. Ze specyfikacją CLS można zaimplementować ograniczenia dostępu kolumny do ochrony danych poufnych. Na przykład zapewnienie, że konkretnych użytkowników mogą uzyskać dostęp tylko niektóre kolumny tabeli odpowiednie do ich działu. Logika ograniczenie dostępu jest znajduje się w warstwie bazy danych zamiast od danych w innej warstwie aplikacji. Za każdym razem, gdy nastąpiła by dostęp do danych z dowolnej wersji bazy danych ma zastosowanie ograniczeń dostępu. System zabezpieczeń dzięki temu bardziej niezawodne i niezawodne zmniejszając powierzchni ogólną systemu zabezpieczeń. Ponadto to również eliminuje potrzebę wprowadzenia widoków filtrowanie kolumn dla ograniczeń dostępu użytkowników. 

Można zaimplementować ze specyfikacją CLS z [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) instrukcji T-SQL. Z tego mechanizmu uwierzytelniania zarówno SQL i Azure Active Directory (AAD) są obsługiwane.

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
Poniższy przykład przedstawia sposób uniemożliwić dostęp do kolumny 'SSN' tabeli "Członkostwa" "TestUser": 

Utwórz tabelę "Członkostwa" z kolumny SSN używany do przechowywania numerów ubezpieczenia społecznego:

```sql
CREATE TABLE Membership   
  (MemberID int IDENTITY,   
   FirstName varchar(100) NULL,   
   SSN char(9) NOT NULL, 
   LastName varchar(100) NOT NULL,   
   Phone varchar(12) NULL,   
   Email varchar(100) NULL);  
```

"TestUser" Zezwalaj na dostęp do wszystkich kolumn z wyjątkiem SSN kolumny, która zawiera poufne dane: 

```sql  
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;   
``` 

Zapytania są wykonywane zgodnie z "TestUser" zakończy się niepowodzeniem, jeśli zawierają one kolumny SSN:

```sql  
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'. 
``` 

## <a name="use-cases"></a>Przypadki użycia
Przykłady sposobu ze specyfikacją CLS używania dzisiaj: 
- Przedsiębiorstwo branży usług finansowych umożliwia tylko konta menedżerów dostępu do numerów ubezpieczenia społecznego klienta (SSN), numerów telefonów i innych informacji umożliwiających identyfikację (dane osobowe).
- Dostawca opieki zdrowotnej umożliwia tylko lekarzy i pielęgniarek na dostęp do poufnych medyczne rekordy nie zezwalając pracownicy działu rozliczeń do wyświetlania tych danych.
