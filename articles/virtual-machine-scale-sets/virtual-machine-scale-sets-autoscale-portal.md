---
title: Automatyczne skalowanie zestawów skalowania maszyny wirtualnej w witrynie Azure portal
description: Jak tworzyć reguły skalowania automatycznego dla zestawów skalowania maszyny wirtualnej w witrynie Azure portal
author: ju-shim
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: 1915b144aec5a5447504c70d18dbf420d255a08e
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010294"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatyczne skalowanie skalowania maszyny wirtualnej w witrynie Azure portal
Podczas tworzenia zestawu skalowania musisz zdefiniować liczbę wystąpień maszyn wirtualnych, które chcesz uruchamiać. W odpowiedzi na zmieniające się zapotrzebowanie aplikacji możesz automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych. Skalowanie automatyczne pozwala spełniać potrzeby klientów lub reagować na zmiany wydajności aplikacji w całym cyklu jej życia.

W tym artykule pokazano, jak utworzyć reguły skalowania automatycznego w witrynie Azure portal, które monitorują wydajność wystąpień maszyn wirtualnych w zestawie skalowania. Te reguły skalowania automatycznego zwiększają lub zmniejszają liczbę wystąpień maszyn wirtualnych w odpowiedzi na te metryki wydajności. Te kroki można również wykonać za pomocą [programu Azure PowerShell](tutorial-autoscale-powershell.md) lub [interfejsu wiersza polecenia platformy Azure.](tutorial-autoscale-cli.md)


## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć reguły skalowania automatycznego, potrzebny jest istniejący zestaw skalowania maszyny wirtualnej. Zestaw skalowania można utworzyć za pomocą [witryny Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)lub [Interfejsu wiersza polecenia platformy Azure.](quick-create-cli.md)


## <a name="create-a-rule-to-automatically-scale-out"></a>Tworzenie reguły do automatycznego skalowania w poziomie
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawie skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Możesz określić, jakie metryki mają być monitorowane, na przykład procesor CPU lub dysk, oraz jak długo obciążenie aplikacji musi przekraczać wartość progową, a także ile wystąpień maszyn wirtualnych ma zostać dodanych do zestawu skalowania.

