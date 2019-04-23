---
title: Harmonogramy konserwacji platformy Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Planowanie konserwacji umożliwia klientom w planowaniu wokół zdarzenia niezbędne zaplanowanej konserwacji, które korzysta z usługi Azure SQL Data Warehouse to dystrybucję nowych funkcji, uaktualnienia i poprawki.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/03/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4bc1867f0dd18a101b2e764970de4e1904b960e0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790862"
---
# <a name="change-a-maintenance-schedule"></a>Zmień harmonogram konserwacji 

## <a name="portal"></a>Portal
Harmonogram konserwacji można zaktualizować lub zmienić w dowolnym momencie. Jeśli wybrane wystąpienie przechodzi w danym cyklu konserwacji aktywne, ustawienia zostaną zapisane. Zapoznasz się aktywne w okresie konserwacji zidentyfikowanych dalej. [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/resource-health-overview) dotyczące monitorowania usługi data warehouse w czasie wykonywania zdarzeń związanych z konserwacją active. 

Aby użyć harmonogramy konserwacji, musisz wybrać dwa okna obsługi w okresie siedmiu dni. Każde okno obsługi może być trzech do ośmiu godzin. Konserwacji może nastąpić w dowolnej chwili, w ramach okna obsługi, ale nie zostanie wykonana poza okna czasowe bez wcześniejszego powiadomienia. Jako usługa wdraża nowy kod z magazynem danych, będzie również występować krótki utraty łączności. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identyfikowanie podstawowe i pomocnicze systemu windows

Podstawowe i pomocnicze systemu windows musi mieć zakresy oddzielnych dnia. Przykładem jest podstawowy okna wtorek — czwartek i pomocniczy okna sobota — niedziela.

Aby zmienić harmonogram konserwacji magazynu danych, wykonaj następujące czynności:
1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  Wybierz magazyn danych, który chcesz zaktualizować. W bloku przeglądu zostanie otwarta strona. 
3.  Otwórz stronę ustawienia harmonogramu konserwacji, wybierając **harmonogram konserwacji (wersja zapoznawcza) podsumowanie** łącze w bloku przeglądu. Lub wybierz **harmonogram konserwacji** opcję w menu po lewej stronie zasobów.  

    ![Opcje bloku — omówienie](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Określ zakres preferowany dnia okna konserwacji głównej za pomocą opcji w górnej części strony. Ten wybór określa, jeśli podstawowy okno będzie pojawiają się na dzień powszedni lub w weekend. Wybór spowoduje zaktualizowanie wartości listy rozwijanej. W trakcie okresu zapoznawczego, w niektórych regionach mogą jeszcze obsługuje pełny zestaw dostępnych **dzień** opcje.

   ![Blok ustawień konserwacji](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Wybierz preferowaną konserwacji podstawowe i pomocnicze systemu windows za pomocą pola listy rozwijanej:
   - **Dzień**: Preferowany dnia do przeprowadzenia konserwacji podczas wybranego okna.
   - **Czas rozpoczęcia**: Godzina rozpoczęcia preferowanych dla okna obsługi.
   - **Przedział czasu**: Preferowany czas trwania okna czasu.

   **Podsumowanie harmonogramu** u dołu bloku zaktualizowaniu, na podstawie wartości, które zostały wybrane. 
  
6. Wybierz pozycję **Zapisz**. Pojawia się komunikat potwierdzający nowego harmonogramu jest teraz aktywna. 

   W przypadku zapisania harmonogramu w regionie, który nie obsługuje planowanie konserwacji, zostanie wyświetlony następujący komunikat. Ustawienia są zapisywane i stają się aktywne po funkcji staje się dostępna w Twoim regionie wybrane.    

   ![Komunikat dotyczący dostępność w poszczególnych regionach](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Kolejne kroki
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) o akcje elementu webhook dla reguł alertów dzienników.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/service-health-overview) dotyczące usługi Azure Service Health.


