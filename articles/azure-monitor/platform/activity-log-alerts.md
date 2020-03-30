---
title: Alerty dziennika aktywności w usłudze Azure Monitor
description: Bądź powiadamiany za pośrednictwem wiadomości SMS, webhook, SMS, e-mail i innych, gdy pewne zdarzenia występują w dzienniku aktywności.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 26ecfdb33b92c91010af63ec14089dd148d6bad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669017"
---
# <a name="alerts-on-activity-log"></a>Alerty w dzienniku aktywności

## <a name="overview"></a>Omówienie

Alerty dziennika aktywności są alerty, które aktywują się po wystąpieniu nowego [zdarzenia dziennika aktywności,](activity-log-schema.md) które spełnia warunki określone w alecie. Na podstawie kolejności i głośności zdarzeń zarejestrowanych w [dzienniku aktywności platformy Azure](platform-logs-overview.md)reguła alertu zostanie podpalona. Reguły alertów dziennika aktywności to zasoby platformy Azure, dzięki czemu można je utworzyć przy użyciu szablonu usługi Azure Resource Manager. Można je również tworzyć, aktualizować lub usuwać w witrynie Azure portal. W tym artykule przedstawiono pojęcia dotyczące alertów dziennika aktywności. Aby uzyskać więcej informacji na temat tworzenia lub używania reguł alertów dziennika aktywności, zobacz [Tworzenie alertów dziennika aktywności i zarządzanie nimi](alerts-activity-log.md).

> [!NOTE]
> **Alerty nie można** utworzyć dla zdarzeń w kategorii alert dziennika aktywności.

Zazwyczaj można utworzyć alerty dziennika aktywności, aby otrzymywać powiadomienia, gdy:

* Określone operacje występują na zasoby w subskrypcji platformy Azure, często ograniczone do określonych grup zasobów lub zasobów. Na przykład można powiadomić o usunięciu dowolnej maszyny wirtualnej w myProductionResourceGroup. Lub może chcesz otrzymywać powiadomienia, jeśli nowe role są przypisane do użytkownika w ramach subskrypcji.
* Występuje zdarzenie kondycji usługi. Zdarzenia kondycji usługi obejmują powiadamianie o zdarzeniach i zdarzeniach konserwacji, które mają zastosowanie do zasobów w ramach subskrypcji.

Prostą analogią do zrozumienia warunków, na których można tworzyć reguły alertów w dzienniku aktywności, jest eksplorowanie lub filtrowanie zdarzeń za pomocą [dziennika aktywności w witrynie Azure portal.](activity-log-view.md#azure-portal) W usłudze Azure Monitor — dziennik aktywności można filtrować lub znaleźć niezbędne zdarzenie, a następnie utworzyć alert przy użyciu przycisku **Dodaj alert dziennika aktywności.**

W obu przypadkach alert dziennika aktywności monitoruje tylko zdarzenia w subskrypcji, w której tworzony jest alert.

Można skonfigurować alert dziennika aktywności na podstawie dowolnej właściwości najwyższego poziomu w obiekcie JSON dla zdarzenia dziennika aktywności. Aby uzyskać więcej informacji, zobacz [Kategorie w dzienniku aktywności](activity-log-view.md#categories-in-the-activity-log). Aby dowiedzieć się więcej o zdarzeniach kondycji usługi, zobacz [Odbieranie alertów dziennika aktywności w powiadomieniach o usługach](alerts-activity-log-service-notifications.md). 

Alerty dziennika aktywności mają kilka typowych opcji:

- **Kategoria:** Administracja, Kondycja usługi, Skalowanie automatyczne, Zabezpieczenia, Zasady i Zalecenia. 
- **Zakres:** Pojedynczy zasób lub zestaw zasobów, dla których zdefiniowano alert w dzienniku działań. Zakres alertu dziennika aktywności można zdefiniować na różnych poziomach:
    - Poziom zasobu: Na przykład dla określonej maszyny wirtualnej
    - Poziom grupy zasobów: Na przykład wszystkie maszyny wirtualne w określonej grupie zasobów
    - Poziom subskrypcji: Na przykład wszystkie maszyny wirtualne w subskrypcji (lub) wszystkie zasoby w ramach subskrypcji
- **Grupa zasobów:** Domyślnie reguła alertu jest zapisywana w tej samej grupie zasobów, co reguła docelowa zdefiniowana w zakresie. Użytkownik może również zdefiniować grupę zasobów, w której powinna być przechowywana reguła alertu.
- **Typ zasobu:** Obszar nazw zdefiniowany przez Menedżera zasobów dla obiektu docelowego alertu.
- **Nazwa operacji:** Nazwa [operacji usługi Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md) wykorzystywana do kontroli dostępu opartej na rolach . Operacje niezarejestrowane w usłudze Azure Resource Manager nie mogą być używane w regule alertu dziennika aktywności.
- **Poziom:** Poziom ważności zdarzenia (pełny, informacyjny, ostrzegawczy, błąd lub krytyczny).
- **Stan:** Stan zdarzenia, zazwyczaj rozpoczęty, nie powiodło się lub powiodło się.
- **Zdarzenie zainicjowane przez**: Znany również jako "wywołujący". Adres e-mail lub identyfikator usługi Azure Active Directory użytkownika, który wykonał operację.

> [!NOTE]
> W ramach subskrypcji można utworzyć maksymalnie 100 reguł alertów dla działania o zakresie w: pojedynczym zasobie, wszystkie zasoby w grupie zasobów (lub) cały poziom subskrypcji.

Po aktywowaniu alertu dziennika aktywności używa grupy akcji do generowania akcji lub powiadomień. Grupa akcji to zestaw odbiorników powiadomień wielokrotnego dostępu, takich jak adresy e-mail, adresy URL programu Webhook lub numery telefonów SMS. Do odbiorników można odwoływać się z wielu alertów, aby scentralizować i pogrupować kanały powiadomień. Podczas definiowania alertu dziennika aktywności, masz dwie opcje. Możesz:

* Użyj istniejącej grupy akcji w alertie dziennika aktywności.
* Utwórz nową grupę akcji.

Aby dowiedzieć się więcej o grupach akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal](action-groups.md).


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z omówieniem alertów](alerts-overview.md).
- Dowiedz się więcej o [tworzeniu i modyfikowaniu alertów dziennika aktywności](alerts-activity-log.md).
- Przejrzyj [schemat programu webhook alertu dziennika aktywności](activity-log-alerts-webhook.md).
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](service-notifications.md).
