---
title: Twórz i wyświetlaj alerty metryk i zarządzaj nimi za pomocą Azure Monitor
description: Dowiedz się, jak używać Azure Portal lub interfejsu wiersza polecenia do tworzenia i wyświetlania reguł alertów metryk oraz zarządzania nimi.
author: harelbr
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: harelbr
ms.subservice: alerts
ms.openlocfilehash: 00f5f37591ed2ed250cb756c686ea15136921512
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705534"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Twórz i wyświetlaj alerty metryk i zarządzaj nimi za pomocą Azure Monitor

Alerty dotyczące metryk w usłudze Azure Monitor umożliwiają otrzymywanie powiadomień, gdy jedna z metryk przekroczy wartość progową. Alerty dotyczące metryk działają na wielu metrykach wielowymiarowych platform, metrykach niestandardowych i standardowych i niestandardowych metrykach usługi Application Insights. W tym artykule opisano, jak tworzyć i wyświetlać reguły alertów dotyczących metryk oraz nimi zarządzać w witrynie Azure Portal oraz za pomocą interfejsu wiersza polecenia platformy Azure. Reguły alertów dotyczących metryk można również utworzyć za pomocą szablonów Azure Resource Manager, które opisano w [osobnym artykule](alerts-metric-create-templates.md).

