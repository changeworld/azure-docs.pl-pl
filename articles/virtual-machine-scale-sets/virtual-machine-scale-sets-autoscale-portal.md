---
title: Zestawy skalowania automatycznego skalowania maszyny wirtualnej w portalu Azure | Dokumentacja firmy Microsoft
description: Ustawia sposobu tworzenia reguł skalowania automatycznego skalowania maszyny wirtualnej w portalu Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 984b16dae26fb6d9d33ef68ac3e8c8b658e82e08
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatycznie skalować skali maszyny wirtualnej w portalu Azure
Podczas tworzenia zestawu skalowania, należy zdefiniować liczba wystąpień maszyn wirtualnych, które chcesz uruchomić. Twoje żądanie aplikacji zmian, można automatycznie zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych. Możliwość skalowania automatycznego umożliwia nadąża z popyt lub reagowania na zmiany wydajności aplikacji w całym cyklu życia aplikacji.

W tym artykule przedstawiono sposób tworzenia reguł automatycznego skalowania w portalu Azure, która monitorowania wydajności wystąpień maszyny Wirtualnej w zestawie skali. Te reguły automatycznego skalowania zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych w odpowiedzi na te metryki wydajności. Można również wykonać te kroki [programu Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) lub [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md).


## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć zasady automatycznego skalowania, należy istniejącej maszyny wirtualnej zestawu skali. Możesz utworzyć zestaw o skali [portalu Azure](quick-create-portal.md), [programu Azure PowerShell](quick-create-powershell.md), lub [Azure CLI 2.0](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Tworzenie reguły automatycznie skalować w poziomie
Jeśli Twoje żądanie aplikacji zwiększa, obciążenie wystąpień maszyn wirtualnych w skali sieci ustawić zwiększa. Jeśli ta zwiększone obciążenie jest spójne, a nie tylko krótki żądanie, można skonfigurować reguł skalowania automatycznego, aby zwiększyć liczbę wystąpień maszyny Wirtualnej w zestawie skalowania. Gdy te wystąpień maszyn wirtualnych są tworzone i wdrożonych aplikacji, zestaw skali rozpoczyna Dystrybuuj ruch do nich za pośrednictwem usługi równoważenia obciążenia. Można określić, jakie metryk do monitorowania, takie jak procesor CPU lub dysku, jak długo obciążenia aplikacji muszą spełniać podany próg i ustaw liczbę wystąpień maszyny Wirtualnej, aby dodać do skali.

