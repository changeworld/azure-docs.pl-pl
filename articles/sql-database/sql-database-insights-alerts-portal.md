---
title: Konfigurowanie alertów i powiadomień za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal, aby utworzyć alerty bazy danych SQL, które mogą wyzwalać automatyzację lub powiadomienia po spełnieniu warunków, które określisz.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 93337e39a117c1f8d38f24dc416ff8ae95513a34
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036059"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Tworzenie alertów dla usługi Azure SQL Database i Data Warehouse przy użyciu witryny Azure portal

## <a name="overview"></a>Omówienie
W tym artykule pokazano, jak skonfigurować alerty usługi Azure SQL Database i Data Warehouse przy użyciu witryny Azure portal. Alerty można wysyłać wiadomości e-mail lub wywołać webhook, gdy niektóre metryki (na przykład rozmiar bazy danych lub użycie procesora CPU) osiąga wartość progową. Ten artykuł zawiera również najlepsze rozwiązania dotyczące ustawienie alertów okresów.    

> [!IMPORTANT]
> Ta funkcja nie jest jeszcze dostępny w wystąpieniu zarządzanym. Alternatywnie, można użyć agenta programu SQL, wysyłać alerty e-mail dotyczące niektóre metryki na podstawie [dynamicznych widoków zarządzania](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Otrzymasz alert na podstawie metryk monitorowania lub zdarzenia na usługi platformy Azure.

* **Wartości metryk** — uruchamia alert, gdy wartość metryki określonego ostrzeżenia przekroczy próg przypisać w dowolnym kierunku. Oznacza to, wyzwala zarówno kiedy warunek jest spełniony, najpierw i następnie później podczas warunku nie jest już trwa spełniany.    
* **Zdarzenia dziennika aktywności** — alert może być wyzwalana przy *co* zdarzenia lub tylko wtedy, gdy występują pewną liczbę zdarzeń.

Możesz skonfigurować alert, aby po jego wyzwoleniu, wykonaj następujące czynności:

* wysyłanie powiadomień e-mail do administratora usługi i współadministratorzy
* Wyślij wiadomość e-mail do dodatkowe adresy e-mail, które określisz.
* Wywołaj element webhook

Można skonfigurować i uzyskać informacje na temat przy użyciu reguł alertów

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [Program PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfejs wiersza polecenia (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfejs API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Tworzenie reguły alertu na metryki w witrynie Azure portal
1. W [portal](https://portal.azure.com/)Znajdź zasób interesuje Cię monitorowania i wybierz ją.
2. Wybierz **alerty (klasyczne)** w sekcji monitorowanie. Tekst i ikona może się nieco różnić dla różnych zasobów.  
   
     ![Monitorowanie](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **TYLKO USŁUGI SQL DW**: Kliknij przycisk **użycie jednostki DWU** wykresu. Wybierz **wyświetlanie alertów klasycznych**

3. Wybierz **Dodaj alert dotyczący metryki (wersja klasyczna)** przycisk, a następnie wypełnij pola.
   
    ![Dodaj alert](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Nazwa** alert reguły, a następnie wybierz **opis**, który pokazuje także w wiadomości e-mail z powiadomieniem.
5. Wybierz **metryki** chcesz monitorować, a następnie wybierz **warunek** i **próg** wartość metryki. Również wybrać **okres** czasu, przez jaki reguła metryki muszą być spełnione przed wyzwala alert. Na przykład jeśli używasz okres "PT5M", a alert szuka procesora CPU przekracza 80%, alert wyzwala kiedy **średni** został procesora CPU przekracza 80% przez 5 minut. Gdy wystąpi to pierwszy wyzwalacz, ponownie uaktywnia to, gdy średnie użycie procesora CPU wynosi poniżej 80% przez 5 minut. Pomiar procesora CPU występuje co minutę. Można znaleźć w poniższej tabeli opisano obsługiwane czas systemu windows, a następnie wpisz agregacji, że każdy alert nie używa wszystkich alertów należy użyć wartości średniej.   
6. Sprawdź **wiadomości E-mail właścicielom...**  chcącym administratorów i współadministratorów, aby otrzymywać pocztą e-mail powiadomienia, gdy zostanie wyzwolony alert.
7. Dodatkowe wiadomości e-mail, aby otrzymać powiadomienie, gdy zostanie wyzwolony alert, dodać je w **email(s) dodatkowego administratora** pola. Wiele wiadomości e-mail należy rozdzielić średnikami - *e-mail\@contoso.com;email2\@contoso.com*
8. Umieść w prawidłowym identyfikatorem URI w **elementu Webhook** jeśli ma ona wywoływana, gdy zostanie wyzwolony alert.
9. Wybierz **OK** po zakończeniu tworzenia alertu.   

W ciągu kilku minut ten alert jest aktywny i wyzwala w sposób opisany wcześniej.

## <a name="managing-your-alerts"></a>Zarządzanie alertami
Po utworzeniu alertu możesz wybrać go oraz:

* Wyświetl wykres przedstawiający próg metryki i rzeczywiste wartości z poprzedniego dnia.
* Edytuj lub usuń go.
* **Wyłącz** lub **Włącz** jej, jeśli chcesz tymczasowo zatrzymać lub wznowić odbieranie powiadomień dla tego alertu.


## <a name="sql-database-alert-values"></a>Wartości alertu bazy danych SQL

| Typ zasobu | Nazwa metryki | Przyjazna nazwa | Typ agregacji | Minimalny czas alertu okna|
| --- | --- | --- | --- | --- |
| Baza danych SQL | cpu_percent | Procent użycia procesora CPU | Średnia | 5 minut |
| Baza danych SQL | physical_data_read_percent | Procent użycia operacji we/wy na danych | Średnia | 5 minut |
| Baza danych SQL | log_write_percent | We/wy dziennika (procent) | Średnia | 5 minut |
| Baza danych SQL | dtu_consumption_percent | Procent użycia jednostek DTU | Średnia | 5 minut |
| Baza danych SQL | magazyn | Łączny rozmiar bazy danych | Maksimum | 30 minut |
| Baza danych SQL | connection_successful | Pomyślnie nawiązane połączenia | Łącznie | 10 minut |
| Baza danych SQL | connection_failed | Połączenia zakończone niepowodzeniem | Łącznie | 10 minut |
| Baza danych SQL | blocked_by_firewall | Blokowane przez zaporę | Łącznie | 10 minut |
| Baza danych SQL | Zakleszczenia | Zakleszczenia | Łącznie | 10 minut |
| Baza danych SQL | storage_percent | Procent użycia rozmiaru bazy danych | Maksimum | 30 minut |
| Baza danych SQL | xtp_storage_percent | Percent(Preview) magazynu OLTP w pamięci | Średnia | 5 minut |
| Baza danych SQL | workers_percent | Procent procesów roboczych | Średnia | 5 minut |
| Baza danych SQL | sessions_percent | Procent sesji | Średnia | 5 minut |
| Baza danych SQL | dtu_limit | DTU limit | Średnia | 5 minut |
| Baza danych SQL | dtu_used | Używane jednostki DTU | Średnia | 5 minut |
||||||
| Pula elastyczna | cpu_percent | Procent użycia procesora CPU | Średnia | 10 minut |
| Pula elastyczna | physical_data_read_percent | Procent użycia operacji we/wy na danych | Średnia | 10 minut |
| Pula elastyczna | log_write_percent | We/wy dziennika (procent) | Średnia | 10 minut |
| Pula elastyczna | dtu_consumption_percent | Procent użycia jednostek DTU | Średnia | 10 minut |
| Pula elastyczna | storage_percent | Procent użycia magazynu | Średnia | 10 minut |
| Pula elastyczna | workers_percent | Procent procesów roboczych | Średnia | 10 minut |
| Pula elastyczna | eDTU_limit | eDTU limit | Średnia | 10 minut |
| Pula elastyczna | storage_limit | Limit magazynu | Średnia | 10 minut |
| Pula elastyczna | eDTU_used | używane jednostki eDTU | Średnia | 10 minut |
| Pula elastyczna | storage_used | Użyty magazyn | Średnia | 10 minut |
||||||               
| magazyn danych SQL | cpu_percent | Procent użycia procesora CPU | Średnia | 10 minut |
| magazyn danych SQL | physical_data_read_percent | Procent użycia operacji we/wy na danych | Średnia | 10 minut |
| magazyn danych SQL | connection_successful | Pomyślnie nawiązane połączenia | Łącznie | 10 minut |
| magazyn danych SQL | connection_failed | Połączenia zakończone niepowodzeniem | Łącznie | 10 minut |
| magazyn danych SQL | blocked_by_firewall | Blokowane przez zaporę | Łącznie | 10 minut |
| magazyn danych SQL | service_level_objective | Warstwy usługi bazy danych | Łącznie | 10 minut |
| magazyn danych SQL | dwu_limit | limit jednostek dwu | Maksimum | 10 minut |
| magazyn danych SQL | dwu_consumption_percent | Procent jednostek DWU | Średnia | 10 minut |
| magazyn danych SQL | dwu_used | Używane jednostki DWU | Średnia | 10 minut |
||||||


## <a name="next-steps"></a>Kolejne kroki
* [Zapoznaj się z omówieniem usługi Azure monitoring](../monitoring-and-diagnostics/monitoring-overview.md) w tym typy informacji, można zbierać i monitorowania.
* Dowiedz się więcej o [konfigurowania elementów webhook w alertach](../azure-monitor/platform/alerts-webhooks.md).
* Pobierz [Przegląd dzienników diagnostycznych](../azure-monitor/platform/diagnostic-logs-overview.md) i Zbieraj szczegółowe metryki o wysokiej częstotliwości w Twojej usłudze.
* Pobierz [omówienie zbierania metryk](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) się upewnić, że usługa jest dostępna i działa prawidłowo.
