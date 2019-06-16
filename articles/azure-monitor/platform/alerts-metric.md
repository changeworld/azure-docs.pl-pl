---
title: Tworzenie, wyświetlanie i zarządzanie metryki alertów za pomocą usługi Azure Monitor
description: Dowiedz się, jak za pomocą witryny Azure portal lub interfejsu wiersza polecenia Tworzenie, wyświetlanie i zarządzanie nimi reguły alertów metryk.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 117b65265c853194e93a97fe5e2b2dcc6e9f5bc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60712993"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Tworzenie, wyświetlanie i zarządzanie przy użyciu usługi Azure Monitor alertów dotyczących metryk

Alerty metryk w usłudze Azure Monitor zapewnia sposób otrzymywania powiadomień, gdy jeden metryki przekroczą próg. Alerty metryki pracy na szeroką gamę metryk wielowymiarowych platformy, metryki niestandardowe, standard usługi Application Insights i metryki niestandardowe. W tym artykule opisujemy sposób tworzyć, wyświetlać i zarządzać reguł alertów dotyczących metryk za pośrednictwem witryny Azure portal i interfejs wiersza polecenia platformy Azure. Można również utworzyć reguł alertów dotyczących metryk za pomocą szablonów usługi Azure Resource Manager, które jest opisane w [oddzielny artykuł](alerts-metric-create-templates.md).

