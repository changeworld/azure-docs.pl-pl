---
title: Tworzenie, wyświetlanie i zarządzanie alertami metryk za pomocą usługi Azure Monitor
description: Dowiedz się, jak używać witryny Azure portal lub interfejsu wiersza polecenia do tworzenia, wyświetlania i zarządzania regułami alertów metryk.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 03/13/2020
ms.subservice: alerts
ms.openlocfilehash: cefccd08ea66638f08f00e280fe2704444a7f916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369390"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi w usłudze Azure Monitor

Alerty metryki w usłudze Azure Monitor umożliwiają uzyskanie powiadomienia, gdy jedna z metryk przekroczy próg. Alerty dotyczące metryk działają na wielu metrykach wielowymiarowych platform, metrykach niestandardowych i standardowych i niestandardowych metrykach usługi Application Insights. W tym artykule opisano, jak tworzyć, wyświetlać i zarządzać regułami alertów metryk za pośrednictwem witryny Azure portal i interfejsu wiersza polecenia platformy Azure. Można również utworzyć reguły alertów metryk przy użyciu szablonów usługi Azure Resource Manager, które są opisane w [osobnym artykule](alerts-metric-create-templates.md).

Więcej informacji o tym, jak działają alerty metryk, można dowiedzieć się więcej o tym, jak działają alerty [metryk, z przeglądu alertów metryk.](alerts-metric-overview.md)

## <a name="create-with-azure-portal"></a>Tworzenie przy użyciu witryny Azure Portal

W poniższej procedurze opisano sposób tworzenia reguły alertów metryki w witrynie Azure Portal:

1. W [witrynie Azure portal](https://portal.azure.com)kliknij **monitor**. Blok Monitor konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

2. Kliknij **pozycję Alerty,** a następnie kliknij przycisk **+ Nowa reguła alertu**.

    > [!TIP]
    > Większość bloków zasobów ma również **alerty** w menu zasobów w obszarze **Monitorowanie**, można utworzyć alerty stamtąd, jak również.

3. Kliknij **pozycję Wybierz miejsce docelowe**, w okienku kontekstu, które się ładuje, wybierz zasób docelowy, na który chcesz powiadomić. Użyj listy rozwijanej **typu subskrypcja** i **typ zasobu,** aby znaleźć zasób, który chcesz monitorować. Możesz również użyć paska wyszukiwania, aby znaleźć swój zasób.

4. Jeśli wybrany zasób ma metryki, na których można tworzyć alerty, **dostępne sygnały** w prawym dolnym rogu będą zawierać metryki. Pełną listę typów zasobów obsługiwanych dla alertów metryk można wyświetlić w tym [artykule](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Po wybraniu zasobu docelowego kliknij przycisk **Dodaj warunek**.

6. Zostanie wyświetlona lista sygnałów obsługiwanych dla zasobu, wybierz metrykę, na której chcesz utworzyć alert.

7. Zostanie wyświetlony wykres dla danych z ostatnich sześciu godzin. Użyj listy rozwijanej **Okres wykresu,** aby wyświetlić dłuższą historię metryki.

8. Jeśli metryka ma wymiary, zostanie wyświetlona tabela wymiarów. Wybierz jedną lub więcej wartości na wymiar.
    - Wyświetlane wartości wymiarów są oparte na danych metryki z ostatnich trzech dni.
    - Jeśli szukana wartość wymiaru nie jest wyświetlana, kliknij "+", aby dodać wartość niestandardową.
    - Można również ** \* wybrać** dla dowolnego wymiaru. **Select \* ** będzie dynamicznie skalować zaznaczenie do wszystkich bieżących i przyszłych wartości dla wymiaru.

    Reguła alertu metryki oceni warunek dla wszystkich kombinacji wybranych wartości. [Dowiedz się więcej o tym, jak działa alerty dotyczące metryk wielowymiarowych](alerts-metric-overview.md).

9. Wybierz typ **próg,** **operator**i **typ agregacji**. Spowoduje to określenie logiki, że reguła alertu metryki oceni.
    - Jeśli używasz progu **statycznego,** kontynuuj definiowanie **wartości progu**. Wykres metryczny może pomóc określić, co może być rozsądny próg.
    - Jeśli używasz progu **dynamicznego,** kontynuuj definiowanie **czułości progu**. Wykres metryczny wyświetli obliczone progi na podstawie ostatnich danych. [Dowiedz się więcej o typie warunku Progi dynamiczne i opcjach czułości](alerts-dynamic-thresholds.md).

10. Opcjonalnie uściślaj stan, dostosowując **szczegółowość agregacji** i **częstotliwość oceny**. 

11. Kliknij przycisk **Gotowe**.

12. Opcjonalnie należy dodać kolejne kryteria, jeśli chcesz monitorować złożoną regułę alertu. Obecnie użytkownicy mogą mieć reguły alertów z kryteriami progów dynamicznych jako pojedynczym kryterium.

13. Wypełnij **szczegóły alertu,** takie jak **nazwa reguły alertu,** **Opis**i **ważność**.

14. Dodaj grupę akcji do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

15. Kliknij **przycisk Gotowe,** aby zapisać regułę alertu metryki.

> [!NOTE]
> Reguły alertów metryk utworzonych za pośrednictwem portalu są tworzone w tej samej grupie zasobów co zasób docelowy.

## <a name="view-and-manage-with-azure-portal"></a>Wyświetlanie portalu azure i zarządzanie nim

Reguły alertów metryki można wyświetlać i zarządzać nimi przy użyciu bloku Zarządzaj regułami w obszarze Alerty. Poniższa procedura pokazuje, jak wyświetlić reguły alertów metryki i edytować jedną z nich.

1. W witrynie Azure portal przejdź do **monitora**

2. Kliknij **alerty** i **zarządzaj regułami**

3. W **bloku Zarządzanie regułami** można wyświetlić wszystkie reguły alertów w ramach subskrypcji. Reguły można dalej filtrować za pomocą **grupy zasobów,** **typu zasobu**i **zasobu**. Jeśli chcesz wyświetlić tylko alerty metryki, wybierz **typ sygnału** jako metryki.

    > [!TIP]
    > W bloku **Zarządzanie regułami** można wybrać wiele reguł alertów i włączyć/wyłączyć je. Może to być przydatne, gdy niektóre zasoby docelowe muszą być objęte konserwacją

4. Kliknij nazwę reguły alertu metryki, którą chcesz edytować

5. W regule edycji kliknij **kryteria alertu,** które chcesz edytować. W razie potrzeby można zmienić dane, warunek progowy i inne pola

    > [!NOTE]
    > Po utworzeniu alertu metryki nie można edytować **zasobu docelowego** i **nazwy reguły alertu.**

6. Kliknij **przycisk Gotowe,** aby zapisać zmiany.

## <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure

W poprzednich sekcjach opisano sposób tworzenia, wyświetlania i zarządzania regułami alertów metryk przy użyciu witryny Azure Portal. W tej sekcji opisano, jak zrobić to samo przy użyciu wieloplatformowego [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) Najszybszym sposobem rozpoczęcia korzystania z interfejsu wiersza polecenia platformy Azure jest [usługa Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) W tym artykule użyjemy Cloud Shell.

1. Przejdź do witryny Azure portal, kliknij **cloud shell**.

2. W wierszu można użyć poleceń z ``--help`` opcją, aby dowiedzieć się więcej o poleceniu i jak z niego korzystać. Na przykład następujące polecenie pokazuje listę poleceń dostępnych do tworzenia, przeglądania i zarządzania alertami metryk

    ```azurecli
    az monitor metrics alert --help
    ```

3. Można utworzyć prostą regułę alertu metryki, która monitoruje, czy średni procent procesora CPU na maszynie wirtualnej jest większy niż 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Można wyświetlić wszystkie alerty metryki w grupie zasobów za pomocą następującego polecenia

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Możesz zobaczyć szczegóły określonej reguły alertu metryki przy użyciu nazwy lub identyfikatora zasobu reguły.

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

- [Tworzenie alertów metryk przy użyciu szablonów usługi Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md).
- [Dowiedz się, jak działają alerty metryk](alerts-metric-overview.md).
- [Dowiedz się, jak działają alerty metryk z warunkiem Progi dynamiczne](alerts-dynamic-thresholds.md).
- [Opis schematu haka sieci Web dla alertów metryk](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

