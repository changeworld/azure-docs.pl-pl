---
title: Tworzenie grup i zarządzanie nimi akcji w witrynie Azure portal
description: Dowiedz się, jak tworzyć grupy i zarządzać nimi akcji w witrynie Azure portal.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 3ce7c5111fa176bb7fa734f54084b9e14e7afbef
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016050"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Tworzenie grup i zarządzanie nimi akcji w witrynie Azure portal
## <a name="overview"></a>Przegląd ##
Grupy akcji to zbiór preferencje powiadamiania zdefiniowana przez użytkownika. Alerty monitorowania i kondycji usług platformy Azure są skonfigurowane do używania grupy określonej akcji po wyzwoleniu alertu. Różne alerty może używać tej samej grupy akcji lub grupy inną akcję w zależności od wymagań użytkownika.

W tym artykule przedstawiono sposób tworzenia grup i zarządzanie nimi akcji w witrynie Azure portal.

Każda akcja składa się z następującymi właściwościami:

* **Nazwa**: identyfikator unikatowy w obrębie grupy akcji.  
* **Typ akcji**: Akcja do wykonania. Przykłady obejmują wysyłanie pocztą e-mail wywołanie, wiadomość SMS, głos; lub różnego rodzaju akcje automatyczne wyzwalanie. Zobacz typy w dalszej części tego artykułu. 
* **Szczegóły**: odpowiednimi szczegółami, które różnią się zależnie od *typ akcji*. 