Dowiedz się więcej o jak metryki alerty działają z [Przegląd alertów metryk](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Tworzenie za pomocą witryny Azure portal

Poniższa procedura opisuje sposób tworzenia regułę alertu metryki w witrynie Azure portal:

1. W [witryny Azure portal](https://portal.azure.com), kliknij pozycję **Monitor**. W bloku Monitor konsoliduje wszystkie ustawienia monitorowania i danych w jednym widoku.

2. Kliknij przycisk **alerty** kliknięcie **+ Nowa reguła alertu**.

    > [!TIP]
    > Większość bloków zasobów również ma **alerty** w ich menu zasobów w ramach **monitorowanie**, alerty można utworzyć, także z niego.

3. Kliknij przycisk **Wybieranie lokalizacji docelowej**, w okienku kontekstowym, który ładuje, zaznacz zasób docelowy, który ma zostać wyświetlony alert. Użyj **subskrypcji** i **typ zasobu** list rozwijanych można znaleźć zasobu, który chcesz monitorować. Umożliwia także na pasku wyszukiwania można znaleźć zasobu.

4. Jeśli wybrany zasób ma metryk, można tworzyć alerty **dostępne sygnały** na dole po prawej stronie będzie zawierać metryki. Można wyświetlić pełną listę typów zasobów, obsługiwane w przypadku alertów dotyczących metryk w tym [artykułu](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Po wybraniu zasobu docelowego kliknij **Dodaj warunek**.

6. Zostanie wyświetlona lista sygnałów obsługiwana dla zasobu, wybierz metrykę, aby utworzyć alert na.

7. Opcjonalnie można dostosować metryki, dostosowując **okres** i **agregacji**. Jeśli Metryka posiada wymiarów, zostaną wyświetlone **wymiary** przedstawionej tabeli. Wybierz co najmniej jednej wartości na wymiar. Zostanie uruchomiony alert dotyczący metryki oceny warunku dla wszystkich kombinacjach wartości wybrane. [Dowiedz się więcej o tym, jak działa alertów dotyczących metryk wielowymiarowych](alerts-metric-overview.md). Możesz również **wybierz \***  na dowolnym z wymiarów. **Wybierz \***  będzie dynamicznie skalowania wyboru, aby wszystkie bieżące i przyszłe wartości wymiaru.

8. Zostanie wyświetlony wykres metryki przez ostatnie 6 godzin. Zdefiniuj parametry alertu; **Typ warunku**, **częstotliwość**, **Operator** i **próg** lub **czułości**, spowoduje Określ logiki, które będą oceniać regułę alertu metryki. [Dowiedz się więcej o opcjach Typ i ważność warunek dynamicznymi progami](alerts-dynamic-thresholds.md).

9. Jeśli używasz statycznych próg wykresu metryki może pomóc określić, co może być uzasadnione wartość progową. Jeśli używasz dynamicznymi progami wykresu metryki będą wyświetlane progi obliczony na podstawie ostatnich danych.

10. Kliknij przycisk **Gotowe**

11. Opcjonalnie Dodaj kryteria inny, jeśli chcesz monitorować złożone reguły alertu. Obecnie użytkownicy mogą mieć reguły alertów z kryteriami dynamicznymi progami jako jedno kryterium.

12. Wypełnij **szczegóły alertu** takich jak **Nazwa reguły alertu**, **opis** i **ważność**

13. Dodawanie grupy akcji alertu, przez wybranie istniejącej grupy akcji lub tworzenia nowej grupy akcji.

14. Kliknij przycisk **gotowe** można zapisać regułę alertu metryki.

> [!NOTE]
> Alert dotyczący metryki, zasady utworzone za pośrednictwem portalu są tworzone w tej samej grupie zasobów co zasób docelowy.

## <a name="view-and-manage-with-azure-portal"></a>Wyświetlanie i zarządzanie nimi za pomocą witryny Azure portal

Można wyświetlać i zarządzać reguł alertów dotyczących metryk za pomocą bloku zasady zarządzania, w sekcji alerty. Poniższa procedura pokazuje, jak wyświetlać swoje reguł alertów dotyczących metryk i edytować jeden z nich.

1. W witrynie Azure portal, przejdź do **monitora**

2. Kliknij pozycję **alerty** i **zarządzanie regułami**

3. W **zarządzanie regułami** bloku można wyświetlić wszystkie reguły alertów w subskrypcjach. Można dalej filtrować reguł za pomocą **grupy zasobów**, **typ zasobu** i **zasobów**. Jeśli chcesz zobaczyć tylko alertów dotyczących metryk, wybierz opcję **sygnał typu** jako metryki.

    > [!TIP]
    > W **zarządzanie regułami** bloku możesz wybrać wiele reguł alertów i Włącz/wyłącz je. Może to być przydatne, gdy niektóre zasoby docelowe muszą znajdować się w trakcie konserwacji

4. Kliknij nazwę regułę alertu metryki, które chcesz edytować

5. Edytuj regułę, kliknij przycisk na **kryteria alertu** chcesz edytować. Możesz zmienić metrykę, warunkiem progu oraz inne pola, zgodnie z potrzebami

    > [!NOTE]
    > Nie można edytować **zasób docelowy** i **Nazwa reguły alertu** po utworzeniu alertu metryki.

6. Kliknij przycisk **gotowe** Aby zapisać zmiany.

## <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure

Przedstawione w poprzednich sekcjach opisano, jak tworzyć, wyświetlać i zarządzać reguł alertów dotyczących metryk za pomocą witryny Azure portal. W tej sekcji opisano sposób zrobić to samo za pomocą wielu platform [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). To najszybszy sposób rozpocząć korzystanie z wiersza polecenia platformy Azure za pośrednictwem [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). W tym artykule używamy usługi Cloud shell.

1. Przejdź do witryny Azure portal, kliknij pozycję **Cloud shell**.

2. W wierszu polecenia można użyć poleceń z ``--help`` opcję, aby dowiedzieć się więcej na temat polecenia i jak z niej korzystać. Na przykład następujące polecenie pokazuje listę poleceń dostępnych do tworzenia, przeglądania i zarządzania alertów dotyczących metryk

    ```azurecli
    az monitor metrics alert --help
    ```

3. Można utworzyć prostą regułę alertu metryki monitorującego, jeśli średnia procentowe użycie procesora CPU na maszynie Wirtualnej jest większa niż 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. W grupie zasobów przy użyciu następującego polecenia można wyświetlić wszystkie alerty metryki

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Możesz zobaczyć szczegółowe informacje o określonym reguła alertu metryki za pomocą nazwy lub Identyfikatora zasobu reguły.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Można wyłączyć regułę alertu metryki za pomocą następującego polecenia.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Możesz usunąć regułę alertu metryki za pomocą następującego polecenia.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie alertów metryk, przy użyciu szablonów usługi Azure Resource Manager](../../azure-monitor/platform/alerts-enable-template.md).
- [Zrozumienie, jak metryki, alerty pracy](alerts-metric-overview.md).
- [Zrozumienie, jak metryki alerty z dynamicznymi progami warunek pracy](alerts-dynamic-thresholds.md).
- [Zrozumienie schematu punktu zaczepienia sieci web dla alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

