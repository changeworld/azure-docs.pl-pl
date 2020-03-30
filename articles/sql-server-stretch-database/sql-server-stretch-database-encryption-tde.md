---
title: Włącz przezroczyste szyfrowanie danych dla rozciągliwej bazy danych
description: Włącz przezroczyste szyfrowanie danych (TDE) dla bazy danych rozciągania programu SQL Server na platformie Azure
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
ms.custom: seo-lt-2019
ms.openlocfilehash: 465338bb793ad7b4fc406e9d1c073b9dc0dc06c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034011"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Włącz przezroczyste szyfrowanie danych (TDE) dla bazy danych stretch na platformie Azure
> [!div class="op_single_selector"]
> * [Portal Azure](sql-server-stretch-database-encryption-tde.md)
> * [Tsql](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data Encryption (TDE) pomaga chronić przed zagrożeniem złośliwej aktywności, wykonując szyfrowanie w czasie rzeczywistym i odszyfrowywanie bazy danych, skojarzone kopie zapasowe i pliki dziennika transakcji w spoczynku bez konieczności wprowadzania zmian w aplikacji.

Technologia TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego nazywanego kluczem szyfrowania bazy danych. Klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera platformy Azure. Firma Microsoft automatycznie obraca te certyfikaty co najmniej co 90 dni. Aby uzyskać ogólny opis TDE, zobacz [Przezroczyste szyfrowanie danych (TDE)].

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Aby włączyć TDE dla bazy danych platformy Azure, która przechowuje dane zmigrowane z bazy danych programu SQL Server z włączoną funkcją Stretch, wykonaj następujące czynności:

1. Otwieranie bazy danych w [witrynie Azure portal](https://portal.azure.com)
2. W bloku bazy danych kliknij przycisk **Ustawienia**
3. Wybierz opcję **Szyfrowanie danych przezroczyste**![][1]
4. Wybierz ustawienie **Włączone,** a następnie wybierz pozycję **Zapisz**
   ![][2]

## <a name="disabling-encryption"></a>Wyłączanie szyfrowania
Aby wyłączyć TDE dla bazy danych platformy Azure, która przechowuje dane zmigrowane z bazy danych programu SQL Server z włączoną funkcją Stretch, wykonaj następujące czynności:

1. Otwieranie bazy danych w [witrynie Azure portal](https://portal.azure.com)
2. W bloku bazy danych kliknij przycisk **Ustawienia**
3. Wybierz opcję **Szyfrowanie danych przezroczyste**
4. Wybierz ustawienie **Wyłączone,** a następnie wybierz pozycję **Zapisz**

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
