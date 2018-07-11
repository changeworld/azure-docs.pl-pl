---
title: Tworzyć, wyświetlać i zarządzać alerty monitora przy użyciu usługi Azure
description: Nowe środowisko unified alerty Azure umożliwia tworzyć, wyświetlać i zarządzać metryki i zaloguj reguły alertów z jednego miejsca.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 9ade63c07003ead55e63093ae74e876589ec8d61
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287841"
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Utwórz, Wyświetl, alerty i zarządzaj nimi za pomocą monitora Azure  

## <a name="overview"></a>Przegląd
W tym artykule przedstawiono sposób konfigurowania alertów za pomocą nowego interfejsu alerty w portalu Azure. Definicja alertu jest trzech części:
- Cel: Określonych zasobów platformy Azure, które mają być monitorowane
- Kryteria: Określony warunek lub logiki który podczas widoczne w sygnału, powinny powodować zgłaszanie akcji
- Akcja: Wywołań wysyłane do odbiorcy powiadomienia - poczty e-mail, programu SMS, webhook itp.

Alerty Azure udostępnia również ujednoliconego podglądu reguły alertów i możliwość zarządzania nimi w jednym miejscu; w tym przeglądanie wszystkie nierozwiązane alerty. Dowiedz się więcej o funkcji [Azure alerty — omówienie](monitoring-overview-unified-alerts.md).

Alert używany jest termin **alerty dziennika** do opisywania alerty, gdy sygnał jest niestandardowe kwerendy na podstawie [analizy dzienników](../log-analytics/log-analytics-tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-analytics.md). [Nowe możliwości alertów metryki](monitoring-near-real-time-metric-alerts.md) zapewnia możliwość alert na [metryki wielowymiarowej](monitoring-metric-charts.md) dla określonych zasobów platformy Azure. Alerty dla tych zasobów można użyć dodatkowych filtrów wymiarów, tworzenie **alerty Metryka wielowymiarowe**.


> [!NOTE]
> Podczas alerty Azure oferuje nowe i udoskonalone środowisko do tworzenia alertów w usłudze Azure. Istniejące [alertów (klasyczne)](monitoring-overview-alerts.md) pozostaje użytecznego środowiska.
>

