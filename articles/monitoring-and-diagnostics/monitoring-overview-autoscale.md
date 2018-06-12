---
title: Omówienie automatycznego skalowania maszyn wirtualnych, usługi w chmurze i aplikacje sieci Web
description: Skalowania automatycznego na platformie Microsoft Azure. Ma zastosowanie do maszyn wirtualnych, zestawy skalowania maszyny wirtualnej, Cloud Services i aplikacji sieci Web.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/02/2016
ms.author: robb
ms.component: autoscale
ms.openlocfilehash: 4eeca81e08a0ecae9ba41ccdd2bf8a2f395f579c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264674"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Omówienie automatycznego skalowania w maszynach wirtualnych platformy Azure firmy Microsoft, usługi w chmurze i aplikacje sieci Web
W tym artykule opisano, jakie skalowania automatycznego Microsoft Azure, jego zalety i sposobu rozpocząć korzystanie z jej.  

Azure Monitor skalowania automatycznego ma zastosowanie tylko do [zestawy skalowania maszyny wirtualnej](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [usługi w chmurze](https://azure.microsoft.com/services/cloud-services/), i [usługi aplikacji — aplikacje sieci Web](https://azure.microsoft.com/services/app-service/web/).

> [!NOTE]
> Platforma Azure ma dwie metody automatycznego skalowania. Starsza wersja programu skalowania automatycznego ma zastosowanie do maszyn wirtualnych (zestawy dostępności). Ta funkcja ma ograniczony pomocy technicznej i rekomendujemy migrację do zestawy skalowania maszyny wirtualnej do obsługi skalowania automatycznego szybszy i bardziej niezawodny. Łącze dotyczące sposobu używania starszej technologii znajduje się w tym artykule.  
>
>

## <a name="what-is-autoscale"></a>Co to jest funkcja automatycznego skalowania?
Funkcja automatycznego skalowania umożliwia ma jednostkom zasoby z uruchomionym do obsługi obciążenia aplikacji. Umożliwia dodawanie zasobów obsługujące wzrost obciążenia, a także oszczędzić pieniądze przez usunięcie zasobów, które są obecne bezczynności. Należy określić minimalną i maksymalną liczbę wystąpień do uruchomienia i dodawanie lub usuwanie maszyn wirtualnych, automatycznie na podstawie zestawu reguł. O minimalnej sprawia, że czy aplikacja zawsze działa nawet w przypadku obciążenia. O maksymalnej ogranicza łącznym możliwy koszt co godzinę. Automatycznie skalować między te dwie wartości graniczne przy użyciu utworzone reguły.

 ![Wyjaśniono skalowania automatycznego. Dodawanie i usuwanie maszyn wirtualnych](./media/monitoring-overview-autoscale/AutoscaleConcept.png)

Gdy warunki reguły są spełnione, co najmniej jednej akcji skalowania automatycznego są wyzwalane. Można dodać i usunąć maszyny wirtualnej lub wykonywać inne akcje. Poniższy diagram koncepcyjny przedstawia tego procesu.  

 ![Diagram przepływu skalowania automatycznego](./media/monitoring-overview-autoscale/Autoscale_Overview_v4.png)

Wyjaśnienia poniżej dotyczą części poprzedni diagram.   

## <a name="resource-metrics"></a>Metryki dla zasobu
Zasoby Emituj metryki, te metryki są później przetwarzane przez zasady. Metryki dostarczane za pomocą różnych metod.
Zestawy skalowania maszyny wirtualnej Użyj danych telemetrycznych z agentów diagnostyki Azure, natomiast dane telemetryczne dla aplikacji sieci Web i usługi w chmurze pochodzi bezpośrednio z infrastruktury platformy Azure. Niektóre często używane dane statystyczne obejmują użycie procesora CPU, pamięć liczników wątków, długość kolejki i użycie dysku. Aby uzyskać listę danych telemetrycznych można użyć, zobacz [wspólnej metryki automatycznego skalowania](insights-autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Metryki niestandardowe
Można również wykorzystać własne niestandardowe metryki, które mogą być emitowanie Twojej aplikacji. Jeśli Twoje aplikacje przesyłają metryk usługi Application Insights mogą korzystać z tych metryk przy podejmowaniu decyzji, czy można skalować lub nie zostały skonfigurowane. 

## <a name="time"></a>Time
Reguły na podstawie harmonogramu są oparte na czas UTC. Należy poprawnie ustawić strefy czasowej podczas konfigurowania reguł.  

## <a name="rules"></a>Reguły
Na diagramie przedstawiono tylko jedną regułę automatycznego skalowania, ale można mieć wiele z nich. Można utworzyć złożone zasady nakładające się odpowiednio do potrzeb.  Typy reguł  

* **Na podstawie Metryka** — na przykład, wykonaj tę akcję, gdy wykorzystanie Procesora jest powyżej 50%.
* **Na podstawie czasu** — na przykład wyzwalacza elementu webhook co 8 am w sobotę danego strefy czasowej.

Reguły metryki pomiaru obciążenia aplikacji i Dodaj lub usuń maszyny wirtualne oparte na tym obciążenia. Zasady oparte na harmonogramie umożliwiają skalowane, gdy Zobacz wzorce czasu w obciążenia i skalowania przed wzrostu obciążenia można lub zmniejszenie.  

## <a name="actions-and-automation"></a>Akcje i automatyzacji
Reguły może wyzwolić co najmniej jeden typ akcji.

* **Skala** -VMs skali przychodzący lub wychodzący
* **Wiadomości e-mail** — Wyślij wiadomość e-mail do administratorów subskrypcji, co Administratorzy i/lub dodatkowy adres e-mail użytkownika
* **Zautomatyzować za pomocą elementów webhook** -Wywołaj elementów webhook, co może uruchamiać wiele złożonych akcji wewnątrz lub na zewnątrz Azure. Wewnątrz Azure można uruchomić elementu runbook usługi Automatyzacja Azure, funkcji platformy Azure lub aplikacji logiki platformy Azure. Przykładowy adres URL innych firm poza Azure obejmują usług takich jak zapas czasu i usługi Twilio.

## <a name="autoscale-settings"></a>Ustawienia skalowania automatycznego
Funkcja automatycznego skalowania użyć następujących terminologii i struktury.

- **Ustawieniu skalowania automatycznego** jest do odczytu przez aparat skalowania automatycznego w celu ustalenia, czy można skalować w górę lub w dół. Zawiera jeden lub więcej profilów, informacje dotyczące zasobu docelowego, a ustawienia powiadomień.

    - **Profilów skalowania automatycznego** jest kombinacją a:

        - **Ustawienia pojemności**, które określa minimalne, maksymalne i domyślne wartości dla wielu wystąpień.
        - **zestaw reguł**, z których każdy zawiera wyzwalacz (czas lub Metryka) oraz akcję skalowania (w górę lub w dół).
        - **Cykl**, co oznacza, że podczas skalowania automatycznego powinien obowiązywać tego profilu.

        Może mieć wiele profilów, które umożliwiają zajmie się innymi wymaganiami nakładające się. Program może profilów skalowania automatycznego różne dla różnych porach dnia lub dni tygodnia, na przykład.

    - A **ustawienie powiadomień o** definiuje, jakie powiadomień powinna wystąpić, gdy wystąpi zdarzenie skalowania automatycznego oparte na spełniające kryteria profile w ustawieniu skalowania automatycznego. Automatycznego skalowania może powiadomić co najmniej jeden adres e-mail lub wykonywania wywołań do co najmniej jeden element webhook.


![Ustawienia skalowania automatycznego Azure, profilu i struktury reguły](./media/monitoring-overview-autoscale/AzureResourceManagerRuleStructure3.png)

Pełną listę można konfigurować pól i opisy jest dostępna w [interfejsu API REST skalowania automatycznego](https://msdn.microsoft.com/library/dn931928.aspx).

Aby uzyskać przykłady kodu zobacz

* [Zestawy skalowania maszyny Wirtualnej za pomocą szablonów usługi Resource Manager konfiguracji zaawansowanej skalowania automatycznego](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Interfejs API REST skalowania automatycznego](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Pionowy skalowanie w poziomie vs
Skalowania automatycznego tylko skalowanie w poziomie, wzrost ("out") lub spadek ("") w liczbie wystąpień maszyn wirtualnych.  Poziomy jest bardziej elastyczna w sytuacji, w chmurze, ponieważ pozwala na uruchamianie potencjalnie tysiące maszyn wirtualnych do obsługi obciążenia.

Z kolei skalowanie w pionie różni się. Zachowuje tę samą liczbę maszyn wirtualnych, ale powoduje, że maszyny wirtualne więcej ("w górę") lub mniej ("wyłączone") zaawansowane. Power jest mierzony w pamięci, szybkości Procesora, miejsca na dysku itp.  Skalowanie w pionie ma więcej ograniczeń. Jest on zależny od dostępności sprzętu większych i szybko trafienia górny limit i zależą od regionu. Skalowanie w pionie również zwykle wymaga maszyny Wirtualnej, aby zatrzymać i uruchomić ponownie.

Aby uzyskać więcej informacji, zobacz [skalowanie w pionie maszyny wirtualnej platformy Azure w usłudze Automatyzacja Azure](../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Metody dostępu
Możesz skonfigurować automatycznego skalowania za pomocą

* [Azure Portal](insights-how-to-scale.md)
* [Program PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
* [Interfejs wiersza polecenia i platform (CLI)](insights-cli-samples.md#autoscale)
* [Interfejs API REST Azure monitora](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Obsługiwane usługi do skalowania automatycznego
| Usługa | Schemat & dokumentów |
| --- | --- |
| Web Apps |[Skalowanie aplikacji sieci Web](insights-how-to-scale.md) |
| Cloud Services |[Funkcja automatycznego skalowania usługi w chmurze](../cloud-services/cloud-services-how-to-scale-portal.md) |
| Maszyny wirtualne: klasycznym |[Skalowanie zestawów dostępności klasyczne maszyny wirtualnej](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Maszyn wirtualnych: Zestawy skalowania systemu Windows |[Ustawia skalowanie skalowania maszyny wirtualnej w systemie Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) |
| Maszyn wirtualnych: Ustawia Linux skalowania |[Ustawia skalowanie skalowania maszyny wirtualnej w systemie Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Maszyny wirtualnej: Przykład systemu Windows |[Zestawy skalowania maszyny Wirtualnej za pomocą szablonów usługi Resource Manager konfiguracji zaawansowanej skalowania automatycznego](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat skalowania automatycznego, użyj wskazówki dotyczące skalowania automatycznego wymienionymi wcześniej, lub można znaleźć w następujących zasobach:

* [Azure metryki wspólnej skalowania automatycznego monitora](insights-autoscale-common-metrics.md)
* [Najlepsze praktyki dotyczące skalowania automatycznego Azure Monitor](insights-autoscale-best-practices.md)
* [Użyj akcji skalowania automatycznego do wysyłania wiadomości e-mail i elementu webhook powiadomień o alertach](insights-autoscale-to-webhook-email.md)
* [Interfejs API REST skalowania automatycznego](https://msdn.microsoft.com/library/dn931953.aspx)
* [Rozwiązywania problemów skalowania automatycznego zestawy skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
