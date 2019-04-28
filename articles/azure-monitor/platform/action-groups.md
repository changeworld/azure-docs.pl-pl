---
title: Tworzenie grup i zarządzanie nimi akcji w witrynie Azure portal
description: Dowiedz się, jak tworzyć grupy i zarządzać nimi akcji w witrynie Azure portal.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 3d06024b7fa4356d4ad0e8b52c45c2ead62ef784
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60778380"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Tworzenie grup i zarządzanie nimi akcji w witrynie Azure portal
## <a name="overview"></a>Omówienie ##
Grupy akcji to zbiór preferencje powiadamiania zdefiniowane przez właściciela subskrypcji platformy Azure. Alerty monitorowania i kondycji usług platformy Azure umożliwia powiadomienie użytkowników, czy alert został wywołany grup akcji. Różne alerty może używać tej samej grupy akcji lub grupy inną akcję w zależności od wymagań użytkownika. Można skonfigurować maksymalnie 2000 grup akcji w ramach subskrypcji.

Możesz skonfigurować akcję, aby powiadomić osoby, adres e-mail lub wiadomości SMS, otrzymują potwierdzenie wskazujący, że zostały one dodane do grupy akcji.

W tym artykule przedstawiono sposób tworzenia grup i zarządzanie nimi akcji w witrynie Azure portal.

Każda akcja składa się z następującymi właściwościami:

* **Nazwa**: Unikatowy identyfikator w obrębie grupy akcji.  
* **Typ akcji**: Akcja została wykonana. Przykłady obejmują wysyłanie pocztą e-mail wywołanie, wiadomość SMS, głos; lub różnego rodzaju akcje automatyczne wyzwalanie. Zobacz typy w dalszej części tego artykułu. 
* **Szczegóły**: Odpowiednimi szczegółami, które różnią się zależnie od *typ akcji*. 

