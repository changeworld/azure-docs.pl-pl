---
title: Transparent Data Encryption w SQL Data Warehouse (Portal) | Microsoft Docs
description: Transparent Data Encryption (TDE) w SQL Data Warehouse
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: kavithaj
ms.reviewer: rortloff
ms.openlocfilehash: e756049110f7d4a81950abf6ebbe73edb3e3ca0a
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "65143163"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Wprowadzenie do Transparent Data Encryption (TDE) w SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Przegląd zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Uwierzytelnianie](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Szyfrowanie (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby włączyć Transparent Data Encryption (TDE), musisz być administratorem lub członkiem roli dbmanager.

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Aby włączyć TDE dla SQL Data Warehouse, wykonaj następujące kroki:

1. Otwórz bazę danych w [Azure Portal](https://portal.azure.com)
2. W bloku baza danych kliknij przycisk **Ustawienia**
3. Wybierz opcję **przezroczystego szyfrowania danych**![][1]
4. Wybierz ustawienie **przy** użyciu![][2]
5. Wybierz pozycję **Zapisz**
   ![][3]  

## <a name="disabling-encryption"></a>Wyłączanie szyfrowania
Aby wyłączyć TDE dla SQL Data Warehouse, wykonaj następujące czynności:

1. Otwórz bazę danych w [Azure Portal](https://portal.azure.com)
2. W bloku baza danych kliknij przycisk **Ustawienia**
3. Wybierz opcję **przezroczystego szyfrowania danych**![][1]
4. Wybierz ustawienie **wyłączone**![][4]
5. Wybierz pozycję **Zapisz**
   ![][5]  

## <a name="encryption-dmvs"></a>Szyfrowanie widoków DMV
Szyfrowanie można potwierdzić przy użyciu następujących widoków DMV:

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
