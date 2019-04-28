---
title: Wspólny schemat alertów dla alertów monitora platformy Azure
description: Opis wspólnego schematu alertu, dlaczego należy używać go i jak go włączyć
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: c18227a491478d0d8010761440a54fd088344b39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776597"
---
# <a name="common-alert-schema"></a>Typowe schematy alertów

W tym artykule opisano, co jest wspólny schemat alertu, korzyści z używania go i jak go włączyć.

## <a name="what-is-the-common-alert-schema"></a>Co to jest wspólny schemat alertów?

Wspólny schemat alertu standaryzuje środowisko wykorzystania powiadomień o alertach na platformie Azure już dziś. W przeszłości trzy typy alertów na platformie Azure już dziś (metryk, dzienników i dziennika aktywności) miały własne szablony wiadomości e-mail, schematy elementu webhook, itp. Za pomocą wspólnego schematu alertu mogą teraz odbierać powiadomienia o alertach za pomocą spójny schemat.

W tym artykule opisano wszystkie wystąpienia alertu **zasób, który będzie miała wpływu na** i **przyczyną alertu**, te wystąpienia są opisane we wspólnym schemacie w następujących sekcjach:
* **Podstawy**: Zbiór **standardowych pól**, wspólny dla wszystkich typów alertów, które opisują **jakim zasobem** alertu znajduje się na wraz z dodatkowych wspólnych metadanych alertu (na przykład, ważności lub opis). 
* **Zgłoś alert, kontekst**: Zestaw pól, które opisują **przyczyny alertu**, z polami, które różnią się **na podstawie typu alertu**. Na przykład alert metryki musi pól, takich jak metryki nazwę i wartość metryki w kontekście alertu alertu dziennika aktywności miałyby informacje o zdarzeniu, które wygenerowało alert. 

Scenariusze typowe integracji, które firma Microsoft skontaktuje się z klientami obejmują routingu wystąpienia alertu do danego zespołu, niektóre pivot (na przykład grupa zasobów), po upływie którego odpowiedzialny zespół uruchamia na nim pracy w oparciu. Za pomocą wspólnego schematu alertu należy można ustalić logikę routingu różnych typów alertów przy użyciu pól podstawowych opuszczania pól kontekstu jest przeznaczona dla danego zespołów do dalszego badania.

Oznacza to, że można zainstalować mniej integracji, dzięki czemu proces zarządzania i konserwacji je _znacznie_ prostsze zadania. Ponadto wzbogacenia przyszłych ładunku alertu (na przykład dostosowania, diagnostycznych wzbogacania itp.) tylko ujawni się we wspólnym schemacie.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Jakie ulepszenia powoduje wspólny schemat alertów?

Wspólny schemat alertów będą przede wszystkim objawiać w powiadomień o alertach. Ulepszenia, które zostaną wyświetlone są wymienione poniżej:

| Akcja | Ulepszenia|
|:---|:---|
| SMS | Spójnego szablon wiadomości SMS dla wszystkich typów alertów. |
| Email | Szablon wiadomości e-mail spójne i szczegółowe umożliwia łatwe diagnozowanie problemów na pierwszy rzut oka. Osadzony głębokiego linków prowadzących do wystąpienia alertu w portalu i odpowiedniego zasobu upewnij się, że możesz szybko przejść do rozwiązywania problemów. |
| Element Webhook/logika aplikacji/usługi Azure funkcję/element Runbook usługi Automation | Spójne strukturę JSON dla wszystkich typów alertów, co pozwala na łatwe tworzenie integracji między różnych typów alertów. |

Nowy schemat również włączyć bardziej zaawansowane środowisko alertów wykorzystania zarówno w witrynie Azure portal, jak i w aplikacji mobilnej platformy Azure w najbliższej przyszłości. 

[Dowiedz się więcej na temat definicji schematu dla elementów Webhook/logika aplikacji/usługi Azure Functions/elementów Runbook usługi Automation.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> Następujące akcje nie obsługują wspólny schemat alertu: Łącznik ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Jak włączyć wspólny schemat alertów?

Można zgodzić się na lub zrezygnować z wspólnego schematu alertów za pośrednictwem grup akcji, w portalu usługi oraz interfejsu API REST. Przełącz, aby przełączyć się do nowego schematu istnieje na poziomie akcji. Na przykład należy włączyć osobno dla akcji poczty e-mail i Akcja elementu webhook.

> [!NOTE]
> 1. Wspólny schemat domyślnie obsługują następujące typy alertów (nie zoptymalizowany pod kątem w wymagane):
>     * Alerty wykrywania inteligentnego
> 1. Następujące typy alertów nie obsługują obecnie wspólny schemat:
>     * Alerty wygenerowane przez [usługi Azure Monitor dla maszyn wirtualnych](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Alerty wygenerowane przez [usługi Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>W witrynie Azure portal

![Zgodzić się na wspólnym schemacie alertu](media/alerts-common-schema/portal-opt-in.png)

1. Otwórz istniejące lub nowe działania w grupy akcji. 
1. Wybierz pozycję "Tak" dla przełącznika umożliwić wspólny schemat alertów, jak pokazano.

### <a name="through-the-action-groups-rest-api"></a>Za pośrednictwem grup akcji interfejsu API REST

Można również użyć [API grup akcji](https://docs.microsoft.com/rest/api/monitor/actiongroups) do zgadzaj się na wspólnym schemacie alertu. Podczas wprowadzania [Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) wywołania interfejsu API REST, można ustawić flagi "useCommonAlertSchema" wartość "true" (aby zgodzić się na) lub "fałsz" (Aby zrezygnować z udziału), dla każdego z następujących akcji — adres e-mail/webhook/logika aplikacji/Azure funkcję/element runbook usługi automation.

Na przykład, następujące żądania treści wprowadzone [Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) interfejsu API REST zostanie wykonaj następujące czynności:

* Włącz wspólny schemat alertów dla akcji w wiadomości e-mail "John Doe wiadomość e-mail"
* Wyłącz wspólny schemat alertów dla akcji w wiadomości e-mail "Jan Kowalski wiadomość e-mail"
* Włącz wspólny schemat alertów dla akcji elementu webhook "Przykładowy element webhook"

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Kolejne kroki

- [Typowe definicje schematu alertów dla elementów Webhook/logika aplikacji/usługi Azure Functions/elementów Runbook usługi Automation.](https://aka.ms/commonAlertSchemaDefinitions)



