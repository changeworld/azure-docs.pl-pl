---
title: 'Witryna Azure portal: baza danych SQL dynamiczne maskowanie danych | Dokumentacja firmy Microsoft'
description: Jak rozpocząć pracę z bazą danych SQL dynamiczne maskowanie danych w witrynie Azure portal
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ronitr
ms.reviewer: vanto
ms.openlocfilehash: 8aaaa9f2b8821be18b321c202e62333858d7d266
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543832"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Rozpoczynanie pracy z usługą SQL Database dane dynamiczne maskowanie przy użyciu witryny Azure portal

W tym artykule dowiesz się, jak zaimplementować [dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md) w witrynie Azure portal. Możesz również wdrożyć przy użyciu maskowania danych dynamicznych [poleceń cmdlet usługi Azure SQL Database](https://docs.microsoft.com/powershell/module/azurerm.sql/) lub [interfejsu API REST](https://msdn.microsoft.com/library/dn505719.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Skonfigurować dynamiczne maskowanie danych dla bazy danych przy użyciu witryny Azure portal
1. Uruchamianie witryny Azure portal pod [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony ustawień bazy danych, która zawiera dane poufne, który chcesz maski.
3. Kliknij przycisk **dynamiczne maskowanie danych** Kafelek, który uruchamia **dynamiczne maskowanie danych** strona konfiguracji.
   
   * Alternatywnie można przewijać w dół do **operacji** sekcji, a następnie kliknij przycisk **dynamiczne maskowanie danych**.
     
     ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. W **dynamiczne maskowanie danych** strony konfiguracji, może zostać wyświetlony niektóre kolumny bazy danych, które aparat zaleceń został oflagowanych maskowania. Aby zaakceptować zalecenia, wystarczy kliknąć **Dodaj maskę** dla co najmniej jednej kolumny i maski jest tworzony w zależności od typu domyślnego dla tej kolumny. Funkcji maskowania można zmienić, klikając regułę maskowania i edytowanie maskowanie format pola do innego formatu wybranych przez użytkownika. Należy kliknąć przycisk **Zapisz** można zapisać ustawień.
   
    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Można dodać do maski dla każdej kolumny w bazie danych, w górnej części **dynamiczne maskowanie danych** strony konfiguracji, kliknij przycisk **Dodaj maskę** otworzyć **Dodaj regułę maskowania** strony konfiguracji .
   
    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Wybierz **schematu**, **tabeli** i **kolumny** do zdefiniowania pola wyznaczone do maskowania.
7. Wybierz **maskowania Format pola** z listy kategorii maskowanie danych poufnych.
   
    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. Kliknij przycisk **Zapisz** w stronę reguły, aby zaktualizować zestaw maskowania reguły w dynamiczne maskowanie zasad danych maskowania danych.
9. Wpisz użytkowników SQL lub tożsamości usługi AAD, które powinny być wykluczeni z maskowania i mieć dostęp, aby usunąć ich maskowanie danych poufnych. Powinno to być rozdzielaną średnikami listę użytkowników. Użytkownicy z uprawnieniami administratora zawsze miały dostęp do oryginalnych pozbawiony maskowania danych.
   
    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Aby uniemożliwić warstwy aplikacji można wyświetlić danymi poufnymi w przypadku użytkowników uprzywilejowanych aplikacji, Dodaj użytkownika SQL lub tożsamości usługi AAD, używanych przez aplikację do wykonywania zapytań w bazie danych. Zdecydowanie zaleca się, że ta lista zawiera minimalnej liczby użytkowników uprzywilejowanych, aby zminimalizować zagrożenia dla poufnych danych.
   > 
   > 
10. Kliknij przycisk **Zapisz** danych maskowania strony konfiguracji, aby zapisać nowe lub zaktualizowane maskowania zasad.


## <a name="next-steps"></a>Kolejne kroki

* Aby zapoznać się z omówieniem dynamiczne maskowanie danych, zobacz [dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md).
* Możesz również wdrożyć przy użyciu maskowania danych dynamicznych [poleceń cmdlet usługi Azure SQL Database](https://docs.microsoft.com/powershell/module/azurerm.sql/) lub [interfejsu API REST](https://msdn.microsoft.com/library/dn505719.aspx).
