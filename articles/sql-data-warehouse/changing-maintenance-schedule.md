---
title: Harmonogramy konserwacji platformy Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Planowanie konserwacji umożliwia klientom planować wokół zdarzenia niezbędne zaplanowanej konserwacji, korzysta z usługi Azure SQL Data warehouse to dystrybucję nowych funkcji, uaktualnienia i poprawki.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/07/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a6eedc0bac7aab69a9138f4f63d0d9d802e74dfc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228377"
---
# <a name="change-a-maintenance-schedule"></a>Zmień harmonogram konserwacji 

## <a name="portal"></a>Portal
Harmonogram konserwacji można zaktualizować lub zmienić w dowolnym momencie. Jednak jeśli wybranym wystąpieniu jest obecnie pośrednictwa cyklu konserwacji aktywne ustawienia będą zapisywane i stają się tylko aktywne w okresie konserwacji zidentyfikowanych dalej. [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/resource-health-overview) dotyczące monitorowania usługi data warehouse w czasie wykonywania zdarzeń związanych z konserwacją active. 

W wersji zapoznawczej firma Microsoft będzie zadać można wybrać dwa okna obsługi w okresie 7 dni. Każde okno obsługi mogą należeć do zakresu od 3 do 8 godzin, z 3 godz. obecnie trwa najkrótszej dostępną opcją. Konserwacji może nastąpić w dowolnej chwili, w ramach okna obsługi określonych, ale nie odbywa się poza tymi zidentyfikowane okna czasowe uprzednim powiadomieniu, będą również środowisko być krótki utraty łączności, ponieważ usługa wdraża nowy kod z danymi Magazyn. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identyfikowanie podstawowe i pomocnicze systemu windows

Podstawowe i pomocnicze systemu windows musi być identyfikowany zakresów dni (to znaczy, okna głównej (wtorek — czwartek), okno pomocnicze (sobota — niedziela)

Wykonaj poniższe kroki, aby zmienić harmonogram konserwacji, która została zastosowana do usługi data warehouse w portalu.
1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  Wybierz magazyn danych, który chcesz zaktualizować. Strona zostanie otwarta w bloku przeglądu. 
3.  Klikając konserwacji harmonogram (wersja zapoznawcza) łącze do podsumowania w bloku przeglądu lub przy użyciu opcji harmonogramu konserwacji, w Menu po lewej stronie zasobów można uzyskać dostępu do strony Ustawienia harmonogramu konserwacji.  

    ![Opcje bloku — omówienie](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Zakres preferowany dnia można zidentyfikować dla okna głównej konserwacji za pomocą przycisków radiowych w górnej części strony. Ten wybór określa, jeśli podstawowy okno będzie pojawiają się na dzień powszedni lub w weekend. Wybór zaktualizuje o wartości list rozwijanych poniżej. W trakcie okresu zapoznawczego w niektórych regionach mogą obsługuje jeszcze pełny zestaw dostępnych opcji dnia. Te wartości zostanie zaktualizowana w najbliższych miesiącach.

   ![Blok ustawień konserwacji](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Wybierz preferowaną podstawowym i pomocniczym konserwacji systemu windows przy użyciu dzień, godzinę rozpoczęcia i czas okna listy rozwijane poniżej. Harmonogram, który zaktualizuje podsumowanie w dolnej części bloku na podstawie wartości list rozwijanych wybrane.

#### <a name="dropdown-options"></a>Lista rozwijana opcji
- Dzień: Preferowany dnia do przeprowadzenia konserwacji podczas wybrane okno.
- Czas rozpoczęcia: preferowany godzina rozpoczęcia okna konserwacji.
- Przedział czasu: Preferowany czas trwania okna czasu.

  Po wybraniu preferowanych konserwacji systemu windows, kliknij przycisk Zapisz. Zostanie wyświetlony komunikat potwierdzenia potwierdzeniu, że nowego harmonogramu nie jest aktywny. W przypadku zapisywania harmonogramu w regionie, który nie obsługuje jeszcze planowania konserwacji, zostanie wyświetlony następujący komunikat. Ustawienia zostaną zapisane i stają się aktywne po funkcji staje się dostępna w Twoim regionie wybrane.    

    ![Nie jest aktywny w regionie wyskakujące](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Kolejne kroki
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) o akcje elementu Webhook dla reguł alertów dzienników.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/service-health-overview) dotyczące usługi Azure Service Health


