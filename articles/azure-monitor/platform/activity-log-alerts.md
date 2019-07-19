---
title: Alerty dziennika aktywności w Azure Monitor
description: Otrzymywanie powiadomień za pośrednictwem wiadomości SMS, elementu webhook, wiadomości SMS, poczty e-mail i innych informacji w przypadku wystąpienia określonych zdarzeń w dzienniku aktywności.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 61b5b96636ea12b5c63da657e006bd3121c34756
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67852609"
---
# <a name="alerts-on-activity-log"></a>Alerty dotyczące dziennika aktywności 

## <a name="overview"></a>Przegląd
Alerty dziennika aktywności są alertami, które uaktywniają się, gdy wystąpi nowe [zdarzenie dziennika aktywności](activity-log-schema.md) zgodne z warunkami określonymi w alercie. Reguła alertu będzie uruchamiana na podstawie kolejności i ilości zdarzeń zarejestrowanych w [dzienniku aktywności platformy Azure](activity-logs-overview.md). Reguły alertów dziennika aktywności to zasoby platformy Azure, dzięki czemu można je tworzyć przy użyciu szablonu Azure Resource Manager. Można je także tworzyć, aktualizować lub usuwać w Azure Portal. W tym artykule przedstawiono pojęcia dotyczące alertów dziennika aktywności. Aby uzyskać więcej informacji na temat tworzenia i używania reguł alertów dziennika aktywności, zobacz [tworzenie alertów dziennika aktywności i zarządzanie nimi](alerts-activity-log.md).

> [!NOTE]
> **Nie można** tworzyć alertów dla zdarzeń w kategorii alertów dziennika aktywności.

Zazwyczaj tworzysz alerty dziennika aktywności, aby otrzymywać powiadomienia, gdy:

* Określone operacje odbywają się na zasobach w ramach subskrypcji platformy Azure, często w zakresie określonych grup zasobów lub zasobów. Na przykład możesz chcieć otrzymywać powiadomienia, gdy dowolna maszyna wirtualna w myProductionResourceGroup zostanie usunięta. Możesz również otrzymywać powiadomienia o tym, czy nowe role są przypisane do użytkownika w ramach subskrypcji.
* Wystąpił zdarzenie kondycji usługi. Zdarzenia dotyczące kondycji usługi obejmują powiadomienie o zdarzeniach dotyczących zdarzeń konserwacji, które dotyczą zasobów w ramach subskrypcji.

Prostą analogową dla zrozumienie warunków, w których można tworzyć reguły alertów w dzienniku aktywności, jest Eksplorowanie lub filtrowanie zdarzeń za pośrednictwem [dziennika aktywności w Azure Portal](activity-log-view.md#azure-portal). W Dzienniku działania Azure Monitor jeden może odfiltrować lub znaleźć wymagane zdarzenie, a następnie utworzyć alert przy użyciu przycisku **Dodaj alert dziennika aktywności** .

W obu przypadkach alert dziennika aktywności jest monitorowany tylko dla zdarzeń w subskrypcji, w której jest tworzony alert.

Alert dziennika aktywności można skonfigurować na podstawie dowolnej właściwości najwyższego poziomu w obiekcie JSON dla zdarzenia dziennika aktywności. Aby uzyskać więcej informacji, zobacz [Omówienie dziennika aktywności platformy Azure](./activity-logs-overview.md#categories-in-the-activity-log). Aby dowiedzieć się więcej na temat zdarzeń usługi Service Health, zobacz [otrzymywanie alertów dziennika aktywności dla powiadomień dotyczących usług](./alerts-activity-log-service-notifications.md). 

Alerty dziennika aktywności mają kilka typowych opcji:

- **Kategoria**: Administracyjne, Service Health, automatyczne skalowanie, zabezpieczenia, zasady i zalecenia. 
- **Zakres**: Pojedynczy zasób lub zestaw zasobów, dla których zdefiniowano alert w dzienniku aktywności. Zakres alertu dziennika aktywności można zdefiniować na różnych poziomach:
    - Poziom zasobu: Na przykład dla określonej maszyny wirtualnej
    - Poziom grupy zasobów: Na przykład wszystkie maszyny wirtualne w określonej grupie zasobów
    - Poziom subskrypcji: Na przykład wszystkie maszyny wirtualne w subskrypcji (lub) wszystkie zasoby w subskrypcji
- **Grupa zasobów**: Domyślnie reguła alertu jest zapisywana w tej samej grupie zasobów co element docelowy zdefiniowany w zakresie. Użytkownik może również zdefiniować grupę zasobów, w której ma być przechowywana reguła alertu.
- **Typ zasobu**: Menedżer zasobów zdefiniowaną przestrzeń nazw dla elementu docelowego alertu.
- **Nazwa operacji**: Nazwa [operacji Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md) używana dla Access Control opartych na rolach. Operacji nie zarejestrowano w Azure Resource Manager nie można użyć w regule alertu dziennika aktywności.
- **Poziom**: Poziom ważności zdarzenia (verbose, informacyjny, ostrzegawczy, błąd lub krytyczny).
- **Stan**: Stan zdarzenia, zazwyczaj rozpoczęte, zakończone niepowodzeniem lub zakończone powodzeniem.
- **Zdarzenie zainicjowane przez**: Znana także jako "obiekt wywołujący". Adres e-mail lub identyfikator Azure Active Directory użytkownika, który wykonał operację.

> [!NOTE]
> W subskrypcji do 100 reguły alertów można utworzyć dla działania zakresu w jednym z nich: pojedynczy zasób, wszystkie zasoby w grupie zasobów (lub) cały poziom subskrypcji.

Po aktywowaniu alertu dziennika aktywności program używa grupy akcji do generowania akcji lub powiadomień. Grupa akcji to zbiór odbiorników powiadomień wielokrotnego użytku, takich jak adresy e-mail, adresy URL elementów webhook lub numery telefonów SMS. Odbiorcy mogą odwoływać się z wielu alertów w celu scentralizowania i grupowania kanałów powiadomień. Podczas definiowania alertu dziennika aktywności dostępne są dwie opcje. Możesz:

* Użyj istniejącej grupy akcji w alercie dziennika aktywności.
* Utwórz nową grupę akcji.

Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](action-groups.md).


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z omówieniem alertów](alerts-overview.md).
- Dowiedz się więcej o [tworzeniu i modyfikowaniu alertów dziennika aktywności](alerts-activity-log.md).
- Przejrzyj [schemat elementu webhook alertu dziennika aktywności](activity-log-alerts-webhook.md).
- Dowiedz się więcej o powiadomieniach o [kondycji usługi](service-notifications.md).