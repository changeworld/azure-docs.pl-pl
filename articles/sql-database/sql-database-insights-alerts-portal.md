---
title: Tworzenie alertów bazy danych SQL za pomocą portalu Azure | Dokumentacja firmy Microsoft
description: Użyj portalu Azure, aby utworzyć alerty bazy danych SQL, które mogą wyzwalać powiadomienia lub automatyzacji po spełnieniu warunków, które określisz.
author: aamalvea
manager: craigg
services: sql-database
ms.service: sql-database
ms.custom: monitor and tune
ms.topic: article
ms.date: 04/01/2018
ms.author: aamalvea
ms.openlocfilehash: 0c543ccf1d2104a83f7610bc870334b460ec40fb
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="use-azure-portal-to-create-alerts-for-azure-sql-database-and-data-warehouse"></a>Utwórz alerty dla bazy danych SQL Azure i magazynem danych za pomocą portalu Azure

## <a name="overview"></a>Przegląd
W tym artykule przedstawiono sposób konfigurowania alertów bazy danych SQL Azure i magazynem danych przy użyciu portalu Azure. Ten artykuł zawiera również najlepsze rozwiązania dotyczące ustawiania alertu okresów.    

Możesz otrzymywać alertu na podstawie metryki monitorowania lub zdarzenia na usługami Azure.

* **Wartości metryki** — uruchamia alert, gdy wartość określonej metryki przekracza próg przypisać w żadnym kierunku. Oznacza to, że oba wyzwala po spełnieniu warunku zostanie najpierw i następnie później podczas warunku jest już spełniane.    
* **Zdarzenia dziennika aktywności** -alert może wyzwolić na *co* zdarzenia lub tylko wtedy, gdy wystąpić określona liczba zdarzeń.

Możesz skonfigurować alert o konieczności wyzwala, wykonaj następujące czynności:

* wysyłanie powiadomień e-mail do administratora usługi i współadministratorzy
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail przez użytkownika.
* Wywołanie elementu webhook

