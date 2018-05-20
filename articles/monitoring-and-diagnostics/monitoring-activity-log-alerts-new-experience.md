---
title: Utwórz działanie dziennika alerty i zarządzaj nimi przy użyciu nowego środowiska alertów w monitorze Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje o sposobie tworzenia działania dziennika alertów na karcie alerty w obszarze Azure Monitor
author: JYOTHIRMAISURI
manager: vvithal
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: ''
ms.openlocfilehash: 070acc6fb6f6f744474edeef0e914dda9d24c558
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-experience"></a>Utwórz dziennik aktywności, które występować alertów za pomocą nowych alertów

Alerty dziennika aktywności są alerty, które uzyskać aktywowany, gdy wystąpi nowe zdarzenie dziennika aktywności spełnia warunki określone w alercie.

Te alerty są zasobów platformy Azure, można utworzyć za pomocą szablonu usługi Azure Resource Manager. One również może być utworzone, zaktualizowane lub usunięte w portalu Azure. W tym artykule przedstawiono pojęć dotyczących alertów dotyczących działań w dzienniku. Go następnie przedstawiono sposób Użyj portalu Azure, aby skonfigurować alert na działanie dziennika zdarzeń przy użyciu nowego środowiska w [alerty Azure](monitoring-overview-unified-alerts.md).

Zazwyczaj w celu utworzenia działania alerty dziennika, aby otrzymywać powiadomienia o wystąpieniu określonych zmian zasobów w Twojej subskrypcji platformy Azure, często są ograniczone do określonych grupach zasobów lub zasobu. Na przykład możesz chcieć powiadomienia, gdy dowolne maszyny wirtualnej (Grupa zasobów przykładowej) **myProductionResourceGroup** zostanie usunięty, można pobrać powiadamiani, jeśli wszystkie nowe role są przypisane do użytkownika w ramach subskrypcji.

Możesz skonfigurować alert dziennika działania na podstawie żadnych najwyższego poziomu właściwości w obiekcie JSON dla działania dziennika zdarzeń. Jednak portalu przedstawia najbardziej typowe opcje zgodnie z opisem w poniższych sekcjach:

>[!NOTE]

> Jeśli kategoria "administracyjne", należy określić co najmniej jednego z poprzednich kryteriów w alertu. Nie można utworzyć alertu, który uaktywnia za każdym razem, gdy zdarzenie jest tworzone w dziennikach działania.
>

Po aktywowaniu alert dziennika aktywności używa grupy akcji do wygenerowania akcje lub powiadomienia. Grupy akcji jest zestawem wielokrotnego użytku odbiorców powiadomień, takie jak adresy e-mail, numerów telefonów adresy URL elementu webhook lub wiadomości SMS. Odbiorcy mogą być przywoływane z wielu alertów scentralizować i grupach kanałów powiadomień. Podczas definiowania alertu dziennika aktywności, dostępne są dwie opcje. Możesz:

* Użyj istniejącej grupy działań w alertu dziennika aktywności.
* Utwórz nową grupę akcji.

Aby dowiedzieć się więcej o grupach akcji, zobacz [tworzenia grup działań w portalu Azure i zarządzanie nimi](monitoring-action-groups.md).

Aby dowiedzieć się więcej na temat powiadomień o kondycji usługi, zobacz [odbierzesz alerty dziennika działania dotyczące powiadomień o kondycji usługi](monitoring-activity-log-alerts-on-service-notifications.md).


## <a name="whats-new-in-alerts-for-activity-logs"></a>What's new in alerty o Dzienniki aktywności?

[Alerty Azure](monitoring-overview-unified-alerts.md) teraz zapewnia lepszą obsługę użytkowników alertów dotyczących działań w dzienniku. Z [rozszerzonego środowiska użytkownika dla alertów](monitoring-overview-unified-alerts.md), możesz teraz:

