---
title: Jak utworzyć bilet pomocy technicznej
description: Tworzenie biletu pomocy technicznej w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 12a977e26f458fa8ee8a58c44985d9d1ae47d5c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692724"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Tworzenie biletu pomocy technicznej dotyczącej usługi SQL Data Warehouse
Jeśli masz jakiekolwiek problemy z usługą SQL Data Warehouse, utwórz bilet pomocy technicznej, aby zespół inżynierów mógł Ci pomóc.

## <a name="create-a-support-ticket"></a>Tworzenie biletu pomocy technicznej
1. Otwórz [portal Azure][Azure portal].
2. Na ekranie głównym kliknij kartę **Pomoc i obsługa techniczna**.
   
    ![Pomoc i obsługa techniczna](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. W bloku Pomoc i obsługa techniczna kliknij pozycję **Nowe żądanie obsługi** i wypełnij blok **Podstawowe**.

   Wybierz odpowiedni [Plan pomocy technicznej platformy Azure][Azure support plan].
   
   * Pomoc techniczna związana z **rozliczeniami, limitami przydziałów i zarządzaniem subskrypcjami** jest dostępna na wszystkich poziomach pomocy technicznej.
   * Pomoc techniczna dotycząca **usuwania poprawek** jest świadczona za pośrednictwem pomocy technicznej [Developer][Developer], [Standard][Standard], [Professional Direct][Professional Direct]i [Premier][Premier] . Naprawa w razie awarii dotyczy problemów napotykanych przez klientów podczas korzystania z platformy Azure, jeśli istnieje uzasadnione podejrzenie, że problem leży po stronie firmy Microsoft.
   * **Wsparcie dla deweloperów** i **usługi doradcze** są dostępne na poziomach pomocy technicznej [Professional Direct][Professional Direct] i [Premium][Premier]. 
     
     Jeśli masz plan pomocy technicznej Premium, możesz zgłaszać także problemy związane z usługą SQL Data Warehouse na portalu [Microsoft Premier Online][Microsoft Premier online portal].  Zobacz [plany pomocy technicznej platformy Azure][Azure support plan] , aby dowiedzieć się więcej o różnych planach pomocy technicznej, takich jak zakres, czasy odpowiedzi, Cennik itp.  Często zadawane pytania dotyczące pomocy technicznej platformy Azure można znaleźć w artykule [FAQ pomocy technicznej dla platformy Azure][Azure support FAQs].  
        
     ![Blok Podstawowe](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Blok Podstawowe1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Wypełnij blok **Problem**.
    ![Blok Problem](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Domyślnie każdy serwer SQL (na przykład myserver.database.windows.net) ma **limit przydziału jednostek DTU** wynoszący 45 000. Ten limit przydziału jest po prostu limitem bezpieczeństwa. Możesz zwiększyć limit przydziału, tworząc bilet pomocy technicznej i wybierając pozycję *Limit przydziału* jako typ żądania. Aby obliczyć potrzeby jednostek DTU, pomnóż 7,5 przez łączną potrzebną [jednostek dwu][DWU] . Jeśli na przykład chcesz hostować dwie bazy danych z poziomem celu usługi DW6000 na jednym serwerze SQL, zażądaj limitu przydziału wynoszącego 90 000.  Aktualne użycie jednostek DTU możesz zobaczyć z poziomu bloku serwera SQL w portalu. Limit przydziału jednostek DTU obejmuje zarówno wstrzymane, jak i niewstrzymane bazy danych. 
   > 
   > 
   
5. Wprowadź swoje **informacje kontaktowe**.
   ![Informacje_kontaktowe](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Kliknij przycisk **Utwórz**, aby przesłać żądanie pomocy technicznej.

## <a name="monitor-a-support-ticket"></a>Monitorowanie biletu pomocy technicznej
Po przesłaniu żądania pomocy technicznej zespół pomocy technicznej platformy Azure skontaktuje się z Tobą. Aby sprawdzić stan żądania i szczegółowe informacje, kliknij pozycję **Wszystkie żądania obsługi** na pulpicie nawigacyjnym.

![Sprawdzanie stanu](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Inne zasoby
Ponadto można połączyć się ze społecznością usługi SQL Data Warehouse w witrynie [Stack Overflow][Stack Overflow] lub na [forum MSDN dotyczącym usługi Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

