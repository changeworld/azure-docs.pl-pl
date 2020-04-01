---
title: Rozwiązania do monitorowania w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Rozwiązania do monitorowania w usłudze Azure Monitor to zbiór reguł logiki, wizualizacji i pozyskiwania danych, które zapewniają metryki przestawne wokół określonego obszaru problemu.  Ten artykuł zawiera informacje na temat instalowania i używania rozwiązań do monitorowania.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: c2690ad7cc4dcaa295bfb08b8c0396438ada0807
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437543"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Rozwiązania do monitorowania w usłudze Azure Monitor
Rozwiązania do monitorowania wykorzystują usługi na platformie Azure, aby zapewnić dodatkowy wgląd w działanie określonej aplikacji lub usługi. Ten artykuł zawiera krótkie omówienie rozwiązań monitorowania na platformie Azure i szczegółowe informacje na temat ich używania i instalowania.

> [!NOTE]
> Rozwiązania monitorujące były wcześniej określane jako rozwiązania do zarządzania.

Rozwiązania do monitorowania zazwyczaj zbierają dane dziennika i dostarczają zapytań i widoków do analizowania zebranych danych. Mogą również korzystać z innych usług, takich jak usługa Azure Automation do wykonywania akcji związanych z aplikacją lub usługą.

Rozwiązania do monitorowania można dodać do usługi Azure Monitor dla wszystkich używanych aplikacji i usług. Zazwyczaj są one dostępne bez żadnych kosztów, ale zbierają dane, które mogą wywoływać opłaty za użycie. Oprócz rozwiązań dostarczanych przez firmę Microsoft partnerzy i klienci mogą [tworzyć rozwiązania do zarządzania, które](solutions-creating.md) będą używane w ich własnym środowisku lub udostępniane klientom za pośrednictwem społeczności.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Używanie rozwiązań do monitorowania
Otwórz stronę **Przegląd** w usłudze Azure Monitor, aby wyświetlić kafelek dla każdego rozwiązania zainstalowanego w obszarze roboczym. 