Aby dowiedzieć się więcej o tym, jak alerty metryk działają na podstawie [alertów dotyczących metryk](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Utwórz za pomocą Azure Portal

Poniższa procedura opisuje sposób tworzenia reguły alertu dotyczącego metryki w Azure Portal:

1. W [Azure Portal](https://portal.azure.com)kliknij pozycję **Monitoruj**. Blok monitor konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

2. Kliknij pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertu**.

    > [!TIP]
    > Większość bloków zasobów zawiera również **alerty** w menu zasobów w obszarze **monitorowanie**, ale można również utworzyć alerty.

3. Kliknij pozycję **Wybierz element docelowy**, w okienku kontekstu, który ładuje, wybierz zasób docelowy, na którym chcesz utworzyć alert. Użyj listy rozwijanej Typ **subskrypcji** i **zasobu** , aby znaleźć zasób, który chcesz monitorować. Możesz również użyć paska wyszukiwania, aby znaleźć zasób.

4. Jeśli wybrany zasób ma metryki, na których można tworzyć alerty, **dostępne sygnały** z prawej strony będą zawierać metryki. W tym [artykule](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)można wyświetlić pełną listę typów zasobów, które są obsługiwane w przypadku alertów dotyczących metryk.

5. Po wybraniu zasobu docelowego kliknij pozycję **Dodaj warunek**.

6. Zostanie wyświetlona lista sygnałów obsługiwanych dla zasobu, wybierz metrykę, dla której chcesz utworzyć alert.

7. Opcjonalnie możesz ograniczyć metrykę przez dostosowanie **okresu** i **agregacji**. Jeśli Metryka ma wymiary, zostanie wyświetlona tabela **wymiarów** . Wybierz co najmniej jedną wartość na wymiar. Zostanie uruchomiony alert dotyczący metryki dla wszystkich wybranych kombinacji wartości. [Dowiedz się więcej o tym, jak działa alert dotyczący metryk wielowymiarowych](alerts-metric-overview.md). Możesz również **wybrać \*** dla dowolnego z wymiarów. **Wybierz \*** będzie dynamicznie skalować zaznaczenie do wszystkich bieżących i przyszłych wartości wymiaru.

8. Zostanie wyświetlony wykres metryki dla ostatnich 6 godzin. Zdefiniuj parametry alertu; **Typ warunku**, **częstotliwość**, **operator** i **próg** lub **czułość**, spowoduje to określenie logiki, która będzie Szacowana przez regułę alertu metryki. [Dowiedz się więcej o opcjach typu i czułości wartości progów dynamicznych](alerts-dynamic-thresholds.md).

9. Jeśli jest używany próg statyczny, wykres metryk może pomóc w ustaleniu, co może być rozsądnym progiem. Jeśli używasz progów dynamicznych, wykres metryki będzie wyświetlał obliczone progi na podstawie ostatnich danych.

10. Kliknij przycisk **Gotowe**

11. Opcjonalnie dodaj kolejne kryteria, jeśli chcesz monitorować złożoną regułę alertów. Obecnie użytkownicy mogą mieć reguły alertów z kryteriami progów dynamicznych jako pojedyncze kryterium.

12. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis** i **ważność** **reguły alertu**

13. Dodaj grupę akcji do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

14. Kliknij przycisk **gotowe** , aby zapisać regułę alertu metryki.

> [!NOTE]
> Reguły alertów metryk utworzonych za pomocą portalu są tworzone w tej samej grupie zasobów co zasób docelowy.

## <a name="view-and-manage-with-azure-portal"></a>Wyświetl Azure Portal i zarządzaj nimi

Reguły alertów dotyczących metryk można wyświetlać i zarządzać nimi za pomocą bloku zarządzanie regułami w obszarze alerty. W poniższej procedurze przedstawiono sposób wyświetlania reguł alertów dotyczących metryk i edytowania jednego z nich.

1. W Azure Portal przejdź do **monitorowania**

2. Kliknij pozycję **alerty** i **Zarządzaj regułami**

3. W bloku **Zarządzanie regułami** można wyświetlić wszystkie reguły alertów w różnych subskrypcjach. Można dodatkowo filtrować reguły przy użyciu opcji **Grupa zasobów**, **Typ zasobu** i **zasób**. Jeśli chcesz zobaczyć tylko alerty metryk, wybierz pozycję **Typ sygnału** jako metryki.

    > [!TIP]
    > W bloku **Zarządzanie regułami** możesz wybrać wiele reguł alertów i włączyć je/wyłączyć. Może to być przydatne w przypadku, gdy pewne zasoby docelowe muszą zostać umieszczone w trybie konserwacji

4. Kliknij nazwę reguły alertu metryki, którą chcesz edytować.

5. W obszarze Edytuj regułę kliknij **kryteria alertu** , które chcesz edytować. W razie potrzeby można zmienić metrykę, warunek progowy i inne pola

    > [!NOTE]
    > Po utworzeniu alertu dotyczącego metryki nie można edytować **zasobu docelowego** i **nazwy reguły alertu** .

6. Kliknij przycisk **gotowe** , aby zapisać zmiany.

## <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure

Poprzednie sekcje opisują sposób tworzenia, wyświetlania i zarządzania regułami alertów dotyczących metryk przy użyciu Azure Portal. W tej sekcji opisano, jak wykonać to samo przy użyciu wieloplatformowego [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). Najszybszym sposobem na rozpoczęcie korzystania z interfejsu wiersza polecenia platformy Azure jest [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). W tym artykule będziemy używać usługi Cloud Shell.

1. Przejdź do Azure Portal, kliknij pozycję **Cloud Shell**.

2. W wierszu polecenia można użyć poleceń z opcją ``--help``, aby dowiedzieć się więcej o poleceniu i sposobach ich użycia. Na przykład następujące polecenie pokazuje listę poleceń dostępnych do tworzenia i wyświetlania alertów metryk i zarządzania nimi

    ```azurecli
    az monitor metrics alert --help
    ```

3. Można utworzyć prostą regułę alertu metryki, która monitoruje, czy średni procent procesora CPU na maszynie wirtualnej jest większy niż 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Wszystkie alerty metryk w grupie zasobów można wyświetlić za pomocą następującego polecenia

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Szczegóły konkretnej reguły alertu metryki można zobaczyć przy użyciu nazwy lub identyfikatora zasobu reguły.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Regułę alertu metryki można wyłączyć za pomocą następującego polecenia.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Regułę alertu metryki można usunąć za pomocą następującego polecenia.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Następne kroki

- [Tworzenie alertów metryk przy użyciu szablonów Azure Resource Manager](../../azure-monitor/platform/alerts-enable-template.md).
- [Informacje o działaniu alertów dotyczących metryk](alerts-metric-overview.md).
- [Informacje o tym, jak działają Alerty metryczne z warunkiem progów dynamicznych](alerts-dynamic-thresholds.md).
- [Informacje o schemacie elementu webhook dla alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

