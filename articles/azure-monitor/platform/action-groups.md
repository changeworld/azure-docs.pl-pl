---
title: Tworzenie grup i zarządzanie nimi akcji w witrynie Azure portal
description: Dowiedz się, jak tworzyć grupy i zarządzać nimi akcji w witrynie Azure portal.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 432f1a89979829bd43596d0d6a3ab7a2a3bfb996
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336486"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Tworzenie grup i zarządzanie nimi akcji w witrynie Azure portal
## <a name="overview"></a>Przegląd ##
Grupy akcji to zbiór preferencje powiadamiania zdefiniowane przez właściciela subskrypcji platformy Azure. Alerty monitorowania i kondycji usług platformy Azure umożliwia powiadomienie użytkowników, czy alert został wywołany grup akcji. Różne alerty może używać tej samej grupy akcji lub grupy inną akcję w zależności od wymagań użytkownika.

Gdy akcji jest skonfigurowana do wysyłania powiadomień osoby za pośrednictwem poczty e-mail lub SMS osoba otrzyma potwierdzenie wskazujący on / użytkownik został dodany do grupy akcji.

W tym artykule przedstawiono sposób tworzenia grup i zarządzanie nimi akcji w witrynie Azure portal.

Każda akcja składa się z następującymi właściwościami:

* **Nazwa**: Unikatowy identyfikator w obrębie grupy akcji.  
* **Typ akcji**: Akcja do wykonania. Przykłady obejmują wysyłanie pocztą e-mail wywołanie, wiadomość SMS, głos; lub różnego rodzaju akcje automatyczne wyzwalanie. Zobacz typy w dalszej części tego artykułu. 
* **Szczegóły**: Odpowiednimi szczegółami, które różnią się zależnie od *typ akcji*. 