Szczegółowe dalej jest przewodnik krok po kroku przy użyciu usługi Azure alerty.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Tworzenie reguły alertu przy użyciu portalu Azure
1. W [portal](https://portal.azure.com/), wybierz pozycję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.  
    ![Monitorowanie](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Wybierz **nową regułę alertu** przycisk, aby utworzyć nowy alert na platformie Azure.
    ![Dodawanie alertu](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. W sekcji Tworzenie alertu jest wyświetlana z trzech części składające się z: *Zdefiniuj warunek alertu*, *Zdefiniuj szczegóły alertu*, i *grupy akcji Definiuj*.

    ![Utwórz regułę](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Zdefiniuj warunek alertu za pomocą **zasobów wybierz** link i określanie docelowej, wybierając zasobu. Filtr, wybierając * subskrypcji, * typ zasobu, a na koniec wybierając wymagane *zasobów*.

    >[!NOTE]

    > Sprawdź sygnały dla wybranego zasobu przed podjęciem dalszych działań.

    ![Wybieranie zasobu](./media/monitor-alerts-unified/Alert-SelectResource.png)

 Interfejs użytkownika pozwala utworzyć różne typy alertów w jednym miejscu. Na podstawie typu alertu potrzeby wybierz następny krok:

    - Dla **alerty Metryka**: wykonaj kroki od 5 do 7; przejdź bezpośrednio do kroku 11
    - Aby uzyskać **alerty dziennika**, przejść do kroku 8.

    > [!NOTE]

    > Alerty dziennika aktywności są również obsługiwane, ale są w wersji zapoznawczej. [Dowiedz się więcej](monitoring-activity-log-alerts-new-experience.md).

5. * Metryki alerty: Upewnij się, **typu zasobu** wybrano typu sygnału jako **Metryka**, następnie raz właściwe **zasobów** jest wybierany kliknij *gotowe*przycisk, aby powrócić do tworzenia alertu. Następnie użyj **Dodaj kryteria** przycisk, aby wybrać określonego sygnału z listy opcji sygnału, ich monitorowania usługi i typu na liście — które są dostępne dla zasobu wybranymi wcześniej.

    ![Wybierz zasób](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Wszystkie [alertów w czasie rzeczywistym w pobliżu](monitoring-near-real-time-metric-alerts.md) stanie zasoby są przedstawione z usługą monitora **platformy** i sygnalizuje typu jako **metryki**

6. *Alerty metryki*: po wybraniu sygnału, należy podać logiki alertów. Odwołania, historyczne dane sygnału są wyświetlane z opcją, aby dostosować przy użyciu okna czasu **Pokaż historię**, różnej od ostatnich sześciu godzin do ostatniego tygodnia. Z wizualizacji w miejscu **alertu logiki** można wybierać z wyświetlane opcje warunku, agregacji i na koniec wartości progowej. Jako wersja zapoznawcza logiki pod warunkiem warunek jest wyświetlany w wizualizacji wraz z historii sygnału wskazująca, czy ma wyzwolenia alertu. Wskaż, jakie czas trwania alertu należy znaleźć określony warunek, wybierając z **okres** opcji oraz jak często Alert ma być uruchamiany przez wybranie **częstotliwość**.

    ![Konfiguruj logikę sygnału metryki](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Alerty metryki*: jeśli sygnał jest metrykę, a następnie okno alertu można filtrować przy użyciu wielu punktów danych lub wymiarów dla tych zasobów platformy Azure. 

    a. Wybierz czas trwania z **Pokaż historię** listy rozwijanej, aby wizualizować inny okres czasu. Możesz wybrać wymiarów dla obsługiwanych metryki odfiltrować szeregów czasowych; Wybieranie wymiarów jest opcjonalna i o pięć wymiary mogą być używane. 

    b. **Alert logiki** można wybierać z podanymi opcjami *warunku*, * agregacji, i *próg*. Jako wersja zapoznawcza logiki pod warunkiem warunek jest wyświetlany w wizualizacji wraz z historii sygnału sygnalizującego, kiedy alert będzie uruchomiony w przeszłości. 

    c. Aby określić czas trwania, wybierz **okres** oraz jak często alert powinno być ono uruchomione po wybraniu **częstotliwość**.

    ![Konfiguruj logikę sygnału metryki wielowymiarowej](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Rejestrowania alertów*: Upewnij się, **typu zasobu** jest źródłem analytics, takich jak *analizy dzienników* lub *usługi Application Insights* i sygnalizuje typu jako **dziennika** , następnie raz właściwe **zasobów** jest wybrana, kliknij przycisk *gotowe*. Następnie użyj **Dodaj kryteria** przycisk, aby wyświetlić listę sygnału opcje dostępne dla zasobu i z listy sygnału **wyszukiwania dziennika niestandardowego** opcja wybrana dziennika monitora usługi, takiej jak *dziennika Analiza* lub *usługi Application Insights*.

   ![Wybierz zasób — dziennik niestandardowy wyszukiwania](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Listy można zaimportować zapytanie analytics jako typ sygnału — alerty **dziennika (zapisane zapytanie)**, jak pokazano powyżej ilustracji. Użytkownicy mogą doskonała kwerendy w module analiz i zapisaniu ich do użytku w przyszłości w alertach — więcej szczegółowych informacji przy użyciu Zapisywanie zapytań dostępne pod adresem [w analizy dzienników przy użyciu wyszukiwania dziennika](../log-analytics/log-analytics-log-searches.md) lub [udostępnione zapytania w usłudze application insights Analiza](../log-analytics/log-analytics-overview.md). 

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
    > Obecnie w alerty — Alerty wyszukiwania dziennika można wykonać niestandardowe *okres* i *częstotliwość* wartość min. Wartości mogą się różnić od 5 do 1440 minut (czyli) 24 godziny. Dlatego alertu czasu na przykład trzech godzin, przekonwertować go do minut — 180 minut przed użyciem

11. Drugim krokiem, należy zdefiniować nazwę alertu w **Nazwa reguły alertów** pola wraz z **opis** wyszczególnieniem szczegóły alertu i **ważność** wartość z Opcje przekazane. Te informacje są używane ponownie w wiadomości e-mail alertów, powiadomień lub wypychanej wykonywane przez Azure Monitor. Ponadto użytkownik może wybrać opcję natychmiast uaktywnić reguły alertu dotyczącego tworzenia odpowiednio przełączając **Włącz regułę po utworzeniu** opcji.

    Aby uzyskać **alerty dziennika** tylko kilka dodatkowych funkcji jest dostępna w szczegółach alertu:

    - **Pomijanie alertów**: po włączeniu funkcji pomijania alertów reguły akcje reguły są wyłączone dla określona długość czasu, po utworzeniu nowego alertu. Reguła jest nadal uruchomiona i tworzy rekordy alertu, podane kryteria są spełnione. Zezwolenie na obecność czas do rozwiązania problemu bez uruchamiania zduplikowane akcje.

        ![Pomijanie alertów dla dziennika alertów](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > Określ pomijanie alertów wartość większą niż częstotliwość alert, aby upewnić się, że powiadomienia zostały zatrzymane bez nakładania się

12. Krok trzeci i końcowych, Określ ile **grupy akcji** musi być wywołany przez regułę alertu, po spełnieniu warunku alertu. Można wybrać żadnych istniejącej grupy akcji z alertem lub Utwórz nową grupę akcji. Zgodnie z wybrane grupy akcji, gdy alert jest wyzwalacza Azure zostanie: wysyłanie email(s), Wyślij SMS(s), wywołaj Webhook(s), skorygować za pomocą elementów Runbook platformy Azure, wypychania do zarządzanie usługami IT — tool, itp. Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md).

    Aby uzyskać **alerty dziennika** kilka dodatkowych funkcji jest dostępna w celu zastąpienia domyślnych akcji:

    - **Powiadomienia e-mail**: zastępuje *temat wiadomości e-mail* w wiadomości e-mail wysyłane za pośrednictwem grupy działań; Jeśli istnieje co najmniej jednej akcji poczty e-mail w grupie tych akcji. Nie można zmodyfikować treść wiadomości e-mail i to pole jest **nie** adresu e-mail.
    - **Uwzględnij niestandardowy ładunek Json**: przesłania elementu webhook JSON używane przez akcję grup, jeśli istnieje co najmniej jednej akcji elementu webhook w grupie tych akcji. Użytkownik może określić format JSON do użycia dla wszystkich elementów webhook skonfigurowane w skojarzonych grup akcji; Aby uzyskać więcej informacji na webhook formatów, zobacz [akcji elementu webhook dla alertów dziennika](monitor-alerts-unified-log-webhook.md). Opcja Webhook testu został dostarczony do Sprawdź format i przetwarzania przez miejsce docelowe za pomocą przykładowej JSON i ta opcja jako etykietą przeznaczone tylko dla **testowania** celów.

        ![Akcja zastąpienia dla dziennika alertów](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

        > [!NOTE]
        > Dla **Webhook testu** do pracy, należy również powinna obsługiwać punktu końcowego [Cross Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/) i użytkownika umożliwia mu problemów "Brak nagłówka Access-Control-Allow-Origin" CORS serwera proxy

13. Jeśli wszystkie pola są prawidłowe i zielony znacznik **Utwórz regułę alertu** kliknięcie przycisku i alert jest tworzony w monitorze Azure - alerty. Wszystkie alerty można wyświetlić alertów pulpitu nawigacyjnego.

    ![Tworzenie reguł](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    W ciągu kilku minut alert jest aktywny i wyzwala w sposób opisany wcześniej.

## <a name="view-your-alerts-in-azure-portal"></a>Wyświetl alerty w portalu Azure

1. W [portal](https://portal.azure.com/), wybierz pozycję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.  

2. **Pulpitu nawigacyjnego alerty** wyświetleniem - którym unified i wyświetlane w liczbie pojedynczej tablicy wszystkie alerty Azure ![alertu pulpitu nawigacyjnego](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)
3. Z góry od lewej do prawej pulpit nawigacyjny zawiera jeden rzut oka, następujące — które można kliknąć, aby wyświetlić listę szczegółowe:
    - *Alerty generowane*: Liczba aktualnie, alerty, które zostały spełnione logiki i w uruchamiany stanu
    - *Łączna liczba reguł alertów*: liczba alertów reguł utworzonych i w subtext, liczba, która jest włączona 
    

        > [!NOTE]
        > Aby zapewnić spójne pulpitu nawigacyjnego ze szczegółami wszystkie wywoływane alerty, między innymi alertów dziennika dla usługi application insights i analizy dzienników; [Rozszerzony ujednoliconego alert (wersja zapoznawcza)](monitoring-overview-unified-alerts.md#enhanced-unified-alerts-public-preview) powinien być używany
  
  
4. Lista wszystkich alertów wypalane jest wyświetlany, które użytkownik może kliknąć, aby wyświetlić szczegóły
5. Nawiąże w określonych znajdowanie alertów; jeden, można użyć opcji listy rozwijanej na górze do filtrowania określonych *subskrypcji, grupy zasobów i/lub zasób*. Dalsze dla żadnego nierozpoznane alertów, użyj jednego *alert filtru* opcję, aby znaleźć podane słowo kluczowe — konkretnych alertów zgodnego z *nazwy, Alert kryteria, grupy zasobów oraz zasób docelowy*

## <a name="managing-your-alerts-in-azure-portal"></a>Zarządzanie alertami w portalu Azure
1. W [portal](https://portal.azure.com/), wybierz pozycję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.  
2. Wybierz **zarządzanie regułami** znajdującego się na górnym pasku, aby przejść do sekcji zarządzania reguły — w którym są wyświetlane wszystkie reguły alertu utworzone; tym alerty, które zostały wyłączone.
3. Aby znaleźć określone reguły alertu, jeden albo użyć filtrów listy rozwijanej w górnej części, która zezwala na lista reguł alertów określonych *subskrypcji, grupy zasobów i/lub zasób*. Można również w używając przycisku Wyszukaj powyżej listy reguły alertów w okienku oznaczenie *filtrowania alertów*, jeden zapewniają — słowo kluczowe, które jest dopasowywana *nazwa alertu, stan i zasobu docelowego*; aby zobaczyć tylko reguł dopasowywania.
   ![Alert zarządzanie regułami](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. Aby wyświetlić lub zmodyfikować określonej reguły alertu, kliknij jego nazwę, który będzie wyświetlany jako łącze.
5. Zdefiniowane wyświetlany jest alert - w strukturze trzy etap: Grupa 3) akcji szczegółów alertu 2) warunku alertu 1). **Docelowa kryteria** można kliknąć, aby zmodyfikować logiki alertu lub nowy kryteria można dodać po użyciu ikonę Kosza do usunięcia starszych logiki. Podobnie, w sekcji szczegółów alertu - **opis** i **ważność** może być modyfikowany. Można zmienić grupy akcji i nowy, może co do łączenia z alertów za pomocą **nową grupę akcji** przycisku.

   ![Zmodyfikuj regułę alertu](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Przy użyciu górnego panelu zmiany dotyczące alertu może być w tym odbicia: **zapisać** aby zatwierdzić zmiany zrobione alertu, **odrzucić** aby wrócić bez zatwierdzania zmian alertu, **Wyłącz**  do Dezaktywuj alert — po upływie którego on już nie działa lub wyzwala żadnych działań. I na koniec **usunąć** Aby trwale usunąć całą reguły alertu z systemu Azure.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o nowe [pobliżu metryki alertów w czasie rzeczywistym](monitoring-near-real-time-metric-alerts.md)
- Pobierz [omówienie zbierania metryk](insights-how-to-customize-monitoring.md) się upewnić, że usługa jest dostępna i elastyczny.
- Dowiedz się więcej o [rejestrowania alertów w alertach Azure](monitor-alerts-unified-log.md)
- [Dowiedz się więcej o alertach dziennika aktywności w środowisku alerty (wersja zapoznawcza)](monitoring-activity-log-alerts-new-experience.md)
