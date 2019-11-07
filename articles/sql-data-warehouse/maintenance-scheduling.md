---
title: Harmonogramy konserwacji platformy Azure
description: Planowanie konserwacji umożliwia klientom zaplanowanie niezbędnych zaplanowanych zdarzeń konserwacji, których usługa Azure SQL Data Warehouse używa do tworzenia nowych funkcji, uaktualnień i poprawek.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 07/16/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 91b202f8a5df841fa3d6aa1f0903999b395f8137
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686068"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Korzystanie z harmonogramów konserwacji do zarządzania aktualizacjami i konserwacją usług

Harmonogramy konserwacji są teraz dostępne we wszystkich regionach Azure SQL Data Warehouse. Funkcja harmonogramu konserwacji integruje Service Health planowane powiadomienia o konserwacji, Resource Health monitorowania i Azure SQL Data Warehouse usłudze planowania konserwacji.

Planowanie konserwacji służy do wybierania przedziału czasu, gdy jest wygodne do otrzymywania nowych funkcji, uaktualnień i poprawek. Możesz wybrać podstawowe i pomocnicze okno obsługi w ciągu siedmiu dni. Aby użyć tej funkcji, należy zidentyfikować podstawowe i pomocnicze okno w różnych zakresach dni.

Na przykład można zaplanować podstawowe okno soboty Sobota 22:00 do niedzieli 01:00, a następnie zaplanować okno pomocnicze w środę 19:00 do 22:00. Jeśli SQL Data Warehouse nie może wykonać konserwacji w ramach głównego okna obsługi, spróbuje przeprowadzić konserwację ponownie w oknie konserwacji dodatkowej. Konserwacja usługi może wystąpić zarówno w podstawowym, jak i pomocniczym systemie Windows. Aby zapewnić szybkie zakończenie wszystkich operacji konserwacji, DW400 (c) i niższe warstwy magazynu danych mogą zakończyć konserwację poza wydzielonym oknem obsługi.

Wszystkie nowo utworzone wystąpienia Azure SQL Data Warehouse będą miały zdefiniowany przez system harmonogram konserwacji stosowany podczas wdrażania. Harmonogram można edytować zaraz po zakończeniu wdrażania.

Każde okno obsługi może należeć do przedziału od trzech do ośmiu godzin. W oknie można przeprowadzić konserwację w dowolnym momencie. Po rozpoczęciu konserwacji wszystkie aktywne sesje zostaną anulowane, a transakcje niezatwierdzone zostaną wycofane. Należy oczekiwać wielu krótkich strat podczas łączności, ponieważ usługa wdraża nowy kod w magazynie danych. Po zakończeniu konserwacji magazynu danych zostanie wyświetlone powiadomienie.

 Wszystkie operacje konserwacji powinny zakończyć się w ramach zaplanowanych okien obsługi. Żadne czynności konserwacyjne nie będą przeprowadzane poza oknami obsługi bez wcześniejszego powiadomienia. Jeśli magazyn danych jest wstrzymany podczas zaplanowanej konserwacji, zostanie on zaktualizowany podczas operacji wznawiania. 

## <a name="alerts-and-monitoring"></a>Alerty i monitorowanie

Integracja z Service Health powiadomieniami i monitorowaniem Resource Health kontrola pozwala klientom na uzyskanie informacji o zbliżającej się aktywności. Nowa Automatyzacja wykorzystuje Azure Monitor. Możesz zdecydować, jak chcesz otrzymywać powiadomienia o zbliżającym się zdarzeniu konserwacji. Ponadto możesz wybrać, które automatyczne przepływy będą pomocne w zarządzaniu przestojami i minimalizacji wpływu operacyjnego.

Powiadomienie z góry 24-godzinne poprzedza wszystkie zdarzenia konserwacji, które nie są używane w przypadku DWC400c i niższych warstw. Aby zminimalizować przestoje wystąpienia, upewnij się, że magazyn danych nie ma długotrwałych transakcji przed wybranym okresem konserwacji.

> [!NOTE]
> W przypadku, gdy wymagane jest wdrożenie aktualizacji krytycznej czasu, zaawansowane czasy powiadomień mogą być znacząco ograniczone.

Jeśli otrzymasz powiadomienie z wyprzedzeniem, że w tym czasie SQL Data Warehouse nie będzie można przeprowadzić konserwacji, otrzymasz powiadomienie o anulowaniu. Następnie konserwacja zostanie wznowiona w następnym zaplanowanym okresie konserwacji.

