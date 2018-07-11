---
title: Tworzenie, wyświetlanie i zarządzanie nimi za pomocą usługi Azure Monitor alertów
description: Nowe środowisko ujednoliconego alertów platformy Azure umożliwia tworzenie, wyświetlanie i zarządzanie metryki oraz rejestrować reguły alertów w jednym miejscu.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: a913075c051c6b784495917b7edbd7340254a212
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952634"
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Tworzenie, wyświetlanie, alerty i zarządzaj nimi przy użyciu usługi Azure Monitor  

## <a name="overview"></a>Przegląd
W tym artykule pokazano, jak skonfigurować alerty przy użyciu nowego interfejsu alertów w witrynie Azure portal. Definicja reguły alertu znajduje się w trzech części:
- Cel: Określony zasób platformy Azure, która ma być monitorowane
- Kryteria: Określony warunek lub Logic Apps, gdy widoczne w sygnał, powinny wyzwalać akcję
- Akcja: Wywołań wysyłanych do odbiorcy powiadomienia — Wyślij wiadomość e-mail, wiadomość SMS, element webhook itp.

Alerty platformy Azure również zapewnia spójny widok wszystkich reguł alertów i możliwość zarządzania nimi w jednym miejscu; w tym wyświetlanie wszystkie nierozwiązane alerty. Dowiedz się więcej o funkcji [alertów platformy Azure — omówienie](monitoring-overview-unified-alerts.md).

Alert używany jest termin **alertów dzienników** do opisania alerty, gdy sygnał jest zapytanie niestandardowe na podstawie [usługi Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-analytics.md). [Nowe możliwości alertu metryki](monitoring-near-real-time-metric-alerts.md) zapewnia możliwość alertu na [metryk wielowymiarowych](monitoring-metric-charts.md) dla określonych zasobów platformy Azure. Alerty dla takich zasobów, można użyć dodatkowych filtrów wymiarów, tworzenie **alertów metryk wielowymiarowych**.


> [!NOTE]
> Podczas alertów platformy Azure oferuje nowe i ulepszone środowisko do tworzenia alertów na platformie Azure. Istniejące [alerty (klasyczne)](monitoring-overview-alerts.md) środowisko pozostaje użyteczne.
>