1. Otwórz witrynę Azure portal i wybierz **grupy zasobów** z menu po lewej stronie pulpitu nawigacyjnego.
2. Wybierz grupę zasobów zawierającą zestaw skalowania, a następnie wybierz zestaw skalowania z listy zasobów.
3. Wybierz **opcję Skalowanie** z menu po lewej stronie okna zestawu skalowania. Wybierz przycisk, aby **włączyć skalowanie automatyczne:**

    ![Włączanie skalowania automatycznego w witrynie Azure portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Wprowadź nazwę ustawień, na przykład *skalowanie automatyczne,* a następnie wybierz opcję **Dodaj regułę**.

5. Utwórzmy regułę, która zwiększa liczbę wystąpień maszyn wirtualnych w skali ustawionej, gdy średnie obciążenie procesora CPU jest większe niż 70% w okresie 10 minut. Gdy reguła wyzwala, liczba wystąpień maszyn wirtualnych zwiększa się o 20%. W zestawach skalowania z niewielką liczbą wystąpień maszyn wirtualnych można ustawić **operację** *na Zwiększenie liczby,* a następnie określić *1* lub *2* dla *liczby wystąpień*. W zestawach skalowania z dużą liczbą wystąpień maszyn wirtualnych zwiększenie o 10% lub 20% wystąpień maszyn wirtualnych może być bardziej odpowiednie.

    Określ następujące ustawienia reguły:
    
    | Parametr              | Wyjaśnienie                                                                                                         | Wartość          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregacja czasu*     | Określa sposób agregacji metryk zebranych do celów analizy.                                                | Średnia        |
    | *Nazwa metryki*          | Metryka wydajności, która jest monitorowana i na której są stosowane akcje zestawu skalowania.                                                   | Procentowe użycie procesora CPU |
    | *Statystyka ziarna czasu* | Określa, jak zebrane metryki w każdym czasie ziarna powinny być agregowane do analizy.                             | Średnia        |
    | *Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                     | Większe niż   |
    | *Próg*            | Procent, który powoduje, że reguła skalowania automatycznego wyzwolić akcję.                                                 | 70             |
    | *Czas trwania*             | Przedział czasu monitorowania, po którym wartość metryki jest porównywana z wartością progową.                                   | 10 minut     |
    | *Operacji*            | Określa, czy zestaw skalowania powinien być skalowany w górę czy w dół, gdy reguła ma zastosowanie i jaki przyrost                        | Zwiększ procent o |
    | *Liczba wystąpień*       | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                            | 20             |
    | *Czas schładzania (minuty)*  | Przedział czasu przed ponownym zastosowaniem reguły, który gwarantuje, że akcje skalowania automatycznego zaczną obowiązywać. | 5 minut      |

    Poniższe przykłady przedstawiają regułę utworzoną w witrynie Azure portal, która pasuje do tych ustawień:

    ![Tworzenie reguły skalowania automatycznego w celu zwiększenia liczby wystąpień maszyn wirtualnych](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Aby utworzyć regułę, wybierz pozycję **Dodaj**


## <a name="create-a-rule-to-automatically-scale-in"></a>Tworzenie reguły do automatycznego skalowania
Wieczorami lub w weekendy zapotrzebowanie aplikacji może być mniejsze. Jeśli spadek obciążenia ma cechy stałego trendu w danym okresie, można skonfigurować reguły skalowania automatycznego umożliwiające zmniejszenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Akcja skalowania w pionie ogranicza koszt używania zestawu skalowania, ponieważ jest uruchomionych tylko tyle wystąpień, ile jest wymaganych do zaspokojenia bieżącego zapotrzebowania.

1. Wybierz ponownie **dodaj regułę.**
2. Utwórz regułę, która zmniejsza liczbę wystąpień maszyn wirtualnych w zestawie skalowania, gdy średnie obciążenie procesora CPU spadnie poniżej 30% w okresie 10 minut. Gdy reguła wyzwala, liczba wystąpień maszyn wirtualnych zmniejsza się o 20%.

    Użyj tego samego podejścia, co w przypadku poprzedniej reguły. Dostosuj następujące ustawienia reguły:
    
    | Parametr              | Wyjaśnienie                                                                                                          | Wartość          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                      | Mniejsze niż   |
    | *Próg*            | Procent, który powoduje, że reguła skalowania automatycznego wyzwolić akcję.                                                 | 30             |
    | *Operacji*            | Określa, czy zestaw skalowania powinien być skalowany w górę czy w dół, gdy reguła ma zastosowanie i jaki przyrost                         | Zmniejsz procent o |
    | *Liczba wystąpień*       | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                             | 20             |

3. Aby utworzyć regułę, wybierz pozycję **Dodaj**


## <a name="define-autoscale-instance-limits"></a>Definiowanie limitów wystąpień skalowania automatycznego
Profil skalowania automatycznego musi definiować minimalną, maksymalną i domyślną liczbę wystąpień maszyn wirtualnych. Po zastosowaniu reguł skalowania automatycznego te limity wystąpień upewnij się, że nie skalowane w poziomie poza maksymalną liczbę wystąpień lub skalować poza minimum wystąpień.

1. Ustaw następujące limity wystąpień:

    | Minimalne | Maksimum | Domyślne|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Aby zastosować reguły skalowania automatycznego i limity wystąpień, wybierz pozycję **Zapisz**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorowanie liczby wystąpień w zestawie skalowania
Aby wyświetlić liczbę i stan wystąpień maszyn wirtualnych, wybierz **wystąpienie** z menu po lewej stronie okna zestawu skalowania. Stan wskazuje, czy *wystąpienie* maszyny Wirtualnej jest Tworzenie jako zestaw skalowania automatycznie skaluje się w poziomie lub jest *usuwanie* jako skala automatycznie skaluje się w.

![Wyświetlanie listy wystąpień maszyn wirtualnych zestawu skalowania](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Skalowanie automatyczne na podstawie harmonogramu
Poprzednie przykłady automatycznie skalowane skalę ustawioną w lub na zewnątrz z podstawowych metryk hosta, takich jak użycie procesora CPU. Można również tworzyć reguły skalowania automatycznego na podstawie harmonogramów. Te reguły oparte na harmonogramie umożliwiają automatyczne skalowanie w poziomie liczby wystąpień maszyn wirtualnych przed przewidywanym wzrostem zapotrzebowania na aplikacje, takim jak podstawowe godziny pracy, a następnie automatyczne skalowanie liczby wystąpień w czasie, który przewiduje mniejsze zapotrzebowanie, na przykład w weekend.

1. Wybierz **opcję Skalowanie** z menu po lewej stronie okna zestawu skalowania. Aby usunąć istniejące reguły skalowania automatycznego utworzone w poprzednich przykładach, wybierz ikonę kosza.

    ![Usuwanie istniejących reguł skalowania automatycznego](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Wybierz opcję **Dodaj warunek skali**. Wybierz ikonę ołówka obok nazwy reguły i podaj nazwę, taką jak *Skaluj w poziomie podczas każdego dnia roboczego.*

    ![Zmienianie nazwy domyślnej reguły skalowania automatycznego](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Wybierz przycisk opcji, aby **skalować do określonej liczby wystąpień**.
4. Aby zwiększyć liczbę wystąpień, wprowadź *10* jako liczbę wystąpień.
5. Wybierz **pozycję Powtórz określone dni** dla typu **Harmonogram.**
6. Wybierz wszystkie dni robocze od poniedziałku do piątku.
7. Wybierz odpowiednią strefę czasową, a następnie określ **godzinę rozpoczęcia** *09:00*.
8. Wybierz ponownie **dodaj warunek skali.** Powtórz proces, aby utworzyć harmonogram o nazwie *Skala w godzinach wieczornych,* który skaluje się do *3* wystąpień, powtarza się co dzień tygodnia i rozpoczyna się o godzinie *18:00*.
9. Aby zastosować reguły skalowania automatycznego oparte na harmonogramie, wybierz pozycję **Zapisz**.

    ![Tworzenie reguł skalowania automatycznego skalu, które są skalowane zgodnie z harmonogramem](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Aby zobaczyć, jak stosowane są reguły skalowania automatycznego, wybierz **pozycję Uruchom historię** w górnej części okna **Skalowanie.** Wykres i lista zdarzeń pokazuje, kiedy wyzwalanie reguł skalowania automatycznego i liczba wystąpień maszyn wirtualnych w zestawie skalowania wzrasta lub zmniejsza.


## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak używać reguł skalowania automatycznego do skalowania w poziomie i zwiększania lub zmniejszania *liczby* wystąpień maszyn wirtualnych w zestawie skalowania. Można również skalować w pionie, aby zwiększyć lub zmniejszyć *rozmiar*wystąpienia maszyny Wirtualnej . Aby uzyskać więcej informacji, zobacz [Pionowa skalowanie automatyczne z zestawami skalowania maszyny wirtualnej](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Aby uzyskać informacje na temat zarządzania wystąpieniami maszyn wirtualnych, zobacz [Zarządzanie zestawami skalowania maszyn wirtualnych za pomocą programu Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Aby dowiedzieć się, jak generować alerty po wyzwoleniu reguł skalowania automatycznego, zobacz [Wysyłanie powiadomień alertów e-mail i webhook w usłudze Azure Monitor za pomocą akcji skalowania automatycznego.](../azure-monitor/platform/autoscale-webhook-email.md) Za pomocą dzienników inspekcji można również [wysyłać powiadomienia o alertach poczty e-mail i elementu webhook w usłudze Azure Monitor.](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
