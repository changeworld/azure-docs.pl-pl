---
title: Przezroczyste szyfrowanie danych w usłudze SQL Data Warehouse (Portal) | Dokumentacja firmy Microsoft
description: Transparent Data Encryption (TDE) w usłudze SQL Data Warehouse
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: db6e2ad41c842e9118b2c004f8024afd894347b4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453256"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Rozpoczynanie pracy z przezroczystego szyfrowania danych (TDE) w usłudze SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Uwierzytelnianie](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby włączyć przezroczystego szyfrowania danych (TDE), musi być administratorem lub członkiem roli dbmanager.

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Aby włączyć funkcję TDE dla usługi SQL Data Warehouse, wykonaj następujące czynności:

1. Otwórz bazę danych w [witryny Azure portal](https://portal.azure.com)
2. W bloku bazy danych kliknij **ustawienia** przycisku
3. Wybierz **technologii Transparent data encryption** opcji ![][1]
4. Wybierz **na** ustawienie ![][2]
5. Wybierz **Zapisz**
   ![][3]  

## <a name="disabling-encryption"></a>Wyłączenie szyfrowania
Aby wyłączyć funkcję TDE dla usługi SQL Data Warehouse, wykonaj następujące czynności:

1. Otwórz bazę danych w [witryny Azure portal](https://portal.azure.com)
2. W bloku bazy danych kliknij **ustawienia** przycisku
3. Wybierz **technologii Transparent data encryption** opcji ![][1]
4. Wybierz **poza** ustawienie ![][4]
5. Wybierz **Zapisz**
   ![][5]  

## <a name="encryption-dmvs"></a>Szyfrowanie dynamiczne widoki zarządzania
Szyfrowanie może zostać potwierdzony przy użyciu następujących widoków DMV:

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
