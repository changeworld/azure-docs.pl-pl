---
title: Monitorowanie rozwiązań w Azure Monitor | Microsoft Docs
description: Monitorowanie rozwiązań w Azure Monitor to zbiór reguł logiki, wizualizacji i pozyskiwania danych, które zapewniają metryki przestawiane wokół określonego obszaru problemu.  Ten artykuł zawiera informacje na temat instalowania i używania rozwiązań monitorowania.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 4006a3c66da9cb98db16b72da0b5fb7059affe22
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900657"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Monitorowanie rozwiązań w Azure Monitor
Rozwiązania do monitorowania wykorzystują usługi platformy Azure, aby zapewnić dodatkowe informacje o działaniu konkretnej aplikacji lub usługi. Ten artykuł zawiera krótkie omówienie rozwiązań monitorowania na platformie Azure i szczegółowe informacje dotyczące korzystania z nich i instalowania ich.

> [!NOTE]
> Rozwiązania do monitorowania były wcześniej określane jako rozwiązania do zarządzania.

Monitorowanie rozwiązań zazwyczaj zbiera dane dzienników i udostępnia zapytania i widoki umożliwiające analizowanie zebranych danych. Mogą również korzystać z innych usług, takich jak Azure Automation, do wykonywania działań związanych z aplikacją lub usługą.

Możesz dodać rozwiązania do monitorowania do Azure Monitor dla wszystkich używanych aplikacji i usług. Są one zazwyczaj dostępne bezpłatnie, ale zbierają dane, które mogą wywołać opłaty za użycie. Oprócz rozwiązań dostarczonych przez firmę Microsoft partnerzy i klienci mogą [tworzyć rozwiązania do zarządzania](solutions-creating.md) , które będą używane w ich własnym środowisku lub udostępniane klientom przez społeczność.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Używanie rozwiązań do monitorowania
Otwórz stronę **przeglądu** w Azure monitor, aby wyświetlić kafelek dla każdego rozwiązania zainstalowanego w obszarze roboczym. 

1. Zaloguj się w witrynie Azure Portal.
1. Otwórz **wszystkie usługi** i Zlokalizuj **monitor**.
1. W menu **Insights (szczegółowe** dane) wybierz pozycję **więcej**.
1. Użyj pól rozwijanych w górnej części ekranu, aby zmienić obszar roboczy lub zakres czasu używany dla kafelków.
1. Kliknij kafelek rozwiązania, aby otworzyć jego widok, który zawiera bardziej szczegółową analizę zebranych danych.

![Przegląd](media/solutions/overview.png)

