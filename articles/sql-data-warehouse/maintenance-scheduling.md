---
title: Harmonogramy konserwacji platformy Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Planowanie konserwacji umożliwia klientom planować wokół zdarzenia niezbędne zaplanowanej konserwacji, korzysta z usługi Azure SQL Data warehouse to dystrybucję nowych funkcji, uaktualnienia i poprawki.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 85e8327d1cac17059b2e91b1ea21becc23002c8e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228388"
---
# <a name="using-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Zarządzanie aktualizacjami usługi i konserwacja za pomocą harmonogramy konserwacji

Azure SQL Data magazynu konserwacji jest teraz w wersji zapoznawczej. Ta nowa funkcja bezproblemowo integrują się planowanej konserwacji powiadomień dotyczących kondycji usługi, Monitor sprawdzanie kondycji zasobu i usługą planowania konserwacji usługi Azure SQL Data Warehouse.

Planowanie konserwacji umożliwia zaplanowanie przedział czasu, gdy jest to wygodne otrzymać nowe funkcje i uaktualnień i poprawki. Klienci będą wybrać podstawowy i pomocniczy konserwacyjne przed upływem 7 dni, czyli sobota 22:00 – Niedziela 01:00 (podstawowy) i środę 19:00 – 22:00 (pomocniczy). Jeśli firma Microsoft nie można wykonać konserwacji podczas okna obsługi podstawowego, firma Microsoft podejmie go ponownie podczas okna obsługi dodatkowej.

Wszystkie nowo utworzone Azure SQL Data Warehouse wystąpienia będą mieć harmonogramu konserwacji zdefiniowaną przez system stosowane podczas wdrażania. Harmonogram może być edytowany, zaraz po zakończeniu wdrożenia.

Każde okno obsługi mogą należeć do zakresu od 3 do 8 godzin, za pomocą 3hrs obecnie najkrótszej dostępną opcją. Konserwacji może nastąpić w dowolnej chwili, w oknie, a jako usługa wdraża nowy kod do magazynu danych należy się spodziewać krótki utraty łączności. 

W trakcie okresu zapoznawczego funkcji prosimy klientów do identyfikowania podstawowe i pomocnicze systemu windows w ramach zakresów dni.   
Wszystkie operacje konserwacji należy wykonać w obrębie okna zaplanowanej konserwacji i konserwacji nie będzie miała miejsce poza oknami obsługi określona, bez wcześniejszego powiadomienia. Jeśli magazyn danych jest wstrzymany podczas zaplanowanej konserwacji, zostaną zaktualizowane podczas operacji wznowienia.  


## <a name="alerts-and-monitoring"></a>Alerty i monitorowanie

Bezproblemowa integracja z powiadomień dotyczących kondycji usługi i kondycję zasobów Sprawdź, czy monitor pozwala klientom na bieżąco o zbliżającym się związanych z konserwacją. Nowe usługi automation korzysta z usługi Azure Monitor i umożliwia klientom określić, jak chcesz otrzymywać powiadomienia o zbliżającym się zdarzenia konserwacji i który zautomatyzowane przepływy powinna być wyzwolona Zarządzanie przestojów i zminimalizowania wpływu na swojej działalności.

Wszystkie zdarzenia konserwacji są poprzedzone powiadomienie 24-godzinnym. Aby zminimalizować przestoje wystąpienia, należy upewnij się, że nie ma żadnych transakcji długotrwałych swój magazyn danych, przed rozpoczęciem konserwacji wybranego okresu. Po uruchomieniu konserwacji wszystkie aktywne sesje zostaną anulowane, transakcji — przydzielone zostanie wycofana i magazynu danych, mogą mieć krótki utraty łączności. Otrzymasz powiadomienie natychmiast, po zakończeniu konserwacji w magazynie danych. 

Jeśli otrzymasz powiadomienie o konserwacji będzie miała miejsce, że nie można wykonać konserwacji, w tym samym czasie, otrzymasz powiadomienie anulowania. Konserwacja następnie zostanie wznowiona podczas kolejnego okresu zaplanowanej konserwacji.
 
Wszystkie zdarzenia w aktywnej konserwacji zostanie wyświetlona w "Kondycja usługi - planowaną konserwację" sekcji. Pełną dokumentację przeszłych zdarzeń zostaną zachowane w ramach historii kondycji usługi. Konserwacja można monitorować za pomocą usługi Azure Service Health sprawdzanie pulpitu nawigacyjnego w portalu podczas aktywne zdarzenie.

### <a name="maintenance-schedule-availability"></a>Dostępność harmonogram konserwacji

Nawet jeśli planowania konserwacji nie jest jeszcze dostępna w Twoim regionie wybrane, można nadal przeglądać i edytować harmonogramu konserwacji w dowolnym momencie. Podczas planowania konserwacji staje się dostępna w Twoim regionie, harmonogram zidentyfikowane natychmiast stanie się aktywne w magazynu danych.


## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej](viewing-maintenance-schedule.md) o wyświetlaniu harmonogram konserwacji 
- [Dowiedz się więcej](changing-maintenance-schedule.md) o zmienianiu harmonogram konserwacji
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) o tworzenie, wyświetlanie i Zarządzanie alertami przy użyciu usługi Azure Monitor
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) Akcje elementu Webhook dla reguł alertów dzienników — informacje
- [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/service-health-overview) dotyczące usługi Azure Service Health







