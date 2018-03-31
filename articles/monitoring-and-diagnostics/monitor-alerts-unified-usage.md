---
title: Utwórz, Wyświetl alerty i zarządzaj nimi alerty za pomocą usługi Azure Monitor — (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Nowe środowisko unified alerty Azure umożliwia tworzyć, wyświetlać i zarządzać metryki i zaloguj reguły alertów z jednego miejsca.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 36729da3-e002-4a64-86b2-2513ca2cbb58
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: 6bd2c452059950f714de304cc30c7b4bd023fd86
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="create-view-and-manage-alerts-using-azure-monitor---alerts-preview"></a>Utwórz, Wyświetl i alertami można zarządzać za pomocą monitora Azure - alerty (wersja zapoznawcza)

## <a name="overview"></a>Przegląd
W tym artykule przedstawiono sposób konfigurowania alertów za pomocą nowego interfejsu alerty (wersja zapoznawcza) w portalu Azure. Definicja alertu jest trzech części:
- Cel: Określonych zasobów platformy Azure, które mają być monitorowane
- Kryteria: Określony warunek lub logiki który podczas widoczne w sygnału, powinny powodować zgłaszanie akcji
- Akcja: Wywołań wysyłane do odbiorcy powiadomienia - poczty e-mail, programu SMS, webhook itp.

Alert (wersja zapoznawcza) używany jest termin **alerty dziennika** do opisywania alerty, gdy sygnał jest niestandardowe kwerendy na podstawie [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) lub [Azure Application Insights](../application-insights/app-insights-analytics.md). Metryki możliwości alertów o nazwie [niemal w czasie rzeczywistym Metryka alerty](monitoring-near-real-time-metric-alerts.md) w istniejące alerty środowisko jest określana jako **alerty Metryka** alertów (wersja zapoznawcza). W *alerty Metryka*, niektóre typy zasobów zapewniają [metryki wielowymiarowej](monitoring-metric-charts.md) dla określonych zasobów platformy Azure i dlatego alerty dla tych zasobów można wprowadzić bardziej szczegółowe za pomocą dodatkowe filtry na wymiary; takie alerty są określane jako **alerty Metryka wielowymiarowe**.
Alerty Azure (wersja zapoznawcza) umożliwia także wyświetlanie ujednoliconego reguły alertów i możliwość zarządzania nimi w jednym miejscu; w tym przeglądanie wszystkie nierozwiązane alerty. Dowiedz się więcej o funkcji [Alerts(Preview) Azure — omówienie](monitoring-overview-unified-alerts.md).

> [!NOTE]
> Gdy alerty Azure (wersja zapoznawcza) oferuje nowe i udoskonalone środowisko do tworzenia alertów w usłudze Azure. Istniejące [alerty Azure](monitoring-overview-alerts.md) środowisko pozostaje można używać
>

Szczegółowe dalej jest przewodnik krok po kroku przy użyciu alertów Azure (wersja zapoznawcza).

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Tworzenie reguły alertu z portalu Azure
1. W [portal](https://portal.azure.com/), wybierz pozycję **Monitor** i w sekcji monitorowanie — wybierz **alertów (wersja zapoznawcza)**.  
    ![Monitorowanie](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Wybierz **nową regułę alertu** przycisk, aby utworzyć nowy alert na platformie Azure.
    ![Dodawanie alertu](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. W sekcji Tworzenie alertu jest wyświetlana z trzech części składające się z: *Zdefiniuj warunek alertu*, *Zdefiniuj szczegóły alertu*, i *grupy akcji Definiuj*.

    ![Utwórz regułę](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Zdefiniuj warunek alertu za pomocą **zasobów wybierz** link i określanie docelowej, wybierając zasobu. Filtr odpowiednio wybierając, potrzebne *subskrypcji*, *typu zasobu*, a na koniec wybranie wymagane *zasobów*.

    >[!NOTE]

    > Sprawdź sygnały dla wybranego zasobu przed podjęciem dalszych działań.

    ![Wybieranie zasobu](./media/monitor-alerts-unified/Alert-SelectResource.png)

 Jako alerty Azure (wersja zapoznawcza) umożliwia tworzenie różnych typów alertów za pomocą pojedynczej interfejsu; na podstawie typu alertu potrzeby wybierz następny krok:

    - Dla **alerty Metryka**: wykonaj kroki od 5 do 7; przejdź bezpośrednio do kroku 11
    - Aby uzyskać **alerty dziennika**, przejść do kroku 8.

    > [!NOTE]

    > Ujednolicone alerty (wersja zapoznawcza) obsługuje również alerty dziennik aktywności. [Dowiedz się więcej](monitoring-activity-log-alerts-new-experience.md).

5. *Alerty metryki*: Upewnij się, **typu zasobu** wybrano typu sygnału jako **Metryka**, następnie raz właściwe **zasobów** jest wybierany kliknij  *Gotowe* przycisk, aby powrócić do tworzenia alertu. Następnie użyj **Dodaj kryteria** przycisk, aby wybrać określonego sygnału z listy opcji sygnału, ich monitorowania usługi i typu na liście — które są dostępne dla zasobu wybranymi wcześniej.

    ![Wybierz zasób](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Wszystkie [alertów w czasie rzeczywistym w pobliżu](monitoring-near-real-time-metric-alerts.md) stanie zasoby są przedstawione z usługą monitora **platformy** i sygnalizuje typu jako **metryki**

6. *Alerty metryki*: po wybraniu sygnału, należy podać logiki alertów. Odwołania, historyczne dane sygnału są wyświetlane z opcją, aby dostosować przy użyciu okna czasu **Pokaż historię**, różnej od ostatnich sześciu godzin do ostatniego tygodnia. Z wizualizacji w miejscu **alertu logiki** można wybierać z wyświetlane opcje warunku, agregacji i na koniec wartości progowej. Jako wersja zapoznawcza logiki pod warunkiem warunek jest wyświetlany w wizualizacji wraz z historii sygnału wskazująca, czy ma wyzwolenia alertu. Wskaż, jakie czas trwania alertu należy znaleźć określony warunek, wybierając z **okres** opcji oraz jak często Alert ma być uruchamiany przez wybranie **częstotliwość**.

    ![Konfiguruj logikę sygnału metryki](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Alerty metryki*: jeśli sygnał jest metrykę, a następnie okno alertu można filtrować przy użyciu wielu punktów danych lub wymiarów dla tych zasobów platformy Azure. Podobnie jak alerty metryki, wizualizacji historii sygnału można wybrać poprzez podanie czasu trwania z **Pokaż historię** listy rozwijanej. Ponadto można wybrać wymiarów dla wybranego metryki filtrowania dla szeregów czasowych wymagane; Wybieranie wymiarów jest opcjonalna i o pięć wymiary mogą być używane. **Alert logiki** można wybierać z wyświetlane opcje warunku, agregacji i na koniec wartości progowej. Jako wersja zapoznawcza logiki pod warunkiem warunek jest wyświetlany w wizualizacji wraz z historii sygnału sygnalizującego, kiedy alert będzie uruchomiony w przeszłości. Wskaż, jakie czas trwania alertu należy znaleźć określony warunek, wybierając z **okres** opcji oraz jak często Alert ma być uruchamiany przez wybranie **częstotliwość**.

    ![Konfiguruj logikę sygnału metryki wielowymiarowej](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Rejestrowania alertów*: Upewnij się, **typu zasobu** jest źródłem analytics, takich jak *analizy dzienników* lub *usługi Application Insights* i sygnalizuje typu jako **dziennika** , następnie raz właściwe **zasobów** jest wybrana, kliknij przycisk *gotowe*. Następnie użyj **Dodaj kryteria** przycisk, aby wyświetlić listę sygnału opcje dostępne dla zasobu i z listy sygnału **wyszukiwania dziennika niestandardowego** opcja wybrana dziennika monitora usługi, takiej jak *dziennika Analiza* lub *usługi Application Insights*.

   ![Wybierz zasób — dziennik niestandardowy wyszukiwania](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Alerty list (wersja zapoznawcza) można zaimportować zapytanie analytics jako typ sygnału — **dziennika (zapisane zapytanie)**, jak pokazano powyżej ilustracji. Użytkownicy mogą doskonała kwerendy w module analiz i zapisaniu ich do użytku w przyszłości w alertach — więcej szczegółowych informacji przy użyciu Zapisywanie zapytań dostępne pod adresem [w analizy dzienników przy użyciu wyszukiwania dziennika](../log-analytics/log-analytics-log-searches.md) lub [udostępnione zapytania w usłudze application insights Analiza](../log-analytics/log-analytics-overview.md). 

9.  *Rejestrowania alertów*: po wybraniu kwerendy alertów może być wyrażona jako **zapytania wyszukiwania** pola; Jeśli składnia kwerendy jest nieprawidłowa, pole zawiera błąd kolorem CZERWONYM. Czy składnia kwerendy jest prawidłowa — dla odwołania do danych historycznych podanego zapytania jest wyświetlany jako wykres z opcją, aby dostosować przedział czasu do ostatniego tygodnia z ostatnich sześciu godzin.

 ![Skonfiguruj reguły alertu](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Wizualizacja danych historycznych można wyświetlić tylko jeśli wyniki zapytania mają szczegóły godziny. Jeśli zapytanie powoduje podsumowanie danych lub wartości w kolumnie określonej - sam jest wyświetlany jako pojedynczej kreślenia.

    >  Dla typu metryki pomiaru wpisów dziennika przy użyciu usługi Application insights, można określić które określonej zmiennej do grupowania danych przy użyciu **agregacji na** opcję; w sposób przedstawiony poniżej:

    ![Agreguj opcji](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Rejestrowania alertów*: Z wizualizacji w miejscu, **alertu logiki** można wybierać z wyświetlane opcje warunku, agregacji i na koniec wartości progowej. Określ koniec logiki, czas do oceny, czy określony warunek, za pomocą **okres** opcji. Wraz z jak często Alert ma być uruchamiany przez wybranie **częstotliwość**.
Aby uzyskać **alerty dziennika** alerty mogą być oparte na:
   - *Liczba rekordów*: alert jest tworzony, jeśli liczba rekordów zwróconych przez kwerendę jest większa lub mniejsza niż podana wartość.
   - *Metryki pomiaru*: alert jest tworzony, jeśli każdy *zagregować wartości* w wynikach przekracza wartość progową, pod warunkiem, i jest *pogrupowane według* wybranych wartości. Liczba naruszeń alertu jest liczba przypadków przekroczenia w wybranym okresie. Całkowita liczba naruszeń dla dowolnej kombinacji naruszeń można określić zestawu wyników lub kolejnych naruszeń wymaganie, że naruszeń musi występować w kolejnych próbek. Dowiedz się więcej o [dziennika alerty i typy](monitor-alerts-unified-log.md).

    > [!TIP]
    > Obecnie w alertach (wersja zapoznawcza) — alerty wyszukiwania dziennika może zająć niestandardowych *okres* i *częstotliwość* wartość min. Wartości mogą się różnić od 5 do 1440 minut (czyli) 24 godziny. Dlatego alertu czasu na przykład trzech godzin, przekonwertować go do minut — 180 minut przed użyciem

11. Drugim krokiem, należy zdefiniować nazwę alertu w **Nazwa reguły alertów** pola wraz z **opis** wyszczególnieniem szczegóły alertu i **ważność** wartość z Opcje przekazane. Te informacje są używane ponownie w wiadomości e-mail alertów, powiadomień lub wypychanej wykonywane przez Azure Monitor. Ponadto użytkownik może wybrać opcję natychmiast uaktywnić reguły alertu dotyczącego tworzenia odpowiednio przełączając **Włącz regułę po utworzeniu** opcji.

    Aby uzyskać **alerty dziennika** tylko kilka dodatkowych funkcji jest dostępna w szczegółach alertu:

    - **Pomijanie alertów**: po włączeniu funkcji pomijania alertów reguły akcje reguły są wyłączone dla określona długość czasu, po utworzeniu nowego alertu. Reguła jest nadal uruchomiona i tworzy rekordy alertu, podane kryteria są spełnione. Zezwolenie na obecność czas do rozwiązania problemu bez uruchamiania zduplikowane akcje.

        ![Pomijanie alertów dla dziennika alertów](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. Krok trzeci i końcowych, Określ ile **grupy akcji** musi być wywołany przez regułę alertu, po spełnieniu warunku alertu. Można wybrać żadnych istniejącej grupy akcji z alertem lub Utwórz nową grupę akcji. Zgodnie z wybrane grupy akcji, gdy alert jest wyzwalacza Azure zostanie: wysyłanie email(s), Wyślij SMS(s), wywołaj Webhook(s), skorygować za pomocą elementów Runbook platformy Azure, wypychania do zarządzanie usługami IT — tool, itp. Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md).

    Aby uzyskać **alerty dziennika** kilka dodatkowych funkcji jest dostępna w celu zastąpienia domyślnych akcji:

    - **Powiadomienia e-mail**: zastępuje *temat wiadomości e-mail* w wiadomości e-mail wysyłane za pośrednictwem grupy działań; Jeśli istnieje co najmniej jednego działania poczty e-mail w grupie tych akcji. Nie można zmodyfikować treść wiadomości e-mail i to pole jest **nie** adresu e-mail.
    - **Uwzględnij niestandardowy ładunek Json**: przesłania elementu webhook JSON używane przez akcję grup, jeśli istnieje co najmniej jeden element webhook akcji w grupie tych akcji. Użytkownik może zdefiniuj format JSON do użycia dla wszystkich elementów webhook skonfigurowane w skojarzonych grup akcji; Aby uzyskać więcej informacji na webhook formatów, zobacz [akcji elementu webhook dla alertów dziennika](monitor-alerts-unified-log-webhook.md). Opcja Webhook testu został dostarczony do Sprawdź format i przetwarzania przez miejsce docelowe za pomocą przykładowej JSON i opcja etykietę przeznaczone tylko dla **testowania** celów.

        ![Akcja zastąpienia dla dziennika alertów](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. Jeśli wszystkie pola są prawidłowe i zielony znacznik **Utwórz regułę alertu** kliknięcie przycisku i Alert jest tworzony w monitorze Azure - alerty (wersja zapoznawcza). Wszystkie alerty można przeglądać z poziomu pulpitu nawigacyjnego alerty (wersja zapoznawcza).

    ![Tworzenie reguł](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    W ciągu kilku minut alert jest aktywny i wyzwala w sposób opisany wcześniej.

## <a name="view-your-alerts-in-azure-portal"></a>Wyświetl alerty w portalu Azure

1. W [portal](https://portal.azure.com/), wybierz pozycję **Monitor** i w sekcji monitorowanie — wybierz **alertów (wersja zapoznawcza)**.  

2. **Alerty pulpitu nawigacyjnego (wersja zapoznawcza)** wyświetleniem - którym unified i wyświetlane w liczbie pojedynczej tablicy wszystkie alerty Azure ![alertu pulpitu nawigacyjnego](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)
3. Z góry od lewej do prawej pulpit nawigacyjny zawiera jeden rzut oka, następujące — które można kliknąć, aby wyświetlić listę szczegółowe:
    - *Alerty generowane*: Liczba aktualnie, alerty, które zostały spełnione logiki i w uruchamiany stanu
    - *Łączna liczba reguł alertów*: liczba alertów reguł utworzonych i w subtext, liczba, która jest włączona
4. Lista wszystkich alertów wypalane jest wyświetlany, które użytkownik może kliknąć, aby wyświetlić szczegóły
5. Nawiąże w określonych znajdowanie alertów; jeden, można użyć opcji listy rozwijanej na górze do filtrowania określonych *subskrypcji, grupy zasobów i/lub zasób*. Dalsze dla żadnego nierozpoznane alertów, użyj jednego *alert filtru* opcję, aby znaleźć podane słowo kluczowe — konkretnych alertów zgodnego z *nazwy, Alert kryteria, grupy zasobów oraz zasób docelowy*

## <a name="managing-your-alerts-in-azure-portal"></a>Zarządzanie alertami w portalu Azure
1. W [portal](https://portal.azure.com/), wybierz pozycję **Monitor** i w sekcji monitorowanie — wybierz **alertów (wersja zapoznawcza)**.  
2. Wybierz **zarządzanie regułami** znajdującego się na górnym pasku, aby przejść do sekcji zarządzania reguły — w którym są wyświetlane wszystkie reguły alertu utworzone; tym alerty, które zostały wyłączone.
3. Aby znaleźć określone reguły alertu, jeden albo użyć filtrów listy rozwijanej w górnej części, która zezwala na lista reguł alertów określonych *subskrypcji, grupy zasobów i/lub zasób*. Można również w używając przycisku Wyszukaj powyżej listy reguły alertów w okienku oznaczenie *filtrowania alertów*, jeden zapewniają — słowo kluczowe, które jest dopasowywana *nazwa alertu, stan i zasobu docelowego*; aby zobaczyć tylko reguł dopasowywania.
   ![Alert zarządzanie regułami](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. Aby wyświetlić lub zmodyfikować określonej reguły alertu, kliknij jego nazwę, który będzie wyświetlany jako łącze.
5. Zdefiniowane wyświetlany jest alert - w strukturze trzy etap: Grupa 3) akcji szczegółów alertu 2) warunku alertu 1). **Docelowa kryteria** można kliknąć, aby zmodyfikować logiki alertu lub nowy kryteria można dodać po użyciu ikonę Kosza do usunięcia starszych logiki. Podobnie, w sekcji szczegółów alertu - **opis** i **ważność** może być modyfikowany. Można zmienić grupy akcji i nowy, może co do łączenia z alertów za pomocą **nową grupę akcji** przycisku.

   ![Zmodyfikuj regułę alertu](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Przy użyciu górnego panelu zmiany dotyczące alertu może być w tym odbicia: **zapisać** aby zatwierdzić zmiany zrobione alertu, **odrzucić** aby wrócić bez zatwierdzania zmian alertu, **Wyłącz**  do Dezaktywuj alert — po upływie którego on już nie działa lub wyzwala żadnych działań. I na koniec **usunąć** Aby trwale usunąć całą reguły alertu z systemu Azure.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o nowe [pobliżu metryki alertów w czasie rzeczywistym (wersja zapoznawcza)](monitoring-near-real-time-metric-alerts.md)
- Pobierz [omówienie zbierania metryk](insights-how-to-customize-monitoring.md) się upewnić, że usługa jest dostępna i elastyczny.
- Dowiedz się więcej o [alerty dziennika w alertach Azure (wersja zapoznawcza)](monitor-alerts-unified-log.md)
- [Dowiedz się więcej o alertach dziennika aktywności w środowisku alerty (wersja zapoznawcza)](monitoring-activity-log-alerts-new-experience.md)