Rozwiązania do monitorowania mogą zawierać wiele typów zasobów platformy Azure i można wyświetlić wszystkie zasoby dołączone do rozwiązania, tak jak każdy inny zasób. Na przykład wszystkie zapytania dzienników zawarte w rozwiązaniu są wymienione w obszarze **zapytania dotyczące rozwiązań** w [Eksploratorze zapytań](../log-query/get-started-portal.md#load-queries) , których można używać podczas przeprowadzania analizy ad hoc przy użyciu [zapytań dzienników](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Wyświetlanie listy zainstalowanych rozwiązań monitorowania 
Aby wyświetlić listę rozwiązań monitorowania zainstalowanych w ramach subskrypcji, należy wykonać poniższą procedurę.

1. Zaloguj się w witrynie Azure Portal.
1. Otwórz **wszystkie usługi** i Znajdź **rozwiązania**.
4. Zostaną wyświetlone rozwiązania zainstalowane we wszystkich Twoich obszarach roboczych. Po nazwie rozwiązania następuje nazwa obszaru roboczego, w którym jest zainstalowana.
1. Użyj pól listy rozwijanej w górnej części ekranu, aby filtrować według subskrypcji lub grupy zasobów.


![Wyświetl listę wszystkich rozwiązań](media/solutions/list-solutions-all.png)

Kliknij nazwę rozwiązania, aby otworzyć jego stronę podsumowania. Na tej stronie są wyświetlane wszystkie widoki zawarte w rozwiązaniu i dostępne są różne opcje dotyczące samego rozwiązania oraz jego obszaru roboczego. Zapoznaj się ze stroną podsumowania rozwiązania, korzystając z jednej z powyższych procedur, aby wyświetlić listę rozwiązań, a następnie kliknij nazwę rozwiązania.

![Właściwości rozwiązania](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Zainstaluj rozwiązanie do monitorowania
Rozwiązania do monitorowania od firmy Microsoft i partnerów są dostępne w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Dostępne rozwiązania można wyszukiwać i instalować przy użyciu poniższej procedury. W przypadku instalowania rozwiązania należy wybrać [obszar roboczy log Analytics](../platform/manage-access.md) , w którym zostanie zainstalowane rozwiązanie oraz gdzie będą zbierane dane.

1. Z [listy rozwiązań dla subskrypcji](#list-installed-monitoring-solutions)kliknij pozycję **Dodaj**.
1. Przeglądaj lub Wyszukaj rozwiązanie. Możesz również przeglądać rozwiązania z [tego linku wyszukiwania](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Znajdź odpowiednie rozwiązanie do monitorowania i przeczytaj jego opis.
1. Kliknij przycisk **Utwórz** , aby rozpocząć proces instalacji.
1. Po rozpoczęciu procesu instalacji zostanie wyświetlony monit o określenie obszaru roboczego Log Analytics i udostępnienie wymaganej konfiguracji rozwiązania.

![Instalowanie rozwiązania](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalowanie rozwiązania ze społeczności
Członkowie społeczności mogą przesyłać rozwiązania do zarządzania przy użyciu szablonów szybkiego startu platformy Azure. Te rozwiązania można zainstalować bezpośrednio lub pobrać szablony do późniejszej instalacji.

1. Postępuj zgodnie z procesem opisanym w [log Analytics obszarze roboczym i koncie usługi Automation](#log-analytics-workspace-and-automation-account) , aby połączyć obszar roboczy i konto.
2. Przejdź do [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/). 
3. Wyszukaj interesujące Cię rozwiązanie.
4. Wybierz rozwiązanie z wyników, aby wyświetlić jego szczegóły.
5. Kliknij przycisk **Wdróż na platformie Azure** .
6. Zostanie wyświetlony monit o podanie informacji, takich jak grupa zasobów i lokalizacja, oprócz wartości parametrów w rozwiązaniu.
7. Kliknij przycisk **Kup** , aby zainstalować rozwiązanie.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics obszaru roboczego i konta usługi Automation
Wszystkie rozwiązania do monitorowania wymagają [log Analytics obszar roboczy](../platform/manage-access.md) do przechowywania danych zbieranych przez rozwiązanie oraz do hostowania przeszukiwanych i widoków dzienników. Niektóre rozwiązania wymagają również, aby [konto usługi Automation](../../automation/automation-security-overview.md#automation-account-overview) zawierało elementy Runbook i powiązane zasoby. Obszar roboczy i konto muszą spełniać następujące wymagania.

* Każda instalacja rozwiązania może korzystać tylko z jednego obszaru roboczego Log Analytics i jednego konta usługi Automation. Rozwiązanie można zainstalować oddzielnie w wielu obszarach roboczych.
* Jeśli rozwiązanie wymaga konta usługi Automation, obszar roboczy Log Analytics i konto usługi Automation muszą być połączone ze sobą. Obszar roboczy Log Analytics może być połączony tylko z jednym kontem usługi Automation, a konto usługi Automation może być połączone tylko z jednym obszarem roboczym Log Analytics.
* Aby można było połączyć obszar roboczy Log Analytics i konto usługi Automation musi znajdować się w tej samej grupie zasobów i regionie. Wyjątkiem jest obszar roboczy w regionie Wschodnie stany USA i konto usługi Automation w regionach Wschodnie stany USA 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Utwórz łącze między obszarem roboczym Log Analytics i kontem usługi Automation
Sposób określania obszaru roboczego Log Analytics i konta usługi Automation zależy od metody instalacji rozwiązania.

* Po zainstalowaniu rozwiązania w portalu Azure Marketplace zostanie wyświetlony monit dotyczący obszaru roboczego i konta usługi Automation. Łącze między nimi jest tworzone, jeśli nie są jeszcze połączone.
* W przypadku rozwiązań poza portalem Azure Marketplace przed zainstalowaniem rozwiązania należy połączyć obszar roboczy Log Analytics i konto usługi Automation. Możesz to zrobić, wybierając dowolne rozwiązanie w witrynie Azure Marketplace i wybierając obszar roboczy Log Analytics i konto usługi Automation. Nie musisz faktycznie instalować rozwiązania, ponieważ link jest tworzony zaraz po wybraniu obszaru roboczego Log Analytics i konta usługi Automation. Po utworzeniu linku można użyć tego Log Analytics obszaru roboczego i konta usługi Automation dla dowolnego rozwiązania.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Weryfikowanie połączenia między obszarem roboczym Log Analytics i kontem usługi Automation
Aby sprawdzić połączenie między obszarem roboczym Log Analytics i kontem usługi Automation, należy wykonać poniższą procedurę.

1. Wybierz konto usługi Automation w Azure Portal.
1. Przewiń do sekcji **powiązane zasoby** menu.
1. Jeśli ustawienie **obszaru roboczego** jest włączone, to konto jest połączone z obszarem roboczym log Analytics. Możesz kliknąć pozycję **obszar roboczy** , aby wyświetlić szczegóły obszaru roboczego.

## <a name="remove-a-monitoring-solution"></a>Usuń rozwiązanie do monitorowania
Aby usunąć zainstalowane rozwiązanie, Znajdź je na [liście zainstalowanych rozwiązań](#list-installed-monitoring-solutions). Kliknij nazwę rozwiązania, aby otworzyć jego stronę podsumowania, a następnie kliknij przycisk **Usuń**.


## <a name="next-steps"></a>Następne kroki
* Pobierz [listę rozwiązań monitorowania od firmy Microsoft](solutions-inventory.md).
* Dowiedz się, jak [tworzyć zapytania](../log-query/log-query-overview.md) do analizowania danych zbieranych przez rozwiązania monitorujące.

