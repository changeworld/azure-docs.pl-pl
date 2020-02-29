---
title: Jak utworzyć bilet pomocy technicznej
description: Jak utworzyć bilet pomocy technicznej w usłudze Azure Synapse Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195703"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Jak utworzyć bilet pomocy technicznej dla usługi Azure Synapse Analytics
Jeśli masz problemy z usługą Azure Synapse Analytics, Utwórz bilet pomocy technicznej, aby zespół pomocy technicznej mógł Ci pomóc.

## <a name="create-a-support-ticket"></a>Tworzenie biletu pomocy technicznej
1. Otwórz [portal Azure](https://portal.azure.com/).
1. Na ekranie głównym kliknij kartę **Pomoc i obsługa techniczna**.
   
    ![Pomoc i obsługa techniczna](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. Zapoznaj się z [planem pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * Pomoc techniczna związana z **rozliczeniami, limitami przydziałów i zarządzaniem subskrypcjami** jest dostępna na wszystkich poziomach pomocy technicznej.
   * Pomoc techniczna dotycząca **usuwania poprawek** jest świadczona za pośrednictwem pomocy technicznej [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)i [Premier](https://azure.microsoft.com/support/plans/premier/) . Naprawa w razie awarii dotyczy problemów napotykanych przez klientów podczas korzystania z platformy Azure, jeśli istnieje uzasadnione podejrzenie, że problem leży po stronie firmy Microsoft.
   * **Wsparcie dla deweloperów** i **usługi doradcze** są dostępne na poziomach pomocy technicznej [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) i [Premium](https://azure.microsoft.com/support/plans/premier/). 
     
     Jeśli masz plan pomocy technicznej Premium, możesz zgłaszać także problemy związane z usługą SQL Data Warehouse na portalu [Microsoft Premier Online](https://premier.microsoft.com/). Zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) , aby dowiedzieć się więcej o różnych planach pomocy technicznej, takich jak zakres, czasy odpowiedzi, Cennik itp.  Często zadawane pytania dotyczące pomocy technicznej platformy Azure można znaleźć w artykule [FAQ pomocy technicznej dla platformy Azure](https://azure.microsoft.com/support/faq/).
1. Na stronie **Pomoc i obsługa techniczna** wybierz pozycję **nowe żądanie obsługi**. Wybierz typ problemu z menu rozwijanego. Następnie Wypełnij informacje na karcie **podstawy** . Wprowadź **Podsumowanie** problemu, a następnie wybierz **typ problemu** z menu i wybierz pozycję Zapisz.

    ![Pomoc i obsługa techniczna](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > Domyślnie każdy serwer SQL (na przykład myserver.database.windows.net) ma **limit przydziału jednostek DTU** wynoszący 45 000. Ten limit przydziału jest po prostu limitem bezpieczeństwa. Możesz zwiększyć limit przydziału, tworząc bilet pomocy technicznej i wybierając pozycję *Limit przydziału* jako typ żądania. Aby obliczyć potrzeby jednostek DTU, pomnóż 7,5 przez łączną potrzebną [jednostek dwu](sql-data-warehouse-overview-what-is.md) . Jeśli na przykład chcesz hostować dwie bazy danych z poziomem celu usługi DW6000 na jednym serwerze SQL, zażądaj limitu przydziału wynoszącego 90 000.  Aktualne użycie jednostek DTU możesz zobaczyć z poziomu bloku serwera SQL w portalu. Limit przydziału jednostek DTU obejmuje zarówno wstrzymane, jak i niewstrzymane bazy danych. 
   > 

1. Możesz zobaczyć rozwiązania pomagające w rozwiązaniu problemu. Jeśli przedstawione rozwiązania nie rozwiązują problemu, wybierz pozycję **Dalej: szczegóły**. Prześlij Szczegóły problemu i informacje kontaktowe. Wybierz pozycję **Dalej: przegląd + tworzenie**
![szczegóły](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. Przejrzyj informacje i wybierz pozycję **Utwórz** w dolnej części formularza, aby przesłać żądanie pomocy technicznej.

## <a name="monitor-a-support-ticket"></a>Monitorowanie biletu pomocy technicznej
Po przesłaniu żądania pomocy technicznej zespół pomocy technicznej systemu Azure skontaktuje się z Tobą. Aby sprawdzić stan żądania i szczegółowe informacje, kliknij pozycję **Wszystkie żądania obsługi** na pulpicie nawigacyjnym.

![Sprawdzanie stanu](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Inne zasoby
Możesz również nawiązać połączenie ze społecznością SQL Data Warehouse na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) lub za pomocą [forum Azure SQL Data Warehouse MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