Wszystkie aktywne zdarzenia konserwacji są wyświetlane w sekcji **konserwacja Service Health-planowana** . Historia Service Health obejmuje pełny zapis przeszłych zdarzeń. Konserwację można monitorować za pomocą pulpitu nawigacyjnego portalu Azure Service Health Check podczas aktywnego zdarzenia.

### <a name="maintenance-schedule-availability"></a>Dostępność harmonogramu konserwacji

Nawet jeśli planowanie konserwacji nie jest dostępne w wybranym regionie, można w dowolnym momencie wyświetlać i edytować harmonogram konserwacji. Gdy planowanie konserwacji stanie się dostępne w Twoim regionie, określony harmonogram zostanie natychmiast uaktywniony w magazynie danych.

## <a name="view-a-maintenance-schedule"></a>Wyświetlanie harmonogramu konserwacji 

### <a name="portal"></a>Portal

Domyślnie podczas wdrażania wszystkich nowo utworzonych wystąpień usługi Azure SQL Data Warehouse są stosowane ośmiogodzinne podstawowe i dodatkowe okna obsługi. Jak wspomniano powyżej, można zmienić system Windows po zakończeniu wdrażania. Żadne czynności konserwacyjne nie będą przeprowadzane poza oknami obsługi bez wcześniejszego powiadomienia.

Aby wyświetlić harmonogram konserwacji zastosowany do magazynu danych, wykonaj następujące czynności:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  Wybierz magazyn danych, który chcesz wyświetlić. 
3.  Wybrany magazyn danych zostanie otwarty w bloku przegląd. Harmonogram konserwacji, który jest stosowany do magazynu danych, zostanie wyświetlony poniżej **harmonogramu konserwacji**.

![Blok przegląd](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Zmiana harmonogramu konserwacji 

### <a name="portal"></a>Portal
Harmonogram konserwacji można aktualizować lub zmieniać w dowolnym momencie. Jeśli wybrane wystąpienie przechodzi przez aktywny cykl konserwacji, ustawienia zostaną zapisane. Staną się one aktywne w następnym określonym okresie konserwacji. [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/resource-health-overview) o monitorowaniu magazynu danych podczas aktywnego zdarzenia konserwacji. 

### <a name="identifying-the-primary-and-secondary-windows"></a>Identyfikowanie podstawowych i pomocniczych okien

Podstawowe i pomocnicze okna muszą mieć oddzielne zakresy dni. Przykładem jest główne okno wtorek – czwartek i dodatkowe okno soboty — niedziela.

Aby zmienić harmonogram konserwacji dla magazynu danych, wykonaj następujące czynności:
1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  Wybierz magazyn danych, który chcesz zaktualizować. Strona zostanie otwarta w bloku przegląd. 
3.  Otwórz stronę ustawienia harmonogramu konserwacji, wybierając łącze **Podsumowanie harmonogramu konserwacji (wersja zapoznawcza)** w bloku przegląd. Lub wybierz opcję **harmonogram konserwacji** w menu zasobów po lewej stronie.  

    ![Opcje bloku przegląd](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Określ preferowany zakres dni dla głównego okna obsługi przy użyciu opcji w górnej części strony. Wybór ten określa, czy okno podstawowe będzie miało miejsce w dniu tygodnia, czy w weekendy. Wybór spowoduje zaktualizowanie wartości listy rozwijanej. W trakcie okresu zapoznawczego niektóre regiony mogą jeszcze nie obsługiwać pełny zestaw dostępnych opcji **dnia** .

   ![Blok ustawień konserwacji](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Wybierz preferowane główne i pomocnicze okna obsługi przy użyciu pól listy rozwijanej:
   - **Dzień**: preferowany dzień przeprowadzenia konserwacji w wybranym oknie.
   - **Godzina rozpoczęcia**: preferowany czas rozpoczęcia okna obsługi.
   - **Przedział czasu**: preferowany czas trwania przedziału czasu.

   Obszar **Podsumowanie harmonogramu** w dolnej części bloku zostanie zaktualizowany na podstawie wybranych wartości. 
  
6. Wybierz pozycję **Zapisz**. Zostanie wyświetlony komunikat z potwierdzeniem, że nowy harmonogram jest teraz aktywny. 

   W przypadku zapisywania harmonogramu w regionie, który nie obsługuje planowania konserwacji, zostanie wyświetlony następujący komunikat. Twoje ustawienia są zapisywane i stają się aktywne, gdy funkcja stanie się dostępna w wybranym regionie.    

   ![Komunikat o dostępności regionu](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) na temat tworzenia i wyświetlania alertów oraz zarządzania nimi przy użyciu Azure monitor.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) o akcjach elementu webhook dla reguł alertów dziennika.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Tworzenie grup akcji i zarządzanie nimi.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health.