1. Przejdź do [witryny Azure portal](https://ms.portal.azure.com). Wyszukaj i wybierz **pozycję Monitor**.
1. W menu **Statystyki** wybierz polecenie **Więcej**.
1. Użyj pól rozwijanych u góry ekranu, aby zmienić obszar roboczy lub zakres czasu używany dla kafelków.
1. Kliknij kafelek, aby otworzyć jego widok, który zawiera bardziej szczegółową analizę zebranych danych.

![Omówienie](media/solutions/overview.png)

Rozwiązania do monitorowania mogą zawierać wiele typów zasobów platformy Azure i można wyświetlać dowolne zasoby dołączone do rozwiązania, tak jak każdy inny zasób. Na przykład wszystkie zapytania dziennika zawarte w rozwiązaniu są wymienione w obszarze **Kwerendy rozwiązań** w [Eksploratorze zapytań](../log-query/get-started-portal.md#load-queries) Można użyć tych kwerend podczas wykonywania analizy ad hoc za pomocą [zapytań dziennika](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lista zainstalowanych rozwiązań do monitorowania 
Poniższa procedura służy do listy rozwiązań monitorowania zainstalowanych w ramach subskrypcji.

1. Przejdź do [witryny Azure portal](https://ms.portal.azure.com). Wyszukaj i wybierz **rozwiązania**.
1. Wymienione są rozwiązania zainstalowane we wszystkich obszarach roboczych. Po nazwie rozwiązania następuje nazwa obszaru roboczego, w którym jest zainstalowany.
1. Użyj pól rozwijanych u góry ekranu, aby filtrować według subskrypcji lub grupy zasobów.


![Lista wszystkich rozwiązań](media/solutions/list-solutions-all.png)

Kliknij nazwę rozwiązania, aby otworzyć jego stronę podsumowania. Ta strona wyświetla wszystkie widoki zawarte w rozwiązaniu i zawiera różne opcje dla samego rozwiązania i jego obszaru roboczego. Wyświetl stronę podsumowania rozwiązania przy użyciu jednej z powyższych procedur, aby wyświetlić listę rozwiązań, a następnie kliknij nazwę rozwiązania.

![Właściwości rozwiązania](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Instalowanie rozwiązania do monitorowania
Rozwiązania do monitorowania firmy Microsoft i partnerów są dostępne w portalu [Azure Marketplace.](https://azuremarketplace.microsoft.com) Można wyszukiwać dostępne rozwiązania i instalować je przy użyciu poniższej procedury. Po zainstalowaniu rozwiązania należy wybrać [obszar roboczy usługi Log Analytics,](../platform/manage-access.md) w którym zostanie zainstalowane rozwiązanie i gdzie będą zbierane jego dane.

1. Z [listy rozwiązań dla subskrypcji](#list-installed-monitoring-solutions)kliknij pozycję **Dodaj**.
1. Przeglądaj lub szukaj rozwiązania. Możesz również przeglądać rozwiązania z [tego linku wyszukiwania](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Znajdź rozwiązanie do monitorowania, które chcesz i przeczytaj jego opis.
1. Kliknij **przycisk Utwórz,** aby rozpocząć proces instalacji.
1. Po uruchomieniu procesu instalacji zostanie wyświetlony monit o określenie obszaru roboczego usługi Log Analytics i podanie dowolnej wymaganej konfiguracji dla rozwiązania.

![Instalowanie rozwiązania](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalowanie rozwiązania od społeczności
Członkowie społeczności mogą przesyłać rozwiązania do zarządzania do szablonów szybki start platformy Azure. Można zainstalować te rozwiązania bezpośrednio lub pobrać je szablony do późniejszej instalacji.

1. Postępuj zgodnie z procesem opisanym w [obszarze roboczym usługi Log Analytics i kontie automatyzacji,](#log-analytics-workspace-and-automation-account) aby połączyć obszar roboczy i konto.
2. Przejdź do [szablonów szybki start platformy Azure](https://azure.microsoft.com/documentation/templates/). 
3. Wyszukaj rozwiązanie, które Cię interesuje.
4. Wybierz rozwiązanie z wyników, aby wyświetlić jego szczegóły.
5. Kliknij przycisk **Wdrażanie na platformie Azure.**
6. Zostanie wyświetlony monit o podanie informacji, takich jak grupa zasobów i lokalizacja oprócz wartości dla wszystkich parametrów w rozwiązaniu.
7. Kliknij **przycisk Zakup,** aby zainstalować rozwiązanie.


## <a name="log-analytics-workspace-and-automation-account"></a>Obszar roboczy usługi Log Analytics i konto automatyzacji
Wszystkie rozwiązania monitorowania wymagają [obszaru roboczego usługi Log Analytics](../platform/manage-access.md) do przechowywania danych zebranych przez rozwiązanie i hostowania jego wyszukiwania dziennika i widoków. Niektóre rozwiązania wymagają również [konta automatyzacji,](../../automation/automation-security-overview.md#automation-account-overview) aby zawierać elementy runbook i powiązanych zasobów. Obszar roboczy i konto muszą spełniać następujące wymagania.

* Każda instalacja rozwiązania może używać tylko jednego obszaru roboczego usługi Log Analytics i jednego konta automatyzacji. Rozwiązanie można zainstalować oddzielnie w wielu obszarach roboczych.
* Jeśli rozwiązanie wymaga konta automatyzacji, a następnie obszaru roboczego usługi Log Analytics i konta automatyzacji muszą być połączone ze sobą. Obszar roboczy usługi Log Analytics może być połączony tylko z jednym kontem automatyzacji, a konto automatyzacji może być połączone tylko z jednym obszarem roboczym usługi Log Analytics.
* Aby można było połączyć, obszar roboczy usługi Log Analytics i konto automatyzacji muszą znajdować się w tej samej grupie zasobów i regionie. Wyjątkiem jest obszar roboczy w regionie wschodnich stanów USA i konto automatyzacji we wschodnich stanach USA 2.

Po zainstalowaniu rozwiązania za pośrednictwem portalu Azure Marketplace zostanie wyświetlony monit o podanie konta obszaru roboczego i usługi Automatyzacja. Łącze między nimi jest tworzone, jeśli nie są jeszcze połączone.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Weryfikowanie łącza między obszarem roboczym usługi Log Analytics a kontem automatyzacji
Można zweryfikować łącze między obszarem roboczym usługi Log Analytics i kontem automatyzacji, wykonując poniższą procedurę.

1. Wybierz konto automatyzacji w witrynie Azure portal.
1. Przewiń do sekcji **Zasoby pokrewne** w menu.
1. Jeśli ustawienie **Obszar roboczy** jest włączone, to konto jest połączone z obszarem roboczym usługi Log Analytics. Możesz kliknąć **obszar roboczy,** aby wyświetlić szczegóły obszaru roboczego.

## <a name="remove-a-monitoring-solution"></a>Usuwanie rozwiązania do monitorowania
Aby usunąć zainstalowane rozwiązanie, znajdź je na [liście zainstalowanych rozwiązań](#list-installed-monitoring-solutions). Kliknij nazwę rozwiązania, aby otworzyć jego stronę podsumowania, a następnie kliknij **przycisk Usuń**.


## <a name="next-steps"></a>Następne kroki
* Pobierz [listę rozwiązań do monitorowania firmy Microsoft](solutions-inventory.md).
* Dowiedz się, jak [tworzyć zapytania](../log-query/log-query-overview.md) do analizowania danych zebranych przez rozwiązania monitorowania.

