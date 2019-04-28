---
title: Migrowanie rozwiązania do usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące migracji do pobierania rozwiązania na platformę Azure SQL Data Warehouse.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 04/17/2018
ms.date: 03/25/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 04c921282d3591e7326d326c230bf72e7f5c1812
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776224"
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migrowanie rozwiązania do usługi Azure SQL Data Warehouse
Zobacz, co jest zaangażowana w migrację istniejącego rozwiązania bazy danych Azure SQL Data Warehouse. 

## <a name="profile-your-workload"></a>Profilu obciążenia
Przed migracją, ma być niektórych SQL Data Warehouse to właściwe rozwiązanie dla obciążenia. Usługa SQL Data Warehouse to system rozproszonych przeznaczone do wykonywania analiz na dużych ilości danych.  Migrowanie do usługi SQL Data Warehouse wymaga niektórych zmian projektowych, które nie są zbyt trudne do zrozumienia, ale może zająć trochę czasu, aby zaimplementować. Jeśli Twoja firma wymaga hurtowni danych klasy korporacyjnej, korzyści są warte włożonej pracy. Jednak jeśli nie potrzebujesz możliwości usługi SQL Data Warehouse jest bardziej opłacalna, aby użyć programu SQL Server lub usługi Azure SQL Database.

Należy rozważyć użycie SQL Data Warehouse po użytkownik:
- Ma co najmniej jeden terabajtów danych
- Zamierzasz uruchamiać funkcje analityczne na dużych ilości danych
- Konieczne jest możliwość skalowania zasobów obliczeniowych i magazynu 
- Czy chcesz zmniejszyć koszty wstrzymywanie zasobów obliczeniowych, gdy nie są potrzebne.

Usługa SQL Data Warehouse nie jest używany do operacyjnej obciążenia (OLTP), które mają:
- Wysoka częstotliwość operacji odczytu i zapisu
- Wybiera dużą liczbę pojedynczego wystąpienia
- Dużej ilości danych z poleceń wstawiania pojedynczy wiersz
- Wiersz po wierszu przetwarzania potrzeb
- Niezgodnych formatach (JSON, XML)

## <a name="plan-the-migration"></a>Planowanie migracji

Gdy okaże się przeprowadzić migrację istniejącego rozwiązania do usługi SQL Data Warehouse, należy zaplanować migrację przed rozpoczęciem pracy. 

Jeden cel, planowania jest upewnij się, że Twoje dane, swoje schematy tabeli i kodzie są zgodne z usługą SQL Data Warehouse. Istnieją pewne różnice zgodności w celu obejścia między bieżącego systemu i SQL Data Warehouse. Ponadto migracja dużych ilości danych do platformy Azure wymaga czasu. Starannego planowania przyspiesza, trwa pobieranie danych do platformy Azure. 

Inny cel, planowania jest wprowadzanie zmian projektu, upewnij się, że rozwiązanie korzysta z wysoką wydajność zapytań, których usługa SQL Data Warehouse zaprojektowano pod kątem. Projektowanie magazyny danych w skali wprowadza różnorodności, wzorce i dlatego tradycyjnych metod nie są zawsze najlepsze. Mimo że można dostosować niektóre projektu po migracji, wcześniej wprowadzania zmian w procesie będzie zaoszczędzić czas później.

Do przeprowadzenia prawidłowej migracji, należy migrować swoje schematy tabeli, kodu i danych. Aby uzyskać wskazówki dotyczące tych tematów migracji zobacz:

-  [Migrowanie usługi schematów](sql-data-warehouse-migrate-schema.md)
-  [Migrowanie kodu](sql-data-warehouse-migrate-code.md)
-  [Migrowanie danych](sql-data-warehouse-migrate-data.md). 

## <a name="next-steps"></a>Kolejne kroki
CAT (zespół doradczy klientów) ma pewne wspaniałe wskazówki SQL Data Warehouse, publikują za pośrednictwem naszych blogach.  Zapoznaj się z ich artykułu [migrowanie danych do usługi Azure SQL Data Warehouse w praktyce] [ Migrating data to Azure SQL Data Warehouse in practice] Aby uzyskać dodatkowe wskazówki dotyczące migracji.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/

<!--Update_Description: update meta properties, wording update-->