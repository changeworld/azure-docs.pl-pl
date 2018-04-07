---
title: 'Portalu Azure: maskowania danych dynamicznych bazy danych SQL | Dokumentacja firmy Microsoft'
description: Jak rozpocząć pracę z bazy danych SQL maskowania danych dynamicznych w portalu Azure
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: ronitr
ms.openlocfilehash: c77ccbd85f6dd31ae2ecfb3d2965e384a1915e28
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Rozpoczynanie pracy z danymi dynamicznymi bazy danych SQL, maskowanie przy użyciu portalu Azure

W tym artykule przedstawiono sposób wykonania [maskowania danych dynamicznych](sql-database-dynamic-data-masking-get-started.md) z portalu Azure. Można też wdrożyć przy użyciu maskowania danych dynamicznych [poleceń cmdlet usługi Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084.aspx) lub [interfejsu API REST](https://msdn.microsoft.com/library/dn505719.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Konfigurowanie dynamiczne maskowanie danych dla bazy danych przy użyciu portalu Azure
1. Uruchamianie portalu Azure pod adresem [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony ustawień bazy danych, która zawiera dane poufne, które do zamaskowania.
3. Kliknij przycisk **dynamicznego maskowania danych** kafelka, który uruchamia **dynamicznego maskowania danych** strony konfiguracji.
   
   * Alternatywnie można przewijać w dół do **operacji** sekcji, a następnie kliknij przycisk **dynamicznego maskowania danych**.
     
     ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. W **dynamicznego maskowania danych** strony konfiguracji, mogą pojawić niektóre kolumny bazy danych, które aparat zalecenia ma flagę maskowania. Aby zaakceptować zalecenia, kliknij **Dodaj maskę** dla co najmniej jedną kolumnę i maska jest tworzone w oparciu domyślny typ dla tej kolumny. Funkcji maskowania można zmienić, klikając reguła maskowania i edytowanie maskowania format pola w innym formacie wybranych przez użytkownika. Należy kliknąć przycisk **zapisać** Aby zapisać ustawienia.
   
    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Aby dodać maski dla każdej kolumny w bazie danych, w górnej części **dynamicznego maskowania danych** strony konfiguracji, kliknij przycisk **Dodaj maskę** można otworzyć **Dodaj regułę maskowania** na stronie konfiguracji .
   
    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Wybierz **schematu**, **tabeli** i **kolumny** do definiowania wskazanego pola do maskowania.
7. Wybierz **Format maskowania pola** z listy Kategorie maskowanie danych poufnych.
   
    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. Kliknij przycisk **zapisać** danych maskowania reguły strony, aby zaktualizować zestaw maskowania reguły w dynamiczne maskowanie zasad danych.
9. Wpisz użytkowników SQL lub tożsamości usługi AAD, które powinny być wykluczeni z maskowania i mają dostęp do danych poufnych pozbawiony maskowania. Powinno to być rozdzielana średnikami lista użytkowników. Użytkownicy z uprawnieniami administratora zawsze miały dostęp do oryginalnych danych pozbawiony maskowania.
   
    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Aby uniemożliwić warstwy aplikacji mogą wyświetlać dane poufne dla użytkowników aplikacji uprzywilejowane, Dodaj użytkownika SQL lub tożsamości usługi AAD aplikacji nawiązywał w bazie danych. Zdecydowanie zaleca się, że ta lista zawiera minimalnej liczbie uprzywilejowani użytkownicy, aby zminimalizować narażenie poufnych danych.
   > 
   > 
10. Kliknij przycisk **zapisać** danych maskowania strony konfiguracji, aby zapisać nowe lub zaktualizowane maskowania zasad.


## <a name="next-steps"></a>Kolejne kroki

* Omówienie maskowania danych dynamicznych, zobacz [maskowania danych dynamicznych](sql-database-dynamic-data-masking-get-started.md).
* Można też wdrożyć przy użyciu maskowania danych dynamicznych [poleceń cmdlet usługi Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084.aspx) lub [interfejsu API REST](https://msdn.microsoft.com/library/dn505719.aspx).
