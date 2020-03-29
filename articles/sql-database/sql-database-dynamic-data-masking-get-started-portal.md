---
title: 'Portal Azure: dynamiczne maskowanie danych'
description: Jak rozpocząć pracę z dynamicznym maskowaniem danych bazy danych SQL w witrynie Azure portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: a8098b31c6b389b640fc03e756da44c70d9f3a70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722122"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Wprowadzenie do dynamicznego maskowania danych bazy danych SQL za pomocą portalu Azure

W tym artykule pokazano, jak zaimplementować [dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md) za pomocą witryny Azure portal. Można również zaimplementować dynamiczne maskowanie danych przy użyciu [poleceń cmdlet usługi Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) lub interfejsu API [REST](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Konfigurowanie dynamicznego maskowania danych dla bazy danych przy użyciu portalu Azure

1. Uruchom portal Platformy [https://portal.azure.com](https://portal.azure.com)Azure pod adresem .
2. Przejdź do strony ustawień bazy danych zawierającej poufne dane, które chcesz zamaskować.
3. Kliknij kafelek **Dynamiczne maskowanie danych,** który uruchamia stronę konfiguracji **dynamiczne maskowanie danych.**

   * Alternatywnie można przewinąć w dół do sekcji **Operacje** i kliknąć **dynamiczne maskowanie danych**.

     ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. Na stronie konfiguracji **maskowania danych dynamicznych** mogą zostać wyświetlone kolumny bazy danych, które aparat rekomendacji oflagował do maskowania. Aby zaakceptować zalecenia, wystarczy kliknąć przycisk **Dodaj maskę** dla jednej lub więcej kolumn, a maska jest tworzona na podstawie domyślnego typu dla tej kolumny. Funkcję maskowania można zmienić, klikając regułę maskowania i edytując format pola maskowania na inny wybrany format. Pamiętaj, aby kliknąć przycisk **Zapisz,** aby zapisać ustawienia.

    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Aby dodać maskę dla dowolnej kolumny w bazie danych, u góry strony konfiguracji **dynamiczne maskowanie danych** kliknij pozycję **Dodaj maskę,** aby otworzyć stronę konfiguracji **Dodaj regułę maskowania.**

    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Wybierz **schemat**, **tabelę** i **kolumnę,** aby zdefiniować wyznaczone pole do maskowania.
7. Wybierz **format pola maskowania** z listy kategorii maskowania poufnych danych.

    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Kliknij **pozycję Zapisz** na stronie reguły maskowania danych, aby zaktualizować zestaw reguł maskowania w zasadach dynamicznego maskowania danych.
9. Wpisz użytkowników SQL lub tożsamości usługi AAD, które powinny być wykluczone z maskowania i mieć dostęp do zdemaskowanych poufnych danych. Powinna to być oddzielona średnikami lista użytkowników. Użytkownicy z uprawnieniami administratora zawsze mają dostęp do oryginalnych zdemaskowanych danych.

    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Aby warstwa aplikacji mogła wyświetlać poufne dane dla użytkowników uprzywilejowanych aplikacji, dodaj użytkownika SQL lub tożsamość usługi AAD, której aplikacja używa do wykonywania zapytań do bazy danych. Zdecydowanie zaleca się, aby ta lista zawierała minimalną liczbę uprzywilejowanych użytkowników w celu zminimalizowania narażenia poufnych danych.

10. Kliknij **pozycję Zapisz** na stronie konfiguracji maskowania danych, aby zapisać nową lub zaktualizowaną zasadę maskowania.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem dynamicznego maskowania danych, zobacz [dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md).
* Można również zaimplementować dynamiczne maskowanie danych przy użyciu [poleceń cmdlet usługi Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) lub interfejsu API [REST](https://docs.microsoft.com/rest/api/sql/).
