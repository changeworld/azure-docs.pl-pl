---
title: Alerty dziennika aktywności w usłudze Azure Monitor
description: Powiadamiane za pośrednictwem wiadomości SMS, elementu webhook, wiadomość SMS, wiadomości e-mail i bardziej, gdy wystąpią pewne zdarzenia w dzienniku aktywności.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 5d0819f71405b1bf1d4bef57a8b93d57bc879087
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244968"
---
# <a name="alerts-on-activity-log"></a>Alerty dotyczące dzienników aktywności 

## <a name="overview"></a>Omówienie
Alerty dzienników aktywności są alerty, które aktywują po wystąpieniu nowego zdarzenia dziennika aktywności, który spełnia warunki określone w alercie. Są one zasobów platformy Azure, dzięki czemu mogą być tworzone przy użyciu szablonu usługi Azure Resource Manager. One również mogą być tworzone, zaktualizował lub usunął w witrynie Azure portal. Ten artykuł wprowadza pojęcia dotyczące alertów dziennika aktywności. Go następnie pokazano, jak skonfigurować alertów dla zdarzenia dziennika aktywności przy użyciu witryny Azure portal. Aby uzyskać więcej informacji na temat użycia, zobacz [tworzenie i zarządzanie nimi alertów dziennika aktywności](alerts-activity-log.md).

> [!NOTE]
> Alerty **nie** można utworzyć dla zdarzeń w kategorii alertu dziennika aktywności.

Zazwyczaj można Tworzenie alertów dziennika aktywności do odbierania powiadomień po:

* Określone operacje są wykonywane na zasoby w subskrypcji platformy Azure, często są ograniczone do określonego zasobu, grupy lub zasobów. Na przykład można otrzymywać powiadomienia, gdy każda maszyna wirtualna w myProductionResourceGroup zostanie usunięty. Możesz też otrzymywać powiadomienia, gdy wszystkie nowe role są przypisane do użytkownika w ramach subskrypcji.
* Występuje zdarzenie kondycji usługi. Zdarzenia usługi Service health zawierają powiadomienie o zdarzeń i zdarzeń konserwacji, które są stosowane do zasobów w ramach subskrypcji.

Prosty sposób analogiczny opis warunki, na których można tworzyć reguły alertu w dzienniku aktywności jest aby eksplorować lub filtrowanie zdarzeń za pomocą [dziennika aktywności w witrynie Azure portal](activity-log-view.md#azure-portal). W usłudze Azure Monitor — dziennik aktywności jeden można filtrować lub znaleźć wymaganych zdarzeń i następnie utworzyć alert przy użyciu **Dodaj alert dziennika aktywności** przycisku.

W obu przypadkach alertu dziennika aktywności monitoruje tylko w przypadku zdarzeń w ramach subskrypcji, w której jest tworzony alert.

Można skonfigurować alertu dziennika aktywności, na podstawie dowolnej właściwości najwyższego poziomu w obiekcie JSON dla zdarzenia dziennika aktywności. Aby uzyskać więcej informacji, zobacz [Przegląd dziennika aktywności platformy Azure](./activity-logs-overview.md#categories-in-the-activity-log). Aby dowiedzieć się więcej na temat zdarzenia usługi service health, zobacz [odbieranie alertów dziennika aktywności dla powiadomień dotyczących usług](./alerts-activity-log-service-notifications.md). 

Alerty dziennika aktywności skorzystać z kilku typowych opcji:

- **Kategoria**: Administracyjne, usługa kondycji, automatycznego skalowania, zabezpieczeń, zasad i zalecenia. 
- **Zakres**: Pojedynczy zasób lub zestaw zasobów, dla którego zdefiniowano alertu w dzienniku aktywności. Zakres dla alertu dziennika aktywności można zdefiniować na różnych poziomach:
    - Poziom zasobu: Na przykład dla konkretnej maszyny wirtualnej
    - Poziomu grupy zasobów: Na przykład wszystkich maszyn wirtualnych w określonej grupie zasobów
    - Poziom subskrypcji: Na przykład wszystkie maszyny wirtualne w ramach subskrypcji (lub) wszystkich zasobów w ramach subskrypcji
- **Grupa zasobów**: Domyślnie reguła alertu jest zapisywany w tej samej grupie zasobów, jak w przypadku docelowej zdefiniowany w zakresie. Użytkownika można również definiować grupy zasobów, przechowywania reguły alertu.
- **Typ zasobu**: Menedżer zasobów zdefiniowane przestrzeni nazw jako cel alertu.

- **Nazwa operacji**: Nazwa operacji kontroli dostępu opartej na roli Menedżera zasobów.
- **Poziom**: Poziom ważności zdarzenia (pełne, komunikat o charakterze informacyjnym, ostrzeżenie, błąd lub krytyczny).
- **Stan**: Stan zdarzenia, zwykle jest uruchomiona, nie powiodło się lub zakończyło się pomyślnie.
- **Zdarzenie zainicjowane przez**: Znany także jako "obiekt wywołujący." Adres e-mail lub usługi Azure Active Directory identyfikator użytkownika, który wykonał operację.

> [!NOTE]
> W ramach subskrypcji można utworzyć maksymalnie 100 reguł alertów dla działania zakresu na poziomie: pojedynczego zasobu, wszystkie zasoby w grupy (lub) poziom całej subskrypcji.

Po aktywowaniu alertu dziennika aktywności używa grupy akcji do wygenerowania powiadomienia lub akcji. Grupy akcji to zbiór wielokrotnego użytku odbiorców powiadomień, takie jak adresy e-mail, numerów telefonów adresy URL elementu webhook lub wysyłać wiadomości SMS. Odbiorniki mogą być przywoływane z wielu alertów, które scentralizowane i grupach kanałów powiadomień. Podczas definiowania alertu dziennika aktywności, masz dwie opcje. Możesz:

* Użyj istniejącej grupy akcji w alertu dziennika aktywności.
* Utwórz nową grupę akcji.

Aby dowiedzieć się więcej o grupach akcji, zobacz [tworzenie grup akcji w witrynie Azure portal i zarządzanie nimi](action-groups.md).


## <a name="next-steps"></a>Kolejne kroki
- Pobierz [Przegląd alertów](alerts-overview.md).
- Dowiedz się więcej o [tworzenie i modyfikowanie alertów dziennika aktywności](alerts-activity-log.md).
- Przegląd [schemat elementów webhook alertu dziennika aktywności](activity-log-alerts-webhook.md).
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](service-notifications.md).


