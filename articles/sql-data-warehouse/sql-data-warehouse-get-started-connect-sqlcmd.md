---
title: Nawiązywanie połączenia przy użyciu narzędzia sqlcmd
description: Użyj narzędzia wiersza polecenia sqlcmd, aby nawiązać połączenie z Azure SQL Data Warehouse i wykonać zapytanie.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e4b432e0be0cdded5089965b9d272aa82e31bd36
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685741"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Nawiązywanie połączenia z usługą SQL Data Warehouse przy użyciu narzędzia sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Program Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Użyj narzędzia wiersza polecenia [sqlcmd][sqlcmd] , aby nawiązać połączenie z Azure SQL Data Warehouse i wykonać zapytanie.  

## <a name="1-connect"></a>1. Połącz
Aby rozpocząć pracę z narzędziem [sqlcmd][sqlcmd], otwórz wiersz polecenia i wpisz **sqlcmd**, a następnie podaj parametry połączenia z bazą danych usługi SQL Data Warehouse. Wymagane parametry połączenia to:

* **Serwer (-S):** serwer w postaci `<`nazwa_serwera`>`.database.windows.net
* **Baza danych (-d):** nazwa bazy danych.
* **Włącz cytowane identyfikatory (-I):** cytowane identyfikatory muszą być włączone w celu połączenia z wystąpieniem usługi SQL Data Warehouse.

Aby użyć uwierzytelniania programu SQL Server, należy dodać parametry nazwy użytkownika/hasła:

* **Użytkownik (-U):** użytkownik serwera w formie `<`Użytkownik`>`
* **Hasło (-P):** hasło skojarzone z użytkownikiem.

Na przykład parametry połączenia mogą wyglądać następująco:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Aby użyć zintegrowanego uwierzytelniania usługi Azure Active Directory, należy dodać parametry usługi Azure Active Directory:

* **Uwierzytelnianie usługi Azure Active Directory (-G):** używaj usługi Azure Active Directory do uwierzytelniania

Na przykład parametry połączenia mogą wyglądać następująco:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Musisz [włączyć uwierzytelnianie usługi Azure Active Directory](sql-data-warehouse-authentication.md), aby uwierzytelniać przy użyciu usługi Active Directory.
> 
> 

## <a name="2-query"></a>2. zapytanie
Po nawiązaniu połączenia można wydawać dowolne obsługiwane instrukcje języka Transact-SQL dotyczące wystąpienia.  W tym przykładzie zapytania są przesyłane w trybie interaktywnym.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

W następnych przykładach przedstawiono, jak można uruchamiać zapytania w trybie wsadowym przy użyciu opcji -Q lub przesyłania potokiem instrukcji języka SQL do narzędzia sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o opcjach dostępnych w programie sqlcmd, zobacz [dokumentację narzędzia sqlcmd][sqlcmd] .

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
