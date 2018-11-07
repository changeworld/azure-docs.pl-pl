---
title: Rozwiązania do zarządzania na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania na platformie Azure to zbiór reguł nabycia logiki, wizualizacji i danych, które udostępniają metryki dotyczące określonego obszaru problemów.  Ten artykuł zawiera informacje na temat instalowania i za pomocą rozwiązania do zarządzania.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 53020eeb96927143b2fb7394aee64f9b7ea55df9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248966"
---
# <a name="management-solutions-in-azure"></a>Rozwiązania do zarządzania na platformie Azure
Rozwiązania do zarządzania korzystać z usług na platformie Azure, aby zapewnić dodatkowy wgląd w działania określonej aplikacji lub usługi. Ten artykuł zawiera krótkie omówienie rozwiązania do zarządzania na platformie Azure i uzyskać szczegółowe informacje na temat używania i instalując je.

Rozwiązania do zarządzania zwykle zbierać informacje w usłudze Log Analytics i zapewnienia wyszukiwań w dziennikach i widoków do analizowania zebranych danych. Mogą one również korzystać z innych usług, takich jak usługa Azure Automation do wykonania akcji związanych z aplikacji lub usługi.

Rozwiązania do zarządzania można dodać do subskrypcji platformy Azure dla dowolnej aplikacji i usług, których używasz. Są one zazwyczaj dostępne w żadnych kosztów, ale zbieranie danych, które można wywołać opłaty za użycie. Oprócz rozwiązania firmy Microsoft, partnerów i klientów można [tworzenie rozwiązań do zarządzania](../monitoring/monitoring-solutions-creating.md) do użycia we własnym środowisku lub udostępniane klientom za pośrednictwem społeczności.

## <a name="using-management-solutions"></a>Za pomocą rozwiązania do zarządzania
**Przegląd** strony dla każdego obszaru roboczego usługi Log Analytics Wyświetla Kafelek dla każdego rozwiązania w obszarze roboczym. Kliknij Kafelek rozwiązania otworzyć jej widok, który zawiera bardziej szczegółowe analizy jego zebranych danych.

![Przegląd](media/monitoring-solutions/overview.png)

Rozwiązania do zarządzania może zawierać wiele typów zasobów platformy Azure i wyświetlić wszystkie zasoby dołączone do rozwiązania, tak jak każdy inny zasób. Na przykład dowolnego przeszukiwania dzienników, zawarty w rozwiązaniu są dołączone **zapisane wyszukiwania** w obszarze roboczym. Podczas przeprowadzania analizy ad-hoc w usłudze Log Analytics, można użyć wyszukiwania.

## <a name="list-installed-management-solutions"></a>Wyświetlanie listy zainstalowanych rozwiązań do zarządzania 
Aby wyświetlić listę rozwiązań do zarządzania zainstalowany w ramach subskrypcji, należy użyć następującej procedury.

1. Zaloguj się w witrynie Azure Portal.
2. W okienku po lewej stronie wybierz **wszystkich usług**.
3. Albo przewiń w dół do **rozwiązania** lub typ *rozwiązania* do **filtru** okna dialogowego.
4. Rozwiązania zainstalowane we wszystkich obszarach roboczych są wyświetlane. Nazwa rozwiązania następuje nazwa obszaru roboczego usługi Log Analytics, który jest instalowany w.
1. Użyj pola listy rozwijanej w górnej części ekranu, aby filtrować według subskrypcji lub grupy zasobów.


![Lista wszystkich rozwiązań](media/monitoring-solutions/list-solutions-all.png)

Kliknij nazwę rozwiązania, aby otworzyć jej stronę podsumowania. Ta strona wyświetla wszystkie widoki usługi Log Analytics, zawarty w rozwiązaniu oraz różne opcje dla rozwiązania wraz z jej obszaru roboczego. Wyświetlić stronę podsumowania dla rozwiązania przy użyciu jednej z powyższych procedur do listy rozwiązań, a następnie kliknij nazwę rozwiązania.

![Właściwości rozwiązania](media/monitoring-solutions/solution-properties.png)



## <a name="install-a-management-solution"></a>Zainstalować rozwiązanie do zarządzania
Rozwiązania do zarządzania firmy Microsoft i partnerów są dostępne z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Możesz wyszukiwać dostępnych rozwiązań i zainstalować je przy użyciu poniższej procedury.

