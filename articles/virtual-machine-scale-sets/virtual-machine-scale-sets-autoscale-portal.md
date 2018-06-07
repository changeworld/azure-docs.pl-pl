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
ms.date: 05/29/2018
ms.author: iainfou
ms.openlocfilehash: c9386f7dd0ba390a5f089be058c7f3edd6e33cf9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652376"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatycznie skalować skali maszyny wirtualnej w portalu Azure
Podczas tworzenia zestawu skalowania musisz zdefiniować liczbę wystąpień maszyn wirtualnych, które chcesz uruchamiać. W odpowiedzi na zmieniające się zapotrzebowanie aplikacji możesz automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych. Skalowanie automatyczne pozwala spełniać potrzeby klientów lub reagować na zmiany wydajności aplikacji w całym cyklu jej życia.

W tym artykule przedstawiono sposób tworzenia reguł automatycznego skalowania w portalu Azure, która monitorowania wydajności wystąpień maszyny Wirtualnej w zestawie skali. Te reguły automatycznego skalowania zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych w odpowiedzi na te metryki wydajności. Można również wykonać te kroki [programu Azure PowerShell](tutorial-autoscale-powershell.md) lub [Azure CLI 2.0](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć zasady automatycznego skalowania, należy istniejącej maszyny wirtualnej zestawu skali. Możesz utworzyć zestaw o skali [portalu Azure](quick-create-portal.md), [programu Azure PowerShell](quick-create-powershell.md), lub [Azure CLI 2.0](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Tworzenie reguły automatycznie skalować w poziomie
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawie skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Możesz określić, jakie metryki mają być monitorowane, na przykład procesor CPU lub dysk, oraz jak długo obciążenie aplikacji musi przekraczać wartość progową, a także ile wystąpień maszyn wirtualnych ma zostać dodanych do zestawu skalowania.

1. Otwórz Azure portalu i wybierz pozycję **grup zasobów** z menu po lewej stronie pulpitu nawigacyjnego.
2. Wybierz grupę zasobów, która zawiera zestaw skalowania, a następnie wybierz Twoje zestaw skalowania z listy zasobów.
3. Wybierz **skalowanie** z menu po lewej stronie skali Ustaw okna. Kliknij przycisk, aby **Włączanie automatycznego skalowania**:

    ![Włączanie automatycznego skalowania w portalu Azure](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Wprowadź nazwę dla ustawienia, takie jak *skalowania automatycznego*, następnie wybierz opcję **dodać regułę**.

5. Ta funkcja pozwala utworzyć regułę, która zwiększa się liczba wystąpień maszyn wirtualnych w skali ustawić, gdy średni obciążenie procesora CPU jest większa niż 70% w okresie 10 minut. Gdy zasada wyzwala, 20% zwiększa się liczba wystąpień maszyn wirtualnych. W zestawach skali z mniejszą liczbą wystąpień maszyn wirtualnych, można ustawić **operacji** do *zwiększyć liczbę przez* , a następnie określ *1* lub *2* dla *wystąpienia licznika*. W zestawach skali z dużą liczbą wystąpień maszyn wirtualnych, zwiększenie 10% lub 20% wystąpień maszyn wirtualnych może być bardziej odpowiednie.

    Określ następujące ustawienia dla reguły:
    
    | Parametr              | Wyjaśnienie                                                                                                         | Wartość          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregacja czasu*     | Określa sposób agregacji metryk zebranych do celów analizy.                                                | Średnia        |
    | *Nazwa metryki*          | Metryka wydajności, która jest monitorowana i na której są stosowane akcje zestawu skalowania.                                                   | Procentowe użycie procesora CPU |
    | *Statystyka ziarno czasu* | Określa, jak metryki zbierane w poszczególnych ziarno czasu powinien agregowane dla celów analizy.                             | Średnia        |
    | *Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                     | Więcej niż   |
    | *Próg*            | Wartość procentowa powoduje, że reguły automatycznego skalowania akcja wyzwalacza.                                                 | 70             |
    | *Czas trwania*             | Przedział czasu monitorowania, po którym wartość metryki jest porównywana z wartością progową.                                   | 10 minut     |
    | *Operacja*            | Określa, czy zestaw skalowania powinny być skalowane w górę lub w dół, gdy ta reguła ma zastosowanie i przez jaki inkrementacji                        | Zwiększ wartość procentową o |
    | *Liczba wystąpień*       | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                            | 20             |
    | *Cool w dół (w minutach)*  | Przedział czasu przed ponownym zastosowaniem reguły, który gwarantuje, że akcje skalowania automatycznego zaczną obowiązywać. | 5 minut      |

    W poniższych przykładach pokazano reguły utworzone w portalu Azure, odpowiadający te ustawienia:

    ![Utwórz regułę skalowania automatycznego, aby zwiększyć liczbę wystąpień maszyn wirtualnych](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Aby utworzyć regułę, wybierz **Dodaj**


## <a name="create-a-rule-to-automatically-scale-in"></a>Tworzenie reguły automatycznie skalować w
Wieczorami lub w weekendy zapotrzebowanie aplikacji może być mniejsze. Jeśli spadek obciążenia ma cechy stałego trendu w danym okresie, można skonfigurować reguły skalowania automatycznego umożliwiające zmniejszenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Akcja skalowania w pionie ogranicza koszt używania zestawu skalowania, ponieważ jest uruchomionych tylko tyle wystąpień, ile jest wymaganych do zaspokojenia bieżącego zapotrzebowania.

1. Wybierz **dodać regułę** ponownie.
2. Utworzyć regułę, która zmniejsza liczbę wystąpień maszyn wirtualnych w skali ustawić, gdy średni obciążenie procesora CPU następnie spadnie poniżej 30% w okresie 10 minut. Gdy zasada wyzwala, 20% zmniejsza się liczba wystąpień maszyn wirtualnych.

    Podobnie jak w przypadku poprzednich regułę należy zastosować te same podejście. Dostosuj następujące ustawienia dla reguły:
    
    | Parametr              | Wyjaśnienie                                                                                                          | Wartość          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                      | Mniej niż   |
    | *Próg*            | Wartość procentowa powoduje, że reguły automatycznego skalowania akcja wyzwalacza.                                                 | 30             |
    | *Operacja*            | Określa, czy zestaw skalowania powinny być skalowane w górę lub w dół, gdy ta reguła ma zastosowanie i przez jaki inkrementacji                         | Zmniejsz wartość procentową o |
    | *Liczba wystąpień*       | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                             | 20             |

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
