---
title: Monitorowanie rozwiązań w Azure Monitor | Microsoft Docs
description: Monitorowanie rozwiązań w Azure Monitor to zbiór reguł logiki, wizualizacji i pozyskiwania danych, które zapewniają metryki przestawiane wokół określonego obszaru problemu.  Ten artykuł zawiera informacje na temat instalowania i używania rozwiązań monitorowania.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 7ea7051b24deeddea25b1db771b73a226f650067
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75401151"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Monitorowanie rozwiązań w Azure Monitor
Rozwiązania do monitorowania wykorzystują usługi platformy Azure, aby zapewnić dodatkowe informacje o działaniu konkretnej aplikacji lub usługi. Ten artykuł zawiera krótkie omówienie rozwiązań monitorowania na platformie Azure i szczegółowe informacje dotyczące korzystania z nich i instalowania ich.

> [!NOTE]
> Rozwiązania do monitorowania były wcześniej określane jako rozwiązania do zarządzania.

Monitorowanie rozwiązań zazwyczaj zbiera dane dzienników i udostępnia zapytania i widoki umożliwiające analizowanie zebranych danych. Mogą one również korzystać z innych usług, takich jak usługa Azure Automation do wykonania akcji związanych z aplikacji lub usługi.

Możesz dodać rozwiązania do monitorowania do Azure Monitor dla wszystkich używanych aplikacji i usług. Są one zazwyczaj dostępne bezpłatnie, ale zbierają dane, które mogą wywołać opłaty za użycie. Oprócz rozwiązania firmy Microsoft, partnerów i klientów można [tworzenie rozwiązań do zarządzania](solutions-creating.md) do użycia we własnym środowisku lub udostępniane klientom za pośrednictwem społeczności.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Używanie rozwiązań do monitorowania
Otwórz stronę **przeglądu** w Azure monitor, aby wyświetlić kafelek dla każdego rozwiązania zainstalowanego w obszarze roboczym. 

