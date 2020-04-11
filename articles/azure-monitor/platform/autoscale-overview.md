---
title: Automatyczne skalowanie na maszynach wirtualnych, usługach w chmurze i aplikacjach sieci Web
description: Skalowanie automatyczne na platformie Microsoft Azure. Dotyczy maszyn wirtualnych, zestawów skalowania maszyn wirtualnych, usług w chmurze i aplikacji sieci Web.
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eeb8b301bf087efa164a7864cdce3a04952f45ed
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114135"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Omówienie funkcji Skalowanie automatyczne na maszynach wirtualnych platformy Microsoft Azure oraz w usługach Cloud Services i Web Apps
W tym artykule opisano, czym jest skalowanie automatyczne platformy Microsoft Azure, jakie są jego zalety i jak rozpocząć korzystanie z niej.  

Skalowanie automatyczne usługi Azure Monitor dotyczy tylko [zestawów skalowania maszyn wirtualnych,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [usług w chmurze,](https://azure.microsoft.com/services/cloud-services/) [usługi aplikacji — aplikacje sieci Web](https://azure.microsoft.com/services/app-service/web/)i usług [zarządzania interfejsami API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> Platforma Azure ma dwie metody skalowania automatycznego. Starsza wersja skalowania automatycznego dotyczy maszyn wirtualnych (zestawy dostępności). Ta funkcja ma ograniczoną obsługę i zaleca się migrację do zestawów skalowania maszyny wirtualnej w celu szybszej i bardziej niezawodnej obsługi skalowania automatycznego. Łącze dotyczące korzystania ze starszej technologii znajduje się w tym artykule.  
>
>

## <a name="what-is-autoscale"></a>Co to jest skalowanie automatyczne?
Skalowanie automatyczne umożliwia odpowiednią ilość zasobów uruchomionych do obsługi obciążenia aplikacji. Pozwala na dodanie zasobów do obsługi wzrost obciążenia, a także zaoszczędzić pieniądze, usuwając zasoby, które siedzą bezczynnie. Należy określić minimalną i maksymalną liczbę wystąpień do uruchamiania i dodawania lub usuwania maszyn wirtualnych automatycznie na podstawie zestawu reguł. Posiadanie minimum zapewnia, że aplikacja jest zawsze uruchomiona nawet bez obciążenia. Mając maksymalne limity całkowitego możliwego kosztu godzinowego. Automatycznie skalujesz między tymi dwoma skrajnościami przy użyciu reguł, które tworzysz.

 ![Autoscale wyjaśnione. Dodawanie i usuwanie maszyn wirtualnych](./media/autoscale-overview/AutoscaleConcept.png)

Po spełnieniu warunków reguły wyzwalana jest co najmniej jedna akcja skalowania automatycznego. Można dodawać i usuwać maszyny wirtualne lub wykonywać inne akcje. Na poniższym diagramie koncepcyjnym przedstawiono ten proces.  

 ![Diagram przepływu automatycznego skalowania](./media/autoscale-overview/Autoscale_Overview_v4.png)

Poniższe wyjaśnienie dotyczy fragmentów poprzedniego diagramu.   

## <a name="resource-metrics"></a>Metryki zasobu
Zasoby emitują metryki, te metryki są później przetwarzane przez reguły. Metryki pochodzą z różnych metod.
Zestawy skalowania maszyny wirtualnej używają danych telemetrycznych z agentów diagnostycznych platformy Azure, podczas gdy dane telemetryczne dla aplikacji sieci Web i usług w chmurze pochodzą bezpośrednio z infrastruktury platformy Azure. Niektóre często używane statystyki obejmują użycie procesora CPU, użycie pamięci, liczby wątków, długość kolejki i użycie dysku. Aby uzyskać listę danych telemetrycznych, których można użyć, zobacz [Skalowanie automatyczne metryk typowych](../../azure-monitor/platform/autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Metryki niestandardowe
Można również wykorzystać własne metryki niestandardowe, które mogą emitować aplikacje. Jeśli aplikacje zostały skonfigurowane do wysyłania metryk do usługi Application Insights, można wykorzystać te metryki do podejmowania decyzji, czy skalować, czy nie.

## <a name="time"></a>Time
Reguły oparte na harmonogramie są oparte na ekwolcie UTC. Podczas konfigurowania reguł należy prawidłowo ustawić strefę czasową.  

## <a name="rules"></a>Reguły
Diagram przedstawia tylko jedną regułę skalowania automatycznego, ale można mieć wiele z nich. W razie potrzeby można utworzyć złożone nakładające się reguły.  Typy reguł obejmują  

* **Oparte na metryce** — na przykład wykonaj tę akcję, gdy użycie procesora CPU przekracza 50%.
* **Oparte na czasie** — na przykład wyzwalanie elementu webhook co 8 rano w sobotę w danej strefie czasowej.

Reguły oparte na metrykach mierzą obciążenie aplikacji i dodają lub usuwają maszyny wirtualne na podstawie tego obciążenia. Reguły oparte na harmonogramie umożliwiają skalowanie, gdy widzisz wzorce czasu w obciążeniu i chcesz skalować przed wystąpieniem możliwego zwiększenia lub zmniejszenia obciążenia.  

## <a name="actions-and-automation"></a>Działania i automatyzacja
Reguły mogą wyzwolić jeden lub więcej typów akcji.

* **Skala** — skalowanie maszyn wirtualnych doł.
* **E-mail** — wysyłanie wiadomości e-mail do administratorów subskrypcji, współadministratorów i/lub dodatkowego adresu e-mail, który określisz
* **Automatyzuj za pomocą elementów webhook** — wywoływanie elementów webhook, które mogą wyzwalać wiele złożonych akcji wewnątrz lub na zewnątrz platformy Azure. Na platformie Azure można uruchomić element runbook usługi Azure Automation, funkcję platformy Azure lub aplikację Logika platformy Azure. Przykładowy adres URL innej firmy poza platformą Azure obejmują usługi, takie jak Slack i Twilio.

## <a name="autoscale-settings"></a>Ustawienia skalowania automatycznego
Skalowanie automatyczne używa następującej terminologii i struktury.

- **Ustawienie skalowania automatycznego** jest odczytywane przez aparat skalowania automatycznego w celu określenia, czy mają być skalowane w górę czy w dół. Zawiera jeden lub więcej profili, informacje o zasobie docelowym i ustawienia powiadomień.

  - **Profil skalowania automatycznego** jest kombinacją:

    - **ustawienie pojemności**, które wskazuje wartości minimalne, maksymalne i domyślne dla liczby wystąpień.
    - **zestaw reguł,** z których każdy zawiera wyzwalacz (czas lub metrykę) i akcję skalowania (w górę lub w dół).
    - **cyklu ,** który wskazuje, kiedy skalowanie automatyczne powinno wprowadzić ten profil w życie.

      Możesz mieć wiele profili, które pozwalają dbać o różne nakładające się wymagania. Możesz mieć różne profile skalowania automatycznego dla różnych porach dnia lub dni tygodnia, na przykład.

  - **Ustawienie powiadomień** określa, jakie powiadomienia powinny wystąpić, gdy występuje zdarzenie skalowania automatycznego na podstawie spełnienia kryteriów jednego z profilów ustawienia skalowania automatycznego. Skalowanie automatyczne może powiadamiać o jednym lub większej liczbie adresów e-mail lub nawiązywać połączenia z jednym lub kilkoma elementami webhook.


![Ustawienie, struktura profilu i reguły automatycznego skalowania platformy Azure](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

Pełna lista konfigurowalnych pól i opisów jest dostępna w [interfejsie API REST w skali automatycznej.](https://msdn.microsoft.com/library/dn931928.aspx)

Przykłady kodu można znaleźć w

* [Zaawansowana konfiguracja skalowania automatycznego przy użyciu szablonów Menedżera zasobów dla zestawów skalowania maszyn wirtualnych](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [Interfejs API REST w automatycznym skalowaniu](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Skalowanie poziome a pionowe
Skalowanie automatyczne jest skalowane tylko w poziomie, co oznacza wzrost ("out") lub zmniejszenie ("in") w liczbie wystąpień maszyn wirtualnych.  Poziome jest bardziej elastyczne w sytuacji chmury, ponieważ umożliwia uruchamianie potencjalnie tysiące maszyn wirtualnych do obsługi obciążenia.

Natomiast skalowanie pionowe jest inne. Zachowuje taką samą liczbę maszyn wirtualnych, ale sprawia, że maszyny wirtualne więcej ("w górę") lub mniej ("w dół") zaawansowane. Moc jest mierzona w pamięci, szybkość procesora, miejsce na dysku, itp.  Skalowanie w pionie ma więcej ograniczeń. Jest zależna od dostępności większego sprzętu, który szybko osiąga górny limit i może się różnić w zależności od regionu. Skalowanie w pionie zwykle wymaga maszyny Wirtualnej, aby zatrzymać i ponownie uruchomić.


## <a name="methods-of-access"></a>Metody dostępu
Można skonfigurować skalowanie automatyczne za pomocą

* [Azure Portal](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Międzyplatformowy interfejs wiersza polecenia (CLI)](../../azure-monitor/platform/cli-samples.md#autoscale)
* [Interfejs API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Obsługiwane usługi skalowania automatycznego
| Usługa | Schemat & dokumenty |
| --- | --- |
| Web Apps |[Skalowanie aplikacji sieci Web](../../azure-monitor/platform/autoscale-get-started.md) |
| Cloud Services |[Automatyczne skalowanie usługi w chmurze](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Maszyny wirtualne: klasyczne |[Skalowanie klasycznych zestawów dostępności maszyn wirtualnych](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Maszyny wirtualne: zestawy skalowania systemu Windows |[Skalowanie zestawów skalowania maszyny wirtualnej w systemie Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Maszyny wirtualne: zestawy skalowania Linuksa |[Skalowanie zestawów skalowania maszyny wirtualnej w systemie Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Maszyny wirtualne: Przykład systemu Windows |[Zaawansowana konfiguracja skalowania automatycznego przy użyciu szablonów Menedżera zasobów dla zestawów skalowania maszyn wirtualnych](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| Usługa api Management|[Automatyczne skalowanie wystąpienia usługi Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o skalowaniu automatycznym, użyj wcześniej wymienionych przewodników w skali automatycznej lub zapoznaj się z następującymi zasobami:

* [Wspólne metryki usługi Azure Monitor](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Najlepsze rozwiązania dotyczące skalowania automatycznego w usłudze Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Wysyłanie powiadomień o alertach e-mail i webhook za pomocą automatycznego skalowania](../../azure-monitor/platform/autoscale-webhook-email.md)
* [Interfejs API REST w automatycznym skalowaniu](https://msdn.microsoft.com/library/dn931953.aspx)
* [Rozwiązywanie problemów z skalowaniem maszyny wirtualnej ustawia skalowanie automatyczne](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

