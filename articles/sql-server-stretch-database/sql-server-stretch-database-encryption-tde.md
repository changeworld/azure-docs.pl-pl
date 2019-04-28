---
title: Włączanie technologii Transparent Data Encryption for Stretch Database — Azure | Dokumentacja firmy Microsoft
description: Włącz przezroczyste szyfrowanie danych (TDE) dla programu SQL Server Stretch Database na platformie Azure
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
ms.openlocfilehash: 1e40e3d9eb1231666acda89c752ebc8f517e8fc6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708904"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Włączanie technologii Transparent Data Encryption (TDE) for Stretch Database na platformie Azure
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Przezroczyste szyfrowanie danych (TDE) ułatwia ochronę przed złośliwymi działaniami, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian do aplikacji.

Funkcja TDE szyfruje magazyn całą bazę danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych. Klucz szyfrowania bazy danych jest chroniona za pomocą wbudowanego serwera certyfikatu. Certyfikat wbudowanego serwera jest unikatowy dla każdego serwera usługi Azure. Microsoft automatycznie przełącza tych certyfikatów, co 90 dni. Aby uzyskać ogólny opis funkcji TDE, zobacz [Transparent Data Encryption (TDE)].

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Aby włączyć funkcję TDE dla platformy Azure bazy danych, która zapisuje dane zmigrowane z obsługą usługi Stretch program SQL Server, wykonaj następujące czynności:

1. Otwórz bazę danych w [witryny Azure portal](https://portal.azure.com)
2. W bloku bazy danych kliknij **ustawienia** przycisku
3. Wybierz **technologii Transparent data encryption** opcji ![][1]
4. Wybierz **na** ustawienia, a następnie wybierz **Zapisz**
   ![][2]

## <a name="disabling-encryption"></a>Wyłączenie szyfrowania
Można wyłączyć funkcję TDE dla platformy Azure bazy danych, która zapisuje dane zmigrowane z obsługą usługi Stretch program SQL Server, wykonaj następujące czynności:

1. Otwórz bazę danych w [witryny Azure portal](https://portal.azure.com)
2. W bloku bazy danych kliknij **ustawienia** przycisku
3. Wybierz **technologii Transparent data encryption** opcji
4. Wybierz **poza** ustawienia, a następnie wybierz **Zapisz**

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
