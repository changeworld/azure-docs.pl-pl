---
title: Włączanie Transparent Data Encryption dla Stretch Database platformy Azure | Microsoft Docs
description: Włącz Transparent Data Encryption (TDE) dla SQL Server Stretch Database na platformie Azure
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.openlocfilehash: 61f556476958484b78b9c3dff2583eb6db043637
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "66003038"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Włącz Transparent Data Encryption (TDE) dla Stretch Database na platformie Azure
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data Encryption (TDE) pomaga chronić przed zagrożeniem złośliwym działaniem, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji.

TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego zwanego kluczem szyfrowania bazy danych. Klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera platformy Azure. Firma Microsoft automatycznie obraca te certyfikaty co najmniej co 90 dni. Ogólny opis TDE można znaleźć w temacie [transparent Data Encryption (TDE)].

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Aby włączyć TDE dla bazy danych platformy Azure, która przechowuje dane migrowane z SQL Server bazy danych z obsługą rozciągnięcia, wykonaj następujące czynności:

1. Otwórz bazę danych w [Azure Portal](https://portal.azure.com)
2. W bloku baza danych kliknij przycisk **Ustawienia**
3. Wybierz opcję **przezroczystego szyfrowania danych**![][1]
4. Wybierz ustawienie **Włącz** , a następnie wybierz pozycję **Zapisz** .
   ![][2]

## <a name="disabling-encryption"></a>Wyłączanie szyfrowania
Aby wyłączyć TDE dla bazy danych platformy Azure, która przechowuje dane migrowane z SQL Server bazy danych z obsługą rozciągnięcia, wykonaj następujące czynności:

1. Otwórz bazę danych w [Azure Portal](https://portal.azure.com)
2. W bloku baza danych kliknij przycisk **Ustawienia**
3. Wybierz opcję **przezroczystego szyfrowania danych**
4. Wybierz ustawienie **wyłączone** , a następnie wybierz pozycję **Zapisz** .

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
