---
title: Harmonogramy konserwacji platformy Azure (wersja zapoznawcza) | Microsoft Docs
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
ms.openlocfilehash: 3875106e8c6301c95bc8d0fbce6a1c0400d07f78
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278123"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Korzystanie z harmonogramów konserwacji do zarządzania aktualizacjami i konserwacją usług

Harmonogramy konserwacji są teraz dostępne we wszystkich regionach Azure SQL Data Warehouse. Ta funkcja integruje Service Health planowane powiadomienia o konserwacji, Resource Health monitorowanie i usługę planowania konserwacji Azure SQL Data Warehouse.

Planowanie konserwacji służy do wybierania przedziału czasu, gdy jest wygodne do otrzymywania nowych funkcji, uaktualnień i poprawek. Możesz wybrać podstawowe i pomocnicze okno obsługi w ciągu siedmiu dni. Przykładem jest okno podstawowe z soboty od 22:00 do niedziela 01:00 i dodatkowe okno z środę 19:00 do 22:00. Jeśli SQL Data Warehouse nie może wykonać konserwacji w ramach głównego okna obsługi, spróbuje przeprowadzić konserwację ponownie w oknie konserwacji dodatkowej. Konserwacja usługi może wystąpić podczas zarówno podstawowego, jak i pomocniczego systemu Windows. Aby zapewnić szybkie zakończenie wszystkich operacji konserwacji, DW400 (c) i niższe warstwy magazynu danych mogą zakończyć konserwację poza wydzielonym oknem obsługi.

Wszystkie nowo utworzone wystąpienia Azure SQL Data Warehouse będą miały zdefiniowany przez system harmonogram konserwacji stosowany podczas wdrażania. Harmonogram można edytować zaraz po zakończeniu wdrażania.

Każde okno obsługi może być od trzech do ośmiu godzin. W oknie można przeprowadzić konserwację w dowolnym momencie. Po rozpoczęciu konserwacji wszystkie aktywne sesje zostaną anulowane, a transakcje niezatwierdzone zostaną wycofane. Należy oczekiwać wielu krótkich strat podczas łączności, ponieważ usługa wdraża nowy kod w magazynie danych. Użytkownik zostanie powiadomiony natychmiast po zakończeniu konserwacji w magazynie danych

Aby użyć tej funkcji, należy zidentyfikować podstawowe i pomocnicze okno w różnych zakresach dni. Wszystkie operacje konserwacji powinny zakończyć się w ramach zaplanowanych okien obsługi. Żadna konserwacja nie zostanie przeprowadzona poza określonymi oknami obsługi bez wcześniejszego powiadomienia. Jeśli magazyn danych jest wstrzymany podczas zaplanowanej konserwacji, zostanie on zaktualizowany podczas operacji wznawiania.  

## <a name="alerts-and-monitoring"></a>Alerty i monitorowanie

Integracja z Service Health powiadomieniami i monitorowaniem Resource Health kontrola pozwala klientom na uzyskanie informacji o zbliżającej się aktywności. Nowa Automatyzacja wykorzystuje Azure Monitor. Możesz zdecydować, jak chcesz otrzymywać powiadomienia o zbliżającym się zdarzeniu konserwacji. Należy także zdecydować, które automatyczne przepływy mogą pomóc w zarządzaniu przestojami i zminimalizować wpływ operacji na działania.

Powiadomienie z góry o 24 godzinie poprzedza wszystkie zdarzenia konserwacji, z powodu bieżącego wyjątku DW400c i niższych warstw. Aby zminimalizować przestoje wystąpienia, upewnij się, że magazyn danych nie ma długotrwałych transakcji przed wybranym okresem konserwacji.

> [!NOTE]
> W przypadku, gdy wymagane jest wdrożenie aktualizacji krytycznej czasu, zaawansowane czasy powiadomień mogą być znacząco ograniczone.

Jeśli otrzymasz powiadomienie z wyprzedzeniem, że w tym czasie SQL Data Warehouse nie będzie można przeprowadzić konserwacji, otrzymasz powiadomienie o anulowaniu. Następnie konserwacja zostanie wznowiona w następnym zaplanowanym okresie konserwacji.

Wszystkie aktywne zdarzenia konserwacji są wyświetlane w sekcji **konserwacja Service Health-planowana** . Historia Service Health obejmuje pełny zapis przeszłych zdarzeń. Konserwację można monitorować za pomocą pulpitu nawigacyjnego portalu Azure Service Health Check podczas aktywnego zdarzenia.

### <a name="maintenance-schedule-availability"></a>Dostępność harmonogramu konserwacji

Nawet jeśli planowanie konserwacji nie jest dostępne w wybranym regionie, można w dowolnym momencie wyświetlać i edytować harmonogram konserwacji. Gdy planowanie konserwacji stanie się dostępne w Twoim regionie, określony harmonogram zostanie natychmiast uaktywniony w magazynie danych.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej](viewing-maintenance-schedule.md) o wyświetlaniu harmonogramu konserwacji.
- [Dowiedz się więcej](changing-maintenance-schedule.md) na temat zmieniania harmonogramu konserwacji.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) na temat tworzenia i wyświetlania alertów oraz zarządzania nimi przy użyciu Azure monitor.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) o akcjach elementu webhook dla reguł alertów dziennika.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Tworzenie grup akcji i zarządzanie nimi.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health.