- [Utwórz](#create-an-alert-rule-for-an-activity-log) i [zarządzanie](#view-and-manage-activity-log-alert-rules) działania logowania reguły alertów z **Monitor** > **alerty** bloku. Dowiedz się więcej o [Dzienniki aktywności](monitoring-overview-activity-logs.md).

- **Nowe opcje dla docelowego alerty**: podczas tworzenia nowej reguły alertu dziennika aktywności, można teraz wybrać zasobu docelowego lub grupę zasobów lub subskrypcji.


## <a name="create-an-alert-rule-for-an-activity-log"></a>Tworzenie reguły alertu dla dziennika aktywności

> [!NOTE]

>  Podczas tworzenia reguły alertu należy sprawdzić, czy:

> - Subskrypcji w zakresie nie jest inny niż subskrypcji, gdy alert jest tworzony.
- Kryteria muszą być poziom/status/wywołującego / grupa zasobów / identyfikator zasobu / Typ zasobu / kategorii zdarzeń, na którym alert został skonfigurowany.
- Nie istnieje warunek "anyOf" lub zagnieżdżone warunków w konfiguracji alertu JSON (zasadniczo wszystkie tylko jedna jest dozwolona z nie dalsze wszystkie/anyOf).


Postępuj zgodnie z następującą procedurą:

1. Z portalu Azure, wybierz **Monitor** > **alertów**
2. Kliknij przycisk **nową regułę alertu** w górnej części **alerty** okna.

     ![nowe reguły alertu](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     **Utwórz reguły** zostanie wyświetlone okno.

      ![nowe opcje reguły alertu](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. **W warunkach zdefiniuj alertu** Podaj następujące informacje, a następnie kliknij przycisk **gotowe**.

    - **Cel alertu:** Aby wyświetlić i wybierz element docelowy dla nowego alertu, należy użyć **filtrowanie według subskrypcji** / **Filtruj według typu zasobu** i wybierz zasób lub grupa zasobów z Lista wyświetlana.

    > [!NOTE]

    > można wybrać zasobu, grupy zasobów lub całej subskrypcji.

    **Widok próbki alertu elementu docelowego**

     ![Wybierz element docelowy](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - W obszarze **kryteria docelowej**, kliknij przycisk **Dodaj kryteria** wybierz typ sygnału jako **dziennik aktywności**.

    - Wybierz sygnał z wyświetlonej listy.

    Możesz wybrać oś czasu historii dziennika i odpowiednie logikę alert ten sygnał docelowy:

    **Dodaj kryteria ekranu**

    ![Dodawanie kryteriów](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Czas historii**: w ciągu ostatnich 12/6/24 godzin, w ostatnim tygodniu.

    **Alert logiki**:

     - **Poziom zdarzenia**-poziom ważności zdarzenia. **Pełne, ostrzeżenie, informacyjny błąd**, lub **krytyczne**.
     - **Stan**: stan zdarzenia. **Uruchomiona, nie powiodło się**, lub **zakończyło się pomyślnie**.
     - **Zdarzenie inicjowane przez**: znany także jako wywołującego Adres e-mail lub identyfikator użytkownika, który wykonał działanie usługi Azure Active Directory.

        **Wykres sygnału próbki z logiką alertu stosowane** :

        ![ wybrane kryteria](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. W obszarze **Zdefiniuj szczegóły reguły alertów**, należy podać następujące dane:

    - **Nazwa reguły alertów** — Nazwa nowej reguły alertu
    - **Opis elementu** — opis nowej reguły alertu
    - **Zapisz alert do grupy zasobów** — wybierz grupę zasobów, której chcesz zapisać tej nowej reguły.

5. W obszarze **grupy akcji**, z menu rozwijanego, należy określić grupę akcji, którą chcesz przypisać do tej nowej reguły alertów. Alternatywnie [Utwórz nową grupę akcji](monitoring-action-groups.md) i przypisać do nowej reguły. Aby utworzyć nową grupę, kliknij przycisk **+ nową grupę**.

6. Aby włączyć reguły, po jego utworzeniu, kliknij przycisk **tak** dla **Włącz regułę po utworzeniu** opcji.
7. Kliknij przycisk **Utwórz regułę alertu**.

    Jest tworzona nowa reguła alertów dla dziennika aktywności i zostanie wyświetlony komunikat potwierdzenia u góry po prawej części okna.

    ![ utworzyć regułę alertu](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    Można włączyć, wyłączyć, edytować lub usunąć regułę. [Dowiedz się więcej](#view-and-manage-activity-log-alert-rules) o zarządzanie regułami dziennika aktywności.

## <a name="view-and-manage-activity-log-alert-rules"></a>Wyświetl i Zarządzaj regułami alertów dziennika aktywności

1. W portalu Azure kliknij **Monitor** > **alerty** i kliknij przycisk **zarządzanie regułami** w lewym górnym rogu okna.

    ![ Zarządzaj regułami alertów](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    Zostanie wyświetlona lista dostępnych zasad.

2. Wyszukaj regułę dziennika aktywności w celu zmodyfikowania.

    ![ Szukaj reguł alertów dziennika aktywności](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    Możesz użyć filtrów dostępne - **subskrypcji**, **grupy zasobów**, lub **zasobów** można znaleźć zasady działania, które chcesz edytować.

    > [!NOTE]

    > Można edytować tylko **opis** , **Target kryteria** i **grupy akcji**.

3.  Wybierz regułę, a następnie kliknij dwukrotnie, aby edytować opcje reguły. Wprowadź wymagane zmiany, a następnie kliknij przycisk **zapisać**.

    ![ Zarządzaj regułami alertów](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  Można wyłączyć, włączyć lub usunąć regułę. Wybierz odpowiednią opcję w górnej części okna, po wybraniu reguły zgodnie z opisem w kroku 2.


## <a name="next-steps"></a>Kolejne kroki

- [Archiwum alertów dotyczących działań w Dzienniku](monitoring-archive-activity-log.md)
- [Dzienniki aktywności strumienia do usługi Event hubs](monitoring-stream-activity-logs-event-hubs.md)
- [Migrowanie do Dzienniki aktywności](monitoring-migrate-management-alerts.md)
