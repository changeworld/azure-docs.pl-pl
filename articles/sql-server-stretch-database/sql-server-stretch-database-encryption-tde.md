---
title: Włącz przezroczystego szyfrowania danych dla bazy danych Stretch - Azure | Dokumentacja firmy Microsoft
description: Włącz przezroczystego szyfrowania danych (funkcji TDE) dla danych programu SQL Server Stretch na platformie Azure
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: barbkess
editor: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
ms.openlocfilehash: ceb355d2ba872ed5d3886c6dc82ca75b1854db0a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23872925"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Włącz przezroczystego szyfrowania danych (funkcji TDE) dla baza danych Stretch na platformie Azure
> [!div class="op_single_selector"]
> * [Azure portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Funkcji przezroczystego szyfrowania danych (TDE) pomaga w ochronie przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji.

Funkcji TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych. Klucz szyfrowania bazy danych jest chroniony za pomocą certyfikatu wbudowanego serwera. Certyfikat serwera wbudowanych jest unikatowy dla każdego serwera usługi Azure. Microsoft automatycznie przełącza tych certyfikatów, co najmniej co 90 dni. Ogólny opis funkcji TDE, zobacz [funkcji przezroczystego szyfrowania danych (TDE)].

## <a name="enabling-encryption"></a>Włączenie szyfrowania
Aby włączyć funkcji TDE platformy Azure bazy danych, które są przechowywane dane migracji z bazy danych programu SQL Server z obsługą odcinek, wykonaj następujące czynności:

1. Otworzyć bazy danych w [portalu Azure](https://portal.azure.com)
2. W bloku bazy danych, kliknij przycisk **ustawienia** przycisku
3. Wybierz **przezroczystego szyfrowania danych** opcji![][1]
4. Wybierz **na** ustawienia, a następnie wybierz **Zapisz**
   ![][2]

## <a name="disabling-encryption"></a>Wyłączenie szyfrowania
Wyłączenie funkcji TDE platformy Azure bazy danych, które są przechowywane dane migracji z bazy danych programu SQL Server z obsługą odcinek, wykonaj następujące czynności:

1. Otworzyć bazy danych w [portalu Azure](https://portal.azure.com)
2. W bloku bazy danych, kliknij przycisk **ustawienia** przycisku
3. Wybierz **przezroczystego szyfrowania danych** opcji
4. Wybierz **poza** ustawienia, a następnie wybierz **Zapisz**

<!--Anchors-->
[funkcji przezroczystego szyfrowania danych (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
