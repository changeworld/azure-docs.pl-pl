---
title: Nawiązywanie połączenia z usługą Azure SQL Data Warehouse — narzędzie sqlcmd | Microsoft Docs
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
ms.openlocfilehash: f3b93660fb9f8f3b0bfdddc37105b9e998ed9eee
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479503"
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

* **Serwer (-S):** Serwer w postaci `<`Server Name`>`. Database.Windows.NET
* **Baza danych (-d):** Nazwa bazy danych.
* **Włącz identyfikatory ujęte w cudzysłów (-I):** Identyfikatory w cudzysłowie muszą być włączone, aby można było połączyć się z wystąpieniem SQL Data Warehouse.

Aby użyć uwierzytelniania programu SQL Server, należy dodać parametry nazwy użytkownika/hasła:

* **Użytkownik (-U):** Użytkownik serwera w formularzu `<`użytkownika`>`
* **Hasło (-P):** Hasło skojarzone z użytkownikiem.

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

## <a name="2-query"></a>2. Zapytanie
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