1. Otwórz Azure portalu i wybierz pozycję **grup zasobów** z menu po lewej stronie pulpitu nawigacyjnego.
2. Wybierz grupę zasobów, która zawiera zestaw skalowania, a następnie wybierz Twoje zestaw skalowania z listy zasobów.
3. Wybierz **skalowanie** z menu po lewej stronie skali Ustaw okna. Kliknij przycisk, aby **Włączanie automatycznego skalowania**:

    ![Włączanie automatycznego skalowania w portalu Azure](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Wprowadź nazwę dla ustawienia, takie jak *skalowania automatycznego*, następnie wybierz opcję **dodać regułę**.

5. Ta funkcja pozwala utworzyć regułę, która zwiększa się liczba wystąpień maszyn wirtualnych w skali ustawić, gdy średni obciążenie procesora CPU jest większa niż 70% w okresie 10 minut. Gdy zasada wyzwala, 20% zwiększa się liczba wystąpień maszyn wirtualnych. W zestawach skali z mniejszą liczbą wystąpień maszyn wirtualnych, można ustawić **operacji** do *zwiększyć liczbę przez* , a następnie określ *1* lub *2* dla *wystąpienia licznika*. W zestawach skali z dużą liczbą wystąpień maszyn wirtualnych, zwiększenie 10% lub 20% wystąpień maszyn wirtualnych może być bardziej odpowiednie.

    Określ następujące ustawienia dla reguły:
    
    | Parametr              | Wyjaśnienie                                                                                                         | Wartość          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregacja czasu*     | Określa, jak zebranych metryk powinny być agregowane dla celów analizy.                                                | Średnia        |
    | *Nazwa metryki*          | Metryki wydajności do monitorowania i zastosować skali ustawić akcje.                                                   | Procentowe użycie procesora CPU |
    | *Statystyka ziarno czasu* | Określa, jak metryki zbierane w poszczególnych ziarno czasu powinien agregowane dla celów analizy.                             | Średnia        |
    | *Operator*             | Operator użyty do porównania danych metryki wartość progową.                                                     | Więcej niż   |
    | *Próg*            | Wartość procentowa powoduje, że reguły automatycznego skalowania akcja wyzwalacza.                                                 | 70             |
    | *Czas trwania*             | Ilość czasu monitorowane przed wartości metryki i próg są porównywane.                                   | 10 minut     |
    | *Operacja*            | Określa, czy zestaw skalowania powinny być skalowane w górę lub w dół, gdy ta reguła ma zastosowanie i przez jaki inkrementacji                        | Zwiększ wartość procentową o |
    | *Liczba wystąpień*       | Gdy zasada wyzwala, należy zmienić wartości procentowej wystąpień maszyny Wirtualnej.                                            | 20             |
    | *Cool w dół (w minutach)*  | Ilość czasu oczekiwania przed reguła została zastosowana ponownie, aby akcji skalowania automatycznego ma czas zaczęły obowiązywać. | 5 minut      |

    W poniższych przykładach pokazano reguły utworzone w portalu Azure, odpowiadający te ustawienia:    

    ![Utwórz regułę skalowania automatycznego, aby zwiększyć liczbę wystąpień maszyn wirtualnych](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Aby utworzyć regułę, wybierz **Dodaj**


## <a name="create-a-rule-to-automatically-scale-in"></a>Tworzenie reguły automatycznie skalować w
Na wieczorem lub w weekendy Twoje żądanie aplikacji mogą się zmniejszyć. Jeśli to zmniejszyć obciążenie jest spójne w danym okresie czasu, można skonfigurować reguły automatycznego skalowania, aby zmniejszyć liczbę wystąpień maszyny Wirtualnej w zestawie skalowania. Ta akcja skali zmniejsza koszt do uruchomienia na skalę ustawione jako tylko Uruchom liczba wystąpień wymaganych do bieżącego zapotrzebowania.

1. Wybierz **dodać regułę** ponownie.
2. Utworzyć regułę, która zmniejsza liczbę wystąpień maszyn wirtualnych w skali ustawić, gdy średni obciążenie procesora CPU następnie spadnie poniżej 30% w okresie 10 minut. Gdy zasada wyzwala, 20% zmniejsza się liczba wystąpień maszyn wirtualnych.

    Podobnie jak w przypadku poprzednich regułę należy zastosować te same podejście. Dostosuj następujące ustawienia dla reguły:
    
    | Parametr              | Wyjaśnienie                                                                                                          | Wartość          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator użyty do porównania danych metryki wartość progową.                                                      | Mniej niż   |
    | *Próg*            | Wartość procentowa powoduje, że reguły automatycznego skalowania akcja wyzwalacza.                                                 | 30             |
    | *Operacja*            | Określa, czy zestaw skalowania powinny być skalowane w górę lub w dół, gdy ta reguła ma zastosowanie i przez jaki inkrementacji                         | Zmniejsz wartość procentową o |
    | *Liczba wystąpień*       | Gdy zasada wyzwala, należy zmienić wartości procentowej wystąpień maszyny Wirtualnej.                                             | 20             |

3. Aby utworzyć regułę, wybierz **Dodaj**


## <a name="define-autoscale-instance-limits"></a>Zdefiniuj ograniczenia wystąpień skalowania automatycznego
Twój profil skalowania automatycznego musi definiować minimum, maksimum i domyślna liczba wystąpień maszyn wirtualnych. Stosowania reguł skalowania automatycznego te ograniczenia wystąpień upewnij się, nie skalowanie w poziomie przekracza maksymalną liczbę wystąpień lub skali w poza minimum wystąpień.

1. Ustaw następujące ograniczenia wystąpień:

    | Minimalne | Maksimum | Domyślne|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Aby zastosować reguł skalowania automatycznego i limity wystąpień, wybierz **zapisać**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitor liczby wystąpień w zestawie skalowania
Aby wyświetlić liczbę i stan wystąpień maszyny Wirtualnej, wybierz **wystąpień** z menu po lewej stronie skali Ustaw okna. Stan wskazuje, czy wystąpienie maszyny Wirtualnej jest *tworzenie* automatycznie zestaw skalowania skaluje się lub jest *usunięcie* jako skali jest automatycznie skalowany w.

![Wyświetl listę wystąpień maszyny Wirtualnej zestawu skali](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Funkcja automatycznego skalowania zgodnie z harmonogramem
Poprzednich przykładach automatycznie przeskalowany skali ustawić przychodzący lub wychodzący z podstawowego hosta metryki, takie jak użycie procesora CPU. Można również tworzyć reguły automatycznego skalowania, oparte na harmonogramów. Te zasady oparte na harmonogramie umożliwiają automatyczne skalowanie liczby wystąpień maszyny Wirtualnej przed oczekiwanego wzrostu popytu aplikacji, takich jak podstawowe godziny pracy, a następnie automatycznie skalować liczbę wystąpień w czasie, która będzie mniej żądanie, takich jak weekend.

1. Wybierz **skalowanie** z menu po lewej stronie skali Ustaw okna. Aby usunąć istniejące zasady automatycznego skalowania utworzone w poprzednich przykładach, wybierz Kosz ikony.

    ![Usuń istniejące zasady automatycznego skalowania](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Wybierz **Dodaj warunek skali**. Wybierz ikonę ołówka obok nazwy reguły, a następnie podaj nazwę, taki jak *skalowanie podczas każdego dnia roboczego*.

    ![Zmień nazwę domyślną regułę automatycznego skalowania](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Wybierz przycisk radiowy, aby **skalowania liczby wystąpień określonego**.
4. Aby skalować liczbę wystąpień, wprowadź *10* jako liczba wystąpień.
5. Wybierz **Powtórz określone dni** dla **harmonogram** typu.
6. Wybierz wszystkie dni robocze, od poniedziałku do piątku.
7. Wybierz odpowiednie strefy czasowej, a następnie określ **godzina rozpoczęcia** z *09:00*.
8. Wybierz **Dodaj warunek skali** ponownie. Powtórz procedurę, aby utworzyć harmonogram o nazwie *skalować w trakcie wieczorem* skaluje się do *3* wystąpień, jest powtarzany co dzień tygodnia i rozpoczyna się od *18:00*.
9. Aby zastosować reguły oparte na harmonogramie skalowania automatycznego, wybierz **zapisać**.

    ![Utwórz zasady automatycznego skalowania, które zgodnie z harmonogramem](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Aby zobaczyć sposób stosowania reguł skalowania automatycznego, wybierz **Historia uruchomień** w górnej części **skalowanie** okna. Wykres i zdarzenia lista przedstawia, gdy liczba wystąpień maszyn wirtualnych w skali sieci i wyzwalaczy reguł skalowania automatycznego wartość zwiększa lub zmniejsza.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób skalowanie w poziomie i zwiększyć lub zmniejszyć za pomocą reguł skalowania automatycznego *numer* wystąpień maszyn wirtualnych w skali sieci ustawiony. Możliwe jest także skalowanie w pionie Aby zwiększyć lub zmniejszyć wystąpienia maszyny Wirtualnej *rozmiar*. Aby uzyskać więcej informacji, zobacz [pionowy automatycznie skalowana za pomocą zestawów skali maszyny wirtualnej](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Aby uzyskać informacje na temat zarządzania wystąpień maszyn wirtualnych, zobacz [ustawia Zarządzaj skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Aby dowiedzieć się, jak generować alerty, kiedy Twoje skalowania automatycznego zasady wyzwalacza, zobacz [użyć akcji skalowania automatycznego do wysyłania wiadomości e-mail i elementu webhook powiadomień o alertach w monitorze Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Możesz również [dzienników inspekcji używany do wysyłania wiadomości e-mail i elementu webhook powiadomień o alertach w monitorze Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
