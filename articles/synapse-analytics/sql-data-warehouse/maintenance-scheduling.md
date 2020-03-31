---
title: Harmonogramy konserwacji puli sql synapse
description: Planowanie konserwacji umożliwia klientom planowanie o konieczności zaplanowanych zdarzeń konserwacji, których usługa Azure Synapse Analytics używa do wdrażania nowych funkcji, uaktualnień i poprawek.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: f193580ca03d4b1805f3c044658a34f468f3f44f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346558"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Zarządzanie aktualizacjami i konserwacją usług umożliwia zarządzanie aktualizacjami i konserwacją serwisów

Funkcja harmonogramu konserwacji integruje powiadomienia o planowanej konserwacji kondycji usługi, monitor sprawdzania kondycji zasobów i usługę planowania konserwacji dla puli usług Synapse SQL (hurtownia danych) w ramach usługi Azure Synapse Analytics. 

Należy użyć planowania konserwacji, aby wybrać przedział czasu, gdy jest to wygodne do odbierania nowych funkcji, uaktualnień i poprawek. Należy wybrać podstawowe i dodatkowe okno konserwacji w okresie siedmiu dni, każde okno musi znajdować się w oddzielnych zakresach dziennych.

Na przykład można zaplanować okno podstawowe w sobotę 22:00 do niedzieli 01:00, a następnie zaplanować dodatkowe okno w środę 19:00 do 22:00. Jeśli nie można wykonać konserwacji podczas podstawowego okna konserwacji, spróbuje ponownie przeprowadzić konserwację podczas dodatkowego okna konserwacji. Konserwacja usługi może czasami wystąpić zarówno podczas okien podstawowych, jak i pomocniczych. Aby zapewnić szybkie zakończenie wszystkich operacji konserwacyjnych, dw400c i niższe warstwy magazynu danych mogą zakończyć konserwację poza wyznaczonym oknem konserwacji.

Wszystkie nowo utworzone wystąpienia magazynu danych będą miały zdefiniowany przez system harmonogram konserwacji stosowany podczas wdrażania. Harmonogram można edytować natychmiast po zakończeniu wdrażania.

Chociaż okno konserwacji może wynosić od trzech do ośmiu godzin, nie oznacza to, że magazyn danych będzie w trybie offline na czas trwania. Konserwacja może wystąpić w dowolnym momencie w tym oknie i należy się spodziewać pojedynczego rozłączenia w tym okresie trwającym ~5 -6 minut, gdy usługa wdraża nowy kod do magazynu danych. DW400c i niższe mogą wystąpić wiele krótkich strat w łączności w różnych okresach podczas okna konserwacji. Po uruchomieniu konserwacji wszystkie aktywne sesje zostaną anulowane, a nieuprawione transakcje zostaną wycofane. Aby zminimalizować przestoje wystąpienia, upewnij się, że magazyn danych nie ma długotrwałych transakcji przed wybranym okresem konserwacji.

Wszystkie operacje konserwacji należy zakończyć w ramach określonych okien konserwacji, chyba że jesteśmy zobowiązani do wdrożenia aktualizacji zależne od czasu. Jeśli magazyn danych zostanie wstrzymany podczas zaplanowanej konserwacji, zostanie zaktualizowany podczas operacji wznawiania. Zostaniesz powiadomiony natychmiast po zakończeniu konserwacji magazynu danych.

## <a name="alerts-and-monitoring"></a>Alerty i monitorowanie

Integracja z powiadomieniami kondycji usługi i monitorem kontroli kondycji zasobów umożliwia klientom śledzenie zbliżających się działań konserwacyjnych. Ta automatyzacja korzysta z usługi Azure Monitor. Możesz zdecydować, w jaki sposób chcesz otrzymywać powiadomienia o zbliżających się zdarzeniach konserwacji. Można również wybrać, które zautomatyzowane przepływy pomogą Ci zarządzać przestojami i zminimalizować wpływ na wydajność.

Powiadomienie z 24-godzinnym wyprzedzeniem poprzedza wszystkie zdarzenia konserwacji, które nie są dla DWC400c i niższych warstw.

> [!NOTE]
> W przypadku, gdy jesteśmy zobowiązani do wdrożenia aktualizacji krytycznej czas, zaawansowane czasy powiadomień mogą być znacznie skrócone.

Jeśli otrzymasz powiadomienie z wyprzedzeniem, że konserwacja będzie mieć miejsce, ale konserwacja nie może być wykonana w okresie w powiadomieniu, otrzymasz powiadomienie o anulowaniu. Konserwacja zostanie wznowiona w następnym zaplanowanym okresie konserwacji.

Wszystkie aktywne zdarzenia konserwacji są wyświetlane w **sekcji Kondycja usługi — planowana konserwacja.** Historia kondycji usługi zawiera pełny zapis przeszłych zdarzeń. Konserwację można monitorować za pośrednictwem pulpitu nawigacyjnego portalu sprawdzania kondycji usługi Azure podczas aktywnego zdarzenia.

