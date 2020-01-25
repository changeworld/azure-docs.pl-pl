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
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717841"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Tworzenie biletu pomocy technicznej dotyczącej usługi SQL Data Warehouse
Jeśli masz problemy z SQL Data Warehouse, Utwórz bilet pomocy technicznej, aby zespół pomocy technicznej mógł Ci pomóc.

## <a name="create-a-support-ticket"></a>Utwórz bilet pomocy technicznej
1. Otwórz [Portalu Azure](https://portal.azure.com/).
2. Na ekranie głównym kliknij kartę **Pomoc i obsługa techniczna**.
   
    ![Pomoc i obsługa techniczna](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. W bloku Pomoc i obsługa techniczna kliknij pozycję **Nowe żądanie obsługi** i wypełnij blok **Podstawowe**.

   Wybierz odpowiedni [Plan pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * Pomoc techniczna związana z **rozliczeniami, limitami przydziałów i zarządzaniem subskrypcjami** jest dostępna na wszystkich poziomach pomocy technicznej.
   * Pomoc techniczna dotycząca **usuwania poprawek** jest świadczona za pośrednictwem pomocy technicznej [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)i [Premier](https://azure.microsoft.com/support/plans/premier/) . Problemy wymagające usunięcia awarii to problemy, które wystąpiły w trakcie korzystania z systemu Azure i w przypadku których występuje uzasadnione przypuszczenie, że zostały spowodowane przez Microsoft.
   * **Wsparcie dla deweloperów** i **usługi doradcze** są dostępne na poziomach pomocy technicznej [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) i [Premium](https://azure.microsoft.com/support/plans/premier/). 
     
     Jeśli masz plan pomocy technicznej Premium, możesz zgłaszać także problemy związane z usługą SQL Data Warehouse na portalu [Microsoft Premier Online](https://premier.microsoft.com/). Zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) , aby dowiedzieć się więcej o różnych planach pomocy technicznej, takich jak zakres, czasy odpowiedzi, Cennik itp.  Często zadawane pytania dotyczące pomocy technicznej platformy Azure można znaleźć w artykule [FAQ pomocy technicznej dla platformy Azure](https://azure.microsoft.com/support/faq/).  
        
     ![Blok Podstawowe](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Blok Podstawowe1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Wypełnij blok **Problem**.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Domyślnie każdy serwer SQL (na przykład myserver.database.windows.net) ma **limit przydziału jednostek DTU** wynoszący 45 000. Ten limit przydziału jest po prostu limitem bezpieczeństwa. Możesz zwiększyć limit przydziału, tworząc bilet pomocy technicznej i wybierając pozycję *Limit przydziału* jako typ żądania. Aby obliczyć potrzeby jednostek DTU, pomnóż 7,5 przez łączną potrzebną [jednostek dwu](sql-data-warehouse-overview-what-is.md) . Jeśli na przykład chcesz hostować dwie bazy danych z poziomem celu usługi DW6000 na jednym serwerze SQL, zażądaj limitu przydziału wynoszącego 90 000.  Aktualne użycie jednostek DTU możesz zobaczyć z poziomu bloku serwera SQL w portalu. Limit przydziału jednostek DTU obejmuje zarówno wstrzymane, jak i niewstrzymane bazy danych. 
   > 
   > 
   
5. Wprowadź swoje **informacje kontaktowe**.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Kliknij przycisk **Utwórz**, aby przesłać żądanie pomocy technicznej.

## <a name="monitor-a-support-ticket"></a>Monitorowanie biletu pomocy technicznej
Po przesłaniu żądania pomocy technicznej zespół pomocy technicznej systemu Azure skontaktuje się z Tobą. Aby sprawdzić stan żądania i szczegółowe informacje, kliknij pozycję **Wszystkie żądania obsługi** na pulpicie nawigacyjnym.

![Sprawdzanie stanu](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Inne zasoby
Możesz również nawiązać połączenie ze społecznością SQL Data Warehouse na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) lub za pomocą [forum Azure SQL Data Warehouse MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