Aby uzyskać informacje na temat konfigurowania grup akcji przy użyciu szablonów usługi Azure Resource Manager, zobacz [szablonów usługi Resource Manager grupy akcji](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Tworzenie grupy akcji przy użyciu witryny Azure portal ##
1. W [portal](https://portal.azure.com), wybierz opcję **Monitor**. **Monitor** bloku konsoliduje wszystkie ustawienia monitorowania i danych w jednym widoku.

    ![Usługa "Monitor"](./media/monitoring-action-groups/home-monitor.png)
1. Wybierz **alerty** polecenie **Zarządzanie grupami działań**.

    ![Zarządzanie przycisk grupy akcji](./media/monitoring-action-groups/manage-action-groups.png)
1. Wybierz **Dodaj grupę akcji**, a następnie wypełnij pola.

    ![Polecenie "Dodaj grupę akcji"](./media/monitoring-action-groups/add-action-group.png)
1. Wprowadź nazwę w **Nazwa grupy akcji** polu, a następnie wprowadź nazwę w **krótką nazwę** pole. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

      ![Okno dialogowe Dodawanie grupy akcji"](./media/monitoring-action-groups/action-group-define.png)

1. **Subskrypcji** polu autofills przy użyciu Twojej bieżącej subskrypcji. Ta subskrypcja jest ten, w którym jest zapisany do grupy akcji.

1. Wybierz **grupy zasobów** w są zapisywane do grupy akcji.

1. Zdefiniuj listę akcji, podając każdej akcji:

    a. **Nazwa**: wprowadź unikatowy identyfikator dla tej akcji.

    b. **Typ akcji**: Wybierz adres E-mail/SMS/wypychania/rejestr, aplikację logiki, element Webhook, ITSM lub elementu Runbook usługi Automation.

    c. **Szczegóły**: oparte na typ akcji, wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook, aplikacja platformy Azure, połączenia narzędzia ITSM lub elementu runbook usługi Automation. Dla akcji ITSM, należy również określić **elementu roboczego** i wymaga narzędziem ITSM, inne pola.

1. Wybierz **OK** można utworzyć grupy akcji.

## <a name="manage-your-action-groups"></a>Zarządzanie grupami działań ##
Po utworzeniu grupy akcji, jest ona widoczna na **grup akcji** części **Monitor** bloku. Wybierz grupę akcji, które mają być zarządzane do:

* Dodawanie, edytowanie lub usuwanie akcji.
* Usuń grupę akcji.

## <a name="action-specific-information"></a>Informacje o określonej akcji
**Aplikacja platformy Azure wypychania** — może mieć maksymalnie 10 akcje aplikacji platformy Azure w grupy akcji. W tej chwili akcji aplikacji platformy Azure obsługuje tylko ServiceHealth alertów. Dowolnym innym czasie alertu zostaną zignorowane. Zobacz [Konfigurowanie alertów po każdym opublikowaniu powiadomienia kondycji usługi](monitoring-activity-log-alerts-on-service-notifications.md).

**Adres e-mail** — wiadomości E-mail będą wysyłane z następujących adresów e-mail. Upewnij się, że filtrowanie wiadomości e-mail jest prawidłowo skonfigurowany
   - azure-noreply@microsoft.com
   - azureemail-noreply@microsoft.com
   - alerts-noreply@mail.windowsazure.com

Może mieć maksymalnie 1000 akcji poczty e-mail do grupy akcji. Zobacz [ograniczania informacje o szybkości](./monitoring-alerts-rate-limiting.md) artykułu

**ITSM** — może mieć maksymalnie 10 akcje ITSM w akcji ITSM grupy akcji wymaga połączenia ITSM. Dowiedz się, jak utworzyć [połączenia narzędzia ITSM](../log-analytics/log-analytics-itsmc-overview.md).

**Aplikacji logiki** — może mieć maksymalnie 10 akcji aplikacji logiki w grupy akcji

**Element Runbook** — może mieć maksymalnie 10 elementów Runbook akcji, w akcji grupy dotyczą [limity usług subskrypcji platformy Azure](../azure-subscription-service-limits.md) limity ładunków elementu Runbook

**SMS** — może mieć maksymalnie 10 akcje programu SMS w akcji grupy, zobacz [ograniczania informacje o szybkości](./monitoring-alerts-rate-limiting.md) artykuł zobacz [SMS alert zachowanie](monitoring-sms-alert-behavior.md) artykułu

**Głos** — może mieć maksymalnie 10 akcje głosu w grupy akcji</dd>
Zobacz [ograniczania informacje o szybkości](./monitoring-alerts-rate-limiting.md) artykułu</dd>

**Element Webhook** — może mieć maksymalnie 10 Akcje elementu Webhook w grupy akcji. Logika ponawiania próby — limit czasu dla odpowiedzi to 10 sekund. Wywołanie elementu webhook zostanie ponowiona maksymalnie 2 godziny po następujące kody stanu HTTP są zwracane: 408, 429, 503, 504 lub punkt końcowy HTTP nie odpowiada. Pierwszym ponowieniem próby odbywa się po 10 sekundach. Drugi i ostatniego ponownych prób odbywa się po 100 sekund.

Zakresy adresów IP źródła
    - 13.106.57.181
    - 13.106.54.3
    - 13.106.54.19
    - 13.106.38.142
    - 13.106.38.148
    - 13.106.57.196

Aby otrzymywać aktualizacje o zmianach na te adresy IP, zaleca się konfigurowania [alertów dotyczących kondycji usługi](./monitoring-service-notifications.md) który monitoruje informacyjny powiadomień dotyczących usługi grupy akcji.


## <a name="next-steps"></a>Kolejne kroki ##
* Dowiedz się więcej o [SMS alert zachowanie](monitoring-sms-alert-behavior.md).  
* Uzyskaj [zrozumieć schemat elementów webhook alertu dziennika aktywności](monitoring-activity-log-alerts-webhook.md).  
* Dowiedz się więcej o [łącznik ITSM](../log-analytics/log-analytics-itsmc-overview.md)
* Dowiedz się więcej o [szybkości](monitoring-alerts-rate-limiting.md) alerty.
* Pobierz [Przegląd alertów dziennika aktywności](monitoring-overview-alerts.md)i Dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [Konfigurowanie alertów po każdym opublikowaniu powiadomienia kondycji usługi](monitoring-activity-log-alerts-on-service-notifications.md).