1. Przejdź do witryny [Azure Portal](https://ms.portal.azure.com). Wyszukaj i wybierz pozycję **monitor**.
1. W obszarze **Insights** menu, wybierz opcję **więcej**.
1. Użyj pola listy rozwijanej w górnej części ekranu, aby zmienić obszar roboczy lub zakres czasu używany w przypadku kafelków.
1. Kliknij kafelek rozwiązania, aby otworzyć jego widok, który zawiera bardziej szczegółową analizę zebranych danych.

![Przegląd](media/solutions/overview.png)

Rozwiązania do monitorowania mogą zawierać wiele typów zasobów platformy Azure i można wyświetlić wszystkie zasoby dołączone do rozwiązania, tak jak każdy inny zasób. Na przykład wszystkie zapytania dzienników zawarte w rozwiązaniu są wymienione w obszarze **zapytania dotyczące rozwiązań** w [Eksploratorze zapytań](../log-query/get-started-portal.md#load-queries) , których można używać podczas przeprowadzania analizy ad hoc przy użyciu [zapytań dzienników](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Wyświetlanie listy zainstalowanych rozwiązań monitorowania 
Aby wyświetlić listę rozwiązań monitorowania zainstalowanych w ramach subskrypcji, należy wykonać poniższą procedurę.

1. Przejdź do witryny [Azure Portal](https://ms.portal.azure.com). Wyszukaj i wybierz **rozwiązania**.
1. Rozwiązania zainstalowane we wszystkich obszarach roboczych są wyświetlane. Po nazwie rozwiązania następuje nazwa obszaru roboczego, w którym jest zainstalowana.
1. Użyj pola listy rozwijanej w górnej części ekranu, aby filtrować według subskrypcji lub grupy zasobów.


![Lista wszystkich rozwiązań](media/solutions/list-solutions-all.png)

Kliknij nazwę rozwiązania, aby otworzyć jej stronę podsumowania. Ta strona wyświetla wszystkie widoki zawarty w rozwiązaniu oraz różne opcje dla rozwiązania wraz z jej obszaru roboczego. Wyświetlić stronę podsumowania dla rozwiązania przy użyciu jednej z powyższych procedur do listy rozwiązań, a następnie kliknij nazwę rozwiązania.

![Właściwości rozwiązania](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Zainstaluj rozwiązanie do monitorowania
Rozwiązania do monitorowania od firmy Microsoft i partnerów są dostępne w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Możesz wyszukiwać dostępnych rozwiązań i zainstalować je przy użyciu poniższej procedury. Po zainstalowaniu rozwiązania, należy wybrać [obszaru roboczego usługi Log Analytics](../platform/manage-access.md) zainstalowanym rozwiązania i gdzie będą zbierane dane.

1. Z [listę rozwiązań dla Twojej subskrypcji](#list-installed-monitoring-solutions), kliknij przycisk **Dodaj**.
1. Przeglądaj lub Wyszukaj rozwiązanie. Możesz również przeglądać rozwiązania z [tego linku wyszukiwania](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Znajdź odpowiednie rozwiązanie do monitorowania i przeczytaj jego opis.
1. Kliknij przycisk **Utwórz** do uruchamiania procesu instalacji.
1. Po rozpoczęciu procesu instalacji zostanie wyświetlony monit o określenie obszaru roboczego Log Analytics i udostępnienie wymaganej konfiguracji rozwiązania.

![Zainstalowanie rozwiązania](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Zainstalowanie rozwiązania od społeczności
Członkowie społeczności mogą przesyłać do platformy Azure — szablony szybkiego startu rozwiązania do zarządzania. Można bezpośrednio zainstalować te rozwiązania lub pobranie szablonów dla nowszych instalacji.

1. Wykonaj czynności opisane w [obszar roboczy usługi Log Analytics i konto usługi Automation](#log-analytics-workspace-and-automation-account) połączyć obszar roboczy i konto.
2. Przejdź do [szablony szybkiego startu platformy](https://azure.microsoft.com/documentation/templates/). 
3. Wyszukaj rozwiązanie, które interesują Cię.
4. Wybierz rozwiązanie z wyników, aby wyświetlić jego szczegóły.
5. Kliknij przycisk **Wdróż na platformie Azure** przycisku.
6. Monit o podanie informacji takich jak grupy zasobów i lokalizacji, oprócz wartości parametrów w rozwiązaniu.
7. Kliknij przycisk **zakupu** może instalować rozwiązanie.


## <a name="log-analytics-workspace-and-automation-account"></a>Obszar roboczy usługi log Analytics i konto usługi Automation
Wszystkie rozwiązania do monitorowania wymagają [log Analytics obszar roboczy](../platform/manage-access.md) do przechowywania danych zbieranych przez rozwiązanie oraz do hostowania przeszukiwanych i widoków dzienników. Niektóre rozwiązania wymagają również [konta usługi Automation](../../automation/automation-security-overview.md#automation-account-overview) zawiera elementy runbook i powiązane zasoby. Obszar roboczy i konto musi spełniać następujące wymagania.

* Każda instalacja rozwiązania można używać tylko jednego obszaru roboczego usługi Log Analytics i jedno konto usługi Automation. Oddzielnie zainstalować rozwiązanie do wielu obszarów roboczych.
* Jeśli to rozwiązanie wymaga konta usługi Automation, następnie obszaru roboczego usługi Log Analytics i konto usługi Automation muszą być połączone ze sobą. Obszar roboczy usługi Log Analytics mogą być łączone tylko na jednym koncie usługi Automation, a konto usługi Automation mogą być łączone tylko z jednym obszarem roboczym usługi Log Analytics.
* Połączone, obszar roboczy usługi Log Analytics i konto usługi Automation musi być w tej samej grupie zasobów i regionie. Wyjątkiem jest obszar roboczy w regionie wschodnie stany USA i konta usługi Automation w regionie wschodnie stany USA 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Utwórz łącze między obszaru roboczego usługi Log Analytics i konto usługi Automation
Jak określić obszar roboczy usługi Log Analytics i konto usługi Automation zależy od metody instalacji dla danego rozwiązania.

* Po zainstalowaniu rozwiązania w portalu Azure Marketplace zostanie wyświetlony monit dotyczący obszaru roboczego i konta usługi Automation. Połączenie między nimi jest tworzony, jeśli już nie są połączone.
* W przypadku rozwiązań poza portalem Azure Marketplace musisz połączyć obszar roboczy usługi Log Analytics i konto usługi Automation przed instalacją rozwiązania. Można to zrobić, wybierając dowolne rozwiązanie w witrynie Azure Marketplace i wybierając obszar roboczy usługi Log Analytics i konto usługi Automation. Nie masz faktycznie zainstalować rozwiązania, ponieważ łącze jest tworzone, tak szybko, jak obszar roboczy usługi Log Analytics i konto usługi Automation są zaznaczone. Po utworzeniu łącza, a następnie można użyć tego obszaru roboczego usługi Log Analytics i konto usługi Automation dla dowolnego rozwiązania.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Sprawdź połączenie między obszaru roboczego usługi Log Analytics i konto usługi Automation
Można sprawdzić połączenie między obszar roboczy usługi Log Analytics i kontem usługi Automation, korzystając z następującej procedury.

1. Wybierz konto usługi Automation w witrynie Azure portal.
1. Przewiń do **powiązane zasoby** części menu.
1. Jeśli **obszaru roboczego** ustawienie jest włączone, a następnie to konto jest połączone z obszarem roboczym usługi Log Analytics. Możesz kliknąć **obszaru roboczego** Aby wyświetlić szczegóły obszaru roboczego.

## <a name="remove-a-monitoring-solution"></a>Usuń rozwiązanie do monitorowania
Aby usunąć rozwiązanie zainstalowany, znajdź go w [listę zainstalowanych rozwiązań](#list-installed-monitoring-solutions). Kliknij nazwę rozwiązania, aby otworzyć jej stronę podsumowania, a następnie kliknij polecenie **Usuń**.


## <a name="next-steps"></a>Następne kroki
* Pobierz [listę rozwiązań monitorowania od firmy Microsoft](solutions-inventory.md).
* Dowiedz się, jak [tworzyć zapytania](../log-query/log-query-overview.md) do analizowania danych zbieranych przez rozwiązania monitorujące.