### <a name="maintenance-schedule-availability"></a>Dostępność harmonogramu konserwacji

Nawet jeśli planowanie konserwacji nie jest dostępne w wybranym regionie, można w dowolnym momencie wyświetlić i edytować harmonogram konserwacji. Gdy planowanie konserwacji staje się dostępne w twoim regionie, zidentyfikowany harmonogram natychmiast stanie się aktywny w puli synapse SQL.

## <a name="view-a-maintenance-schedule"></a>Wyświetlanie harmonogramu konserwacji 

Domyślnie wszystkie nowo utworzone wystąpienia magazynu danych mają osiem godzin podstawowe i pomocnicze okno konserwacji stosowane podczas wdrażania. Jak wskazano powyżej, można zmienić okna, jak tylko wdrożenie zostanie zakończone. Żadne czynności konserwacyjne nie będą przeprowadzane poza oknami obsługi bez wcześniejszego powiadomienia.

Aby wyświetlić harmonogram konserwacji zastosowany do puli sql synapse, wykonaj następujące kroki:

1.    Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2.    Wybierz pulę Sql Synapse, którą chcesz wyświetlić. 
3.    Wybrana pula języka SQL Synapse zostanie otwarta w bloku przeglądu. Harmonogram konserwacji zastosowany do magazynu danych jest wyświetlany poniżej **harmonogramu konserwacji**.

![Ostrze przeglądowe](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Zmienianie harmonogramu konserwacji 

Harmonogram konserwacji można zaktualizować lub zmienić w dowolnym momencie. Jeśli wybrane wystąpienie przechodzi aktywny cykl konserwacji, ustawienia zostaną zapisane. Będą one aktywne podczas następnego zidentyfikowanego okresu konserwacji. [Dowiedz się więcej](../../service-health/resource-health-overview.md) o monitorowaniu magazynu danych podczas aktywnego zdarzenia konserwacji. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identyfikowanie okien podstawowych i pomocniczych

Okna podstawowe i pomocnicze muszą mieć oddzielne zakresy dni. Przykładem jest podstawowe okno wtorek na czwartek i pomocnicze okna sobota-niedziela.

Aby zmienić harmonogram konserwacji puli sql synapse, wykonaj następujące kroki:
1.    Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2.    Wybierz pulę Sql Synapse, którą chcesz zaktualizować. Strona zostanie otwarta na bloku przeglądu. 
3.    Otwórz stronę ustawień harmonogramu konserwacji, wybierając **łącze podsumowania harmonogram konserwacji** w bloku przeglądu. Możesz też wybrać opcję **Harmonogram konserwacji** w menu zasobów po lewej stronie.  

    ![Opcje tarczy przeglądowej](./media/maintenance-scheduling/maintenance-change-option.png)

4. Zidentyfikuj preferowany zakres dni dla podstawowego okna konserwacji, korzystając z opcji u góry strony. To zaznaczenie określa, czy okno podstawowe będzie miało miejsce w dzień powszedni, czy w weekend. Wybór zaktualizuje wartości rozwijane. Podczas podglądu niektóre regiony mogą jeszcze nie obsługiwać pełnego zestawu dostępnych opcji **dnia.**

   ![Ostrze ustawień konserwacji](./media/maintenance-scheduling/maintenance-settings-page.png)

5. Wybierz preferowane podstawowe i dodatkowe okna konserwacji, korzystając z pól listy rozwijanej:
   - **Dzień:** Preferowany dzień do wykonania konserwacji podczas wybranego okna.
   - **Godzina rozpoczęcia:** Preferowany czas rozpoczęcia okna konserwacji.
   - **Przedział czasu:** Preferowany czas trwania przedziału czasu.

   Obszar **podsumowania harmonogramu** u dołu bloku jest aktualizowany na podstawie wybranych wartości. 
  
6. Wybierz **pozycję Zapisz**. Zostanie wyświetlony komunikat z potwierdzeniem, że nowy harmonogram jest teraz aktywny. 

   Jeśli zapisujesz harmonogram w regionie, który nie obsługuje planowania konserwacji, zostanie wyświetlony następujący komunikat. Ustawienia zostaną zapisane i uaktywnone, gdy funkcja stanie się dostępna w wybranym regionie.    

   ![Komunikat o dostępności regionu](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej](../../azure-monitor/platform/alerts-metric.md) o tworzeniu, wyświetlaniu i zarządzaniu alertami przy użyciu usługi Azure Monitor.
- [Dowiedz się więcej](../..//azure-monitor/platform/alerts-log-webhook.md) o akcjach elementu webhook dla reguł alertów dziennika.
- [Dowiedz się więcej](../..//azure-monitor/platform/action-groups.md) Tworzenie grup akcji i zarządzanie nimi.
- [Dowiedz się więcej](../../service-health/service-health-overview.md) o usłudze Azure Service Health.
