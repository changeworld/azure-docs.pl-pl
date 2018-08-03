---
title: Tworzenie alertów dziennika aktywności klasycznego
description: Powiadamiane za pośrednictwem wiadomości SMS, element webhook i wiadomości e-mail po wystąpieniu określonego zdarzenia w dzienniku aktywności.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/18/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 120fd3552ad36b3d19179f39ca95ce2b3ee2c2e6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426622"
---
# <a name="create-activity-log-alerts-classic"></a>Utwórz działanie alertów dzienników (wersja klasyczna)

## <a name="overview"></a>Przegląd
Alerty dzienników aktywności są alerty, które aktywują po wystąpieniu nowego zdarzenia dziennika aktywności, który spełnia warunki określone w alercie. Są one zasobów platformy Azure, dzięki czemu mogą być tworzone przy użyciu szablonu usługi Azure Resource Manager. One również mogą być tworzone, zaktualizował lub usunął w witrynie Azure portal. Ten artykuł wprowadza pojęcia dotyczące alertów dziennika aktywności. Go następnie pokazano, jak skonfigurować alertów dla zdarzenia dziennika aktywności przy użyciu witryny Azure portal.

> [!NOTE]

>  Nowy [alerty](monitoring-overview-unified-alerts.md) środowisko została zastąpiona tej procedury. W tym artykule podano jako odwołanie dla poprzednie środowisko. [Dowiedz się więcej](monitoring-activity-log-alerts-new-experience.md).

Zazwyczaj można Tworzenie alertów dziennika aktywności do odbierania powiadomień po:

* Określone zmiany zostaną wprowadzone w zasobach w subskrypcji platformy Azure, często są ograniczone do określonego zasobu, grupy lub zasobów. Na przykład można otrzymywać powiadomienia, gdy każda maszyna wirtualna w myProductionResourceGroup zostanie usunięty. Możesz też otrzymywać powiadomienia, gdy wszystkie nowe role są przypisane do użytkownika w ramach subskrypcji.
* Występuje zdarzenie kondycji usługi. Zdarzenia usługi Service health zawierają powiadomienie o zdarzeń i zdarzeń konserwacji, które są stosowane do zasobów w ramach subskrypcji.

W obu przypadkach alertu dziennika aktywności monitoruje tylko w przypadku zdarzeń w ramach subskrypcji, w której jest tworzony alert.

Można skonfigurować alertu dziennika aktywności, na podstawie dowolnej właściwości najwyższego poziomu w obiekcie JSON dla zdarzenia dziennika aktywności. Jednak w portalu jest wyświetlany najbardziej typowe opcje:

- **Kategoria**: administracyjne, usługa kondycji, automatycznego skalowania i zalecenia. Aby uzyskać więcej informacji, zobacz [Przegląd dziennika aktywności platformy Azure](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Aby dowiedzieć się więcej na temat zdarzenia usługi service health, zobacz [odbieranie alertów dziennika aktywności dla powiadomień dotyczących usług](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Grupa zasobów**
- **Zasób**
- **Typ zasobu**
- **Nazwa operacji**: Nazwa operacji The Resource Manager Role-Based kontroli dostępu.
- **Poziom**: poziom ważności zdarzenia (pełne, komunikat o charakterze informacyjnym, ostrzeżenie, błąd lub krytyczny).
- **Stan**: stan zdarzeń, zwykle jest uruchomiona, nie powiodło się lub zakończyło się pomyślnie.
- **Zdarzenie zainicjowane przez**: znany także jako "obiekt wywołujący." Adres e-mail lub usługi Azure Active Directory identyfikator użytkownika, który wykonał operację.

> [!NOTE]
> Po "administracyjne" kategorii, należy określić co najmniej jednego z poprzednich kryteriów w alertu. Nie można utworzyć alert, który aktywuje za każdym razem, gdy zdarzenie zostanie utworzone w dziennikach aktywności.

Po aktywowaniu alertu dziennika aktywności używa grupy akcji do wygenerowania powiadomienia lub akcji. Grupy akcji to zbiór wielokrotnego użytku odbiorców powiadomień, takie jak adresy e-mail, numerów telefonów adresy URL elementu webhook lub wysyłać wiadomości SMS. Odbiorniki mogą być przywoływane z wielu alertów, które scentralizowane i grupach kanałów powiadomień. Podczas definiowania alertu dziennika aktywności, masz dwie opcje. Możesz:

* Użyj istniejącej grupy akcji w alertu dziennika aktywności.
* Utwórz nową grupę akcji.

Aby dowiedzieć się więcej o grupach akcji, zobacz [tworzenie grup akcji w witrynie Azure portal i zarządzanie nimi](monitoring-action-groups.md).

Aby dowiedzieć się więcej na temat powiadomień dotyczących kondycji usługi, zobacz [odbieranie alertów dziennika aktywności dla powiadomień dotyczących kondycji usług](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-classic-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Tworzenie alertu (model klasyczny) na zdarzenia dziennika aktywności przy użyciu nowej grupy akcji przy użyciu witryny Azure portal
1. W [portal](https://portal.azure.com), wybierz opcję **Monitor**.

    ![Usługa "Monitor"](./media/monitoring-activity-log-alerts/home-monitor.png)
1. W **dziennika aktywności** zaznacz **alerty (klasyczne)**.

    ![Na karcie "Alerty"](./media/monitoring-activity-log-alerts/alerts-blades.png)
1. Wybierz **Dodaj alert dziennika aktywności**, a następnie wypełnij pola.

1. Wprowadź nazwę w **nazwa alertu dziennika aktywności** i zaznacz **opis**.

    ![Polecenie "Dodaj alert dziennika aktywności"](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

1. **Subskrypcji** polu autofills przy użyciu Twojej bieżącej subskrypcji. Ta subskrypcja jest ten, w którym jest zapisany do grupy akcji. Alert zasobów jest wdrożony w tej subskrypcji i monitoruje zdarzenia dziennika aktywności z niego.

    ![Okno dialogowe "Dodaj alert dziennika aktywności"](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

1. Wybierz **grupy zasobów** w którym zostanie utworzony alert zasobów. Nie jest grupa zasobów, który jest monitorowany przez alert. Zamiast tego jest grupa zasobów, w którym znajduje się zasób alertu.

1. Opcjonalnie można zaznaczyć **kategorii zdarzeń** zmodyfikować dodatkowe filtry, które są wyświetlane. Zdarzenia administracyjne, można użyć **grupy zasobów**, **zasobów**, **typ zasobu**, **nazwy operacji**, **Poziom**, **stan**, i **zdarzenie zainicjowane przez**. Te wartości identyfikują zdarzenia, które należy monitorować ten alert.

    >[!NOTE]
    >Należy określić co najmniej jednego z poprzednich kryteriów w alertu. Nie można utworzyć alert, który aktywuje za każdym razem, gdy zdarzenie zostanie utworzone w dziennikach aktywności.
    >
    >

1. Wprowadź nazwę w **Nazwa grupy akcji** polu, a następnie wprowadź nazwę w **krótką nazwę** pole. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

1.  Zdefiniuj listę akcji, podając akcji:

    a. **Nazwa**: Wprowadź nazwę, alias lub identyfikator akcji.

    b. **Typ akcji**: Wybierz wiadomości SMS, wiadomości e-mail lub elementu webhook.

    c. **Szczegóły**: oparte na typ akcji, wprowadź numer telefonu, adres e-mail lub identyfikator URI elementu webhook.

1.  Wybierz **OK** do utworzenia alertu.

Alert zajmuje kilka minut, w pełni Propagacja i stanie się aktywna. Uruchamia go, gdy nowe zdarzenia spełniających kryteria alertu.

Aby uzyskać więcej informacji, zobacz [zrozumieć schemat elementów webhook, używane w alertów dziennika aktywności](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Grupa akcji zdefiniowane w ramach tej procedury jest do ponownego użycia istniejącej grupy akcji dla wszystkich przyszłych definicji alertu.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Tworzenie alertów dotyczących zdarzenia dziennika aktywności dla istniejącej grupy akcji przy użyciu witryny Azure portal
1. Wykonaj kroki od 1 do 7 w poprzedniej sekcji, aby utworzyć alertu dziennika aktywności.

1. W obszarze **powiadomienia**, wybierz opcję **istniejące** przycisk grupy akcji. Wybierz istniejącą grupę akcji z listy.

1. Wybierz **OK** do utworzenia alertu.

Alert zajmuje kilka minut, w pełni Propagacja i stanie się aktywna. Uruchamia go, gdy nowe zdarzenia spełniających kryteria alertu.

## <a name="manage-your-alerts"></a>Zarządzanie alertami

Po utworzeniu alertu, jest widoczna w sekcji Alerty bloku Monitor. Wybierz alert, który ma być zarządzany do:

* Czy chcesz go edytować.
* Usuń go.
* Wyłączyć lub włączyć, jeśli chcesz tymczasowo zatrzymać lub wznowić odbieranie powiadomień o alercie.

## <a name="next-steps"></a>Kolejne kroki
- Pobierz [Przegląd alertów](monitoring-overview-alerts.md).
- Dowiedz się więcej o [powiadomień szybkości](monitoring-alerts-rate-limiting.md).
- Przegląd [schemat elementów webhook alertu dziennika aktywności](monitoring-activity-log-alerts-webhook.md).
- Dowiedz się więcej o [grup akcji](monitoring-action-groups.md).  
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](monitoring-service-notifications.md).
- Tworzenie [alertu dziennika aktywności, aby monitorować wszystkie operacje aparat skalowania automatycznego w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Tworzenie [alertu dziennika aktywności, aby monitorować wszystkie operacje skalowania — w/skalowalnego w poziomie nie powiodło się automatyczne skalowanie w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