1. Z [listę rozwiązań dla Twojej subskrypcji](#list-installed-management-solutions), kliknij przycisk **Dodaj**. 
1. Po prawej stronie **rozwiązań do zarządzania**, kliknij przycisk **więcej**. 
1. Znajdź rozwiązania do zarządzania ma i zapoznaj się z jego opis.
1. Kliknij przycisk **Utwórz** do uruchamiania procesu instalacji.
1. Podczas uruchamiania procesu instalacji, zostanie wyświetlony monit podanie wymaganej konfiguracji, które różni się w poszczególnych rozwiązań. Wszystkie z nich wymagają wybierz obszar roboczy usługi Log Analytics zainstalowanym rozwiązania i gdzie będą zbierane dane. 

![Zainstalowanie rozwiązania](media/monitoring-solutions/install-solution.png)

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
Wszystkie rozwiązania zarządzania wymagają [obszaru roboczego usługi Log Analytics](../log-analytics/log-analytics-manage-access.md) do przechowywania danych zbieranych przez to rozwiązanie i hostowania wyszukiwań w dziennikach i widoków. Niektóre rozwiązania wymagają również [konta usługi Automation](../automation/automation-security-overview.md#automation-account-overview) zawiera elementy runbook i powiązane zasoby. Obszar roboczy i konto musi spełniać następujące wymagania.

* Każda instalacja rozwiązania można używać tylko jednego obszaru roboczego usługi Log Analytics i jedno konto usługi Automation. Oddzielnie zainstalować rozwiązanie do wielu obszarów roboczych.
* Jeśli to rozwiązanie wymaga konta usługi Automation, następnie obszaru roboczego usługi Log Analytics i konto usługi Automation muszą być połączone ze sobą. Obszar roboczy usługi Log Analytics mogą być łączone tylko na jednym koncie usługi Automation, a konto usługi Automation mogą być łączone tylko z jednym obszarem roboczym usługi Log Analytics.
* Połączone, obszar roboczy usługi Log Analytics i konto usługi Automation musi być w tej samej grupie zasobów i regionie. Wyjątkiem jest obszar roboczy w regionie wschodnie stany USA i konta usługi Automation w regionie wschodnie stany USA 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Tworzenie połączenia między obszaru roboczego usługi Log Analytics i konto usługi Automation
Jak określić obszar roboczy usługi Log Analytics i konto usługi Automation zależy od metody instalacji dla danego rozwiązania.

* Po zainstalowaniu rozwiązania w portalu Azure Marketplace, zostanie wyświetlony monit o obszar roboczy i konto usługi Automation. Połączenie między nimi jest tworzony, jeśli już nie są połączone.
* W przypadku rozwiązań poza portalem Azure Marketplace musisz połączyć obszar roboczy usługi Log Analytics i konto usługi Automation przed instalacją rozwiązania. Można to zrobić, wybierając dowolne rozwiązanie w witrynie Azure Marketplace i wybierając obszar roboczy usługi Log Analytics i konto usługi Automation. Nie masz faktycznie zainstalować rozwiązania, ponieważ łącze jest tworzone, tak szybko, jak obszar roboczy usługi Log Analytics i konto usługi Automation są zaznaczone. Po utworzeniu łącza, a następnie można użyć tego obszaru roboczego usługi Log Analytics i konto usługi Automation dla dowolnego rozwiązania.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Weryfikowanie połączenia między obszaru roboczego usługi Log Analytics i konto usługi Automation
Można sprawdzić połączenie między obszar roboczy usługi Log Analytics i kontem usługi Automation, korzystając z następującej procedury.

1. Wybierz konto usługi Automation w witrynie Azure portal.
1. Przewiń do **powiązane zasoby** części menu.
1. Jeśli **obszaru roboczego** ustawienie jest włączone, a następnie to konto jest połączone z obszarem roboczym usługi Log Analytics. Możesz kliknąć **obszaru roboczego** Aby wyświetlić szczegóły obszaru roboczego.

## <a name="remove-a-management-solution"></a>Usuń to rozwiązanie do zarządzania
Aby usunąć rozwiązanie zainstalowany, znajdź go w [listę zainstalowanych rozwiązań](#list-installed-management-solutions). Kliknij nazwę rozwiązania, aby otworzyć jej stronę podsumowania, a następnie kliknij polecenie **Usuń**.




## <a name="next-steps"></a>Kolejne kroki
* Pobierz [listę rozwiązań do zarządzania przez firmę Microsoft](monitoring-solutions-inventory.md).
* Dowiedz się, jak [tworzenia zapytań](../log-analytics/log-analytics-queries.md) analizować dane zbierane przez rozwiązania do zarządzania.