Można skonfigurować i uzyskać informacje na temat przy użyciu reguły alertów

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [Program PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Interfejs wiersza polecenia (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Interfejs API REST Azure monitora](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Tworzenie reguły alertu na Metryka z portalu Azure
1. W [portal](https://portal.azure.com/)zasobów planuje się monitorowanie Znajdź i zaznacz go.
2. Ten krok jest różne dla bazy danych SQL i pul elastycznych w porównaniu z magazynu danych SQL: 

   - **Bazy danych SQL i elastyczne pule tylko**: Wybierz **alerty** lub **reguły alertów** w sekcji monitorowanie. Tekst i ikona mogą się nieco różnić dla różnych zasobów.  
   
     ![Monitorowanie](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButton.png)
  
   - **TYLKO z magazynu danych SQL**: Wybierz **monitorowanie** w sekcji typowych zadań. Kliknij przycisk **użycia DWU** wykresu.

     ![TYPOWE ZADANIA](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButtonDW.png)

3. Wybierz **Dodaj alert** poleceń i wypełnij pola.
   
    ![Dodaj alert](../monitoring-and-diagnostics/media/insights-alerts-portal/AddDBAlertPage.png)
4. **Nazwa** alertu reguły, a następnie wybierz pozycję **opis**, który pokazuje również w wiadomości e-mail z powiadomieniem.
5. Wybierz **Metryka** chcesz monitorować, a następnie wybierz pozycję **warunku** i **próg** wartość metryki. Wybierz również **okres** czas, przez który metryki reguły muszą zostać spełnione przed wyzwalaczy alertu. Tak na przykład, jeśli używasz okres "PT5M" i alertu szuka procesora CPU przekracza 80%, alert wyzwala kiedy **średni** były procesora CPU przekracza 80% 5 minut. W momencie to pierwszy wyzwalacz, ponownie uruchamia to, gdy średnie wykorzystanie Procesora wynosi poniżej 80% ponad 5 minut. Pomiar Procesora występuje co minutę. Poszukaj poniższą tabelę obsługiwanych czas systemu windows i agregacji wpisz każdy alert nie używa wszystkie alerty Użyj średnia wartość.   
6. Sprawdź **E-mail właścicieli...**  Jeśli chcesz, aby administratorzy i współadministratorzy w celu przesłania pocztą e-mail po zgłoszeniu alertu.
7. Jeśli chcesz, dodatkowe wiadomości e-mail, aby otrzymać powiadomienie po zgłoszeniu alertu, dodaj je w **email(s) dodatkowe administratora** pola. Wiele wiadomości e-mail należy rozdzielić średnikami -  *email@contoso.com;email2@contoso.com*
8. Umieść w prawidłowym identyfikatorem URI w **Webhook** jeśli ma ona wywoływana po zgłoszeniu alertu.
9. Wybierz **OK** po zakończeniu można utworzyć alertu.   

W ciągu kilku minut alert jest aktywny i wyzwala w sposób opisany wcześniej.

## <a name="managing-your-alerts"></a>Zarządzanie alertami
Po utworzeniu alertu, zostanie ona wybrana oraz:

* Wyświetl wykres przedstawiający próg metryki i rzeczywistymi wartościami z poprzedniego dnia.
* Edytuj lub usuń go.
* **Wyłącz** lub **włączyć** go, jeśli chcesz tymczasowo zatrzymać lub wznowić odbieranie powiadomień dla tego alertu.


## <a name="sql-database-alert-values"></a>Wartości alertu bazy danych SQL

| Typ zasobu | Nazwa metryki | Przyjazna nazwa | Typ agregacji | Minimalny czas alertu okna|
| --- | --- | --- | --- | --- |
| Baza danych SQL | cpu_percent | Procent użycia procesora CPU | Średnia | 5 minut |
| Baza danych SQL | physical_data_read_percent | Procent użycia operacji we/wy na danych | Średnia | 5 minut |
| Baza danych SQL | log_write_percent | Procent we/wy dziennika | Średnia | 5 minut |
| Baza danych SQL | dtu_consumption_percent | Procent użycia jednostek DTU | Średnia | 5 minut |
| Baza danych SQL | magazyn | Rozmiar całkowitą bazy danych | Maksimum | 30 minut |
| Baza danych SQL | connection_successful | Udane połączenia | Łącznie | 10 minut |
| Baza danych SQL | connection_failed | Połączenia nie powiodło się | Łącznie | 10 minut |
| Baza danych SQL | blocked_by_firewall | Blokowane przez zaporę | Łącznie | 10 minut |
| Baza danych SQL | Zakleszczenie | Zakleszczenie | Łącznie | 10 minut |
| Baza danych SQL | storage_percent | Procent użycia rozmiaru bazy danych | Maksimum | 30 minut |
| Baza danych SQL | xtp_storage_percent | Percent(Preview) magazynu OLTP w pamięci | Średnia | 5 minut |
| Baza danych SQL | workers_percent | Procent pracowników | Średnia | 5 minut |
| Baza danych SQL | sessions_percent | Procent sesji | Średnia | 5 minut |
| Baza danych SQL | dtu_limit | Limit jednostek dtu w warstwie | Średnia | 5 minut |
| Baza danych SQL | dtu_used | Jednostek dtu w warstwie używane | Średnia | 5 minut |
||||||
| Pula elastyczna | cpu_percent | Procent użycia procesora CPU | Średnia | 10 minut |
| Pula elastyczna | physical_data_read_percent | Procent użycia operacji we/wy na danych | Średnia | 10 minut |
| Pula elastyczna | log_write_percent | Procent we/wy dziennika | Średnia | 10 minut |
| Pula elastyczna | dtu_consumption_percent | Procent użycia jednostek DTU | Średnia | 10 minut |
| Pula elastyczna | storage_percent | Procent użycia magazynu | Średnia | 10 minut |
| Pula elastyczna | workers_percent | Procent pracowników | Średnia | 10 minut |
| Pula elastyczna | eDTU_limit | limit liczby jednostek eDTU | Średnia | 10 minut |
| Pula elastyczna | storage_limit | Limit magazynu | Średnia | 10 minut |
| Pula elastyczna | eDTU_used | eDTU używane | Średnia | 10 minut |
| Pula elastyczna | storage_used | Użyty magazyn | Średnia | 10 minut |
||||||               
| magazyn danych SQL | cpu_percent | Procent użycia procesora CPU | Średnia | 10 minut |
| magazyn danych SQL | physical_data_read_percent | Procent użycia operacji we/wy na danych | Średnia | 10 minut |
| magazyn danych SQL | magazyn | Rozmiar całkowitą bazy danych | Maksimum | 10 minut |
| magazyn danych SQL | connection_successful | Udane połączenia | Łącznie | 10 minut |
| magazyn danych SQL | connection_failed | Połączenia nie powiodło się | Łącznie | 10 minut |
| magazyn danych SQL | blocked_by_firewall | Blokowane przez zaporę | Łącznie | 10 minut |
| magazyn danych SQL | service_level_objective | Cel poziomu usługi bazy danych | Łącznie | 10 minut |
| magazyn danych SQL | dwu_limit | Jednostka dwu limit | Maksimum | 10 minut |
| magazyn danych SQL | dwu_consumption_percent | Procent jednostka DWU | Średnia | 10 minut |
| magazyn danych SQL | dwu_used | Jednostka DWU używane | Średnia | 10 minut |
||||||


## <a name="next-steps"></a>Kolejne kroki
* [Omówienie monitorowania Azure](../monitoring-and-diagnostics/monitoring-overview.md) w tym typy informacji, można zbierać i monitorowania.
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Pobierz [Przegląd dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) i zbieranie szczegółowych metryki wysokiej częstotliwości w usłudze.
* Pobierz [omówienie zbierania metryk](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) się upewnić, że usługa jest dostępna i elastyczny.