Aby uzyskać informacje na temat konfigurowania grup akcji przy użyciu szablonów usługi Azure Resource Manager, zobacz [szablonów usługi Resource Manager grupy akcji](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Tworzenie grupy akcji przy użyciu witryny Azure portal ##
1. W [portal](https://portal.azure.com), wybierz opcję **Monitor**. **Monitor** bloku konsoliduje wszystkie ustawienia monitorowania i danych w jednym widoku.

    ![Usługa "Monitor"](./media/action-groups/home-monitor.png)
1. Wybierz **alerty** polecenie **Zarządzanie grupami działań**.

    ![Zarządzanie przycisk grupy akcji](./media/action-groups/manage-action-groups.png)
1. Wybierz **Dodaj grupę akcji**, a następnie wypełnij pola.

    ![Polecenie "Dodaj grupę akcji"](./media/action-groups/add-action-group.png)
1. Wprowadź nazwę w **Nazwa grupy akcji** polu, a następnie wprowadź nazwę w **krótką nazwę** pole. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

      ![Okno dialogowe Dodawanie grupy akcji"](./media/action-groups/action-group-define.png)

1. **Subskrypcji** polu autofills przy użyciu Twojej bieżącej subskrypcji. Ta subskrypcja jest ten, w którym jest zapisany do grupy akcji.

1. Wybierz **grupy zasobów** w są zapisywane do grupy akcji.

1. Zdefiniuj listę akcji, podając każdej akcji:

    a. **Nazwa**: Wprowadź unikatowy identyfikator dla tej akcji.

    b. **Typ akcji**: Wybierz adres E-mail/SMS/wypychania/rejestr, aplikację logiki, element Webhook, ITSM lub elementu Runbook usługi Automation.

    c. **Szczegóły**: Na podstawie akcji typu, wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook, aplikacja platformy Azure, połączenia narzędzia ITSM lub elementu runbook usługi Automation. Dla akcji ITSM, należy również określić **elementu roboczego** i wymaga narzędziem ITSM, inne pola.

1. Wybierz **OK** można utworzyć grupy akcji.

## <a name="manage-your-action-groups"></a>Zarządzanie grupami działań ##
Po utworzeniu grupy akcji, jest ona widoczna na **grup akcji** części **Monitor** bloku. Wybierz grupę akcji, które mają być zarządzane do:

* Dodawanie, edytowanie lub usuwanie akcji.
* Usuń grupę akcji.

## <a name="action-specific-information"></a>Informacje o określonej akcji
**Aplikacja platformy Azure wypychania** — może mieć maksymalnie 10 akcje aplikacji platformy Azure w grupy akcji. W tej chwili akcji aplikacji platformy Azure obsługuje tylko ServiceHealth alertów. Dowolnym innym czasie alertu zostaną zignorowane. Zobacz [Konfigurowanie alertów po każdym opublikowaniu powiadomienia kondycji usługi](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**Adres e-mail** — wiadomości E-mail będą wysyłane z następujących adresów e-mail. Upewnij się, że filtrowanie wiadomości e-mail jest prawidłowo skonfigurowany
   - azure-noreply@microsoft.com
   - azureemail-noreply@microsoft.com
   - alerts-noreply@mail.windowsazure.com

Może mieć maksymalnie 1000 akcji poczty e-mail do grupy akcji. Zobacz [ograniczania informacje o szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) artykułu

**ITSM** — może mieć maksymalnie 10 akcje ITSM w akcji ITSM grupy akcji wymaga połączenia ITSM. Dowiedz się, jak utworzyć [połączenia narzędzia ITSM](../../azure-monitor/platform/itsmc-overview.md).

**Aplikacji logiki** — może mieć maksymalnie 10 akcji aplikacji logiki w grupy akcji

**Funkcja aplikacji** -klawiszy funkcyjnych dla aplikacji funkcji, skonfigurowany jako akcje są odczytywane za pomocą interfejsu API funkcji, która obecnie wymaga aplikacji funkcji w wersji 2 do skonfigurowania aplikacji, ustawienie "AzureWebJobsSecretStorageType" na "files", zobacz [ Zmienia się na zarządzanie kluczami w funkcje w wersji 2]( https://aka.ms/funcsecrets) Aby uzyskać więcej informacji.

**Element Runbook** — może mieć maksymalnie 10 elementów Runbook akcji, w akcji grupy dotyczą [limity usług subskrypcji platformy Azure](../../azure-subscription-service-limits.md) limity ładunków elementu Runbook

**SMS** — może mieć maksymalnie 10 akcje programu SMS w akcji grupy, zobacz [ograniczania informacje o szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) artykuł zobacz [SMS alert zachowanie](../../azure-monitor/platform/alerts-sms-behavior.md) artykułu

**Głos** — może mieć maksymalnie 10 akcje głosu w grupy akcji</dd>
Zobacz [ograniczania informacje o szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) artykułu</dd>

**Element Webhook** — może mieć maksymalnie 10 Akcje elementu Webhook w grupy akcji. Logika ponawiania próby — limit czasu dla odpowiedzi to 10 sekund. Wywołanie elementu webhook zostanie ponowiona maksymalnie 2 godziny po następujące kody stanu HTTP są zwracane: 408, 429, 503, 504 lub punkt końcowy HTTP nie odpowiada. Pierwszym ponowieniem próby odbywa się po 10 sekundach. Drugi i ostatniego ponownych prób odbywa się po 100 sekund.

Zakresy adresów IP źródła
    - 13.106.57.181
    - 13.106.54.3
    - 13.106.54.19
    - 13.106.38.142
    - 13.106.38.148
    - 13.106.57.196

Aby otrzymywać aktualizacje o zmianach na te adresy IP, zaleca się konfigurowania [alertów dotyczących kondycji usługi](./../../monitoring-and-diagnostics/monitoring-service-notifications.md) który monitoruje informacyjny powiadomień dotyczących usługi grupy akcji.


## <a name="next-steps"></a>Kolejne kroki ##
* Dowiedz się więcej o [SMS alert zachowanie](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Uzyskaj [zrozumieć schemat elementów webhook alertu dziennika aktywności](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Dowiedz się więcej o [łącznik ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Dowiedz się więcej o [szybkości](../../azure-monitor/platform/alerts-rate-limiting.md) alerty.
* Pobierz [Przegląd alertów dziennika aktywności](../../azure-monitor/platform/alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [Konfigurowanie alertów po każdym opublikowaniu powiadomienia kondycji usługi](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