Szczegółowe dalej jest przewodnik krok po kroku do korzystania z alertów platformy Azure.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Utwórz regułę alertu przy użyciu witryny Azure Portal
1. W [portal](https://portal.azure.com/), wybierz opcję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.  
    ![Monitorowanie](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Wybierz **nową regułę alertu** przycisk, aby utworzyć nowego alertu na platformie Azure.
    ![Dodaj Alert](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. W sekcji Tworzenie alertu jest przedstawiana wraz z trzech części, składający się z: *Zdefiniuj warunek alertu*, *Zdefiniuj szczegóły alertu*, i *zdefiniuj grupę akcji*.

    ![Utwórz regułę](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Zdefiniuj warunek alertu przy użyciu **wybierz zasób** łącze i określenie docelowej, wybierając zasobu. Filtr, wybierając pozycję * subskrypcji, * typ zasobu, a na koniec wybierając wymagane *zasobów*.

    >[!NOTE]

    > Sprawdź dostępne sygnały dla wybranego zasobu, przed kontynuowaniem.

    ![Wybieranie zasobu](./media/monitor-alerts-unified/Alert-SelectResource.png)

 Interfejs użytkownika umożliwia tworzenie różnych typów alertów w jednym miejscu. Na podstawie typu alertu żądanego wybierz następny krok:

    - Aby uzyskać **alerty metryki**: wykonaj kroki od 5 do 7; następnie przejść bezpośrednio do kroku 11
    - Aby uzyskać **alertów dzienników**, przejdź do kroku 8.

    > [!NOTE]

    > Alerty dziennika aktywności są również obsługiwane, ale są w wersji zapoznawczej. [Dowiedz się więcej](monitoring-activity-log-alerts-new-experience.md).

5. *Alerty metryki*: Upewnij się, **typ zasobu** wybrano z typem sygnału jako **metryki**, następnie po odpowiednim **zasobów** wybrano kliknij  *Gotowe* przycisk, aby wrócić do tworzenia alertów. Następnie użyj **Dodaj kryteria** przycisk, aby wybrać określonego sygnału z listy opcji sygnał, usługi monitorowania i typu wymienionego — które są dostępne dla zasobów, wybrana wcześniej.

    ![Wybierz zasób](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Wszystkie [alerty w czasie rzeczywistym w pobliżu](monitoring-near-real-time-metric-alerts.md) możliwością zasoby są przedstawione za pomocą usługi monitor **platformy** i sygnałów typ jako **metryki**

6. *Alerty metryki*: po wybraniu sygnału może być wyrażona logikę alertów. Odwołanie, danych historycznych sygnału jest wyświetlany z opcją, aby dostosować za pomocą okna czasu **Pokaż historię**, różnej od ostatnich sześciu godzin do ostatniego tygodnia. Za pomocą wizualizacji w miejscu **Alert Logic** można wybrać opcje pokazano warunku, agregacji i na koniec próg. Jako wersja zapoznawcza logikę, pod warunkiem warunek jest wyświetlany w wizualizacji, wraz z historii sygnału, aby wskazać, czy masz wyzwolenia alertu. Wskaż, dla jakich czas trwania alertu należy szukać określony warunek, wybierając z **okres** opcji razem z częstotliwość uruchamiania alertu, wybierając **częstotliwość**.

    ![Konfigurowanie logiki sygnału dla metryki](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Alerty metryki*: sygnał jeżeli jest metrykę, a następnie okno alertu można filtrować przy użyciu wielu punktów danych lub wymiarów dla wymienionych zasobów platformy Azure. 

    a. Wybierz czas trwania z **Pokaż historię** listę rozwijaną, aby wizualizować inny okres czasu. Możesz wybrać wymiarów dla obsługiwanych metryk filtrowanie szeregów czasowych; Wybieranie wymiarów jest opcjonalny, a w górę do pięciu wymiary mogą być używane. 

    b. **Alert Logic** można wybierać z wyświetlanych opcji *warunek*, * agregacji i *próg*. Jako wersja zapoznawcza logikę, pod warunkiem warunek jest wyświetlany w wizualizacji, wraz z historii sygnału sygnalizującego, kiedy alert będzie uruchomiony w przeszłości. 

    c. Aby określić czas trwania, wybierz **okres** oraz częstotliwość uruchamiania alertu, wybierając **częstotliwość**.

    ![Konfigurowanie logiki sygnału dla metryk wielowymiarowych](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Alerty dzienników*: Upewnij się, **typ zasobu** jest źródłem analizy, takie jak *usługi Log Analytics* lub *usługi Application Insights* i sygnałów typ jako **dziennika** , następnie raz właściwe **zasobów** jest wybrana, kliknij przycisk *gotowe*. Dalej używać **Dodaj kryteria** przycisk, aby wyświetlić listę sygnałów dostępnymi dla zasobu, a z listy sygnału **przeszukiwania dzienników niestandardowych** opcję dla wybranego dziennika usługi monitor, takich jak *dziennika Analiza* lub *usługi Application Insights*.

   ![Wybierz zasób — wyszukiwanie dzienników niestandardowych](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Listy można zaimportować zapytania usługi analytics jako typ sygnału — alerty **dziennika (zapisane zapytanie)**, jak pokazano powyżej ilustracji. Dzięki temu użytkownicy może zapytania w usłudze Analytics udoskonalania i zapisać je do użytku w przyszłości w alertach — więcej szczegółowych informacji na temat korzystania z Zapisywanie zapytań dostępne pod adresem [przy użyciu przeszukiwania dzienników w usłudze log analytics](../log-analytics/log-analytics-log-searches.md) lub [udostępnionego zapytania w usłudze application insights Analiza](../log-analytics/log-analytics-overview.md). 

9.  *Alerty dzienników*: po wybraniu zapytania dotyczące alertów może być wyrażona w **zapytania wyszukiwania** pola; Jeśli składnia zapytania jest nieprawidłowa, pola są wyświetlane w kolorze CZERWONYM błędu. Jeśli składnia zapytania jest poprawna, — do użytku w danych historycznych w określonej kwerendy jest wyświetlany jako wykres z opcją, aby dostosować okno czasowe z ostatnich sześciu godzin do ostatniego tygodnia.

 ![Konfigurowanie reguły alertu](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Wizualizacja danych historycznych mogą być wyświetlane tylko jeśli wyniki zapytania mają szczegóły godziny. Jeśli zapytanie powoduje podsumowane dane lub wartości określonej kolumny — sama jest wyświetlany jako pojedynczej powierzchni.

    >  Dla typu pomiar metryki alertów dzienników za pomocą usługi Application insights, można określić, które określonej zmiennej, aby pogrupować dane przy użyciu **agregowane na** opcji; co przedstawiono poniżej:

    ![Agreguj według opcji](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Alerty dzienników*: wizualizacji w miejscu, **Alert Logic** można wybrać opcje pokazano warunku, agregacji i na koniec próg. Wskaż w logice, czas oceny pod kątem określonego warunku, przy użyciu **okres** opcji. Wraz z częstotliwość uruchamiania alertu, wybierając **częstotliwość**.
Aby uzyskać **alertów dzienników** alerty mogą być oparte na:
   - *Liczba rekordów*: alert jest tworzony, jeśli liczba rekordów zwróconych przez zapytanie jest większa lub mniejsza niż podana wartość.
   - *Pomiar metryki*: alert jest tworzony, jeśli każdy *agregować wartości* w wynikach przekracza wartość progową, pod warunkiem, i jest *pogrupowane według* wybrana wartość. Liczba naruszeń alertu jest liczba przypadków, gdy próg został przekroczony w wybranym okresie. Łączna liczba naruszeń w jakiejkolwiek kombinacji naruszeń można określić przez zestaw wyników lub kolejne naruszenia, aby wymagać, że naruszeń musi wystąpić w kolejnych próbek. Dowiedz się więcej o [alertów dzienników i ich typy](monitor-alerts-unified-log.md).

    > [!TIP]
    > Obecnie alerty — alertów wyszukiwania dziennika może to zająć niestandardowe *okres* i *częstotliwość* wartość min. Wartości mogą się różnić od 5 minut do 1440 minut (to znaczy) 24 godziny. Dlatego jeśli chcesz, aby alert czasu Załóżmy, że trzy godziny, przekonwertuj go w ciągu kilku minut — 180 minut przed użyciem

11. W drugim kroku należy zdefiniować nazwę alertu w **Nazwa reguły alertu** pola wraz z **opis** szczegółowych informacji na temat specyfiki alertu i **ważność** z wartości Opcje przekazane. Te informacje są używane ponownie w wiadomości e-mail alertów, powiadomień lub wypychania wykonywane przez usługi Azure Monitor. Ponadto użytkownik może wybrać od razu aktywować regułę alertu przy tworzeniu odpowiednio przełączając **Włącz regułę po utworzeniu** opcji.

    Aby uzyskać **alertów dzienników** tylko kilka dodatkowych funkcji jest dostępna w szczegółach alertu:

    - **Pomiń alerty**: po włączeniu funkcji pomijania reguła alertu akcje dla reguły są wyłączone w przypadku określona długość czasu, po utworzeniu nowego alertu. Reguła jest nadal uruchomione i tworzy rekordy alertów, pod warunkiem spełnienia kryteriów. Umożliwiając czasu, należy rozwiązać problem bez konieczności uruchamiania zduplikowane akcji.

        ![Pomiń alerty dla dziennika alertów](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > Określ wartość alertu Pomiń większe niż częstotliwość alertu, aby upewnić się, że powiadomienia zostały zatrzymane bez nakładania się

12. W trzeciej i ostatniej kroku określ ewentualne **grupy akcji** musi być wyzwalane dla reguły alertu, gdy zostanie spełniony warunek alertu. Można wybrać wszystkie istniejące grupy akcji z alertem lub Utwórz nową grupę akcji. Zgodnie z opisem w wybrane grupy akcji, gdy alert jest wyzwalacza Azure będzie: wysyłanie email(s), Wyślij SMS(s), wywoływać elementy webhook, korygowanie za pomocą elementów Runbook usługi Azure, wypychania narzędziem ITSM, itp. Dowiedz się więcej o [grup akcji](monitoring-action-groups.md).

    Aby uzyskać **alertów dzienników** kilka dodatkowych funkcji jest dostępny zastąpić domyślne akcje:

    - **Powiadomienia e-mail**: zastępuje *temat wiadomości e-mail* w wiadomości e-mail wysyłane za pośrednictwem grupy akcji, jeśli istnieje co najmniej jednej akcji poczty e-mail, wspomnianych grupy akcji. Nie można zmodyfikować treść wiadomości e-mail i to pole jest **nie** adresu e-mail.
    - **Uwzględnij niestandardowy ładunek Json**: zastępuje element webhook JSON używany przez grupy akcji; jeśli istnieje co najmniej jednej akcji elementu webhook, wspomnianych grupy akcji. Użytkownik może określić format JSON ma być używany dla wszystkich elementów webhook skonfigurowane w skojarzonej grupy akcji; Aby uzyskać więcej informacji dotyczących formatów elementu webhook, zobacz [Akcja elementu webhook dla dziennika alertów](monitor-alerts-unified-log-webhook.md). Opcja elementu Webhook test znajduje się w celu sprawdzenia formatu i przetwarzania przez miejsce docelowe za pomocą przykładowych JSON i tę opcję, zgodnie z etykietą jest przeznaczone tylko dla **testowania** celów.

        ![Akcja zastąpienia dla dziennika alertów](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

        > [!NOTE]
        > Dla **Testuj Webhooka** opcji do pracy, punkt końcowy powinien obsługiwać [Cross Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/) i użytkownika przy użyciu mechanizmu CORS proxy uniknąć problemów "Bez nagłówka Access-Control-Allow-Origin"

13. Jeśli wszystkie pola są prawidłowe i za pomocą znaczników zielony **Utwórz regułę alertu** kliknięcie przycisku, a alert jest tworzony w usłudze Azure Monitor — alerty. Wszystkie alerty mogą być wyświetlane z alerty pulpitu nawigacyjnego.

    ![Tworzenie reguł](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    W ciągu kilku minut ten alert jest aktywny i wyzwala w sposób opisany wcześniej.

## <a name="view-your-alerts-in-azure-portal"></a>Wyświetlanie alertów w witrynie Azure Portal

1. W [portal](https://portal.azure.com/), wybierz opcję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.  

2. **Pulpit nawigacyjny alertów** jest wyświetlany - polegającego unified wszystkie alerty platformy Azure i wyświetlana w liczbie pojedynczej tablicy ![pulpit nawigacyjny alertów](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)
3. Od lewej górnej, prawej strony na pulpicie nawigacyjnym prezentowana na pierwszy rzut oka następujące — które można kliknąć, aby wyświetlić szczegółową listę:
    - *Wyzwolone alerty*: Liczba obecnie alertów, które zostały spełnione logiki, a w wyzwolone stanu
    - *Łączna liczba reguł alertów*: Numer reguły alertów utworzone, a w polu podtekstu, liczba, która jest włączona 
    

        > [!NOTE]
        > Aby zapewnić spójne pulpit nawigacyjny ze szczegółowymi informacjami o wszystkich alertach wyzwoleń alertów dziennika dla usługi application insights i log analytics; w tym [Rozszerzony ujednoliconego alert (wersja zapoznawcza)](monitoring-overview-unified-alerts.md#enhanced-unified-alerts-public-preview) powinien być używany
  
  
4. Wyświetlana lista wszystkich wyzwolone alerty, które użytkownik może kliknąć, aby wyświetlić szczegóły
5. Łatwiejszemu w właściwe znajdowanie alertów; jeden, można użyć opcji listy rozwijanej na górze filtrowania określonych *subskrypcja, grupa zasobów i/lub zasób*. Dalsze dla dowolnego nierozpoznanych alert jedno użycie *alert filtru* — słowo kluczowe — pod warunkiem opcję, aby znaleźć konkretne alerty zgodnego z *nazwy, Alert kryteria, grupa zasobów, a zasób docelowy*

## <a name="managing-your-alerts-in-azure-portal"></a>Zarządzanie alertami w witrynie Azure Portal
1. W [portal](https://portal.azure.com/), wybierz opcję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.  
2. Wybierz **zarządzanie regułami** znajdujący się na górnym pasku, aby przejść do sekcji Zarządzanie reguły — gdzie znajduje się lista wszystkich reguł alertów utworzone; w tym alerty, które zostały wyłączone.
3. Aby znaleźć określone reguły alertu, albo użyć można filtry listy rozwijanej u góry, które umożliwiają lista reguł alertów dla określonego *subskrypcji, grupy zasobów i/lub zasób*. Również na temat korzystania z wyszukiwania nad listą reguły alertu w okienku oznaczone *filtrowania alertów*, jeden zapewniają słowo kluczowe, które jest dopasowywana *nazwę alertu, stan i zasób docelowy*; aby pokazać tylko reguł dopasowania.
   ![Alert zarządzanie regułami](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. Aby wyświetlić lub zmodyfikować określonej reguły alertu, kliknij jego nazwę, która będzie wyświetlana jako łącze.
5. Definicja wyświetlany jest alert — w strukturze trzech etapów: 1) Alert warunek alertu (2) szczegółów (3) akcję grupy. **Docelowa kryteria** można kliknąć, aby zmodyfikować alert Logic Apps lub nowe kryteria można dodać po użycia ikonę Kosza, aby usunąć wcześniej logiki. Podobnie, w sekcji szczegółów alertu — **opis** i **ważność** może być modyfikowany. I można zmienić grupy akcji lub nowy, można specjalnie w celu łączenia z alertów za pomocą **Nowa grupa akcji** przycisku.

   ![Modyfikowanie reguły alertu](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Za pomocą panelu najważniejsze zmiany dotyczące alertu może być tym odbijającą: **Zapisz** do zatwierdzenia wszystkich zmian alert, **odrzucić** aby wrócić bez zobowiązania wszelkie zmiany wprowadzone do alertu, **wyłączone**  do Dezaktywuj alert - po upływie którego go już nie działa lub wyzwala żadnych działań. I wreszcie **Usuń** Aby trwale usunąć całą regułę alertu z systemu Azure.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o nowym [niemal w czasie rzeczywistym alertów dotyczących metryk](monitoring-near-real-time-metric-alerts.md)
- Pobierz [omówienie zbierania metryk](insights-how-to-customize-monitoring.md) się upewnić, że usługa jest dostępna i działa prawidłowo.
- Dowiedz się więcej o [alerty dzienników w alertów platformy Azure](monitor-alerts-unified-log.md)
- [Więcej informacji na temat alertów dziennika aktywności w środowisku alerty (wersja zapoznawcza)](monitoring-activity-log-alerts-new-experience.md)
