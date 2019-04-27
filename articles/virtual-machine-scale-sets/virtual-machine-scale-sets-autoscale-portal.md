---
title: Zestawy skalowania maszyn wirtualnych skalowania automatycznego w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Tworzenie reguły skalowania automatycznego skalowania maszyn wirtualnych ustawia się w witrynie Azure portal
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 648bc0295cd5435e9c3e44f33b7ae80522fa8e0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618882"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatyczne skalowanie maszyn wirtualnych zestawu skalowania w witrynie Azure portal
Podczas tworzenia zestawu skalowania musisz zdefiniować liczbę wystąpień maszyn wirtualnych, które chcesz uruchamiać. W odpowiedzi na zmieniające się zapotrzebowanie aplikacji możesz automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych. Skalowanie automatyczne pozwala spełniać potrzeby klientów lub reagować na zmiany wydajności aplikacji w całym cyklu jej życia.

W tym artykule przedstawiono sposób tworzenia reguły automatycznego skalowania w portalu Azure, monitorowanie wydajności wystąpień maszyn wirtualnych w zestawie skalowania. Te reguły skalowania automatycznego zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych w odpowiedzi na te metryki wydajności. Można również wykonać te czynności przy użyciu [programu Azure PowerShell](tutorial-autoscale-powershell.md) lub [wiersza polecenia platformy Azure](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć reguły skalowania automatycznego, należy istniejącej maszyny wirtualnej zestawu skalowania. Można utworzyć zestaw skalowania z [witryny Azure portal](quick-create-portal.md), [programu Azure PowerShell](quick-create-powershell.md), lub [wiersza polecenia platformy Azure](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Tworzenie reguły do automatycznego dowolnego skalowania w poziomie
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawie skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Możesz określić, jakie metryki mają być monitorowane, na przykład procesor CPU lub dysk, oraz jak długo obciążenie aplikacji musi przekraczać wartość progową, a także ile wystąpień maszyn wirtualnych ma zostać dodanych do zestawu skalowania.

1. Otwórz w witrynie Azure portal i wybierz **grup zasobów** z menu po lewej stronie pulpitu nawigacyjnego.
2. Wybierz grupę zasobów, która zawiera zestaw skalowania, a następnie wybierz swój zestaw skalowania z listy zasobów.
3. Wybierz **skalowanie** z menu po lewej stronie skali ustawić okno. Wybierz przycisk, aby **włączyć Skalowanie automatyczne**:

    ![Włącz Skalowanie automatyczne w witrynie Azure portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Wprowadź nazwę dla ustawienia, takie jak *skalowania automatycznego*, następnie wybierz opcję, aby **Dodaj regułę**.

5. Utworzymy regułę, która zwiększa liczbę wystąpień maszyn wirtualnych w zestawie skalowania, jeśli średnie obciążenie procesora CPU jest większy niż 70% w okresie 10 minut. Wyzwolenie reguły liczbę wystąpień maszyn wirtualnych, zwiększa się o 20%. W zestawach skalowania z mniejszą liczbą wystąpień maszyn wirtualnych, można ustawić **operacji** do *Zwiększ liczbę o* , a następnie określ *1* lub *2* dla *liczba wystąpień*. W zestawach skalowania z dużą liczbę wystąpień maszyn wirtualnych, zwiększenia dotyczącego co najmniej 20% 10% wystąpień maszyn wirtualnych może być bardziej odpowiednie.

    Określ następujące ustawienia dla reguły:
    
    | Parametr              | Wyjaśnienie                                                                                                         | Wartość          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregacja czasu*     | Określa sposób agregacji metryk zebranych do celów analizy.                                                | Średnia        |
    | *Nazwa metryki*          | Metryka wydajności, która jest monitorowana i na której są stosowane akcje zestawu skalowania.                                                   | Procentowe użycie procesora CPU |
    | *Statystyka ziarna czasu* | Definiuje, jak powinna zostać zagregowana w każdym ziarno czasu agregacji metryk zebranych do analizy.                             | Średnia        |
    | *Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                     | Większe niż   |
    | *Próg*            | Wartość procentowa, która powoduje wyzwolenie akcji przez regułę skalowania automatycznego.                                                 | 70             |
    | *Czas trwania*             | Przedział czasu monitorowania, po którym wartość metryki jest porównywana z wartością progową.                                   | 10 minut     |
    | *Operacja*            | Określa, czy zestaw skalowania powinny być skalowane w górę lub w dół, gdy ta reguła ma zastosowanie i z jakich przyrostem                        | Zwiększ wartość procentową o |
    | *Liczba wystąpień*       | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                            | 20             |
    | *Czas ochładzania (minuty)*  | Przedział czasu przed ponownym zastosowaniem reguły, który gwarantuje, że akcje skalowania automatycznego zaczną obowiązywać. | 5 minut      |

    W poniższych przykładach pokazano reguły utworzone w witrynie Azure portal, który odpowiada tych ustawień:

    ![Tworzenie reguły skalowania automatycznego w celu zwiększenia liczby wystąpień maszyn wirtualnych](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Aby utworzyć regułę, wybierz **Dodaj**


## <a name="create-a-rule-to-automatically-scale-in"></a>Tworzenie reguły do automatycznego dowolnego skalowania w
Wieczorami lub w weekendy zapotrzebowanie aplikacji może być mniejsze. Jeśli spadek obciążenia ma cechy stałego trendu w danym okresie, można skonfigurować reguły skalowania automatycznego umożliwiające zmniejszenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Akcja skalowania w pionie ogranicza koszt używania zestawu skalowania, ponieważ jest uruchomionych tylko tyle wystąpień, ile jest wymaganych do zaspokojenia bieżącego zapotrzebowania.

1. Możliwość **Dodaj regułę** ponownie.
2. Utwórz regułę, która zmniejsza liczbę wystąpień maszyn wirtualnych w zestawie skalowania, jeśli średnie obciążenie procesora CPU spadnie poniżej 30% w okresie 10 minut. Wyzwolenie reguły o 20% zmniejsza się liczba wystąpień maszyn wirtualnych.

    Użyj tej samej metody, podobnie jak w poprzedniej regule. Dostosuj następujące ustawienia dla reguły:
    
    | Parametr              | Wyjaśnienie                                                                                                          | Wartość          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                      | Mniej niż   |
    | *Próg*            | Wartość procentowa, która powoduje wyzwolenie akcji przez regułę skalowania automatycznego.                                                 | 30             |
    | *Operacja*            | Określa, czy zestaw skalowania powinny być skalowane w górę lub w dół, gdy ta reguła ma zastosowanie i z jakich przyrostem                         | Zmniejsz wartość procentową o |
    | *Liczba wystąpień*       | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                             | 20             |

3. Aby utworzyć regułę, wybierz **Dodaj**


## <a name="define-autoscale-instance-limits"></a>Zdefiniuj limity wystąpień automatycznego skalowania
Twój profil skalowania automatycznego, zdefiniuj minimalnej, maksymalne i domyślną liczbę wystąpień maszyn wirtualnych. Po zastosowaniu reguły skalowania automatycznego, te limity wystąpień upewnij się, czy możesz skalować w poziomie przekracza maksymalną liczbę wystąpień lub skalowania poza minimum wystąpień.

1. Ustaw następujące limity wystąpień:

    | Minimalne | Maksimum | Domyślne|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Aby zastosować reguł automatycznego skalowania i limity wystąpień, wybierz **Zapisz**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorowania liczby wystąpień w zestawie skalowania
Aby wyświetlić liczbę i stan wystąpień maszyn wirtualnych, wybierz **wystąpień** z menu po lewej stronie skali ustawić okno. Stan wskazuje, czy wystąpienie maszyny Wirtualnej jest *tworzenie* skalowania ustawiane automatycznie skaluje lub jest *usuwanie* jako skalowania automatycznie skaluje w.

![Wyświetl listę wystąpień maszyn wirtualnych zestawu skalowania](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatyczne skalowanie na podstawie harmonogramu
Poprzednich przykładach automatycznie skalować zestaw skalowania wewnątrz lub na zewnątrz z hosta podstawowych metryk, takich jak użycie procesora CPU. Można również tworzyć reguły automatycznego skalowania, na podstawie zgodnie z harmonogramami. Te reguły na podstawie harmonogramu umożliwiają automatycznie skalować liczbę wystąpień maszyn wirtualnych w przód od przewidywanego wzrostu zapotrzebowania aplikacji, takich jak podstawowe godziny pracy, a następnie automatyczne skalowanie liczby wystąpień w czasie, który spodziewasz się mniej żądanie, takie jak weekend.

1. Wybierz **skalowanie** z menu po lewej stronie skali ustawić okno. Aby usunąć istniejące reguły skalowania automatycznego, utworzony w poprzednich przykładach, wybierz ikonę Kosza.

    ![Usuń istniejące reguły skalowania automatycznego](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Możliwość **Dodaj warunek skalowania**. Wybierz ikonę ołówka obok nazwy reguły, a następnie podaj nazwę, taką jak *skalowanie w poziomie w każdy dzień roboczy*.

    ![Zmień nazwę domyślną regułę skalowania automatycznego](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Wybierz przycisk radiowy, aby **Skaluj do określonej liczby wystąpień**.
4. Aby skalować liczbę wystąpień, wprowadź *10* jako liczbę wystąpień.
5. Wybierz **Powtarzaj w określone dni** dla **harmonogram** typu.
6. Wybierz wszystkie dni robocze, od poniedziałku do piątku.
7. Wybierz odpowiednie strefa czasowa, a następnie określ **czas rozpoczęcia** z *09:00*.
8. Możliwość **Dodaj warunek skalowania** ponownie. Powtórz te czynności, aby utworzyć harmonogram o nazwie *skalowanie w trakcie co wieczór* skaluje się do *3* wystąpienia, jest powtarzany w każdy dzień tygodnia i są prezentowane od momentu *18:00*.
9. Aby zastosować reguły na podstawie harmonogramu skalowania automatycznego, wybierz **Zapisz**.

    ![Tworzenie reguł skalowania automatycznego, które można skalować zgodnie z harmonogramem](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Aby zobaczyć, jak są stosowane reguły skalowania automatycznego, wybierz **historii uruchamiania** w górnej części **skalowanie** okna. Wykres i zdarzenia liście widać, gdy wyzwalacz reguły skalowania automatycznego i liczbę wystąpień maszyn wirtualnych w zestawie skalowania jest ustawiony wzrosty i spadki.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób reguły automatycznego skalowania umożliwia skalowanie w poziomie i zwiększyć lub zmniejszyć *numer* wystąpień maszyn wirtualnych w zestawie skalowania jest ustawiony. Możesz również skalować w pionie do zwiększania lub zmniejszania wystąpienia maszyny Wirtualnej *rozmiar*. Aby uzyskać więcej informacji, zobacz [pionowe skalowania automatycznego za pomocą zestawów skalowania maszyn wirtualnych](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Aby uzyskać informacje na temat zarządzania wystąpień maszyn wirtualnych, zobacz [zestawów skalowania maszyn wirtualnych zarządzania za pomocą programu Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Aby dowiedzieć się, jak generować alerty, gdy wyzwalacz reguł z automatycznego, zobacz [użyć akcji skalowania automatycznego, aby wysyłać wiadomości e-mail i elementy webhook powiadomienia o alertach w usłudze Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md). Możesz również [korzystaj z dzienników inspekcji do wysyłania wiadomości e-mail i elementy webhook powiadomień o alertach w usłudze Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