Aby uzyskać informacje na temat konfigurowania grup akcji przy użyciu szablonów usługi Azure Resource Manager, zobacz [szablonów usługi Resource Manager grupy akcji](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Tworzenie grupy akcji przy użyciu witryny Azure portal ##
1. W [portal](https://portal.azure.com), wybierz opcję **Monitor**. **Monitor** okienko konsoliduje wszystkie ustawienia monitorowania i danych w jednym widoku.

    ![Usługa "Monitor"](./media/action-groups/home-monitor.png)
1. Wybierz **alerty** polecenie **Zarządzanie grupami działań**.

    ![Zarządzanie przycisk grupy akcji](./media/action-groups/manage-action-groups.png)
1. Wybierz **Dodaj grupę akcji**, a następnie wypełnij pola.

    ![Polecenie "Dodaj grupę akcji"](./media/action-groups/add-action-group.png)
1. Wprowadź nazwę w **Nazwa grupy akcji** polu, a następnie wprowadź nazwę w **krótką nazwę** pole. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

      ![Okno dialogowe Dodawanie grupy akcji"](./media/action-groups/action-group-define.png)

1. **Subskrypcji** polu autofills przy użyciu Twojej bieżącej subskrypcji. Ta subskrypcja jest ten, w którym jest zapisany do grupy akcji.

1. Wybierz **grupy zasobów** w są zapisywane do grupy akcji.

1. Zdefiniuj listę akcji. Podaj następujące czynności dla każdej akcji:

    a. **Nazwa**: Wprowadź unikatowy identyfikator dla tej akcji.

    b. **Typ akcji**: Wybierz adres E-mail/SMS/wypychania/rejestr, aplikację logiki, element Webhook, ITSM lub elementu Runbook usługi Automation.

    c. **Szczegóły**: Na podstawie akcji typu, wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook, aplikacja platformy Azure, połączenia narzędzia ITSM lub elementu runbook usługi Automation. Dla akcji ITSM, należy również określić **elementu roboczego** i wymaga narzędziem ITSM, inne pola.

1. Wybierz **OK** można utworzyć grupy akcji.

## <a name="manage-your-action-groups"></a>Zarządzanie grupami działań ##
Po utworzeniu grupy akcji, jest ona widoczna na **grup akcji** części **Monitor** okienka. Wybierz grupę akcji, które mają być zarządzane do:

* Dodawanie, edytowanie lub usuwanie akcji.
* Usuń grupę akcji.

## <a name="action-specific-information"></a>Informacje o określonej akcji
> [!NOTE]
> Zobacz [limity usług subskrypcji do monitorowania](https://docs.microsoft.com/azure/azure-subscription-service-limits#monitor-limits) dla ograniczeń numerycznych na każdym z poniższych elementów.  

**Aplikacja platformy Azure wypychania** — mogą mieć ograniczoną liczbę akcji aplikacji platformy Azure w grupy akcji. W tej chwili akcji aplikacji platformy Azure obsługuje tylko ServiceHealth alertów. Inne typu alertu zostaną zignorowane. Zobacz [Konfigurowanie alertów po każdym opublikowaniu powiadomienia kondycji usługi](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**Adres e-mail** — wiadomości E-mail będą wysyłane z następujących adresów e-mail. Upewnij się, że filtrowanie wiadomości e-mail jest prawidłowo skonfigurowany
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Mogą mieć ograniczoną liczbę akcji poczty e-mail do grupy akcji. Zobacz [ograniczania informacje o szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) artykułu

**ITSM** — może być ograniczona liczba ograniczoną liczbę akcje ITSM w grupy akcji. Akcja ITSM wymaga połączenia narzędzia ITSM. Dowiedz się, jak utworzyć [połączenia narzędzia ITSM](../../azure-monitor/platform/itsmc-overview.md).

**Aplikacji logiki** — może być ograniczona liczba akcji aplikacji logiki do grupy akcji.

**Funkcja aplikacji** — funkcja kluczy dla aplikacji funkcji, skonfigurowany jako akcje są odczytywane przy użyciu interfejsu API funkcji wymagająca aplikacji funkcji w wersji 2, aby skonfigurować ustawienie "AzureWebJobsSecretStorageType" aplikacji "pliki". Aby uzyskać więcej informacji, zobacz [zmieni się na zarządzanie kluczami w funkcje w wersji 2]( https://aka.ms/funcsecrets).

**Element Runbook** — mogą mieć ograniczoną liczbę działań elementu Runbook w grupy akcji. Zapoznaj się [limity usług subskrypcji platformy Azure](../../azure-subscription-service-limits.md) limity ładunków elementu Runbook.

**SMS** — mogą mieć ograniczoną liczbę akcji programu SMS w grupy akcji. Zobacz też [ograniczania informacje o szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) i [SMS alert zachowanie](../../azure-monitor/platform/alerts-sms-behavior.md) dodatkowe ważne informacje. 

**Głos** — mogą mieć ograniczoną liczbę akcji głosu w grupy akcji. Zobacz [ograniczania informacje o szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) artykułu.

**Element Webhook** — mogą mieć ograniczoną liczbę akcji elementów Webhook w grupy akcji. Elementy Webhook są zwalniane, używając następujących reguł. Wywołanie elementu webhook zostanie ponowiony więcej niż 2 godziny po następujące kody stanu HTTP są zwracane: 408, 429, 503, 504 lub punkt końcowy HTTP nie odpowiada. Pierwsze ponowienie próby odbywa się po 10 sekundach. Drugi ponawiania odbywa się po 100 sekund. Po awarii dwóch węzłów żadna grupa akcji wywoła punktu końcowego w ciągu 30 minut. 

Zakresy adresów IP źródła
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Aby otrzymywać aktualizacje o zmianach na te adresy IP, firma Microsoft zaleca skonfigurować [usługa alert o kondycji, która monitoruje informacyjny powiadomień dotyczących usługi grupy akcji.


## <a name="next-steps"></a>Kolejne kroki ##

* Dowiedz się więcej o [SMS alert zachowanie](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Uzyskaj [zrozumieć schemat elementów webhook alertu dziennika aktywności](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Dowiedz się więcej o [łącznik ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Dowiedz się więcej o [szybkości](../../azure-monitor/platform/alerts-rate-limiting.md) alerty.
* Pobierz [Przegląd alertów dziennika aktywności](../../azure-monitor/platform/alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [Konfigurowanie alertów po każdym opublikowaniu powiadomienia kondycji usługi](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

