---
title: Skonfiguruj alerty i powiadomienia przy użyciu Azure Portal | Microsoft Docs
description: Użyj Azure Portal, aby utworzyć alerty SQL Database, które mogą wyzwalać powiadomienia lub automatyzację, gdy zostaną spełnione określone warunki.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 11/02/2018
ms.openlocfilehash: 3103b556098d0b9ccbfca7348ab70c183a69753d
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262230"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Tworzenie alertów dla Azure SQL Database i magazynu danych przy użyciu Azure Portal

## <a name="overview"></a>Przegląd
W tym artykule opisano sposób konfigurowania Azure SQL Database i alertów magazynu danych przy użyciu Azure Portal. Alerty mogą wysyłać wiadomość e-mail lub wywoływać element webhook, gdy pewna Metryka (na przykład rozmiar bazy danych lub użycie procesora) osiągnie wartość progową. W tym artykule przedstawiono również najlepsze rozwiązania dotyczące ustawiania okresów alertów.    

> [!IMPORTANT]
> Ta funkcja nie jest jeszcze dostępna w wystąpieniu zarządzanym. Alternatywnie można użyć programu SQL Agent do wysyłania alertów e-mail dotyczących niektórych metryk w oparciu o [dynamiczne widoki zarządzania](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Możesz otrzymywać alerty na podstawie metryk monitorowania dla usługi lub zdarzeń w usłudze Azure.

* **Wartości metryk** — alert wyzwalany, gdy wartość określonej metryki przekroczy próg przypisany w dowolnym kierunku. Oznacza to, że jest wyzwalane podczas pierwszego spełnienia warunku, a następnie gdy ten warunek nie jest już spełniony.    
* **Zdarzenia dziennika aktywności** — alert może być wyzwalany dla *każdego* zdarzenia lub tylko wtedy, gdy wystąpi pewna liczba zdarzeń.

Można skonfigurować alert, aby wykonać następujące czynności podczas wyzwalania:

* Wysyłanie powiadomień e-mail do administratora usługi i współadministratorów
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail, które określisz.
* Wywoływanie elementu webhook

Można skonfigurować i uzyskać informacje o regułach alertów za pomocą

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfejs wiersza polecenia (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfejs API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Tworzenie reguły alertu w ramach metryki z Azure Portal
1. W [portalu](https://portal.azure.com/)Znajdź zasób, który chcesz monitorować, i wybierz go.
2. W sekcji monitorowanie wybierz pozycję **alerty (klasyczne)** . Tekst i ikona mogą się nieco różnić w zależności od różnych zasobów.  
   
     ![Monitorowanie](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **TYLKO DW SQL**: Kliknij wykres **użycie jednostek dwu** . Wybierz pozycję **Wyświetl klasyczne alerty**

3. Wybierz przycisk **Dodaj alert metryki (klasyczny)** i wypełnij pola.
   
    ![Dodawanie alertu](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Nadaj nazwę** regule alertu i wybierz **Opis**, która również jest wyświetlana w wiadomościach e-mail z powiadomieniami.
5. Wybierz **metrykę** , którą chcesz monitorować, a następnie wybierz **warunek** i wartość **progową** dla metryki. Należy również wybrać **okres** , przez jaki reguła metryki musi być spełniona przed wyzwalaczem alertu. Jeśli na przykład używasz okresu "PT5M", a alert szuka procesora o wartości powyżej 80%, alert zostanie wyzwolony, gdy **Średni** procesor cpu powyżej 80% przez 5 minut. Po wystąpieniu pierwszego wyzwalacza ponownie wyzwalane, gdy średni procesor CPU będzie niższy od 80% ponad 5 minut. Pomiar procesora CPU występuje co 1 minutę. Zapoznaj się z poniższą tabelą dla obsługiwanych okien czasu i typ agregacji używany przez każdy alert — nie wszystkie alerty używają wartości średniej.   
6. Sprawdź **właścicieli poczty e-mail...** , jeśli chcesz, aby administratorzy i współadministratorzy mogli otrzymywać pocztą e-mail po uruchomieniu alertu.
7. Jeśli chcesz, aby dodatkowe wiadomości e-mail otrzymywały powiadomienie po uruchomieniu alertu, Dodaj je w polu **dodatkowe adresy E-mail administratora** . Rozdziel wiele wiadomości e-mail średnikami — *e-mail\@contoso. com\@; email2 contoso.com*
8. Umieść w prawidłowym identyfikatorze URI w polu **elementu webhook** , jeśli chcesz, gdy zostanie wywołany alert.
9. Po zakończeniu kliknij **przycisk OK** , aby utworzyć alert.   

W ciągu kilku minut alert jest aktywny i wyzwalacze opisane wcześniej.

## <a name="managing-your-alerts"></a>Zarządzanie alertami
Po utworzeniu alertu możesz go wybrać i:

* Wyświetl wykres pokazujący próg metryki i rzeczywiste wartości z poprzedniego dnia.
* Edytuj lub Usuń.
* **Wyłącz** lub **Włącz** tę opcję, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień dla tego alertu.


## <a name="sql-database-alert-values"></a>SQL Database wartości alertów

| Typ zasobu | Nazwa metryki | Przyjazna nazwa | Typ agregacji | Okno minimalnego czasu alertu|
| --- | --- | --- | --- | --- |
| Baza danych SQL | cpu_percent | Procent użycia procesora CPU | Average | 5 minut |
| Baza danych SQL | physical_data_read_percent | Operacje we/wy danych (procent) | Average | 5 minut |
| Baza danych SQL | log_write_percent | Operacje we/wy dziennika (procent) | Average | 5 minut |
| Baza danych SQL | dtu_consumption_percent | Procent jednostek DTU | Average | 5 minut |
| Baza danych SQL | magazyn | Łączny rozmiar bazy danych | Maksimum | 30 minut |
| Baza danych SQL | connection_successful | Udane połączenia | Łącznie | 10 minut |
| Baza danych SQL | connection_failed | Połączenia zakończone niepowodzeniem | Łącznie | 10 minut |
| Baza danych SQL | blocked_by_firewall | Zablokowane przez zaporę | Łącznie | 10 minut |
| Baza danych SQL | Stanu | Zakleszczenia | Łącznie | 10 minut |
| Baza danych SQL | storage_percent | Procent użycia rozmiaru bazy danych | Maksimum | 30 minut |
| Baza danych SQL | xtp_storage_percent | Procent magazynu OLTP w pamięci (wersja zapoznawcza) | Average | 5 minut |
| Baza danych SQL | workers_percent | Procesy robocze (procent) | Average | 5 minut |
| Baza danych SQL | sessions_percent | Procent sesji | Average | 5 minut |
| Baza danych SQL | dtu_limit | Limit jednostek DTU | Average | 5 minut |
| Baza danych SQL | dtu_used | Używane jednostki DTU | Average | 5 minut |
||||||
| Pula elastyczna | cpu_percent | Procent użycia procesora CPU | Average | 10 minut |
| Pula elastyczna | physical_data_read_percent | Operacje we/wy danych (procent) | Average | 10 minut |
| Pula elastyczna | log_write_percent | Operacje we/wy dziennika (procent) | Average | 10 minut |
| Pula elastyczna | dtu_consumption_percent | Procent jednostek DTU | Average | 10 minut |
| Pula elastyczna | storage_percent | Procent miejsca do magazynowania | Average | 10 minut |
| Pula elastyczna | workers_percent | Procesy robocze (procent) | Average | 10 minut |
| Pula elastyczna | eDTU_limit | limit liczby jednostek eDTU | Average | 10 minut |
| Pula elastyczna | storage_limit | Limit magazynu | Average | 10 minut |
| Pula elastyczna | eDTU_used | użyta wartość eDTU | Average | 10 minut |
| Pula elastyczna | storage_used | Użyty magazyn | Average | 10 minut |
||||||               
| SQL Data Warehouse | cpu_percent | Procent użycia procesora CPU | Average | 10 minut |
| SQL Data Warehouse | physical_data_read_percent | Operacje we/wy danych (procent) | Average | 10 minut |
| SQL Data Warehouse | connection_successful | Udane połączenia | Łącznie | 10 minut |
| SQL Data Warehouse | connection_failed | Połączenia zakończone niepowodzeniem | Łącznie | 10 minut |
| SQL Data Warehouse | blocked_by_firewall | Zablokowane przez zaporę | Łącznie | 10 minut |
| SQL Data Warehouse | service_level_objective | Warstwa usługi bazy danych | Łącznie | 10 minut |
| SQL Data Warehouse | dwu_limit | limit jednostek dwu | Maksimum | 10 minut |
| SQL Data Warehouse | dwu_consumption_percent | JEDNOSTEK dwu procent | Average | 10 minut |
| SQL Data Warehouse | dwu_used | JEDNOSTEK dwu używane | Average | 10 minut |
||||||


## <a name="next-steps"></a>Następne kroki
* [Zapoznaj się z omówieniem monitorowania platformy Azure](../monitoring-and-diagnostics/monitoring-overview.md) , w tym typy informacji, które można zbierać i monitorować.
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../azure-monitor/platform/alerts-webhooks.md).
* Zapoznaj się [z omówieniem dzienników diagnostycznych](../azure-monitor/platform/resource-logs-overview.md) i zbierz szczegółowe metryki o wysokiej częstotliwości dotyczące usługi.
* Zapoznaj się z [omówieniem kolekcji metryk](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , aby upewnić się, że usługa jest dostępna i odpowiada.
