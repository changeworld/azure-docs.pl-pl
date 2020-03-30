---
title: Wspólny schemat alertów dla alertów monitora platformy Azure
description: Opis wspólnego schematu alertu, dlaczego należy go używać i jak go włączyć
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249050"
---
# <a name="common-alert-schema"></a>Typowe schematy alertów

W tym artykule opisano, co jest wspólny schemat alertów, korzyści z używania go i jak go włączyć.

## <a name="what-is-the-common-alert-schema"></a>Co to jest wspólny schemat alertu?

Wspólny schemat alertów standaryzuje środowisko zużycia powiadomień alertów na platformie Azure już dziś. Historycznie trzy typy alertów na platformie Azure dzisiaj (metryka, dziennik i dziennik aktywności) miały własne szablony wiadomości e-mail, schematy zestawów webhook itp. Za pomocą wspólnego schematu alertu można teraz odbierać powiadomienia alertów ze spójnym schematem.

Każde wystąpienie alertu opisuje **zasób, którego dotyczy problem,** i **przyczynę alertu,** a te wystąpienia są opisane we wspólnym schemacie w następujących sekcjach:
* **Podstawowe informacje:** Zestaw **znormalizowanych pól,** wspólnych dla wszystkich typów alertów, które opisują, **na jakim zasobie** znajduje się alert wraz z dodatkowymi typowymi metadanymi alertów (na przykład ważnością lub opisem). 
* **Kontekst alertu:** Zestaw pól **opisujących przyczynę alertu,** z polami, które różnią się w zależności od **typu alertu.** Na przykład alert metryki będzie miał pola, takie jak nazwa metryki i wartość metryki w kontekście alertu, podczas gdy alert dziennika aktywności będzie miał informacje o zdarzeniu, które wygenerowało alert. 

Typowe scenariusze integracji, które słyszymy od klientów, obejmują routing wystąpienia alertu do danego zespołu na podstawie niektórych przestawnych (na przykład grupy zasobów), po czym odpowiedzialny zespół rozpoczyna pracę nad nim. Za pomocą wspólnego schematu alertu można mieć znormalizowane logiki routingu między typami alertów, wykorzystując podstawowe pola, pozostawiając pola kontekstu, jak jest dla zainteresowanych zespołów do dalszego zbadania.

Oznacza to, że potencjalnie można mieć mniej integracji, dzięki czemu proces zarządzania i utrzymywania ich _znacznie_ prostsze zadanie. Ponadto przyszłe wzbogacenia ładunku alertów (na przykład dostosowywanie, wzbogacenie diagnostyczne itp.) będą pojawiać się tylko we wspólnym schemacie.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Jakie ulepszenia zawiera wspólny schemat alertów?

Schemat alertów typowych będzie przede wszystkim manifestować się w powiadomieniach alertów. Ulepszenia, które zostaną wyświetlone, są wymienione poniżej:

| Akcja | Ulepszenia |
|:---|:---|
| SMS | Spójny szablon SMS dla wszystkich typów alertów. |
| Adres e-mail | Spójny i szczegółowy szablon wiadomości e-mail, umożliwiający łatwe diagnozowanie problemów na pierwszy rzut oka. Osadzone głębokie łącza do wystąpienia alertu w portalu i zasobu, którego dotyczy problem, zapewniają szybkie przejście do procesu korygowania. |
| Webhook/Logic App/Azure Function/Automation Runbook | Spójna struktura JSON dla wszystkich typów alertów, która umożliwia łatwe tworzenie integracji między różnymi typami alertów. |

Nowy schemat umożliwi również bogatsze środowisko użycia alertów w witrynie Azure portal i aplikacji mobilnej platformy Azure w najbliższej przyszłości. 

[Dowiedz się więcej o definicjach schematów dla zestawów Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> Następujące akcje nie obsługują wspólnego schematu alertu: ŁĄCZNIK ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Jak włączyć wspólny schemat alertu?

Możesz wyrazić zgodę na wspólny schemat alertów lub zrezygnować za pośrednictwem grup akcji, zarówno w portalu, jak i za pośrednictwem interfejsu API REST. Przełącznik, aby przełączyć się do nowego schematu istnieje na poziomie akcji. Na przykład musisz osobno zdecydować się na akcję e-mail i akcję elementu webhook.

> [!NOTE]
> 1. Następujące typy alertów domyślnie obsługują wspólny schemat (nie jest wymagana opt in):
>     * Inteligentne alerty wykrywania
> 1. Następujące typy alertów obecnie nie obsługują wspólnego schematu:
>     * Alerty generowane przez [usługę Azure Monitor dla maszyn wirtualnych](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Alerty generowane przez [usługę Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Za pośrednictwem portalu Azure

![Wspólny schemat alertów](media/alerts-common-schema/portal-opt-in.png)

1. Otwórz dowolną istniejącą lub nową akcję w grupie akcji. 
1. Wybierz "Tak" dla przełącznika, aby włączyć wspólny schemat alertu, jak pokazano.

### <a name="through-the-action-groups-rest-api"></a>Za pośrednictwem interfejsu API REST grup akcji

Można również użyć [interfejsu API grup akcji,](https://docs.microsoft.com/rest/api/monitor/actiongroups) aby zdecydować się na wspólny schemat alertów. Podczas tworzenia [lub aktualizowania](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) wywołania interfejsu API REST, można ustawić flagę "useCommonAlertSchema" na "true" (aby wyrazić zgodę) lub "false" (aby zrezygnować) dla dowolnej z następujących akcji - email/webhook/logic app/Azure Function/automation runbook.

Na przykład następująca treść żądania do interfejsu API [create lub update](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST wykona następujące czynności:

* Włącz wspólny schemat alertu dla akcji e-mail "John Doe's email"
* Wyłącz wspólny schemat alertu dla akcji e-mail "E-mail Jane Smith"
* Włącz wspólny schemat alertu dla akcji elementu webhook "Przykładowy element webhook"

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





## <a name="next-steps"></a>Następne kroki

- [Typowe definicje schematów alertów dla zestawów Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)
- [Dowiedz się, jak utworzyć aplikację logiki, która wykorzystuje wspólny schemat alertów do obsługi wszystkich alertów.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



